# Choset et al., *Principles of Robot Motion: Theory, Algorithms, and Implementations* (MIT Press, 2005) — Part 2
Source: `cb2bd4a2-Robot_Motion_Theory_and_Algorithms.pdf`, PDF pages 206–410 (book pages 190–396)

> **Range check (TOC, PDF pp. 8–10).** Page offset is not constant: PDF = book + 16 for book pp. 190–267, +15 for book pp. 269–347 (blank book p. 268 is missing from the scan), +14 for book pp. 349–396 (blank p. 348 missing).
> So this range is **not** ch. 5 roadmaps + ch. 6 cell decompositions (those are book pp. 107–196 = PDF ≈123–212, i.e. Part 1). It covers:
> - end of **ch. 6** §6.3 pursuit/evasion + ch. 6 problems (book 190–196)
> - **ch. 7 Sampling-Based Algorithms** complete (book 197–267) — ASBR week 6 / A4 / Assignment 3
> - **ch. 8 Kalman Filtering** complete (book 269–300) — ASBR A5
> - **ch. 9 Bayesian Methods** complete (book 301–347) — ASBR A5/A6
> - **ch. 10 Robot Dynamics** complete (book 349–372) — RDKDC R7
> - **ch. 11 Trajectory Planning** book 373–396 (§11.1–11.3 and start of §11.4 timing/optimal control) — R8/A4 context
> Graph search (A*, Dijkstra, BFS/DFS) is **Appendix H**, not in this range.
> Notation: the book writes the local planner as $\Delta$ (rendered as "!" by pdftotext), $\mathcal{Q}_\text{free}$ for free C-space.

---

## Ch. 6 (end) — §6.3 Visibility-based decompositions for pursuit/evasion (PDF pp. 206–212, book pp. 190–196) — Lessons: A3 (enrichment only)
**Big idea:** A cell decomposition need not be built on obstacle geometry alone: here cells are *conservative regions* inside which the pursuer's visibility "gap-edges" do not change combinatorially. Pairing each cell with every possible labeling of its gap-edges (0 = cleared, 1 = possibly contaminated) yields an **information graph**; plain graph search to any all-zero node is a guaranteed search strategy. It is a beautiful example of "decompose, build adjacency graph, search" applied to an *information state* rather than a configuration.

**Key definitions / results:**
- Gap-edge label transitions when crossing a critical boundary (book p. 191): (1) a gap-edge appears → label 0; (2) disappears → drop it; (3) several gap-edges merge → label 1 if any was 1; (4) one gap-edge splits → children inherit the label.
- Information graph $G_i$: node = (conservative cell, label vector $B(x)$); edges = admissible transitions. Solution = any node with $B(x)=\mathbf{0}$ (Figs. 6.38–6.39).
- Pursuer counts (book pp. 192–194): simply connected polygon with $n$ edges needs $O(\log n)$ pursuers (recursive diagonal splits + "static pursuers" at each level; U-shape example Fig. 6.40); polygon with $h$ holes needs $O(\sqrt h+\log n)$ (trichromatic triangles + planar separator theorem, Fig. 6.41).

**Exercises worth doing (ch. 6, book pp. 195–196):** P2 (implement trapezoidal-decomposition planner for a convex translating robot — the canonical ASBR week-5 coding exercise, reuses ch. 3 C-space generator); P4 (path via adjacency graph in Fig. 6.2, c5→c6); P3/P5 (3-D generalizations of trapezoidal / boustrophedon cells); P7 (why pursuit/evasion is posed in workspace, not C-space). P6, P8, P9 optional.

**Pitfall:** the pursuit/evasion solution is planned in the *workspace* (visibility is a workspace property), unlike the rest of the book.

---

## Ch. 7 Sampling-Based Algorithms — overview (PDF pp. 213–218, book pp. 197–202) — Lessons: A4 (also A3 for local planner/collision checking)
**Big idea:** Exact roadmap/cell methods need an explicit model of $\mathcal{Q}_\text{free}$, which is hopeless beyond ~3 DOF (generalized mover's problem is PSPACE-hard [Reif]; PSPACE-complete [Canny]; Schwartz–Sharir doubly exponential; Canny's roadmap singly exponential). Sampling-based planners only need a **membership oracle** — "is $q$ collision-free?" — and exploit the fact that this test is cheap. PRM samples coarsely for nodes and *very finely* for edges (local paths). The price is a weaker guarantee: **probabilistic completeness** (random samples) or **resolution completeness** (deterministic / quasirandom / grid samples).

**Key definitions (book p. 201):**
- *Complete*: always answers correctly in bounded time — impractical for > 3 DOF.
- *Probabilistically complete*: if a path exists, probability of finding it → 1 as running time → ∞ (random sampling). First shown for RPP; PRM in [221, 228].
- *Resolution complete*: same with deterministic sampling, relative to the sampling resolution.
- *Multiple-query* (PRM: learning/construction phase + query phase) vs *single-query* (EST, RRT, SBL, LazyPRM). SRT blends both.
- Collision checking is a separate module (GJK, SOLID, V-Clip, I-Collide, PQP, RAPID, SWIFT++ …, book p. 201) — modern equivalent in MoveIt 2 is FCL via the planning scene.

**Map of the chapter:** §7.1 PRM (basic, practical implementation, sampling strategies, connection strategies) · §7.2 single-query trees (EST, RRT, SBL) · §7.3 SRT · §7.4 analysis (ball tiling in $\mathbb{R}^d$, $(\epsilon,\alpha,\beta)$-expansiveness, abstract path tiling) · §7.5 beyond basic planning (kinodynamic, multi-robot, manipulation, assembly, flexible objects, biology).

---

## §7.1.1 Basic PRM (PDF pp. 218–224, book pp. 202–208) — Lessons: A4
**Big idea:** Two phases. *Learning*: drop $n$ random collision-free nodes, try to connect each to its $k$ nearest neighbours with a cheap local planner $\Delta$; the graph $G=(V,E)$ approximates the connectivity of $\mathcal{Q}_\text{free}$. *Query*: hook $q_\text{init}$ and $q_\text{goal}$ onto the graph and run Dijkstra/A*. Works well for 5–12 DOF.

