# Choset et al., *Principles of Robot Motion* (2005) — Part 1: Ch. 1–6 (through §6.3 start)
Source: cb2bd4a2-Robot_Motion_Theory_and_Algorithms.pdf, PDF pages 1–205 (front matter + book pp. 1–189)
Authors: Choset, Lynch, Hutchinson, Kantor, Burgard, Kavraki, Thrun (MIT Press). **Primary textbook of JHU ASBR (EN.601.663).**

## Chapter → PDF page map (whole book; 616 PDF pages)
The PDF omits blank verso pages at chapter ends, so the offset (PDF − book) drifts from +19 to +13. Use this table, not a fixed offset.

| Book part | Book pages | PDF pages | Offset | ASBR week |
|---|---|---|---|---|
| Cover, series, title, copyright, dedication | — | 1–6 | | |
| Contents (vii–xiv) | vii–xiv | 7–14 | | |
| Foreword (Latombe), Preface, Acknowledgments | xv–xxi | 14–19 | | |
| 1 Introduction | 1–15 | 20–34 | +19 | |
| 2 Bug Algorithms | 17–38 | 35–56 | +18 | |
| 3 Configuration Space (problems pp. 71–75) | 39–75 | 57–93 | +18 | wk 2 |
| 4 Potential Functions (problems p. 105) | 77–106 | 94–123 | +17 | wk 4 |
| 5 Roadmaps (problems pp. 155–159) | 107–159 | 124–176 | +17 | wk 5 |
| 6 Cell Decompositions (problems p. 195) | 161–196 | 177–212 | +16 | wk 5 |
| 7 Sampling-Based Algorithms (problems p. 267) | 197–267 | 213–283 | +16 | wk 6 |
| 8 Kalman Filtering (problems pp. 297–299) | 269–299 | 284–314 | +15 | wk 8–9 |
| 9 Bayesian Methods (problems pp. 345–347) | 301–347 | 316–362 | +15 | wk 10–12 |
| 10 Robot Dynamics | 349–371 | 363–385 | +14 | |
| 11 Trajectory Planning | 373–399 | 387–413 | +14 | |
| 12 Nonholonomic & Underactuated Systems | 401–471 | 415–485 | +14 | |
| App A Notation / B Sets | 473 / 475 | 487 / 489 | +14 | |
| App C Topology & metric spaces (C.5 Jacobians & gradients p. 483) | 478–486 | 492–500 | +14 | |
| App D Curve tracing (implicit fn thm, Newton–Raphson) | 487–488 | 501–502 | +14 | |
| App E Representations of orientation (Euler, RPY, axis-angle, quaternions) | 489–498 | 503–512 | +14 | wk 2 (A1) |
| App F Polyhedral robots (star algorithm, SE(2) C-obstacles, GJK-style distances) | 499–512 | 513–526 | +14 | wk 2 (A3) |
| App G Algorithms & complexity | 513–520 | 527–534 | +14 | |
| App H Graph search: BFS/DFS (H.1), A* (H.2, p. 527), Dijkstra (H.2.3), D* (H.3, p. 536) | 521–546 | 535–560 | +14 | wk 5 (A3) |
| App I Statistics primer | 547–551 | 561–565 | +14 | wk 8 (A5) |
| App J Linear systems & control (observability J.4) | 552–563 | 566–577 | +14 | wk 8 (A5) |
| Bibliography | 565–596 | 578–609 | +13 | |
| Index | 597–603 | 610–616 | +13 | |

Within my range: §3.5.1 matrix reps p. 60 = PDF 78; §3.6 SO(3) p. 66 = PDF 84; §3.8 velocity p. 69 = PDF 87; §4.1 p. 80 = PDF 97; §4.3.2 Brushfire p. 86 = PDF 103; §4.5 wavefront p. 90 = PDF 107; §4.6 navigation fns p. 93 = PDF 110; §4.7 p. 99 = PDF 116; §5.1 visibility graph p. 110 = PDF 127; §5.2 GVD p. 117 = PDF 134; §6.1 trapezoidal p. 162 = PDF 178; §6.2 Morse/boustrophedon p. 168 = PDF 184.

Preface's suggested semester themes (p. xviii): *Path Planning* = ch. 3, 4, G, 5, 7, 6; *Mobile Robotics* = 2, H, 3, 4, 5, D, 6; *Position Estimation* = I, J, 8, 9. ASBR effectively runs Path Planning + Position Estimation.

---

## Front matter & Ch. 1 Introduction (PDF pp. 1–34, book pp. i–15) — Lessons: A3 (vocabulary)
**Big idea:** Motion planning = compiling a high-level task into motions that respect geometry, mechanics, and uncertainty. The book's organising move is to plan in the **configuration space** Q, not the workspace W: a robot becomes a point, obstacles become C-obstacles, and a path is a curve in $Q_{free}$.

