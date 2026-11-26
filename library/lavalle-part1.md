# LaValle, *Planning Algorithms* (Cambridge University Press, 2006) — Part 1: Ch. 1–7
Source: `PlanningAlgorithmsBook.pdf` (student's copy, folder `Algorithms_For_Sensor_Based_Robotics`), PDF pages 10–192 (book pages 3–368). Official reference text for **EN.601.663 ASBR** (course page, other resources). Ch. 8–15 are in a separate file.

> **Page mapping (checked against running headers).** The PDF is printed **2-up**: each PDF page holds two book pages, an odd one on the left and the next even one on the right. **PDF page = ⌊(book page + 17)/2⌋**; equivalently PDF page $n$ holds book pages $2n-17$ and $2n-16$. No pages are missing in this range.
> Chapter starts: Ch. 1 p. 3 = PDF 10 · Ch. 2 p. 27 = PDF 22 · Part II overview p. 77 = PDF 47 · Ch. 3 p. 81 = PDF 49 · Ch. 4 p. 127 = PDF 72 · Ch. 5 p. 185 = PDF 101 · Ch. 6 p. 249 = PDF 133 · Ch. 7 p. 311 = PDF 164 · (Ch. 8 p. 369 = PDF 193, not in this file).
> Every citation below gives **book page, PDF page**.
>
> **Lesson codes** follow the ASBR plan: A1 kinematics/spatial Jacobian · A2 hand-eye · A3 C-space + potentials · A4 graph planning · A5 sampling planners · A6 KF/EKF/EKF-SLAM · A7 Bayes/PF/MCL · A8 tools + exam prep. Older library files use A4 for sampling planners; here **A5 = sampling planners**. "Assignment 3" means the graded MoveIt 2 planner plugin (EST in 2026), not lesson A3.
>
> **Scope and overlap.** Choset's notes (`choset-part1.md`, `choset-part2.md`) already cover the visibility graph, GVD, trapezoidal/boustrophedon cells, PRM/EST/RRT/SBL/LazyPRM, narrow-passage samplers, kd-trees, metrics, and the basic dispersion/discrepancy/Halton definitions. This file keeps those short and spends its space on what LaValle adds: value iteration, the RDT formulation, uniform SO(3) sampling, safe edge checking, explicit $\mathcal C_{obs}$ construction, the shortest-path and maximum-clearance roadmaps, algebraic/complete methods, complexity results, closed chains, time-varying and multi-robot planning.
>
> **Book-specific notation:** state $x\in X$, action $u\in U(x)$, transition $x'=f(x,u)$; $x_I$, $X_G$; cost-to-come $C(x)$, **cost-to-go $G(x)$** (opposite letter to Russell–Norvig's $g$); C-space $\mathcal C$, $\mathcal C_{obs}$, $\mathcal C_{free}$, query $(q_I,q_G)$; dense sample sequence $\alpha(i)$; metric $\rho$; quaternion $h=a+bi+cj+dk$ (scalar first). Full table in the Notation section.

---

## Ch. 1 Introduction (PDF 10–21, book pp. 3–25) — Lessons: A8 (vocabulary)
**Big idea:** "Planning" covers robotics motion planning, AI planning and control-style trajectory design. All share the same ingredients.

- **Ingredients** (§1.3, book pp. 17–19, PDF 17–18): state (usually represented implicitly), time (explicit or just ordering), actions (inputs/controls), initial and goal states, a criterion (**feasibility** vs **optimality**), and a plan. A plan can be a sequence of actions, a feedback plan (action = function of state) or a function of the information state.
- **Plans are used three ways** (§1.4.3, book pp. 21–23, PDF 19–20): execution, refinement, and hierarchical inclusion (a plan becomes an action inside a larger plan). Fig. 1.19 (book p. 23, PDF 20) shows the classic robotics refinement chain: collision-free path → smooth for differential constraints → time-scale into a trajectory → feedback tracking. That is exactly MoveIt's pipeline (planner → smoothing/time-parameterization adapters → `ros2_control` tracking).
- Exercises: none worth doing for ASBR.

---

## Ch. 2 Discrete Planning (PDF 22–46, book pp. 27–75) — Lessons: A4 (core), A3 (wavefront link), A5 (template reused for sampling planners)
**Big idea:** Planning on a countable state space is graph search on a state-transition graph that is **revealed incrementally** by applying actions. It is never given in full. Optimal planning is dynamic programming. Dijkstra and A* are the "only touch the interesting states" versions of **value iteration**.

### §2.1 Problem formulation (book pp. 28–31, PDF 22–24)
- **Formulation 2.1 (book p. 29, PDF 23):** nonempty countable state space $X$; finite action space $U(x)$; transition $x'=f(x,u)$ (2.1); $U=\bigcup_x U(x)$ (2.2); initial state $x_I$; goal set $X_G\subset X$. Equivalent to a directed state-transition graph, or to asking whether a DFA accepts some string.
- Example 2.1 (book p. 29, PDF 23): the 4-connected grid with $U=\{(0,\pm1),(\pm1,0)\}$, $f(x,u)=x+u$. Shading tiles deletes vertices. This is the grid-planning model used in A4.

### §2.2 Searching for feasible plans (book pp. 32–43, PDF 24–30)
- **Systematic search** (book p. 32, PDF 24): on a finite graph, it visits every reachable state and so decides existence in finite time. On an infinite graph, every reachable vertex is reached in the limit (Fig. 2.3: expand in wavefronts, not in one direction).
- States are **unvisited**, **alive** (in queue $Q$), or **dead** (all successors visited) (book p. 33, PDF 25).

**Fig. 2.4 General forward search (book p. 33, PDF 25)**
```
Q.Insert(x_I); mark x_I visited
while Q not empty:
    x ← Q.GetFirst()
    if x ∈ X_G: return SUCCESS
    for u ∈ U(x):
        x' ← f(x,u)
        if x' not visited: mark x' visited; Q.Insert(x')   # store parent[x'] = x (and u) to recover the plan
        else: Resolve duplicate x'                           # e.g. lower C(x') and reorder Q
return FAILURE
```
The **only** difference between methods is how $Q$ is sorted (book p. 34, PDF 25):