**Key definitions:** local planner $\Delta(q,q')$ returns a collision-free path or NIL (assume symmetric + deterministic for now); $\text{dist}:\mathcal{Q}\times\mathcal{Q}\to\mathbb{R}_{\ge 0}$, usually a metric.

**Algorithm 6 — Roadmap construction (book p. 204)**
```
Input: n (#nodes), k (#nearest neighbours)    Output: G=(V,E)
V ← ∅ ; E ← ∅
while |V| < n:
    repeat q ← random configuration in Q  until q collision-free
    V ← V ∪ {q}
for all q ∈ V:
    N_q ← k closest neighbours of q in V according to dist
    for all q' ∈ N_q:
        if (q,q') ∉ E and Δ(q,q') ≠ NIL:  E ← E ∪ {(q,q')}
```
Note: node degree can exceed $k$ because $q$ can appear in many other nodes' neighbour lists (Fig. 7.3, $k=3$).

**Algorithm 7 — Solve query (book p. 206)**
```
N_init ← k closest neighbours of q_init in V;  N_goal ← same for q_goal
V ← {q_init, q_goal} ∪ V
try Δ(q_init, q') for q' ∈ N_init in order of increasing dist until one succeeds → add edge
same for q_goal
P ← shortest_path(q_init, q_goal, G)     # Dijkstra or A*
return P or failure
```
- With several connected components, both query points must be connected to the *same* component (try every component); otherwise report failure.
- If queries fail often, resume Alg. 6 on the existing roadmap (skip lines 1–2), ideally with *biased* (connection) sampling.

**Directed roadmaps / stored local paths (book pp. 207–208):** non-reversible local paths ⇒ directed graph, and $(q',q)$ must be checked separately. Deterministic $\Delta$ ⇒ store only endpoints, recompute path at query time; nondeterministic $\Delta$ ⇒ must store the local path on the edge (more memory, more powerful planners allowed, cf. SRT).

## §7.1.2 A practical implementation of basic PRM (PDF pp. 224–231, book pp. 208–215) — Lessons: A4, A3, R1 (rotation metrics)
**Sampling (uniform):** draw each translational DOF uniformly in its range; for rotations, sampling must be **symmetry-invariant** — do not sample Euler angles uniformly (that biases orientations); use e.g. a uniform random unit quaternion (book p. 208; example draws "random axis and random angle", p. 214 — note: uniform axis + uniform angle is *not* uniform on SO(3); see pitfalls).

**Nearest neighbours — kd-tree (book p. 209, Fig. 7.5):** recursively split the point set by an axis-aligned hyperplane into halves of roughly equal size. Storage $O(dn)$, build $O(dn\log n)$, rectangular range query $O(n^{1-1/d}+m)$ ($m$ = reported points) → essentially linear as $d$ grows; hence **approximate nearest neighbours (ANN)** [Arya–Mount]. (OMPL uses GNAT/NearestNeighborsGNATNoThreadSafety by default — same idea.)

**Distance functions (book pp. 209–211):**
- Ideal: dist should predict the probability that $\Delta$ fails — e.g. the workspace volume swept by the robot along $\Delta(q',q'')$. Exact swept volume is too hard, so approximate.
- *Embedding metric:* pick $p$ points on the robot, transform them by $q$, stack into a vector $\text{emb}(q)\in\mathbb{R}^{p\cdot\dim W}$, and use $\text{dist}(q',q'')=\lVert\text{emb}(q')-\text{emb}(q'')\rVert$. Drawbacks: how many points / which points, ignores obstacles.
- *Weighted SE(3) metric:* $\text{dist}(q',q'') = w_t\lVert X'-X''\rVert + w_r f(R',R'')$, with $f$ ideally the geodesic length on SO(3); hard to pick $w_t,w_r$, unclear for articulated robots.
- Worked example (book pp. 214–215): 6-DOF rigid polyhedral robot through a narrow hole in a wall; config $q=(p,r)$ with quaternion $r$; embedding = the two farthest-apart surface points of the robot (6-D vector); $n=1000$, $k=10$; subdivision collision checking; translations interpolated linearly, quaternions by **slerp**; query by A*.

**Local planner trade-off (book p. 211):** powerful-but-slow $\Delta$ ⇒ fewer nodes; fast-but-weak $\Delta$ ⇒ more nodes, more calls, each cheap. Recommendation: a very fast (straight-line) planner and a large, well-spread roadmap — also usable for connecting $q_\text{init}, q_\text{goal}$ at query time. Interpolate translation and rotation components separately.

**Edge collision checking (book pp. 211–213, Fig. 7.6):** discretize the segment into $q_1=q',\dots,q_\ell=q''$ with consecutive spacing < `step_size`.
- *Incremental:* walk from $q'$ in steps; stop at first collision.
- *Subdivision (bisection):* check the midpoint, recurse on both halves until a collision is found or segment length < `step_size`. **Better in practice** because collisions tend to be found early (short paths tend to be free; it shortens the unchecked portion fastest). In Fig. 7.6 subdivision rejects after 3 checks vs 5 incremental — but if the obstacle is near $q'$ incremental wins.
- Both may *miss* a collision if `step_size` is too large; the adaptive method of [376] relates obstacle distance to the maximum distance travelled by any robot point and is **exact**.
- Key PRM insight: edges are checked by sampling **much more finely** than nodes.

**Postprocessing (book pp. 212–214, Fig. 7.7):** shortcutting — try to connect non-adjacent path configurations with $\Delta$ (random pairs, or greedy: from $q_\text{init}$ try $q_\text{goal}$, then the next-to-last, …, find farthest reachable $q_0$, recurse). Smoothing — fit splines through PRM waypoints and collision-check the curves. Cost: adds query-time overhead; denser roadmaps already give shorter paths.

## §7.1.3 PRM sampling strategies (PDF pp. 232–241, book pp. 216–225) — Lessons: A4
**Big idea:** Uniform random sampling is the robust baseline (no adversary can defeat it; but running time varies across runs). It fails on the **narrow passage problem**: if every solution must pass through a set of tiny measure, you must be lucky enough to sample there. **Importance sampling** raises density where it matters.

| Strategy | Mechanism (book page) | Notes |
|---|---|---|
| **OBPRM** (obstacle-based) [18] | sample uniformly; for each colliding $q_\text{in}$, pick random direction $v$, find free $q_\text{out}$ along $v$, binary-search to the free config closest to the obstacle surface; keep it (p. 217) | samples hug C-obstacle boundary |
| **Gaussian sampler** [59] | $q_1$ uniform; $q_2$ at distance ~ $\mathcal{N}(0,\sigma^2)$ from $q_1$; keep the free one **iff exactly one** of the pair is in collision (p. 217) | density concentrated near boundaries |
| **Dilated free space** [194] | allow small penetration so narrow passages widen; later push samples back into $\mathcal{Q}_\text{free}$ by local resampling | |
| **Bridge test** [193] | $q',q''$ uniform; if **both collide**, add midpoint $q_m$ if free (p. 217) | short bridges are easy only inside narrow passages |
| **Medial-axis / GVD retraction** [427] | bisection moves a sample until equidistant from two boundary points of $\mathcal{Q}_\text{free}$ (p. 217–218); or workspace-GVD-guided handle points [155,171,191] | max clearance inside passages; workspace GVD via graphics hardware |
| **Visibility PRM** [337] | keep a new free $q$ only if it (1) sees no existing node (new "guard"/component) or (2) connects ≥ 2 components (p. 218) | very small roadmaps |
| **Manipulability-based** [281,282] | sample more where $\omega(q)=\sqrt{\det J(q)J(q)^T}$ (Eq. 7.1) is low (near singularities); rejection sampling using an approximate CDF of $\omega$ (pp. 218–219) | links to RDKDC R5 |
| **Quasirandom** (Halton, Hammersley) | deterministic low-discrepancy sequences (pp. 219–222) | same run time every run; resolution complete; perturb to avoid adversarial cases |
| **Grid / lattice** [52, 269, 291] | coarse grid nodes, fine collision checks between neighbours; refine resolution progressively (p. 223) | resolution complete |
| **Connection sampling** [221,231] | pick roadmap node $q$ with prob. $w(q)$, expand it in its neighbourhood (or random-bounce walk), try to connect to other components (pp. 223–224) | never creates new components |

**Quasirandom definitions (book pp. 219–222):**
- Discrepancy (Def. 7.1.1): $D(P,\mathcal{R})=\sup_{R\in\mathcal{R}}\left|\frac{\mu(R)}{\mu(X)}-\frac{|P\cap R|}{N}\right|$ (range space $\mathcal{R}$ = axis-aligned boxes or $d$-balls).
- Dispersion (Def. 7.1.2): $\delta(P,\rho)=\sup_{x\in X}\min_{p\in P}\rho(x,p)$ = radius of the largest empty ball.
- **Sukharev criterion** ($L_\infty$, unit $d$-cube): $\delta(P,\rho)\ge \dfrac{1}{2\lfloor N^{1/d}\rfloor}$, so to reach dispersion $\delta^*$ you need $N\ge (1/(2\delta^*))^d$ — **exponential in $d$**; a regular grid is already optimal for dispersion.
- Van der Corput: reverse the binary digits of $n$ about the radix point, $\Phi(n)=\sum_i a_i2^{-(i+1)}$ (0, 1/2, 1/4, 3/4, 1/8, 5/8, … Fig. 7.9a). Halton: radical inverse in pairwise coprime bases $b_1=2,b_2=3,b_3=5,\dots$ per coordinate; $D\le O((\log N)^d/N)$ for boxes, for balls the book prints $O(N^{-(d+1)/2})$ (almost certainly should be $O(N^{-(d+1)/(2d)})$ — see pitfalls). Hammersley (fixed $N$): $p_n=(n/N,\Phi_{b_1}(n),\dots,\Phi_{b_{d-1}}(n))$ — best asymptotic discrepancy.

**Connection-sampling weight (book p. 224):** $w(q)=\dfrac{1/(\deg(q)+1)}{\sum_{q'\in V}1/(\deg(q')+1)}$ — low-degree nodes are presumed to be in difficult regions. Random-bounce walk: random direction until collision, repeat; store the resulting path explicitly (nondeterministic).

**Choosing (book pp. 224–225):** uniform random is fine for 3–7 DOF "difficult but not pathological" problems; quasirandom/lattice if run-time consistency matters; narrow-passage samplers when passages exist; **hybrids** $\pi=(1-w)\pi_A+w\pi_B$ or filters (Gaussian used to filter bridge-test nodes [263]). No strategy dominates; pathological instances exist for every sampler.

## §7.1.4 PRM connection strategies (PDF pp. 241–243, book pp. 225–227) — Lessons: A4
- **Sparse roadmaps:** skip candidate edges whose endpoints are already in the same connected component (maintain components with union-find / disjoint sets). Result is a forest — no cycles ⇒ fewer local-planner calls but longer paths; adding 2–3 redundant edges per node recovers path quality cheaply [162]; "useful cycles" [336].
- **Connecting components:** connection sampling, tree planners (§7.2 → used in SRT), random walks, RPP.
- **Lazy evaluation (LazyPRM)** [Bohlin–Kavraki 52–54]: assume all nodes/edges free; at query time search shortest path; check its **nodes** first (remove colliding nodes and incident edges, re-search), then its **edges** at coarse-to-fine resolution (remove colliding edges, re-search); cache "known free" nodes and per-edge checked resolution; if graph exhausted, add nodes (preferably in difficult regions). Variant [335] assigns each edge a probability of being free from its checked resolution (FuzzyPRM, §7.5.3).

## §7.2 Single-query sampling-based planners (PDF pp. 243–245, book pp. 227–229) — Lessons: A4
**Big idea:** For one query, don't map all of $\mathcal{Q}_\text{free}$ — grow **trees** rooted at $q_\text{init}$ (and usually $q_\text{goal}$) and stop as soon as they meet. Sampling is **conditional**: each new sample depends on the current tree(s) and the query. The tension: bias toward the query, yet still be able to cover all of $\mathcal{Q}_\text{free}$ (needed for probabilistic completeness).
- Predecessors: **RPP** (randomized potential field: gradient descent + random walks to escape minima, builds graph of local minima, ch. 4), **Ariadne's clew** (genetic optimisation to place samples far apart + local search to goal), 2Z-method.
- PRM as single-query: insert $q_\text{init},q_\text{goal}$ first, check periodically whether they share a component, stop when they do (LazyPRM is the efficient version).
- **EST** = pure forward propagation: pushes the tree into unexplored regions by *expanding from sparse nodes*. **RRT** = steering: *pulls* the tree toward random points. Bidirectional (two trees) is usually more efficient than one tree for geometric planning; one tree is typical for kinodynamic planning.

## §7.2.1 Expansive-Spaces Trees (EST) (PDF pp. 246–249, book pp. 230–233) — Lessons: A4 (**JHU ASBR Assignment 3 = EST as a MoveIt 2 planner plugin**)
**Big idea:** Pick a node of the tree **with probability inversely related to how crowded its neighbourhood is**, sample a random configuration *near that node*, and keep it only if the local planner can connect it. The tree therefore spreads out toward sparse regions instead of piling up around the roots. Named after the "expansive space" notion used to prove its probabilistic completeness [Hsu, Latombe, Motwani 192].

**Algorithm 8 — Build EST (book p. 230)**
```
Input: q0 (root), n (#expansion attempts)      Output: tree T=(V,E), ≤ n configs
V ← {q0}; E ← ∅
for i = 1..n:
    q ← a configuration of T chosen at random with probability π_T(q)
    extend_EST(T, q)
return T
```
**Algorithm 9 — Extend EST (book p. 231)**
```
q_new ← a random collision-free configuration from the neighbourhood of q
if Δ(q, q_new):                 # local planner succeeds (edge collision-free)
    V ← V ∪ {q_new}; E ← E ∪ {(q, q_new)}
    return q_new
return NIL
```
- Contrast with PRM: a PRM node is never rejected (and is not required to connect); an EST node is added **only if connected to an existing tree node** ⇒ every tree node is reachable from the root (Fig. 7.12).
- *Node selection* (book p. 232): weight $w_T(q)$ = number of tree nodes within a predefined neighbourhood of $q$; choose $\pi_T(q)\propto 1/w_T(q)$. Naive computation is $O(n)$ per step — too slow for large trees. **Practical approximation:** impose a (coarse) grid on $\mathcal{Q}$ (or on a low-dimensional projection), pick a non-empty cell at random (favouring cells with few nodes), then a node from that cell — the SBL method (§7.2.3); hashing also works. Other $\pi_T$ (Phillips & Kavraki [349,350]): combine generation order, number of neighbours, out-degree and an A* cost $g(q)+h(q)$ (potential-field-like goal bias).
- *Neighbourhood sampling:* uniform in a ball/box of user-chosen radius around $q$ (the book leaves the radius as a parameter).
- *Merging (book p. 232, Fig. 7.13):* expand $T_\text{init}$ to produce new $q$; try $\Delta$ from $q$ to its $k$ closest nodes in $T_\text{goal}$; on success the trees merge, path = root$_\text{init}\to q_1$ in $T_\text{init}$ + local path + $q_2\to$ root$_\text{goal}$ in $T_\text{goal}$; otherwise **swap** the trees' roles and repeat a fixed number of times.
- Implementation warning: the update of $\pi_T$ must be fast as nodes are added — use grid/hash structures for large $n$ and high-dimensional $\mathcal{Q}$.

**OMPL/MoveIt connection (for A3):** OMPL's `EST` / `BiEST` / `ProjEST` follow exactly this design: `ProjEST` uses a *projection* to a low-dim grid and chooses cells with probability ∝ 1/(cell size); `range` = neighbourhood radius; `goal_bias` = probability of sampling the goal. (Background knowledge, not in the book.)

## §7.2.2 Rapidly-exploring Random Trees (RRT) (PDF pp. 249–254, book pp. 233–238) — Lessons: A4
**Big idea:** Sample $q_\text{rand}$ uniformly in $\mathcal{Q}_\text{free}$, find the **nearest** tree node $q_\text{near}$, and step from it a distance `step_size` toward $q_\text{rand}$. Because a node's chance of being "nearest" is proportional to the volume of its Voronoi cell, frontier nodes in big empty regions are extended most — the *Voronoi bias* (that phrase is not used in the book, but it is the mechanism). Simpler to implement than EST: no density bookkeeping, just a nearest-neighbour query.

**Algorithm 10 — Build RRT (book p. 234)**
```
V ← {q0}; E ← ∅
for i = 1..n:
    q_rand ← random free configuration
    extend_RRT(T, q_rand)
return T
```
**Algorithm 11 — Extend RRT (book p. 234)**
```
q_near ← closest neighbour of q in T
q_new  ← move q_near by step_size along the straight line toward q (book writes q_rand)
if q_new is collision-free:          # in practice also check the segment q_near→q_new
    V ← V ∪ {q_new}; E ← E ∪ {(q_near, q_new)}
    return q_new
return NIL
```
**Algorithm 12 — Connect RRT (greedy, book p. 235)**
```
repeat q_new ← extend_RRT(T, q) until q_new = q or q_new = NIL
return connected if q_new = q else failure
```
- `step_size` can be dynamic (large when far from obstacles); RRT is sensitive to the metric because it determines both the nearest node and the step.
- Trade-off (book pp. 235–236): small step + Connect ⇒ many nodes ⇒ memory and NN cost grow; option: add only the *last* configuration of a Connect run.
- **Goal bias (book p. 236):** pure greedy ($q_\text{rand}=q_\text{goal}$ always) degenerates into a randomized potential-field planner stuck in local minima; use $q_\text{rand}=q_\text{goal}$ with probability $p$ (e.g. $p=0.05$), uniform otherwise; better still, sample in a region around $q_\text{goal}$ defined by the tree vertices closest to it.

**Algorithm 13 — Merge RRT (bidirectional, book p. 237)**
```
for i = 1..ℓ:
    q_rand ← random free configuration
    q_new1 ← extend_RRT(T1, q_rand)
    if q_new1 ≠ NIL:
        q_new2 ← extend_RRT(T2, q_new1)
        if q_new1 = q_new2: return merged
        SWAP(T1, T2)
return failure
```
Replacing `extend` by `connect` in line 3 and/or line 5 gives the greedier variants (both Connect = RRT-Connect "Connect–Connect"; Extend then Connect = the classic **RRT-Connect** of Kuffner & LaValle 2000). Book: greedier works well [249, 271]; choice is problem-dependent. Note: in the book's listing the swap is inside the `if` — so the trees only swap after a successful extension.

## §7.2.3 Connection strategies and the SBL planner (PDF p. 254, book p. 238) — Lessons: A4
**SBL = Single-query, Bi-directional, Lazy collision-checking** [Sánchez & Latombe 367]: two ESTs (roots $q_\text{init},q_\text{goal}$), new nodes sampled per EST rules, but **edges are not checked** when created. An edge is checked exactly once, and only when it lies on a candidate path joining the two trees. Node selection uses a **coarse grid over $\mathcal{Q}$**: pick a random non-empty cell, then a random sample in it ⇒ samples in sparsely-populated cells are more likely — the cheap approximation of $\pi_T\propto 1/w_T$ applicable to all EST planners. (OMPL `SBL` uses a projection grid the same way.)

## §7.3 Sampling-based Roadmap of Trees (SRT) (PDF pp. 254–258, book pp. 238–242) — Lessons: A4 (enrichment)
**Big idea:** A PRM whose *nodes are trees* and whose local planner is a bidirectional tree planner (EST/RRT). For very hard problems the cost of building SRT and answering a query can beat any single-query planner, blurring the single/multiple-query distinction.
- Roadmap of trees $G_T=(V_T,E_T)$: partition the configuration roadmap into trees $T_1..T_n$, contract each; $(T_i,T_j)\in E_T$ iff some $q_i\in T_i$, $q_j\in T_j$ are joined by a local path.
- Add trees: roots sampled uniformly (any §7.1.3 sampler), grown with Alg. 8 or 10. Add edges: neighbours $N_{T_i}$ = $k$ nearest (by representative/aggregate configuration $q_{T_i}$) **plus $r$ random** trees (random neighbours offset metric failures); skip pairs already in the same component.
- **Algorithm 14 Connect SRT (book p. 241):** for each candidate pair, first try fast deterministic $\Delta$ between some random $q_i\in T_i$ and their closest $q_j\in T_j$; if none succeed, run `Merge Trees` (bidirectional tree planner).
- Queries: grow small $T_\text{init},T_\text{goal}$, connect to $k$ closest + $r$ random trees until they share a component; concatenate paths; smooth.
- Parameters make SRT degenerate to PRM (1 config per tree, 1 close pair, 0 merge iterations) or to RRT/EST (0 trees in the roadmap). Parallelizes well (tree growth independent; edge computation harder).

## §7.4 Analysis of PRM (PDF pp. 258–269, book pp. 242–253) — Lessons: A4, F8
**Definition (book p. 243):** PRM is probabilistically complete if for every query $(a,b)$ that is solvable in $\mathcal{Q}_\text{free}$, $\lim_{n\to\infty}\Pr[(a,b)\ \text{FAILURE}]=0$, with $n$ = number of samples (a work measure).

### §7.4.1 PRM in $\mathbb{R}^d$ — path tiling with balls (book pp. 243–246)
Setting: $\mathcal{Q}_\text{free}$ open in $[0,1]^d$, Euclidean dist, straight-line $\Delta$, uniform sampling $\Pr(x\in A)=\mu(A)/\mu(\mathcal{Q}_\text{free})$, clearance $\rho=\text{clr}(\gamma)>0$.

**Theorem 7.4.1.** If a path $\gamma$ of length $L$ and clearance $\rho$ joins $a,b$, then after $n$ samples
$$\Pr[(a,b)\ \text{SUCCESS}]\ \ge\ 1-\left\lceil\frac{2L}{\rho}\right\rceil e^{-\sigma\rho^d n},\qquad \sigma=\frac{\mu(B_1(\cdot))}{2^d\,\mu(\mathcal{Q}_\text{free})}.$$
**Proof sketch (redo this — classic exam/homework proof):**
1. Place $m=\lceil 2L/\rho\rceil$ points $x_1=a,\dots,x_m=b$ on $\gamma$ with $\text{dist}(x_i,x_{i+1})<\rho/2$.
2. If $y_i\in B_{\rho/2}(x_i)$ and $y_{i+1}\in B_{\rho/2}(x_{i+1})$ then both lie in $B_\rho(x_i)\subset\mathcal{Q}_\text{free}$ (convex ball), so segment $y_iy_{i+1}$ is free (Fig. 7.17).
3. Indicator $I_i=1$ iff some sample lies in $B_{\rho/2}(x_i)$. Failure ⊂ $\bigcup_i\{I_i=0\}$ ⇒ union bound $\Pr[\text{FAIL}]\le\sum_i\Pr[I_i=0]$.
4. $\Pr[I_i=0]=(1-\mu(B_{\rho/2})/\mu(\mathcal{Q}_\text{free}))^n=(1-\sigma\rho^d)^n$, since $\mu(B_{\rho/2})=(\rho/2)^d\mu(B_1)$.
5. $(1-\beta)^n\le e^{-\beta n}$ ⇒ bound.
**Consequences:** failure probability decays **exponentially** in $n$; harder with long paths ($L$), small clearance ($\rho$ — narrow passages hit as $\rho^d$!) and high dimension $d$. Can be inverted to choose $n$ for a target failure probability. Extends to varying clearance along the path [223].
*Pitfall:* the step "events $I_i=0$ are independent" (book p. 245) is not needed (and not quite true for fixed $n$); the union bound alone gives the result.

### §7.4.2 $(\epsilon,\alpha,\beta)$-expansiveness (book pp. 246–250) — why uniform sampling works on "non-pathological" spaces
- Def. 7.4.1 $\text{reach}(S)=\{x\in\mathcal{Q}_\text{free}\mid\exists y\in S:\ \overline{xy}\subset\mathcal{Q}_\text{free}\}$ (visibility set).
- Def. 7.4.2 **$\epsilon$-good:** $\mu(\text{reach}(x))\ge\epsilon\,\mu(\mathcal{Q}_\text{free})$ for all $x$.
- Def. 7.4.3 **$\beta$-lookout:** $\text{lookout}_\beta(S)=\{x\in S\mid \mu(\text{reach}(x)\setminus S)\ge\beta\,\mu(\mathcal{Q}_{\text{free}_i}\setminus S)\}$.
- Def. 7.4.4 **$(\epsilon,\alpha,\beta)$-expansive:** $\epsilon$-good and every connected $S$ has $\mu(\text{lookout}_\beta(S))\ge\alpha\,\mu(S)$.
- Fig. 7.19: two $W\times W$ rooms joined by a corridor of width $w$ ⇒ $\epsilon,\alpha,\beta\approx w/W$ (narrow passage ⇒ small constants).
- Def. 7.4.5 linking sequence $x_1=x,\dots,x_\ell$ with $x_i\in\text{lookout}_\beta(X_{i-1})$, $X_i=X_{i-1}\cup\text{reach}(x_i)$.
- Lemma 7.4.6: with $s=1/(\alpha\epsilon)$, a length-$t$ linking sequence for $x$ exists in $n$ uniform samples w.p. $\ge 1-s\,e^{-(n-t-1)/s}$. Lemma 7.4.7: if $t\ge\beta^{-1}\ln 4$ then $\mu(X_t)\ge\frac34\mu(\mathcal{Q}_{\text{free}_i})$.
- **Theorem 7.4.2:** with $2n+2$ uniform samples and $n=\left\lceil \frac{8\ln(8/(\epsilon\alpha\delta))}{\epsilon\alpha}+\frac{3}{\beta}\right\rceil$, w.p. $\ge 1-\delta$ every roadmap subgraph $G_i$ (per free-space component) is connected — i.e. the roadmap's connectivity matches $\mathcal{Q}_\text{free}$'s. Proof idea: grow linking sequences from $x$ and $y$ in one half of the samples; each covers ≥ 3/4 of the component so they overlap in ≥ 1/2; the other half of the samples lands in the overlap w.p. $\ge 1-e^{-n\epsilon/2}$; union-bound over $\binom n2$ pairs.
- Constants are nonzero for any polyhedral space ⇒ probabilistic completeness. Same notion underlies EST's completeness proof.

### §7.4.3 Abstract path tiling (book pp. 250–253)
Generalises to any probability measure $\mu$ (non-uniform sampling) and any measurable **reachability relation** $R\subset\mathcal{Q}_\text{free}\times\mathcal{Q}_\text{free}$ ($xRy$: local planner reaches $y$ from $x$; need not be symmetric or reflexive — covers dynamics/time).
- **Algorithm 15 Random incremental:** $x_0\leftarrow x$; loop: if $x_\ell R y$ return path; sample $x_{\ell+1}\sim\mu$; if not $x_\ell R x_{\ell+1}$ return "no path".
- **Theorem 7.4.3:** Alg. 15 succeeds with nonzero probability on every query ⇔ PRM is probabilistically complete; and then $\Pr[(x,y)\ \text{FAILURE}]\le \ell e^{-pn}$ for some constants $\ell\ge0$, $p>0$. ⇒ "probabilistic completeness always comes with an exponentially decaying failure bound". (Proof: decompose the set of successful length-$\ell$ sample sequences into a countable union of rectangles $A_{i1}\times\dots\times A_{i\ell}$, take one with max measure, $p=\min_j\mu(A_{ij})$, union bound.)

## §7.5 Beyond basic path planning (PDF pp. 269–283, book pp. 253–267) — Lessons: A4 (context), H (robot learning context)
- **Control-based (kinodynamic) planning (§7.5.1):** given controls $U$ and propagation $f:\mathcal{Q}\times U\to\mathcal{Q}$, generate samples by applying random controls (forward propagation). EST is pure forward propagation; RRT steers but need not reach $q_\text{rand}$ exactly. Typically a single tree from $q_\text{init}$, stop when goal reached within tolerance. Time can be added to the state (dynamic obstacles). PRM usable only if you can solve the two-point boundary value problem (exactly/approx.), e.g. cars, tractor-trailers.
- **Multiple robots (§7.5.2):** *centralized* (product C-space, complete in principle, high-dim) vs *decoupled* (plan each, then coordinate velocities along paths — incomplete even with complete subroutines) vs *prioritized* (plan in order, earlier robots become moving obstacles). Tree-planner trick [14]: move robots one at a time toward $q_\text{rand}$, adding each robot to the collision check incrementally. SRT: prioritized edges + incremental centralized endpoints.
- **Manipulation planning (§7.5.3):** transit (empty-handed) vs transfer (grasping) paths + regrasps; manipulation graph of stable placements; two-level PRM / FuzzyPRM [335]; nonprehensile manipulation (pushing [Mason], tray tilting, conveyor fences, parallel-jaw squeezes: $2n-1$ squeezes orient any part with $n$-sided convex hull [166], MEMS actuator arrays).
- **Assembly planning (§7.5.4):** treat a subassembly as the robot; removal paths for maintainability from CAD; α-puzzle narrow-passage benchmark (Fig. 7.23); NDBG.
- **Flexible objects (§7.5.5):** infinite-dimensional C-space ⇒ low-dim shape parametrizations, energy threshold for feasibility, collision checking expensive (no precomputed hierarchies); PRM of quasi-static low-energy configurations.
- **Biology (§7.5.6):** molecule = tree-like articulated robot with dihedral angles as DOF; energy-based acceptance $\Pr(\text{accept})=1$ if $E\le E_\min$, $\frac{E_\max-E}{E_\max-E_\min}$ in between, 0 above $E_\max$ (book p. 266); PRM for folding pathways; Stochastic Roadmap Simulation.

## Ch. 7 problems (PDF p. 283, book p. 267) — which to do
| # | Task | Why / ASBR link |
|---|---|---|
| 1 | random free configs of a rigid body with quaternions + local planner | uniform SO(3) sampling + slerp — the exact pieces of an EST/RRT state space in OMPL `SE3StateSpace` |
| 2 | polygon–polygon collision test | the collision oracle; separating-axis theorem |
| 4 | two distance functions for rigid and articulated 3-D robots, pros/cons | metric choice drives NN, EST density, RRT steering |
| 5 | closest-neighbour query with a grid | the SBL/EST grid trick |
| **6** | **basic PRM, polygonal robot in the plane** | core A4 exercise |
| **7** | add Gaussian or bridge-test sampling | narrow passages |
| 8 | path shortcutting/smoothing | post-processing (MoveIt adapters) |
| **9** | **SBL or RRT for a point robot, display trees** | direct rehearsal for Assignment 3 (implement EST/SBL) |
| 3 | local planner for 4 planar robots | multi-robot (optional) |

## Conventions / pitfalls (ch. 7)
- Book's `step_size` means two different things: the RRT extension distance (§7.2.2) and the collision-checking resolution along an edge (§7.1.2). Don't conflate (OMPL: `range` vs `longest_valid_segment_fraction`/state validity resolution).
- In Alg. 11 the book writes "toward $q_\text{rand}$" although the input is named $q$; in Connect/Merge it is the passed-in target. Also Alg. 11 checks only $q_\text{new}$; a real implementation must check the edge $q_\text{near}\to q_\text{new}$ (motion validator).
- RRT tree growth uses **nearest** node; EST uses **density-weighted random** node + random sample near it. Mixing these up is the most common error.
- EST "neighbourhood weight" counts nodes *of the same tree* near $q$, not obstacle proximity.
- Uniform random axis + uniform random angle (book's example, p. 214) is **not** uniform on SO(3) (it over-samples small rotations); use Shoemake's method (3 uniforms → unit quaternion) or normalised 4-D Gaussian.
- Typos in Thm 7.4.2 proof (book p. 249–250): "choosing $t\ge1.5\beta$" should be $t\ge1.5/\beta$ ($>\beta^{-1}\ln 4\approx1.39/\beta$); the chain "$e^{-n\epsilon/2}\le e^{-n/2\alpha\epsilon}$" has the wrong exponent — it should be $e^{-n\epsilon/2}\le e^{-n\alpha\epsilon/2}=e^{-n/(2s)}$ (since $\alpha\le1$). The theorem statement is fine.
- Halton ball-discrepancy (book p. 222) is printed as $O(N^{-(d+1)/2})$, which would be *better* than the box bound and than random sampling's $N^{-1/2}$ — implausible; the standard order is $N^{-1/2-1/(2d)}=N^{-(d+1)/(2d)}$. Treat as a typo.
- Probabilistic completeness says nothing about path quality: plain PRM/EST/RRT are **not asymptotically optimal** (RRT*, PRM* came in 2011 — outside this 2005 book).

---

## Ch. 8 Kalman Filtering — overview & §8.1 Probabilistic estimation (PDF pp. 284–287, book pp. 269–272) — Lessons: A5, F8
**Big idea:** Dead reckoning (integrating commands or odometry) only ever *loses* information — the PDF over pose spreads out (Fig. 8.1: B21 odometry drifts ~45° in heading). Sensors *inject* information. Every probabilistic estimator alternates **prediction** (push the PDF through the motion model; uncertainty grows) and **update** (merge with the measurement PDF; uncertainty shrinks). The Kalman filter is the special case where everything is linear and Gaussian, so the PDF stays Gaussian and is carried exactly by $(\hat x, P)$. Ch. 9 handles arbitrary (multimodal) PDFs.

## §8.2 Linear Kalman filtering — a geometric derivation (PDF pp. 287–304, book pp. 272–289) — Lessons: A5, F4 (projection/least squares), F8
**Model (Eqs. 8.1–8.2):** $x(k+1)=F(k)x(k)+G(k)u(k)+v(k)$, $\;y(k)=H(k)x(k)+w(k)$; $v\sim\mathcal{N}(0,V(k))$ process noise, $w\sim\mathcal{N}(0,W(k))$ measurement noise, both zero-mean **white** (independent across time); $H$ full row rank. Notation $\hat x(k_1\mid k_2)$ = estimate at $k_1$ given outputs up to $k_2$.
> Notation warning vs. most texts (Thrun, Welch–Bishop, ASBR slides): Choset uses $V$ = process covariance (others: $Q$), $W$ = measurement covariance (others: $R$), and **$R$ = Kalman gain** (others: $K$). $K$ in Choset is an auxiliary $p\times p$ matrix.

The derivation builds up in three steps — redo it, it is the best intuition for the KF available:
1. **Simple observer, no noise (§8.2.2, book pp. 274–277).** Predict $\hat x(k+1|k)=F\hat x(k|k)+Gu$ (8.5). Measurement confines the state to the affine set $\Omega=\{x: Hx=y(k+1)\}$. Update = **Euclidean projection** of the prediction onto $\Omega$: correction $\Delta x\perp\Omega\Rightarrow \Delta x\in\text{col}(H^T)$, $\Delta x=H^TK\nu$ with innovation $\nu=y-H\hat x(k+1|k)$; enforcing $H(\hat x+\Delta x)=y$ gives $K=(HH^T)^{-1}$, i.e. $\hat x^+=\hat x^-+H^T(HH^T)^{-1}\nu$ (pseudo-inverse / minimum-norm least squares — F4). Flaw: components parallel to $\Omega$ are never corrected.
2. **Observer with covariance, process noise only (§8.2.3, book pp. 277–282).** Gaussian PDF (8.8). Covariance prediction (8.12): $P(k+1|k)=FP(k|k)F^T+V$ (cross terms vanish because $v$ is zero-mean and independent of the estimate error). Update = the **most likely** point of $\Omega$ under $\mathcal{N}(\hat x^-,P^-)$ = projection in the **Mahalanobis** metric $\lVert x\rVert_M^2=x^TP^{-1}x$ (Fig. 8.3: smallest ellipse tangent to $\Omega$). Then $\Delta x=P^-H^T\gamma$, $K=(HP^-H^T)^{-1}$ (8.14), $R=P^-H^T(HP^-H^T)^{-1}$, $\hat x^+=\hat x^-+R\nu$ (8.18), $P^+=P^--RHP^-$ (8.19). Flaw: with noiseless measurements $P^+$ becomes singular.
3. **Full Kalman filter (§8.2.4, book pp. 282–284).** Measurement now gives a Gaussian in output space. Project the prediction to output space: $\hat y=H\hat x^-$, $\widehat W=HP^-H^T$. Fuse with $\mathcal{N}(y,W)$ using **Theorem 8.2.1 (product of Gaussians):** $(z_1,C_1)\cdot(z_2,C_2)\propto\mathcal{N}(z_3,C_3)$, $Q=C_1(C_1+C_2)^{-1}$, $z_3=z_1+Q(z_2-z_1)$, $C_3=C_1-QC_1$. The most likely output $y^*$ defines $\Omega^*=\{x:Hx=y^*\}$; Mahalanobis-project onto it (Fig. 8.4).

**Kalman filter summary (§8.2.5, Eqs. 8.26–8.32, book pp. 284–285)**
$$\begin{aligned}
&\text{predict:} && \hat x(k+1|k)=F\hat x(k|k)+Gu(k), \qquad P(k+1|k)=FP(k|k)F^T+V(k)\\
&\text{update:} && \nu=y(k+1)-H\hat x(k+1|k),\quad S=HP(k+1|k)H^T+W(k+1),\quad R=P(k+1|k)H^TS^{-1}\\
& && \hat x(k+1|k+1)=\hat x(k+1|k)+R\nu, \qquad P(k+1|k+1)=P(k+1|k)-RHP(k+1|k)
\end{aligned}$$
- $S$ = innovation covariance; large gain $R$ ⇒ trust sensor, small ⇒ trust prediction.
- Optimality (book p. 285): with Gaussian noise the KF output is not just "best" but the **exact posterior**; with non-Gaussian noise it is still the **best linear** (minimum mean-square-error) estimator.
- Problem 11: an unbiased initial estimate stays unbiased.

**Worked example — 1-D dead reckoning (§8.2.6, book pp. 285–288, Figs. 8.5–8.7):** state $[x_r,v_r]^T$, force input, $F=\begin{bmatrix}1&T\\0&1\end{bmatrix}$, $G=\begin{bmatrix}0\\T/m\end{bmatrix}$ (8.33), velocity sensor $H=[0\;1]$ (8.34); $m=1$, $W=0.5$, $T=0.5$, $V=\begin{bmatrix}0.2&0.05\\0.05&0.1\end{bmatrix}$, $\hat x=[2,4]^T$, $P=\text{diag}(1,2)$, $u=0$. Prediction ellipse grows and shears; update squashes it in velocity only. **Redo numerically** (good warm-up for ASBR KF assignment).

**Observability (§8.2.7, Thm 8.2.2):** LTI system is observable iff $\mathcal{O}=[H;\,HF;\,HF^2;\dots;HF^{n-1}]$ has rank $n$. In the example, $\mathcal{O}=\begin{bmatrix}0&1\\0&1\end{bmatrix}$ has rank 1 ⇒ position error grows without bound — not the filter's fault. For observable systems the KF error covariance stays bounded.

## §8.3 Extended Kalman filter (PDF pp. 304–309, book pp. 289–294) — Lessons: A5, F5 (Jacobians)
**Model (8.35–8.36):** $x(k+1)=f(x,u,k)+v$, $y=h(x,k)+w$, $f,h$ $C^1$. Linearize about the current estimate:
$$\hat x(k+1|k)=f(\hat x(k|k),u(k),k),\quad P(k+1|k)=FP(k|k)F^T+V,\quad F=\left.\tfrac{\partial f}{\partial x}\right|_{\hat x(k|k)}\ (8.37–8.39)$$
$$\nu=y(k+1)-h(\hat x(k+1|k)),\ S=HP^-H^T+W,\ R=P^-H^TS^{-1},\ H=\left.\tfrac{\partial h}{\partial x}\right|_{\hat x(k+1|k)}\ (8.40–8.45)$$
Key point: the **nonlinear** $f,h$ propagate the mean and form the innovation; the **Jacobians** only propagate covariance and form the gain.

**§8.3.1 Range–bearing localization with known landmarks (book pp. 290–292):** unicycle $x=[x_r,y_r,\theta_r]^T$, $u=[u_1,u_2]$ (forward and angular increments; note no $T$ — $u$ is per-step displacement), $f=[x_r+u_1\cos\theta_r,\ y_r+u_1\sin\theta_r,\ \theta_r+u_2]^T$, $F=\begin{bmatrix}1&0&-u_1\sin\theta_r\\0&1&u_1\cos\theta_r\\0&0&1\end{bmatrix}$. Measurement per visible landmark $j$ via association map $a(i)=j$: range $\sqrt{(x_r-x_j)^2+(y_r-y_j)^2}$, bearing $\operatorname{atan2}(y_r-y_j,x_r-x_j)-\theta_r$; $H_i$ rows: range $[\frac{x_r-x_j}{\rho},\frac{y_r-y_j}{\rho},0]$, bearing $[\frac{-(y_r-y_j)}{\rho^2},\frac{x_r-x_j}{\rho^2},-1]$; stack rows for the $p(k)$ visible landmarks.
**§8.3.2 Data association:** for measurement $i$ and landmark $j$, $\nu_{ij}=y_i-h(\hat x^-,j)$, $S_{ij}=H_{ij}P^-H_{ij}^T+W_i$, $\chi^2_{ij}=\nu_{ij}^TS_{ij}^{-1}\nu_{ij}$; choose $a(i)=\arg\min_j\chi^2_{ij}$ (nearest neighbour in Mahalanobis distance; gate with a $\chi^2$ threshold). Fig. 8.8: Nomad Scout EKF track.
**§8.3.3 Range-only:** delete the bearing rows.

## §8.4 Kalman filter SLAM (PDF pp. 309–312, book pp. 294–297) — Lessons: A5/A6 (enrichment)
- **Simple SLAM (Smith–Self–Cheeseman):** augment state with landmark positions $x=[x_r,y_r,x_{\ell1},y_{\ell1},\dots]^T$; omnidirectional robot $x_r^+=x_r+u_x$, landmarks static (zero process noise on landmark block of $V$); relative-displacement measurement $y_i=[x_{\ell i}-x_r;\,y_{\ell i}-y_r]+w_i$ is **linear**: $H_i=[-I_2\ 0\cdots I_2\cdots 0]$; stack all → ordinary linear KF solves SLAM.
- **Range–bearing SLAM (§8.4.2, Eq. 8.47):** EKF with dense robot columns and sparse landmark columns in $H_i$; data association by $\chi^2$ (8.48) plus a **new-landmark rule**: if the minimum $\chi^2$ exceeds a high threshold, initialise a new landmark.

## Ch. 8 problems worth doing (PDF pp. 312–315, book pp. 297–300)
- **P9 (EKF by hand):** unicycle with $T=0.25$, $\hat x(1|1)=[1,0.5,\pi/4]^T$, $u=[3,\pi]$, $P=I$, $y=x_1+w$, $y(2)=1.7$ → compute $\hat x(2|1),P(2|1),\hat x(2|2),P(2|2)$ — exactly the ASBR A5 EKF skill. (Note the book gives $v$ variance 0.5 but no $V$ matrix or $W$; assume $V=0.5I$, $W$ as needed — state your assumption.)
- **P10:** input noise ⇒ $P^-=FPF^T+G_u\,\text{diag}(\sigma_1^2,\sigma_2^2)G_u^T$ with $G_u=\partial f/\partial u$ — the standard way odometry noise enters (also Thrun's $V_tM_tV_t^T$).
- **P7** (derive 8.15), **P8** (prove product-of-Gaussians), **P4–P6** (Mahalanobis orthogonality, null/column spaces — F2/F4), **P3** (observability geometry), **P2** (discretize mass–spring–damper with Euler — F7), **P1** (where Gaussian-white-additive noise assumptions fail), **P11** (unbiasedness).

## Conventions / pitfalls (ch. 8) — including errors found in the book
- **Sign typo, book p. 283:** printed $y^*=\hat y+\widehat W(\widehat W+W)^{-1}(\hat y-y(k+1))$; by Thm 8.2.1 with $z_1=\hat y$, $z_2=y$ it must be $(y(k+1)-\hat y)$.
- Same page mixes $W(k)$ and $W(k+1)$ in the gain; use $W(k+1)$ (noise of the measurement being fused).
- **Eq. 8.34 typo:** printed $y(k+1)=[0\ 1]x(k)+w(k)\triangleq Gx(k)+w(k)$; should be $y(k)=Hx(k)+w(k)$ with $H=[0\ 1]$ ($G$ is already the input matrix).
- **Eq. 8.48 typo:** $\chi^2_{ij}$ printed with $S_{ij}$; it must be $S_{ij}^{-1}$ (as in §8.3.2, book p. 293).
- Bearing convention differs between §8.3.1 ($\operatorname{atan2}(y_r-y_j,x_r-x_j)-\theta_r$, i.e. direction *from landmark to robot*) and §8.4.2 ($\operatorname{atan2}(y_j-y_r,x_j-x_r)-\theta_r$, the usual robot→landmark bearing). They differ by $\pi$; the Jacobians are the same, but the predicted measurement must use the same convention as the sensor. Always wrap bearing innovations to $(-\pi,\pi]$ — the book never mentions angle wrapping.
- In the $P(k+1|k)$ derivation (book p. 278) the cross term is written "$2F(x-\hat x)v^T$"; it is really $Fe\,v^T+v\,e^TF^T$ (matrices don't commute) — harmless since both vanish in expectation.
- Covariance update $P^+=(I-RH)P^-$ is the "short form"; numerically prefer Joseph form $(I-RH)P^-(I-RH)^T+RWR^T$ (not in the book).

---

## Ch. 9 Bayesian Methods — §9.1 Localization (PDF pp. 316–342, book pp. 301–327) — Lessons: A5, A6, F8
**Big idea:** Drop the Kalman assumptions (linear, Gaussian, unimodal). Keep the same predict/update recursion but over an *arbitrary* belief. That is what makes **global localization** (unknown start) and the **kidnapped-robot problem** (must *unlearn* a confident wrong belief) solvable. Fig. 9.1 (1-D corridor with three identical doors) is the canonical picture: uniform prior → multimodal after seeing a door → shifted and blurred by motion → one dominant peak after the second door. Multimodality is a consequence of **unknown data association** (identical doors); if association were known a KF would suffice (book p. 304).

**Key equations (book pp. 304–310):**
- Belief / posterior: $P(x(k)\mid u(0{:}k-1),y(1{:}k))$; motion model $P(x(k)\mid u(k-1),x(k-1))$; observation model $P(y(k)\mid x(k))$; map $m$ implicit.
- **Recursive Bayes filter** (9.1 discrete, 9.2 continuous): $\text{bel}_k(x)=\eta_k\,P(y_k\mid x)\int P(x\mid u_{k-1},x')\,\text{bel}_{k-1}(x')\,dx'$.
- Prediction (9.3): $\overline{\text{bel}}_k(x)=\sum_{x'}P(x\mid u_{k-1},x')\,\text{bel}_{k-1}(x')$. Update (9.4): $\text{bel}_k(x)=\eta_kP(y_k\mid x)\overline{\text{bel}}_k(x)$, $\eta_k^{-1}=\sum_x P(y_k\mid x)\overline{\text{bel}}_k(x)$ (9.6) — computed on the fly.
- **Derivation (§9.1.3, 9.7–9.15):** Bayes rule with background knowledge → **Markov assumption #1** (measurement independent of past given current state, 9.8) → total probability over $x(k-1)$ (9.10) → **Markov assumption #2** ($x_k$ depends only on $x_{k-1},u_{k-1}$, 9.11) → assumption that $u_{k-1}$ carries no information about $x_{k-1}$ (9.13; the book's two-room counter-example shows this is only approximately true).
- Initial belief: uniform (global localization) or narrow Gaussian (tracking).

**§9.1.4 Representations of the posterior:**
1. **Kalman / EKF** — efficient, high-dim, floating-point accuracy; unimodal only; needs known start or unique landmarks (Gaussian mixtures as an extension).
2. **Grid (Markov localization)** — topological (junctions, doors, GVD nodes [Choset & Nagatani]) or fine metric 3-D grid over $(x,y,\theta)$, 10–30 cm and 2–10° suffice (Fig. 9.2). **Algorithm 16** (book p. 313): for each step, predict by summing over all predecessors (naïvely $O(N^2)$), multiply by likelihood, normalize. Efficient prediction [Fox, Burgard, Thrun]: **shift** each $\theta$-slice by the odometry offset $(\Delta x,\Delta y)$ (which depends on $\theta$), then **convolve** with a separable bounded Gaussian kernel $[0.25,0.5,0.25]$ per axis (9.16; borders $2/3,1/3$), repeated to match motion noise. Statistics: mean (with **circular mean** $\hat\phi=\operatorname{atan2}(\sum P\sin\phi,\sum P\cos\phi)$, 9.17) vs mode (always a feasible pose, but jumps). Cost exponential in dimension; fixes: selective update, tree-structured grids storing only high-probability cells. Example: AAAI '94 arena 31×22 m, 15 cm/3°, global localization from 24 sonar sweeps in 80 s (Figs. 9.3–9.4).
3. **Particle filter (MCL)** — $N$ weighted samples $(x,\omega)$.

**Algorithm 17 — particle-filter localization (book p. 317)** (sequential importance sampling with resampling)
```
for i = 1..k:
    for j = 1..N:  x_j ← sample from P(x | u(i-1), x_j)          # prediction = proposal
    η ← 0; for j: w_j ← P(y(i) | x_j); η ← η + w_j                # importance weights
    for j: w_j ← w_j / η
    M ← resample(M)
```
**Algorithm 18 — resample, O(N) "low-variance / systematic" (book p. 318)**
```
M' ← ∅; Θ ← rand((0, 1/N]); c ← ω_0; i ← 0
for j = 0..N-1:
    u ← Θ + j/N
    while u > c:  i ← i+1; c ← c + ω_i
    M' ← M' ∪ {(x_i, 1/N)}
```
(Single random number, $N$ evenly spaced pointers; Isard–Blake binary search is $O(N\log N)$.)

**Odometry motion model (Fig. 9.5, Eqs. 9.18–9.21, book pp. 317–319):** decompose each motion into rotation $\alpha$, translation $d$, rotation $\beta$; perturb
$\alpha'=\alpha+\alpha\,\mathcal{N}(\sigma_1)+d\,\mathcal{N}(\sigma_2)$, $\beta'=\beta+\beta\,\mathcal{N}(\sigma_3)+d\,\mathcal{N}(\sigma_4)$, $d'=d+d\,\mathcal{N}(\sigma_5)+(\alpha+\beta)\,\mathcal{N}(\sigma_6)$, then
$x^i\leftarrow x^i+[d'\cos(\theta^i+\alpha'),\ d'\sin(\theta^i+\alpha'),\ \alpha'+\beta']^T$ (same structure as Thrun's `sample_motion_model_odometry`; $\sigma_i$ learnable from data). Without observations the cloud grows and bends ("banana", Fig. 9.6). Map knowledge is easy to add: reject samples that pass through obstacles.
- Statistics: weighted mean; mode = max-weight particle *before* resampling ($O(N)$), after resampling need kd-tree ($O(N\log N)$) or a coarse histogram.
- Examples: global localization with 10 000 samples converges after ~65 sonar steps (Fig. 9.7); tracking (Fig. 9.8).
- **Kidnapping / failure recovery (book pp. 321–322):** inject a fraction of uniformly random particles [Fox]; or monitor the average likelihood $\tilde p=N^{-1}\sum_jP(y\mid x_j)$ and restart global localization when it falls below a threshold [Burgard]; adapt the number of random particles to $\tilde p$ [Lenser–Veloso]; smooth $\tilde p$ [Gutmann–Fox] (precursor of Augmented MCL).

**§9.1.5 Beam-based range sensor model (book pp. 322–327):** compute expected range $d(x)$ by ray casting in the map, $P(y\mid x)=P(y\mid d(x))$ (9.22). Mixture of four causes (Fig. 9.9 B21 sonar scan): (1) correct hit $\alpha\,\mathcal{N}(y;d,\sigma)$; (2) unmodelled object / crosstalk — short readings $\beta\,\lambda e^{-\lambda y}$; (3) random reading — uniform $\gamma$; (4) max-range failure — point mass $\delta$. Discretised histogram (9.23) $h_{i,j}=\int_{d_j-\Delta/2}^{d_j+\Delta/2}(\alpha_i\mathcal{N}+\beta_i\lambda_ie^{-\lambda_iy}+\gamma_i)\,dy$ for $j<n-1$, $h_{i,n-1}=\delta_i$, with $\sum_jh_{i,j}=1$ (9.24); parameters by **maximum likelihood** on recorded data (Figs. 9.10–9.11: laser much sharper than sonar; $\sigma$ also absorbs map uncertainty). Beams assumed independent, $P(y_1..y_m\mid x)=\prod_iP(y_i\mid x)$ (9.25) — over-confident, so subsample beams (Fox et al. used 60 of 181). Speed-ups: precompute expected distances in a lookup table (1 byte per entry, 256×256 histograms); non-smoothness near doorways/grazing angles → also learn per-state variance of the expected distance. Alternatives: endpoint/likelihood-field models (Konolige, Thrun) avoid ray casting.

## §9.2 Mapping (PDF pp. 343–362, book pp. 328–347) — Lessons: A6 (enrichment), C6 (ICP link)
**§9.2.1 Occupancy grid mapping with known poses (book pp. 328–336):**
- Each cell $m_l$ binary, cells assumed independent: $P(m)=\prod_lP(m_l)$. Bayes + Markov give the odds recursion (9.34) $\text{Odds}(m\mid x_{1:k},y_{1:k})=\dfrac{\text{Odds}(m\mid x_k,y_k)\,\text{Odds}(m\mid x_{1:k-1},y_{1:k-1})}{\text{Odds}(m)}$, $\text{Odds}(x)=P/(1-P)$ (9.33).
- **Log-odds form (9.35):** $\ell_k=\ell(m\mid x_k,y_k)-\ell_0+\ell_{k-1}$ — additive per-cell update with an **inverse sensor model** $P(m_l\mid x_k,y_k)$; with prior 0.5, $\ell_0=0$ (9.36). Recover $P=1-1/(1+\text{Odds})$ (9.37–9.39). **Algorithm 19** implements (9.40).
- Inverse sonar model (9.41–9.42, Figs. 9.12–9.16): piecewise linear in range $d$ along the beam — prior $-s$ for $d<y-d_1$ (free), linear ramp up through $y\pm d_1$, plateau prior $+s$ until $y+d_2$ (obstacle depth), ramp back to prior by $y+d_3$, prior beyond; $s(y,\theta)=g(y)\,\mathcal{N}(\theta;0,\sigma_\theta)$ with $g$ decreasing in range and a Gaussian in the off-axis angle (15° cone, $\sigma_\theta=0.05$). Maximum-likelihood map = threshold at 0.5 (Fig. 9.18).
- Limitations: cell independence, binary occupancy (partially covered cells), beam independence → overconfidence.

**§9.2.2 Bayesian SLAM (book pp. 337–345):**
- Full SLAM posterior over path and map (9.43). Two classic families: EKF-SLAM (landmarks; ch. 8) and global optimization (Lu–Milios spring network = early **pose-graph** SLAM; EM-based batch methods).
- **Incremental scan matching** (9.44): $\hat x_k=\arg\max_x P(y_k\mid x,\hat m_{k-1})P(x\mid u_{k-1},\hat x_{k-1})$ — trades map consistency against odometry consistency; typically ICP (CIS I C6 link). Greedy, never revises ⇒ locally good but globally inconsistent maps after loops (Figs. 9.19–9.20: 28×28 m, 491 m travelled).
- **Rao-Blackwellized particle filter (FastSLAM / GMapping family):** factor (9.45–9.47) $P(x_{1:k},m\mid u,y)=P(m\mid x_{1:k},y_{1:k})\,P(x_{1:k}\mid y_{1:k},u)$ — sample **paths** with a PF, compute each particle's map analytically (occupancy grid given known poses). Weight = $P(y_k\mid x_j,m^{(j)})$. **Algorithm 20** (book p. 343) = Alg. 17 with per-particle maps. Efficiency: share map history in trees (Montemerlo; Parr–Eliazar DP-SLAM) or build local maps from a few recent scans; reduce particle count by turning scan matching into accurate odometry (Hähnel et al.). Loop closure kills particles whose maps disagree (Fig. 9.22). Landmark version with per-landmark Gaussians = FastSLAM [Montemerlo].

## Ch. 9 problems worth doing (PDF pp. 360–362, book pp. 345–347)
| # | Task | Why |
|---|---|---|
| 1 | Bayes rule with background knowledge | used in every derivation line |
| **2** | **derive the KF from the Bayes filter (9.2)** | ties A5 together; shows KF = Bayes filter with Gaussians |
| **6** | circular 10-place world, landmarks at 0, 3, 6 ($P(z\mid L)=0.8$, else 0.4); observe L, move 3, observe L, move 4, observe ¬L | hand discrete Bayes filter. My answer: posterior $[0.10, 0.075, 0.075, 0.05, 0.15, 0.075, 0.025, \mathbf{0.30}, 0.075, 0.075]$ for places 0–9, max at place 7 (= 0+3+4) |
| 7 | same with 0.8 success / 0.2 stay motion | effect of motion noise (my run: belief flattens, max 0.15) |
| **4, 5** | implement odometry motion model + MCL with Algs. 17/18 | ASBR A6 particle filter |
| 3 | implement the beam sensor model, histograms with 3-inch bins | sensor-model practice |
| 9 | PF with an almost perfect sensor | particle deprivation; why you inflate $\sigma$ / need more particles |
| 11 | circular world with a PF | compare with exact grid answer |
| 13 | occupancy convergence with $P(m\mid y_1)=0.8$, $P(m\mid y_2)=1/3$, $y_1$ one-third of the time | log-odds increments $\ln4$ and $-\ln2$: per 3 readings $\ln4-2\ln2=0$ ⇒ stays at the prior (0.5) — a nice sanity check of (9.35) |
| 8, 10, 12, 14, 15 | conceptual (independence, obstacle-aware motion, vision likelihoods, sensor sharpness, snail-shell SLAM) | discussion |

## Conventions / pitfalls (ch. 9)
- Choset indexes inputs $u(0{:}k-1)$ and measurements $y(1{:}k)$ (input $u(k-1)$ moves $x(k-1)\to x(k)$); Thrun's *Probabilistic Robotics* uses $u_{1:t}$, $z_{1:t}$ with $u_t$ moving $x_{t-1}\to x_t$. Same filter, shifted index.
- Book p. 304 writes the posterior once as $P(x(k)\mid u(1{:}k-1),y(1{:}k))$ — typo for $u(0{:}k-1)$; p. 306 calls the motion model "the posterior $P(x(k)\mid u(k-1),x(k-1))$" — misnomer.
- The book's particle mean is a plain weighted average; for $\theta$ use the circular mean as in (9.17).
- In (9.21) the term $(\alpha+\beta)\,\mathcal{N}(\sigma_6)$ should use $|\alpha|+|\beta|$ (opposite rotations shouldn't cancel the noise) — a common fix in implementations.
- Weighted resampling is done **every** step in Alg. 17; in practice resample only when $N_\text{eff}=1/\sum w_j^2$ is low (not in the book).
- Grid-based and particle-based filters approximate the *same* Bayes filter; KF/EKF is the Gaussian special case.

---

## Ch. 10 Robot Dynamics (PDF pp. 363–386, book pp. 349–372) — Lessons: R7, R8 (also F7)
**Big idea:** A *path* is geometry; a *trajectory* is path + timing, so it needs masses, inertias, actuator limits, gravity, friction. Lagrange's recipe — write kinetic minus potential energy in generalized coordinates and differentiate — gives the equations of motion mechanically (even symbolically in Mathematica/Maple). The structure $M\ddot q+C\dot q+g=u$ is entirely determined by $M(q)$ (plus $V$): Coriolis/centrifugal terms are the "bending" of unforced motion caused by non-inertial coordinates.

**Key results & equations:**
- **Euler–Lagrange (10.1):** $L=K-V$, $\frac{d}{dt}\frac{\partial L}{\partial\dot q}-\frac{\partial L}{\partial q}=u$.
- Ex. 10.1.1 planar body, $L=\tfrac12m(\dot q_1^2+\dot q_2^2)+\tfrac12I\dot q_3^2-ma_gq_2$: $u_1=m\ddot q_1$, $u_2=m\ddot q_2+ma_g$, $u_3=I\ddot q_3$. **Book (10.3) prints $u_2=m\ddot q_2-ma_g$ — sign error**: $\partial L/\partial q_2=-ma_g$, so $-\partial L/\partial q_2=+ma_g$ (holding the body still needs an *upward* force; compare the $+a_g$ terms in the RP arm, 10.6).
- **Ex. 10.1.2 RP arm (book pp. 351–353, redo it):** $L=\tfrac12(I_1+I_2+m_1r_1^2+m_2q_2^2)\dot q_1^2+\tfrac12m_2\dot q_2^2-a_g\sin q_1(m_1r_1+m_2q_2)$ (10.5);
  $u_1=(I_1+I_2+m_1r_1^2+m_2q_2^2)\ddot q_1+2m_2q_2\dot q_1\dot q_2+a_g(m_1r_1+m_2q_2)\cos q_1$, $u_2=m_2\ddot q_2-m_2q_2\dot q_1^2+a_gm_2\sin q_1$ (10.6).
- **Standard form (10.7):** $u=M(q)\ddot q+C(q,\dot q)\dot q+g(q)$, $M$ symmetric positive definite. With friction: $+\,b(q,\dot q)$.
- **Christoffel symbols (10.8–10.9):** $c_i=\sum_{j,k}\Gamma^i_{jk}\dot q_j\dot q_k$, $\Gamma^i_{jk}=\tfrac12\left(\frac{\partial M_{ij}}{\partial q_k}+\frac{\partial M_{ik}}{\partial q_j}-\frac{\partial M_{kj}}{\partial q_i}\right)$; $j=k$ terms centrifugal, $j\ne k$ Coriolis; one choice $C_{ij}=\sum_k\Gamma^i_{kj}\dot q_k$ (this choice makes $\dot M-2C$ skew-symmetric — not stated in the book but key for R8 passivity proofs). Compact (10.10) $u=M\ddot q+\dot q^T\Gamma(q)\dot q+g$. RP arm: $\Gamma^1_{12}=\Gamma^1_{21}=m_2q_2$, $\Gamma^2_{11}=-m_2q_2$ (Ex. 10.2.1). Polar point mass: $\Gamma^1_{22}=-mq_1$, $\Gamma^2_{12}=\Gamma^2_{21}=mq_1$. Only the *sum* $M\ddot q+C\dot q$ is a generalized force — the split depends on coordinates.
- **Actuator mapping (10.11–10.12):** $T(q)f=M\ddot q+C\dot q+g$; for thrusters at a point with forward kinematics $x=\phi(q)$, $T=J^T(q)$ (statics duality $u=J^Tf$ — R5). If $T$ has full rank, define virtual actuators $u=Tf$ ("feedback transformation").
- **Kinetic energy (10.13–10.14):** $K=\tfrac12\dot q^TM\dot q$, $M_{ij}=\partial^2K/\partial\dot q_i\partial\dot q_j$; $M$ constant ⇒ Christoffels vanish (planar body); mobile manipulators: invariant to base pose but not arm joints.
- **Pfaffian velocity constraints (§10.3):** $A(q)\dot q=0$ (10.15). Constrained Lagrange: $M\ddot q+C\dot q+g=u+A^T\lambda$, $A\ddot q+\dot A\dot q=0$ (10.16–10.17); $\lambda=(AM^{-1}A^T)^{-1}(-\dot A\dot q+AM^{-1}(C\dot q+g-u))$. Projections $P_u=I-A^T(AM^{-1}A^T)^{-1}AM^{-1}$ (10.18) and $P=M^{-1}P_uM=I-M^{-1}A^T(AM^{-1}A^T)^{-1}A$ (10.19), $P=P_u^T$, rank $n-k$; $P\ddot q=PM^{-1}(u-C\dot q-g)$ (10.20). Orthogonality is **with respect to the inertia metric $M$**, not the identity. Ex. 10.3.1 knife-edge $A=[\sin q_3,-\cos q_3,0]$ (10.21–10.28). Holonomic (integrable to $c(q)=0$, e.g. point on a circle $x\dot x+y\dot y=0$) vs nonholonomic (ch. 12).
- **Rigid bodies (§10.4):** mass, COM; planar $\tau_z=I_{zz}\ddot q$ (10.32), parallel-axis $I_{z'z'}=I_{zz}+md^2$ (10.33). Spatial: $K=\tfrac12\omega_s^TI_s\omega_s$ (10.36), **Euler's equation** spatial $\tau_s=\hat\omega_sI_s\omega_s+I_s\dot\omega_s$ (10.38) with $\dot R=\hat\omega_sR$ (10.39); body frame $I=R^TI_sR$ constant (10.41–10.42), $\dot R=R\hat\omega$, $\tau=\hat\omega I\omega+I\dot\omega$ (10.43–10.44); principal axes (10.45): $\tau_x=I_{xx}\dot\omega_x+(I_{zz}-I_{yy})\omega_y\omega_z$ etc. — $\dot\omega\ne0$ possible with $\tau=0$. Composite bodies: shift with $I_i'=I_i+m_i(\lVert r_i\rVert^2I-r_ir_i^T)$ (10.46), rotate $I_i''=R_iI_i'R_i^T$, sum. Fig. 10.5 inertia table (sphere $2mR^2/5$, cylinder $m(3R^2+h^2)/12$ and $mR^2/2$, box $m(b^2+c^2)/12$, ellipsoid $m(b^2+c^2)/5$). Euler angles (10.47–10.49) give $M(q)$ but are singular and messy.

**Exercises worth doing (book pp. 370–372):** **P2 + P3** (2R arm in a vertical plane: Lagrange → standard form → 8 Christoffel symbols — exactly the RDKDC R7 homework), P1 (polar point mass, Christoffels), P4 (PR arm), P6 (inertia transform with given $R_1$, $r_1$ — R1/R2 practice), P7 (barbell inertia), P8 (prove 10.46), P9 (derive Euler-angle rates), P10 (simulate tumbling — intermediate-axis instability), P11–P13 (point on sphere: Pfaffian constraint, projection $P$, reduced coordinates).

**Conventions / pitfalls (ch. 10):**
- Book Eq. (10.3) gravity sign error (see above).
- Book: "positive definite iff determinant and trace positive" (p. 353) — **true only for 2×2**. For $n\times n$ use Sylvester (all leading principal minors > 0) or all eigenvalues > 0.
- Book: holonomic example integrates to "$x^2+y^2=R$" (p. 361) — should be $R^2$.
- Choset uses $V$ for potential energy here but $V$ for process-noise covariance in ch. 8, and $u$ for generalized forces (MLS uses $\tau$). $\hat\omega$ = skew matrix (book uses a tilde/hat).
- Spatial vs body angular velocity: $\dot R=\hat\omega_sR=R\hat\omega_b$ — same as MLS $\hat\omega^s=\dot RR^T$, $\hat\omega^b=R^T\dot R$ (R1).

---

## Ch. 11 Trajectory Planning, §11.1–11.3 (PDF pp. 387–410, book pp. 373–396; chapter continues past this range) — Lessons: R8, A4 (kinodynamic context), F6
**Big idea:** Two routes to a dynamically feasible, (near-)optimal trajectory for a fully actuated robot. **Decoupled:** plan a collision-free path $q(s)$ (chs. 4–7), then find the time-optimal **time scaling** $s(t)$ under actuator limits — a 2-D phase-plane problem with a bang-bang answer. **Direct:** search in state space — optimal control (Pontryagin), nonlinear optimization (SQP over parameterized trajectories/controls), grid search, RRTs (ch. 7), or potential-field feedback (ch. 4).

**Definitions (§11.1):** path $q:[0,1]\to\mathcal{Q}$ ($C^2$); time scaling $s:[0,t_f]\to[0,1]$, $C^2$, $\dot s>0$; trajectory $q(s(t))$.

**§11.2 Time-optimal time scaling (Bobrow–Dubowsky–Gibson; Shin–McKay):**
- $\dot q=q'\dot s$, $\ddot q=q'\ddot s+q''\dot s^2$ (11.2–11.3) ⇒ dynamics along the path (11.6): $a(s)\ddot s+b(s)\dot s^2+c(s)=u$, with $a=Mq'$, $b=Mq''+q'^T\Gamma q'$, $c=g$.
- Actuator limits $u^\min\le u\le u^\max$ (11.1) ⇒ per joint $\alpha_i=(u_i^\max-b_i\dot s^2-c_i)/a_i$, $\beta_i=(u_i^\min-\dots)/a_i$ (11.8) (swap if $a_i<0$) ⇒ $L(s,\dot s)=\max_iL_i\le\ddot s\le U(s,\dot s)=\min_iU_i$ (11.9). **Motion cones** in the $(s,\dot s)$ plane (Fig. 11.1); **velocity limit curve** $v(s)$ where $L=U$ (11.10); above it is inadmissible.
- Optimal = maximize $\dot s$ everywhere ⇒ **bang-bang**: always on $U$ or $L$, at least one actuator saturated.
- **Time-scaling algorithm (book p. 378, Fig. 11.3):** (1) $S=\{\}$; (2) integrate $\ddot s=L$ **backward** from $(1,\dot s_f)$ → curve $F$; (3) integrate $\ddot s=U$ **forward** from current point → $A_i$; if it crosses $F$ record the switch and stop; if it hits the velocity limit, (4) search forward along $v(s)$ for a **tangent point** ($dv/ds=U/v$), integrate $L$ backward from it to meet $A_i$ (max→min switch), (5) from the tangent point (min→max switch) go to 3. No solution if $\dot s=0$ reached.
- **Zero-inertia points (§11.2.1):** $a_i(s)=0$ ⇒ actuator $i$ gives a *velocity* constraint (11.11); critical and singular points; at singular points use tangent accelerations $\ddot s^\max=\min(\ddot s^+_\text{tangent},U)$, $\ddot s^\min=\max(\ddot s^-_\text{tangent},L)$ to slide along singular arcs.
- **Ex. 11.2.1 (book pp. 381–383):** RP arm along the straight line $x(s)=[2s-1,1]^T$; IK $q=[\operatorname{atan2}(x_2,x_1),\lVert x\rVert]$; zero-inertia point at $s=\tfrac12$; $m_1=5$, $I_1=0.1$, $r_1=0.2$, $m_2=3$, $I_2=0.05$, $|u_1|\le20$ N·m, $|u_2|\le40$ N, $a_g=9.8$ ⇒ $t_f\approx0.888$ s, one switch. Good project to implement.
- **§11.2.2 Global time optimality (Shiller–Dubowsky):** grid of candidate workspace paths, cheap lower bounds on time, spline smoothing, full time scaling as upper bound, prune, local optimization.

**§11.3 Direct trajectory planning:**
- **Optimal control (§11.3.1):** $\dot x=f(x,u)$ (11.24), $J=\int_0^{t_f}L(x,u,t)dt$ (11.25), Hamiltonian $\mathcal{H}=L+\lambda^Tf$ (11.26); **Pontryagin minimum principle** $\mathcal{H}(x^*,u^*,\lambda^*)\le\mathcal{H}(x^*,u,\lambda^*)$ (11.27); unconstrained: $\partial\mathcal{H}/\partial u=0$ (11.28), Legendre–Clebsch $\partial^2\mathcal{H}/\partial u^2>0$ (11.29), adjoint $\dot\lambda=-\partial\mathcal{H}/\partial x$ (11.30).
  - **Ex. 11.3.1 (redo):** double integrator, min $\int u^2$, rest-to-rest distance $d$ in $t_f$: $u(t)=\frac{6d}{t_f^2}-\frac{12d}{t_f^3}t$ (linear control ⇒ cubic position profile — the same cubic used for joint-space interpolation in R8). Min-time with $|u|\le u_\max$: $\partial\mathcal{H}/\partial u=\lambda_2$ has no $u$; the minimum principle gives $u^*=-u_\max\operatorname{sign}(\lambda_2)$ — bang-bang, with $\lambda_2$ linear in $t$ ⇒ at most one switch.
  - **Shooting:** guess $\lambda(0)$, integrate, correct by approximate gradient of the final-state map.
- **Nonlinear optimization (§11.3.2, 11.33–11.45):** parameterize $q(t)$ (compute $u$ by inverse dynamics), or $u(t)$ (integrate forward), or both (collocation-style, dynamics as equality constraints at sample points); constraints enforced at $k+1$ points spaced $t_f/k$; solve with SQP; objective/constraints must be $C^1$ (better $C^2$); analytic gradients; local optima ⇒ multiple initial guesses or seed with a global planner.
- **Grid search (§11.3.3, Donald–Xavier; Alg. 21):** controls $\{-a_\max,0,a_\max\}^{n}$ (Fig. 11.10, $3^n$ branches), timestep $h$, BFS in state space on the lattice (Δq̇ multiples of $a_\max h$, Δq multiples of $\tfrac12a_\max h^2$), prune states already reached, near obstacles, or over $v_\max$ ⇒ time-optimal for the discretization. **Thm 11.3.2:** for a point robot in 2-D/3-D polygonal worlds, with $h$ chosen from $\epsilon$, safety parameters $c_0,c_1$ (speed-dependent clearance $c_0+c_1\lVert\dot q\rVert$), $v_\max,a_\max$, finds a $(1-\epsilon)$-safe trajectory no slower than $T_\text{opt}$ in time polynomial in $1/\epsilon$ ($O(\epsilon^{-3n})$), exponential in $n$.

**Exercises:** none in this range (ch. 11 problems are beyond PDF p. 410).

**Pitfalls (ch. 11):** Choset's "Lagrangian" $L$ in optimal control is the running cost, not $K-V$ (book flags this). $L(s,\dot s)$ in §11.2 is the *lower acceleration bound*, unrelated to either. Time scaling assumes the robot can hold any configuration statically ($\dot s=0$ admissible) and at most one admissible→inadmissible switch per $s$ (no "islands").

---

## Corrections & upgrades for the knowledge base
Checked against Notion ASBR page (A3–A7, fetched 2026-11-26) and `courses/05-algorithms-sensor-based-robotics/README.md` §5.4–5.5.

**A3 (C-space, potential fields, graph search)**
- [A3] "Learn from: Choset ch. 2–4" → week 5 also covers **roadmaps and cell decompositions** (Choset ch. 5 visibility graph/GVD, book pp. 107–160; ch. 6 trapezoidal/boustrophedon, book pp. 161–196), and the A*/Dijkstra material is **Choset Appendix H**, not ch. 2–4 → change to "Choset ch. 2–6 + App. H". (Ch. 5–6 sit in Part 1 of the PDF; only §6.3 pursuit/evasion and the ch. 6 problems fall in this file's range.)
- [A3] Lecture text never mentions the visibility graph, GVD or trapezoidal decomposition although the README (§5.4) does → add a short paragraph and a build item: Choset ch. 6 Problem 2 (trapezoidal-decomposition planner for a translating convex robot) is the natural coding exercise.
- [A3] No error found in the potential formulas (match README and Choset ch. 4 notation $\zeta,\eta,Q^*$; ch. 4 itself is outside this range).

**A4 (sampling-based planners) — vs Choset ch. 7**
- [A4] Claims checked and correct: PRM = sample, connect neighbours with collision-free local paths, search (Alg. 6–7, pp. 204–206); RRT grows toward random samples (Alg. 10–11, p. 234); EST "expands from nodes in sparsely explored regions" (p. 232); probabilistic completeness definition (p. 201, 243).
- [A4] Incomplete EST description → add the actual mechanism (book Alg. 8–9, pp. 230–232): pick tree node $q$ with $\pi_T(q)\propto1/w_T(q)$, $w_T$ = number of tree nodes in a neighbourhood of $q$; sample $q_\text{new}$ *in the neighbourhood of $q$*; add it **only if the local planner connects $q\to q_\text{new}$**; bidirectional merge = connect each new node to its $k$ nearest nodes in the other tree, else swap. Practical density estimate = coarse grid/projection (SBL, p. 238) — this is what OMPL `EST`/`ProjEST`/`BiEST` do.
- [A4] Note for Assignment 3: the 2026 handout weights nodes by **degree** ($w\approx1/(1+\deg)$, README §4.2). That is Choset's **connection-sampling** weight for PRM (p. 224), *not* the book's EST density weight. Both favour under-explored nodes, but in a tree, degree ≠ local density (a leaf in a crowded region has degree 1). Follow the handout, but know the difference. Likewise the handout samples nearby with a Gaussian; the book samples uniformly in a neighbourhood — either is fine.
- [A4] Add the precise completeness statements: random sampling → probabilistic completeness, deterministic/quasirandom/grid → **resolution** completeness (p. 201, 222); failure probability decays **exponentially**, $\Pr[\text{fail}]\le\lceil2L/\rho\rceil e^{-\sigma\rho^dn}$ (Thm 7.4.1, p. 244) — narrow passages hurt as $\rho^d$. Likely exam derivation.
- [A4] Missing topics worth a line each: local planner + **subdivision vs incremental edge collision checking** (pp. 211–213; the A3 `is_local_path_collision_free` TODO); distance metrics for SE(3) / embeddings (pp. 209–211); kd-tree/ANN nearest neighbours (p. 209; the A3 code uses FLANN); goal bias $p\approx0.05$ (p. 236); RRT-Connect/merge (Alg. 12–13); LazyPRM/SBL lazy collision checking (pp. 226–228, 238); narrow-passage samplers (Gaussian, bridge, OBPRM, p. 217).
- [A4] "Learn from: Choset ch. 7" → make it specific: §7.1.1–7.1.2 (pp. 203–215), §7.2 (pp. 227–238), §7.4.1 (pp. 243–246); RRT* is **not** in Choset (2005) — cite Karaman & Frazzoli 2011 for it (README already does).
- [A4] Build list: add "bidirectional EST with grid-based density" and Choset ch. 7 Problems 6, 7, 9.

**A5 (KF/EKF) — vs Choset ch. 8** (bonus: in this range)
- [A5] Notation clash to flag in the lesson: Choset uses $F,G,H$; $V$ = process covariance; $W$ = measurement covariance; **$R$ = Kalman gain**. Notion/README use Thrun's $A,B,H,Q,R,K$, so "R" means opposite things in the two books.
- [A5] Self-test answer "gain near $H^{-1}$" → $H$ is generally not square. Better: as $W\to0$ (or $P^-\to\infty$), $K\to P^-H^T(HP^-H^T)^{-1}$, the $P^{-1}$-weighted right pseudo-inverse of $H$ (Choset Eq. 8.21, p. 281) — the update then projects the prediction onto the measurement hyperplane $\{x:Hx=z\}$; only if $H$ is invertible is this $H^{-1}$.
- [A5] Add Choset §8.2 (pp. 274–285) to "Learn from": the geometric (projection/Mahalanobis) derivation is the clearest intuition for the KF; §8.2.7 observability test $\text{rank}[H;HF;\dots;HF^{n-1}]=n$; §8.3.1–8.3.2 EKF range-bearing localization + $\chi^2$ data association (Leonard's `Chap8-Kalman-Mapping.pdf` is derived from it).
- [A5] Book errata to warn about: $y^*$ sign typo (p. 283), Eq. 8.34 $G$ vs $H$ typo, Eq. 8.48 missing inverse on $S_{ij}$, inconsistent bearing convention §8.3.1 vs §8.4.2 (see ch. 8 pitfalls above).
- README §5.5 formulas (KF, EKF unicycle $F$, range-bearing $H$) checked: correct.

**A6 (PF/MCL) — vs Choset ch. 9**
- [A6] Correct as written. Add Choset §9.1 (pp. 302–327) as a reference: Alg. 17 (PF), Alg. 18 (O(N) low-variance resampling), odometry motion model (9.18–9.21), beam sensor-model mixture (9.23), kidnapped-robot recovery via average likelihood (p. 322). Choset Problem 6 (circular 10-place world) is a perfect hand-computed discrete Bayes filter warm-up (answer in ch. 9 section above). Choset uses the beam model; A5 uses the likelihood field (Choset p. 327 mentions it only in passing → keep PR ch. 6 as main ref).

**R7 (dynamics) — vs Choset ch. 10**
- [R7] Choset ch. 10 (pp. 349–372) is a compact Lagrangian reference: RP-arm worked example, Christoffel symbols, Pfaffian constraints with $M$-orthogonal projections, Euler's equations. Errata: Eq. 10.3 gravity sign; "PD iff det and trace > 0" only for 2×2; $x^2+y^2=R$ → $R^2$.

**Errata list for Choset ch. 7 (for the A4 lesson):** Thm 7.4.2 proof "$t\ge1.5\beta$" → $1.5/\beta$; "$e^{-n\epsilon/2}\le e^{-n/2\alpha\epsilon}$" → $e^{-n\alpha\epsilon/2}$; Halton ball-discrepancy exponent (p. 222); random axis + random angle is not uniform on SO(3) (p. 214); Alg. 11 checks only $q_\text{new}$, not the edge.

## Best-reference map
- [A3] Choset §6.3 + ch. 6 problems (PDF 206–212, book 190–196) — enrichment only; main A3 material (ch. 2–6, App. H) is in Part 1 / appendices.
- [A4] **Choset ch. 7** (PDF 213–283, book 197–267): PRM §7.1.1 (book 203–208) · practical PRM: sampling, kd-tree, metrics, local planner, edge collision checking, smoothing §7.1.2 (208–215) · sampling strategies §7.1.3 (216–225) · connection strategies / LazyPRM §7.1.4 (225–227) · **EST §7.2.1 (230–233)** · RRT/RRT-Connect §7.2.2 (233–238) · SBL §7.2.3 (238) · SRT §7.3 (238–242) · completeness analysis §7.4 (242–253) · kinodynamic/multi-robot/manipulation §7.5 (253–262) · problems p. 267.
- [A5] **Choset ch. 8** (PDF 284–315, book 269–300): KF derivation §8.2.2–8.2.5 (274–285) · dead-reckoning example §8.2.6 (285–288) · observability §8.2.7 (287–289) · EKF §8.3 (289–294) · data association §8.3.2 (292–293) · EKF-SLAM §8.4 (294–297) · problems 297–300 (P9 EKF by hand).
- [A5/A6] **Choset ch. 9** (PDF 316–362, book 301–347): Bayes filter derivation §9.1.2–9.1.3 (304–310) · grid localization Alg. 16 (312–316) · particle filter Alg. 17–18 + motion model (316–322) · beam sensor model §9.1.5 (322–327) · occupancy grids / log-odds §9.2.1 (328–337) · RBPF SLAM §9.2.2 (337–345) · problems 345–347.
- [R7] **Choset ch. 10** (PDF 363–386, book 349–372): Lagrange + RP arm (349–353), Christoffel symbols (353–355), constraints (357–361), rigid-body/Euler equations (361–370), problems 370–372 (P2–P3 = 2R arm).
- [R8/A4] Choset ch. 11 §11.2 time-optimal time scaling (book 374–384), §11.3.1 Pontryagin + min-effort cubic example (385–389), §11.3.2 trajectory optimization (389–392), §11.3.3 grid search (392–396).
- [F8] Thm 8.2.1 product of Gaussians (book p. 283); Bayes rule with background knowledge (ch. 9 P1).
- [F4] Simple observer = minimum-norm least squares / projection (book pp. 274–277).