**Key definitions (pp. 9–15):**
- Tasks: **navigation**, **coverage**, **localization**, **mapping** (and SLAM = localization + mapping).
- Robot properties: DOF (= dimension of Q), **omnidirectional** vs **nonholonomic** (velocity constraint, e.g. a car can't slide sideways), kinematic (velocity inputs) vs dynamic (force inputs).
- Algorithm properties: optimal vs feasible; complexity; **complete** (finds a path if one exists, else reports failure in finite time); **resolution complete** (complete at a given discretisation); **probabilistic complete** (P(success) → 1 as time → ∞); **offline** vs **online/sensor-based**.
- Piano mover's problem (free-flying rigid body, known static obstacles, exact execution; 6 DOF); sofa mover's (planar); generalized mover's (linked bodies).
- Notation (p. 14): workspace W, obstacles $WO_i$, $W_{free}=W\setminus\bigcup_i WO_i$; robot occupancy $R(q)$; C-obstacle $QO_i=\{q \mid R(q)\cap WO_i\neq\emptyset\}$; $Q_{free}=Q\setminus\bigcup_i QO_i$.

**Key results & equations:**
- Eq (1.1) path: $c:[0,1]\to Q$, $c(0)=q_{start}$, $c(1)=q_{goal}$, $c(s)\in Q_{free}\ \forall s$; $c\in C^0$.
- Path vs **trajectory**: a trajectory is time-parameterised and must be $C^2$ (velocities and accelerations exist). "Path planning" vs "trajectory/motion planning".
- Convention: configurations, velocities, forces are column vectors $q=[q_1,\dots,q_n]^T$.

**Conventions / pitfalls:** Optimality, completeness and complexity trade off against each other (p. 11). The book does not treat low-level feedback control; it assumes a controller exists.

---

## Ch. 2 Bug Algorithms (PDF pp. 35–56, book pp. 17–38) — Lessons: A3 (sensor-based planning), F5 (implicit fn thm, Jacobian null space), F6 (Newton corrector)
**Big idea:** The simplest planners with *provable* guarantees: a point robot in the plane with perfect odometry, a known goal, and unknown obstacles alternates two behaviours, *move toward goal* and *follow boundary*. Bug1 is exhaustive (circle the whole obstacle, leave at the best point); Bug2 is greedy (leave at the first better point on the fixed m-line). Tangent Bug uses a range sensor to cut corners. The implementation section is a hidden gem: boundary-following at a safe distance is curve tracing of the implicit curve $D(x)-W^*=0$ by predictor–corrector with a Newton step.

**Assumptions (p. 17–18):** point robot, perfect positioning, contact sensor (Bug1/2) or 360° infinite-resolution range sensor (Tangent Bug), can measure $d(x,y)$, bounded workspace ($W\subset B_r(x)$ for some finite r).

**Key definitions:**
- **m-line**: in Bug1 the segment from the current leave point $q^L_i$ to $q_{goal}$ (it changes); in **Bug2 it is fixed**, $q_{start}\to q_{goal}$.
- **Hit point** $q^H_i$ (first contact with obstacle i), **leave point** $q^L_i$.
- Raw distance function (2.3): $\rho(x,\theta)=\min_{\lambda\in[0,\infty]} d(x, x+\lambda[\cos\theta,\sin\theta]^T)$ s.t. $x+\lambda[\cos\theta,\sin\theta]^T\in\bigcup_i WO_i$. Saturated version $\rho_R$ = ρ if ρ < R, else ∞.
- **Interval of continuity**: a connected set of boundary points where $\rho_R$ is finite and continuous in θ; endpoints $O_i$ arise from occlusion (ray tangent to obstacle) or range limit.
- Tangent Bug quantities: blocking obstacle (closest sensed obstacle intersecting segment $x\to q_{goal}$), followed obstacle; $d_{followed}$ = shortest distance from goal to any sensed point of the followed obstacle so far; $d_{reach}=\min_{c\in\Lambda} d(q_{goal},c)$ over visible points Λ of the followed obstacle; T = point where the sensor circle of radius R meets segment $x\to q_{goal}$.

**Key results & equations:**
- (2.1) $L_{Bug1}\le d(q_{start},q_{goal})+1.5\sum_{i=1}^n p_i$ (full circumnavigation + at most half perimeter back to the leave point), $p_i$ = perimeter.
- (2.2) $L_{Bug2}\le d(q_{start},q_{goal})+\tfrac12\sum_{i=1}^n n_i p_i$, $n_i$ = # intersections of the start–goal line with obstacle i. So Bug2 can be **arbitrarily worse** than Bug1 for obstacles that cross the m-line many times (fig 2.4); Bug2 wins on simple obstacles.
- Bug1 failure test: if moving from the leave point toward the goal immediately re-enters the same obstacle → goal unreachable. Bug2 failure test: re-encounter the hit point without finding a valid m-line leave point.

**Algorithms:**
- *Bug1 (Alg 1, p. 19):* loop { from $q^L_{i-1}$ move to goal until goal or hit $q^H_i$; follow boundary all the way round back to $q^H_i$; go (by the shorter way) to the perimeter point $q^L_i$ closest to goal; if the step toward the goal hits this obstacle, report failure }.
- *Bug2 (Alg 2, p. 22):* loop { move along m-line until goal or hit $q^H_i$; turn left (or right) and follow boundary until goal, or $q^H_i$ re-encountered (fail), or m-line re-met at a point m ≠ $q^H_i$ with $d(m,q_{goal})<d(q^H_i,q_{goal})$ and the step toward goal is free; set $q^L_{i+1}=m$ }. (As extracted, line 13 reads "d(m,q_goal) < d(m,q^H_i)", which appears inconsistent with the text on p. 20: "closer to the goal than the initial point of contact"; use $d(m,q_{goal})<d(q^H_i,q_{goal})$.)
- *Tangent Bug (Alg 3, p. 28):* motion-to-goal: move continuously toward $n\in\{T,O_i\}$ minimising heuristic $d(x,n)+d(n,q_{goal})$ until goal or this heuristic starts increasing (local minimum M of $d(\cdot,q_{goal})$ restricted to the path). Then boundary-following in the same direction as the last motion: keep moving toward the $O_i$ on the followed obstacle in that direction, updating $d_{reach},d_{followed}$; stop when goal reached, a full loop completed (fail), or $d_{reach}<d_{followed}$ (then resume motion-to-goal). With zero sensor range Tangent Bug ≈ Bug2-like but hitting an obstacle does *not* switch modes; with infinite range it never boundary-follows (fig 2.13).

**Implementation (§2.3, pp. 30–37):**
- Distance to nearest obstacle (2.4): $D(x)=\min_{c\in\bigcup_i WO_i} d(x,c)=\min_\theta\rho(x,\theta)$. The **gradient** $\nabla D(x)$ is the unit vector pointing *away* from the closest point along the shortest ray; so the sensor with the smallest reading points along $-\nabla D$.
- Sonar: time-of-flight; accurate in depth, poor in azimuth (Polaroid beam ≈ 22.5°, reflection anywhere on an arc of length $2\pi d\,22.5/360$); "centerline model" assumes echo from cone axis.
- **Offset curve** at safety distance $W^*$: $G(x)=D(x)-W^*=0$. Tangent space = null space of the Jacobian $DG(x)=DD(x)=\nabla D(x)^T$ → tangent direction $(\nabla D(x))^\perp$.
- Predictor–corrector: step $\Delta\lambda$ along the tangent (predict), then Newton correct in the orthogonal coordinate y: Eq (2.5) $y^{h+1}=y^h-(D_YG)^{-1}G(y^h,\lambda^h)$. Valid by the implicit function theorem when $D_YG$ is surjective/full rank (true since $DG=[0\ D_YG]$ on the offset curve). See App D.

**Exercises worth doing (p. 37–38):** 2 (Bug1 vs Bug2 tradeoffs, exam-style); 4 & 5 (compare Tangent Bug with zero range vs Bug2 on fig 2.11, draw examples); 7 (prove/disprove Bug1 never touches obstacles outside the disk of radius $d(q_{start},q_{goal})$ about the goal — nice proof practice); 9 (completeness of Tangent Bug); 11–13 (write $D_YG$; why $D(x)+1$ and $D(x)+2$ have the same Jacobian; implicit function $y^3+y-x^2=0$ near origin → F5 practice).

**Conventions / pitfalls:** Bug algorithms are **complete** under their assumptions (perfect localisation is the big one — Problem 6/8). Bug2's m-line is fixed; Bug1's isn't. "Grazing" the line to the goal does not trigger boundary following. Choset reuses D for both distance and differential (p. 36) — read from context.

---
## Ch. 3 Configuration Space (PDF pp. 57–93, book pp. 39–75) — Lessons: A3 (C-space, C-obstacles), A1 / F3 / R1–R2 (SO(n), SE(n), frames vs transforms), F5 / R5 (Jacobian), F2
**Big idea:** A configuration is a *complete* specification of where every point of the robot is; Q is the space of all of them, and its dimension is the DOF. Planning for a body with volume becomes planning for a point in Q among C-obstacles. The deep lesson is that Q is usually **not** $\mathbb{R}^n$: the 2R arm lives on the torus $T^2$, a planar body on $SE(2)\cong\mathbb{R}^2\times S^1$, a spacecraft on $SE(3)$. Topology dictates representation: no single chart covers $S^1$, $T^2$ or $SO(3)$, so you either live with singularities (Euler angles), use an atlas, or **embed** in a higher-dimensional space with constraints (rotation matrices, unit quaternions). The chapter ends with the Jacobian of a map between representations, $\dot x=J(q)\dot q$.

### 3.1 Specifying a configuration (PDF 57–60, pp. 39–42)
- Configuration, C-space Q (some authors write C), DOF = dim Q = *minimum* number of parameters.
- Translating disk: $q=(x,y)$, $R(x,y)=\{(x',y') : (x-x')^2+(y-y')^2\le r^2\}$, $Q=\mathbb{R}^2$.
- 2R arm without joint limits: $Q=S^1\times S^1=T^2$; flattened to $[0,2\pi)\times[0,2\pi)$ with wrap-around edges (fig 3.2). The arm's **workspace** (reachable end-effector points) is an annulus, which is *not* a C-space because interior points are reached by two configurations (elbow-up/down = left/right arm, fig 3.3).

### 3.2 Obstacles and the configuration space (PDF 61–65, pp. 43–47)
- $QO_i=\{q\in Q\mid R(q)\cap WO_i\ne\emptyset\}$; $Q_{free}=Q\setminus(\bigcup_i QO_i)$; **free path** $c:[0,1]\to Q_{free}$; **semifree path** $c:[0,1]\to cl(Q_{free})$ (contact allowed). $Q_{free}$ is assumed open.
- Disk robot: C-obstacle = slide the robot around the obstacle and trace the reference point → obstacle "grows" by r (walls grow inward). Growing can **disconnect** $Q_{free}$ (fig 3.5c: biggest robot has no solution).
- **Minkowski sums (not named in ch.3; see App F.3, PDF 521–522, book p. 507–508):** for a *translating* convex polygon the C-obstacle is the **Minkowski difference** (F.7) $WO\ominus R=\{c-r : c\in WO,\ r\in R(0)\}$, i.e. $WO\oplus(-R)$ (reflect the robot about its reference point, then Minkowski-sum). The **star algorithm** builds it in $O(n_R+n_O)$ by negating robot edge normals, merging/sorting with obstacle edge normals by angle, and emitting vertices $o_j-r_i$ (App F.3). For rotation ($SE(2)$) the C-obstacle is a stack of such slices over θ (App F.4, fig F.8). Exercise 3.4 (convex ⊕ convex is convex) and 3.18 (program it) drill this.
- 2R arm: no closed form for C-obstacles, so **grid the torus** and collision-check each cell (figs 3.6–3.8, made with the Berkeley C-space applet). Pitfall: a grid only certifies grid points, not the edges between them; fix by *thickening* the robot at each test (conservative ⇒ planner is only **resolution complete**) or by choosing a fine enough resolution (footnote 3, p. 47). Fig 3.8 shows a solution path that uses the wrap-around.

### 3.3 Dimension of the configuration space (PDF 65–68, pp. 47–50)
- Point-counting method: planar rigid body — place A (2), B on circle about A (1), C fixed by $d(A,C), d(B,C)$ (0) ⇒ 3 DOF, $Q=\mathbb{R}^2\times S^1$. Spatial rigid body: A (3) + B on sphere (2) + C on circle (1) = 6 = 9 coordinates − 3 distance constraints.
- **Holonomic constraint** $g(q,t)=0$: each independent one removes one dimension, so n coordinates with m constraints ⇒ $(n-m)$-dim Q. **Nonholonomic** $g(q,\dot q,t)=0$ does not reduce dim Q (ch. 12).
- Planar 2R via counting: 12 coordinates − 10 constraints = 2.
- Joints: R (1), P (1), spherical (3). Serial chain DOF = sum of joint DOF.
- **Grübler's formula** Eq (3.1): $M=N(k-1)-\sum_{i=1}^n (N-f_i)=N(k-n-1)+\sum_{i=1}^n f_i$, with N = 3 planar / 6 spatial, k links *including ground*, n joints, $f_i$ joint freedoms. Valid only if joint constraints are independent. Fig 3.9: $3(6-7-1)+7=1$. (Four-bar: $3(4-4-1)+4=1$.)
- Redundant (more DOF than the task needs; human arm has > 6) and hyper-redundant.

### 3.4 Topology (PDF 68–75, pp. 50–58)
- Surjective (onto), injective (1-1), bijective; image $\phi(S)$, preimage $\phi^{-1}(T)$. E.g. $\sin:(-\pi/2,\pi/2)\to(-1,1)$ bijective; $\sin:\mathbb{R}\to[-1,1]$ only surjective.
- **Def 3.4.1 homeomorphism**: bijection with φ and $\phi^{-1}$ continuous. **Def 3.4.2 diffeomorphism**: smooth ($C^\infty$) bijection with smooth inverse. Diffeo ⇒ homeo.
- Circle (3.2) $x^2+y^2-1=0$, ellipse (3.3) $x^2/4+y^2-1=0$, racetrack (3.4–3.5, two half circles + straight segments): all homeomorphic (e.g. $\phi(x,y)=(x,y)/\sqrt{x^2+y^2}$ maps ellipse→circle and is in fact a diffeomorphism), but the racetrack is **not** diffeomorphic to the others (curvature jumps at $(\pm1,\pm1)$).
- Open ball $B_\epsilon(p)=\{p' : d(p,p')<\epsilon\}$, neighbourhood, locally homeo/diffeomorphic. Joint limits $\theta_l<\theta<\theta_u$ turn $T^2$ into an open subset diffeomorphic to $\mathbb{R}^2$ (stretch with tan).
- **Def 3.4.3 manifold** (k-dim): locally homeomorphic to $\mathbb{R}^k$. **Def 3.4.4 chart** $(U,\phi)$, φ a diffeomorphism from open U to an open set of $\mathbb{R}^k$; $\phi^{-1}$ is a **parameterization**. **Def 3.4.5 $C^\infty$-related** charts: transition maps $\psi\circ\phi^{-1}$ and $\phi\circ\psi^{-1}$ smooth on the overlap. **Atlas** = $C^\infty$-related charts covering Q = differentiable structure.
- Atlas for $S^1$ (p. 57): $U_1=\{x_2>0\},\phi_1=x_1$; $U_2=\{x_2<0\},\phi_2=x_1$; $U_3=\{x_1>0\},\phi_3=x_2$; $U_4=\{x_1<0\},\phi_4=x_2$; parameterizations $\phi_1^{-1}(z)=(z,\sqrt{1-z^2})$, $\phi_2^{-1}(z)=(z,-\sqrt{1-z^2})$, $\phi_3^{-1}(z)=(\sqrt{1-z^2},z)$, $\phi_4^{-1}(z)=(-\sqrt{1-z^2},z)$, $z\in(-1,1)$; e.g. $\phi_1\circ\phi_3^{-1}(z)=\sqrt{1-z^2}$. Two charts suffice (Problem 9).
- §3.4.3 **Connected** (path-connected for manifolds); obstacles split $Q_{free}$ into **connected components**; no solution if start and goal are in different components. **Compact** ≈ closed and bounded: $S^n, T^n, SO(n)$ compact; $\mathbb{R}^n$, $SE(n)$ not. A joint limit is compact only if the joint may *reach* the limit. Compact factor of $M_1\times M_2$. Compact and non-compact spaces are never diffeomorphic.
- §3.4.4 Not all C-spaces are manifolds: closed square is a manifold *with boundary*; some 1-DOF parallel mechanisms have figure-eight C-spaces.