| Method | Queue order | Notes (book page, PDF) |
|---|---|---|
| BFS | FIFO | fewest steps; $O(\lvert V\rvert+\lvert E\rvert)$; systematic even without a visited set (p. 35, PDF 26) |
| DFS | LIFO | $O(\lvert V\rvert+\lvert E\rvert)$; **not** systematic on infinite graphs (p. 36, PDF 26) |
| Dijkstra | cost-to-come $C(x)$, $C(x')=C^*(x)+l(x,u)$ | a state's cost is optimal once it is popped (dead); $O(\lvert V\rvert\lg\lvert V\rvert+\lvert E\rvert)$ with a Fibonacci heap (pp. 36–37, PDF 26–27) |
| A* | $C^*(x')+\hat G^*(x')$, with $\hat G^*$ an **underestimate** of the cost-to-go | Manhattan distance underestimates on the 4-grid; $\hat G^*\equiv0$ gives Dijkstra (pp. 37–38, PDF 27) |
| Best-first | $\hat G^*$ only | greedy, not optimal, not systematic; the spiral-tube Fig. 2.5 costs $k$ extra steps for any $k$ (p. 38, PDF 27) |
| Iterative deepening / IDA* | DFS to depth $i$, restart with $i+1$ | low memory; pays off at large branching factor (p. 39, PDF 28) |

- **Backward search** (Fig. 2.6, book p. 40, PDF 28): backward action space $U^{-1}(x')=\{(x,u)\in U^{-1}\mid x'=f(x,u)\}$ (2.3) and $x=f^{-1}(x',u^{-1})$, i.e. search on the reversed graph from $X_G$.
- **Bidirectional search** (Fig. 2.7, book p. 42, PDF 29): two queues $Q_I,Q_G$, alternate expansions, succeed when a state is "visited from both". There are Dijkstra/A* versions. Greedy versions may come close but fail to connect.
- **§2.2.4 Unified view** (book pp. 41–43, PDF 29–30). This 6-step template is reused verbatim for sampling planners in §5.4:
  1. initialize $G(V,E)$ with $x_I$ and/or $x_G$;
  2. select a vertex;
  3. apply an action;
  4. insert an edge;
  5. check for a solution;
  6. repeat.

### §2.3 Discrete optimal planning (book pp. 43–57, PDF 30–37)
- **Formulation 2.2** (fixed length $K$, book p. 44, PDF 30). Stage-additive cost (2.4):
  $$L(\pi_K)=\sum_{k=1}^{K} l(x_k,u_k)+l_F(x_F),\qquad l_F(x_F)=\begin{cases}0 & x_F\in X_G\\ \infty & \text{otherwise}\end{cases}$$
  So $L=\infty$ encodes infeasibility. $l\equiv1$ gives the fewest steps; $l\equiv0$ gives pure feasibility.
- **Backward value iteration** (book pp. 45–46, PDF 31). $G^*_F=l_F$ (2.6), then
  $$G^*_k(x_k)=\min_{u_k}\{l(x_k,u_k)+G^*_{k+1}(f(x_k,u_k))\}\quad(2.11)$$
  Each sweep is $O(\lvert X\rvert\lvert U\rvert)$, so $O(K\lvert X\rvert\lvert U\rvert)$ total. It gives the optimal cost-to-go from **every** start to a fixed $X_G$.
- **Forward value iteration** (book pp. 48–50, PDF 32–33). $C^*_1=l_I$ ($0$ at $x_I$, $\infty$ elsewhere) (2.13), then $C^*_k(x_k)=\min_{u^{-1}_k}\{C^*_{k-1}(x_{k-1})+l(x_{k-1},u_{k-1})\}$ (2.16). It gives optimal plans from a fixed $x_I$ to **every** state.
- **Formulation 2.3** (variable length, book pp. 50–51, PDF 33–34): add a **termination action** $u_T$ (stay put, zero cost forever). Run until stationary ($G^*_{i-1}=G^*_i$). This is guaranteed if $l\ge0$ (or at least there are no negative cycles), after at most (longest optimal plan) iterations (book p. 52, PDF 34). Then:
  - Bellman equation: $$G^*(x)=\min_u\{l(x,u)+G^*(f(x,u))\}\quad(2.18)$$
  - feedback recovery: $$u^*=\arg\min_{u\in U(x)}\{l(x,u)+G^*(f(x,u))\}\quad(2.19)$$
  $G^*$ is a **navigation function** (book p. 52, PDF 34), which is the discrete analogue of the wavefront potential in Choset §4.5 (A3). Forward counterpart: (2.20) (book p. 53, PDF 35).

```
BACKWARD_VALUE_ITERATION(X, U, f, l, X_G)        # Formulation 2.3, eq. (2.18)
G[x] ← 0 if x ∈ X_G else ∞   for all x
repeat
    G' ← G
    for x ∈ X:  G'[x] ← min( G[x] /*u_T*/ , min_{u∈U(x)} l(x,u) + G[f(x,u)] )
    changed ← (G' ≠ G);  G ← G'
until not changed
policy(x) = argmin_u l(x,u) + G[f(x,u)]           # eq. (2.19)
```
- **Worked example (Example 2.3/2.5, five states a–e, $X_G=\{d\}$; graph in Fig. 2.8, book p. 47, PDF 32).** Redo it by hand from the figure (its edge weights are garbled in the text extraction; read the figure).
  - Fixed $K=4$: backward tables $G^*_5\ldots G^*_1$ (Fig. 2.9, book p. 47) end with $G^*_1=(6,4,5,4,\infty)$; forward tables from $x_I=a$ (Fig. 2.12, book p. 50, PDF 33).
  - Variable length: stationary $G^*=(4,2,1,0,\infty)$ after 3 sweeps (Fig. 2.14, book p. 54, PDF 35); forward from $x_I=b$, $C^*=(2,0,1,2,3)$ (Fig. 2.15, book p. 55, PDF 36).
  - Optimal plan from a is $a\to b\to c\to d$.
- **§2.3.3 Dijkstra revisited** (book pp. 55–57, PDF 36–37): Dijkstra is forward value iteration that only updates the "interesting" states. Unvisited = cost still $\infty$; dead = stationary; queue = finite but not yet final. Value iteration still matters because it extends to continuous, stochastic and game settings (Ch. 8, 10).

**Fig. 2.16 Forward label-correcting (book p. 56, PDF 36)** — optimal for **any** queue order on finite $X$:
```
C(x) ← ∞ for x ≠ x_I; C(x_I) ← 0; Q.Insert(x_I)
while Q not empty:
    x ← Q.GetFirst()
    for u ∈ U(x):
        x' ← f(x,u)
        if C(x) + l(x,u) < min{C(x'), C(x_G)}:     # prune with the best goal cost found so far
            C(x') ← C(x) + l(x,u)                   # dead states may become alive again
            if x' ≠ x_G: Q.Insert(x')
```

### §2.4–2.5 Logic-based planning (book pp. 57–71, PDF 37–44) — brief, not ASBR material
- STRIPS-like Formulation 2.4 (book p. 58, PDF 37): instances, predicates, operators (preconditions/effects), initial set $S$, goal $G$. Flashlight Example 2.6 (book pp. 59–60, PDF 38). It encodes up to $2^{\lvert P\rvert\lvert I\rvert^k}$ states (2.26, book p. 62, PDF 39), so it is a compressed input and the planning problem is NP-hard or worse.
- Methods: partial-plan search (book pp. 63–64, PDF 40), Graphplan planning graph with mutex layers (book pp. 64–69, PDF 40–43), planning as SAT with fixed horizon $K$ and frame axioms (book pp. 69–71, PDF 43–44).

**Exercises worth doing (book pp. 72–75, PDF 44–46):**
- **1** (value iteration by hand on Fig. 2.21, both directions; the figure's edge weights are partly unreadable in the extraction, so read the PDF);
- **6** (backward Dijkstra and backward A*, prove optimal);
- **18** (A* on grids with Manhattan vs Euclidean heuristics; explain why the tighter heuristic wins and why many tied optima hurt A*);
- **19** (BFS vs best-first vs A* adversarial cases);
- **20** (bidirectional grid search trade-off);
- **22–23** (implement value iteration and Dijkstra and check they agree, which is a direct warm-up for A4 grid planners in C++).
- Skip 7–17 (logic) for ASBR.

**Pitfalls (ch. 2):**
- LaValle's **$G$ is cost-to-go** and $C$ is cost-to-come. In Choset App. H and Russell–Norvig, $g$ is cost-so-far and $h$ the heuristic. Don't mix them in an exam answer.
- A* optimality "if $\hat G^*$ is an underestimate" (book p. 37) holds when states can be re-opened (label-correcting). With the Fig. 2.4 template, where popped states are dead forever, you need a **consistent** heuristic, $\hat G^*(x)\le l(x,u)+\hat G^*(f(x,u))$. Grid Manhattan, octile and Euclidean heuristics are all consistent.
- Value iteration needs no negative cycles. Dijkstra needs $l\ge0$.

---

## Part II overview (PDF 47–48, book pp. 77–80)
Two themes:
1. Implicit representations: $\mathcal C$ is uncountable and $\mathcal C_{obs}$ is never built explicitly in practice.
2. Continuous → discrete, done either combinatorially (Ch. 6, **complete**) or by sampling (Ch. 5, **resolution/probabilistic completeness**).

Ch. 8 then redefines a plan as a feedback function over the state space.

---

## Ch. 3 Geometric Representations and Transformations (PDF 49–71, book pp. 81–125) — Lessons: A1 (transforms, DH), A3 (models used for collision), F3
**Big idea:** Bodies are Boolean combinations of primitives. Moving a body means applying a rigid transform to its points, or $h^{-1}$ to its implicit primitives. Chains are products of per-link homogeneous transforms.

### §3.1 Geometric modeling (book pp. 81–92, PDF 49–54)
- World $\mathcal W=\mathbb R^2$ or $\mathbb R^3$; obstacle region $\mathcal O\subseteq\mathcal W$ and robot $\mathcal A$ are **closed** sets.
- Convex polygon = intersection of half-planes $H_i=\{(x,y)\mid f_i(x,y)\le0\}$ (3.2–3.3). Vertices are listed counterclockwise and $f_i<0$ on the interior (left) side. Nonconvex = union of convex pieces (3.4) (book pp. 82–84, PDF 49–50).
- Collision predicate (book p. 85, PDF 51): $\phi=\bigvee_i\bigwedge_j e_{ij}$ ("sum of products", (3.5)–(3.6)), evaluated in $O(n)$ for $n$ primitives.
- Polyhedra: the outward normal of a face is $v_{12}\times v_{23}$ from three counterclockwise vertices (book p. 87, PDF 52). The **doubly connected edge list** (half-edges with origin, twin, face, next, prev) is on book p. 86 (PDF 51) and is reused in Ch. 6.
- Semi-algebraic models: polynomial $f\le0$ primitives; gingerbread face (3.11) (book pp. 87–89, PDF 52–53). Strict $<$ is needed for open sets.
- Other models (book pp. 89–92, PDF 53–54): oriented polygons with clockwise holes, 3D triangle soups (no inside/outside, which is what PQP/FCL meshes are), NURBS (3.18), **bitmaps/occupancy grids**, superquadrics, generalized cylinders.

### §3.2 Rigid-body transformations (book pp. 92–100, PDF 54–58)
- Transforming an implicit primitive needs the **inverse** (book p. 93, PDF 55): $h(H_i)=\{w\in\mathcal W\mid f_i(h^{-1}(w))\le0\}$ (3.24). Notation $\mathcal A(q)$ = robot placed at $q$.
- Two interpretations: move the body in a fixed world frame, or move the frame (Fig. 3.7, book p. 95, PDF 56). LaValle always uses the first.
- 2D homogeneous transform: rotate by $\theta$ then translate (3.35, book p. 96, PDF 56).
- **Yaw–pitch–roll** (book p. 98, PDF 57): $R(\alpha,\beta,\gamma)=R_z(\alpha)R_y(\beta)R_x(\gamma)$ (3.42). Roll is applied first, about **fixed world axes**. This is the same convention as ROS `tf2::Quaternion::setRPY(roll, pitch, yaw)`.
- Extraction (3.47–3.49) (book p. 99, PDF 58): $\alpha=\mathrm{atan2}(r_{21},r_{11})$, $\beta=\mathrm{atan2}(-r_{31},\sqrt{r_{32}^2+r_{33}^2})$, $\gamma=\mathrm{atan2}(r_{32},r_{33})$.
- 4×4 homogeneous transform (3.50) (book p. 100, PDF 58).

### §3.3 Kinematic chains (book pp. 100–112, PDF 58–64)
- 2D chain (book p. 102, PDF 59): $T_i$ = rotate $\theta_i$ then translate $a_{i-1}$ along $x$ (3.52). A point of $\mathcal A_m$ maps by $T_1T_2\cdots T_m$ (3.53).
- **3D chain uses modified (Craig) DH** (book pp. 103–107, PDF 60–62), the same convention as MR App. C, not Spong's standard DH:
  - $R_i$ (3.54) is a screw about $z_i$ ($\theta_i,d_i$);
  - $Q_{i-1}$ (3.55) is a screw about $x_{i-1}$ ($a_{i-1},\alpha_{i-1}$);
  - $T_i=Q_{i-1}R_i$ (3.56):
  $$T_i=\begin{pmatrix}\cos\theta_i&-\sin\theta_i&0&a_{i-1}\\ \sin\theta_i\cos\alpha_{i-1}&\cos\theta_i\cos\alpha_{i-1}&-\sin\alpha_{i-1}&-\sin\alpha_{i-1}d_i\\ \sin\theta_i\sin\alpha_{i-1}&\cos\theta_i\sin\alpha_{i-1}&\cos\alpha_{i-1}&\cos\alpha_{i-1}d_i\\0&0&0&1\end{pmatrix}$$
  - $T_1=R_1$ ($a_0=\alpha_0=0$) (book p. 107, PDF 62).
- **Puma 560 worked example** (Example 3.4, DH table Fig. 3.17, book pp. 107–110, PDF 62–63): $\alpha_{i-1}=(0,-\pi/2,0,\pi/2,-\pi/2,\pi/2)$; $a_2,a_3,d_2,d_3,d_4$ are signed ($a_3,d_3<0$). Verify (3.59) and (3.61) from (3.56) by hand. It is good A1 practice in a convention different from the UR5e PoE.
- Octane chain (Example 3.5, book pp. 110–112, PDF 63–64): bonds are the $z$ axes, $a_i=0$, $d_i$ = bond length, $\alpha$ = bond angle.

### §3.4–3.5 Trees and nonrigid bodies (book pp. 112–122, PDF 64–69) — brief
- Trees (book pp. 112–118, PDF 64–67): apply chain formulas along the root-to-link path. **Junctions** (links with more than two joints) get one frame per incoming chain, offset by a fixed angle (Fig. 3.23, eq. 3.78). Loops are deferred to §4.4.
- Nonrigid (book pp. 120–122, PDF 68–69): scaling and shear matrices (3.83–3.84), flexible bodies.

**Exercises worth doing (book pp. 123–125, PDF 70–71):**
- **2** (characterise the yaw–pitch–roll non-uniqueness, i.e. gimbal lock at $\beta=\pm\pi/2$);
- **3**;
- **4** (ZYZ Euler matrix and inverse: $\alpha=\mathrm{atan2}(r_{23},r_{13})$, $\beta=\mathrm{atan2}(\sqrt{1-r_{33}^2},r_{33})$, $\gamma=\mathrm{atan2}(r_{32},-r_{31})$, checked correct);
- **7–8** (planar 3-link FK with homogeneous matrices);
- **9** (spherical joint as three zero-length revolutes: why it breaks topology, see §4.2.3);
- **18** (Puma simulator: a good C++/Eigen FK exercise, F10).

**Pitfalls (ch. 3):**
- LaValle's DH is **modified DH**. Mixing it with standard DH tables (the RDKDC UR5 handout uses standard Spong) silently breaks FK.
- Yaw–pitch–roll extraction fails at $\cos\beta=0$ (gimbal lock), **not** when $r_{11}=0$ or $r_{33}=0$ as the book says (see errata).
- Transform points by $h$ but implicit primitives by $h^{-1}$.

---

## Ch. 4 The Configuration Space (PDF 72–100, book pp. 127–184) — Lessons: A3 (core), A1/F3 (SO(3), quaternions), A5 (topology-aware sampling/metrics)
**Big idea:** $\mathcal C$ is the manifold of all transforms of the robot. Its topology (wrap-around, $\mathbb{RP}^3$) must be respected by every planner. $\mathcal C_{obs}$ can be built exactly only in easy cases (translation, polygons). In general it is a semi-algebraic set that sampling planners query implicitly. Closed chains give varieties, which need not be manifolds.

### §4.1 Topology (book pp. 127–144, PDF 72–80)
- Open/closed sets, interior/exterior/boundary, closure $\mathrm{cl}(U)=\mathrm{int}(U)\cup\partial U$ (book pp. 128–130, PDF 72–73). Hausdorff (p. 131, PDF 74). Continuity = preimages of open sets are open; **homeomorphism** = continuous bijection with continuous inverse (pp. 131–132, PDF 74).
- **Manifold** (book p. 134, PDF 75): every point has a neighbourhood homeomorphic to $\mathbb R^n$, with $n$ fixed.
- Identifications $X/\!\sim$ (book pp. 136–139, PDF 76–78, Fig. 4.5 on p. 137):
  - $\mathbb S^1=[0,1]/0\!\sim\!1$;
  - cylinder $\mathbb R\times\mathbb S^1$; torus $T^2=\mathbb S^1\times\mathbb S^1$ ($\ne\mathbb S^2$); $T^n=(\mathbb S^1)^n$;
  - Möbius band, Klein bottle;
  - $\mathbb{RP}^n=\mathbb S^n/(x\sim-x)$, the lines through the origin of $\mathbb R^{n+1}$.
- Paths and connectivity (book pp. 139–141, PDF 78–79): path = continuous $\tau:[0,1]\to X$; path-connected (the topologist's sine curve is connected but not path-connected, (4.8)). **Homotopic paths** = continuous deformation with fixed endpoints (4 conditions, p. 140). Simply vs multiply connected.
- Fundamental group $\pi_1$ (book pp. 142–144, PDF 79–80):
  - $\pi_1(\mathbb S^1)=\mathbb Z$;
  - $\pi_1(T^n)=\mathbb Z^n$ (also the plane with $n$ holes, i.e. a mobile robot among $n$ obstacles);
  - $\pi_1(\mathbb{RP}^2)=\pi_1(\mathbb{RP}^3)=\mathbb Z_2$, so there are **only two homotopy classes of loops in SO(3)** (the belt trick);
  - $\pi_1(\mathbb S^2)=1$.

### §4.2 Defining $\mathcal C$ (book pp. 145–155, PDF 81–86)
- Matrix groups (book pp. 145–147, PDF 81–82): $SO(n)\le O(n)\le GL(n)$. $\dim O(n)=n^2-n(n+1)/2=n(n-1)/2$. $O(2)$ = two circles; $\det=1$ selects $SO(2)\cong\mathbb S^1$ (Example 4.12).
- $SE(2)$: $\mathcal C=\mathbb R^2\times\mathbb S^1$ (4.17) (book p. 148, PDF 82). **Planners must know about the wrap-around** (Fig. 4.8: solvable only through the identification).
- Unit complex numbers $a+bi\leftrightarrow\begin{pmatrix}a&-b\\b&a\end{pmatrix}$ (4.18) (book p. 150, PDF 83): rotations become polynomial with no trig. This trick is used throughout Ch. 4 and 6.
- **Quaternions** (book pp. 150–153, PDF 83–85):
  - product (4.19), or in vector form $(a_1a_2-v_1\cdot v_2,\ a_1v_2+a_2v_1+v_1\times v_2)$;
  - unit quaternion → rotation matrix (4.20), diagonal $2(a^2+b^2)-1$, etc.;
  - axis–angle $h=\cos\frac\theta2+\sin\frac\theta2(v_1i+v_2j+v_3k)$ (4.21);
  - $R(h)=R(-h)$, so $SO(3)\cong\mathbb S^3/(h\sim-h)=\mathbb{RP}^3$;
  - hemisphere rule for uniqueness: $a>0$, or $a=0,b>0$, … (book p. 152, PDF 84);
  - rotate a point by $h\,p\,h^*$ with $p=0+xi+yj+zk$ (book p. 153, PDF 85);
  - matrix → quaternion (4.24–4.30): $a=\tfrac12\sqrt{r_{11}+r_{22}+r_{33}+1}$, $b=(r_{32}-r_{23})/4a$, $c=(r_{13}-r_{31})/4a$, $d=(r_{21}-r_{12})/4a$.
- $SE(3)$: $\mathcal C=\mathbb R^3\times\mathbb{RP}^3$ (4.31) (book p. 154, PDF 85).
- Chains (book pp. 154–155, PDF 85–86):
  - planar $n$-revolute arm $\mathcal C=T^n$ (4.33); with joint limits $\theta_i\in(-\pi/2,\pi/2)$, $\mathcal C=\mathbb R^n$;
  - each prismatic or screw joint contributes $\mathbb R$; cylindrical $\mathbb R\times\mathbb S^1$; planar $\mathbb R^2\times\mathbb S^1$; spherical $\mathbb{RP}^3$ in theory, $\mathbb R^3$ or $\mathbb R^2\times\mathbb S^1$ in practice.
  - A wrist modelled as three DH revolutes has the same topology problems as Euler angles.

### §4.3 Configuration-space obstacles (book pp. 155–167, PDF 86–92)
- $\mathcal C_{obs}=\{q\in\mathcal C\mid\mathcal A(q)\cap\mathcal O\ne\emptyset\}$ (4.34) (book p. 155, PDF 86). It is **closed**, so $\mathcal C_{free}=\mathcal C\setminus\mathcal C_{obs}$ is open. Touching counts as collision (4.35). Shortest-path problems therefore use $\mathrm{cl}(\mathcal C_{free})$.
- Multi-link robot (book p. 156, PDF 86): add self-collision over **collision pairs** $P$ (4.36). Adjacent links are usually excluded. This is exactly MoveIt's Allowed Collision Matrix.
- **Formulation 4.1, Piano Mover's Problem** (book pp. 157–158, PDF 87): $\mathcal W,\mathcal O,\mathcal A$ semi-algebraic; $\mathcal C$; $q_I,q_G\in\mathcal C_{free}$; a complete algorithm returns a continuous $\tau:[0,1]\to\mathcal C_{free}$ or correctly reports failure. **PSPACE-hard** (Reif).
- **Translation only** (book pp. 158–164, PDF 87–90): Minkowski difference $\mathcal C_{obs}=\mathcal O\ominus\mathcal A(0)=\mathcal O\oplus(-\mathcal A)$ (4.37). 1-D check (Example 4.13, p. 158): $\mathcal A=[-1,2]$, $\mathcal O=[0,4]$ ⇒ $\mathcal C_{obs}=[-2,5]$.
- **Star algorithm** (book pp. 159–161, PDF 88–89): for convex $\mathcal A$ and $\mathcal O$, $\mathcal C_{obs}$ is a convex polygon with $n+m$ edges. Merge the inward normals of $\mathcal A$ with the outward normals of $\mathcal O$ by angle; this costs $O(n+m)$ because both lists are already sorted.
- **Half-plane form** (book pp. 161–163, PDF 89–90):
  - Type **EV** = edge of $\mathcal A$ touches a vertex of $\mathcal O$; type **VE** = vertex of $\mathcal A$ touches an edge of $\mathcal O$.
  - Each gives a line $n\cdot v(x_t,y_t)=0$ and a half-plane $H=\{f\le0\}$; $\mathcal C_{obs}=\bigcap H$.
  - Worked table: Example 4.15 / Fig. 4.19 (book p. 163, PDF 90), triangle $(1,0),(0,1),(-1,-1)$ against the square $[-1,1]^2$, giving 7 half-planes. Redo this by hand; it is the classic exam question.
- 3D polyhedra (book pp. 163–164, PDF 90): contact types FV, VF, EE; $O(n+m+k)$ with $k\le nm$ faces.
- **With rotation** (book pp. 164–167, PDF 90–92):
  - an EV contact applies only when $n(\theta)\cdot v_1\ge0$ and $n(\theta)\cdot v_2\ge0$; build $H_A=H_1\cup H_2\cup H_3$ (4.39–4.41) and intersect over all contacts;
  - use $(a,b)$ instead of $\theta$ (4.42), so $\mathcal C_{obs}$ is a **linear polytope in $\mathbb R^4$ intersected with the cylinder $a^2+b^2=1$** (Example 4.16: 12 EV + 12 VE contacts × 3 + 1 = **73 primitives** for a triangle and a square);
  - in 3D, quaternions give polynomial patches in 7 variables plus the unit constraint.

### §4.4 Closed kinematic chains (book pp. 167–180, PDF 92–98) — solid
- Fields, polynomials, **varieties** $V(f_1,\dots,f_k)=\{a\mid f_i(a)=0\ \forall i\}$ (4.53) (book p. 170, PDF 93). Intersection = union of the polynomial lists (4.54); union = pairwise products.
- **Closure varieties need not be manifolds**, and every compact real algebraic variety is the C-space of some linkage (book p. 168, PDF 92).
- Two-link chain, point $(1,0)\in\mathcal A_2$ held at $(1,1)$ (book p. 172, PDF 94): $V(a_1a_2-b_1b_2+a_1-1,\ a_1b_2+a_2b_1+b_1-1,\ a_1^2+b_1^2-1,\ a_2^2+b_2^2-1)$ (4.64). Two points: $(\theta_1,\theta_2)=(0,\pi/2),(\pi/2,-\pi/2)$.
- Holding only $x$ (book pp. 173–174, PDF 95, Fig. 4.23):
  - $f_1=0$ → two circles touching at $(\pi,\pi)$ (not a manifold);
  - $f_1=1/8$, $f_1=1$ → circles;
  - $f_1=2$ → a point;
  - $f_1>2$ → empty.
- Three links: Figs. 4.24–4.26 (book pp. 175–178, PDF 96–97). A four-bar ($f_1=1,f_2=0$) gives line segments in $(\theta_1,\theta_2,\theta_3)$, e.g. $(0,2t,\pi)$, $t\in[0,\pi]$.
- **General recipe** (book pp. 176–180, PDF 96–98):
  1. linkage graph (vertex = link, edge = joint);
  2. choose a root;
  3. spanning tree;
  4. tree transforms;
  5. closure constraints for each non-tree edge (2 per planar point, 3 per spatial point; up to 3 or 6 to fix a body);
  6. replace trig by complex numbers or quaternions;
  7. list $f=0$.
- Upper bound: $\dim\le m-l$. Kutzbach / **Grübler** (book p. 180, PDF 98). Choset ch. 3 has the formula.

**Exercises worth doing (book pp. 181–184, PDF 99–100):**
- **5** (C-space of an axially symmetric rod: $\mathbb R^3\times\mathbb S^2$, or $\mathbb R^3\times\mathbb{RP}^2$ if the two ends are indistinguishable);
- **8** (Asteroids ship: $T^2\times\mathbb S^1$);
- **10** (car on a sphere: $SO(3)$, not $\mathbb S^2\times\mathbb S^1$);
- **11** (Minkowski/star algorithm for two equilateral triangles, list contact types: exam-style);
- **12** (check (4.20) is a rotation);
- **14** (quaternion composition by hand);
- **16** (five free bodies: $\mathbb R^{15}\times(\mathbb{RP}^3)^5$, dim 30);
- **22** ($\dim SO(4)=6$);
- **26** (implement the star algorithm, then rotation slices: this builds a C-obstacle visualiser in C++ for A3).

**Pitfalls (ch. 4):**
- Never sample or interpolate SO(3) through Euler angles. Use quaternions and respect $h\sim-h$.
- Grid/A* planners on revolute arms must wrap angles (torus neighbours).
- Book formulas (4.24–4.30) are numerically fragile near $a\approx0$ ($\theta\approx\pi$). In code, use Shepperd's method (pick the largest of $\mathrm{tr}R$ and the $r_{ii}$) or Eigen's `Quaterniond(R)`.
- Fig. 4.19's "n" column for EV rows is $\mathcal A$'s **outward** edge normal (sign chosen so $f\le0$ contains $\mathcal C_{obs}$), even though sorting used inward normals.

---

## Ch. 5 Sampling-Based Motion Planning (PDF 101–132, book pp. 185–248) — Lessons: A5 (core; **Assignment 3**), A3 (collision checking)
**Big idea:** Hide the geometry behind a collision-detection oracle, sample $\mathcal C$, and run a discrete search on the samples. Completeness is traded for **resolution completeness** (a dense deterministic sequence) or **probabilistic completeness** (random sampling, dense with probability 1) (book pp. 185–186, PDF 101). The quality of the sample sequence (dispersion/discrepancy), the metric, and the edge-checking routine decide performance.

### §5.1 Distance and volume in $\mathcal C$ (book pp. 186–195, PDF 101–106)
- **Metric axioms and metrics** (book pp. 187–188, PDF 102): nonnegativity, reflexivity, symmetry, triangle inequality. $L_p$ metric (5.1), $L_\infty=\max_i\lvert x_i-x'_i\rvert$ (5.2). Subspace metrics are inherited. Product metric $c_1\rho_X+c_2\rho_Y$ (5.4) or the $L_p$-combination (5.5).
- **Metrics for motion planning** (book pp. 188–191, PDF 102–104):
  - $SO(2)$: chord (5.6) or arc $\min\{\lvert\theta_1-\theta_2\rvert,2\pi-\lvert\theta_1-\theta_2\rvert\}$ (5.7) $=\cos^{-1}(a_1a_2+b_1b_2)$ (5.8).
  - $SO(3)$ (Example 5.4): $\rho(h_1,h_2)=\min\{\lVert h_1-h_2\rVert,\lVert h_1+h_2\rVert\}$ (5.9), or the geodesic on $\mathbb S^3$ $\rho_s=\cos^{-1}(h_1\cdot h_2)$ (5.10) with $\rho=\min\{\rho_s(h_1,h_2),\rho_s(h_1,-h_2)\}$ (5.11), i.e. $\cos^{-1}\lvert h_1\cdot h_2\rvert$.
  - $SE(2)$ / $SE(3)$: weighted sums; the units clash (Example 5.5).
  - **Robot displacement metric** $\rho(q_1,q_2)=\max_{a\in\mathcal A}\lVert a(q_1)-a(q_2)\rVert$ (5.12) (Example 5.6): ideal but expensive.
  - $T^n$ metric weights: weight proximal joints more (Example 5.7).
  - **Pseudometrics** (book p. 191, PDF 104): cost-to-go functions, asymmetric (car can't reverse), potentials.
- Measure: σ-algebra, Borel sets, Lebesgue measure (book pp. 191–193, PDF 104–105).
- **Haar measure** $\mu(A)=\mu(gA)=\mu(Ag)$ (book pp. 193–195, PDF 105–106): for SO(3), uniform on $\mathbb S^3$ **is** Haar. Uniform Euler angles are biased.

### §5.2 Sampling theory (book pp. 195–209, PDF 106–113)
- **Dense sequence** (book p. 195, PDF 106): $\mathrm{cl}(U)=V$. A random sequence is dense with probability 1: $\Pr(\text{interval of length }e\text{ missed by }k\text{ samples})=(1-e)^k\to0$ (book p. 196, PDF 106).
- **van der Corput** (book pp. 196–197, PDF 106–107, Fig. 5.2): $\nu(i)$ = bit-reversal of $i$ about the radix point: $0,\tfrac12,\tfrac14,\tfrac34,\tfrac18,\tfrac58,\dots$ Perfectly spaced when $i$ is a power of 2. Dispersion $\le1/k$.
```
VAN_DER_CORPUT(i, base=2):  r ← 0; f ← 1/base
    while i > 0:  r ← r + f*(i mod base); i ← ⌊i/base⌋; f ← f/base
    return r                    # base p for coordinate j gives the Halton sequence (5.24)–(5.25)
```
- **Uniform random SO(3)** (Shoemake, book p. 198, PDF 107): $u_1,u_2,u_3\sim U[0,1]$,
  $$h=\big(\sqrt{1-u_1}\sin2\pi u_2,\ \sqrt{1-u_1}\cos2\pi u_2,\ \sqrt{u_1}\sin2\pi u_3,\ \sqrt{u_1}\cos2\pi u_3\big)\quad(5.15)$$
  Negate $h$ if $a<0$ to stay in one hemisphere. Component order does not affect uniformity, but match your library's scalar-first or scalar-last storage. Intuition: equal-thickness slices of a sphere have equal area, plus the Hopf fibration (book pp. 198–199, PDF 107–108).
- **Random directions** (book p. 199, PDF 108): normalise a vector of i.i.d. zero-mean Gaussians, not of uniforms, which bias toward cube corners.
- Pseudorandom numbers: LCG $y_{i+1}=ay_i+c \bmod M$ (5.16) shows correlation across products; prefer the Mersenne twister (`std::mt19937`). The χ² box test (5.18) rejects samples that are too uniform to be random (book p. 200, PDF 108).
- **Dispersion** $\delta(P)=\sup_{x\in X}\min_{p\in P}\rho(x,p)$ (5.19) (book p. 201, PDF 109) is the radius of the largest empty ball (a cube for $L_\infty$), equal to the largest Voronoi-vertex disc. LaValle calls it a **generalisation of grid resolution**.
- **Sukharev grid and bound** (book p. 203, PDF 110): $\delta(P)\ge\frac1{2\lfloor k^{1/d}\rfloor}$ (5.20). Points sit at cube centres; $k=9$ in $[0,1]^2$ gives coordinates $\{1/6,1/2,5/6\}$ and dispersion $1/6$ (Example 5.15). On a torus the grid can be shifted to start at 0.
- Lattices $x=\sum k_jg_j$ (5.21) (book p. 204, PDF 110): neighbours by adding generators.
- **Multi-resolution grids** (book pp. 204–205, PDF 110–111): use $n$ bits at a time to choose the orthant. A full grid appears only after $2^{ni}$ points ($n=10$: $1,2^{10},2^{20},\dots$), so partial grids are needed. Asymptotic dispersion is $O(k^{-1/n})$ with constants 1 (van der Corput), $3/2$ (multi-resolution Sukharev), $1/\ln4$ (best known).
- **Discrepancy** $D(P,\mathcal R)=\sup_{R\in\mathcal R}\big\lvert\frac{\lvert P\cap R\rvert}k-\frac{\mu(R)}{\mu(X)}\big\rvert$ (5.22) over axis-aligned boxes (book p. 206, PDF 111):
  - best $O(k^{-1}\log^nk)$ for sequences and $O(k^{-1}\log^{n-1}k)$ for fixed-size sets;
  - $\delta(P,L_\infty)\le D(P,\mathcal R)^{1/d}$ (5.23): low discrepancy ⇒ low dispersion, not conversely (book p. 207, PDF 112).
- Halton $r(i,p)=\frac{a_0}p+\frac{a_1}{p^2}+\cdots$ per coordinate with coprime bases (5.24–5.25). Hammersley $(i/k,r(i,p_1),\dots)$ for known $k$ (5.26) (book pp. 207–208, PDF 112). Constants blow up beyond about 10 dimensions.
- $(t,s)$-sequences (Sobol', Faure, Niederreiter–Xing). Lattice $(i/k,\{i\alpha_1\},\dots)$ with $\alpha$ irrational, e.g. the golden ratio (5.27) (book pp. 208–209, PDF 112–113).

### §5.3 Collision detection (book pp. 209–217, PDF 113–117) — the "black box", opened
- Predicate $\phi:\mathcal C\to\{\text{true},\text{false}\}$ vs a **distance** $d(q)=\min_{e\in E,f\in F}\lVert e-f\rVert$ (5.28). Distance is slower but enables safe stepping (book pp. 209–210, PDF 113).
- **Two-phase** (book p. 210, PDF 113): a **broad phase** (bounding boxes, hashing, collision pairs) then a **narrow phase**.
- **Hierarchical bounding volumes** (book pp. 210–212, PDF 113–114):
  - candidates, from loose and cheap to tight and expensive: sphere, AABB, OBB, convex hull (Fig. 5.9);
  - build the tree by splitting primitives into halves (Fig. 5.10);
  - descend both trees only where volumes overlap; test triangle pairs at the leaves;
  - for distance queries, prune with the best distance so far, which requires each child volume to lie inside its parent.
  - This is what FCL (MoveIt) and PQP do.
- **Incremental (Lin–Canny / V-Clip)** (book pp. 212–214, PDF 114–115): Voronoi regions of the **features** (vertices and edges; faces too in 3D) of convex polygons. Closest-feature pairs are VV, EV or EE. If $p_1\in\mathrm{Vor}(F_2)$ and $p_2\in\mathrm{Vor}(F_1)$ (5.29), the feature distance is the polygon distance. "Almost constant time" when the motion between calls is small. Needs coherent (watertight) models.
- **Checking a path segment** (book pp. 214–217, PDF 115–117):
  - Practical: a fixed step $\Delta q$ in the metric $\rho$; too large lets the robot tunnel through thin obstacles.
  - **Guaranteed version:** if $\mathcal A(q)$ is at least $d$ from obstacles and no point of $\mathcal A$ moves more than $d$, the whole motion is free.
  - For $SE(2)$ with $r$ the largest radius on $\mathcal A$: $\lVert a(\theta)-a(\theta')\rVert<r\lvert\theta-\theta'\rvert$ (5.30), so the box $\lvert\Delta x\rvert+\lvert\Delta y\rvert+r\lvert\Delta\theta\rvert<d$ (5.31) is free.
  - Chains: Lipschitz bound $\lVert a(q)-a(q')\rVert<c\lVert q-q'\rVert$ (5.32), better **per-joint** $\sum_ic_i\lvert q_i-q'_i\rvert$ (5.33–5.34). Proximal joints get small steps. Bounds are configuration dependent (a folded chain moves less), and a local Jacobian bound is tighter.
  - **Order:** check $\tau(1)$, then the points in van der Corput order $\tau(0),\tau(\tfrac12),\tau(\tfrac14),\tau(\tfrac34),\dots$ (recursive bisection) until the dispersion falls below $\Delta q$. This finds collisions earlier when there are any.

### §5.4 Incremental sampling and searching (book pp. 217–228, PDF 117–122)
- **Template** (book pp. 217–218, PDF 117), the §2.2.4 template with actions replaced by path segments:
  1. initialise $G$ with $q_I$ and/or $q_G$;
  2. **VSM** (vertex selection method);
  3. **LPM** (local planning method: a collision-checked segment from $q_{cur}$ to $q_{new}$);
  4. insert the edge;
  5. check for a solution;
  6. repeat.
  $G$ is an undirected topological graph.
- Hard cases (Fig. 5.13, book p. 219, PDF 118): a bowl (grid search fills about $100^n$ cells), a **bug trap** (backward or bidirectional search helps), a double bug trap (multi-directional search), and a hopeless corridor. Unidirectional, bidirectional and multi-directional methods are discussed on book pp. 219–220 (PDF 118–119).
- **Grid adaptation** (book pp. 220–224, PDF 118–120):
  - reparameterise to $[0,1]^n/\!\sim$;
  - steps $\Delta q_i$ (5.35); grid points $\sum j_i\Delta q_i$ (5.36);
  - **1-neighbourhood** has $2n$ neighbours (5.37); the 2-neighbourhood (5.38) adds diagonals; the $n$-neighbourhood has $3^n-1$;
  - connect $q_I$ and $q_G$ to nearby grid points; collision-check lazily during search.
  - Resolution choices: doubling ($2^n,4^n,\dots$) grows too fast. Iterative-deepening grids ($i$ points per axis: $2^n,3^n,4^n,\dots$) are better. Best is a dense sequence plus **union-find** connectivity, checked as each point is added.
- **Randomized potential field** (RPP, book pp. 224–227, PDF 120–122):
  - three modes (Fig. 5.15): BEST FIRST (descend $g(q)$) → RANDOM WALK when stuck (each coordinate $\pm\Delta q_i$ by coin flip, random length) → BACKTRACK to a random random-walk vertex after $K\approx20$ failures;
  - solved 31-DOF problems but needs heavy tuning;
  - **Shortcutting** (5.39): pick random $t_1<t_2$ and replace $\tau|_{[t_1,t_2]}$ by a straight segment if it is free.
- **Other methods** (book pp. 227–228, PDF 122):
  - **Ariadne's Clew**: explore (genetic optimisation places a vertex far from the others) and search (try to reach the goal).
  - **Expansive-space planner** (Hsu et al.):
    - VSM picks $v_e$ with probability ∝ 1/(number of vertices near $v_e$);
    - the LPM samples $v_n$ near $v_e$ and **keeps it with probability ∝ 1/(number of vertices near $v_n$)**;
    - the same vertex is reused for a fixed number of iterations;
    - bidirectional;
    - the neighbourhood radius is effectively a resolution parameter.
  - **Random-walk planner** with adaptive Gaussian steps.

### §5.5 Rapidly exploring dense trees (RDTs) (book pp. 228–237, PDF 122–127) — LaValle's own formulation of RRT
- RDT = tree grown toward any **dense** sequence $\alpha$. It is an RRT when $\alpha$ is random. **No step-size parameter**: connect all the way to $\alpha(i)$, or to the stopping configuration (book pp. 228–229, PDF 122–123; the footnote notes the original 1998 RRT had a step size).
- **Swath** $S=\bigcup_{e\in E}e([0,1])$ (5.40): the nearest point may lie **inside an edge**, which is then split (Fig. 5.18, book p. 230).

**Fig. 5.16 SIMPLE_RDT (no obstacles, book p. 229, PDF 123) and Fig. 5.21 RDT (book p. 232, PDF 124)**
```
RDT(q0):
    G.init(q0)
    for i = 1..k:
        q_n ← NEAREST(S(G), α(i))                    # nearest point of the swath (vertex or edge interior)
        q_s ← STOPPING-CONFIGURATION(q_n, α(i))      # last free config on the segment toward α(i)
        if q_s ≠ q_n:
            G.add_vertex(q_s); G.add_edge(q_n, q_s)   # split an edge if q_n was interior
```
- Fig. 5.19 (book p. 230, PDF 123): fractal-like growth; the main branches reach the far corners first. Voronoi bias.
- **Nearest neighbours** (book pp. 232–234, PDF 124–125):
  - *exact*: point-to-segment distance over agglomerated "supersegments" (on SO(3), arcs or a cube-surface embedding);
  - *approximate* (simplest, most used): insert intermediate vertices every $\Delta q$ and use vertices only. This puts it back inside the §5.4.1 template.
  - **Kd-tree** (Fig. 5.23, book p. 234, PDF 125): median splits cycling through axes; build $O(nk\lg k)$; query about $O(\lg k)$ but the constant is exponential in $n$. Useful up to about 20 dimensions. Rule of thumb: worth it when there are more than $2^n$ points. Wrap-around topologies need care.
- **Planning** (book pp. 235–237, PDF 126–127):
  - single tree with $\alpha(i)=q_G$ every 100th sample, or with **probability 1/100** (the slides and Choset use about 0.05);
  - too much bias turns RRT into RPP.

**Fig. 5.24 RDT_BALANCED_BIDIRECTIONAL (book p. 236, PDF 126)**
```
T_a.init(q_I); T_b.init(q_G)
for i = 1..K:
    q_n ← NEAREST(S_a, α(i)); q_s ← STOPPING-CONFIGURATION(q_n, α(i))
    if q_s ≠ q_n:
        T_a.add_vertex(q_s); T_a.add_edge(q_n, q_s)
        q'_n ← NEAREST(S_b, q_s); q'_s ← STOPPING-CONFIGURATION(q'_n, q_s)   # T_b grows toward the NEW vertex
        if q'_s ≠ q'_n:
            T_b.add_vertex(q'_s); T_b.add_edge(q'_n, q'_s)
        if q'_s = q_s: return SOLUTION
    if |T_b| > |T_a|: SWAP(T_a, T_b)        # balance: always explore with the smaller tree
return FAILURE
```
- Size can be measured as the vertex count or the total edge length. Swapping every iteration gives the unbalanced variant. With a deterministic $\alpha$, give each tree its own dense sequence (book p. 236). More than two trees: open research (book pp. 236–237, PDF 126–127).

### §5.6 Roadmap methods for multiple queries (book pp. 237–244, PDF 127–130)
- LaValle calls PRMs **sampling-based roadmaps**; the "probabilistic" part is inessential. There is a preprocessing phase and a query phase (book p. 237, PDF 127).

**Fig. 5.25 BUILD_ROADMAP — corrected indexing (book p. 238, PDF 127; see errata)**
```
G.init(); j ← 0                                   # j = sample index, |V| = vertex count
while |V| < N:
    q ← α(j); j ← j + 1
    if q ∈ C_free:
        G.add_vertex(q)
        for q' ∈ NEIGHBORHOOD(q, G) sorted by ρ(q, q'):
            if (not G.same_component(q, q')) and CONNECT(q, q'):   # union-find, near-constant time
                G.add_edge(q, q')
```
- The `same_component` test builds a **forest**: each successful edge merges two components, so no collision check is wasted. To keep alternative homotopy classes, replace it with `vertex_degree(q') < K` ($K=15$) (book p. 239, PDF 128). CONNECT: a straight segment checked in van der Corput order.
- **NEIGHBORHOOD** options (book p. 239, PDF 128):
  1. **nearest $K$**, $K\approx15$ ("if unsure, use this");
  2. component $K$, with $K=1$ per connected component;
  3. radius $r$, capped at $K\approx20$, shrinking with dispersion;
  4. all vertices (the visibility roadmap).
- Query (book p. 240, PDF 128): treat $q_I$ and $q_G$ as two more samples, then graph search. Failure is not proof of infeasibility; with known dispersion, only corridors narrower than the largest empty ball can be missed.
- **ε-goodness** $\epsilon(\mathcal C_{free})=\min_q\mu(V(q))/\mu(\mathcal C_{free})$ (5.41), with $V(q)$ the set visible to the LPM. Bounds follow but are conservative (book pp. 240–241, PDF 128–129).
- **Visibility roadmap** (book pp. 241–242, PDF 129):
  - a sample that sees no guard becomes a **guard**;
  - a sample that sees guards from at least two components becomes a **connector**;
  - otherwise it is discarded.
  - Very small roadmaps; order dependent; still probabilistically or resolution complete.
- **Heuristics** (book pp. 242–244, PDF 129–130):
  - **vertex enhancement**: statistic $n_f/(n_t+1)$ (failed/total connection attempts), $P(v)=\frac{n_f/(n_t+1)}{m}$, then random motions from $v$;
  - boundary sampling by **binary search** along random directions from a colliding sample (Fig. 5.29a);
  - Gaussian sampling;
  - **bridge test**;
  - medial-axis sampling (retract samples away from $\partial\mathcal C_{free}$, or use the workspace medial axis).

**Exercises worth doing (book pp. 246–248, PDF 131–132):**
- Proofs: **2–3** (is $\rho^2$ a metric? the discrete metric), **7** (Haar measure on $\mathbb S^3$), **9** (dispersion of base-$p$ van der Corput), **14** (bound point displacement from a quaternion change: the SO(3) version of (5.30), useful for safe edge checking in a 6-DOF A5 planner), **16–17** (count and choose $k$-neighbours in high-dimensional grids), **19** (tighter version of (5.31)).
- Implementations: **22** (sampling-based roadmap; compare visibility roadmap, neighbourhood rules, random vs deterministic samples, bridge test), **23** (balanced bidirectional RRT; vary intermediate vertices; use a kd-tree), **25** (**expansive-space planner vs RRT with fixed parameters**, the closest book exercise to Assignment 3). Build them first in Python, then port the core to C++ with `std::mt19937` and nanoflann/FLANN.

**Pitfalls (ch. 5):**
- LaValle's RDT has **no step size** and connects to the nearest point of the **swath** (an edge interior counts). The slides (LaValle & Kuffner 1999) and Choset use a step-size, vertex-only RRT. Both are "RRT", so state which one you mean.
- Goal bias: LaValle 1/100, Choset and slides about 0.05. Both are heuristics.
- LaValle's expansive-space planner also rejects $v_n$ with probability ∝ local density. Choset's EST only weights the selection. Assignment 3 uses $1/(1+\deg)$. Know all three.
- Resolution completeness in LaValle means a dense deterministic sequence. It may still run forever when no solution exists.
- A fixed $\Delta q$ on joint angles is not a workspace bound: proximal joints sweep the end effector farthest, so use per-joint steps (5.33).
- The `same_component` rule gives a forest: fast connectivity, bad path quality. Add redundant edges or shortcut.

---

## Ch. 6 Combinatorial Motion Planning (PDF 133–163, book pp. 249–309) — Lessons: A4 (exact roadmaps, cell decompositions), A3 (C-obstacles), A8 (complexity facts for the exam)
**Big idea:** Build an **exact** discrete representation (cell decomposition → roadmap) of $\mathcal C_{free}$, then search it. The result is **complete**: it finds a path or proves none exists. Elegant and fast for 2-D polygonal cases; general but impractical (CAD, Canny) in high dimension. Lower bounds (PSPACE-hard) explain why Ch. 5 exists.

### §6.1 Roadmap definition (book pp. 249–251, PDF 133–134)
A topological graph $G$ with swath $S$ is a **roadmap** if:
1. **accessibility**: any $q\in\mathcal C_{free}$ can be connected simply and efficiently to some $s\in S$;
2. **connectivity-preserving**: if $q_I$ and $q_G$ are connected in $\mathcal C_{free}$, their access points are connected in $S$.

Choset uses three conditions (accessibility, departability, connectivity); LaValle merges the first two.

### §6.2 Polygonal $\mathcal C_{obs}$ in the plane (book pp. 251–264, PDF 134–140)
- **Representation** (book pp. 251–253, PDF 134–135): Minkowski differences per convex pair, merged into simple polygons. Doubly connected edge list with the obstacle always to the left of a half-edge: counterclockwise around obstacles, clockwise around holes.
- **Vertical cell decomposition** (book pp. 253–259, PDF 135–138):
  - at each vertex, extend rays up and/or down until they hit $\mathcal C_{obs}$ (4 cases, Fig. 6.2). 2-cells are trapezoids or triangles, 1-cells are the vertical segments; there are no 0-cells (they lie in $\mathcal C_{obs}$);
  - **roadmap: one sample point per 2-cell AND per 1-cell**, edges from each 2-cell sample to its boundary 1-cell samples (Fig. 6.4);
  - query: locate the cells of $q_I$ and $q_G$, search, "connect the dots" (Fig. 6.5). The footnote (p. 257) says this fixes Latombe's version, which could collide;
  - **plane sweep**: sort vertices by $x$ ($O(n\lg n)$), keep the edges stabbed by the sweep line in a balanced BST (event table Fig. 6.7, book p. 259, PDF 138), $O(\lg n)$ per event, **$O(n\lg n)$ total** (brute force $O(n^2)$);
  - general position: no two vertices with equal $x$ and no vertical edges (perturb, or handle explicitly as in Ex. 1).
- **Maximum-clearance roadmap = generalized Voronoi diagram / retraction** (book pp. 260–261, PDF 138–139):
  - deformation retract (3 conditions on the homotopy $h$);
  - pieces: **edge–edge → line, vertex–vertex → line (bisector), vertex–edge → parabola** (Fig. 6.9);
  - naive $O(n^4)$ over feature pairs; best $O(n\lg n)$;
  - use it when localisation is poor (mobile robots).
- **Shortest-path roadmap = reduced visibility graph** (book pp. 261–264, PDF 139–140):
  - vertices = **reflex vertices** (interior angle in $\mathcal C_{free}$ greater than $\pi$; every vertex of a convex obstacle qualifies);
  - edges = consecutive reflex vertices along $\partial\mathcal C_{obs}$, plus **bitangents** (mutually visible, and the line extends past both without entering $\mathcal C_{obs}$);
  - query: connect $q_I$ and $q_G$ to every visible roadmap vertex, then Dijkstra with Euclidean edge costs gives the shortest path in $\mathrm{cl}(\mathcal C_{free})$;
  - cost: naive $O(n^3)$; radial sweep $O(n^2\lg n)$; optimal $O(n\lg n+m)$; $O(n)$ inside a simple polygon.
  - **Trig-free predicates** (book p. 264, PDF 140): left turn $f_l(p_1,p_2,p_3)$ ⇔ $\det\begin{pmatrix}1&x_1&y_1\\1&x_2&y_2\\1&x_3&y_3\end{pmatrix}>0$ (6.2). $p_2$ is reflex iff $f_l(p_{prev},p_2,p_{next})$. $(p_2,p_5)$ is a bitangent iff
    $$\big(f_l(p_1,p_2,p_5)\oplus f_l(p_3,p_2,p_5)\big)\vee\big(f_l(p_4,p_5,p_2)\oplus f_l(p_6,p_5,p_2)\big)$$
    is **false** (6.1), with $p_1,p_3$ the neighbours of $p_2$ and $p_4,p_6$ the neighbours of $p_5$, assuming segment $p_2p_5$ is free.

```
SHORTEST_PATH_ROADMAP(polygons):
    R ← {v : v reflex}                                   # left-turn test at each vertex
    E ← {(v, next(v)) : v, next(v) ∈ R and they share a C_obs edge}
    for (u, w) ∈ R×R:  if segment uw free and BITANGENT(u, w) by (6.1): E ← E ∪ {(u,w)}
    query: add q_I, q_G with edges to all visible v ∈ R; DIJKSTRA with |uw| edge costs
```

### §6.3 Cell decompositions (book pp. 264–280, PDF 140–148)
- **Complexes** (book pp. 265–268, PDF 141–142):
  - $k$-simplex $=\{\sum\alpha_ip_i\mid\alpha_i\ge0,\sum\alpha_i=1\}$ (6.3); a simplicial complex needs faces included and intersections equal to common faces;
  - **singular complex**: cells are images of open balls under continuous maps; each $S_k$ is closed and each $k$-cell is open in $S_k$;
  - the vertical decomposition is a singular, not simplicial, complex (Example 6.1).
- 2-D alternatives (book pp. 268–270, PDF 142–143):
  - finding the minimum convex decomposition with holes is NP-hard;
  - triangulation: naive $O(n^3)$, $O(n\lg n)$ via monotone pieces, $O(n)$ (Chazelle) for simple polygons but impractical;
  - **cylindrical decomposition**: rays cross everything, so each vertical strip alternates free/obstacle (Figs. 6.18–6.19). It generalises to CAD.
- 3-D vertical decomposition (book pp. 270–273, PDF 143–145): sweep a plane in $x$, keep a 2-D vertical decomposition of each slice, update at vertex events. Needs piecewise-linear $\mathcal C_{obs}$, e.g. a translating polyhedron.
- **Line-segment robot in the plane, $\mathcal C=\mathbb R^2\times\mathbb S^1$** (book pp. 273–280, PDF 145–148):
  - approximate version: $K$ orientation slices with polygonal $\mathcal C_{obs}$ each; not complete;
  - exact version: **radar map** at $(x,y)$, the circular list of free orientation intervals between contact features, $([f_1,f_2],\dots)$ (6.6);
  - critical curves where the list changes (5 types, Figs. 6.24–6.25): within $L$ of an edge, within $L$ of a vertex, edge-extension at reflex vertices, bitangent pairs within $L$, and the **conchoid of Nicomedes** (4th degree, see errata for the sign);
  - noncritical 2-cells lifted to stacks of 3-cells $\{R,[f_i,f_{i+1}]\}$; roadmap edges across shared critical curves, including split/merge of intervals (Fig. 6.27, roadmap Fig. 6.29);
  - $O(n^5)$ cells worst case ($O(n^2)$ curves → $O(n^4)$ regions × $O(n)$ stack); best known $O(n^2\lg n)$.

### §6.4 Computational algebraic geometry (book pp. 280–298, PDF 148–157) — know the ideas, not the algebra
- **Tarski sentences** (book pp. 282–283, PDF 149–150): quantifier-free formulas over polynomial atoms $f\bowtie0$ with $\exists/\forall$. **Decision problem** and **quantifier elimination** (6.14) (output = semi-algebraic set (6.15)). Example 6.3: $\Phi(z)=\exists y\forall x\,(x^2-zx^2-y<0)$ is true iff $z\ge1$.
- Sign-invariant **semi-algebraic decomposition** $\mathrm{sgn}_{\mathcal F}:\mathbb R^n\to\{-1,0,1\}^m$ (book p. 284, PDF 150). It decides a Tarski sentence by checking one point per region. The gingerbread face has 9 regions (Fig. 6.30).
- **Cylindrical algebraic decomposition (Collins)** (book pp. 286–293, PDF 151–155):
  - project $\mathbb R^n\to\mathbb R^{n-1}\to\cdots\to\mathbb R$; cell boundaries come from **folds** (zeros of $\gcd(f,\partial f/\partial x_n)$) and **intersections** (via principal subresultant coefficients);
  - 1-D base: roots $\beta_1<\dots<\beta_k$ of $\prod f_i$ give alternating 1-cells and 0-cells (6.20), with samples $\beta_1-1$, midpoints, $\beta_k+1$;
  - lift cylinders into **sections** $x_i=f_j(p)$ and **sectors** $f_j(p)<x_i<f_{j+1}(p)$ (6.24–6.27);
  - exact real algebraic numbers via Sturm sequences and isolating intervals;
  - roadmap: $n$-cells adjacent through $(n-1)$-cells; use stereographic projection for $\mathbb S^1,\mathbb S^3$;
  - cost polynomial in $m,d$ but **doubly exponential in $n$**: $(md)^{O(1)^n}$.
- **Canny's roadmap** (book pp. 293–298, PDF 155–157):
  - for a manifold defined by $\psi=(f_1..f_k)$ and a generic linear map $g:\mathbb R^n\to\mathbb R^{1\text{ or }2}$, **critical points / silhouettes** are where the Jacobian of $h=(\psi,g)$ (6.28) loses rank;
  - unit sphere with $g=x_1$ → poles $(\pm1,0,0)$; with $g=(x_1,x_2)$ → the equator (Example 6.7);
  - recurse on slices through critical points (the solid torus needs slices through the 4 critical points, Figs. 6.36–6.39);
  - $q_I,q_G$ are made critical points; roadmaps of sign-invariant sets are joined by the Linking Lemma; lies in $\partial\mathcal C_{free}$;
  - **singly exponential** $m^n(\lg m)d^{O(n^4)}$, randomized $d^{O(n^2)}$; no general implementation exists.

### §6.5 Complexity (book pp. 298–307, PDF 157–162)
- Classes P ⊆ NP ⊆ PSPACE ⊆ EXPTIME (P ⊊ EXPTIME). X-hard / X-complete. **PSPACE-hard ⇒ NP-hard** (book pp. 298–300, PDF 157–158).
- Lower bounds (book pp. 300–302, PDF 158–159):
  - **generalized mover's problem PSPACE-hard (Reif 1979)**: polyhedral bodies with spherical joints simulate a Turing machine; **in PSPACE (Canny)** ⇒ PSPACE-complete;
  - **Warehouseman's problem** (axis-aligned translating rectangles in a box) PSPACE-hard; Sokoban PSPACE-hard; planar chains PSPACE-hard;
  - **3-D Euclidean shortest path among polyhedra NP-hard** (§7.7.1).
  - Bounding the dimension (e.g. $\le2^{1000}$) formally makes the problem polynomial, which is useless in practice.
- **Davenport–Schinzel sequences** (book pp. 302–304, PDF 159–160): no immediate repeats and no alternating $a\,b\,a\,b\dots$ subsequence of length $s+2$. $\lambda_1(n)=n$, $\lambda_2=2n-1$, $\lambda_3=\Theta(n\alpha(n))$ (6.32–6.38), with $\alpha$ the inverse Ackermann function. The lower envelope of $n$ segments has $\Theta(n\alpha(n))$ pieces.
- **Upper bounds** (book pp. 304–307, PDF 160–162):
  - convex $k$-gon translating among convex obstacles with $n$ total edges: $O((n+km)\lg^2n)$;
  - nonconvex: $\Theta(kn\alpha(k))$ for one component, solvable in $O(kn\lg^2n)$;
  - ladder (segment) in the plane: $O(n^2\lg n)$, with paths needing $\Omega(n^2)$;
  - segment in 3-D: $O(n^{4+\epsilon})$;
  - convex polygon in $SE(2)$: $O(kn\lambda_6(kn)\lg kn)$;
  - polyhedron translating in 3-D: one component in $O((kn)^{2+\epsilon})$.

**Exercises worth doing (book pp. 307–309, PDF 162–163):**
- **1** (degenerate cases in the vertical decomposition: the real implementation work);
- **2** (prove the left-turn bitangent test (6.1));
- **6** (derive the conchoid: you will find the sign error);
- **12** (shortest-path roadmap on a flat torus: wrap-around for a 2R arm);
- **13** (implement vertical cell decomposition: the ASBR week-5 roadmap assignment in spirit; reuse the sweep from Choset ch. 6 P2);
- **14** (maximum-clearance roadmap);
- **9** (CAD of three circles by hand: optional).

**Pitfalls (ch. 6):**
- Sample points on 1-cells (not only 2-cell centroids) are needed for collision-free "connect the dots" paths.
- Shortest paths exist only in $\mathrm{cl}(\mathcal C_{free})$; pad the result to get clearance.
- The shortest-path roadmap does not extend to 3-D (paths bend on edge interiors).
- Use determinant predicates, not `atan2`, for robust geometry.
- Canny's roadmap lies on $\partial\mathcal C_{free}$; it decides connectivity but is not a usable path.

---

## Ch. 7 Extensions of Basic Motion Planning (PDF 164–192, book pp. 311–368) — Lessons: A5 (closed-chain/constrained sampling, multi-robot), A4 (coordination grids), A8 (context)
**Big idea:** Many variants reduce to planning in a product state space $X$ with extra structure:
- time with monotonicity;
- several robots with cylindrical obstacles;
- discrete modes layered over $\mathcal C$;
- closure varieties without a parameterisation;
- energies instead of obstacles;
- coverage;
- Pareto-optimal costs.

### §7.1 Time-varying problems (book pp. 311–318, PDF 164–167)
- **Formulation 7.1** (book pp. 312–313, PDF 164–165): $X=\mathcal C\times T$; $X_{obs}=\{(q,t)\mid\mathcal A(q)\cap\mathcal O(t)\ne\emptyset\}$ (7.1); the path must be **time-monotonic**. Piecewise-linear obstacle motion with polygonal $\mathcal C_{obs}(t)$ gives a polyhedral $X_{obs}$ (Example 7.1).
- Sampling methods (book pp. 314–315, PDF 165–166):
  - pseudometric (7.3): $0$ if $q=q'$; $\infty$ if $q\ne q'$ and $t'\le t$; $\rho(q,q')$ otherwise;
  - RDTs grow forward in time; the goal tree is awkward because $X_G$ is a line;
  - roadmaps must be **directed**: no edge when $t=t'$.
- Combinatorial methods: CAD with $t$ projected last; vertical decomposition swept along $t$ first (Fig. 7.2: transitivity breaks otherwise).
- **Bounded speed** (book pp. 315–316, PDF 166): the velocity cone $\lVert\Delta y\rVert^2+\lVert\Delta z\rVert^2\le b^2\Delta t^2$ (7.4). PSPACE-hard even for piecewise-linear obstacles. Roadmap neighbours must lie in the cone.
- **Velocity tuning** (book pp. 317–318, PDF 167): plan a path $\tau$ against static obstacles, then plan a timing $\sigma$ in the 2-D $(t,s)$ space with $X_{obs}=\{(t,s)\mid\mathcal A(\tau(s))\cap\mathcal O(t)\ne\emptyset\}$ (7.5). Moves must go forward in $t$; $s$ may go backward. Complete only relative to $\tau$.

### §7.2 Multiple robots (book pp. 318–327, PDF 167–172)
- **Formulation 7.2** (book pp. 319–320, PDF 168): $X=\mathcal C^1\times\cdots\times\mathcal C^m$ with dimension $\sum\dim\mathcal C^i$ (12 rigid bodies → 72). $X_{obs}=\bigcup_iX^i_{obs}\cup\bigcup_{i\ne j}X^{ij}_{obs}$ (7.7–7.10). Robot–robot obstacles are **cylindrical**: they depend on two coordinates only (Fig. 7.6).
- Assembly planning: NP-hard in general (book pp. 321–322, PDF 169).
- **Decoupled planning** (book pp. 322–327, PDF 169–172):
  - **Prioritized**: plan $\mathcal A^1$, then treat it as a moving obstacle for $\mathcal A^2$, and so on, using §7.1 methods. **Incomplete** (Fig. 7.8).
  - **Fixed-path coordination**: coordination space $X=[0,1]^m$, $q^i=\tau_i(s_i)$, obstacles $X^{ij}_{obs}$ (7.11–7.12). Fully characterised by the $\tfrac12m(m-1)$ 2-D projections (Fig. 7.9). Paths need not be monotone; grid search works well.
  - **Fixed-roadmap coordination**: a **cube complex** with one $m$-cube per combination of edges (Fig. 7.10). With a **garage configuration** for every robot, completeness carries over from the planner on $X$.

### §7.3 Mixing discrete and continuous spaces (book pp. 327–337, PDF 172–177)
- **Formulation 7.3, hybrid** (book pp. 327–329, PDF 172–173): $X=\mathcal C\times M$; mode-dependent $\mathcal O(m)$ and $\mathcal A(m)$; actions change the mode $f_m(x,u)$ with $q$ fixed (no race conditions). The output is a path plus an action trajectory $\sigma$. Examples: the portiernia with keys and doors (12 modes) and a reconfigurable robot (book pp. 329–331).
- **Manipulation planning** (book pp. 332–337, PDF 174–177):
  - $\mathcal C=\mathcal C^a\times\mathcal C^p$ (7.14); remove $\mathcal C^a_{obs},\mathcal C^p_{obs}$ (interior of the part hits $\mathcal O$) and $\mathcal C^{ap}_{obs}$ (7.15–7.17); admissible set $\mathcal C_{adm}$ (7.18);
  - **stable** set $\mathcal C_{sta}$ (7.19), **grasped** set $\mathcal C_{gr}$, $\mathcal C_{free}=\mathcal C_{sta}\cup\mathcal C_{gr}$;
  - modes: **transit** (empty hand, in $\mathcal C_{sta}$) and **transfer** (carrying, in $\mathcal C_{gr}$); switches only in $\mathcal C_{tra}=\mathcal C_{sta}\cap\mathcal C_{gr}$;
  - **manipulation graph**: vertices = connected components of $X_{tra}$, edges = transit/transfer paths, then search. Generalises to many parts (still 2 modes) and to $k$ robots ($2^k$ modes).
  - This is the conceptual model behind MoveIt Task Constructor pick-and-place stages.

### §7.4 Planning for closed kinematic chains (book pp. 337–347, PDF 177–182)
- Closure space $\mathcal C_{clo}=\{q\mid f_i(q)=0\ \forall f_i\in\mathcal P\}$ (7.21) with dimension $m<n$; feasible space $\mathcal C_{fea}=\mathcal C_{clo}\cap\mathcal C_{free}$ (book p. 339, PDF 178). Uniform samples hit it with probability 0. Examples: dual-arm grasps, the Stewart–Gough platform, molecules.
- **RDT on the constraint manifold by continuation** (book pp. 340–343, PDF 178–180), the idea behind OMPL's projection-based constrained planning:
```
STOPPING-CONFIGURATION_CLO(q, α):                 # replaces line 4 of Fig. 5.21
    repeat
        J ← [∂f_i/∂q_j](q)                            # k×n, eq. (7.23)
        e_1..e_m ← orthonormal basis of null(J)        # tangent space via SVD
        v ← α − q;  w ← Σ_i (v·e_i) e_i                # project onto the tangent plane (7.24)
        q ← q + step·w/‖w‖
        while max_i |f_i(q)| > ε:  q ← q − J⁺ f(q)    # Newton step back to C_clo ("normal motion")
    until no progress (w ≈ 0) or collision
    return last valid q
```
  Alternative: thicken to $\lvert f_i\rvert<\epsilon$ and sample directions inside (easier, slower).
- **Active–passive decomposition** (book pp. 343–346, PDF 180–181):
  - sample the $m$ active joints and solve inverse kinematics for the passive ones: 3 passive joints in the plane, 6 in space (= $\dim SE(2)$, $\dim SE(3)$);
  - the **random loop generator** samples active joints sequentially inside reachability intervals $I_i$ from workspace bounds (Fig. 7.26);
  - results up to $\dim\mathcal C=97$, $\dim\mathcal C_{clo}=25$ (Logabex, Fig. 7.27);
  - kinematic singularities are ignored, so completeness is lost.

### §7.5 Folding problems (book pp. 347–354, PDF 182–185) — brief
Carton folding (a tree of panels), knot untangling (the Ochiai unknot), drug design (pharmacophore closure constraints plus the energy (7.25): bonds, angles, torsions, Lennard-Jones, Coulomb), protein-folding pathways via PRMs. Energies act as "soft obstacles".

### §7.6 Coverage planning (book pp. 354–357, PDF 185–187)
- Optimal coverage is NP-hard (via TSP).
- **Boustrophedon cells**: keep only the vertical-decomposition events that extend both up and down (Fig. 7.36). Same as Choset §6.2.
- **Spanning-tree coverage (Gabriely–Rimon)**: tile $\mathcal C_{free}$ with $\epsilon$-squares, build a spanning tree of the square adjacency graph, halve the resolution and circumnavigate the tree (Figs. 7.37–7.38). Optimal for the approximation; online versions exist.

### §7.7 Optimal motion planning (book pp. 357–364, PDF 187–190)
- Euclidean shortest paths (book pp. 357–358, PDF 187–188):
  - in 2-D: shortest-path roadmap, or the **continuous Dijkstra** wavelet method in $O(n\lg n)$;
  - in 3-D: shortest paths cross edge interiors (Fig. 7.39), so the problem is **NP-hard**; approximation schemes exist.
- **Formulation 7.4** (book pp. 359–360, PDF 188): approximate optimal planning with $q'=q+\epsilon u$, stage cost (7.26), and termination $u_T$. This is Formulation 2.3 on a lattice. Motion models (book pp. 360–362, PDF 188–189):
  - Manhattan (2n actions, 1-neighbourhood) — all monotone staircase paths tie;
  - **independent-joint** ($3^n-1$ actions) — natural for revolute arms;
  - Euclidean ($\mathbb S^{n-1}$ actions) — reachable set dense, no grid, needs Ch. 8 interpolation;
  - **weighted-region** costs (terrain, D*/Stentz).
- **Pareto optimality for multiple robots** (book pp. 362–364, PDF 189–190):
  - cost vector $L(\gamma)=(L_1,\dots,L_m)$ (7.28); $\gamma$ **dominates** $\gamma'$ if $L_i(\gamma)\le L_i(\gamma')\ \forall i$ and they are not equivalent;
  - two robots and one corridor give two Pareto plans $(0,t_{off})$ and $(t_{off},0)$;
  - scalarising with $\sum\alpha_iL_i$ ($\alpha_i>0$) (7.29) yields a Pareto plan but hides the alternatives;
  - finitely many unique Pareto plans exist on topological graphs; there is a grid DP algorithm.

**Exercises worth doing (book pp. 365–368, PDF 191–192):**
- **2** (bounded-speed time-varying PRM);
- **4** (sketch the coordination space for Fig. 7.43);
- **10** (four-link closure constraints, polynomial form, velocity constraints: links §4.4 to Jacobians, A1/F5);
- **12–13** (grid coordination of three robots, then Pareto-optimal wavefront: a good C++ DP exercise);
- **14** (bidirectional RRT on the implicit torus (6.40) using the §7.4.1 projection: a rehearsal for constrained planning in MoveIt);
- **18** (fast replanning with moving obstacles).

**Pitfalls (ch. 7):**
- Prioritized and fixed-path coordination are incomplete. Completeness needs the joint space or roadmaps with garages.
- Velocity tuning is complete only for the chosen path.
- Tangent-space stepping drifts, so always re-project and accept a tolerance $\epsilon$.
- Active–passive sampling fails near singularities.
- Weighted sums give only one Pareto point.

---

## Notation: LaValle vs Choset vs ASBR slides
| Thing | LaValle (2006) | Choset (2005) | ASBR slides (Leonard) | Notes |
|---|---|---|---|---|
| Configuration space | $\mathcal C$ | $Q$ | $Q$ / C-space | |
| Obstacle / free C-space | $\mathcal C_{obs}$, $\mathcal C_{free}$ (closed / open) | $QO_i$, $Q_{free}$ | $Q_{obs}$, $Q_{free}$ | LaValle: touching = collision |
| Workspace obstacles, robot | $\mathcal O$, $\mathcal A$, $\mathcal A(q)$; world $\mathcal W$ | $WO_i$, $\mathcal A$ / $R(q)$ | — | |
| Query | $(q_I,q_G)$; discrete: $x_I$, $X_G$ | $q_{start},q_{goal}$ / $q_{init},q_{goal}$ | $q_{init},q_{goal}$ | |
| Discrete state / action | $x\in X$, $u\in U(x)$, $x'=f(x,u)$ | nodes, edges (App. H) | — | |
| Cost-to-come / cost-to-go | $C(x)$ / $G(x)$, heuristic $\hat G^*$ | $g(n)$ / $h(n)$ (App. H) | — | **$G$ ≠ Choset's $g$** |
| Dense sample sequence | $\alpha(i)$ | $q_{rand}$ | $q_{rand}$ | $\alpha$ also = yaw angle (3.42) and DH $\alpha_{i-1}$ |
| Tree planner | RDT (RRT if $\alpha$ random), swath $S$, STOPPING-CONFIGURATION | RRT, EXTEND/CONNECT, step_size | RRT with step | |
| Local planner | LPM / `connect` | $\Delta$ | GENERATEPATH | |
| Vertex selection | VSM | $\pi_T(q)$ | SELECTCONFIGFROMTREE | |
| Metric | $\rho$ | dist | $\rho$ | Choset/slides proof also use $\rho$ for **clearance** |
| Dispersion / discrepancy | $\delta(P)$, $D(P,\mathcal R)$ | same | — | |
| C-obstacle (translation) | Minkowski **difference** $\mathcal O\ominus\mathcal A(0)=\mathcal O\oplus(-\mathcal A)$ | Minkowski difference (App. F) | Minkowski sum | same set; "difference" ≠ morphological erosion |
| Rotations | $SO(3)\cong\mathbb{RP}^3$; quaternion $h=a+bi+cj+dk$ (scalar first) | App. E, scalar first | — | ROS/Eigen coeffs store scalar last |
| RPY | $R_z(\alpha)R_y(\beta)R_x(\gamma)$, fixed axes | own RPY naming | — | LaValle = ROS `setRPY` |
| DH | **modified (Craig)**: $T_i=Q_{i-1}R_i$ | — | — | same as MR App. C, not Spong |
| Completeness | complete / resolution complete (dense deterministic) / probabilistically complete | same, resolution relative to grid | same | |

---

## Errata found in LaValle Ch. 1–7 (checked before listing)
Items marked **(verified)** were re-read on the rendered PDF page and checked algebraically or numerically.

1. **Book p. 159, PDF 88, eq. (4.38) (verified).** $h(x)=\int f(\tau)g(x-\tau)d\tau$ computes $\mathcal O\oplus\mathcal A$, not $\mathcal C_{obs}=\mathcal O\ominus\mathcal A$. It needs $g(\tau-x)$ (cross-correlation), or $g$ = indicator of $-\mathcal A$. Check with Example 4.13: the printed formula gives $[-1,6]$, but $\mathcal C_{obs}=[-2,5]$.
2. **Book p. 175, PDF 96, eqs. (4.70)–(4.73) (verified).** The three-link polynomials are wrong; at $\theta=0$ they give $x=4$, but the reach is 3, as the same page states. Correct forms:
   - $x=a_1+a_1a_2-b_1b_2+a_1a_2a_3-a_1b_2b_3-b_1a_2b_3-b_1b_2a_3$;
   - $y=b_1+a_1b_2+b_1a_2+a_1a_2b_3+a_1b_2a_3+b_1a_2a_3-b_1b_2b_3$.
3. **Book p. 177, PDF 97, Fig. 4.25 caption (verified).** "If $1<p<3$" should be $1<f_1<3$. "At $f_1=0$ it is a point" should be $f_1=3$ (the text on p. 175 says so).
4. **Book p. 153, PDF 85, after (4.30) (verified).** The failure list repeats "$r_{12}=r_{23}=0$" and omits $r_{12}=r_{13}=0$. The claim that failures "correspond precisely" to pure yaw, pitch or roll is false: with $a=0$ the denominator vanishes whenever any one of $b,c,d$ is 0. Example: rotation by $\pi$ about $(0,1,1)/\sqrt2$ gives $r_{12}=r_{13}=0$, $r_{23}=1$. Use Shepperd's method.
5. **Book p. 199, PDF 108 (verified).** "If $n=4$, use the SO(3) trick; if $n=3$ or $n>4$, Gaussian" is off by one. Sampling $\mathbb S^n$ with the quaternion trick needs $n=3$; use Gaussians for $n=2$ or $n>3$. The suggestion to sum $k\ge12$ uniforms for a Gaussian is outdated; use Box–Muller or `std::normal_distribution`.
6. **Book p. 238, PDF 127, Fig. 5.25 BUILD_ROADMAP (verified).** Two indexing bugs:
   - when $\alpha(i)\in\mathcal C_{obs}$, $i$ is not incremented, so the same sample is retested forever;
   - after `i ← i+1` on line 4, lines 5–7 connect $\alpha(i+1)$ (the next, unchecked sample) instead of the vertex just added.
   Use a separate sample counter (corrected pseudocode above).
7. **Book p. 277, PDF 147, eq. (6.7) (verified).** The conchoid of Nicomedes is $(x^2+y^2)(y+d)^2-y^2L^2=0$. The book prints $x^2-y^2$, whose only point on the $y$-axis is the origin, while the figure's loop reaches $y=L-d$.
8. **Book p. 283, PDF 150, Example 6.3 (verified).** The equivalent quantifier-free formula should be $\phi(z)=(z\ge1)$, not $(z>1)$; it contradicts (6.13) at $z=1$.
9. **Book pp. 288–289, PDF 152–153, Example 6.5 (verified).** Fig. 6.31 says the set is $[1,2]$ and the text says $x=1$ makes $\Phi$ true. Both need $\le$ in (6.21)–(6.22). With the printed $\ge$, the set is $(-\infty,0]\cup[3,\infty)$ and $x=1$ fails.
10. **Book p. 342, PDF 179, §7.4.1 (verified).** "If the rank of the matrix is $m\le n$, then $m$ displacements can be chosen independently." With $m=\dim\mathcal C_{clo}$, the Jacobian rank is $n-m$ and the tangent space (null space) has dimension $m$.
11. **Book p. 105, PDF 61.** "$a_i$ is the distance between the $z_i$- and $z_{i-1}$-axes" should say $a_{i-1}$, consistent with (3.55). **Book p. 107, PDF 62:** "Prismatic joints can be modeled by allowing $a_i$ to vary" is wrong for 3-D DH; a prismatic joint along $z_i$ varies $d_i$.
12. **Book p. 99, PDF 58.** The C function `atan2` returns $(-\pi,\pi]$, not $[0,2\pi)$. The method "assumes $r_{11}\ne0$ and $r_{33}\ne0$", but with atan2 the real failure is $\cos\beta=0$ (gimbal lock).
13. **Book p. 134, PDF 75.** "According to Whitney's embedding theorem, $m\ge2n+1$." The theorem says $m=2n+1$ **suffices** ($\mathbb S^1\subset\mathbb R^2$ is a counterexample to the inequality as written). **Book p. 149, PDF 83:** "$O(3)$ … two three-spheres $\mathbb S^3\sqcup\mathbb S^3$" should be two copies of $SO(3)\cong\mathbb{RP}^3$; $\mathbb S^3$ only double-covers SO(3).
14. **Book p. 169, PDF 93.** Monomial exponents should be **nonnegative** integers (the book itself calls 1 a monomial). (4.53) should read $(a_1..a_n)\in\mathbb F^n$. **Book p. 173, PDF 95:** "the point $(0,1)$ in $\mathcal A_2$" should be $(1,0)$.
15. **Book p. 295, PDF 156.** (6.28) labels the rows $m+k$ and the minors $(m+k)\times(m+k)$; it should be $k+j$. "Intersected with $\mathbb S^3$" should be $\mathbb S^2$.
16. **Book p. 37, PDF 27.** "$\lvert V\rvert$ and $\lvert E\rvert$ are the numbers of edges and vertices, respectively" has the words swapped.
17. **Minor typos.**
    - Book p. 58: "Formulation 2.4.1" should be 2.4.
    - Book p. 60: "four operators" vs three in Fig. 2.18.
    - Ex. 2.10: "Example 2.17" should be Example 2.6.
    - Book p. 212: "children of $v_1$ … children of $v_1$" should end "of $v_2$".
    - Book p. 244: the medial axis is "one-dimensional" only in the plane; it is $(n-1)$-dimensional in general.
    - Book p. 361: "$\mathbb S^{n-1}$ … the $m$-dimensional sphere" should say $(n-1)$-dimensional.
    - Book p. 92 (superquadrics): "an ellipse is generated" should be "an ellipsoid".
18. **Book p. 37, PDF 27 (incomplete statement, not wrong).** A* is optimal with an underestimate only if closed states can be reopened. With the dead-state template, it needs a consistent heuristic.

---

## Gaps this fills in the knowledge base
Checked against `courses/05-algorithms-sensor-based-robotics/README.md` (§3 table, §5.3–5.4 checklist, §9 projects) and `library/choset-part1.md`, `choset-part2.md`.

1. **[A4] Value iteration and the Bellman equation.**
   - Missing from the README: $G^*(x)=\min_u\{l(x,u)+G^*(f(x,u))\}$, the termination action, and policy extraction by argmin.
   - The wavefront planner (A3) is backward value iteration with unit costs, and Dijkstra is its lazy version.
   - The label-correcting algorithm is optimal for any queue order.
   - LaValle §2.3, book pp. 43–57.
2. **[A4] Cost-to-go/cost-to-come naming clash and the consistency caveat.** LaValle's $G$ is cost-to-go. A* with a closed list needs a consistent heuristic. Add to the A4 pitfalls.
3. **[A5] RDT formulation.** RRT needs only a **dense** sequence (deterministic gives resolution completeness). The nearest point is on the **swath** (edge interiors), there is no step size, and **balanced** bidirectional growth swaps trees by size (Fig. 5.24). The README only has step-size RRT and RRT-Connect.
4. **[A5/A1] Uniform SO(3) sampling, metrics and interpolation for a 6-DOF or free-flying planner:**
   - Shoemake (5.15);
   - quaternion metric $\min\{\lVert h_1-h_2\rVert,\lVert h_1+h_2\rVert\}$ or $\cos^{-1}\lvert h_1\cdot h_2\rvert$;
   - the Haar-measure argument for why Euler angles are biased.
   The README mentions SLERP but no sampler or metric.
5. **[A5/A3] Guaranteed-safe edge checking.** Use clearance $d$ from distance queries plus displacement bounds $r\lvert\Delta\theta\rvert$ and per-joint Lipschitz constants $\sum c_i\lvert\Delta q_i\rvert$ (5.30–5.34), and check in van der Corput (bisection) order. The README only says "discretize at ε". This is the `is_local_path_collision_free` TODO in Assignment 3.
6. **[A3] Collision-detection internals:**
   - broad/narrow phase;
   - bounding-volume hierarchies (sphere/AABB/OBB/hull);
   - incremental closest-feature (Lin–Canny/V-Clip, "almost constant time").
   These explain what FCL does inside MoveIt (§5.3).
7. **[A4] Exact 2-D roadmaps beyond the visibility graph:**
   - **reflex-vertex + bitangent** shortest-path roadmap with the trig-free left-turn determinant test (6.1–6.2);
   - maximum-clearance pieces (line/line/parabola);
   - vertical decomposition with 1-cell sample points and an $O(n\lg n)$ sweep.
8. **[A3] Explicit C-obstacles with rotation.** EV/VE half-planes (4.39–4.46), the complex-number $(a,b)$ trick, and the "73 primitives" example show why sampling planners avoid building $\mathcal C_{obs}$.
9. **[A8] Complexity facts for exams:**
   - generalized mover's problem PSPACE-complete (Reif hard, Canny in PSPACE);
   - Warehouseman PSPACE-hard;
   - 3-D shortest path NP-hard;
   - CAD doubly exponential vs Canny singly exponential;
   - dispersion needs $N\ge(1/2\delta)^d$ samples (Sukharev).
10. **[A5] Closed-chain / constrained sampling.** Tangent-space projection plus Newton correction (7.22–7.24) and active–passive inverse kinematics. This is the theory behind MoveIt/OMPL path constraints, e.g. keeping a grasped object level.
11. **[A5] Multi-robot and time-varying planning:** state–time space with time monotonicity, velocity tuning, prioritized planning (incomplete), coordination space, Pareto optimality. Relevant if a Jackal/Husky assignment ever adds moving obstacles.
12. **[A5] PRM engineering details missing from the README:**
    - union-find `same_component` (forest) vs `degree < K`;
    - neighbourhood rules (nearest $K=15$, component $K=1$, radius with cap 20);
    - visibility roadmap guards/connectors;
    - the vertex-enhancement statistic $n_f/(n_t+1)$;
    - ε-goodness.

---

## Corrections & upgrades for the knowledge base
- **README §3 table, row "Sampling-based (PRM, RRT, EST, RRT*)", LaValle column "Ch. 5".** LaValle (2006) has **no RRT\*** and no PRM\*. EST appears only as the one-paragraph "expansive-space planner" (book pp. 227–228, PDF 122). Change to "Ch. 5.4–5.6 (RDT/RRT, sampling-based roadmaps, expansive-space planner; no RRT*)".
- **README §3 table, row "Graph search", LaValle "Ch. 2".** Correct. Make it specific: §2.2 (book pp. 32–43) plus value iteration §2.3 (book pp. 43–57).
- **README §3 table, row "Roadmaps (visibility, GVD), cell decomposition", LaValle "Ch. 6".** Correct. The visibility graph appears as the "shortest-path roadmap" (§6.2.4, book pp. 261–264); the GVD as the "maximum-clearance roadmap" (§6.2.3, book pp. 260–261).
- **README §3 table, row "Configuration space; Minkowski".** Correct. LaValle §4.3.2 (book pp. 158–164, PDF 87–90) gives the star algorithm and the EV/VE half-planes with a worked table (Fig. 4.19).
- **README §3 table, row "Quaternions", LaValle "Ch. 4.2.2".** Correct (book pp. 148–154). Add §5.1.2/§5.2.2 for metrics and uniform sampling.
- **README §5.3 "Collision detection: discretize edges at a resolution ε, or conservative advancement".** Add LaValle's guaranteed bound (5.31)/(5.34) and the van der Corput check order. Note that a resolution in joint space ≠ in workspace.
- **README §5.4 PRM pseudocode.** It connects to all kNN without a component test. That is fine, but cite LaValle's union-find variant and its forest trade-off.
- **README §5.4 RRT pseudocode** (goal-bias $p\approx0.05$–0.1). Consistent with Choset and the slides; LaValle uses 1/100 and no step size. Keep and add a footnote.
- **`library/README.md` index.** Add a row: `lavalle-part1.md` | LaValle *Planning Algorithms* ch. 1–7 | 10–192 | **PDF = ⌊(book+17)/2⌋ (2-up)** | ASBR. Add a LaValle column to the notation table (cost-to-go $G$, $\alpha(i)$, scalar-first quaternions, modified DH, ROS-style RPY). Add the errata above.

## Best-reference map
- **[A3] C-space topology, manifolds, identifications, $\pi_1$** → §4.1–4.2, book pp. 127–155, PDF 72–86 (SE(2) wrap-around Fig. 4.8 p. 148; SO(3) ≅ RP³ pp. 148–154).
- **[A3] C-obstacles: definition, Piano Mover's, Minkowski/star, EV/VE, rotation** → §4.3, book pp. 155–167, PDF 86–92 (Example 4.15 p. 163; Example 4.16 p. 166). Exercises 4.11, 4.26.
- **[A3] Collision detection** → §5.3, book pp. 209–217, PDF 113–117.
- **[A3] Geometric models / predicates** → §3.1, book pp. 81–92, PDF 49–54.
- **[A4] Discrete search (BFS/DFS/Dijkstra/A*/best-first/IDA*/backward/bidirectional)** → §2.2, book pp. 32–43, PDF 24–30.
- **[A4] Value iteration, termination action, Dijkstra as DP, label correcting** → §2.3, book pp. 43–57, PDF 30–37 (Examples 2.3–2.5). Exercises 2.1, 2.6, 2.18–2.23.
- **[A4] Grid planning on C-space (k-neighbourhoods, resolution schedules)** → §5.4.2, book pp. 220–224, PDF 118–120; optimal grid motion models §7.7.1, book pp. 359–362, PDF 188–189.
- **[A4] Exact roadmaps: vertical decomposition, maximum clearance, shortest-path roadmap** → §6.1–6.2, book pp. 249–264, PDF 133–140. Exercises 6.1, 6.2, 6.13, 6.14.
- **[A4] Cell complexes, line-segment robot, CAD, Canny (concepts)** → §6.3–6.4, book pp. 264–298, PDF 140–157.
- **[A5] Metrics, Haar measure** → §5.1, book pp. 186–195, PDF 101–106.
- **[A5] Sampling: van der Corput, random SO(3), dispersion, Sukharev, discrepancy, Halton/Hammersley, lattices** → §5.2, book pp. 195–209, PDF 106–113.
- **[A5] Incremental framework, bug traps, RPP, Ariadne, expansive-space, random walk** → §5.4, book pp. 217–228, PDF 117–122 (expansive-space p. 227 for **Assignment 3** context).
- **[A5] RDT/RRT, nearest neighbours, kd-tree, balanced bidirectional** → §5.5, book pp. 228–237, PDF 122–127.
- **[A5] Sampling-based roadmaps, neighbourhoods, visibility roadmap, Gaussian/bridge/boundary/medial-axis samplers** → §5.6, book pp. 237–244, PDF 127–130. Exercises 5.22, 5.23, 5.25.
- **[A5] Closed chains / constrained sampling** → §4.4 (book pp. 167–180, PDF 92–98) + §7.4 (book pp. 337–347, PDF 177–182).
- **[A5] Time-varying, multi-robot, manipulation planning** → §7.1–7.3, book pp. 311–337, PDF 164–177.
- **[A8] Complexity results for exam answers** → §6.5, book pp. 298–307, PDF 157–162; §7.7.1 (3-D shortest path NP-hard), book p. 358, PDF 188.
- **[A1] / [F3] Rotations, yaw–pitch–roll, modified DH, Puma example, quaternions** → §3.2–3.3, book pp. 92–112, PDF 54–64; §4.2.2, book pp. 148–154, PDF 82–85.
- **[F2–F4] Groups, $O(n)$/$SO(n)$ dimension count** → §4.1.3 + §4.2.1, book pp. 141–148, PDF 79–82.
- **[F5] Jacobian rank / tangent spaces** → Canny silhouettes (6.28) book p. 295, PDF 156; closure tangent space (7.22–7.23), book p. 342, PDF 179.
- **[F10] C++ implementation targets** → Ex. 2.18–2.23 (A*), 5.23 (BiRRT + kd-tree), 5.25 (expansive-space vs RRT), 6.13 (vertical decomposition).
