# LaValle, *Planning Algorithms* (Cambridge University Press, 2006) — Part 2: Parts III–IV, Ch. 8–15
Source: `PlanningAlgorithmsBook.pdf` (the student's copy, printed **2-up**), PDF pages 193–470 (book pages 369–924). Ch. 1–7 are in `lavalle-part1.md`.

> **Page mapping (checked against the running header of every even book page in the file; constant throughout).** Each PDF page holds two book pages: PDF page $n$ = book pages $2n-17$ (left, odd) and $2n-16$ (right, even). Book page $p$ → PDF $\lfloor (p+17)/2\rfloor$. Every citation below reads "(book p. X, PDF Y)".
>
> **Chapter starts (PDF / book):** Ch. 8 → 193 / 369 · Part III intro → 225 / 433 · Ch. 9 → 227 / 437 · Ch. 10 → 256 / 495 · Ch. 11 → 288 / 559 · Ch. 12 → 325 / 633 · Part IV intro → 364 / 711 · Ch. 13 → 366 / 715 · Ch. 14 → 402 / 787 · Ch. 15 → 439 / 861 · Bibliography → 471 / 925.
>
> **Status for JHU ASBR (EN.601.663):** LaValle is an official "other resource". The course readings cite only Ch. 5 (sampling planners). Ch. 8–15 are not assigned, but they are the most general treatment in the library of three ASBR themes: feedback plans and navigation functions (A3–A4), filtering as an information-space update (A6–A7), and planning for cars and dynamical systems (A5). Depth here: thorough for Ch. 8, 11, 12, 13 and 14; solid for Ch. 10 and 15; brief for Ch. 9 and all the game theory (§9.3–9.5, §10.5, §11.7, §13.5).
>
> **Read this first, notation:** in Ch. 9–12, $\theta$ is **nature's action** (process noise / hidden parameter), not a heading or joint angle. $\psi$ is nature's *sensing* action (measurement noise), $y$ is the observation (Thrun's $z$), and $\eta$ is the information state (Thrun's $\eta$ is the Bayes normalizer). LaValle **minimizes cost** $G^*$ everywhere; Thrun's MDP chapter and RL texts maximize reward. The full translation table is in the Notation section near the end.
>
> **How the equations were checked:** garbled equations were read off rendered page images. An entry is listed as an erratum only after it was confirmed on the rendered page *and* re-derived by hand or numerically. The few items checked from the text extraction alone are marked that way.

---

## Ch. 8 Feedback Motion Planning (PDF 193–224, book pp. 369–432) — Lessons: A3 (core), A4, A5
**Big idea:** A path is an open-loop plan; if the robot ends up somewhere unexpected, the path is useless. A **feedback plan** says what to do from *every* state. LaValle handles uncertainty *implicitly* here (no model of disturbances; just be ready for any state) and defers explicit uncertainty to Ch. 10–12 (book pp. 369–371, PDF 193–194). The unifying object is the **navigation function**: a potential whose greedy descent reaches the goal from everywhere reachable. Dijkstra/wavefront run *backward from the goal* already computes one; that is the key link to Choset ch. 4.

**What LaValle adds to Choset ch. 4 (see `choset-part1.md` §4.5–4.6):** Choset gives the wavefront and the Rimon–Koditschek sphere/star constructions. LaValle (i) defines a feedback plan $\pi:X\to U$ and shows it is interchangeable with a navigation function; (ii) proves "Dijkstra from the goal = optimal navigation function" via the principle of optimality; (iii) gives NF2 (maximum-clearance), Dial's algorithm, triangulation vector fields, exact optimal navigation functions in polygons, the acceleration-tracking law, harmonic potentials, funnel/cover composition, and **value iteration with interpolation** for continuous C-spaces. His general "navigation function" is weaker than Rimon–Koditschek's (no smoothness or Morse requirement); only §8.4.4.3 matches Choset Def 4.6.1.

### §8.2 Discrete state spaces (PDF 194–199, book pp. 371–381) — Lessons: A3, A4
**Big idea:** On a finite graph, store one action per state instead of one path per start. Any cost-to-go table with no spurious local minima is a plan.

- **Formulation 8.1** (book p. 372, PDF 194): finite $X$, action sets $U(x)$, transition $x'=f(x,u)$, stages $k=1,2,\dots$, goal $X_G$, stage-additive cost $L(\tilde x_F,\tilde u_K)=\sum_{k=1}^K l(x_k,u_k)+l_F(x_F)$, $F=K+1$ (8.3). No initial state is given.
- **Feedback plan** $\pi:X\to U$, with $\pi(x)=u_T$ (terminate) on $X_G$ (book p. 373, PDF 195). *Feasible*: reaches $X_G$ from every state that can reach it. *Optimal*: minimizes $L(\pi,x)$ from **every** $x$. One plan suffices for all starts (dynamic programming principle).
- **Local operator** (book p. 375, PDF 196): feasible $u^*=\arg\min_{u\in U(x)}\phi(f(x,u))$ (8.4); optimal $u^*=\arg\min_{u}\{l(x,u)+\phi(f(x,u))\}$ (8.5). With **isotropic** cost ($l$ equal for all non-terminal actions) (8.5) reduces to (8.4).
- **(Feasible) navigation function** (book p. 375, PDF 196): (1) $\phi=0$ on $X_G$; (2) $\phi(x)=\infty$ iff $X_G$ unreachable from $x$; (3) for every reachable $x\notin X_G$ the local operator gives $\phi(x')<\phi(x)$ (no local minima, no cycling).
- **Optimal navigation function** = optimal cost-to-go $G^*$, satisfying $\phi(x)=\min_u\{l(x,u)+\phi(f(x,u))\}$ (8.6) (book p. 376, PDF 196). Fig. 8.3 shows wavefront values on a grid.
- **Computing them** (book p. 377, PDF 197): Dijkstra backward from $X_G$ gives the optimal one; any backward search that records distance-to-goal gives a feasible one. $\pi$ and $\phi$ are interchangeable representations.
- **Grid resolution** (book p. 378, PDF 197): feedback needs 50–100 points per axis because the grid must *cover* $\mathcal C$, not just contain one path. Practical only for 3–4 dimensions (book p. 380).

**Wavefront propagation (Fig. 8.4, book p. 378, PDF 197)**: Dijkstra with unit costs, no priority queue, $O(n)$ in reachable cells (book p. 379, PDF 198).
```
W_0 ← X_G ; i ← 0
loop:
    W_{i+1} ← ∅
    for x in W_i: φ(x) ← i ; add unexplored neighbours of x to W_{i+1}
    if W_{i+1} = ∅: stop (unreached cells get φ = ∞)
    i ← i + 1
```
**NF2 maximum-clearance navigation function (book pp. 379–380, PDF 198)** (Latombe's name):
```
1. W_0 ← free cells adjacent to an obstacle; wavefront outward (= brushfire)
2. skeleton S ← cells where two fronts meet from opposite directions (grid GVD)
3. add a path from x_G to S into S
4. φ1 ← wavefront from x_G restricted to S          # moves along skeleton
5. φ2 ← wavefront with S as goal                     # moves onto skeleton
6. φ(x) = φ1(x) on S; else φ1(x') + φ2(x), x' = nearest skeleton cell to x
```
It optimizes length along the skeleton, not the minimum clearance across homotopy classes (book p. 380).

**Dial's algorithm** (book p. 380, PDF 198): integer costs → bucket queue indexed by cost (wavefront needs only two buckets); 8-neighbourhood grids reduce staircase paths.

### §8.3 Vector fields and integral curves (PDF 199–207, book pp. 381–398) — Lessons: A3 (background), R1 (tangent spaces of SO(3))
**Big idea:** In continuous space a feedback plan is a **vector field**, and execution is integrating it. Discontinuous fields (needed for exact planners) require Filippov solutions.

- Vector field $f:\mathbb R^n\to\mathbb R^n$ ↔ ODE $\dot x=f(x)$ (8.11–8.13) (book p. 386, PDF 201). Tangent space $T_p$, tangent bundle (8.9) (book p. 384).
- **Integral curve** $\tau$: $\frac{d\tau}{dt}(t)=f(\tau(t))$ (8.14), $\tau(t)=x_0+\int_0^tf(\tau(s))ds$ (8.15) (book p. 387, PDF 202). Unique if $f$ smooth or **Lipschitz** $\|f(x)-f(x')\|\le c\|x-x'\|$ (8.16) (book p. 388).
- **Switching boundary** $S=\{s(x)=0\}$ (8.17–8.19) and **Filippov solution** $\dot\tau(t)\in\bigcap_{\delta>0}\bigcap_{\mu(X_0)=0}\mathrm{hull}(f(B(\tau(t),\delta)\setminus X_0))$ (8.21), a differential inclusion (book pp. 388–389, PDF 202–203). Fig. 8.7: consistent, inward (sliding along $S$, chattering in practice → sliding-mode control), outward (non-unique start on $S$), tangent flows.
- §8.3.2 smooth manifolds (book pp. 390–398, PDF 203–207): charts, atlases, change of coordinates (Euler angles ↔ quaternions, Ex. 8.12), $S^n$ and $\mathbb{RP}^n$ atlases (8.22–8.30, $SO(3)\cong\mathbb{RP}^3$), tangent space via directional derivatives (8.31–8.34), $S^2$ tangent basis (Ex. 8.16, 8.35–8.37). Skippable for ASBR; useful for R1.

### §8.4 Complete methods for continuous spaces (PDF 207–214, book pp. 398–412) — Lessons: A3 (core), A4
**Big idea:** Lift the discrete ideas to $\mathcal C_{free}$: plan = vector field, navigation function = potential whose (generalized) gradient flow reaches the goal. Exact constructions exist for cell complexes and polygons; requiring smoothness (for dynamics) brings in Rimon–Koditschek and harmonic functions.

- **Formulation 8.2** (book pp. 398–399, PDF 207–208): $X$ smooth manifold (usually $\mathcal C_{free}$), $U(x)=T_x(X)$, $u_T=0$, $\dot x=u$ (8.38), goal $X_G$. $\pi$ is a solution if integral curves from all $x_I\in X_r$ reach $X_G$. Smooth fields reach a point goal only **asymptotically**; normalized fields ($\|f\|=1$ or 0) reach it in finite time. Hairy-ball theorem: no continuous nonvanishing field on $S^{n}$, $n$ even (book p. 400, PDF 208). Cost functional $L=\int_0^{t_F}l(x,u)dt+l_F(x(t_F))$ (8.39).
- **Continuous navigation function** (book p. 401, PDF 209): $\pi(x)=-\nabla\phi|_x$ with $\nabla\phi$ (8.40) (Khatib-style gradient descent); if $\nabla\phi$ does not exist, $u^*=\arg\min_u\phi(x')$ over a short step $\Delta t$ (8.41). The optimal cost-to-go is the optimal navigation function. Ex. 8.17: $\phi=\tfrac12(x_1^2+x_2^2)$ gives the inward field. Additive attractive + repulsive potentials generally have local minima (book p. 402).

**§8.4.2 Vector fields over cell complexes (book pp. 402–404, PDF 209–210)**
```
1. superstates = n-cells; superactions = move to a neighbour sharing an (n-1)-face
2. discrete navigation function φ̌ by Dijkstra/BFS from the cell containing x_G
3. goal cell: normalized inward field toward x_G
4. other cells: field that flows to the exit face chosen by π̌(cell)
   triangulation: outward field from the "repulsive vertex" opposite the exit edge (Fig. 8.11)
```
Integral curves (Filippov sense) reach $x_G$ in finite time; the field is discontinuous across faces.

**§8.4.3 Optimal navigation functions in a simple polygon (book pp. 405–407, PDF 211–212)**: $\phi(x)=\|x-x_G\|$ on the visibility polygon $V(x_G)$ (8.42). Repeatedly pop a **way point** $x$ (near vertex of a $V$-edge crossing the interior), set $\phi(x')=\phi(x)+\|x'-x\|$ on newly visible points (8.43), push new way points. $V(x)$ by radial sweep $O(n\lg n)$; total $O(n^2\lg n)$; with holes needs Dijkstra ordering ($O(n\lg n)$ by [443]). A continuous Dijkstra; no extension to 3-D (shortest paths NP-hard).

**§8.4.4 Toward dynamics (book pp. 407–412, PDF 212–214)**:
- Acceleration control $\ddot x=u$ (8.44). Tracking a field $f$: $u=K(f(x)-\dot x)+\nabla_{\dot x}f(x)$ (8.47) (book p. 408, PDF 212), $\nabla_{\dot x}f$ = directional derivative (8.45). Feed-forward + P on velocity error.
- Bounded velocity $\|\dot x\|<v_{max}$; bounded acceleration $\|u\|\le a_{max}$ requires $\|\nabla_{\dot x}f\|\le a_{max}$; discontinuous fields violate any bound; Lipschitz with $C<a_{max}$ suffices (book p. 409, PDF 213).
- Hessian (8.48), critical-point classification (book p. 410). **If $X$ is not contractible, any smooth $\phi$ has critical points besides $x_G$** (Morse theory), so smooth navigation functions must allow a measure-zero set of bad starts.
- **Rimon–Koditschek navigation function** (book p. 411, PDF 214): (1) $C^2$; (2) unique local minimum at $x_G$ on its component; (3) maximal and constant on $\partial\mathcal C_{free}$; (4) Morse. Built by distorting a sphere-world function (star-shaped regions); Lyapunov analysis is easy; hard in high dimensions because $\mathcal C_{obs}$ is implicit.
- **Harmonic potentials** $\nabla^2\phi=\sum_i\partial^2\phi/\partial x_i^2=0$ (8.49) (book pp. 411–412, PDF 214): Dirichlet (constant on boundary) or Neumann (flow tangent to obstacles) conditions; no spurious interior minima; costly numerical solve (Gauss–Seidel, related to value iteration).

### §8.5 Sampling-based methods for continuous spaces (PDF 214–223, book pp. 412–429) — Lessons: A5 (covers), A4 (value iteration ≈ Dijkstra), H-track (fitted value iteration)
**§8.5.1 Composition of funnels (book pp. 412–418, PDF 214–218).** **Big idea:** Mason's funnels: cover $\mathcal C_{free}$ with simple overlapping neighborhoods, plan on the neighborhood graph, and in each neighborhood use a field that flows into the next one.
- Cover: $\tilde X\subseteq\bigcup_{O\in\mathcal O}O$ (8.50); approximate if $\tilde X\subsetneq X$. Superaction $O\to O'$ when they overlap; field on $O$ sends $O\setminus O'$ into $O\cap O'$ (Fig. 8.16). Global potential $\phi(x)=\min_{O\ni x}\{\phi_O(x)+c\,\check\phi(O)\}$ (8.51) (book p. 416, PDF 216).
- **Incremental cover construction (Fig. 8.17, book p. 416, PDF 216)** = sampling-based neighborhood graph [Yang–LaValle]:
```
O ← ∅ ; i ← 1
loop:
    x ← α(i) ; d ← clearance(x)               # distance from collision checker
    if d > 0 and x ∉ any O: add ball B(x, r(d)) to O
    if termination test passes: break
    i ← i + 1                                   # book says "go to Step 1"; see Errata
remove neighborhoods contained in another
```
- Ball radius: point robot $r=d$. Planar rigid body with farthest point at radius $r$ from the origin: $(\Delta x)^2+(\Delta y)^2+(r\Delta\theta)^2<d^2$ (8.53); reparameterize $z=r\theta$ to get a true ball (8.54–8.55) (book p. 417, PDF 217). Same bound gives conservative edge checking for A5.
- Termination: fraction of samples landing outside the cover estimates uncovered volume (1 new ball per 1000 samples ≈ 99.9% covered); volume tests cannot guarantee narrow passages are kept (book p. 418).

**§8.5.2 Dynamic programming with interpolation (book pp. 418–429, PDF 217–223).** **Big idea:** Run backward value iteration on a grid of samples, but let the next state land *between* samples and interpolate $G^*$ there. This gives near-Euclidean (non-staircase) optimal navigation functions on continuous C-spaces.
- Recurrence $G^*_k(x_k)=\min_{u_k}\{l(x_k,u_k)+G^*_{k+1}(x_{k+1})\}$ (8.56) (book p. 419, PDF 218). Alternatives: parametric (neural-network) approximation [97] vs samples + interpolation.
- 1-D linear interpolation $G^*(x)\approx\alpha G^*(s_i)+(1-\alpha)G^*(s_{i+1})$ (8.57) with $s_i=i/r$ and weight (8.58) (book p. 420, PDF 218; see Errata). 2-D bilinear (8.59) (book p. 421, PDF 219); $2^n$ neighbours in $n$-D, or $n+1$ via Coxeter–Freudenthal–Kuhn simplices with $O(n\lg n)$ point location (Fig. 8.20). Interpolation region $R(S)$ (book p. 422).
- Motion planning set-up (book pp. 422–425, PDF 219–221): $U(x)=\{\|u\|\le1\}$ (8.60), sampled with low dispersion and always $u=0$; Euler step $x_{k+1}=x_k+\Delta t\,u$ (8.62–8.64); $l_d=\int_0^{\Delta t}l\,dt$ (8.65), $l_d=\|u_k\|$ for shortest paths; obstacle samples fixed at $\infty$ and any action whose interpolation neighbours touch $\mathcal C_{obs}$ costs $\infty$; goal must have nonempty $R(G)$ (enlarge a point goal). **$\Delta t$ must move the state to new interpolation neighbours**, else values freeze. Euclidean optimality depends on the C-space parameterization (book p. 426).
- **Dijkstra-like version** (book pp. 426–429, PDF 221–223): dead/alive/unvisited samples; backprojection $B(x)=\{x'\mid\exists u',\,x=f(x',u')\}$ (8.66–8.67); frontier $\mathrm{Front}(S')=(B(R(S'))\setminus S')\cap S$ (8.68).
```
G*(s) ← 0 for s ∈ G ; D ← G
Q ← Front(G), each with value from (8.56)
while Q ≠ ∅:
    s_s ← pop min(Q) ; D ← D ∪ {s_s}           # its value is now final
    recompute (8.56) for s ∈ Q ∩ Front(D)
    insert s ∈ B(R(D)) \ Q with value from (8.56)
```
Same idea as Tsitsiklis [946] / fast marching; LaValle never writes the Eikonal equation and mentions level-set methods only in Further Reading (book p. 429). Unit-stage wavefront version: $W_{i+1}=\mathrm{Front}(W_i)$ (book p. 429, PDF 223).

**Exercises worth doing (book pp. 430–432, PDF 223–224):**
- **20** (attractive + repulsive potentials with disc obstacles: how hard is it to avoid local minima?) — A3, the ASBR potentials project in one exercise.
- **2** (Dijkstra navigation function on a grid; add a clearance penalty) and **3** (fix NF2 so it picks the max-clearance route) — A3/A4.
- **15** (value iteration with interpolation for a translating and rotating polygon, weighted metric) — A4; H-track (fitted VI). **12** (n-D multilinear interpolation) and **13** ($\|u\|=1$ vs $\|u\|\le1$ convergence) as warm-ups.
- **7** (continuous field into $X_G$ on an annulus: possible?) — A3, the topological obstruction.
- **17** (triangulation vector field with needle diagram) and **18a** (optimal navigation function in a polygon) — A3/A4.
- **14** (numerically solve Laplace (8.49) with boundary conditions) — A3.
- **10/11** (collision-free ball from clearance with Euler angles / quaternions) — A5 conservative edge checking; R1.
- **4** (tangent-space bases for unit quaternions and $SO(3)\subset\mathbb R^9$) — R1/R2.

**Pitfalls:**
- "Navigation function" means two things in this chapter: LaValle's general one (any $\phi$ with no spurious minima, discrete or discontinuous allowed) vs Rimon–Koditschek's ($C^2$, Morse, constant max on the boundary). Exams quoting Choset mean the latter.
- Smooth navigation functions on non-contractible $\mathcal C_{free}$ must have saddles; "no critical points except the goal" is impossible there.
- Optimal (cost-to-go) navigation functions are only piecewise smooth: their fields are discontinuous and violate any acceleration bound. Smooth them or track them with (8.47).
- Wavefront/Dijkstra paths hug obstacles; NF2 fixes clearance only along one skeleton route.
- Feedback grids need far finer resolution than open-loop grid search; dimension ≤ 3–4.
- Value iteration with interpolation: too-small $\Delta t$ freezes values; point goals give an empty $R(G)$; sample-only collision checks can let interpolation cubes clip $\mathcal C_{obs}$.
- "Euclidean shortest" in $\mathbb R^2\times S^1$ depends on the rotation weight (cf. the $r\theta$ reparameterization, 8.55).

---

## Ch. 9 Basic Decision Theory (PDF 227–255, book pp. 437–494) — Lessons: A7 (core of §9.2), A6, H-track
**Big idea:** One decision, made against **nature**, a fictitious decision maker that models uncertainty. Nature is either **nondeterministic** (only the set of possible actions is known → worst case) or **probabilistic** (a distribution is known → expected case). Observations shrink nature's possible set or update its distribution by Bayes' rule. Ch. 10–12 iterate this single step over stages; the probabilistic version with observations is the measurement step of every Bayes/KF/PF filter.

### §9.1 Preliminaries (PDF 227–231, book pp. 438–446) — brief
- Formulation 9.1 optimization $u^*=\arg\min_uL(u)$ (9.1); use inf when the min may not exist ($U=(-1,1)$, $L=u$) (book p. 438–439). Multiobjective: dominance and **Pareto optimality** (Formulation 9.2, Ex. 9.1–9.3, book pp. 440–441).
- Probability review (book pp. 441–444, PDF 229–231): Bayes' rule $P(F|E)=P(E|F)P(F)/P(E)$ (9.6), conditional Bayes (9.7), marginalization (9.8), normalized form (9.9), expectation (9.10–9.12), conditional independence.
- Randomized (mixed) strategies $w$, expected cost $\bar L(w)=\sum_uL(u)w_u$ (9.13); randomization never helps against a fixed cost, only against an adversary (matching pennies, Ex. 9.7) (book pp. 445–446, PDF 231).

### §9.2 A game against nature (PDF 231–238, book pp. 446–459) — Lessons: A7, A6
- **Formulation 9.3** (book p. 447, PDF 232): robot actions $U$, nature actions $\Theta$, cost $L(u,\theta)$ (a $|U|\times|\Theta|$ cost matrix). Formulation 9.4: nature knows $u$, so $\Theta(u)$.
- **Nondeterministic (worst case, minimax):** $u^*=\arg\min_u\max_\theta L(u,\theta)$ (9.14) (book p. 448); with $\Theta(u)$: (9.18).
- **Probabilistic (expected case):** $u^*=\arg\min_uE_\theta[L(u,\theta)]$, $E_\theta[L]=\sum_\theta L(u,\theta)P(\theta)$ (9.15–9.16) (book p. 449, PDF 233); with $P(\theta|u)$: (9.19). Ex. 9.9: same matrix gives $u_1$ (minimax) or $u_2$ (expected).
- **Regret** $T(u,\theta)=\max_{u'}\{L(u,\theta)-L(u',\theta)\}$ (9.20); minimax regret (9.21), expected regret (9.22) (book p. 450).
- **Observations, Formulation 9.5** (book pp. 451–452, PDF 234): observation space $Y$; nondeterministic $Y(\theta)\subseteq Y$, probabilistic $P(y|\theta)$ + prior $P(\theta)$. A strategy is $\pi:Y\to U$.
  - Nondeterministic: $\Theta(y)=\{\theta\mid y\in Y(\theta)\}$ (9.23), $\pi^*(y)=\arg\min_u\max_{\theta\in\Theta(y)}L(u,\theta)$ (9.24).
  - Probabilistic: $P(\theta|y)=\dfrac{P(y|\theta)P(\theta)}{\sum_{\theta}P(y|\theta)P(\theta)}$ (9.25); $\pi^*(y)=\arg\min_u\sum_\theta L(u,\theta)P(\theta|y)$ (9.26) = minimize **conditional Bayes risk**; the normalizer can be dropped (book p. 453, PDF 235).
- **Nature acts twice, Formulation 9.6** (book pp. 453–454, PDF 235): nature picks $\theta$, then a **nature observation action** $\psi\in\Psi(\theta)$; sensor mapping $y=h(\theta,\psi)$. Nondeterministic $\Theta(y)=\{\theta\mid\exists\psi:\,y=h(\theta,\psi)\}$ (9.27); probabilistic $P(y|\theta)=P(\psi|\theta)$ for the unique $\psi$ (9.28). This is the generic "$z=h(x)+v$" sensor model, with $\psi$ the noise.
- **Multiple observations** (book p. 454, PDF 235): $\pi:Y_1\times\dots\times Y_n\to U$ (9.29); nondeterministic: intersect $\Theta(y_1)\cap\dots\cap\Theta(y_n)$ (9.30) (set-membership estimation); probabilistic with conditional independence: $P(\theta|y_{1:n})\propto P(\theta)\prod_iP(y_i|\theta)$ (9.31) ("naive Bayes"). With a static state, this is exactly repeated Bayes-filter measurement updates.
- **Examples (book pp. 455–459, PDF 236–238):** Bayesian classifier with 0–1 loss (9.32) minimizes the probability of error; OCR with shape/end/hole/corner features (Ex. 9.11, Fig. 9.1, (9.33–9.34)); parameter estimation with $L=(u-\theta)^2$, uniform prior, Gaussian $p(y|\theta)$ (9.35–9.40). Under squared loss the optimal $u$ is the posterior mean.

### §9.3–9.5 Games and critiques (PDF 238–254, book pp. 459–491) — brief, Lessons: H-track
- **Zero-sum games** (§9.3, book pp. 459–467, PDF 238–242): $L_1=-L_2$ (9.41); security strategies (9.44–9.47); saddle point $L(u^*,v)\le L(u^*,v^*)\le L(u,v^*)$ (9.52); multiple saddle points are interchangeable; mixed strategies $\bar L(w,z)=wAz$ (9.58) always admit a randomized saddle point (von Neumann), computed by linear programming (Ex. 9.16).
- **Nonzero-sum** (§9.4, book pp. 467–477, PDF 242–247): Nash equilibrium (9.66–9.67); non-unique, non-interchangeable; admissible equilibria; Battle of the Sexes, Prisoner's Dilemma; randomized Nash equilibria always exist (not an LP in general).
- **Critique** (§9.5, book pp. 477–491, PDF 247–254): utility theory and rationality axioms justify minimizing expected cost; Bayesians vs frequentists (frequentist risk); priors are the weak point, and a uniform "noninformative" prior is not parameterization-invariant (Ex. 9.26–9.27); worst-case reasoning needs $\Theta$ large enough, and an unbounded sensor-error set makes any sensor useless (§9.5.3).

**Exercises worth doing (book pp. 491–494, PDF 254–255):**
- **2 + 4** (minimax, expected, and regret decisions on a 3×4 matrix with $P=[.2,.4,.1,.3]$) — A7 warm-up. My answers: minimax $u_1$ (cost 3); expected $u_2$ (0.2); minimax regret $u_1$ (3); min expected regret $u_2$ (0.8).
- **7** (two classes, $\mathcal N(0,1)$ vs $\mathcal N(6,4)$, equal priors) — A7. Answer: choose $\omega_1$ iff $-6.22<y<2.22$ (unequal variances give two thresholds).
- **16** (Gaussian prior $\mathcal N(0,4^2)$, likelihood $\sigma=9$, $y_i=4$, squared loss) — A6: this is a 1-D Kalman update with a static state. Answer $u^*=64k/(81+16k)$: 0.660 ($k=1$), 2.656 ($k=10$), 3.980 ($k=1000$). Repeat with sampled $y_i$.
- **3b** (Hurwicz optimism coefficient) and **5/8c** (which priors make minimax = Bayes) — optional, show how decision rules depend on the prior.
- **10, 15, 17** (matching pennies, 2×2 mixed equilibrium, LP saddle-point solver) — H-track / game theory only.

**Pitfalls:**
- In Ch. 9–12, $\theta$ is nature's action (often a hidden state), not a joint angle or heading.
- LaValle minimizes **cost**; RL and Thrun's MDP chapter maximize reward/payoff. Flip signs.
- Worst-case and expected-case analyses can pick different actions from the same matrix (Ex. 9.9); know which one a question asks for.
- Naive Bayes (9.31) assumes conditionally independent observations; correlated sensor errors (for example, adjacent laser beams) make it overconfident, the same failure as beam independence in MCL.
- A uniform prior is a modeling choice, not "no information" (§9.5.2.2).

---

## Ch. 10 Sequential Decision Theory (PDF 256–287, book pp. 495–558) — Lessons: A4 (DP / Dijkstra with uncertainty), A3 (probabilistic navigation functions), H-track (MDPs, value/policy iteration, TD, Q-learning)
**Big idea:** Ch. 2's discrete planning plus a second decision maker, *nature*, who picks $\theta_k$ at every stage. The current state is always known (that changes in Ch. 11); only the *future* is uncertain. Because the outcome of an action is not predictable, a plan must be a **feedback plan** $\pi:X\to U$, not an action sequence. Two uncertainty models run in parallel throughout: **nondeterministic** (only the set $\Theta(x,u)$ is known; plan for the worst case) and **probabilistic** (a distribution $P(\theta\mid x,u)$ is known; plan for the expected case = an MDP). Everything is phrased with *costs to minimize*, not rewards.

### §10.1 Model, forward projections, backprojections, plans (PDF 256–262, book pp. 496–508)
- Markov nature (10.3) (book p. 497, PDF 257): $P(\theta_k\mid\tilde x_k,\tilde u_k)=P(\theta_k\mid x_k,u_k)$. Transition (10.4): $x_{k+1}=f(x_k,u_k,\theta_k)$. Induced transition probability (10.6)–(10.7): $P(x_{k+1}\mid x_k,u_k)=\sum_{\theta:\,f(x_k,u_k,\theta)=x_{k+1}}P(\theta\mid x_k,u_k)$.
- **Formulation 10.1 (Discrete Planning with Nature)** (book p. 498, PDF 257): state space $X$; action sets $U(x)$ incl. termination $u_T$; nature sets $\Theta(x,u)$; $f$; stages $k=1,2,\dots$ (or $F=K+1$); $x_I$; $X_G$; stage-additive cost (10.8) $L=\sum_{k=1}^K l(x_k,u_k,\theta_k)+l_F(x_F)$. Feasible version: $l\equiv0$, $l_F=0$ on $X_G$ else $\infty$ (10.9). With probabilities this *is* an MDP ("controlled Markov chain"). Equivalent shortcut: give $X_{k+1}(x,u)$ or $P(x'\mid x,u)$ directly and never mention $\theta$ (book p. 499, PDF 258).
- Ex. 10.1 (number line, book p. 499) and Ex. 10.2 (15×15 grid, nature adds a random neighbour move, book p. 500, PDF 258) are the running examples.
- **Forward projection** (book pp. 501–503, PDF 259–260): nondeterministic $X_{k+1}(X_k,u_k)=\{f(x_k,u_k,\theta)\mid x_k\in X_k,\theta\in\Theta\}$ (10.12); probabilistic = marginalization (10.13); with column-stochastic matrices $M_u$, $m_{ij}=P(x_{k+1}=i\mid x_k=j,u)$ (10.15)–(10.16), the $k$-stage projection is $v'=M_{u_{k-1}}\cdots M_{u_1}v$ (10.17). Ex. 10.4: two steps of $u=2$ give $[1,2,3,2,1]/9$ (10.19).
- **Backprojections** (book pp. 503–505, PDF 260–261): weak $\mathrm{WB}(x,u)=\{x'\mid\exists\theta: f(x',u,\theta)=x\}$ (10.20); strong $\mathrm{SB}(x,u)=\{x'\mid\forall\theta: f(x',u,\theta)=x\}$ (10.21); set versions (10.22)–(10.24) — $\mathrm{WB}(S,u)=\bigcup_{x\in S}\mathrm{WB}(x,u)$ but $\mathrm{SB}(S,u)$ is **not** the union of $\mathrm{SB}(x,u)$; action-free versions (10.25)–(10.26). $x'\in\mathrm{SB}(S,u)\iff X_{k+1}(x',u)\subseteq S$. These are the discrete ancestors of **preimage planning** (Ch. 12.5).
- **Plan and its cost** (book pp. 505–508, PDF 261–262): $\pi:X\to U$; stage-dependent $\pi:X\times\mathcal K\to U$ only for fixed $K$. Fixing $\pi$ leaves a Markov chain $M_\pi$ (10.30) and plan graph $G_\pi$. Worst-case cost $G_\pi(x_1)=\max_{H(\pi,x_1)}L$ (10.31); expected cost $G_\pi(x_1)=E_{H(\pi,x_1)}[L]$ (10.32). One plan $\pi^*$ is optimal from every initial state (principle of optimality, book p. 508).

### §10.2 Algorithms for feedback plans (PDF 262–269, book pp. 508–521)
**Value iteration** (book pp. 508–514, PDF 262–265). Backward DP, $G^*_F=l_F$ (10.34):
- Nondeterministic (10.39) (book p. 510, PDF 263): $G^*_k(x_k)=\min_{u_k}\max_{\theta_k}\{l(x_k,u_k,\theta_k)+G^*_{k+1}(f(x_k,u_k,\theta_k))\}$.
- Probabilistic (10.45)–(10.46) (book p. 511, PDF 264): $G^*_k(x)=\min_u\{l(x,u)+\sum_{x'}G^*_{k+1}(x')P(x'\mid x,u)\}$.
- Recover the plan *locally* at run time (10.48)–(10.49) (book p. 514, PDF 265): $\pi^*(x)=\arg\min_u E_\theta[l+G^*(f(x,u,\theta))]$ — the cost-to-go *is* the plan, exactly as a navigation function is (Ch. 8; Choset §4.5–4.6).
- Convergence (book pp. 511–514): nondeterministic VI diverges where the goal is only *possibly* reachable (Fig. 10.2); probabilistic VI can converge only **asymptotically** when $G_\pi$ has cycles with probabilities in (0,1) (Fig. 10.3). Ex. 10.6 (book p. 513, PDF 265): a 4-cost loop taken with prob. 1/2 gives $G_\pi(x_I)=3+4\sum_{i\ge1}2^{-i}=7$ (10.47; tail-sum form of $E[\#\text{loops}]=1$). Stop when $\max_x|\Delta G|<\epsilon$.

```
VALUE ITERATION (probabilistic, cost-to-go form; book pp. 510–514)
G(x) ← 0 for x ∈ X_G, ∞ elsewhere          # l_F
repeat
    for x ∈ X \ X_G:
        G'(x) ← min_u [ l(x,u) + Σ_x' P(x'|x,u) G(x') ]
    G ← G'
until max_x |G'(x) − G(x)| < ε
π(x) ← argmin_u [ l(x,u) + Σ_x' P(x'|x,u) G(x') ]      # evaluated on line during execution
```

**Policy iteration** (Fig. 10.4, book p. 516, PDF 266). Evaluate a fixed plan by solving the $n\times n$ linear system (10.53) $G_\pi(x)=l(x,\pi(x))+\sum_{x'}G_\pi(x')P(x'\mid x,\pi(x))$; improve with (10.54) $\pi'(x)=\arg\min_u\{l(x,u)+\sum_{x'}G_\pi(x')P(x'\mid x,u)\}$; stop when no state improves. Finite plans ⇒ finite termination. Fewer iterations than VI, but each one is a linear solve (SVD/sparse) — LaValle prefers VI for big $X$ (book p. 518).
```
POLICY ITERATION (Fig. 10.4)
π ← u_T on X_G, arbitrary elsewhere
loop
    solve G_π = l_π + P_π G_π   (n linear equations, G_π = 0 on X_G)
    π'(x) ← argmin_u [ l(x,u) + Σ_x' P(x'|x,u) G_π(x') ]
    if no G value improves: return π
    π ← π'
```
Ex. 10.7 (book pp. 516–518, PDF 266–267), three states $a,b,c=x_G$: initial $\pi\equiv1$ gives $G_\pi(a)=G_\pi(b)=3$; improved $\pi\equiv2$ gives $G_\pi(a)=12/7$, $G_\pi(b)=10/7$ — optimal (checked by hand).

**Graph-search methods** (book pp. 518–521, PDF 267–269):
- **Backprojection algorithm** (Fig. 10.6, book p. 519, PDF 268): $S\leftarrow X_G$; repeatedly add any $x$ with some $u$ such that $x\in\mathrm{SB}(S,u)$; stop when $\mathrm{SB}(S)=S$. Complete for *guaranteed* (worst-case) feasible plans; restrict candidates to $\mathrm{WB}(S)\setminus S$.
- **Nondeterministic Dijkstra** (Fig. 10.8, book p. 520, PDF 268): pop the min-$G$ state of the active set $A$, freeze $G^*$, then update its frontier $\mathrm{Front}(x_s,S)$ = states having some $u$ whose forward projection contains $x_s$ and lies inside $S$, using the worst-case $G(x)=\min_{u\in U_f(x)}\max_\theta\{l+G(f)\}$ (10.64).
- **Probabilistic Dijkstra** (10.65) (book p. 521, PDF 269) works **only** if some plan makes $G_\pi$ strictly decrease along every positive-probability transition; otherwise no propagating "band" exists and Dijkstra breaks.

### §10.3 Infinite-horizon problems (PDF 269–272, book pp. 522–527)
**Formulation 10.2** (book p. 522): no goal, no $u_T$. Two cost models: **discounted** (10.67) $L=\sum_{k\ge0}\alpha^kl_k$, $\alpha\in(0,1)$, bounded by $c/(1-\alpha)$ (10.68); **average cost per stage** (10.69) $L=\lim\frac1K\sum_{k<K}l_k$. Discounted VI via the index flip $J^*_{K-k}=\alpha^{-k}G^*_k$ (10.73) gives (10.74) and the stationary **Bellman equation** (10.76) (book p. 526, PDF 271): $J^*(x)=\min_u\{l(x,u)+\alpha\sum_{x'}J^*(x')P(x'\mid x,u)\}$. Discounted policy iteration (10.77)–(10.78). Average cost: run undiscounted VI, test $\max_x|G^*_k/N-G^*_{k+1}/(N-1)|<\epsilon$ (10.80); rescale or use **relative value iteration** (subtract $G$ at a reference state) to stop overflow (book p. 527, PDF 272). Small $\alpha$ converges fast but is "short-sighted".

### §10.4 Reinforcement learning (PDF 272–276, book pp. 527–535) — H-track
**Big idea:** same DP, but $P(x'\mid x,u)$ and $l$ are unknown; a Monte Carlo simulator (Fig. 10.11, book p. 529, PDF 273) returns $x_{k+1}$ and $l(x_k,u_k)$. LaValle's preferred name: *simulation-based dynamic programming*. Exploration vs exploitation: every $(x,u)$ must be tried infinitely often for convergence (book p. 530).
- Running mean (10.83): $m_i=m_{i-1}+\frac1i(c_i-m_{i-1})$. **Monte Carlo evaluation** (10.85)–(10.86) (book pp. 530–531, PDF 273–274): update every visited state with its observed cost-to-go.
- **Temporal difference** (10.87) (book p. 531, PDF 274): $d_k=l(x_k,u_k)+\hat G_\pi(x_{k+1})-\hat G_\pi(x_k)$; TD(1) = MC via telescoping (10.88)–(10.92); **TD($\lambda$)** (10.93) and **TD(0)** (10.94) (book p. 533, PDF 275): $\hat G_\pi(x_k)\mathrel{+}=\frac1{v_k}d_k$ with visit count $v_k$. Generic Robbins–Monro step (10.95)–(10.96): $\hat G_\pi(x)\leftarrow(1-\rho)\hat G_\pi(x)+\rho\,(l(x,u)+\hat G_\pi(x'))$.
- **Q-factors** (book pp. 534–535, PDF 275–276): $G^*(x)=\min_uQ^*(x,u)$ (10.97); $Q^*(x,u)=l(x,u)+\sum_{x'}P(x'\mid x,u)\min_{u'}Q^*(x',u')$ (10.98); $\pi^*(x)=\arg\min_uQ^*(x,u)$ (10.100). **Q-learning** (10.101)/(10.102) (book p. 535, PDF 276):
```
Q-LEARNING (cost-minimizing, discounted; book p. 535)
Q(x,u) ← 0 for all x,u
repeat each simulated step:
    choose u at x (explore/exploit); simulator returns x', l(x,u)
    Q(x,u) ← (1−ρ) Q(x,u) + ρ [ l(x,u) + α min_{u'} Q(x',u') ]
π(x) = argmin_u Q(x,u)
```
- Simulation-based policy iteration (10.103)–(10.104): estimate $Q_\pi$ by simulation, then improve.

### §10.5 Sequential game theory (PDF 276–284, book pp. 536–551) — brief
Game trees (extensive form), information models (alternating play, stage-by-stage, open loop), **security plans** by bottom-up min/max propagation and **alpha-beta pruning** (book pp. 538–543); Formulation 10.4 sequential zero-sum games on a state space solved by min-max value iteration (book pp. 544–547); Nash equilibria in sequential games, games with nature (book pp. 548–551). Not needed for ASBR.

### §10.6 Continuous state spaces (PDF 284–286, book pp. 551–556) — A3, A4
Value iteration with interpolation (Ch. 8.5.2) now with nature: (10.122)–(10.123) (book p. 552, PDF 284) replace sums by integrals over $p(\theta\mid x,u)$ or $p(x'\mid x,u)$; discretize $\Theta$ into intervals to evaluate them. Discrete-time motion model with nature (10.124): $x(t+\Delta t)=x(t)+\Delta t\,(u+\theta)$. Fig. 10.20 (book p. 553, PDF 285): a **probabilistic navigation function** and its vector field for a 2-D robot whose heading is disturbed — every execution differs, average cost ≈ optimum. Formulation 10.5 (book p. 555, PDF 286): hybrid system where nature flips a mode (door open/closed); VI over $C\times$modes, interpolation only in $C$ (Figs. 10.21–10.22). Continuous Dijkstra-like versions need the *weak* backprojection.

**Exercises worth doing (book pp. 556–558, PDF 286–287):** 1–2 (SB/WB set facts, 10 min each), 10 (detect "possibly but not guaranteed reachable" in VI), 15 (policy iteration on a deterministic problem — collapses to Dijkstra-like wave, A4), 16 (discounted ≡ stochastic shortest path, H-track), 17 (nondeterministic discounted VI); implementations 23 (reproduce the asymptotic convergence of Fig. 10.3), 24 (nondeterministic Dijkstra on a grid, A4), 25 (probabilistic Dijkstra and its monotonicity condition), 26 (Q-learning: effect of $\rho$, **H-track**). Also redo Ex. 10.6 and 10.7 by hand, and code Ex. 10.2 (grid with nature) with VI and compare the policy to an A\* path.

**Pitfalls:**
- LaValle minimizes **cost** with discount $\alpha$; RL texts (and Thrun Ch. 15) maximize **reward** with $\gamma$. Q-learning here uses $\min$, not $\max$.
- Probabilistic VI with cycles never terminates exactly; use an $\epsilon$ stopping rule. Nondeterministic VI diverges on states where nature can cycle you forever.
- Probabilistic Dijkstra is not generally valid; check the strict-decrease condition.
- Matrix convention: LaValle's $M_u$ is **column**-stochastic (10.16); many RL/Markov-chain texts use row-stochastic $P$.

---

## Ch. 11 Sensors and Information Spaces (PDF 288–324, book pp. 559–632) — Lessons: A7 (Bayes filter, histogram/grid filter, PF — general frame), A6 (KF as a sufficient Gaussian I-map; linear sensor models), A3/A4 (planning without full state; preimages), A8 (exam-level unifying view)
**Big idea:** When the state is hidden, the only thing the robot really knows is its **history I-state** $\eta_k=(\eta_0,\tilde u_{k-1},\tilde y_k)$: initial condition, actions so far, observations so far. Treat the space of these as a new, *fully observed* state space (the **I-space**) and plan feedback $\pi:\mathcal I\to U$ on it. Histories grow without bound, so compress them with an **information map** $\kappa$; the compression is usable online only if it is **sufficient** (the next derived I-state is computable from the current one, $u_k$ and $y_{k+1}$). The two key sufficient maps are the **nondeterministic I-state** (the set of states consistent with the history — updated by *union then intersection*) and the **probabilistic I-state** (the posterior — updated by *marginalization then Bayes rule*). The Bayes filter, KF and particle filter are just ways to compute $\kappa_{prob}$. This is the general frame that the ASBR filtering weeks (Choset ch. 8–9, Thrun ch. 2–4) are special cases of.

### §11.1 Discrete state spaces: sensors, history I-space, planning problem (PDF 289–293, book pp. 561–570)
- **Sensor** = observation space $Y$ + sensor mapping $h$ (book p. 561, PDF 289). Three kinds: state sensor $y=h(x)$; state-nature sensor $y=h(x,\psi)$, $\psi\in\Psi(x)$ (nature *sensing* action, unknown, modeled nondeterministically or by $P(\psi\mid x)$); history-based $y_k=h_k(x_1,\dots,x_k,\psi_k)$ (11.1).
- Examples 11.1–11.10 (book pp. 562–564): odd/even, mod, sign, selective (one coordinate), bijective/identity, null, disturbed ($y=x+\psi$), disturbed sign, disturbed parity (useless under the nondeterministic model, informative under a probabilistic one), delayed observation.
- **Preimage** $H(y)=\{x\mid y=h(x)\}$ (11.6) (book p. 563, PDF 290) partitions $X$ (the sensor's "resolution"); for $h(x,\psi)$, $H(y)=\{x\mid\exists\psi: y=h(x,\psi)\}$ (11.11) (book p. 564) — sets overlap, no partition. Probabilistic sensor model $P(y\mid x)$ from $P(\psi\mid x)$ (11.7).
- Initial condition $\eta_0$: known $x_1$, a set $X_1$, or a prior $P(x_1)$ (book p. 565, PDF 291). **History I-state** (11.14)–(11.15) (book p. 566, PDF 291): $\eta_k=(\eta_0,\tilde u_{k-1},\tilde y_k)=(\eta_{k-1},u_{k-1},y_k)$. Note $u$ history runs only to $k-1$. History I-space $\mathcal I_k=\mathcal I_0\times\tilde U_{k-1}\times\tilde Y_k$ (11.17), $\mathcal I_{hist}=\bigcup_k\mathcal I_k$ (11.19) (book p. 567, PDF 292) — a union over stages, not a product.
- **Formulation 11.1 (Discrete I-space planning)** (book pp. 568–569, PDF 292–293): Formulation 10.1's $X,U,\Theta,f$ + $Y$, $\Psi(x)$, $h$, $\eta_0$, $\mathcal I_{hist}$, cost (11.21); plan $\pi:\mathcal I_{hist}\to U$ (11.20). Worst-case cost (11.22), expected cost (11.23) (book p. 570, PDF 293). $U$ is fixed (state-dependent $U(x)$ would leak state information).
- **"The I-space is just another state space"** (book p. 570, PDF 293): information transition $\eta_{k+1}=f_{\mathcal I}(\eta_k,u_k,y_{k+1})$ (11.25); $y_{k+1}$ plays the role nature's $\theta_k$ played in Ch. 10. So Ch. 10's machinery (VI, backprojections) applies on $\mathcal I$ — that is Ch. 12.

### §11.2 Derived I-spaces (PDF 294–299, book pp. 571–581)
**I-maps** (book pp. 571–574, PDF 294–295): $\kappa:\mathcal I_{hist}\to\mathcal I_{der}$; plan $\pi\circ\kappa$; $\kappa$ partitions histories via preimages $\kappa^{-1}$ (11.26). Ex. 11.11: the classical estimator $\kappa_{est}:\mathcal I_{hist}\to X$ gives a state-feedback plan but **throws away the uncertainty** (cannot act on covariance) and is usually **not sufficient** (book pp. 572–574). Ex. 11.12: $\kappa_{stage}(\eta_k)=k$ = open-loop plan. **Sufficient I-map** (book p. 573, PDF 295): $\kappa(\eta_{k+1})=f_{\mathcal I_{der}}(\kappa(\eta_k),u_k,y_{k+1})$ (11.27) — the I-space analogue of a sufficient statistic; commutative diagram Fig. 11.4.

**Nondeterministic I-states** $X_k(\eta_k)\subseteq X$ (book pp. 574–577, PDF 295–297). Set-valued $F(x,u)=\{f(x,u,\theta)\mid\theta\in\Theta(x,u)\}$ (11.28).
- Init (11.29): $X_1(\eta_1)=X_1\cap H(y_1)$.
- Predict (11.30): $X_{k+1}(\eta_k,u_k)=\bigcup_{x_k\in X_k(\eta_k)}F(x_k,u_k)$.
- Update (11.31): $X_{k+1}(\eta_{k+1})=X_{k+1}(\eta_k,u_k)\cap H(y_{k+1})$.
$\mathcal I_{ndet}=\mathrm{pow}(X)$, finite if $X$ is; goal reached when $X_k(\eta_k)\subseteq X_G$. Ex. 11.13 (three states, $f=(x+u+\theta)\bmod3$, $y=x+\psi$; book pp. 576–577, PDF 296–297) — worked by hand, numbers check. This is **set-membership (bounded-error) filtering**; no existing library note covers it.

**Probabilistic I-states** $P(x_k\mid\eta_k)$ (book pp. 577–580, PDF 297–298): union → marginalization, intersection → Bayes rule.
- Init (11.36): $P(x_1\mid\eta_1)\propto P(y_1\mid x_1)P(x_1)$.
- Predict (11.38): $P(x_{k+1}\mid\eta_k,u_k)=\sum_{x_k}P(x_{k+1}\mid x_k,u_k)P(x_k\mid\eta_k)$ (uses (11.37) Markov).
- Update (11.39)–(11.40): $P(x_{k+1}\mid\eta_{k+1})=\dfrac{P(y_{k+1}\mid x_{k+1})P(x_{k+1}\mid\eta_k,u_k)}{\sum_{x_{k+1}}P(y_{k+1}\mid x_{k+1})P(x_{k+1}\mid\eta_k,u_k)}$.
This is exactly Thrun's Bayes filter: $bel(x_t)\equiv P(x_k\mid\eta_k)$, $\overline{bel}(x_t)\equiv P(x_{k+1}\mid\eta_k,u_k)$. $\mathcal I_{prob}$ for $|X|=n$ is the $(n-1)$-simplex (Ex. 11.14, Fig. 11.6, book pp. 579–580; §11.3.3 POMDPs, book p. 589, PDF 303); vertices = perfect knowledge.
```
DISCRETE BAYES / HISTOGRAM FILTER AS I-STATE UPDATE (11.36)–(11.40)
p ← P(x1);  p(x) ← P(y1|x) p(x); normalize
for each stage k:
    p⁻(x') ← Σ_x P(x'|x,u_k) p(x)          # (11.38) — nondet: union of F(x,u_k)
    p(x')  ← P(y_{k+1}|x') p⁻(x'); normalize # (11.39) — nondet: ∩ H(y_{k+1})
```
**Limited-memory I-spaces** (book pp. 580–581, PDF 298–299): last-$i$-stages map $\kappa_i$ (11.41) (sufficient, finite if $X,U$ finite); **sensor feedback** $\kappa_{sf}(\eta_k)=y_k$, plan $\pi:Y\to U$ = purely reactive plan (sufficient but usually loses solvability).

### §11.3 Discrete examples (PDF 299–303, book pp. 581–589)
Ex. 11.15 sign sensor: three reachable nondeterministic I-states, solvable by the reactive plan (11.43) (book p. 582). Ex. 11.16 **goal recognizability** (book p. 582, PDF 299): with $|\psi|\le5$ you can never certify reaching $\{0\}$, only an 11-wide goal. Ex. 11.17 **L-shaped corridor without sensors** (book pp. 582–585, PDF 299–301): nature over-shoots 1–3 cells, yet "push left 9 times, then up 9 times" localizes exactly — walls (blocking) shrink the I-state; derived spaces $\mathcal I_3$ (not sufficient) vs $\mathcal I_{19}$ (sufficient). §11.3.2 NFAs (book pp. 585–589): NFA acceptance = sensorless planning on $\mathrm{pow}(X)$; subset construction (NFA→DFA, $2^n$ states) *is* the nondeterministic I-space. §11.3.3: POMDP = Formulation 11.1 with probabilities; planning in the simplex (algorithms in §12.1.3).

### §11.4 Continuous state spaces (PDF 303–307, book pp. 589–598)
- Same definitions with manifolds $X,Y,\Psi,U,\Theta$ and densities (book p. 590, PDF 303); continuous-time history $\eta_t=(\eta_0,\tilde u_t,\tilde y_t)$ (11.53), cost (11.55) (book p. 591, PDF 304).
- Continuous probabilistic update (book p. 592, PDF 304): (11.56) init; **predict** (11.57) $p(x_{k+1}\mid\eta_k,u_k)=\int_Xp(x_{k+1}\mid x_k,u_k)p(x_k\mid\eta_k)\,dx_k$; **update** (11.58) $p(x_{k+1}\mid\eta_{k+1})=\dfrac{p(y_{k+1}\mid x_{k+1})p(x_{k+1}\mid\eta_k,u_k)}{\int p(y_{k+1}\mid x_{k+1})p(x_{k+1}\mid\eta_k,u_k)\,dx_{k+1}}$.
- **Conservative approximations** of nondeterministic I-states (book pp. 593–595, PDF 305–306): bound $X_k$ by a ball/ellipsoid each step, (11.60) $\hat X'_{k+1}=\big(\bigcup_{x\in\hat X_k}F(x,u_k)\big)\cap H(y_{k+1})$, then re-bound with $\hat X_{k+1}\supseteq\hat X'_{k+1}$. Not sufficient: the approximate sequence diverges from the exact one (Figs. 11.9–11.10), but stays safe if always conservative. Ellipsoids cost $\binom n2+2n$ parameters vs $n+1$ for balls.
- **Moment-based approximations** $\kappa_{mom}$ (book pp. 595–597, PDF 306–307): keep mean (11.62) and covariance (11.63)–(11.64) — i.e. assume Gaussian. Mean only = state estimation (uncertainty lost). Generally not sufficient; exact only for linear-Gaussian models (→ KF). Gaussian confidence ellipsoids ↔ nondeterministic ellipsoids.
- Continuous-time nondeterministic I-state (11.66) (book p. 597, PDF 307): all $x$ reachable from $X_0$ by some $\tilde\theta_t$ consistent with the observation history under some $\tilde\psi_t$.

### §11.5 Continuous examples and sensor catalog (PDF 307–315, book pp. 598–614)
**Sensor models** (book pp. 598–605, PDF 307–311):

| Sensor | Model (book page) | ASBR link |
|---|---|---|
| Identity / bounded noise | $y=x+\psi$, $\|\psi\|\le r$ (11.67) ⇒ $H(y)$ = ball of radius $r$ at $y$ (p. 598, Fig. 11.11) | GPS-like position fix (A6) |
| Gaussian noise | $p(\psi)$ multivariate Gaussian (11.68) (p. 599) | KF/EKF noise (A6) |
| Linear | $y=Cx+W\psi$ (11.70); singular $C$ observes a rank-$C$ projection (11.71) (pp. 599–600) | KF $H$ matrix, observability (A6) |
| Projection | position sensor $y=p$; compass $y=s$, $\lvert s-y\rvert\le\epsilon$; gyroscope; joint encoder; speedometer, accelerometer (pp. 600–601) | GPS+IMU (Assignment 4) |
| Boundary | contact $h=1$ iff $x\in\partial X$; proximity within $r$; force sensor (p. 601) | bug algorithms, A3 |
| Landmark | homing (bearing) $h(x)$ = direction to landmark $l$; "Geiger counter" range $h=\lVert x-l\rVert$; pebble / landmark-region detector; three non-collinear bearings give the exact state (pp. 602–603) | range–bearing EKF (A6), MCL (A7) |
| Depth | $k$ sonars $Y=[0,\infty]^k$; range scanner = continuum of sonars over $S^1$; **gap sensor** reports only depth discontinuities (pp. 603–605, Figs. 11.14–11.16) | beam / likelihood-field models (A7) |
| Odometry | history-based sensor $y=h(\tilde x_t)$ (distance travelled), or per-stage distance; wall clock (p. 605) | odometry motion model (A6/A7) |
LaValle does **not** count the action history as odometry (it is already in $\eta_k$), unlike Thrun's odometry-as-control (book p. 605, PDF 311).

**Projection examples** (book pp. 605–609, PDF 311–313): Ex. 11.20 point on $x_2=\sin x_1$ observing only height — the nondeterministic I-state doubles at every extremum (bifurcation, Fig. 11.19); Ex. 11.21 point on a graph — I-space layers with directional reachability (Fig. 11.21). **Nature sensing examples** (book pp. 609–612, PDF 313–314): Ex. 11.22 above/below a disc (decidable iff disc radius > sensor error); Ex. 11.23 **simple mobile robot** with bounded distance and heading errors: $F(x,u)$ is an annular sector, then intersect with the ball $H(y)$ (Figs. 11.25–11.27) — a set-valued picture of EKF predict/update.

**§11.5.4 Gaining information without sensors** (book pp. 612–614, PDF 314–315): Ex. 11.24 **tray tilting** — six tilts put a ball in a chosen corner from any start, with no sensing (Fig. 11.29). Uncertainty shrinks because of known action effects + boundary contact. Links to sensorless manipulation (§12.5.2) and to Ex. 11.17.

### §11.6 Computing probabilistic I-states (PDF 315–318, book pp. 614–619) — A6, A7
**Kalman filter** (book pp. 615–617, PDF 316–317). Linear-Gaussian (LG) model: $\kappa_{mom}$ is exact and sufficient, $\mathcal I_{gauss}\subset\mathbb R^{2n+\binom n2}$.
- Motion (11.75): $x_{k+1}=A_kx_k+B_ku_k+G_k\theta_k$, $\theta\sim\mathcal N(0,\Sigma_\theta)$. Sensor (11.77): $y_k=C_kx_k+H_k\psi_k$, $\psi\sim\mathcal N(0,\Sigma_\psi)$.
- Init (11.78)–(11.79): $\mu_1=\mu_0+L_1(y_1-C_1\mu_0)$, $\Sigma_1=(I-L_1C_1)\Sigma_0$.
- Predict (11.81): $\Sigma'_{k+1}=A_k\Sigma_kA_k^T+G_k\Sigma_\theta G_k^T$.
- Gain (11.83): $L_k=\Sigma'_kC_k^T\big(C_k\Sigma'_kC_k^T+H_k\Sigma_\psi H_k^T\big)^{-1}$ (transpose missing in print — see Errata).
- Update (11.80), (11.82): $\mu_{k+1}=A_k\mu_k+B_ku_k+L_{k+1}\big(y_{k+1}-C_{k+1}(A_k\mu_k+B_ku_k)\big)$, $\Sigma_{k+1}=(I-L_{k+1}C_{k+1})\Sigma'_{k+1}$.
- **LQG** remark (book p. 617): the optimal quadratic-cost plan depends only on $\mu_k$ (certainty equivalence), but $\Sigma_k$ is still needed to *compute* $\mu_k$, so $\kappa:\mathcal I\to X$ alone is not sufficient. **EKF** = the second-order moment approximation applied to nonlinear models; not justified in general but widely used.

**Sampling-based approaches** (book pp. 617–619, PDF 317–318):
- **Grid filter**: approximate densities by probability mass in Voronoi "buckets" of grid samples (e.g. Sukharev grid), then apply discrete (11.38)–(11.39); drop near-empty buckets (book p. 618).
- **Particle filter** (book pp. 618–619, PDF 317–318):
```
PARTICLE FILTER, LaValle's SIR form (book p. 619)
given samples S_k with distribution P_k
repeat m times:
    1. draw x_k ∈ S_k according to P_k                  # resample
    2. draw x_{k+1} ~ p(x_{k+1} | x_k, u_k)             # predict (motion model)
    3. weight w(x_{k+1}) = p(y_{k+1} | x_{k+1})          # correct (sensor model)
normalize weights over S_{k+1} → P_{k+1}
```
Converges as $m\to\infty$; main failure mode = too few samples where the mass is (same issue as Ch. 5 sampling planners). Localization use is in §12.2.3.

### §11.7 Information spaces in game theory (PDF 318–322, book pp. 619–627) — brief
Each player has its own nondeterministic I-space; game-tree information sets (Kuhn) drawn as circled vertex groups give alternating-play, stage-by-stage, open-loop and stranger models (Fig. 11.30); n-player games on state spaces with per-player sensors and I-states (§11.7.2); Battleship (Ex. 11.27) as an I-space game. Not ASBR content.

**Exercises worth doing (book pp. 628–631, PDF 322–324):**
- 1, 2 — forward projections in $\mathcal I_{ndet}$ and $\mathcal I_{prob}$ (they *are* the predict step; 15 min) — A7.
- 5 — numeric Bayes-filter drill on the 3-state POMDP (history $(1,0,1,0,0)$); best paper-exam practice for A7. Fix the transition matrices first (see Errata).
- 10 — is a composition of sufficient I-maps sufficient? (A8 concept check).
- 12 — three bearing beacons recover position; what do two give? (A6/A7 landmark localization, triangulation).
- 13 — nondeterministic I-states for Ex. 11.23 (set-based localization; contrast with EKF ellipses).
- 14 — tray-tilt planner for rectilinear polygons (sensorless planning, A3).
- 16 — "where did I come from?": $X_1(\eta_k)$, $P(x_1\mid\eta_k)$ = smoothing (A7 extension).
- 18 — card counting as an I-map (H-track intuition for compressed state).
- Implementations 19 (planar KF with confidence ellipses — A6, mirrors Assignment 4 plots), 20 (grid filter vs particle filter on a 2-D polygon — A7, mirrors Assignment 5).

**Pitfalls:**
- $\psi$ is nature's *sensing* action; $\theta$ is nature's *transition* action. Do not mix them with the ASBR/Choset use of $\theta$ for heading.
- In LaValle's KF, $H_k$ is the **noise-input** matrix of the sensor and $C_k$ is the measurement matrix. In the ASBR slides and Thrun, $H$ (or $C_t$ in Thrun) is the measurement matrix. Translate before copying.
- A state estimate ($\kappa_{est}$) is not an I-state: planning on the mean alone ignores how uncertain you are (the motivation for coastal navigation / POMDPs).
- Nondeterministic I-states only shrink through intersection with $H(y)$ or through boundary contact; with large $\Psi$, some goals are never recognizable (Ex. 11.16).
- Stage indexing: $y_k$ is observed *at* stage $k$, $u_k$ moves to $k+1$; the history holds $u_1..u_{k-1}$ but $y_1..y_k$. Thrun applies $u_t$ then senses $z_t$ in the same step.

---

## Ch. 12 Planning Under Sensing Uncertainty (PDF 325–363, book pp. 633–710) — Lessons: A6, A7 (localization = filtering in an I-space), A4 (D*, bug algorithms, sensor-based navigation), A3 (bug ↔ potential descent); Robot Learning H-track (POMDP belief planning)
**Big idea:** Ch. 11 defined the information space (I-space); Ch. 12 plans in it. The general recipe is "treat the derived I-space as a big state space with perfect state feedback, then run the Ch. 10 dynamic programming on it." That explodes immediately (nondeterministic I-space of an $n$-state problem has $2^n-1$ states; probabilistic I-space is an $(n-1)$-simplex), so every practical method shrinks the I-space with a problem-specific I-map: odometric coordinates for grid localization, visibility cells for continuous localization, latitude + turn count for maze search, gap trees for navigation without a map, shadow-component labels for pursuit–evasion, preimages for compliant manipulation. Choset and Thrun teach the *filtering* half (passive localization); LaValle adds the *active* half: choosing actions so the I-state shrinks.

### §12.1 General methods (PDF 325–328, book pp. 634–640)
**The I-space as a big state space (Fig. 12.1, book p. 635, PDF 326):** from a Formulation-11.1 problem build a Formulation-10.1 problem with arrowed components: $\vec x=\eta_{der}$ (a set $X_k(\eta_k)$ or a distribution $P(x_k\mid\eta_k)$), $\vec U=U$, **nature action = the next observation** $\vec\theta=y$, $\vec f$ = the derived information transition (filter update), $\vec x_I=\eta_0$.
- Goal (12.1) (book p. 636, PDF 326): $\vec X_G=\{X(\eta)\in\mathcal I_{ndet}\mid X(\eta)\subseteq X_G\}$; probabilistic version: all mass (or ≥ 98%) inside $X_G$.
- Expected stage cost (12.3)–(12.4) (book p. 636): $\vec l(\eta_k,u_k)=\sum_{x_k}P(x_k\mid\eta_k)\,l(x_k,u_k)$. Nondeterministic case keeps stage-additivity only if $l$ is constant over $X_k(\eta_k)$ (12.5)–(12.6) (book pp. 636–637, PDF 326–327). Reason: worst-case per stage can pick a different path each stage.
- No sensors ⇒ the I-state trajectory is predictable ⇒ an open-loop action sequence suffices (book p. 637).

**Nondeterministic I-spaces (book pp. 637–638, PDF 327):** value iteration (12.7) $G^*_k(\vec x_k)=\min_{u}\max_{\vec\theta}\{\vec l+G^*_{k+1}(\vec f(\vec x_k,u,\vec\theta))\}$; the possible observations (12.8)/(12.9) are those produced from the forward projection $X_{k+1}(\eta_k,u_k)$. Backprojection search / nondeterministic Dijkstra (Fig. 10.8) gives worst-case-optimal plans. Practical limit ≈ 20 original states.

**Probabilistic I-spaces = POMDPs (book pp. 638–640, PDF 327–328):** approximate value iteration over sampled beliefs with interpolation (12.10) $G^*_k(\vec x_k)=\min_u\{\vec l+\sum_{\vec x_{k+1}}G^*_{k+1}(\vec x_{k+1})P(\vec x_{k+1}\mid\vec x_k,u)\}$; finitely many observations ⇒ finitely many successor beliefs (Dirac masses). Exact methods: cost-to-go is piecewise linear and convex ⇒ LP (as in Thrun ch. 16 α-vectors, see `probrob-part2.md`); or enumerate all $\eta_F$ for short horizons (exponential in $K$).
**What this adds:** the clean statement "observation = nature action in the belief MDP" — the bridge from the A7 Bayes filter to Robot Learning POMDPs/belief-space RL.

### §12.2 Localization (PDF 328–336, book pp. 640–655) — Lessons: A6, A7
**Definitions (book p. 640, PDF 328):** *passive* localization = compute the I-state from the history (this is all of Choset ch. 8–9 / Thrun ch. 7–8); *active* localization = plan actions that shrink it. *Kidnapped robot* = no initial knowledge (default here).

**§12.2.1 Discrete localization with nondeterministic I-states (book pp. 640–647, PDF 328–332):**
- State $x=(p,d)$, position × heading in {N,E,W,S}; no compass. Actions (12.11) $U=\{F,B,R,L\}$ relative to the unknown heading; blocked move = turn only (Fig. 12.3a). Sensor $y=h(x_{k-1},x_k)\in\{0,1\}$ = "did I move?" ⇒ observations start at $y_2$ (12.12)–(12.13) (book p. 642, PDF 329).
- Ex. 12.1 (book pp. 642–643, PDF 329–330): from $\eta_0=X$ (20 states), actions $(F,L,F,L)$ all succeed; the set shrinks to a singleton after three actions. Ex. 12.2 (book pp. 643–644, PDF 330): a loop map has rotational **symmetry**; the I-state can never get below a symmetry class of ≤ 4 states.
- Active localization = search in $\vec X=\mathcal I_{ndet}$ ($|\vec X|=2^n-1$). *Precomputed plan* (backprojection search from singleton I-states) vs *lazy plan* (on-board search). Two twists vs ordinary graph search (book pp. 645–646, PDF 331): **reroute paths** (the robot must physically drive back to a node to expand it) and **information improvement** ($X(\eta_2)\subseteq X(\eta_1)$ when revisiting a position, so the search graph is directed). Odometric coordinates $(i,j)$ relative to the start make revisits recognizable.
- Variants (book pp. 646–647): a compass trivializes Ex. 12.1–12.2 (solved by $(E,N)$); a graph without angular odometry makes all same-degree vertices indistinguishable.

**§12.2.2 Combinatorial continuous localization (book pp. 647–651, PDF 332–334):** polygon $X$, $\dot x=u$, perfect compass + perfect omnidirectional range ("visibility") sensor. Static localization = find all translations that overlay the measured visibility polygon on $\partial X$: $H(y)\subseteq X$. **Visibility cell decomposition** (Fig. 12.4–12.6): extend rays from mutually visible vertex pairs and from reflex vertices; $O(n^2r)$ cells ($r$ reflex vertices); visibility-skeleton query $O(m+\lg n+s)$ after $O(n^5)$-space preprocessing, or $O(mn)$ without (book p. 649, PDF 333). Then overlay the $k$ candidate copies of the map and move so as to cross a cell boundary that separates them (Ex. 12.3, Figs. 12.7–12.10). Guarantee: travel ≤ $k$ × optimal verification distance; *optimal* localization is **NP-hard** (book p. 651, PDF 334).

**§12.2.3 Probabilistic localization (book pp. 651–655, PDF 334–336):** grid version with noisy motion and a noisy "moved?" sensor $y_k=h(x_k,x_{k-1},\psi_k)$ (12.19). Because $h$ depends on two states, the update needs an extra marginalization (12.20)–(12.22) (book pp. 652–653, PDF 334–335):
$$P(y_k\mid\eta_{k-1},u_{k-1},x_k)=\sum_{x_{k-1}}P(y_k\mid x_{k-1},x_k)\,P(x_{k-1}\mid\eta_{k-1},u_{k-1},x_k),$$
with the second factor from Bayes' rule using $P(x_k\mid u_{k-1},x_{k-1})$. Continuous case (book pp. 653–655): $X=SE(2)$, $p(x_{k+1}\mid x_k,u_k)$ and $p(y\mid x)$ = product of i.i.d. Gaussians on $n$ range beams (the beam model without Thrun's short/max/random mixture); KF if linear-Gaussian, EKF = second-moment approximation, particle filter = **Monte Carlo localization** (book p. 655, PDF 336). Active probabilistic localization is left open.
**Pitfall:** the i.i.d.-Gaussian beam product is overconfident on real lidar; use the likelihood field (A7 / Assignment 5) or Thrun's mixture.

### §12.3 Environment uncertainty and mapping (PDF 336–350, book pp. 655–684) — Lessons: A4, A6, A7
**§12.3.1 Grid problems (book pp. 655–662, PDF 336–339):** state $X=\mathbb Z\times\mathbb Z\times D\times E$ (12.23) (book p. 656, PDF 336), $E$ = set of environments (often only defined implicitly). Task types (book pp. 656–657): **map building** (= SLAM on a grid), **determining the environment** (which of $N$ buildings?), **navigation** (goal given in odometric coordinates, no map), **searching** (goal recognized only on arrival). Ex. 12.4 (book pp. 658–659, PDF 337–338) disambiguates six environments by bumping into walls; Ex. 12.5 reaches a goal while learning only what is needed. Environments that differ by an $SE(2)$ transform or by unreachable tiles are indistinguishable and may be declared equivalent.
- Algorithms: small $E$ ⇒ precompute worst-case-optimal plan on $\mathcal I_{ndet}$ (value iteration / nondeterministic Dijkstra); large $E$ ⇒ lazy DFS (few reroutes). A\* with "all unknown tiles free" as heuristic finds the optimal *route* but ignores exploration cost (book p. 660).
- **Blum–Kozen maze search (book pp. 660–662, PDF 338–339):** any bounded grid maze can be searched by a finite automaton plus **one binary counter** ($O(\log n)$ memory, no map). Keep latitude $l(\tilde u'_k)$ = #north − #south moves, and loop orientation from (#right − #left) turns, which is $\pm4$ for a simple loop and can be kept mod 3. Wall-follow with virtual walls dropped south from each obstacle's unique (south-most, then west-most) point; add zig-zags (Fig. 12.16) to cover interior tiles. Compare spanning-tree coverage (§7.6), which needs the map.

**§12.3.2 Stentz's algorithm D\* (book pp. 662–667, PDF 339–342):** a dynamic, *backward* Dijkstra: cost-to-go grows from $x_G$; when execution reveals an edge cost different from the model, affected states re-enter the queue and costs are repaired locally. Keys: $G_{best}(x)$ = lowest cost-to-go while on $Q$ (sort key), $G_{cur}(x)$ = current value; $G_{via}(x,x')=G_{cur}(x')+l(x,u)$ (12.24) (book p. 665, PDF 341).
```
STENTZ / D* — one iteration (Fig. 12.18, book p. 666, PDF 341)
1. x ← pop min G_best from Q
2. if G_best(x) < G_cur(x):            # x was raised while queued
       for u ∈ U(x), x' = f(x,u):
           if G_via(x,x') < G_cur(x) and G_cur(x') ≤ G_best(x):
               G_cur(x) ← G_via(x,x'); π(x) ← u
3. for each predecessor x' with x = f(x',u'):
       if x' unvisited:                        π(x') ← u'; push x' with G_via(x',x)
4.     elif π(x') = u' and G_via(x',x) ≠ G_cur(x'):   push x' with G_via(x',x)
5.     elif π(x') ≠ u' and G_via(x',x) < G_cur(x'):
           if G_cur(x) = G_best(x): π(x') ← u'; push x'
           else: push x with G_cur(x)
6.     elif x' dead and π(x') ≠ u' and G_via(x,x') < G_cur(x) and G_cur(x') > G_best(x):  # see errata
           push x' with G_cur(x')
Execution: follow π; on a cost mismatch update l(x_k,u_k), re-queue x_k if dead,
iterate until G_cur(x_k) ≤ G_best(x) for all x ∈ Q, then continue.
```
I-space reading (book pp. 665–667): state = (configuration, environment = full cost assignment); D\* guesses one environment in the current nondeterministic I-state (optimism: unknown = default cost) and switches guess on each mismatch. Each executed step is optimal given current knowledge. Modern use: D\* Lite (Koenig–Likhachev, not in the book) is simpler and is what ROS-era planners implement.

**§12.3.3 Unknown continuous environments: bug algorithms and competitive ratios (book pp. 667–673, PDF 342–345):** sensors = goal sensor (distance + absolute bearing ⇒ global $(x,y)$) + contact-range wall sensor.
- Bug1 bound (12.25) $d+\frac32\sum_iP_i$; Bug2 (12.26) $d+\frac12\sum_i n_iP_i$ ($n_i$ = crossings of the start–goal line); TangentBug (12.27) $d+\sum_iP_i+\sum_iP_im_i$ ($m_i$ = local minima of obstacle $i$; only obstacles meeting the disc of radius $d$ about the goal count) (book pp. 669–671, PDF 343–344). TangentBug leave rule: $d_r<d_f$ (reachable distance via visible obstacles < best distance seen on the boundary). Same as Choset ch. 2; LaValle's addition is the I-space framing and:
- **Competitive ratio** (12.28) (book p. 672, PDF 344): $\max_{e\in E}\frac{\text{cost without knowing }e}{\text{cost knowing }e}$. Bug algorithms have **unbounded** ratio. **Lost cow** (book pp. 672–673, PDF 344–345): gate at known distance 1 ⇒ ratio 3; unknown distance ≥ 1 ⇒ doubling zig-zag ("spiral") search, ratio **9**, optimal (cf. iterative deepening).

**§12.3.4 Gap navigation trees (book pp. 673–679, PDF 345–348):** a robot with only a **gap sensor** (directions of depth discontinuities; no distances, no compass) in a simply connected region. Actions = "chase gap $g$". Critical events (book pp. 674–675): **appear/disappear** (crossing an inflection ray), **split/merge** (crossing a bitangent ray). Tree update rules: disappear ⇒ delete leaf; merge ⇒ insert intermediate vertex; split ⇒ remove it; appear ⇒ new *primitive* leaf (Figs. 12.30–12.33). Exploration = chase every non-primitive leaf; done when all leaves are primitive. Chasing a vertex follows the **shortest-path roadmap** (bitangent edges), so navigation is distance-optimal with zero metric information. Many environments map to the same GNT (the I-map collapses $E$).

**§12.3.5 Probabilistic localization and mapping (book pp. 679–684, PDF 348–350):** $X=\mathcal C\times E$ (12.29); next-best-view = active version. Passive SLAM via **EM**: parameters = environment $e$, data = history $\eta_K$, hidden = configuration history $\tilde q_K$.
- E-step (12.30) (book p. 683, PDF 350): $Q(e,\hat e_{i-1})=\int p(\eta_K,\tilde q_K\mid e)\,p(\tilde q_K\mid\eta_K,\hat e_{i-1})\,d\tilde q_K$ — computing $p(q_k\mid\eta_K,\hat e)$ needs a forward filter *and* a backward pass (smoothing).
- M-step (12.32): $\hat e_i=\arg\max_e Q(e,\hat e_{i-1})$; generalized EM only needs $Q(\hat e_i,\hat e_{i-1})>Q(\hat e_{i-1},\hat e_{i-1})$. Converges to a local optimum. Representations of $E$: landmarks or occupancy grids. Same EM-mapping as Thrun draft ch. 13; no EKF-SLAM or particle SLAM here.

### §12.4 Visibility-based pursuit–evasion (PDF 350–354, book pp. 684–691) — brief, enrichment for A4
Formulation 12.1 (book p. 684, PDF 350): pursuer with omnidirectional visibility, evader of unbounded speed in a simple polygon; find a pursuer path that guarantees detection. Nondeterministic I-state = (pursuer position, set of possible evader positions) (12.33). Shadow components are labelled cleared/contaminated; only a **merge** can recontaminate. Cells from inflection + bitangent rays are information-conservative ⇒ directed graph $G_I$ (cell × label vector), search to all-cleared (book pp. 687–690, PDF 352–353). Exponential worst case; $O(n^2)$ algorithm via the two-flashlight equivalence; minimum number of pursuers with holes is NP-hard; three pairwise-disjoint $V(V(p_i))$ ⇒ one pursuer is not enough (Ex. 12.7). Same algorithm as Choset §6.3 (see `choset-part2.md`).

### §12.5 Manipulation planning with sensing uncertainty (PDF 354–360, book pp. 691–704) — brief
- **Preimage (LMT) planning** (book pp. 692–699): compliant motion along $\partial\mathcal C_{obs}$ with a Coulomb friction cone, bounded control and sensing error, motion commands $\mu:\mathcal I_{hist}\to\{u,u_T\}$; preimage $P(\mu,G)$ = I-states from which $\mu$ is guaranteed to reach and *recognize* $G$; plan = chain of preimages back to $\eta_0$ (12.34). Strong vs weak backprojections. General preimage planning is uncomputable; 3D version is hard (book p. 696).
- **Sensorless part orienting by squeezing** (Goldberg–Mason, book pp. 701–704, PDF 358–360): diameter function $d(\alpha)$, squeeze function $s(\alpha)$ (12.37), backward greedy interval growth $|S(A_{i-1})|<|A_i|$ (12.39); any polygon is oriented up to symmetry in $O(n)$ squeezes, $O(n^2)$ planning time; Ex. 12.9: a rectangle needs 2 squeezes. Lesson: **less sensing can shrink the I-space**.

**Exercises worth doing (book pp. 706–709, PDF 361–363):** 1 (grid I-states by hand; read "Figure 12.2a"), 3 (simplest localizing plan for Fig. 12.54), **22 / 26** (probabilistic grid localization = histogram filter with a two-state sensor model — A7 warm-up), **24** (search on $\mathcal I_{ndet}$ for active grid localization — A4 + A7 hybrid), **25** (Bug1/Bug2/VisBug), 8 (prove lost-cow ratio 9), 11 (worst case for Bug1), 23 (exact POMDP value iteration — Robot Learning), 19 (I-map of Bug1: is it sufficient?).
**Pitfalls:** "localization" in LaValle includes the *active* problem; ASBR treats only the passive one. A nondeterministic I-state is a **set** (set-membership filter), not a distribution; it never gets worse in §12.2.1 but can get worse in pursuit–evasion (merges). D\* assumes optimistic defaults for unseen cells; the executed path is a sequence of optimal plans, not one optimal plan.

---

## Ch. 13 Differential Models (PDF 366–401, book pp. 715–786) — Lessons: A5 (steering functions for kinodynamic RRT), A6/A7 (motion models $x_{k+1}=f(x_k,u_k)$ for EKF/MCL), A3 (C-space of cars), R7 (Lagrangian dynamics), R1/R2 (rigid-body rotation, $\dot R$), F7 (ODEs)
**Big idea:** the continuous-time state transition equation $\dot x=f(x,u)$ is the analogue of $x_{k+1}=f(x_k,u_k)$. Velocity constraints come in **implicit** form $g(q,\dot q)\bowtie0$ (what is forbidden) or **parametric** form $\dot q=f(q,u)$ (what is allowed); planners need the parametric form. Dynamics are handled by moving to the **phase space** $x=(q,\dot q)$, which turns second-order constraints into first-order ones at the cost of doubling the dimension and introducing **drift** (you cannot stop instantly). Compared with MR ch. 13 (`mr-part3.md`) and Choset ch. 12 (`choset-part3.md`), LaValle adds the implicit→parametric recipe, the car *family* defined by its action set, trailers, the integrator-chaining trick, and a full Newton–Euler/Lagrange/Hamilton tour written for planners.

### §13.1 Velocity constraints on the C-space (PDF 366–376, book pp. 716–735)
**§13.1.1 Implicit vs parametric (book pp. 716–722, PDF 366–369):**
- Implicit (13.1) $g(q,\dot q)\bowtie0$ with $\bowtie\in\{=,<,>,\le,\ge\}$ (book p. 718, PDF 367). Eight planar examples (book pp. 716–717): $\dot x>0$, $\dot x=0$, $a\dot x+b\dot y+c=0$ (cannot stop), $\dot x^2+\dot y^2\le1$ (speed limit only), $\dot x^2+\dot y^2\ge1$. Rule: if every $U(q)$ contains an open set around $0$, feasibility is unaffected.
- Parametric (13.2) $\dot q=f(q,u)$ (book p. 719, PDF 368): fix $q$ ⇒ $f$ parametrizes a subset of $T_q(\mathcal C)$; fix $u$ ⇒ $f$ is a vector field. Always specify $U$. Any model can be rewritten as $\dot q=u$, $u\in U(q)$ (13.4).
- **Pfaffian constraints** (13.5) $g_1(q)\dot q_1+\dots+g_n(q)\dot q_n=0$ (book p. 720, PDF 368). With $k$ independent ones: $n-k$ free actions, set $\dot q_i=u_i$ for $i\le n-k$, solve for the rest (13.6) (book p. 721, PDF 369). Ex. 13.2: $(\cos q_3)\dot q_1-(\sin q_3)\dot q_2-\dot q_3=0$ ⇒ $\dot q_3=u_1\cos q_3-u_2\sin q_3$ (13.9)–(13.10). Nonlinear equalities: implicit function theorem when $\partial(g_1..g_k)/\partial(\dot q_1..\dot q_n)$ has rank $k$ (book p. 722). Practical: compute the null space of the $k\times n$ constraint matrix with an SVD.

**§13.1.2 Wheeled systems (book pp. 722–731, PDF 369–374):** frame origin at the **rear-axle centre** for the car, **axle midpoint** for the differential drive.

| Model | $\dot q=f(q,u)$ | Action set $U$ | Notes |
|---|---|---|---|
| Simple car (13.15), book p. 724, PDF 370 | $\dot x=u_s\cos\theta,\ \dot y=u_s\sin\theta,\ \dot\theta=\frac{u_s}{L}\tan u_\phi$ | $[-1,1]\times(-\phi_{max},\phi_{max})$, book p. 725 | from $-\dot x\sin\theta+\dot y\cos\theta=0$ (13.12) and $\rho=L/\tan\phi$ (13.13)–(13.14); $\rho_{min}=L/\tan\phi_{max}$ |
| Tricycle | same | $[-1,1]\times[-\pi/2,\pi/2]$ | turns in place, $\rho_{min}=0$ |
| Reeds–Shepp car | same | $u_s\in\{-1,0,1\}$, $|u_\phi|\le\phi_{max}$ | STLC; shortest paths in §15.3 |
| Dubins car | same | $u_s\in\{0,1\}$ | no reverse ⇒ not STLC, still controllable in open plane |
| Differential drive (13.16), book p. 726, PDF 371 | $\dot x=\frac r2(u_l+u_r)\cos\theta,\ \dot y=\frac r2(u_l+u_r)\sin\theta,\ \dot\theta=\frac rL(u_r-u_l)$ | wheel rates | $L$ = wheel separation; (13.17) with $u_\omega=\frac{u_r+u_l}2$, $u_\psi=u_r-u_l$ |
| Unicycle (13.18), book p. 729, PDF 373 | $\dot x=u_s\cos\theta,\ \dot y=u_s\sin\theta,\ \dot\theta=u_\omega$ | — | diff-drive and unicycle simulate each other (kinematics only) |
| Car with $k$ trailers (13.19), book p. 731, PDF 374 | $\dot\theta_0=\frac sL\tan\phi$, $\dot\theta_i=\frac s{d_i}\Big(\prod_{j=1}^{i-1}\cos(\theta_{j-1}-\theta_j)\Big)\sin(\theta_{i-1}-\theta_i)$ | — | $\theta_i$ absolute; hitch at rear-axle centre of the body ahead; $\dim\mathcal C=k+3$ |

- Diff-drive shortest path for the axle centre is just the Euclidean segment (spin, drive, spin; Fig. 13.4) — rotation looks free, so use time or wheel rotation as cost (§15.3, Balkcom–Mason).
- **Convention clash:** MR uses wheel separation $2d$ and $(\phi,x,y)$; LaValle uses $L$ = separation and $(x,y,\theta)$; Choset's car typo $L/\tan^{-1}\gamma$ is printed correctly here as $\rho=L/\tan\phi$. LaValle's steering angle $\phi$ is *negative* for the right turn drawn in Fig. 13.1 (book p. 723).
- **§13.1.3 Other examples (book pp. 731–735, PDF 374–376):** stable box pushing ≈ Dubins car with asymmetric steering limits $[a,b]$ (Lynch–Mason); constant-altitude aircraft = Dubins with $s>0$; 3-D aircraft (13.20) $\dot z=u_z$ ⇒ helices; rolling ball (13.21), $\mathcal C=\mathbb R^2\times SO(3)$ with 2 inputs, fully controllable; **trapped on a surface** (13.22) $\dot x=yu,\ \dot y=-xu$ is the time derivative of $x^2+y^2=r^2$ ⇒ *completely integrable* = **holonomic**; otherwise **nonholonomic** (decided by Frobenius, §15.4.2). Closed-chain constraints $h(q)=0$ become Pfaffian by differentiation (13.26), so kinodynamic planners also handle closed chains.

### §13.2 Phase space (PDF 376–381, book pp. 735–745)
- Implicit (13.27) $g(\ddot q,\dot q,q)=0$, parametric (13.28) $\ddot q=f(\dot q,q,u)$ (book p. 736, PDF 376). Reduce order by raising dimension: $x_1=y,\ x_2=\dot y$, e.g. $\ddot y-3\dot y+y=0$ ⇒ (13.32) $\dot x_1=x_2,\ \dot x_2=3x_2-x_1$ (book p. 737, PDF 377). Vector case: $x=(q,\dot q)\in T\mathcal C$, $2n$ equations $\dot x_i=x_{n+i}$, $\dot x_{n+i}=h_i(x,u)$ (book p. 738).
- **Double integrator** (13.35) $\dot x_1=x_2,\ \dot x_2=u$ (Ex. 13.3, book pp. 737–738); **chain of integrators** $q^{(k)}=u$ with $x=(q,\dot q,\dots,q^{(k-1)})$ (13.36) (Ex. 13.4, book p. 739, PDF 378).
- **Drift** (book p. 739): some $x$ has no $u$ with $f(x,u)=0$; kinematic models of §13.1 are **driftless**.
- Linear system (13.37) $\dot x=Ax+Bu$ (book p. 740, PDF 378); allowed velocities at $x$ form an $m$-dim affine set; observability with $y=Cx$ (13.39) mentioned only; time-varying (13.40).
- **Control-affine** (13.41) $\dot x=f(x)+\sum_{i=1}^m g_i(x)u_i$; **driftless** (13.42) $\dot x=\sum g_i(x)u_i$ (book p. 741, PDF 379). **Nonholonomic (Brockett/Heisenberg) integrator** (13.43) $\dot x_1=u_1,\ \dot x_2=u_2,\ \dot x_3=x_1u_2-x_2u_1$ (book p. 742, PDF 379). Pfaffian form on phase space (13.44).
- **Adding integrators (§13.2.4, book pp. 742–744, PDF 379–380):** rename action $u_i$ as a state, add $\dot x_{n+1}=u_i'$; $k$ integrators make the original input $C^{k-1}$ with bounded rate. Models: unicycle with speed state (13.45) $(x,y,\theta,s)$, $\dot s=u_a$; **second-order unicycle** (13.46) $(x,y,\theta,s,\omega)$, $\dot s=u_a,\ \dot\omega=u_\alpha$; **continuous-steering car** (13.47) $\dot x=\cos\theta,\ \dot y=\sin\theta,\ \dot\theta=\frac{\tan\phi}L,\ \dot\phi=u_\omega$; $C^1$ steering (13.48); smooth diff-drive with wheel-rate states (13.49). These are the standard test systems for kinodynamic RRT (A5) and the "constant-acceleration" motion models in KF tracking (A6).

### §13.3 Newton–Euler mechanics (PDF 381–389, book pp. 745–762) — compact; R7, R1/R2
- Particle: $\ddot q=f/m$ (13.50) ⇒ scaled double integrators (13.51)–(13.53), bounded force set (13.54). **Lunar lander** (Ex. 13.7, (13.56)–(13.60), book pp. 748–750, PDF 382–383): $m\ddot q_1=u_lf_l-u_rf_r$, $m\ddot q_2=u_uf_u-mg$, binary thrusters ⇒ $|U|=8$. **Pendulum** (Ex. 13.8, (13.61)–(13.65), book pp. 750–751, PDF 383–384): $\dot x_2=-\frac gL\sin x_1-\frac km x_2+\frac{1}{mL}u_f$.
- Momentum $d=m\dot q$, torque $n=q\times f$, angular momentum $e=q\times d$, $\dot e=n$, $T=\frac12m\dot q\cdot\dot q$ (13.66)–(13.70); systems of particles (13.71)–(13.76).
- Rigid body (book pp. 753–760, PDF 385–388): translating vs body frame; $\dot R=\omega\times R$ (13.84) (spatial $\omega$, i.e. $\dot R=[\omega]R$); inertia (13.90) $I=\int((r\cdot r)I_3-rr^T)\sigma(r)dr$, $E=I\omega$; body-frame Euler equations (13.98) $N=I\dot\omega+\omega\times(I\omega)$, principal-axis form (13.101)–(13.102) $\dot\omega_1=(N_1+(I_{22}-I_{33})\omega_2\omega_3)/I_{11}$ etc.; 12-D phase vector $(p,v,b,c,d,\omega)$ (13.104)–(13.105) — **use the corrected quaternion kinematics (errata)**. Planar rigid body (13.109): $\dot p=v,\ \dot v=F/m,\ \dot\theta=\omega,\ \dot\omega=N/I$.
- **Skidding car** (13.110)–(13.113) (book p. 761, PDF 389): state $(x,y,\theta,\omega,v)$, linear tyre model with cornering stiffnesses $c_f,c_r$, parameters $m=1460$ kg, $c_f=17000$, $c_r=20000$, $a=1.2$, $b=1.5$ m, $I=2170$ kg·m², $s=27$ m/s (book p. 762). **Tyre-force signs need correcting (errata).**

### §13.4 Lagrangian and Hamiltonian mechanics (PDF 389–398, book pp. 762–780) — R7
- Calculus of variations: functional (13.114), **Euler–Lagrange** (13.118) $\frac{d}{dt}\frac{\partial L}{\partial\dot x}-\frac{\partial L}{\partial x}=0$ (book p. 765, PDF 391); $L=K-V$ (13.129), $K=\frac12\dot q^TM(q)\dot q$ (13.126); with generalized forces (13.136) $\frac{d}{dt}\frac{\partial L}{\partial\dot q}-\frac{\partial L}{\partial q}=u$ (book p. 768, PDF 392). Five-step recipe (book pp. 768–769).
- General form (13.140)–(13.147) (book p. 770, PDF 393): $\sum_jm_{ij}\ddot q_j+\sum_{j,k}h_{ijk}\dot q_j\dot q_k+g_i=u_i$; matrix form (13.142) $M(q)\ddot q+C(q,\dot q)\dot q+g(q)=u$, Christoffel symbols $c_{ijk}=\frac12\big(\frac{\partial m_{ij}}{\partial q_k}+\frac{\partial m_{ik}}{\partial q_j}-\frac{\partial m_{jk}}{\partial q_i}\big)$, $c_{ij}=\sum_kc_{ijk}\dot q_k$ (same as MLS/MR); phase form (13.148) $\ddot q=M^{-1}(u-C\dot q-g)$ (book p. 771, PDF 394).
- Ex. 13.13 two-link manipulator (book pp. 771–773, PDF 394–395): $m_{11},m_{12},m_{22}$ in (13.156) are correct; **most of the remaining lines of the example contain errors (see errata) — study MLS ch. 4 §2 / MR ch. 8 instead.**
- Constraints (§13.4.3, book pp. 774–777, PDF 395–397): $L_c=L+\sum\lambda_ig_i$ (13.166); Pfaffian case (13.172); **$M\ddot q+C\dot q+g+G^T\lambda=u$** (13.179) with $\lambda=(GM^{-1}G^T)^{-1}GM^{-1}(u-C\dot q)$ as printed (13.180); differentiating $G\dot q=0$ gives the general form $\lambda=(GM^{-1}G^T)^{-1}\big(GM^{-1}(u-C\dot q-g)+\dot G\dot q\big)$ (see errata). Ex. 13.14: particle on a sphere ⇒ great circles. Friction as generalized force (13.181)–(13.184).
- Hamiltonian (§13.4.4, book pp. 778–780, PDF 397–398): Legendre transform, $p_i=\partial L/\partial\dot q_i$ (13.191), $H=p\cdot\dot q-L$ (13.192); Hamilton's equations (13.198) — **sign error, see errata**. Why it matters here: it is the template for Pontryagin's minimum principle (§15.2.3).

### §13.5 Multiple decision makers (PDF 398–400, book pp. 780–783) — brief
Nature in continuous time: $\dot x=f(x,u,\theta)$ (13.199) = **differential inclusion** (13.200) in the nondeterministic case, SDE in the probabilistic case. Ex. 13.16: simple car with steering error $\gamma$, $\dot\theta=\frac{u_s}L\tan(u_\phi+\gamma)$ (13.202) — the continuous-time origin of the noisy motion models used in A6/A7. Differential games: linear (13.204), **homicidal chauffeur** (Dubins pursuer vs agile evader, (13.205)), two cars (13.207).

**Exercises worth doing (book pp. 784–786, PDF 400–401):** 1 (Pfaffian → parametric, two constraints in $\mathbb R^4$), 2–3 (phase-space conversions), **4** (derive the trailer equations), **5** (smooth-steering trailer model), **6** (two diff-drives joined by a bar: find the true DOF), **10** (pendulum by constrained Lagrangian — R7), **11** (double pendulum — R7), **12 / 14 / 17** (three-link extension, phase form, simulator for the two-link arm — R7; build from corrected equations and check energy conservation with $u=0$), 13 (sphere geodesics two ways), 15 (Hamiltonian of a planar rigid body), 16 (spacecraft simulator — R1/R2 quaternion integration).
**Pitfalls:** (i) model ≠ action set: Dubins and Reeds–Shepp share (13.15) and differ only in $U$; (ii) the reference point matters (rear-axle centre vs CG vs axle midpoint) — EKF motion models in ASBR use the unicycle at the axle midpoint; (iii) kinematic models are only valid at low speed; add integrators or use the skidding model at speed; (iv) $\theta$ lives on $S^1$ — wrap it; (v) $\omega$ in Euler's equations is body-frame, in (13.84) it is spatial.

---

## Ch. 14 Sampling-Based Planning Under Differential Constraints (PDF pp. 402–438, book pp. 787–860) — Lessons: A5 (primary: kinodynamic RRT, steering/BVP, metrics), A4 (lattice and cell-based search, DP), A3 (Lyapunov/navigation-function link), R7 (path-constrained arm dynamics), H-track (motion primitives, trajectory optimization)
**Big idea:** Part II planned paths in $\mathcal C_{free}$; here a solution is an **action trajectory** $\tilde u$ that is integrated through $\dot x=f(x,u)$, so planners sample the space of action trajectories, not $X$. The new pain points are (i) the **two-point boundary value problem (BVP)**: exactly connecting two given states is hard, and (ii) **drift**: with momentum, states near obstacles may already be doomed. Everything in Ch. 5 (PRM, RRT, grids) carries over once you have a **system simulator** and a **local planning method (LPM)**; whether you also have a cheap BVP solver decides which planners are practical.

### §14.1 Introduction (PDF 402–407, book pp. 788–797)
- **State trajectory from actions (14.1) (book p. 789, PDF 403):** $x(t)=x(0)+\int_0^t f(x(t'),u(t'))\,dt'$. $\tilde x(x_0,\tilde u)$ denotes the whole trajectory.
- **Formulation 14.1 (book pp. 789–790, PDF 403):** world, robot, $\mathcal C$ as in Formulation 4.1; time $T=[0,\infty)$; state space $X$ ($=\mathcal C$ or a phase space) with $q=\kappa(x)$; $X_{obs}$; bounded $U(x)\subseteq\mathbb R^m\cup\{u_T\}$ with **termination action** $u_T$ ($f(x,u_T)=0$, no further cost); system $\dot x=f(x,u)$; $x_I$, $X_G$. Solution: $\tilde u$ with $x(0)=x_I$ and some $t$ where $u(t)=u_T$ and $x(t)\in X_G$. Optimality uses cost functional (8.39). Already PSPACE-hard (special case: Piano Mover's); exact methods exist only for the double integrator in $\mathbb R$ and $\mathbb R^2$ (book p. 790).
- **Vocabulary (book pp. 791–794, PDF 403–405):** *nonholonomic planning* = nonintegrable velocity constraints (LaValle allows them on $\mathcal C$ or any phase space); *kinodynamic* = velocity + acceleration bounds, i.e. second-order constraints (not necessarily nonholonomic); *trajectory planning* = path + velocity for an arm, usually by **time scaling** a path; *drift* = cannot stop instantly; *underactuated* = fewer actuators than $\dim\mathcal C$; **symmetric** system = any motion can be reversed (Reeds–Shepp yes, Dubins no). Control-theory "motion planning" without obstacles = the **steering problem** (§15.5).
- **Phase-space obstacles (14.2) (book p. 795, PDF 406):** $X_{obs}=\{x\mid\kappa(x)\in\mathcal C_{obs}\}$ (cylinder over $\mathcal C_{obs}$, Fig. 14.1), plus phase constraints $h_i(x)\le 0$ (speed limits, rollover). "Violation-free" = collision-free and all $h_i\le 0$.
- **Region of inevitable collision (14.3) (book p. 796, PDF 406):** $X_{ric}=\{x(0)\mid\forall\tilde u\in\mathcal U_\infty,\ \exists t>0:\ x(t)\in X_{obs}\}$ ($\mathcal U_\infty$ excludes $u_T$). For driftless systems $X_{ric}=X_{obs}$. **Ex. 14.1 (book pp. 796–797, PDF 406–407):** double integrator, $|u|\le 1$: stopping distance from speed $v$ is $v^2/2$, so $X_{ric}$ grows quadratically with speed, on the side you are moving toward. Planners never compute $X_{ric}$ (no known "collision detector" for it).

### §14.2 Reachability and completeness (PDF 407–413, book pp. 798–810)
- **Reachable set (14.4) (book p. 798, PDF 407):** $R(x_0,\mathcal U)=\{x_1\mid\exists\tilde u,\exists t:\ x(t)=x_1\}$; time-limited $R(x_0,\mathcal U,t)$ (14.5) (book p. 799, PDF 408); backward versions $B(x_f,\mathcal U)$, $B(x_f,\mathcal U,t)$ (14.6–14.7) (book p. 801, PDF 409). Complete algorithm: report a solution iff $x_G\in R(x_I,\mathcal U_{free})$. Ex. 14.2: $au_1+bu_2=0$ gives a **foliation** of parallel lines (leaves). **Fig. 14.4 (book p. 800, PDF 408):** Dubins small-time reachable set is a forward "fan"; the Reeds–Shepp one is *not* just the mirrored fan: it contains an open ball around $q$ (STLC).
- **Discrete-time model (book pp. 801–802, PDF 409):** fix $\Delta t$, finite $U_d\subset U$, hold $u$ constant per interval; $\mathcal U_d$ = all such action sequences. **Reachability tree** $T_r(x_0,\mathcal U_d)$ and **reachability graph** $\mathcal G_r$ (duplicates merged), swath $S(T_r)=\bigcup_e\bigcup_t x_e(t)$ (14.8) (book p. 803, PDF 410). Ex. 14.4: Dubins with 3 actions gives $3^k$ new leaves at stage $k$.
- **Resolution completeness (book pp. 805–808, PDF 411–412):** for $\dot x=u$ with Manhattan $U_d$ (14.9) the reachability graph is a grid; halve $\Delta t$ on failure. For general $f$ the graph can have countably many vertices even on a bounded $X$ (Dubins circle with irrational circumference/$\Delta t$ ratio is dense). Require **Lipschitz** $f$: $\|f(x,u)-f(x',u')\|\le c(\|x-x'\|+\|u-u'\|)$ (14.10–14.11) (book p. 806, PDF 411); then as $\Delta t\to 0$ and dispersion of $U_d\to 0$ the graph becomes dense in $R(x_I,\mathcal U)$. Search infinitely many graphs by **triangular (dovetail) enumeration**, Fig. 14.7 (book p. 807, PDF 412).
- **Motion primitives (book pp. 808–809, PDF 412–413):** $x_{k+1}=f_d(x_k,u_k)$ (14.12) generalizes to $x_{k+1}=f_p(x_k,\tilde u^p_k)$ (14.13) with primitives of varying duration; **maneuver automaton** (Frazzoli; trim trajectories + maneuvers as a hybrid system, Fig. 14.8). Better performance, harder completeness proofs.

### §14.3 Sampling-based planning revisited (PDF 413–418, book pp. 810–820)
- **Metrics on $X$ (book pp. 810–811, PDF 413–414):** Cartesian-product weighted metrics are arbitrary once velocities enter; the **ideal distance is the optimal cost-to-go** (sub-Riemannian / nonholonomic metric; not symmetric). Practical: underestimate it by dropping obstacles or dynamics, e.g. **Dubins/Reeds–Shepp path length** as the metric for cars; also usable as an A* heuristic.
- **Collision checking (book p. 812, PDF 414):** check $\kappa(x)\in\mathcal C_{free}$ and every $h_i(x)\le 0$; pointwise checks along a trajectory are "efficient but technically incorrect".
- **System simulator (book pp. 813–816, PDF 415–416):** black box $(x(0),\tilde u_t,t)\mapsto\tilde x_t$ (Fig. 14.9). Closed form for linear systems (matrix exponential). **Euler** (14.16): $x(\Delta t)\approx x(0)+\Delta t\,f(x(0),u(0))$. **RK4** (14.18–14.19) (book p. 815, PDF 416):
  $x(\Delta t)\approx x(0)+\tfrac{\Delta t}{6}(w_1+2w_2+2w_3+w_4)$, $w_1=f(x_0,u(0))$, $w_2=f(x_0+\tfrac{\Delta t}{2}w_1,u(\tfrac{\Delta t}{2}))$, $w_3=f(x_0+\tfrac{\Delta t}{2}w_2,u(\tfrac{\Delta t}{2}))$, $w_4=f(x_0+\Delta t\,w_3,u(\Delta t))$. Adaptive step: scale $\Delta t$ by $(\Delta t/\Delta x)^{1/5}$. Multistep (Adams) if integration dominates. **Reverse-time simulation** (14.20) (book p. 816): integrate $-f$; this is *not* a BVP.
- **Local planning (book pp. 816–817, PDF 416–417):** either (1) design sampling/primitives so the BVP is trivial or have a fast **BVP solver** (Fig. 14.10: steering methods of §15.5, LQR for linear systems, Dubins/RS curves) and then any Ch. 5 planner incl. PRM works; or (2) avoid BVPs: the LPM just returns some primitive that makes progress (incremental planners).
- **General framework (book p. 818, PDF 417):** (1) init graph with $x_I$; (2) **swath-point selection** $x_{cur}\in S(\mathcal G)$; (3) LPM generates a violation-free primitive from $x_{cur}$ to some $x_r$; (4) insert edge (split an edge if $x_{cur}$ is interior); (5) check for solution; (6) repeat.
- **Where the BVP appears (Fig. 14.11, book pp. 818–820, PDF 417–418):** forward single tree into a *large* goal region: none. Point goal: one BVP at the goal. Backward tree: BVP at $x_I$. **Bidirectional: BVP in the middle** where trees meet; fix small gaps by concatenating $\tilde u_1,\tilde u_2$ and re-simulating from $x_I$, accepting if still violation-free and ending in $X_G$.

### §14.4 Incremental sampling and searching methods (PDF 418–427, book pp. 820–837)
- **Double-integrator lattice (book pp. 821–823, PDF 419–420):** $\ddot q=u$, $U_d=\{-1,0,1\}$: $\dot q(t)=\dot q(0)+ut$ (14.21), $q(t)=q(0)+\dot q(0)t+\tfrac12ut^2$ (14.22), and every reachable state is $q_k=q_1+i\tfrac12(\Delta t)^2$, $\dot q_k=\dot q_1+j\Delta t$ (14.23), integers $i,j$: a slanted lattice (Fig. 14.13). BFS gives approximately time-optimal paths; connect $x_I,x_G$ to lattice points inside time-limited reachable cones (Fig. 14.14). $n$ decoupled integrators: lattice in $\mathbb R^{2n}$, $3^n$ edges per vertex. Fully actuated arms: rewrite $\ddot q=h(q,\dot q,u)$ as $\ddot q=u'$, $u'\in U'(q,\dot q)$ (14.24) (book p. 825, PDF 421), grid-sample $U'$ with a safety margin (Fig. 14.15).
- **Approximation guarantee (book pp. 826–827, PDF 421–422):** $\delta(c_0,c_1)(\dot q)=c_0+c_1\|\dot q\|$ (14.25) safety ball; BFS on the lattice finds a $(1-\epsilon)\delta$-safe trajectory with time $\le(1+\epsilon)t_{opt}$, running time pseudopolynomial in $1/\epsilon$ (Canny–Donald–Reif–Xavier). Not necessarily the same homotopy class as the optimum.
- **Cell-based search (Barraquand–Latombe style, book pp. 828–832, PDF 422–424):** partition $X$ into cells (cubes, or Voronoi cells of samples via kd-tree); keep **at most one search vertex per cell**, which prunes the infinite reachability graph.
```
CELL-BASED SEARCH(x_I, x_G)            # Fig. 14.17, book p. 830
Q.insert(x_I); G.init(x_I); mark cell(x_I) visited
while Q ≠ ∅ and cell(x_G) unvisited:
    x_cur ← Q.pop()                    # priority = cost so far (time, #switches, ...)
    for each (ũ_t, x) in reached(x_cur):   # apply each u ∈ U_d for Δt, keep violation-free
        if cell(x) unvisited:
            Q.insert(x); G.add_vertex(x); G.add_edge(ũ_t); mark cell(x) visited
return G
```
  Resolution completeness: on failure shrink $\Delta t$, enlarge $U_d$, or refine cells (all three dispersions $\to 0$). Pitfall (Fig. 14.18, book p. 832): a needed vertex can be pruned because its cell already holds a worse vertex (Dubins U-turn).
- **RDT/RRT with differential constraints (book pp. 832–836, PDF 424–426):**
```
SIMPLE RDT WITH DIFFERENTIAL CONSTRAINTS(x_0)   # Fig. 14.19, book p. 833
G.init(x_0)
for i = 1 to k:
    x_n ← nearest(S(G), α(i))          # nearest point in the swath under distance ρ
    (ũ_p, x_r) ← local_planner(x_n, α(i))
        # default: try every u ∈ U_d for Δt, keep the x_r minimizing ρ(x_r, α(i)),
        # never re-try a primitive already applied from x_n; skip if violation
    G.add_vertex(x_r); G.add_edge(ũ_p)
```
  New vertex is $x_r$, not $\alpha(i)$. Edges are curved, so use the approximate nearest-in-swath (intermediate vertices). With momentum, **don't extend all the way to obstacles** (go about half way), or the tree piles up on $X_{ric}$. Single forward tree with a gentle goal bias avoids BVPs; bidirectional needs a connection tolerance and gap closing (variational methods). Performance degrades as $\rho$ diverges from the optimal cost-to-go $\rho^*$; even crude obstacle-free $\rho^*$ (e.g. value iteration for a helicopter maneuver automaton) helps a lot. Resolution-complete RDT: dense $\alpha$, never repeat a primitive at a vertex, periodically shrink $\Delta t$.
- **Other (book p. 837, PDF 427):** randomized potential fields with primitives (Lyapunov functions as potentials), EST for spacecraft, path-directed subdivision trees, PRM only with an efficient steering method.

### §14.5 Feedback planning under differential constraints (PDF 427–429, book pp. 837–841)
- Feedback plan $\pi:X_{free}\to U$, closed loop $\dot x=f(x,\pi(x))$; cost (14.26) $L=\int_0^{t_F}l(x,u)\,dt+l_F(x(t_F))$ (book p. 838).
- **Value iteration with interpolation (14.27) (book p. 839, PDF 428):** $G^*_k(x_k)=\min_{u_k\in U_d}\{l_d(x_k,u_k)+G^*_{k+1}(x_{k+1})\}$, $l_d=\infty$ if the simulated segment violates constraints; store $G^*$ on samples $P$, interpolate elsewhere (barycentric over simplexes, $O(n\lg n)$). Policy (14.28): $\pi(x)=\arg\min_{u\in U_d}\{l_d(x,u)+G^*(f_d(x,u))\}$. Practical only for $\dim X\lesssim 6$. Dijkstra-like variants via backprojections $B(x)=\{x'\mid\exists u\in U_d: x=f_d(x',u)\}$ (14.29) (book p. 841, PDF 429).

### §14.6 Decoupled approaches (PDF 429–436, book pp. 841–855)
- **Four modules (book pp. 841–842, PDF 429):** (1) collision-free path $\tau$; (2) transform to satisfy velocity constraints; (3) timing function $\sigma$ (time scaling); (4) tracking feedback. Each module can lose completeness.
- **Plan-and-transform (Fig. 14.21, (14.30), book p. 843, PDF 430):** pick $s_1<s_2$, replace $\tau|_{[s_1,s_2]}$ by an LPM path $\gamma$ reparameterized by $(s-s_1)/(s_2-s_1)$; repeat (random or binary subdivision). Ex. 14.5: Dubins bitangent LPM. **Topological property (book p. 845, PDF 431):** $\forall\epsilon\,\exists\delta$: $\rho(q,q')<\delta\Rightarrow$ the LPM path stays within $\epsilon$ of $q$. It guarantees every path with clearance can be converted; Reeds–Shepp (STLC) has such an LPM, Dubins does not. Multi-level variant adds constraints $g_i(q,\dot q)=0$ one at a time (Fig. 14.25).
- **Path-constrained time scaling (book pp. 846–855, PDF 431–436):** $q=\tau(s)$, $\dot q=\tau'\dot s$ (14.33), $\ddot q=\tau''\dot s^2+\tau'\ddot s$ (14.34); each fully actuated row gives $\ddot s=\frac{1}{\tau_i'}h_i'(s,\dot s,u_i)-\frac{\tau_i''}{\tau_i'}\dot s^2$ (14.39); the feasible $\ddot s$ form an interval $U'(s,\dot s)$, so the problem is a **single double integrator** $\ddot s=u'$ in the 2-D phase plane $(s,\dot s)$ with $S_{obs}$ where $U'$ is empty (Fig. 14.27). Arm (14.44) (book p. 851, PDF 434): $M(\tau)(\tau''\dot s^2+\tau'\ddot s)+C(\tau,\tau'\dot s)\tau'\dot s+g(\tau)=u$ (see errata for (14.45)).
  - DP over vertical lines in $s$ (14.46), wavefront from $s=1$ backward (book pp. 852–853, PDF 434–435).
  - **Bang-bang time-optimal (Fig. 14.28, book p. 854, PDF 435; Bobrow / Shin–McKay):**
```
1. From (1,0) integrate backward with s̈ = u'_min until S_obs or ṡ = 0 (fail).
2. (s_cur, ṡ_cur) ← (0,0)
3. Integrate forward with s̈ = u'_max until hitting S_obs (limit curve) or the step-1 curve (done).
4. From the crossing, find the next tangent point on the limit curve φ(s);
   integrate backward with u'_min until it meets the step-3 curve; set s_cur to the tangent point; go to 3.
```
  Time-optimal solutions ride the reachable-set boundary, so they cannot be tracked if errors occur (book p. 853).

### §14.7 Gradient-based trajectory optimization (PDF 436–437, book pp. 855–857)
- Improves a feasible plan (needs an initial guess, cannot change homotopy class). **Single shooting**: parameterize $u(t)$, e.g. $u=w_1+w_2t$ (14.47), perturb along $-\nabla$ cost, re-integrate, enforce $x(t_F)=x_G$ as a constraint. **Multiple shooting** splits into segments with continuity constraints; **transcription/collocation** adds more coupling points and uses sparse NLP. Obstacle penalties cause local minima; in NPC/CAT(0) spaces local optima are global within a homotopy class.

**Exercises worth doing (book pp. 858–860, PDF 437–438):** 1 ($X_{ric}$ for a point mass, A5), 4 (parabolic boundaries of the reachability cones), **8 (Euler vs RK4 on single/double/triple integrators; A6/A7 motion-model integration)**, 11 (time-optimal double-integrator lattice search in $\mathbb R^4$, A4), **13 (value iteration with interpolation for the Dubins car, A4, H-track)**, **17–18 (DP and bang-bang time scaling for the 2-link arm of Ex. 13.13, R7)**, **19 (Fig. 14.7 dovetailing for the Dubins car, A5)**, 20 (value iteration for Reeds–Shepp; compare to Fig. 14.4). Optional: 6 (differential-drive reachability graph), 12 (RDT for a thruster spacecraft, metric choice), 15 (car + trailer parking).

**Pitfalls:**
- Kinodynamic RRT samples $\alpha(i)$ but adds the *reached* state $x_r$; a goal "hit" means within tolerance, and with drift the run time explodes as the tolerance shrinks.
- Bidirectional trees are the default for geometric RRT-Connect but are awkward under differential constraints (BVP in the middle).
- Weighted Euclidean metrics on $(q,\dot q)$ are arbitrary; Dubins distance is asymmetric and discontinuous (§15.3).
- Checking only sampled states along an edge can miss collisions and phase-constraint violations.
- Cell pruning (one vertex per cell) can discard the only feasible branch.

---

## Ch. 15 System Theory and Analytical Techniques (PDF pp. 439–470, book pp. 861–924) — Lessons: A5 (steering methods and Dubins/RS curves as LPMs and metrics), A3 (Lyapunov ↔ navigation functions), A4 (HJB = continuous value iteration), R2 (Lie brackets, Lie algebras, CBHD), R8 (stability, Lyapunov, LQR), H-track (HJB, LQR, Pontryagin)
**Big idea:** No obstacles here; the chapter supplies tools for building better LPMs and metrics for Ch. 14. Stability and controllability say whether and how a goal can be reached; dynamic programming in continuous time becomes the HJB PDE (closed form for LQR) and, along a single trajectory, Pontryagin's principle; optimal car paths (Dubins, Reeds–Shepp, Balkcom–Mason) solve the BVP exactly; Lie brackets decide integrability (Frobenius) and controllability (Chow), and the Lie algebra structure gives steering algorithms.

### §15.1 Stability and controllability (PDF 439–443, book pp. 862–870)
- **Equilibrium** $f(x_G)=0$; **Lyapunov stable** (book p. 863, PDF 440): every neighbourhood $O_1$ of $x_G$ has $O_2$ with $x_I\in O_2\Rightarrow x(t)\in O_1\ \forall t>0$; **asymptotically stable** = Lyapunov stable + convergence; **exponentially stable** $\|x(t)-x_G\|\le me^{-\alpha t}\|x_I-x_G\|$ (15.2) (book p. 864). **Brockett's condition** (book p. 864): driftless systems like the car and unicycle have no continuous time-invariant stabilizing feedback (need time-varying or discontinuous). Domain of attraction, limit cycles (book p. 865, PDF 441).
- **Linear test (book p. 865, PDF 441):** $\dot x=Ax$ asymptotically stable iff all $\mathrm{Re}\,\lambda<0$; Lyapunov stable if $\mathrm{Re}\,\lambda\le 0$ and the imaginary-axis eigenvalues are *distinct* roots (correct, unlike Choset Thm J.2.1).
- **Lyapunov functions (book p. 866, PDF 441):** Lie derivative $\dot\phi(x)=\sum_i\frac{\partial\phi}{\partial x_i}f_i(x)$ (15.3); "hill" $h$ (continuous, increasing, $h(0)=0$); $\phi$ locally positive definite and $\dot\phi\le 0$ ⇒ Lyapunov stable; $-\dot\phi$ also locally p.d. ⇒ asymptotically stable; both globally p.d. (radially unbounded) ⇒ globally asymptotically stable. Ex. 15.1: $\dot x=-x^5$, $\phi=\tfrac12x^2$, $\dot\phi=-x^6$. **Planning link (book p. 867, PDF 442):** optimal cost-to-go is a discrete Lyapunov function; Lyapunov functions are navigation functions that respect the dynamics and make good **RDT distance functions**.
- **Controllability (book p. 867–868, PDF 442):** controllable iff $x_G\in R(x_I,\mathcal U)$ for all pairs, i.e. the obstacle-free BVP always has a solution. **Kalman rank condition (15.4):** $\mathrm{rank}[B\ AB\ \cdots\ A^{n-1}B]=n$ ($n\times nm$). Full rank of the linearization ⇒ controllable from that point; rank deficiency does not rule it out (the car).
- **STLC (book pp. 868–869, PDF 442–443):** $x_I\in\mathrm{int}\,R(x_I,\mathcal U,t')$ for all small $t'$. Why planners care: with STLC, recursive-subdivision plan-and-transform converts any collision-free path with clearance $\epsilon$ into a feasible one. **Accessible:** $\mathrm{int}\,R(x_I,\mathcal U,t)\ne\emptyset$ (Ex. 15.3: $\dot x=yu,\dot y=-xu$ is not).

### §15.2 Continuous-time dynamic programming (PDF 443–448, book pp. 870–879)
- **Discrete form (book p. 871, PDF 444):** $G^*(x)=\min_u\{l(x,u)+G^*(f_d(x,u))\}$ (15.6); along the optimal action $G^*(f_d(x,u^*))-G^*(x)=-l(x,u^*)$ (15.8).
- **HJB (15.14) (book p. 872, PDF 444):** from $l_d\approx l\Delta t$ and $G^*(x')\approx G^*(x)+\nabla G^*\cdot f\,\Delta t$: $\min_{u\in U(x)}\{l(x,u)+\sum_i\frac{\partial G^*}{\partial x_i}f_i(x,u)\}=0$; time-varying (15.16–15.17); HJI min-max for differential games (15.18–15.19) (book p. 873, PDF 445).
- **LQR (book p. 874, PDF 445):** $\dot x=Ax+Bu$, $L=\tfrac12\int_0^\infty(x^TQx+u^TRu)\,dt$ (15.20), $(A,B)$ stabilizable, $(A,Q)$ detectable; ARE $SA+A^TS-SBR^{-1}B^TS+Q=0$ (15.21); $\pi(x)=-R^{-1}B^TSx$ (15.23); $G^*(x)=\tfrac12x^TSx$ (15.24); closed loop $A-BR^{-1}B^TS$ Hurwitz (15.22). LQG mentioned (book p. 875).
- **Pontryagin minimum principle (book pp. 875–879, PDF 446–448):** adjoint $\lambda_i=\partial G^*/\partial x_i$ (15.25), Hamiltonian $H=l(x,u)+\lambda^Tf(x,u)$ (15.26); along the optimum $H=0$ (15.27) (time-invariant, free final time), $u^*=\arg\min_uH$ (15.28), $\dot\lambda=-\partial H/\partial x$ (15.36). Necessary, not sufficient (HJB is sufficient). **Ex. 15.4 (book pp. 877–878, PDF 447):** min-time double integrator, $H=1+\lambda_1x_2+\lambda_2u$, $u^*=-\mathrm{sgn}\lambda_2$, $\lambda_2=c_2-c_1t$ switches at most once ⇒ 4 cases, bang-bang. Singular arcs; Legendre–Clebsch ($\partial^2H/\partial u^2\succ 0$). Time-optimal view: with $\lambda=-\partial G^*/\partial x$ (15.40) maximize $\langle-\nabla G^*,f\rangle$ (15.41), i.e. push into the reachable-set wavefront.

### §15.3 Optimal paths for wheeled vehicles (PDF 448–452, book pp. 880–888)
- Fix $q_I=(0,0,0)$ by symmetry; cost = length traced by the **rear-axle centre** (15.42).
- **Dubins (book pp. 880–883, PDF 448–450):** $\dot x=\cos\theta,\dot y=\sin\theta,\dot\theta=u$, $u\in[-\tan\phi_{max},\tan\phi_{max}]$ (15.43) (with $L=1$, so $\rho_{min}=1/\tan\phi_{max}$; normalized to 1). Primitives $S,L,R$ ($u=0,1,-1$, Fig. 15.3). Six optimal words $\{LRL,RLR,LSL,LSR,RSL,RSR\}$ (15.44); with durations $\{L_\alpha R_\beta L_\gamma,\dots,R_\alpha S_dR_\gamma\}$, $\alpha,\gamma\in[0,2\pi)$, $\beta\in(\pi,2\pi)$, $d\ge0$ (15.45); compressed $\{CCC,CSC\}$ (15.46–15.47). LPM: try all six (tangent circles) or use the cell partition (Fig. 15.5). **Dubins metric** is asymmetric and **discontinuous** (Fig. 15.6) because the Dubins car is not STLC.
- **Reeds–Shepp (book pp. 884–886, PDF 450–451):** $\dot x=u_1\cos\theta,\dot y=u_1\sin\theta,\dot\theta=u_1u_2$, $u_1\in\{-1,1\}$ (15.48). Nine base words with cusps "|" (15.49) incl. $C_{\pi/2}$ and matched $C_\beta$ arcs; parameter ranges Fig. 15.7; six primitives $S^\pm,L^\pm,R^\pm$ (Fig. 15.8; $L^-$ = wheel turned left, driving backward). **48 words** (Fig. 15.10), **46** after Sussmann–Tang removed $L^-R^+L^-$ and $R^-L^+R^-$; at most 5 segments. RS metric is **continuous** (RS car is STLC).
- **Balkcom–Mason (book pp. 886–888, PDF 451–453):** differential drive, wheel speeds in $[-1,1]^2$, cost $\int\sqrt{\dot x^2+\dot y^2}+|\dot\theta|\,dt$ (15.50) (= time); four primitives (forward, backward, spin CCW, spin CW, Fig. 15.11); nine base words (15.51), **40** distinct curves (Fig. 15.13), at most 5 pieces.
- Compared with library notes: MR Thm 13.11–13.13 states the same three families; Choset §12.5.6 lists the RS words. LaValle adds the parameter ranges (Fig. 15.7), the 48→46 reduction, the full word table (Fig. 15.10), and the metric continuity argument.

### §15.4 Nonholonomic system theory (PDF 452–463, book pp. 888–910)
- **Control-affine (15.52) (book p. 890, PDF 453):** $\dot x=h_0(x)+\sum_{i=1}^m h_i(x)u_i$; driftless (15.53) $\dot x=H(x)u$ (15.57), $H=[h_1\cdots h_m]$ (15.56); dual Pfaffian form $G(x)\dot x=0$ (15.59), $k$ constraints ↔ $m=n-k$ inputs. Ex. 15.5 (15.54): unicycle/diff-drive/simple car share $h_1=[\cos\theta,\sin\theta,0]^T$, $h_2=[0,0,1]^T$; only $U$ differs.
- **Completely integrable vs nonholonomic (book p. 893, PDF 455):** integrable = Pfaffian constraints are time derivatives of $f_i(x)=0$ (trapped on leaves of a foliation; Ex. 15.6 spheres). **Distribution** $\triangle=\mathrm{span}\{h_1,\dots,h_m\}$ (15.66), nonsingular if $\dim\triangle(x)=m$ everywhere (book p. 895, PDF 456); Ex. 15.8 singular distribution.
- **Lie bracket (book pp. 897–900, PDF 457–458):** commutator motion $f,g,-f,-g$ for $\Delta t$ each (15.71) gives $x(4\Delta t)=x(0)+\Delta t^2\big(\frac{\partial g}{\partial x}f-\frac{\partial f}{\partial x}g\big)+\dots$ (15.79), so
  $$[f,g]=\frac{\partial g}{\partial x}f-\frac{\partial f}{\partial x}g,\qquad [f,g]_i=\sum_j\Big(f_j\frac{\partial g_i}{\partial x_j}-g_j\frac{\partial f_i}{\partial x_j}\Big)\quad(15.80\text{–}15.81).$$
  Same sign convention as Choset (12.5) and MR (13.28). Ex. 15.9: diff drive $[f,g]=[\sin\theta,-\cos\theta,0]^T$ (sideways, to the right at $\theta=0$). Ex. 15.10: nonholonomic integrator $f=[1,0,-x_2]^T$, $g=[0,1,x_1]^T$ ⇒ $[f,g]=[0,0,2]^T$.
- **Frobenius (book p. 901, PDF 459):** involutive ($[h_i,h_j]\in\triangle$ for all $i<j$) iff completely integrable (smooth, nonsingular). Test: append $[h_i,h_j]$ to $H$; rank $m+1$ for any pair ⇒ nonholonomic. Ex. 15.11: diff drive $\det H'=1$; Ex. 15.13: sphere system has $\det H'=0$ ⇒ integrable. Driftless linear $\dot x=Bu$ is always integrable.
- **Lie algebra and P. Hall basis (book pp. 904–908, PDF 460–462):** axioms bilinearity, skew symmetry, Jacobi (15.95–15.97); cross product and $[A,B]=AB-BA$ on $GL(n)$ (left-invariant fields, Ex. 15.15) as examples. $L(\triangle)$ = span of all nested brackets; Ex. 15.17 chained form in $\mathbb R^5$ reaches dimension 5 with brackets of depth up to 4. **P. Hall basis:** BFS by bracket order $d([\phi_1,\phi_2])=d(\phi_1)+d(\phi_2)$ (15.108) with skew/Jacobi pruning (Ex. 15.18). **Nilpotent of order $k$** if all brackets of depth $>k$ vanish.
- **Chow–Rashevskii (book p. 908, PDF 462):** driftless, $U$ contains a neighbourhood of 0: STLC at $x$ iff $\dim L_x(\triangle)=n$. Classification (15.110): $\dim L=m$ integrable; $m<\dim L<n$ nonholonomic, not STLC; $=n$ nonholonomic and STLC. **Drift (book pp. 909–910, PDF 463):** full-rank Lie algebra only gives accessibility; Ex. 15.20 $\dot x_1=u,\dot x_2=x_1^2$ is accessible but not STLC ("bad bracket" $[h_1,[h_0,h_1]]=[0,-2]^T$); Sussmann good/bad brackets (details in choset-part3 Thm 12.3.3).

### §15.5 Steering methods (PDF 463–469, book pp. 910–922)
- **P. Hall / Lafferriere–Sussmann (book pp. 910–916, PDF 463–466):** extended system $\dot x=\sum_{i=1}^s b_i(x)v_i$ (15.114) with fictitious inputs on bracket fields; phase 1: pick any path $\tau$ and solve $\dot\tau=\sum b_iv_i$; phase 2: convert $\tilde v$ to real $\tilde u$ via the formal exponential (15.116–15.117), CBHD formula (15.125), Chen–Fliess series $S=e^{z_sB_s}\cdots e^{z_1B_1}$ (15.127), CFS equation $\dot z=Q(z)v$ (15.132). Ex. 15.23 nonholonomic integrator: $\dot z_1=v_1$, $\dot z_2=v_2$, $\dot z_3=v_3+z_1v_2$ (15.143). Exact for nilpotent systems, iterative otherwise.
- **Sinusoids (book pp. 917–920, PDF 467–468):** nonholonomic integrator $\dot x_3=x_1u_2-x_2u_1$ (15.146), cost $\int u_1^2+u_2^2$ (15.147); Euler–Lagrange with multiplier gives $u$ rotating at constant rate $\lambda$ (15.151), $\lambda=2k\pi$ to close $x_1,x_2$; $x_3(1)=(u_1(0)^2+u_2(0)^2)/\lambda$ (15.153). Two-phase steering: fix $x_1,x_2$, then a closed loop to fix $x_3$. First-order controllable systems (15.155–15.158): sums of sinusoids at $2\pi k$; second-order (15.159) three phases. **Chained form** (15.160): steer $x_1,x_2$, then for $k=3..n$ one sinusoid pair per state (corrected inputs in errata) with $\Delta x_k=(a/4\pi)^{k-2}b/(k-2)!$ (15.162). Goursat normal form = Pfaffian version.
- **Others (book pp. 921–922, PDF 469):** differential flatness (15.163–15.165; car with trailers: last trailer position), decoupling vector fields / kinematic controllability (lifts C-space steering to dynamics; used as an RRT LPM), averaging, variational (Newton) methods, Pontryagin, numerical DP (works on $SE(2)$/$SO(3)$ using symmetry).

**What LaValle adds over choset-part3 (Ch. 12) and mr-part3 (Ch. 13):** HJB derived as the limit of value iteration and LQR from it; Pontryagin with the double-integrator bang-bang example; Lyapunov functions as planning metrics; Dubins/RS parameter ranges and metric (dis)continuity; P. Hall basis + Chen–Fliess–Sussmann steering (Choset covers only sinusoids, flatness, gradient methods; MR none); and the formal Ch. 14 framework explaining *why* these LPMs matter (BVP placement, topological property).

**Exercises worth doing (book pp. 923–924, PDF 470):** **1** (Lyapunov with $\phi=x_1^2+x_2^2$ for $\dot x_1=x_2,\dot x_2=-x_2^2-x_1$: $\dot\phi=-2x_2^3$ is not sign-definite, so this $\phi$ is inconclusive; good trap, R8), **2–4** (Pontryagin on the double/triple integrator, switching curves, H-track/R8), 5 (why some Dubins words are not RS-optimal), **6 (derive the Lie bracket by Taylor expansion, R2)**, **7, 9 (nonholonomic? STLC? by bracket rank, R2/A5)**, 8 (linear systems cannot be nonholonomic), 11 (one-input systems), 14 (CFS equation), **16 (Dubins LPM implementation, A5)**, **18 (Dubins curves as RRT LPM and metric, A5)**, 15 (symbolic P. Hall basis; in Python use a dict of bracket trees).

**Pitfalls:**
- Lie bracket sign conventions differ between books; LaValle, Choset and MR agree on $[f,g]=\frac{\partial g}{\partial x}f-\frac{\partial f}{\partial x}g$, but MLS's matrix-group bracket $[\hat\xi_1,\hat\xi_2]$ for left- vs right-invariant fields can flip sign.
- Turning radius: LaValle's simple car uses the rear-axle centre, $\rho_{min}=L/\tan\phi_{max}$ (book p. 725, PDF 371), and normalizes $u\in[-1,1]$ for Dubins/RS. MR uses $\dot\phi=v\tan\psi/\ell$; Choset (12.34) uses steering angle $x_3$ with $\frac1L\tan x_3$.
- LQR's $Q,R$ are cost weights, not the KF's noise covariances; $S$ here is the Riccati solution (often $P$).
- The min-time Hamiltonian is "maximized" in Pontryagin's original sign convention.
- Chow needs driftless systems; for drift, full Lie rank gives only accessibility.

---

## Notation: LaValle vs Thrun, Choset and the ASBR slides

| Thing | LaValle (Ch. 8–15) | Thrun (draft) | Choset | ASBR slides |
|---|---|---|---|---|
| State | $x\in X$; $q$ only for a configuration, $q=\kappa(x)$; in Ch. 8, $X=\mathcal C_{free}$ | $x_t$ | $x(k)$; $q$ for configurations | $x$; $q$ for configurations |
| Action | $u\in U(x)$, plus termination $u_T$; in Formulation 8.2 $u$ **is a velocity** ($\dot x=u$) | $u_t$ (control or odometry) | $u(k)$ | $u$ |
| Transition | $x_{k+1}=f(x_k,u_k,\theta_k)$; set-valued $F(x,u)$; $P(x'\mid x,u)$; continuous $\dot x=f(x,u)$; $f_d$ discrete-time; $f_p$ primitives | $p(x_t\mid u_t,x_{t-1})$, $g(u,x)$ | $f$, Jacobian $F$ | $f$, Jacobian $F$ |
| Process noise | **nature action** $\theta_k$, covariance $\Sigma_\theta$, entering through $G_k$ | $\epsilon_t\sim\mathcal N(0,R_t)$ | $v$, covariance $V$ | $Q$ |
| Observation | $y\in Y$, sensor mapping $y=h(x,\psi)$; preimage $H(y)$ | $z_t$ | $y(k)$ | $z$ |
| Measurement noise | **nature sensing action** $\psi_k$, $\Sigma_\psi$, entering through $H_k$ | $\delta_t\sim\mathcal N(0,Q_t)$ | $w$, covariance $W$ | $R$ |
| Measurement matrix | $C_k$ | $C_t$ ($H_t$ in the EKF) | $H$ | $H$ |
| Kalman gain | $L_k$ | $K_t$ | **$R$** | $K$ |
| Belief | probabilistic I-state $P(x_k\mid\eta_k)$; nondeterministic I-state $X_k(\eta_k)$ (a **set**) | $bel(x_t)$, $\overline{bel}(x_t)$ | $P(x(k)\mid\cdot)$ | $bel$; prime = prior |
| Information | history I-state $\eta_k=(\eta_0,\tilde u_{k-1},\tilde y_k)$; I-map $\kappa$ | — (its $\eta$ = normalizer) | — | — |
| Value | cost-to-go $G^*$ (minimize), $J^*$ discounted, $Q^*(x,u)$; discount $\alpha$, step size $\rho$ | payoff $C(s)$ (maximize), $\gamma$ | — | — |
| Potential | navigation function $\phi$ (weaker definition than Rimon–Koditschek, see §8.4) | — | $\varphi$ (navigation fn.), $U$ (potentials) | $U_{att}$, $U_{rep}$ |
| Car | steering $\phi$, wheelbase $L$, $\rho_{min}=L/\tan\phi_{max}$; reference point = rear-axle centre | — | steering $\gamma$ or $x_3$ | — |
| Diff drive | $L$ = full wheel separation, wheel radius $r$ | — | $L$ | — |
| Vector fields | control-affine $\dot x=h_0+\sum h_iu_i$ ($g$ is kept for Pfaffian rows $G(x)\dot x=0$) | — | $g_0$, $g_i$ | — |
| Lie bracket | $[f,g]=\frac{\partial g}{\partial x}f-\frac{\partial f}{\partial x}g$ | — | same (12.5) | — |
| Stage indexing | $u_k$ moves $k\to k+1$; $y_k$ is observed at stage $k$; $F=K+1$ | $u_t$ applied, then $z_t$ sensed | $u(k-1)$ takes $x(k-1)\to x(k)$ | $t$ |

**Letter collisions to watch:** $H$ = sensor-noise matrix (§11.6), preimage $H(y)$, Hessian (§8.4.4), Hamiltonian (§13.4, §15.2) and $H(x)$ = matrix of vector fields (§15.4). $L$ = Kalman gain, wheelbase, cost functional and Lagrangian. $\alpha$ = discount, dense sample sequence $\alpha(i)$ and interpolation weight. $T(u,\theta)$ = regret. $S$ = Riccati matrix in §15.2, swath $S(\mathcal G)$ in Ch. 14. LQR's $Q,R$ are cost weights, not noise covariances. MR's diff drive uses $2d$ for the wheel separation and the order $(\phi,x,y)$.

---

## Gaps this fills in the knowledge base
Items missing from `courses/05-algorithms-sensor-based-robotics/README.md` and from the existing library notes (`choset-part*.md`, `probrob-part*.md`, `mr-part3.md`, `asbr-slides-part*.md`), by lesson code.

**A3 (C-space, potentials):**
- A **feedback plan** $\pi:X\to U$ and a navigation function are interchangeable. The optimal navigation function is the cost-to-go $G^*$, computed by **Dijkstra run backward from the goal**; greedy descent $\pi(x)=\arg\min_u\{l(x,u)+\phi(f(x,u))\}$ is the plan (8.4)–(8.6) (book pp. 375–377, PDF 196–197).
- LaValle's "navigation function" (no local minima only) vs Rimon–Koditschek's ($C^2$, Morse, maximal on the boundary). On a non-contractible $\mathcal C_{free}$, every smooth navigation function has extra critical points, so only a measure-zero set of bad starts can be avoided (book pp. 410–411, PDF 213–214).
- **NF2 maximum-clearance** navigation function (brushfire skeleton + two wavefronts), **Dial's** bucket queue, and wavefront = unit-cost Dijkstra in $O(n)$ (book pp. 378–380, PDF 197–198).
- **Harmonic potentials** $\nabla^2\phi=0$ have no spurious interior minima (book pp. 411–412, PDF 214). The tracking law $u=K(f(x)-\dot x)+\nabla_{\dot x}f(x)$ (8.47) runs a navigation field on an acceleration-controlled robot.
- **Lyapunov functions are navigation functions that respect the dynamics**, and they make good RRT metrics (book p. 867, PDF 442).

**A4 (graph planning):**
- **Value iteration with interpolation** on a continuous C-space (Euclidean-like, not staircase, optimal paths), its Dijkstra-like (fast-marching-style) version, and the rule that $\Delta t$ must move the state past its interpolation neighbours (book pp. 418–429, PDF 217–223).
- **Stochastic Dijkstra:** weak and strong backprojections, the backprojection algorithm, **nondeterministic Dijkstra**, and the strict-decrease condition under which probabilistic Dijkstra is valid (book pp. 501–505, 518–521, PDF 259–261, 267–269).
- The **D\* (Stentz) algorithm itself** (Fig. 12.18): $G_{best}/G_{cur}$, optimistic unknown costs, local repair (book pp. 662–667, PDF 339–342). The README names D\* but gives no algorithm.
- **TangentBug bound** (12.27), **competitive ratio**, and the lost-cow ratio 9 (book pp. 669–673, PDF 343–345).
- **Lattice and cell-based search** for dynamical systems (Barraquand–Latombe style), the ancestor of state lattices and hybrid A\* (book pp. 820–832, PDF 418–424).

**A5 (sampling planners):**
- **Kinodynamic RRT/RDT** (Fig. 14.19): find the nearest point in the swath under a dynamics-aware $\rho$, try each $u\in U_d$ for $\Delta t$, and add the *reached* state (book p. 833, PDF 425). The README's RRT is geometric only.
- **The steering problem (two-point BVP)** and where it appears: none for a forward tree into a goal region; at the goal for a point goal; in the middle for bidirectional trees; on every PRM edge (Fig. 14.11, book pp. 818–820, PDF 417–418).
- **Dubins (6 words, CSC/CCC) and Reeds–Shepp (48 words, 46 after Sussmann–Tang)** as exact local planners and metrics. The Dubins metric is asymmetric and discontinuous; the Reeds–Shepp metric is continuous because that car is STLC (book pp. 880–886, PDF 448–451).
- **Region of inevitable collision** $X_{ric}$; stopping distance $v^2/2a_{max}$; with momentum, do not extend RRT edges all the way to obstacles (book pp. 796–797, 835, PDF 406–407, 425).
- **Car family defined by its action set** (tricycle / simple / Reeds–Shepp / Dubins), plus differential drive, unicycle, trailers, continuous-steering car and second-order unicycle as test systems (book pp. 722–744, PDF 369–380).
- **Resolution completeness under differential constraints:** Lipschitz $f$, $\Delta t\to0$, the dispersion of $U_d\to0$, dovetailing (book pp. 805–808, PDF 411–412).
- **Plan-and-transform** works only for STLC systems (the topological property). **Path-constrained time scaling** in the $(s,\dot s)$ phase plane with bang-bang/Bobrow switching is what MoveIt-style time parameterization does (book pp. 841–855, PDF 429–436).
- **Bracket-rank tests:** Frobenius (nonholonomic?) and Chow–Rashevskii (STLC?). For the diff drive this is a 3-line computation (book pp. 897–908, PDF 457–462).
- A ball of collision-free configurations from the clearance $d$ for a planar rigid body: $(\Delta x)^2+(\Delta y)^2+(r\Delta\theta)^2<d^2$ (8.53). This gives conservative edge checking (book p. 417, PDF 217).

**A6 (KF, EKF):**
- The KF is **exact** because a linear-Gaussian model keeps the I-state Gaussian (the moment I-map is sufficient). The EKF is the same moment approximation applied to a nonlinear model, with no general justification. LQG certainty equivalence still needs $\Sigma$ to compute $\mu$ (book pp. 595–597, 615–617, PDF 306–307, 316–317).
- **Euler vs RK4** for integrating motion models (14.16)–(14.19) (book pp. 813–816, PDF 415–416). The README's EKF unicycle model is the Euler step of (13.18).
- **Kalman rank test** $\mathrm{rank}[B\ AB\cdots A^{n-1}B]=n$ (book p. 868, PDF 442), the dual of the observability test in Choset Thm 8.2.2.
- Ex. 9.12 / Exercise 9.16: Gaussian prior × Gaussian likelihood with squared loss = posterior mean = a static-state KF update (book pp. 458–459, 494, PDF 237–238, 255).

**A7 (Bayes filter, PF, MCL):**
- **Information-space frame:** history I-state $\eta_k$; the sufficient I-map $\kappa_{prob}$ *is* the Bayes filter (predict = marginalization, update = Bayes rule). The grid filter and PF are ways to compute it (book pp. 566–580, 592, 617–619, PDF 291–298, 304, 317–318).
- **Set-membership (nondeterministic) filtering:** predict = union of $F(x,u)$, update = $\cap H(y)$ (11.29)–(11.31), with conservative ball/ellipsoid bounds (11.60). No other library note covers it.
- **Active vs passive localization:** ASBR covers only passive localization. Grid localization with a "did I move?" sensor needs an extra marginalization because $h$ depends on two states (12.20)–(12.22) (book pp. 640–655, PDF 328–336).
- **Sensor catalog:** bearing-only (homing), range-only ("Geiger counter"), gap sensor, and odometry as a history-based sensor (book pp. 598–605, PDF 307–311).
- **Goal recognizability and information without sensors:** Ex. 11.16, 11.17, 11.24 (tray tilting), and sensorless part orienting (§12.5.2).
- **Single-step decision rules:** minimax vs Bayes risk vs regret; the set-intersection update (9.30) vs the product of likelihoods (9.31) (book pp. 448–454, PDF 232–235).

**A8 (exam prep):** "planning = feedback on the I-space; filtering = computing the I-state" is the single picture that ties the course's two halves together (Ch. 11 intro, §11.1.3, §12.1.1). Good paper drills: Exercises 9.2/9.4, 9.7, 9.16, 11.1, 11.2 and 11.5 (after fixing its matrices, see Errata).

**Robot Learning (H-track):**
- Value iteration, policy iteration, TD($\lambda$) and Q-learning in **cost-minimizing** form (Ch. 10), with the asymptotic-convergence and Dijkstra-validity caveats.
- POMDP = MDP on beliefs with "observation = nature action" (§12.1.1, §12.1.3).
- Fitted value iteration (§8.5.2); HJB → LQR and Pontryagin (§15.2); motion primitives / maneuver automata (§14.2.3); single and multiple shooting and collocation (§14.7).

**RDKDC:**
- **R7:** Euler–Lagrange recipe, $M\ddot q+C\dot q+g=u$ with Christoffel symbols (13.142)–(13.147), and constrained dynamics $M\ddot q+C\dot q+g+G^T\lambda=u$ (13.179). The Ex. 13.13 two-link arm is full of errors: use MLS/MR for it. Also path-constrained time scaling (14.44).
- **R1/R2:** $\dot R=[\omega]R$; Euler's equations; quaternion kinematics (use the corrected form in Errata); Lie brackets, the CBHD formula and the P. Hall basis.
- **R8:** Lyapunov stability, including the correct LTI test with distinct imaginary-axis eigenvalues; Kalman rank; LQR.

---

## Corrections & upgrades for the knowledge base
- **ASBR README §3 table, LaValle column:** it shows "—" for KF/EKF, particle filter/MCL, motion and sensor models, and occupancy grids/SLAM. LaValle does cover them:
  - KF: §11.6.1 (book pp. 615–617, PDF 316–317).
  - Grid filter and PF: §11.6.2 (pp. 617–619, PDF 317–318).
  - MCL: §12.2.3 (pp. 651–655, PDF 334–336).
  - Sensor models: §11.5.1 (pp. 598–605, PDF 307–311).
  - Wheeled motion models: §13.1.2 (pp. 722–731, PDF 369–374).
  - EM mapping: §12.3.5 (pp. 679–684, PDF 348–350).
  
  There is no UKF, EKF-SLAM or log-odds occupancy grid in the book.
- **README §3 "Potential functions, navigation functions, wavefront → LaValle 8.4":** add §8.2.2–8.2.3 (wavefront, NF2; book pp. 374–381, PDF 195–199). §8.4 is the continuous version.
- **README §3 "Bug algorithms → 12.3":** narrow it to §12.3.3 (book pp. 667–673, PDF 342–345).
- **README §3 "Bayes filter → Ch. 11":** narrow it to §11.2.3 and §11.4.3 (book pp. 577–580, 592; PDF 297–298, 304).
- **README §3 "Optimization-based planning → LaValle Ch. 8, 14":** Ch. 8 is feedback planning, not trajectory optimization. The optimization content is §14.7 (book pp. 855–857, PDF 436–437, very short) and §15.2 (HJB/Pontryagin).
- **README §5.4:** add D\* per LaValle Fig. 12.18 (D\* Lite is not in the book) and a kinodynamic RRT line (Fig. 14.19).
- **`library/README.md` notation table:** add a LaValle column. The KF letters are measurement matrix **$C$**, noise-input matrices **$G$, $H$**, gain **$L$**, and covariances $\Sigma_\theta$ / $\Sigma_\psi$. $\theta$ = nature.
- **`choset-part1.md` §4.6:** note that LaValle's "navigation function" (§8.2.2, §8.4.1) is weaker than Choset Def 4.6.1. Only LaValle §8.4.4.3 matches Choset.
- **`choset-part3.md` App. J pitfall:** LaValle p. 865 (PDF 441) states the LTI stability test correctly and can be cited as the fix.
- **`choset-part3.md` Ch. 12, p. 451 typo:** LaValle's $\rho_{min}=L/\tan\phi_{max}$ (book p. 725, PDF 371) confirms the corrected form $L/\tan\gamma$.
- **`choset-part3.md` Ch. 12, Reeds–Shepp:** Choset lists the 9 base word families. LaValle gives the count (48 words, 46 after Sussmann–Tang) and the parameter ranges (Figs. 15.7, 15.10).
- **`probrob-part2.md` Ch. 15:** LaValle's discounted Bellman equation (10.76) keeps the discount $\alpha$ throughout. Use it to check the $\gamma$ that the Thrun draft drops.
- **Lie-bracket sign:** LaValle, Choset and MR agree. Any lesson that quotes the opposite sign must say so.

---

## Errata found in this range (book pages; do not copy these into code)

### Ch. 8–9
- **Eq. 8.58** (book p. 420, PDF 218): printed $\alpha=1-\dfrac{x-s_i}{r}$. With $s_i=i/r$ the spacing is $1/r$, so it should be $\alpha=1-r(x-s_i)$. As printed, $x=s_{i+1}$ gives $\alpha=1-1/r^2\ne0$, contradicting the next sentence.
- **Fig. 8.17 step 4** (book p. 416, PDF 216): "go to Step 1" should be "go to Step 2"; Step 1 re-initializes $\mathcal O=\emptyset$, $i=1$.
- **Example 9.10** (book p. 451, PDF 234): with uniform $P(\theta)$ the expected regrets are $u_1:4/3$, $u_2:1$, $u_3:2$. The minimizer is $u_2$ (value 1), not $u_1$ as printed.
- **Book p. 454** (PDF 235), after (9.28): "A strategy still takes the form $\pi:\Theta\to U$" should be $\pi:Y\to U$; the robot never sees $\theta$.
- **Exercise 9.18** (book p. 494, PDF 255): "$l_F(x_F)=\infty$ if $x_F\in X_{goal}$" should be $x_F\notin X_{goal}$.
- Minor: Fig. 8.5a caption (book p. 385, PDF 201) says $f=[1\ 1]$ while Ex. 8.6 (book p. 384) says $[1\ 2]$; Ex. 9.5 (book p. 444) cites (9.11) (continuous) for a die, where (9.10) applies.

### Ch. 10–11
- **(10.25)–(10.26)** (book p. 504, PDF 260): $\{x'\in X\mid\exists u\in U(x)\text{ s.t. }x\in\mathrm{WB}(S,u)\}$ mixes $x$ and $x'$; read $\exists u\in U(x')$ with $x'\in\mathrm{WB}(S,u)$ (same for SB).
- **(10.60)–(10.61)** (book p. 517, PDF 267): written with $l(x,a)$, $P(x'\mid x,a)$ where $a$ is the state; read $l(a,u)$, $P(x'\mid a,u)$. In (10.61) the last term should be $(0)\tfrac12$ (Fig. 10.5: $P(c\mid a,2)=1/2$), not $(0)\tfrac14$; the result $5/2$ is unaffected.
- **(10.92)** (book p. 532, PDF 274): per (10.91) every update of $x_k$ uses $1/v_k$, so the first line should be $\frac1{v_k}(d_k+\dots+d_K)$, and the telescoped sum should start at $l(x_k,u_k)$, not $l(x_1,u_1)$: $\hat G_\pi(x_k)\mathrel{+}=\frac1{v_k}\big(\sum_{j=k}^Kl(x_j,u_j)-\hat G_\pi(x_k)\big)$.
- **(10.104)** (book p. 535, PDF 276): policy improvement should use $Q_\pi(x,u)$ from (10.103), not $Q^*$.
- **p. 580 (PDF 298), after (11.41):** "If $i\le k$, then the derived I-state is the full history" should be "if $i\ge k$".
- **(11.63)** (book p. 596, PDF 306): $\sigma_{ij}=\int x_ix_j\,p\,dx$ is the raw second moment; covariance is $\int(x_i-\bar x_i)(x_j-\bar x_j)\,p\,dx$. (Also "$x_i$ and $x_i$" → "$x_i$ and $x_j$".)
- **(11.68)** (book p. 599, PDF 308): Gaussian exponent should be $-\tfrac12\psi^T\Sigma^{-1}\psi$, not $\Sigma$; likewise (11.69) should use the entries of $\Sigma^{-1}$.
- **Homing sensor** (book p. 602, PDF 309): $h(x)=\mathrm{atan2}(l_1-x_1,l_2-x_2)$ has its arguments swapped relative to the book's own C-style $\mathrm{atan2}(y,x)$ (eq. 3.47); use $\mathrm{atan2}(l_2-x_2,\,l_1-x_1)$. (Seen in text extraction and rendered page; convention checked against (3.47).)
- **(11.79), (11.83)** (book pp. 616–617, PDF 316–317): innovation covariance printed $C\Sigma'C^T+H\Sigma_\psi H$; must be $H\Sigma_\psi H^T$ (dimensionally required when $H$ is not square).
- **Exercise 5, (11.91)–(11.92)** (book p. 628, PDF 322): both matrices are **row**-stochastic, contradicting the stated "$j$th entry of the $i$th row is $P(x_{k+1}=i\mid x_k=j)$"; read row $i$ = current state. (11.92) row 1 $[1/10,\,5/5,\,1/10]$ sums to $6/5$; "5/5" is almost certainly $4/5$.
- Minor (text extraction only, not rendered): p. 589 (PDF 303) simplex projection should be $p_{n-1}=1-(p_0+\dots+p_{n-2})$; Ex. 11.19 (book p. 588) calls the accept set "$F=\{a\}$" (clashes with transition $F$) and its table (11.50) gives $b\xrightarrow{0}\{a,b\}$ while (11.47) gives $\{a\}$; Ex. 11.17 "$l_i$ for $1\le j\le9$" index slip.

### Ch. 12–13
1. **(13.198), book p. 779, PDF 398:** Hamilton's equations print $\dot p_i=\partial H/\partial q_i$; correct is $\dot p_i=-\partial H/\partial q_i$ (follows from (13.195)–(13.197) in the same derivation). Same page: Hamilton's equations are first-order *ordinary* (not partial) differential equations; "Louisville" → Liouville.
2. **(13.103) and (13.105), book pp. 759–760, PDF 388:** quaternion kinematics drop the factor $\tfrac12$ and the scalar-part terms. With body-frame $\omega$ and $q=(a,b,c,d)$: $\dot a=-\tfrac12(b\omega_1+c\omega_2+d\omega_3)$, $\dot b=\tfrac12(a\omega_1+c\omega_3-d\omega_2)$, $\dot c=\tfrac12(a\omega_2+d\omega_1-b\omega_3)$, $\dot d=\tfrac12(a\omega_3+b\omega_2-c\omega_1)$. Checked numerically ($\dot R$ from finite differences matches $R[\omega]$ to $10^{-10}$; the printed form is off by $O(1)$).
3. **Book p. 758, PDF 387:** "$I(q)=RI$" should be $I(q)=R\,I\,R^T$.
4. **Ex. 13.13 two-link arm, book pp. 772–773, PDF 394–395:** (13.149) $K_1$ should have $\ell_1^2$; (13.151) $K_2=\tfrac12m_2\dot p\cdot\dot p$ (mass and dot missing); $p$ is the centre of mass of $\mathcal A_2$, not $\mathcal A_1$; (13.152)–(13.153) need $\theta_1+\theta_2$ in place of $\theta_2$ (the figure and $m_{11}$ use a relative $\theta_2$); (13.157) $c_{112}=c_{121}=c_{122}=-m_2d_1\ell_2\sin\theta_2$, $c_{211}=+m_2d_1\ell_2\sin\theta_2$ (book writes $\ell_1$ for $d_1$ and the position coordinate $p_2$ for $\sin\theta_2$); (13.158) $g_1=(m_1\ell_1+m_2d_1)g\cos\theta_1+m_2\ell_2g\cos(\theta_1+\theta_2)$, $g_2=m_2\ell_2g\cos(\theta_1+\theta_2)$; (13.159) the (2,1) entry must be $-\dot\theta_1$ inside the $-r(\cdot)$ bracket to agree with (13.161); (13.161) second row should read $m_{21}\ddot\theta_1+m_{22}\ddot\theta_2+r\dot\theta_1^2+g_2=u_2$, with $r=m_2d_1\ell_2\sin\theta_2$.
5. **(13.138), book p. 769, PDF 393:** $K=\tfrac12m\dot q\cdot\dot q+\tfrac12I\dot q_3^2$ double-counts $\dot q_3$; should be $\tfrac12m(\dot q_1^2+\dot q_2^2)+\tfrac12I\dot q_3^2$ (as (13.139) implies). (13.132), book p. 767: spurious "$=0$" after the Lagrangian (text only, not rendered).
6. **(13.112)–(13.113), book p. 761, PDF 389:** with (13.111) as printed, $f_r=c_r(v-b\omega)/s$ makes lateral slip self-amplifying. Standard linear bicycle model: $f_f=c_f\big(\phi-(v+a\omega)/s\big)$, $f_r=c_r(b\omega-v)/s$. Book p. 762: $I=2170$ kg/m² → kg·m². The text also says (13.110) is the rear-axle centre although $a,b$ are measured from the centre of mass.
7. **(13.39), book p. 740, PDF 378:** $y=h(x)=Cy$ → $Cx$. Ex. 13.4 (book p. 739): indices run to $n$ where $k$ is meant.
8. **Tricycle, book p. 725, PDF 371:** "$u_\phi=\pi/2$ or $u_\phi=\pi/2$" → second should be $-\pi/2$.
9. **Exercise 12.1, book p. 706, PDF 361:** refers to "Figure 12.1a"; Fig. 12.1 is a table — the grid map is Fig. 12.2a.
10. **§12.3.5, book p. 683, PDF 350 (text, not rendered):** "selecting $\hat e_i$ that minimizes (12.30)" and "gradient descent … local minimum" contradict $\arg\max$ in (12.32); EM *maximizes* (ascent to a local maximum).
11. **Probable, Fig. 12.18 step 6, book p. 666, PDF 341:** the last condition "$G_{cur}(x)>G_{best}(x)$" appears to correspond to Stentz (1994) PROCESS-STATE's $h(Y)>k_{old}$, i.e. $G_{cur}(x')>G_{best}(x)$. Check against Stentz's paper before relying on it.
12. **(13.180), book p. 777, PDF 397 (from text extraction, derived by hand):** $\lambda$ omits $g(q)$ although (13.179) contains it, and drops $\dot G\dot q$, which is zero only for constant $G$ (a $q$-dependent Pfaffian matrix has $\dot G\ne0$ even when not explicitly time-varying). Correct: $\lambda=(GM^{-1}G^T)^{-1}\big(GM^{-1}(u-C\dot q-g)+\dot G\dot q\big)$; same $\dot G\dot q$ caveat for (13.184).
13. Minor inconsistencies (text): (13.20) text calls the yaw action $u_\phi$, equation uses $u_\omega$ (book p. 733); (13.47) text says "two action variables, $u_s$ and $u_\omega$" although $s=1$ is fixed (book p. 744); diff-drive "rotates clockwise if $u_l=-u_r$" holds only for $u_r<0$ under (13.16) (book p. 726).

### Ch. 14–15
- **Book p. 818 (PDF 417), §14.3.4 step 3:** "$\tilde u^p:[0,t_F]\to X_{free}$ such that $u(0)=x_{cur}$, $u(t_F)=x_r$" should be $\tilde u^p:[0,t_F]\to U$ with $x(0)=x_{cur}$, $x(t_F)=x_r$.
- **Book p. 822 (PDF 419):** "If $q\ne0$, then the configuration changes quadratically" and "If $q\ne0$, then the $\dot q$ coordinate changes by $\pm\frac12u(\Delta t)^2$" should read "If $u\ne0$"; per (14.21)–(14.23), $\dot q$ changes by $\pm\Delta t$ and $q$ gains $\pm\tfrac12(\Delta t)^2$ on top of $\dot q\Delta t$.
- **Book p. 850 (PDF 433), Ex. 14.6:** second case "If $|a_1|>|a_2|$, then $\ddot s\in[-1/a_2,1/a_2]$" should be $|a_1|<|a_2|$ (the larger $|a_i|$ sets the tighter bound). Same page: $S_{obs}$ "includes any states that belong to $X_{free}$" should be "that do not belong to $X_{free}$".
- **Book p. 851 (PDF 434), (14.45):** $\alpha_i\ddot s+\beta_i\dot s^2+\gamma_i(s)\dot s=u_i$ should be $\alpha_i\ddot s+\beta_i\dot s^2+\gamma_i(s)=u_i$: substituting into (14.44), $C(\tau,\tau'\dot s)\tau'\dot s$ is quadratic in $\dot s$ and $g(\tau(s))$ has no $\dot s$ (standard Bobrow/Shin–McKay form).
- **Book p. 852 (PDF 434):** "$s_k=(k\Delta s)/m$" should be $s_k=k\Delta s=k/m$ (with $\Delta s=1/m$).
- **Book p. 863 (PDF 440):** Euclidean Lyapunov-stability definition "for any $t>0$, there exists some $\delta>0$" should be "for any $\epsilon>0$ there exists $\delta>0$ such that $\|x_I-x_G\|<\delta$ implies $\|x(t)-x_G\|<\epsilon$ for all $t>0$".
- **Book p. 874 (PDF 445):** $R$ must be $m\times m$, not $n\times n$; footnote 4 "$x\in\mathbb R$" should be $\mathbb R^n$.
- **Book p. 901 (PDF 459), (15.85):** the vector multiplying $\partial f/\partial x$ is printed $[0,1,-x_1]^T$ but $g=[0,1,x_1]^T$; the result $[0,0,2]^T$ is unaffected.
- **Book p. 903 (PDF 460), (15.92):** $H'$ is printed with $f,g,[f,g]$ as rows (the transpose of definition (15.56)); the determinant (0) is unaffected.
- **Book p. 904 (PDF 460), (15.95):** second bilinearity line should be $[u,av+bw]=a[u,v]+b[u,w]$.
- **Book p. 906 (PDF 461), (15.105)–(15.106):** with (15.80), $[h_1,[h_1,[h_1,h_2]]]=[0,0,0,0,-1]^T$, not $[0,0,0,0,1]^T$ (signs alternate: $-e_3,+e_4,-e_5$; checked numerically). Rank is unaffected.
- **Book p. 913 (PDF 465), (15.125), and p. 916 (15.145):** the fourth-order CBHD term should be $-\tfrac1{24}[p,[q,[p,q]]]$ (equal to the standard $-\tfrac1{24}[q,[p,[p,q]]]$ by Jacobi); checked numerically with matrix log/exp (error drops from $2|t_4|$ to fifth order with the minus sign).
- **Book p. 918 (PDF 467):** "$\dot u_1=-\lambda\dot u_2$ and $\dot u_2=\lambda\dot u_1$" should be $\dot u_1=-\lambda u_2$, $\dot u_2=\lambda u_1$ (as (15.151) assumes); "$\lambda=2k\pi$ for some integer $n$" → integer $k$; since cost $=\lambda a>0$, the minimum is $k=+1$ for $a>0$ ($k=-1$ for $a<0$), not "$k=-1$, $\lambda=2\pi$"; and $\|u(0)\|=\sqrt{2\pi|a|}$, not $2\pi a$ (checked by RK4 integration).
- **Book p. 920 (PDF 468), (15.161):** "$u_1=a\sin2\pi kt$, $u_2=b\cos2\pi kt$" does not produce (15.162) (numerically, $k=3$ gives $ab/12\pi$ instead of $ab/4\pi$ and does not isolate $x_k$). Use $u_1=a\sin 2\pi t$, $u_2=b\cos 2\pi(k-2)t$, which gives $\Delta x_k=(a/4\pi)^{k-2}b/(k-2)!$ and leaves $x_1..x_{k-1}$ unchanged (Murray–Sastry; matches Choset (12.29)–(12.31)).

---

## Best-reference map
- **[A1]** Not covered in Ch. 8–15. Background only: rigid-body rotation $\dot R=[\omega]R$ (§13.3.3, book pp. 753–761, PDF 385–389).
- **[A2]** Not covered.
- **[A3]**
  - Feedback plans and navigation functions: §8.2.1–8.2.2 (book pp. 371–377, PDF 194–197).
  - Continuous navigation functions: §8.4.1 (pp. 398–402, PDF 207–209).
  - Rimon–Koditschek and harmonic potentials, and the topological obstruction: §8.4.4 (pp. 407–412, PDF 212–214).
  - Lyapunov functions ↔ navigation functions: §15.1.2 (pp. 865–867, PDF 441–442).
  - Pfaffian constraints / car C-space: §13.1.1 (pp. 716–722, PDF 366–369).
  - Exercises 8.7, 8.14, 8.20 (PDF 223–224).
- **[A4]**
  - Wavefront, NF2, Dial: §8.2.3 (pp. 377–381, PDF 197–199).
  - Cell-complex fields and the exact optimal navigation function in a polygon: §8.4.2–8.4.3 (pp. 402–407, PDF 209–212).
  - Value iteration with interpolation and its Dijkstra-like version: §8.5.2 (pp. 418–429, PDF 217–223).
  - Backprojections and nondeterministic/probabilistic Dijkstra: §10.1.2, §10.2.3 (pp. 501–505, 518–521, PDF 259–261, 267–269).
  - D\*: §12.3.2 (pp. 662–667, PDF 339–342).
  - Bug algorithms and competitive ratio: §12.3.3 (pp. 667–673, PDF 342–345).
  - Lattice and cell-based search: §14.4.1–14.4.2 (pp. 820–832, PDF 418–424).
  - HJB as continuous value iteration: §15.2.1 (pp. 870–873, PDF 443–445).
- **[A5]**
  - Differential-constraint planning framework: §14.1–14.3 (pp. 787–820, PDF 402–418), especially Fig. 14.11 on where the BVP appears (p. 819, PDF 418).
  - Kinodynamic RDT: Fig. 14.19 and the metric discussion, §14.4.3 (pp. 832–836, PDF 424–426).
  - Plan-and-transform: §14.6.2 (pp. 842–846, PDF 429–431).
  - Dubins / Reeds–Shepp / Balkcom–Mason: §15.3 (pp. 880–888, PDF 448–452).
  - Brackets, Frobenius, Chow: §15.4.2–15.4.3 (pp. 892–910, PDF 455–463).
  - Wheeled models: §13.1.2 (pp. 722–731, PDF 369–374).
  - Integrator chains: §13.2.4 (pp. 742–744, PDF 379–380).
  - Funnels, sampling covers and the clearance ball: §8.5.1 (pp. 412–418, PDF 214–218).
  - Exercises 14.1, 14.19, 15.16, 15.18.
- **[A6]**
  - Kalman filter as an I-state update: §11.6.1 (pp. 615–617, PDF 316–317).
  - Moment (Gaussian) approximation: §11.4.3 (pp. 595–597, PDF 306–307).
  - Linear sensor models: §11.5.1 (pp. 598–600, PDF 307–308).
  - KF/EKF framing of localization: §12.2.3 (pp. 653–655, PDF 335–336).
  - Euler/RK4 simulators: §14.3.2 (pp. 813–816, PDF 415–416).
  - Noisy car (13.202) (p. 781, PDF 399).
  - Ex. 9.12 and Exercise 9.16 (PDF 237–238, 255).
  - Exercise 11.19 (planar KF with ellipses).
- **[A7]**
  - Observations and Bayes risk: §9.2.3 (pp. 451–454, PDF 234–235).
  - Nondeterministic and probabilistic I-states: §11.2.2–11.2.3 (pp. 574–580, PDF 295–298).
  - Continuous Bayes filter: §11.4.3 (p. 592, PDF 304).
  - Sensor catalog: §11.5.1 (pp. 598–605, PDF 307–311).
  - Ex. 11.23, the set-based mobile robot (pp. 610–612, PDF 313–314).
  - Grid filter and particle filter: §11.6.2 (pp. 617–619, PDF 317–318).
  - Discrete and probabilistic localization, MCL: §12.2.1, §12.2.3 (pp. 640–647, 651–655, PDF 328–332, 334–336).
  - EM mapping: §12.3.5 (pp. 679–684, PDF 348–350).
  - Exercises 11.1, 11.2, 11.5, 11.20, 12.22, 12.26.
- **[A8]**
  - Unifying picture: Ch. 11 intro + §11.1.3 + §11.2.1 sufficient I-maps (pp. 559–574, PDF 288–295) and §12.1.1 (pp. 634–637, PDF 325–327).
  - The Errata list above, to avoid copying bad equations.
- **[H-track, Robot Learning]**
  - MDP formulation, value iteration, policy iteration, infinite horizon, TD and Q-learning: §10.1–10.4 (pp. 496–535, PDF 256–276).
  - Probabilistic navigation functions: §10.6 (pp. 551–556, PDF 284–286).
  - POMDP as a belief MDP: §11.3.3 and §12.1.3 (pp. 589, 638–640, PDF 303, 327–328).
  - Fitted value iteration: §8.5.2.
  - Motion primitives: §14.2.3 (pp. 808–810, PDF 412–413).
  - Trajectory optimization: §14.7 (pp. 855–857, PDF 436–437).
  - HJB/LQR/Pontryagin: §15.2 (pp. 870–879, PDF 443–448).
  - Exercises 10.16, 10.26, 12.23, 15.2–15.4.
- **[R1/R2]**
  - Charts and tangent spaces of $SO(3)$, $\mathbb{RP}^3$, $S^2$: §8.3.2 (pp. 390–398, PDF 203–207).
  - Rigid-body rotation and Euler's equations: §13.3.3 (pp. 753–761, PDF 385–389); quaternion kinematics corrected below.
  - Lie brackets, Lie algebra, P. Hall basis, CBHD: §15.4.2–15.5.1 (pp. 897–916, PDF 457–466).
  - Exercises 8.4, 15.6.
- **[R7]**
  - Lagrangian mechanics: §13.4.1–13.4.3 (pp. 762–777, PDF 389–397); use MLS/MR for the two-link example.
  - Path-constrained time scaling: §14.6.3 and Ex. 14.7 (pp. 846–855, PDF 431–436).
  - Exercises 13.10, 13.11, 13.17, 14.17–14.18.
- **[R8]**
  - Stability, Lyapunov and controllability: §15.1 (pp. 862–870, PDF 439–443).
  - LQR: §15.2.2 (pp. 874–875, PDF 445–446).
  - Hamiltonian → Pontryagin: §13.4.4 (pp. 778–780, PDF 397–398).
  - Exercise 15.1 (an inconclusive Lyapunov candidate).