### 3.5 Embeddings; matrix representations (PDF 76–84, pp. 59–66) — A1 / R1 / R2 / F3
- Single-chart impossible ⇒ choose: (i) minimal parameters with singularities, (ii) atlas, (iii) **embedding** in higher-dim $\mathbb{R}^n$ with constraints (submanifold). $S^1$ cannot embed in $\mathbb{R}^1$; $T^2$ cannot embed in $\mathbb{R}^2$. Any differentiable manifold embeds in $\mathbb{R}^n$ for n large (Whitney).
- Matrix groups are **Lie groups** (smooth manifold + smooth group op + smooth inverse; footnote 9).
- **Rotation matrix** $R=[\tilde x\ \tilde y\ \tilde z]$: columns = body axes expressed in the stationary frame. 9 numbers − 6 constraints (3 unit norm + 3 orthogonality) = 3 DOF; right-handed ⇒ det = +1. $SO(n)=\{R\in\mathbb{R}^{n\times n}: RR^T=I,\ \det R=1\}$, $R^T=R^{-1}$. $SO(2)$: $\begin{bmatrix}\cos\theta&-\sin\theta\\ \sin\theta&\cos\theta\end{bmatrix}$.
- **Homogeneous transform** $T=\begin{bmatrix}R&p\\0&1\end{bmatrix}\in SE(3)$, p = origin of body frame in stationary coords. $SE(n)=\begin{bmatrix}SO(n)&\mathbb{R}^n\\0&1\end{bmatrix}$.
- Three uses: (1) represent a configuration ("frame"), (2) change reference frame, (3) displace a point/frame ("transform").
- **Subscript-cancelling rule:** $T_{AB}T_{BC}=T_{AC}$; points in homogeneous coords $w=[\cdot,\cdot,\cdot,1]^T$, $T_{BC}w_C=w_B$.
- **Worked example (fig 3.17, pp. 63–64) — redo it by hand:** $T_{AB}=\begin{bmatrix}-1&0&0&-2\\0&-1&0&0\\0&0&1&0\\0&0&0&1\end{bmatrix}$, $T_{BC}=\begin{bmatrix}0&1&0&-4\\-1&0&0&-1\\0&0&1&0\\0&0&0&1\end{bmatrix}$ ⇒ $T_{AC}=\begin{bmatrix}0&-1&0&2\\1&0&0&1\\0&0&1&0\\0&0&0&1\end{bmatrix}$; $w_C=[-2,1,0,1]^T$, $w_B=[-3,1,0,1]^T$, $w_A=[1,-1,0,1]^T$.
- **Displacement:** $T_{AB}w_A$ violates subscript cancelling — it *moves* w (rotate about A's origin by $R_{AB}$, then translate by $p_{AB}$): $w'_A=[-3,1,0,1]^T$ (fig 3.18).
- **Body vs world frame transforms (p. 65–66):** $T_{AB'}=T_{AB}T_1=\begin{bmatrix}R_{AB}R_1&R_{AB}p_1+p_{AB}\\0&1\end{bmatrix}$ = rotate about B's own origin by $R_1$ (in B coords) then translate by $p_1$ in original B frame (**body-frame, post-multiply**). $T_{AB''}=T_1T_{AB}=\begin{bmatrix}R_1R_{AB}&R_1p_{AB}+p_1\\0&1\end{bmatrix}$ = rotate about A's origin, translate in A (**world-frame, pre-multiply**). n world transforms: $T_n\cdots T_1T_{AB}$; n body transforms: $T_{AB}T_1\cdots T_n$. Fig 3.19 numbers: $T_1=\begin{bmatrix}-1&0&0&0\\0&-1&0&1\\0&0&1&0\\0&0&0&1\end{bmatrix}$ gives $T_{AB'}$ with $p=(-2,-1)$ and $T_{AB''}$ with $p=(2,1)$, both $R=I$.

### 3.6 Parameterizations of SO(3) (PDF 84–85, pp. 66–67) — A1 / R1 / F3
- No global 3-parameter chart for SO(3) (like $S^1$ with one angle).
- **Z-Y-Z Euler angles** $(\phi,\theta,\psi)$: rotate about z by φ → about the *new* y by θ → about the *new* z by ψ; Eq (3.6–3.8):
  $R=R_{z,\phi}R_{y,\theta}R_{z,\psi}=\begin{bmatrix}c_\phi c_\theta c_\psi-s_\phi s_\psi & -c_\phi c_\theta s_\psi-s_\phi c_\psi & c_\phi s_\theta\\ s_\phi c_\theta c_\psi+c_\phi s_\psi & -s_\phi c_\theta s_\psi+c_\phi c_\psi & s_\phi s_\theta\\ -s_\theta c_\psi & s_\theta s_\psi & c_\theta\end{bmatrix}$.
  Successive rotations about *body* (moving) axes multiply on the **right**. Singular at $\theta=0,\pi$ (only $\phi\pm\psi$ determined) — details in App E (PDF 503–512: Euler, roll-pitch-yaw, axis-angle, quaternions).

### 3.7 Example configuration spaces (PDF 85–87, pp. 67–69)
- Table 3.1: translating planar robot $\mathbb{R}^2$; translating+rotating planar robot $SE(2)$ or $\mathbb{R}^2\times S^1$; translating 3D body $\mathbb{R}^3$; spacecraft $SE(3)$ or $\mathbb{R}^3\times SO(3)$; n-joint revolute arm $T^n$; planar mobile base + n-joint arm $SE(2)\times T^n$.
- Not equal (≠): $T^n\ne S^n$; $S^1\times S^1\times S^1\ne SO(3)$; $SE(2)\ne\mathbb{R}^3$; $SE(3)\ne\mathbb{R}^6$. (So 3 independent angles do **not** make SO(3) — the classic misconception behind "just interpolate Euler angles".)
- Dimensions: 1-D $\mathbb{R}^1, SO(2)$; 2-D $\mathbb{R}^2,S^2,T^2$; 3-D $\mathbb{R}^3,SE(2),SO(3)$; 6-D $\mathbb{R}^6,T^6,SE(3)$.

### 3.8 Transforming configuration and velocity representations (PDF 87–89, pp. 69–71) — R5, F5, A1
- Forward kinematics $\phi:Q\to M$, inverse $\phi^{-1}$ (may not be homeomorphisms, dims may differ). Velocity: $\dot x=\frac{\partial\phi}{\partial q}\dot q=J(q)\dot q$, J = differential $D\phi$. J also maps forces (via $J^T$, §4.7 and ch. 10).
- **Example 3.8.1 (2R arm, p. 69–70):** $\phi(q)=\begin{bmatrix}L_1c_1+L_2c_{12}\\L_1s_1+L_2s_{12}\end{bmatrix}$, $J=\begin{bmatrix}-L_1s_1-L_2s_{12}&-L_2s_{12}\\L_1c_1+L_2c_{12}&L_2c_{12}\end{bmatrix}$. With $L_1=L_2=1,\theta_1=\pi/4,\theta_2=\pi/2,\dot q=[1,0]^T$: $J=\begin{bmatrix}-\sqrt2&-\sqrt2/2\\0&-\sqrt2/2\end{bmatrix}$, $\dot x=[-\sqrt2,0]^T$. **Singular when $\sin\theta_2=0$** (arm straight or folded): rank 1, motion impossible in one direction. IK is one-to-two (elbow up/down) → Problem 20 (law of cosines + atan2).
- **Example 3.8.2 (point on a planar polygon):** $x=\begin{bmatrix}q_1\\q_2\end{bmatrix}+\begin{bmatrix}\cos q_3&-\sin q_3\\ \sin q_3&\cos q_3\end{bmatrix}\begin{bmatrix}r_1\\r_2\end{bmatrix}$, $J=\begin{bmatrix}1&0&-r_1\sin q_3-r_2\cos q_3\\0&1&r_1\cos q_3-r_2\sin q_3\end{bmatrix}$ (2×3, inverse is one-to-many). This J is exactly what ch. 4 §4.7.2 uses to map workspace forces on control points to C-space generalized forces.

**Exercises worth doing (pp. 71–75):** 2 (DOF of 9 systems — fast DOF intuition, exam staple); 4 & 5 (convexity of C-obstacles; union commutes with the C-obstacle operator); 6 (DOF of a rigid body in n-space: $n+n(n-1)/2$, with $n(n-1)/2$ rotational — two proofs); 9, 10 (two-chart atlases for $S^1$, $S^2$); 12 (count connected components of fig 3.7's C-space); 13–15 (compact vs non-compact; diffeo (a,b)→ℝ; torus implicit equation $(\sqrt{x^2+y^2}-R)^2+z^2-r^2=0$); **16 ($T^{-1}=\begin{bmatrix}R^T&-R^Tp\\0&1\end{bmatrix}$ — must know for A1/R2)**; **17 (compose world-frame rotations, transform points — direct A1/F3 drill)**; **18 & 19 (programs: C-space of translating convex polygon via Minkowski/star; 2R C-space grid at 1° — this is the ASBR C-space assignment pattern)**; **20 (2R IK) & 21 (3R FK + Jacobian)** → R4/R5/R6; 22 (where FK is a diffeomorphism); 23 (SE(2) vs $\mathbb{R}^2\times SO(2)$ as groups: same topology, different group — SE(2) is non-commutative, semidirect product).

**Conventions / pitfalls:**
- Choset's rotation matrix columns are *body axes in the stationary frame* (same as MLS $R_{ab}$, Lynch & Park $R_{sb}$).
- Post-multiply = body frame, pre-multiply = world frame. Z-Y-Z Euler composes about moving axes ⇒ right-multiplication.
- The book's text on p. 56 says chart $\phi_1:U_1\to(0,1)$ but its own parameterization on p. 57 uses $(-1,1)$; $(-1,1)$ is correct for the upper semicircle.
- Grid-based C-spaces are only resolution complete; edges between grid cells are not checked unless you thicken the robot.
- Joint-space workspace (annulus) ≠ configuration space.

---
## Ch. 4 Potential Functions (PDF pp. 94–123, book pp. 77–106) — Lessons: A3 (core), F6 (gradient descent), F5 (gradients/Hessians), R5 (Jacobian transpose statics), A1 (J^T as in resolved-rate/J-transpose IK)
**Big idea:** Instead of building Q explicitly, put an artificial energy landscape U on it: the goal is a sink, obstacles are hills, and the robot rolls downhill, $\dot c(t)=-\nabla U(c(t))$. It is cheap, online, and works in any dimension — but generic additive potentials have **spurious local minima**, so the planner is not complete. Two cures: (i) a grid potential with only one minimum (the **wave-front planner**, resolution complete but exponential in dimension), or (ii) an analytically constructed **navigation function** (Rimon–Koditschek) on sphere/star worlds. Finally, for real robots we define potentials in the workspace at **control points** and pull the forces back to C-space with $u=J^T f$.

### Intro (PDF 94–97, pp. 77–80)
- Potential $U:\mathbb{R}^m\to\mathbb{R}$, differentiable; gradient $\nabla U(q)=DU(q)^T=[\partial U/\partial q_1,\dots,\partial U/\partial q_m]^T$ points to steepest *increase*. Gradient vector field: work around any closed path is zero.
- In this chapter systems are **first-order** — gradients are treated as **velocities**, not forces (until §4.7).
- Critical point $q^*$: $\nabla U(q^*)=0$; classify by the **Hessian** $[\partial^2U/\partial q_i\partial q_j]$: nonsingular ⇒ non-degenerate/isolated; positive-definite ⇒ local min; negative-definite ⇒ local max; indefinite ⇒ saddle. Gradient descent generically ends only at local minima (maxima and saddles are unstable to perturbation), so no need to compute the Hessian.
- Two fixes for local minima: search-based planner on top of the potential; or navigation functions. Both require full prior knowledge of Q.

### 4.1 Additive attractive/repulsive potential (PDF 97–101, pp. 80–84) — **know these formulas cold**
$U(q)=U_{att}(q)+U_{rep}(q)$.
- **Conic** attractive: $U=\zeta\,d(q,q_{goal})$, $\nabla U=\frac{\zeta}{d(q,q_{goal})}(q-q_{goal})$ — constant magnitude ζ, undefined at goal ⇒ **chattering** near the goal.
- **Quadratic** attractive, Eq (4.1): $U_{att}=\tfrac12\zeta d^2(q,q_{goal})$, $\nabla U_{att}=\zeta(q-q_{goal})$ — vanishes linearly at goal (less overshoot) but grows unbounded far away.
- **Combined** Eq (4.2)/(4.3): 
  $U_{att}(q)=\begin{cases}\tfrac12\zeta d^2(q,q_{goal}), & d\le d^*_{goal}\\ d^*_{goal}\zeta d(q,q_{goal})-\tfrac12\zeta (d^*_{goal})^2, & d>d^*_{goal}\end{cases}$, 
  $\nabla U_{att}(q)=\begin{cases}\zeta(q-q_{goal}), & d\le d^*_{goal}\\ \dfrac{d^*_{goal}\zeta(q-q_{goal})}{d(q,q_{goal})}, & d>d^*_{goal}\end{cases}$ — $C^1$ at the switch (both equal $\zeta(q-q_{goal})$ there); far-field gradient magnitude saturates at $\zeta d^*_{goal}$.
- **Repulsive** (closest obstacle) Eq (4.4)/(4.5), with $D(q)$ = distance to nearest obstacle:
  $U_{rep}(q)=\begin{cases}\tfrac12\eta\left(\frac1{D(q)}-\frac1{Q^*}\right)^2, & D(q)\le Q^*\\0, & D(q)>Q^*\end{cases}$, 
  $\nabla U_{rep}(q)=\begin{cases}\eta\left(\frac1{Q^*}-\frac1{D(q)}\right)\frac1{D^2(q)}\nabla D(q), & D\le Q^*\\0, & D>Q^*\end{cases}$. 
  $Q^*$ = radius of influence, η = gain (both by trial and error). Since $1/Q^*-1/D<0$ inside the influence zone, $\nabla U_{rep}$ points *toward* the obstacle and $-\nabla U_{rep}$ pushes away; $U_{rep}\to\infty$ as $D\to0$.
- **Per-obstacle version** to kill oscillations on two-way-equidistant points where D is non-smooth: Eq (4.6) $d_i(q)=\min_{c\in QO_i}d(q,c)$; Eq (4.7) for convex $QO_i$ with closest point c: $\nabla d_i(q)=\frac{q-c}{d(q,c)}$ (unit vector away from nearest point); $U_{rep_i}=\tfrac12\eta(1/d_i-1/Q_i^*)^2$ for $d_i\le Q^*_i$, $U_{rep}=\sum_i U_{rep_i}$. Requires convex obstacles (or decomposition into convex pieces).

### 4.2 Gradient descent (PDF 101–102, pp. 84–85) — F6
- **Algorithm 4:** $q(0)=q_{start}$; while $\nabla U(q(i))\ne0$: $q(i+1)=q(i)-\alpha(i)\nabla U(q(i))$; $i\leftarrow i+1$. **Erratum: the book prints "+α(i)∇U" in line 4 (p. 85); the text and $\dot c=-\nabla U$ require a minus.**
- Step size α(i): small enough not to jump into obstacles, large enough to be fast; often ad hoc (e.g. scaled by distance to nearest obstacle or goal). Stop when $\|\nabla U\|<\epsilon$.

### 4.3 Computing distance in the plane (PDF 102–106, pp. 85–89)
- §4.3.1 **Sensor-based:** $D(q)$ = *global* min over θ of the raw distance function ρ (ch. 2); each $d_i(q)$ = a *local* min of ρ(q,θ) in θ (a sonar whose reading is less than both neighbours faces the closest point on its obstacle, i.e. points along $-\nabla d_i$). Occlusion can corrupt $d_i$.
- §4.3.2 **Brushfire** (distance transform on a grid): pixels 0 = free, 1 = obstacle. Label free pixels adjacent to obstacles 2; free pixels adjacent to a 2 get 3; …; every free pixel adjacent to value i gets i+1, until all are labelled (fig 4.9). Output = (approx.) distance to nearest obstacle; **gradient at a pixel = vector toward the neighbour with the *lowest* value** (that is $-\nabla D$ direction, i.e. toward the obstacle; repulsion goes the other way) — 4 or 8 possible directions, ties broken arbitrarily. **Four-point connectivity ↔ Manhattan ($L_1$) metric**; eight-point includes diagonals ($L_\infty$/chessboard). 3-D: 6- vs 26-connectivity. The lines where fronts collide (fig 4.9) are the discrete **GVD** (ch. 5). Intractable in high dimension.
- Pseudo-code (Brushfire, as BFS):
  ```
  queue ← all free cells 4/8-adjacent to an obstacle; label them 2
  while queue not empty: c ← pop; for each free unlabeled neighbour n: label(n) = label(c)+1; push n
  ```

### 4.4 Local minima problem (PDF 106–107, pp. 89–90)
- Gradient descent converges (for suitable α) to *a* minimum, not necessarily the goal ⇒ attractive/repulsive is **not complete**. Examples: horseshoe/concave obstacle (fig 4.10, repulsion from the base cancels attraction), and **even two convex obstacles** side by side (fig 4.11).
- **Randomized Path Planner (RPP)** (Barraquand & Latombe): follow $-\nabla U$; when stuck, do random walks to escape, then resume descent.

### 4.5 Wave-front planner (PDF 107–109, pp. 90–92)
- Grid of 0/1; label **goal = 2**; free neighbours of 2 get 3, neighbours of 3 get 4, … (BFS wave from the goal) until the wave reaches the **start**. Then from the start, repeatedly step to any neighbour whose value is one less, until reaching 2 (gradient descent on the grid). Boundedness guarantees there is always such a neighbour.
- Properties: potential with a **single local minimum** (the goal) ⇒ **resolution complete**; gives the **shortest path in the grid metric** (Manhattan for 4-connectivity) — but hugs obstacles ("dangerously close"); must search (essentially) the whole space; exponential in dimension (voxels: 6- or 26-connectivity).
- Where the wave collides with itself (fig 4.12) = a saddle of distance-to-goal; the trace of that collision = points with two equally short routes (around above vs below the obstacle).
- Relationship: Brushfire = BFS from *obstacles* (distance to obstacles → repulsion/GVD); Wavefront = BFS from the *goal* (distance to goal → navigation). Wavefront ≡ Dijkstra/BFS with unit costs (App H).

### 4.6 Navigation potential functions (PDF 110–116, pp. 93–99)
- **Def 4.6.1** $\varphi:Q_{free}\to[0,1]$ is a **navigation function** if it is (1) smooth (at least $C^k$, k ≥ 2), (2) has a **unique minimum at $q_{goal}$** in the goal's connected component, (3) is **uniformly maximal on the boundary** of free space, (4) is **Morse** (all critical points non-degenerate ⇒ isolated; saddles/maxima destabilise under perturbation).
- Obstacles as sublevel sets: $QO_i=\{q\mid\beta_i(q)\le0\}$ (β < 0 inside, 0 on boundary, > 0 outside).
- **Sphere-space** (§4.6.1): world = ball of radius $r_0$ at $q_0$ containing disjoint ball obstacles $(q_i,r_i)$: $\beta_0(q)=-d^2(q,q_0)+r_0^2$, $\beta_i(q)=d^2(q,q_i)-r_i^2$. Eq (4.8) $\beta(q)=\prod_{i=0}^n\beta_i(q)$ (zero on any boundary, positive in free space). Eq (4.9) $\gamma_\kappa(q)=d(q,q_{goal})^{2\kappa}$. $\gamma_\kappa/\beta$ has a unique min for large κ (steep bowl; other critical points pushed onto obstacles and become saddles) but is unbounded. Eq (4.10) **analytic switch** $\sigma_\lambda(x)=\frac{x}{\lambda+x}$; Eq (4.11) $s(q,\lambda)=\frac{\gamma_\kappa}{\lambda\beta+\gamma_\kappa}$ ∈ [0,1]. Eq (4.12) sharpening $\xi_\kappa(x)=x^{1/\kappa}$. Eq (4.13), λ = 1: 
  $$\varphi(q)=\left(\xi_\kappa\circ\sigma_1\circ\frac{\gamma_\kappa}{\beta}\right)(q)=\frac{d^2(q,q_{goal})}{\left[d(q,q_{goal})^{2\kappa}+\beta(q)\right]^{1/\kappa}}$$
  — unique minimum at $q_{goal}$ for sufficiently large κ. Fig 4.15: five obstacles; κ = 3 has 3 local minima; by κ = 10 the bad minima have turned into saddles. **Drawback:** large κ makes φ flat near the goal and far away with sharp transitions in between ⇒ numerically nasty for gradient descent.
- **Star-space** (§4.6.2): star-shaped set: ∃x with line of sight to every y ∈ S ($tx+(1-t)y\in S\ \forall t$); convex ⇒ star-shaped, not conversely. **Key theorem:** if φ is a navigation function on M and $h:F\to M$ is a diffeomorphism, then $\varphi\circ h$ is a navigation function on F (critical points correspond 1-1). Construction: translated scaling map Eq (4.14) $T_i(q)=\nu_i(q)(q-q_i)+p_i$, Eq (4.15) $\nu_i(q)=(1+\beta_i(q))^{1/2}\frac{r_i}{d(q,q_i)}$ (maps star boundary to sphere of radius $r_i$ at $p_i$); switches Eq (4.16) $s_i(q,\lambda)=\frac{\gamma_\kappa\bar\beta_i}{\gamma_\kappa\bar\beta_i+\lambda\beta_i}$ with Eq (4.17) $\bar\beta_i=\prod_{j\ne i}\beta_j$; Eq (4.18) $s_{q_{goal}}=1-\sum_i s_i$; Eq (4.19) $h_\lambda(q)=s_{q_{goal}}(q,\lambda)\,q+\sum_{i=0}^M s_i(q,\lambda)T_i(q)$ — a diffeomorphism for suitable λ.

### 4.7 Potential functions in non-Euclidean spaces (PDF 116–121, pp. 99–104) — R5 / A1 link
- Define potentials in the (Euclidean) **workspace**, then lift to Q. Now gradients are treated as **forces**.
- **Virtual work / power invariance**, Eq (4.20–4.21): $f^T\dot x=u^T\dot q$, $\dot x=J\dot q$ ⇒ $f^TJ=u^T$ ⇒ $$u=J^T(q)f.$$
- **Example 4.7.1 (force on polygon vertex a=(a_x,a_y)):** $\phi(q)=\begin{bmatrix}x+a_x\cos\theta-a_y\sin\theta\\y+a_x\sin\theta+a_y\cos\theta\end{bmatrix}$, $J=\begin{bmatrix}1&0&-a_x\sin\theta-a_y\cos\theta\\0&1&a_x\cos\theta-a_y\sin\theta\end{bmatrix}$, Eq (4.22–4.23) $u=[f_x,\ f_y,\ -f_x(a_x s_\theta+a_y c_\theta)+f_y(a_x c_\theta-a_y s_\theta)]^T$; $u_\theta$ = torque $r\times f$ about the robot origin.
- **Control points** (§4.7.2): enough points to pin the body (2 for a planar rigid body). Attractive per point $U_{att,j}$ = combined quadratic/conic on $d(r_j(q),r_j(q_{goal}))$ with gain $\zeta_j$; repulsive Eq (4.24)/(4.25) same form as (4.4)/(4.5) with $d_i(r_j(q))$, $\eta_j$, $Q^*_i$. **Floating repulsive control point** $r_{float}$ = point on the robot boundary closest to any obstacle (vertices alone can let an edge hit a sharp obstacle, fig 4.20). Eq (4.26): $u(q)=\sum_j J_j^T(q)\nabla U_{att,j}(q)+\sum_i\sum_j J_j^T(q)\nabla U_{rep_{i,j}}(q)$ — **add forces in C-space, never in the workspace** (each control point has its own Jacobian). Then descend $q\leftarrow q-\alpha u$.
- Design knobs: unequal $\zeta_j$ → "follow the leader"; distinct $Q^*_i$ so no obstacle's influence region contains a control point's goal and to avoid overlapping influence regions.
- §4.7.3 **Articulated arms:** attractive points on the end-effector, repulsive (incl. one floating point per link) on links; $J_j$ per control point maps workspace forces to joint torques/forces. Still plagued by local minima.

**Worked examples worth redoing:** fig 4.9 Brushfire by hand on a small grid (8-connectivity); fig 4.12 wavefront (4-connectivity) and read off the shortest path; Example 4.7.1 (J^T of a polygon vertex — the same as Example 3.8.2); implement (4.13) for fig 4.14 and sweep κ = 3…10 to watch minima turn into saddles.

**Exercises worth doing (pp. 105–106):** 1 ($d_i$ is a local min of ρ; D from $d_i$); **2 (is wavefront shortest? — yes, in the grid's $L_1$/$L_\infty$ metric, not Euclidean)**; **3 (wavefront program from polygon file → binary grid; the ASBR grid-planning assignment pattern)**; 4 (wavefront on $(x,y,\theta)$ for a rotating convex robot — combines with ch. 3 ex. 18); **5 (wavefront on the 2R torus — remember wrap-around)**; 6–7 (implement attractive/repulsive for points among polygons/circles/polyhedra); 8 (moving obstacles); 9 (why Bug2 and navigation-function paths look similar).

**Conventions / pitfalls:**
- Algorithm 4 sign typo (+ → −). Also p. 102: the conic branch of $U_{att,j}$ is printed $-\tfrac12\zeta_j d^*_{goal}$; it should be $-\tfrac12\zeta_j (d^*_{goal})^2$ to match (4.2), and the quadratic branch's gain is printed $\zeta_i$ for $\zeta_j$.
- Choset's gradient convention: $\nabla U$ points uphill; "force" in §4.7 is $\nabla U$ and the descent direction is $-u$. Don't double-negate.
- Repulsive gradient formula (4.5) uses $\nabla D$ (unit, away from obstacle) and has a negative scalar prefactor inside $Q^*$.
- Attractive/repulsive is **not complete**; wavefront is **resolution complete**; navigation functions are complete but only for sphere/star worlds known in advance.
- Gradients on grids (Brushfire/wavefront) are only 4/8-direction approximations.
- Summing forces in the workspace (instead of $\sum J_j^Tf_j$) is wrong because the points have different Jacobians.

---
## Ch. 5 Roadmaps (PDF pp. 124–176, book pp. 107–159) — Lessons: A3 (roadmaps + graph search), A4 (PRM is a sampled roadmap), F5 (preimage theorem, Lagrange multipliers)
**Big idea:** If you will plan many queries in one environment, precompute a 1-D network (a **roadmap**) embedded in $Q_{free}$ that captures its topology, like a highway system: get on, drive, get off. Exact roadmaps: the **visibility graph** (shortest paths in polygonal 2-D, but hugs obstacles), the **GVD/GVG** (max-clearance, sensor-constructible), and **Canny's silhouette roadmap / OPP** (general but complex). Ch. 7's PRM is the randomized descendant of this idea.

**Map types (p. 107–108):** topological (graph of distinct places + adjacency), geometric (line segments, meshes), occupancy grids (cell = P(occupied), ch. 9).

**Def 5.0.2 Roadmap (p. 109):** a union of 1-D curves RM such that for all $q_{start},q_{goal}$ connectable in $Q_{free}$: (1) **accessibility** (path $q_{start}\to q'_{start}\in RM$), (2) **departability** ($q'_{goal}\in RM\to q_{goal}$), (3) **connectivity** (path in RM between $q'_{start}$ and $q'_{goal}$). Five families: visibility maps, deformation retracts, retract-like structures, piecewise retracts, silhouettes.

### 5.1 Visibility graph (PDF 127–134, pp. 110–117)
- Nodes: start, goal, all C-obstacle vertices (polygonal 2-D Q). Edge $e_{ij}$ iff $sv_i+(1-s)v_j\in cl(Q_{free})\ \forall s\in[0,1]$ (semi-free: obstacle edges are graph edges). Accessible/departable by definition; connectivity is Problem 1.
- **Contains the Euclidean shortest path in the plane** (search with $L_2$ edge costs, e.g. A*/Dijkstra, App H); **not** in 3-D polyhedral worlds (shortest paths bend on obstacle edges — Problems 2–3).
- **Reduced visibility graph**: keep only edges on **supporting lines** (tangent to two obstacles, both on the same side) or **separating lines** (tangent, obstacles on opposite sides) (figs 5.5–5.7). Works for non-convex obstacles via *local convexity*: $(c-\bar c)\cdot N$ has constant sign in a neighbourhood. Extends to curved obstacles.
- Construction: naive $O(n^3)$ (n vertices: each of $O(n^2)$ segments tested against $O(n)$ edges). **Rotational plane sweep (Algorithm 5, p. 114)**: for each v, sort other vertices by angle $\alpha_i$; maintain active list S of edges hit by the half-line from v, sorted by distance (balanced tree); sweep $0\to2\pi$; $v_i$ visible iff segment $vv_i$ doesn't cross the closest edge in S and the ray doesn't go into v's own obstacle; insert/delete edges at their begin/end vertices. $O(n\log n)$ per vertex ⇒ **$O(n^2\log n)$** total. Worked trace: fig 5.8 + Table 5.1 (adds $(v,v_4),(v,v_8),(v,v_1)$). General position (no three collinear) assumed; perturb otherwise.

### 5.2 Deformation retracts: the GVD (PDF 134–146, pp. 117–129)
- Voronoi diagram of point sites → **generalized Voronoi region** (5.1) $F_i=\{q\in Q_{free}\mid d_i(q)\le d_h(q)\ \forall h\ne i\}$, $d_i$ from (4.6). Two-equidistant surface $S_{ij}=\{q : d_i(q)-d_j(q)=0\}$; **surjective** version $SS_{ij}=\{q\in S_{ij}\mid\nabla d_i(q)\ne\nabla d_j(q)\}$ (removes the spurious part produced by arbitrary convex decomposition of a non-convex obstacle, fig 5.10, and matches what a range sensor sees as two local minima); face $F_{ij}=\{q\in SS_{ij}\mid d_i\le d_h\ \forall h\}$; $GVD=\bigcup_i\bigcup_j F_{ij}$. Planar GVD edges end at **meet points** (equidistant to ≥ 3 obstacles) or **boundary points** (distance 0, ends of "spokes").
- **Accessibility** (5.2): gradient *ascent* of distance, $\dot c=\nabla D(c)$, $c(0)=q_{start}$, until two-way equidistant (Lemma 5.2.1). **Departability**: reverse, or drive straight to a goal in line of sight (every free point sees some GVD point). **Connectivity**: from the deformation retraction.
- **Retraction** $f:X\to A\subset X$ continuous with $f(a)=a$. **Homotopy** $H:U\times[0,1]\to V$, $H(x,0)=f(x)$, $H(x,1)=g(x)$ (e.g. $(1-t)f+tg$); path-homotopy classes $[c]$. **Deformation retraction**: homotopy with $H(x,0)=x$, $H(x,1)\in A$, $H(a,t)=a$. Preserves the **fundamental group** $\pi_1$ (number of homotopy classes of loops) ⇒ the GVD has as many "loop types" as $Q_{free}$; 1-D deformation retracts are roadmaps.
- **Dimension via the Preimage Theorem (Thm 5.2.2):** if n is a regular value of smooth $G:M\to N$ (DG surjective on $G^{-1}(n)$), then $G^{-1}(n)$ is a closed submanifold with $T_mG^{-1}(n)=\ker DG(m)$ and $\dim=\dim M-\dim N$. With $G=d_i-d_j$, $DG=(\nabla d_i-\nabla d_j)^T\ne0$ exactly by the "surjective" condition ⇒ GVD edges are 1-D in the plane (and (m−1)-D in $\mathbb{R}^m$). Example: $f=x^2+y^2$, any c > 0 is regular ⇒ circle.
- **Construction (§5.2.5):** (a) *sensor-based*: access, then trace with ch. 2's predictor–corrector on $G=d_i-d_j$; tangent = null space of $\nabla G$ = perpendicular to the line joining the two closest points (fig 5.13); meet point = sudden change of a closest obstacle; explore the graph (mark edges, backtrack at boundary points, stop when no meet point has unexplored edges). (b) *polygonal*: vertex–vertex and edge–edge bisectors are lines, vertex–edge is a **parabola**; with n obstacles/N vertices, # GVD edges ∈ $[3(n+1)/2,\ 6N+3n-3]$ and # nodes ∈ $[(n+5)/2,\ 4N-n-2]$ (p. 128, citing [359]). (c) **grid/Brushfire**: propagate from obstacles with back-pointers to the source obstacle; a pixel reached by two fronts with different back-pointers is a GVD pixel.

### 5.3 GVG and HGVG (PDF 146–155, pp. 129–138) — brief
- In $\mathbb{R}^3$ the GVD is 2-D (sheets). **GVG** = points equidistant to 3 obstacles (in $\mathbb{R}^m$: m obstacles), 1-D; $G=[d_i-d_j,\ d_i-d_k]^T:\mathbb{R}^3\to\mathbb{R}^2$, full rank by a **transversality** assumption ($T_xM_1+T_xM_2=T_xM$; codimensions add). Table 5.2: GVD dim m−1 (codim 1, 2-way equidistant); GVG dim 1 (m-way). In the plane they coincide.
- The GVG is generally **not connected** in ≥ 3-D (no 1-D deformation retract of a punctured 3-space; fig 5.19 box-in-room "halo"). Fix: **second-order GVG edges** (5.4) on GVD sheets ($d_i=d_j\le d_k=d_l$), union = **HGVG** (retract-like); link a detected **period** to an inner GVG cycle by following $\dot c=-\pi_{T F_{ij}}\nabla d_k$.
- **Sensor-based tracing control law** (5.5): $\dot q=\alpha v+\beta\,(DG(q))^\dagger G(q)$, $v\in\text{Null}(DG)$, $(DG)^\dagger=DG^T(DG\,DG^T)^{-1}$ (right pseudoinverse); **Lyapunov function** $\Lambda=\tfrac12G^TG$, $\dot\Lambda=\beta G^TG<0$ for **β < 0** — note: the same "tangent + pseudo-inverse correction" structure as task-priority / resolved-rate control (A1/R6). Meet-point homing: null space empty, move to centre of circle (sphere) through the 3 (4) closest points.

### 5.4 Rod-HGVG (PDF 155–157, pp. 138–141) — brief
- Rod (line segment) in the plane, $Q=SE(2)$; distances measured in the workspace from the rod's nearest point. Rod-GVG edges ($RF_{ijk}$, 3-way equidistant configurations) + **R-edges** (rod tangent to the planar point-GVG) = **rod-HGVG**, a **piecewise retract** (retracts of junction regions $J_{ijk}$ stitched together). Workspace-sensed roadmap of a non-Euclidean C-space.

### 5.5 Silhouette methods (PDF 158–171, pp. 141–154) — brief
- **Canny's roadmap** (first general complete algorithm; complexity $p^n(\log p)w^{O(n^4)}$, p polynomials of degree ≤ w, n DOF — exponential in n, establishing the theory): sweep a slice $Q_\lambda=\{q:\pi_1(q)=\lambda\}$ in $q_1$; silhouette = trace of extrema of $\pi_2$ on each slice; at **critical slices** (where silhouette count changes; slice tangent to silhouette) recurse in one lower dimension; add slices through start and goal. Tools: **Lagrange multiplier** (Lemma 5.5.1: at an extremum of h on $f^{-1}(c)$, $\nabla h=\mu\nabla f$); generalized version (Lemma 5.5.2: critical iff $\begin{bmatrix}Df\\Dh\end{bmatrix}$ loses rank); slice lemma; **Morse theory** — topology of slices is constant between adjacent critical values.
- **Opportunistic Path Planner (OPP)** (Canny & Lin): trace **local maxima of distance D restricted to slices** ("freeways"), access/depart by slice-constrained gradient ascent, connect with **bridge curves** at interesting critical points (slice tangent to an obstacle: $\nabla h\parallel\nabla f$) and bifurcation points. D is non-smooth at equidistant points: **generalized gradient** $\partial D(q^*)=\text{Co}\{\nabla d_i(q^*) : i\in Z(q^*)\}$ (convex hull of gradients of the closest obstacles); $0\in\partial D$ ⇒ local max. OPP ⊂ GVG (Problem 12).

**Worked examples worth redoing:** fig 5.8 / Table 5.1 rotational sweep by hand; Lagrange-multiplier extrema of $\pi_1$ on a sphere (p. 145); generalized-Lagrange silhouette of a sphere = equator (p. 147).

**Exercises worth doing (pp. 155–159):** 1 (visibility graph connected); **2–3 (why VG is not shortest in 3-D)**; **5 (implement VG + search — classic ASBR/RBE planning assignment)**; 6 (extrema of a quadratic form on the unit circle → eigenvectors! F4 link); 11 (roadmaps vs pixel maps tradeoffs — exam); **14 (planar GVD via Brushfire)**; 15–16 (sketch GVD with circle vs L1-square analogy); 17 (pros/cons of potential functions); 18 (preimage theorem on $x^2+y^2-z^2=c$: manifold for c ≠ 0; c=0 cone not a manifold at origin); 19 ($d_i$ convex for convex $QO_i$).

**Conventions / pitfalls:** VG = shortest but zero clearance; GVD = maximum clearance but longer; both are 2-D-specific in their nice forms. "Surjective" in $SS_{ij}$ refers to the differential being onto (regular value), not to a set map. Access to GVD is gradient **ascent** of D (contrast with descent on U in ch. 4). Brushfire collision fronts = GVD.

---
## Ch. 6 Cell Decompositions (PDF pp. 177–205 read, book pp. 161–189; chapter continues to PDF 212 / p. 196 incl. problems p. 195 — in part 2) — Lessons: A3 (cell decomposition + graph search), coverage
**Big idea:** Chop $Q_{free}$ into simple cells (convex trapezoids, or cells where a sweeping slice keeps constant topology), build the **adjacency graph** (cells = nodes, shared boundary = edge), search it, then stitch a geometric path through cell boundaries. Unlike roadmaps, cells give **coverage** for free: cover each simple cell with back-and-forth laps and visit all cells with an exhaustive walk.

**Key definitions:** exact cell decomposition; adjacent cells share a boundary; adjacency graph; coverage path planner (passes the effector over every free point); **critical point** (where the free part of the slice, $Q_{free_\lambda}=Q_\lambda\cap Q_{free}$, changes connectivity); slice intervals; **Def 6.2.1 Morse decomposition**: cells = connected components of $Q_{free}\setminus I^*$ (remove the critical slice intervals); **Reeb graph** (dual: nodes = critical points, edges = cells).

### 6.1 Trapezoidal decomposition (PDF 178–183, pp. 162–167)
- Polygonal Q, general position (distinct vertex x-coords). At each vertex draw **upper and lower vertical extensions** until they hit the polygon edge directly above/below. Cells = trapezoids (triangles are degenerate trapezoids); convex ⇒ any two boundary points are joined by a free segment.
- Path: locate start/goal cells → search adjacency graph → connect **midpoints of vertical extensions ↔ cell centroids** → join start/goal to the midpoints (fig 6.3).
- Construction by **sweep line** stopping at vertices ("events"): sort vertices $O(n\log n)$; maintain active edge list L in a balanced tree; find $e_{LOWER},e_{UPPER}$ bracketing v in $O(\log n)$. Four event types by whether the two edges incident on v ($e_{lower},e_{upper}$) lie left/right of the sweep line: both left → delete both; both right → insert both; lower left/upper right → replace lower by upper; lower right/upper left → replace upper by lower. Total **$O(n\log n)$** (naive $O(n^2)$). Point location via "slabs": two $O(\log n)$ searches.

### 6.2 Morse cell decompositions (PDF 184–203, pp. 168–187)
- **Boustrophedon** ("ox-turning") decomposition: only keep extensions at vertices where the vertical line can extend **both** up and down (critical points ↔ Canny's critical points). Fewer, larger cells ⇒ shorter coverage path than trapezoidal (fig 6.9). Coverage: exhaustive walk of the adjacency graph by **DFS** (e.g. 1–2–4–2–3–5–6–5–7–5–8–1, fig 6.10), then laps one robot-width apart within each cell.
- Generalisation: slice = level set of $h:Q\to\mathbb{R}$. Boustrophedon $h=x$; **spiral** $h=x^2+y^2$ (annular cells; spiral pattern); **spike** $h=\tan^{-1}(y/x)$ (pie slices; denser near centre — good for detectors with false negatives); **squarel** $h=|x|+|y|$ (diamonds; needs the generalized gradient); **brushfire** $h=D(q)$ (cells born at saddle points where obstacle wave-fronts first collide; coverage by following obstacle boundaries at multiples of robot width ⇒ robust to dead-reckoning error but needs long-range sensing); **wave-front** h = shortest path length from a point (continuous version of §4.5; cusps where fronts collide; good for tethered robots).
- **Sensor-based coverage (§6.2.4):** incrementally build the Reeb graph while covering. **Detect a critical point** where the obstacle surface normal is parallel to the sweep direction ($\nabla h\parallel\nabla m$, Lemma 5.5.2) — seen as the global min of the range ring aligned with the sweep. Plain raster scanning misses "closing" critical points on the ceiling; the **cycle algorithm** (forward phase: lap + forward boundary-follow one lap width; reverse phase: reverse laps + reverse boundary following; closing phase: return to $S_i$'s slice) guarantees finding the closing critical point. Complete when every critical point has no unexplored edges.
- **Complexity (§6.2.5):** Euler's formula $v-e+f=2$ on the planar Reeb graph ⇒ **$N_{ce}=N_{cp}+N_{ob}-1$** (fig 6.25: 21 critical points, 2 obstacles ⇒ 22 cells). With Δ = diameter of the smallest enclosing disk, 2r = inter-lap spacing, $P_{total}$ = total perimeter: lapping ≤ $\Delta\lceil\Delta/2r\rceil+\Delta N_{ce}$; boundary following ≤ $2P_{total}$; backtracking ≤ $P_{total}+\Delta N_{ce}$; extra discovery ≤ $P_{total}$; DFS transit ≤ $P_{total}+2N_{ce}\Delta$; total **$<\frac{\Delta^2}{2r}+4\Delta N_{ce}+5P_{total}=\frac{\Delta^2}{2r}+4\Delta(N_{cp}+N_{ob})+5P_{total}-4\Delta$** — linear in area, number of critical points and perimeter.

### 6.3 Visibility-based decompositions for pursuit/evasion (PDF 203–205 read, pp. 187–189; rest in part 2)
- Pursuers $\gamma_i$, evader e (points in $W_{free}$); capture when $e(t)\in V(\gamma_i(t))$, the (star-shaped) **visibility polygon**. Regions are **contaminated / clear / recontaminated**; visibility polygon edges in free space are **gap-edges** labelled 0 (bounds cleared) or 1 (contaminated); $B(x)$ = label vector; information state $(x,B(x))$. **Conservative region**: connected set where B(x) is constant — cells built by extending rays from convex vertices, and from mutually visible vertex pairs outward (bitangent extensions). Planning must happen in the *information space* decomposition (transitions: gap disappears → nothing; gap appears → label 0; …). $H(W_{free})$ = minimum number of pursuers.

**Worked examples worth redoing:** figs 6.5–6.8 sweep-line list updates for trapezoidal decomposition; fig 6.10 DFS exhaustive walk; fig 6.25 Euler-count check.

**Exercises worth doing:** ch. 6 problems are on book p. 195 (PDF 211) — outside this part. Self-set: implement trapezoidal decomposition + adjacency graph + BFS/A* for a polygon file (pairs with ch. 5 Problem 5 and ch. 4 Problem 3 for a 3-way planner comparison: VG vs trapezoid vs wavefront).

**Conventions / pitfalls:** trapezoidal paths via midpoints/centroids are feasible, not optimal. Boustrophedon critical points need the extension to go *both* ways. In a Morse decomposition the slice $Q_\lambda$ itself never changes connectivity — $Q_{free_\lambda}$ does. Brushfire *decomposition* cells ≠ GVD Voronoi regions.

---
## Corrections & upgrades for the knowledge base
Checked against Notion ASBR page (fetched 2026-11-26) and `courses/05-algorithms-sensor-based-robotics/README.md`.

- **[A3] "The C-space has one dimension per joint"** → Choset defines dim Q = DOF = *minimum number of parameters* to place every point of the robot (§3.1, p. 40; §3.3 pp. 47–50). "One per joint" holds only for serial open chains; a planar mobile robot has $SE(2)$ (3-D), a spacecraft $SE(3)$, closed chains follow Grübler (3.1) $M=N(k-n-1)+\sum f_i$. → Rewrite: "one dimension per independent configuration parameter (per joint for a serial arm; 3 for a planar rigid body; Grübler for closed chains)". Also add that Q is often non-Euclidean ($T^2$ for the 2R arm, wrap-around in the grid!).
- **[A3] "foundation of motion planning (weeks 4–5)"** → the course readings put **Choset ch. 3 (C-space) in week 2**, ch. 4 (potentials) in week 4, ch. 5–6 (roadmaps, cell decompositions) in week 5. → Split the week tag: C-space = week 2, potentials = week 4, graph search/roadmaps/cells = week 5.
- **[A3] "Learn from: Choset ch. 2–4"** → ch. 2 (Bug) is not an assigned ASBR reading, while ch. 5–6 (assigned, week 5) and **App H (A\*, Dijkstra, D\*: book pp. 521–546 = PDF 535–560)** are missing, and Minkowski sums live in **App F.3 (p. 507, PDF 521)**, not ch. 3. → Replace with: "Choset §3.1–3.4 & 3.7 (C-space, topology), App F.3 (Minkowski/star algorithm), ch. 4 (all; esp. eqs. 4.1–4.7, Alg 4, §4.3.2 Brushfire, §4.5 wavefront, §4.6 navigation fns, §4.7 $u=J^Tf$), ch. 5.1–5.2 (visibility graph, GVD), ch. 6.1–6.2.1 (trapezoidal, boustrophedon), App H (graph search). Ch. 2 optional."
- **[A3] potential formulas** → $U_{att}$ and $U_{rep}$ shown match Choset (4.1) and (4.4) *up to notation*, but: (a) Choset uses $D(q)$/$d_i(q)$ for distance and $Q^*$ for the influence radius — **in Choset ρ is the raw range-sensor function (2.3)**, so writing ρ(q) for obstacle distance clashes with the textbook (ρ₀ is Khatib/Latombe notation); (b) add the gradients $\nabla U_{att}=\zeta(q-q_{goal})$ and $\nabla U_{rep}=\eta(\frac1{Q^*}-\frac1{D})\frac1{D^2}\nabla D$ (4.5); (c) add the combined quadratic/conic attractive (4.2–4.3) that bounds far-field speed (book recommends it); (d) add the per-obstacle $d_i$ version (4.6–4.7) that removes oscillation at equidistant points.
- **[A3] gradient descent** → Choset Algorithm 4 (p. 85, PDF 102) prints $q(i+1)=q(i)+\alpha(i)\nabla U$ — a **sign typo**; correct is $-\alpha\nabla U$ (as our page says "follow $-\nabla U$" ✓). Add a pitfall note so the student doesn't copy the book's line. Stop on $\|\nabla U\|<\epsilon$.
- **[A3] local-minimum fixes** → page lists only "trapped by local minima". Add the book's three remedies: wavefront planner (resolution complete, single minimum; §4.5), navigation functions (Def 4.6.1, eq. 4.13), randomized escape (RPP, §4.4). Note local minima occur even with two *convex* obstacles (fig 4.11), not just U-shapes.
- **[A3] A\* claim** "optimal when the heuristic never overestimates (admissible)" → true for tree search / A\* with re-opening; for graph search with a closed list, optimality without re-expansion needs a **consistent** (monotone) heuristic. Add "consistent ⇒ admissible; octile/Manhattan on 8/4-grids are consistent". (Check against Choset App H.2.2, PDF 545, in part 3.)
- **[A3] missing link to A1** → Choset §4.7 (PDF 116–121) lifts workspace forces to C-space with $u=J^T(q)f$ (virtual work, eq. 4.21) and sums over control points (4.26) — same Jacobian as A1's resolved-rate control. Add one line + Example 4.7.1 to A3's lecture.
- **[A3] Build** → good (matches Choset Prob. 3.19). Upgrade: run the **wavefront** on the 2-link torus with wrap-around neighbours (Choset Prob. 4.5) and compare 4- vs 8-connectivity path lengths (wavefront is shortest only in the grid metric, Prob. 4.2).
- **[A1] Learn from** → add Choset §3.5.1 (PDF 78–84: SO(n)/SE(n), frame vs transform, subscript cancelling, body-frame = post-multiply vs world-frame = pre-multiply, worked fig 3.17–3.19), §3.6 + App E (Euler/RPY/axis-angle/quaternions, PDF 503–512) and §3.8 (Jacobian, 2R singularity at $\sin\theta_2=0$). Choset has no twists/adjoint/body Jacobian, so MR ch. 3–5 (and MLS ch. 2–3) stay the primary A1 references. Formula $\dot\theta=J^{-1}V$ / DLS is consistent with the book's $\dot x=J\dot q$ (no error found).
- **[courses/05 README, line 110] "Configuration space, Minkowski sums → PoRM Ch. 3"** → ch. 3 only shows obstacle "growing" (figs 3.4–3.5); the Minkowski difference (F.7) and star algorithm are in **App F.3–F.4** (PDF 521–523). Point there.
- **[F6] / [A3]** → wherever gradient descent from Choset is cited, flag the Algorithm 4 sign typo; also note p. 102 typo in $U_{att,j}$ (missing square on $d^*_{goal}$).
- **[F5]** → Choset App C.5 (PDF 497–500) and §2.3.3 / §5.2.4 give the "tangent space = null space of the Jacobian" and preimage-theorem view; good optional reading for implicit curves.

## Best-reference map
- **[A3] C-space definitions, DOF, holonomic constraints, Grübler** → §3.1–3.3, PDF 57–68 (book 39–50).
- **[A3] C-obstacles / Minkowski** → §3.2 (PDF 61–65) + App F.3–F.4 star algorithm & SE(2) slices (PDF 521–523); exercises 3.4, 3.5, 3.18.
- **[A3] 2R-arm C-space on the torus, grid C-space, wrap-around** → §3.1 fig 3.2 (PDF 58–60), §3.2.2 figs 3.6–3.8 (PDF 63–65); exercises 3.12, 3.19.
- **[A3] Topology (manifolds, charts, atlas, connectedness, compactness)** → §3.4 (PDF 68–77).
- **[A1]/[F3]/[R1–R2] SO(n), SE(n), frames vs transforms, body vs world composition** → §3.5.1 (PDF 78–84); Euler Z-Y-Z §3.6 (PDF 84–85); App E (PDF 503–512); exercises 3.16, 3.17, 3.23.
- **[R5]/[A1]/[F5] Jacobian, singularity, $\dot x=J\dot q$** → §3.8 Examples 3.8.1–3.8.2 (PDF 87–89); exercises 3.20, 3.21, 3.22.
- **[A3]/[F6] Attractive/repulsive potentials + gradient descent** → §4.1–4.2, eqs. 4.1–4.7, Alg 4 (PDF 97–102).
- **[A3] Brushfire distance transform** → §4.3.2 fig 4.9 (PDF 103–106); GVD via Brushfire §5.2.5 (PDF 145–146).
- **[A3] Local minima, RPP** → §4.4 (PDF 106–107).
- **[A3] Wavefront planner** → §4.5 fig 4.12 (PDF 107–109); continuous version §6.2.3 (PDF 194–195); exercises 4.2, 4.3, 4.5.
- **[A3] Navigation functions (sphere/star worlds)** → §4.6 eqs. 4.8–4.19 (PDF 110–116).
- **[A3]/[R5] Workspace→C-space forces $u=J^Tf$, control points** → §4.7 eqs. 4.20–4.26 (PDF 116–121).
- **[A3] Visibility graph + rotational plane sweep** → §5.1 Alg 5, Table 5.1 (PDF 127–134); exercise 5.5.
- **[A3] GVD / deformation retracts / preimage theorem** → §5.2 (PDF 134–146).
- **[A3] Canny roadmap / OPP (conceptual only)** → §5.5 (PDF 158–171).
- **[A3] Trapezoidal & boustrophedon decompositions, coverage** → §6.1–6.2 (PDF 177–203).
- **[A3] Graph search (A\*, Dijkstra, D\*)** → App H (PDF 535–560) — part 3 reader.
- **[A4] Sampling-based (PRM/EST/RRT)** → ch. 7 (PDF 213–283) — part 2 reader.
- **[A5] Kalman/EKF** → ch. 8 (PDF 284–314) + App I, J (PDF 561–577). **[A6] Bayes/particle filter, MCL, occupancy grids** → ch. 9 (PDF 316–362).
- **[A3] Sensor-based planning (Bug1/Bug2/Tangent Bug)** → ch. 2 (PDF 35–56), optional for ASBR.
