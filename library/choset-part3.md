# Choset et al., *Principles of Robot Motion* (MIT Press, 2005) — Part 3: end of Ch 11, Ch 12, Appendices A–J (+ Ch 8–9 deep dive)
Source: cb2bd4a2-Robot_Motion_Theory_and_Algorithms.pdf, PDF pages 411–616 (book pp. 397–603).
**Page map:** book page = PDF page − 14 throughout the book (e.g. book p. 397 = PDF 411).

> **Scope note (important for the parent plan).** The assigned PDF range 411–616 does **not** contain Ch 8 (Kalman filtering, book pp. 269–300 = PDF 283–314) or Ch 9 (Bayesian methods, book pp. 301–348 = PDF 315–362). Those fall in the PDF 206–410 slice. Because the brief says ASBR A5/A6 lean on Ch 8–9, and Appendices I (statistics primer) and J (linear systems/observability) in my range are the background for exactly those chapters, I also read Ch 8–9 in full and wrote the deep section for them below (§ "Ch 8" and § "Ch 9"). If a part-2 reader also covered them, treat this as a cross-check.
>
> Assigned range actually contains: tail of §11.3.3 (grid-based trajectory search) + Ch 11 problems (PDF 411–414), Ch 12 Nonholonomic & Underactuated Systems (PDF 416–485), Appendix A Notation (487–488), B Sets (489–491), C Topology/metric spaces/Jacobians (492–500), D Curve tracing (501–502), E Orientation (503–512), F Polyhedral worlds (513–526), G Complexity (527–534), H Graphs/A*/D* (535–560), I Statistics primer (561–565), J Linear systems & control (566–577), Bibliography (578–609), Index (610–616).

---

## Ch 11 tail — §11.3.3 Grid-based search for trajectories + Ch 11 problems (PDF pp. 411–414, book pp. 397–400) — Lessons: A4, R7
**Big idea:** Kinodynamic planning by discretising *accelerations* rather than configurations. Holding a constant grid acceleration for a timestep $h$ makes the reachable states lie on a regular lattice in state space $(q,\dot q)$, so one can run breadth-first/graph search on that lattice. With "δ-safety" (clearance that grows with speed) the method has a provable approximation guarantee — but it is exponential in the number of DOF, which is why RRT/EST (Ch 7) took over in practice.

**Key definitions:**
- $\delta_v(c_0,c_1)$-safe trajectory: obstacles avoided by at least $c_0 + c_1\lVert\dot q\rVert$ at every point (speed-dependent clearance).
- For a manipulator, $\ddot q = M^{-1}(q)\,(u - C(q,\dot q)\dot q - g(q))$ (Eq 11.46) with box limits $|u_i|\le u_i^{\max}$ (11.47) maps the control box into a state-dependent **parallelepiped** $A(q,\dot q)$ of feasible accelerations (Fig 11.12). One uses a conservative $\hat A(q,\dot q)\subset A$ valid over the whole timestep (Fig 11.13), which needs bounds on the eigenvalues of $M(q)$ and on $\dot M$.

**Key results:** If a $\delta_v$-safe trajectory of time $T_{opt}$ exists, GRID SEARCH (Alg 21) finds a $(1-\epsilon)\delta_v$-safe trajectory between $O(\epsilon)$-perturbed start/goal of time $\le (1+\epsilon)T_{opt}$ (point mass: $\le T_{opt}$); running time polynomial in $1/\epsilon$, exponential in $n_Q$ (e.g. $O((1/\epsilon)^{3n_Q})$ for the point robot). The found trajectory can be quite different from the true optimum. Faster variants: non-uniform discretisation, goal-biased heuristics; RRT/EST also discretise controls with a fixed timestep but give up optimality for probabilistic completeness.

**Exercises worth doing (Ch 11, book p. 400):** P1–P2 (velocity-limit curve and time-scaling for the RP arm of Ex 11.2.1 — good companion to R7 dynamics), P7–P8 (Hamiltonian / adjoint equations, then NLP trajectory optimisation for the RP arm — ties to F6), P9 (implement GRID SEARCH for a planar point among polygons — A3/A4 practice).

---

## Ch 12 Nonholonomic and Underactuated Systems (PDF pp. 416–485, book pp. 401–471) — Lessons: R2 (Lie bracket background), R7/R8 (mechanical systems), A3/A4 (planning for cars), F7 (flows/ODEs)
**Big idea:** A velocity constraint (car wheels can't slip sideways) does not necessarily constrain *where* you can go — parallel parking proves it. The tool to decide is the **Lie bracket**: wiggling along $g_1, g_2, -g_1, -g_2$ for time $\epsilon$ each produces net motion $\epsilon^2[g_1,g_2]$, a direction not in the span of the original fields. If repeated brackets span the whole tangent space (LARC/Chow), the system can reach a full-dimensional set; with symmetry (driftless + controls that can be negated), it is locally controllable. Then the chapter gives planners that exploit structure: optimal control, sinusoids for chained form, differential flatness, Reeds–Shepp + grid search for cars.

**Running examples:** unicycle (kinematic, $x=q=[q_1,q_2,q_3]$, $g_1^{uni}=[\cos x_3,\sin x_3,0]^T$ roll, $g_2^{uni}=[0,0,1]^T$ spin; Ex 12.1.1/12.1.3) and the planar body with thrusters PBWT (second-order, $x\in\mathbb R^6$; drift $g_0=[x_4,x_5,x_6,0,a_g,0]^T$ as printed (book p. 407) although the text says gravity acts in the $-q_2$ direction — so either read $a_g$ as a signed (negative) number or write $-a_g$; $g_1=[0,0,0,\cos x_3,\sin x_3,0]^T$, $g_2=[0,0,0,-\sin x_3,\cos x_3,-d]^T$; Ex 12.1.2/12.1.4).

**Key definitions (§12.1):**
- Tangent space $T_xM$, tangent bundle $TM$ ($=M\times\mathbb R^n$ here), smooth vector field $g:M\to TM$, flow $\phi_t^g$ with $\frac{d}{dt}\phi^g_t(x)=g(\phi^g_t(x))$, integral curve.
- Distribution $D=\mathrm{span}(G)$ ("positive" form) vs constraint form $D(x)=\{\dot x:\omega(x)\dot x=0\}$ with covector/one-form $\omega$; unicycle: $\omega=[-\sin x_3,\cos x_3,0]$ (Eq 12.1). Codistribution annihilates $D$.
- **Nonholonomic** = velocity constraint $f(q,\dot q)=0$ (12.2) not integrable to a configuration constraint; **Pfaffian** form $a(q)\dot q=0$ (workless constraint force). Underactuation in 2nd-order systems gives acceleration constraints $f(q,\dot q,\ddot q)=0$.
- **Lie bracket** (Eq 12.5): $[g_1,g_2]=\frac{\partial g_2}{\partial x}g_1-\frac{\partial g_1}{\partial x}g_2$; from Eq 12.4: $x(4\epsilon)=x_0+\epsilon^2[g_1,g_2](x_0)+O(\epsilon^3)$ for the flow sequence $g_1,g_2,-g_1,-g_2$ (12.3). Commuting fields: bracket $=0$. Properties: skew-symmetry, Jacobi identity; Philip Hall basis picks a minimal set of brackets.
- Unicycle (Ex 12.1.5): $[g_1^{uni},g_2^{uni}]=[\sin x_3,-\cos x_3,0]^T$ = sideways; $\det[g_1\,g_2\,g_3]=1$ ⇒ full rank everywhere (Ex 12.1.6).
- Lie algebra $\mathrm{Lie}(G)$, filtration $D_1\subset D_2\subset\dots$, regular filtration, involutive closure $\bar D$, nilpotent. **Frobenius:** a regular $m$-dim distribution has $m$-dim integral manifolds iff involutive. Foliation/leaves (Ex 12.1.7: $\det=x_1x_2$ gives 9 leaves, Fig 12.9). PBWT: $\det[g_1\dots g_6]=d^4$ (Ex 12.1.8).

**Control systems & controllability (§12.2–12.3):**
- Control-affine system (Eq 12.6): $\dot x=g_0(x)+\sum_{i=1}^m g_i(x)u_i$, drift $g_0$; driftless if $g_0=0$. Control sets: $U_\pm$ (origin in interior of convex hull — positively spans) vs $U_+$ (merely spans, e.g. non-negative controls). Drift in $\mathrm{span}\{g_i\}$ can be cancelled ("walking on a conveyor").
- Four notions (Fig 12.11–12.12): **controllable**, **accessible**, **STLA**, **STLC**. STLC everywhere + connected ⇒ controllable; analytic ⇒ accessible ⇒ STLA. Second-order systems can only be STLC at zero velocity (double integrator example, Fig 12.13). Key point: linearisations of these systems are typically *not* controllable — controllability here is a genuinely nonlinear phenomenon (contrast App J).
- **Thm 12.3.1 (LARC / Chow):** STLA iff $\mathrm{Lie}(\{g_0,\dots,g_m\})(x)=T_xM$ (for analytic fields); symmetric (driftless, $U\in U_\pm$) + LARC ⇒ STLC.
- **Thm 12.3.3 (Sussmann):** bad bracket = $g_0$ appears odd times and each $g_i$ even times. STLC at $x$ if $g_0(x)=0$, $U\in U_\pm$, LARC by good brackets up to degree $k$, and every bad bracket of degree $j\le k$ is a combination of good brackets of lower degree. PBWT ($a_g=0$) is STLC at equilibria (Ex 12.3.4); with one thruster $g_2$ only: STLA ($\det=-16d^8$), not STLC but globally controllable; Fig 12.14 summarises 3/2/1 thrusters.
- **Thm 12.3.5 (Lian–Wang–Fu):** WPPS drift (nonwandering set = $M$, e.g. periodic/energy-conserving drift on compact space) + $U_\pm$ + LARC ⇒ globally controllable. Satellite: one body-fixed torque suffices.

**Simple mechanical control systems (§12.4):** $M(q)\ddot q+C(q,\dot q)\dot q=T(q)u$ (12.8) ⇒ $\ddot q=-M^{-1}C\dot q+\sum Y_i(q)u_i$ (12.10), $Y_i$ = columns of $M^{-1}T$. Intrinsic acceleration $\nabla_{\dot q}\dot q=\ddot q+M^{-1}\dot q^T\Gamma\dot q$ (12.12–12.13) — "$a=M^{-1}f$" with Christoffel corrections; unforced motions are geodesics. **Symmetric product** $\langle Y_1:Y_2\rangle=\nabla_{Y_1}Y_2+\nabla_{Y_2}Y_1$; from rest, $[g_i,g_j]=0$, $[g_0,g_i]=[-Y_i;0]$, $[g_i,[g_0,g_j]]=[0;\langle Y_i:Y_j\rangle]$. Thms 12.4.1–12.4.2 (Lewis–Murray): STLA/STLC and configuration versions STLCA/STLCC/STLEC via $\mathrm{Sym}(Y)$ and $\mathrm{Lie}(\mathrm{Sym}(Y))$. Kinematic reductions / **decoupling vector fields** (Thm 12.4.5, 12.4.7): systems that can follow certain paths at any speed → plan in $Q$ (dim $n_Q$) instead of $TQ$ (dim $2n_Q$), then time-scale (Ch 11). Examples: PBWT, hopper (Ex 12.4.4/12.4.6), knife edge (Ex 12.4.9).

**Motion planning (§12.5):**
- **Optimal control, Brockett system** (12.23): $\dot x_1=u_1,\dot x_2=u_2,\dot x_3=x_2u_1$, minimise $\tfrac12\int u^Tu$ (12.24). Pontryagin gives $\dot u_1=-\lambda_3u_2,\ \dot u_2=\lambda_3u_1$ ⇒ equal-amplitude sinusoids 90° out of phase (12.26–12.27); with $\lambda_3=2k\pi$, $x_{1,2}$ return and $x_3(1)=\mp(u_1(0)^2+u_2(0)^2)/4\pi$ for $k=\pm1$. Shape vs fiber variables (falling cat/astronaut; Fig 12.20). Unicycle → this form by $z=A(x)x$, $v=B(x)u$.
- **Chained form** (12.28): $g_1=[1,0,x_2,x_3,\dots,x_{n-1}]^T$, $g_2=[0,1,0,\dots,0]^T$; always STLC. Steering by sinusoids $u_1=a\sin2\pi t$, $u_2=b\cos2k\pi t$ (12.29–12.30) changes $x_{k+2}$ by $\left(\frac{a}{4\pi}\right)^k\frac{b}{k!}$ (12.31) and leaves lower states unchanged ⇒ algorithm: drive $x_1,x_2$, then fix $x_3,x_4,\dots$ in order.
- **Nonlinear optimisation** (§12.5.3): parameterise $u(t)$ (not $q(t)$, which may be infeasible); local minima, needs good initial guess.
- **Gradient method for driftless systems** (§12.5.4): end-state map $x_f=f(x_{start},u^p)$, error $e=f-x_{goal}$, step $v^T=-e^T\,\partial f/\partial u^p$, $u^p\leftarrow u^p+\alpha v$ — i.e. Jacobian-transpose descent (same idea as R6 J-transpose IK!). Converges if $\partial f/\partial u^p$ has rank $n$; escape singular controls by appending a random "generic loop".
- **Differential flatness** (§12.5.5): flat outputs $y$ with $x=\phi(y,\dot y,\dots)$, $u=\psi(y,\dot y,\dots)$ ⇒ planning = curve fitting. Unicycle (Ex 12.5.1): $y=(x_1,x_2)$, $x_3=\tan^{-1}(\dot y_2/\dot y_1)$, $u_1=\pm\sqrt{\dot y_1^2+\dot y_2^2}$, $u_2=(\dot y_1\ddot y_2-\ddot y_1\dot y_2)/(\dot y_1^2+\dot y_2^2)$ (12.32–12.33); $[0,0,0]\to[1,1,0]$ with $y_1=t$, $y_2=3t^2-2t^3$. PBWT flat outputs = centre of percussion $(x_1+\tfrac1d\cos x_3,\ x_2+\tfrac1d\sin x_3)$. Car (+trailers): midpoint of the last axle. All chained-form systems flat with outputs $x_1,x_n$.
- **Cars** (§12.5.6): kinematic car (12.34) $g_1=[\cos x_4,\sin x_4,0,\tfrac1L\tan x_3]^T$, $g_2=[0,0,1,0]^T$; simplified (12.35) = unicycle with $|\omega|<|v/r_{min}|$; differential drive with $v=(u_1+u_2)/2$, $\omega=(u_1-u_2)/L$. STLC ⇒ any collision-free *open* path for a free-flying body can be converted into a feasible car path; parallel parking in a slot $\epsilon$ longer than the car needs $O(1/\epsilon^2)$ reversals. **Reeds–Shepp** shortest paths (forward+reverse): one of $\{C|C|C,\ CC|C,\ C|CC,\ CC_a|C_aC,\ C|C_aC_a|C,\ C|C_{\pi/2}SC,\ CSC_{\pi/2}|C,\ C|C_{\pi/2}SC_{\pi/2}|C,\ CSC\}$. Planners: Barraquand–Latombe **CAR GRID SEARCH** (Alg 22: best-first over actions $L_\pm,R_\pm,S_\pm$, cost = $a\cdot$steps + $b\cdot$steering changes + $c\cdot$cusps, prune revisits by occupancy grid of $d^3$ cells, resolution complete); **omnidirectional→nonholonomic path transformation** (plan holonomic path, recursively bisect replacing segments by Reeds–Shepp curves, then shortcut); **PRM with Reeds–Shepp local planner** (probabilistically complete); smooth-curvature variants. **Trailers** (12.36–12.38): $v=w\cos\theta$, $\omega=\tfrac wd\sin\theta$; STLC; convertible to chained form.
- §12.5.7 kinematic reductions (plan with decoupling vector fields, minimise switches); §12.5.8 fictitious inputs (Lafferriere–Sussmann, Campbell–Baker–Hausdorff), motion libraries (maneuver automaton), RRT/EST with $\dot x=f(x,u)$ + metric.

**Algorithms:** CAR GRID SEARCH (Alg 22, book p. 454); sinusoidal steering for chained form (book p. 444); gradient end-state iteration (book p. 446).

**Worked examples worth redoing:** Ex 12.1.5–12.1.6 (unicycle bracket + determinant — do by hand), Ex 12.3.4 (PBWT good/bad brackets — practice symbolic Jacobians, F5), Brockett optimal control (book pp. 440–442 — Pontryagin practice), Ex 12.5.1 (flat-output trajectory for unicycle — 10-line NumPy exercise, F1).

**Exercises worth doing (book pp. 467–471):** P8 (Lie bracket of linear vector fields $Ax,Bx$ = matrix commutator — links to R1/R2 $[\hat\omega_1,\hat\omega_2]$ and adjoint), P9 (write a symbolic Lie-bracket routine — SymPy), P13 (prove the car (12.34) is STLC), P19 (2R arm with one actuator — underactuation, R7), P23 (implement CAR GRID SEARCH — A3/A4), P24 (differential drive with wheel angles), P25 (chained-form systems are flat), P29 (derive the Lie-bracket formula 12.4 by Taylor expansion — F5/F7).

**Conventions / pitfalls:**
- Bracket sign convention: Choset uses $[g_1,g_2]=\frac{\partial g_2}{\partial x}g_1-\frac{\partial g_1}{\partial x}g_2$ (some texts, e.g. Murray–Li–Sastry, use the same; others the negative). With this convention the unicycle bracket points to the robot's right ($[\sin x_3,-\cos x_3,0]$).
- Christoffel symbols: Choset's $\Gamma$ does *not* contain $M^{-1}$ (footnote 2, p. 425); differential-geometry texts' $\Gamma^*$ do.
- **Typo:** book p. 451 writes $r_{min}=L/\tan^{-1}\gamma$; geometrically it is $r_{min}=L/\tan\gamma$ (rear-axle midpoint radius with virtual front wheel at steering angle $\gamma$).
- Unicycle configuration is really $\mathbb R^2\times S^1$; mod-$2\pi$ on $x_3$.

---

## Appendix A Mathematical Notation & Appendix B Basic Set Definitions (PDF pp. 487–491, book pp. 473–477) — Lessons: F2, A3
**Big idea:** A symbol table plus the point-set vocabulary used for C-space reasoning in Ch 3–7.
**Key definitions:** $\epsilon$-ball $B_\epsilon(x)$; open/closed sets (closedness depends on the ambient space: $(0,1)$ is neither open nor closed in $\mathbb R^2$); closure, interior, boundary $\partial A=\mathrm{cl}(A)\cap\mathrm{cl}(S\setminus A)$ (Def B.0.2, Ex B.0.3); dense subsets; **Minkowski sum** $A\oplus B=\{a+b\}$ and **difference** $A\ominus B=\{a-b\}$ (the C-obstacle of a translating robot is $WO\ominus R$); line of sight, convexity, convex hull $\mathrm{Co}(A)=\{\sum a_ix_i: a_i\ge0,\sum a_i=1\}$, star-shaped sets.
**Conventions / pitfalls:** `atan2(y,x)` returns $[-\pi,\pi)$; $S^n\subset\mathbb R^{n+1}$; $\Gamma$ = Christoffel symbol; $\nabla$ is both gradient and affine connection (context).

## Appendix C Topology and Metric Spaces (PDF pp. 492–500, book pp. 478–486) — Lessons: F2, F5, R5
**Big idea:** Just enough topology to talk about paths/continuity, plus the book's **Jacobian and gradient conventions** that the potential-field chapter (and everything else) uses.
**Key definitions:** topology (Def C.1.1; trivial/discrete topologies), neighbourhood; metric (Def C.2.1: definiteness, symmetry, triangle inequality ⇒ non-negativity), Euclidean vs Manhattan metrics induce the same topology; normed space, Cauchy sequence, Banach space; inner product (Def C.3.2), Hilbert space; continuity (topological Def C.4.1 and ε–δ (C.1); Ex C.4.2 proves $x^2$ continuous); $C^0, C^k, C^\infty$ — a *path* need only be $C^0$, a *trajectory* needs $C^k, k>0$.
**Key results:** **Differential/Jacobian** $Df$ is the $n\times m$ matrix $[\partial f_i/\partial x_j]$ (also written $J$, $Tf$, $\partial f/\partial x$); **gradient** $\nabla g=Dg^T$ is a *column* vector (robotics/potential-field convention). Chain rule $\frac{d}{dt}(g\circ h\circ c)=\frac{\partial g}{\partial x}\big|_{h(c)}\frac{\partial h}{\partial y}\big|_{c}\dot c$, dimensions $(1\times n)(n\times n)(n\times1)$; Ex C.5.1 (polar→Cartesian, $\dot g=-4\pi\sin2\pi t$) is a good 5-minute check.
**Conventions / pitfalls:** forces are row vectors (cotangent), velocities columns (tangent), so $Fv$ = power — same idea as wrenches pairing with twists in R2/R5.
**Typos spotted (book pp. 481, 484):** inner-product norm should be $\lVert e\rVert=\sqrt{\langle e,e\rangle}$ (book omits the square root); in the change-of-coordinates paragraph "$x=g(y)$" should be $x=h(y)$, and the last line "$D_c(f\circ g)\dot c$" should be $D_c(g\circ h)\dot c$.

## Appendix D Curve Tracing (PDF pp. 501–502, book pp. 487–488) — Lessons: F6, R6
**Big idea:** Predictor–corrector tracing of $f(x,y)=0$ (used for GVD/boundary following in Ch 2 and 5): step along the tangent, then Newton-correct on a plane orthogonal to the tangent.
**Key results:** **Implicit Function Thm D.1.1** — if $D_yf(x_0,y_0)$ is invertible, locally $y=g(x,z)$ with $f(x,g(x,z))=z$ (this is why IK solution branches are smooth away from singularities, R5/R6). **Newton–Raphson Thm D.2.1** — with $\lVert Df(y^*)^{-1}\rVert\le\beta$ and $Df$ Lipschitz ($\gamma$) on $B_\rho(y^*)$, iterates $y_{h+1}=y_h-Df(y_h)^{-1}f(y_h)$ stay in the ball and converge quadratically, $\lVert y_{h+1}-y^*\rVert\le a\lVert y_h-y^*\rVert^2$, $a=\beta\gamma/(2(1-\rho\beta\gamma))$. Same iteration as Newton-Raphson numerical IK (R6) and Gauss–Newton (F6).

## Appendix E Representations of Orientation (PDF pp. 503–512, book pp. 489–498) — Lessons: F3, R1, C1
**Big idea:** No 3-parameter chart covers SO(3) without singularities; Euler/RPY/axis-angle are local charts, and unit quaternions give a 4-chart atlas plus cheap composition.
**Key results & equations:**
- **ZYZ Euler** (E.1) $R=R_z(\phi)R_y(\theta)R_z(\psi)$; inverse: $\theta=\mathrm{atan2}(\pm\sqrt{1-R_{33}^2},R_{33})$ (E.3/E.4); for $s_\theta>0$: $\phi=\mathrm{atan2}(R_{23},R_{13})$, $\psi=\mathrm{atan2}(R_{32},-R_{31})$ (E.5–E.6); for $s_\theta<0$ negate both arguments (E.7–E.8). Singular at $R_{33}=\pm1$: only $\phi+\psi=\mathrm{atan2}(R_{21},R_{11})$ (E.9) or $\phi-\psi$ (E.11) is defined. 12 possible Euler sequences.
- **Fixed-axis "roll–pitch–yaw"** (E.12): $R=R_{z,\phi}R_{y,\theta}R_{x,\psi}$, with the full matrix given; first row $[c_\phi c_\theta,\ -s_\phi c_\psi+c_\phi s_\theta s_\psi,\ s_\phi s_\psi+c_\phi s_\theta c_\psi]$, last row $[-s_\theta,\ c_\theta s_\psi,\ c_\theta c_\psi]$.
- **Axis–angle** (E.13–E.18): $R_{k,\theta}=R_{z,\alpha}R_{y,\beta}R_{z,\theta}R_{y,-\beta}R_{z,-\alpha}$ → the Rodrigues matrix with $v_\theta=1-c_\theta$. Inverse: $\theta=\cos^{-1}\frac{\mathrm{tr}R-1}{2}$, $k=\frac{1}{2\sin\theta}[R_{32}-R_{23},R_{13}-R_{31},R_{21}-R_{12}]^T$; $R_{k,\theta}=R_{-k,-\theta}$ (E.19); chart $\phi(R)=\theta k$ (E.20) = exponential coordinates (R1); $\theta$ is a good distance on SO(3).
- **Quaternions** (E.22–E.35): $i^2=j^2=k^2=-1$, $i=jk$, $j=ki$, $k=ij$; $Q=(\cos\frac\theta2, n\sin\frac\theta2)$ (E.26), unit norm; $R(Q)$ (E.28) with diagonal $2(q_0^2+q_i^2)-1$, off-diagonals $2(q_1q_2\mp q_0q_3)$ etc.; $q_0=\tfrac12\sqrt{1+\mathrm{tr}R}$, $q_{1:3}=\frac{1}{4q_0}[R_{32}-R_{23},R_{13}-R_{31},R_{21}-R_{12}]$ (E.29–E.30); product $z_0=x_0y_0-x\cdot y$, $z=x_0y+y_0x+x\times y$; conjugate $Q^*=(q_0,-q)$ (E.31), $QQ^*=(\lVert Q\rVert^2,0)$; rotate a point $Q(0,p)Q^*+T$ (E.34); rotate a normal $Q(0,n)Q^*$ (E.35). Four charts $U_i$ = "component $i$ is the largest", $\phi_i$ = other three components divided by $|q_i|$.
**Conventions / pitfalls:**
- **Naming trap:** Choset (following Spong) calls the rotation about world **x "yaw" (ψ)** and about world **z "roll" (φ)** — the reverse of the aerospace/ROS convention (roll about x, yaw about z). The *matrix* $R_zR_yR_x$ is the same as ROS's RPY → when cross-referencing to ROS `tf` or an IMU in A5, map Choset's $(\phi,\theta,\psi)$ to ROS (yaw, pitch, roll).
- Fixed-axis $x\to y\to z$ (premultiply) = moving-axis $z\to y\to x$ (postmultiply) — same matrix.
- Scalar-first quaternion $(q_0,q)$; Eigen and ROS store $(x,y,z,w)$ (scalar last) — classic A7 bug.
- **Book errata:** (i) the axis–angle chart domain is written $\{R\mid \mathrm{Tr}(R)\ne\pm1\}$ (p. 493); the actual singularities are $\theta=0$ ($\mathrm{tr}=3$) and $\theta=\pi$ ($\mathrm{tr}=-1$), so it should read $\mathrm{tr}R\notin\{3,-1\}$ ($\mathrm{tr}R=1$ is the harmless $\theta=\pi/2$). (ii) Similarly for (E.29–E.30) only $\mathrm{tr}R=-1$ ($q_0=0$) is a problem. (iii) Typo in the quaternion product line "$y_0+iy_1+yx_2+yx_3$" → $y_0+iy_1+jy_2+ky_3$. (iv) Chart sets $U_i$ should compare $|q_i|$, not signed $q_i$.

## Appendix F Polyhedral Robots in Polyhedral Worlds (PDF pp. 513–526, book pp. 499–512) — Lessons: A3, C5
**Big idea:** Polygons as intersections of half-planes make collision tests and C-obstacle construction purely linear-algebraic; the Minkowski difference unifies C-obstacles (star algorithm) and distance computation (GJK).
**Key definitions:** line $h(x,y)=ax+by-c$ (F.1), half-planes $h^\pm$; convex polygonal region = intersection of *negative* half-planes; polygonal region = finite union of convex ones; polygon = closed, simply connected polygonal region.
**Key results:**
- Contact types (F.2): **Type A** robot edge $E_i^R$ touches obstacle vertex $o_j$, applicable iff $(o_{j-1}-o_j)\cdot n_i^R(\theta)\ge0$ and $(o_{j+1}-o_j)\cdot n_i^R(\theta)\ge0$ (F.3), half-space $n_i^R\cdot(o_j-r_i(x,y,\theta))\le0$ (F.4). **Type B** obstacle edge touches robot vertex (F.5–F.6). Collision ⇔ all applicable half-space inequalities hold (worked tables for Fig F.7a/b).
- **Star algorithm** (§F.3): sort obstacle normals and *negated* robot normals by angle, merge; each interleaving gives a C-obstacle vertex pair $o_j-r_i,\ o_j-r_{i+1}$ (type A) or $o_j-r_i,\ o_{j+1}-r_i$ (type B). Implements $WO\ominus R$ (F.7) in $O(n+m)$ after sorting. SE(2): stack θ-slices (Fig F.8).
- **GJK** (§F.5, Alg 23): $d(A,B)=\min_{z\in A\ominus B}\lVert z\rVert$ (F.8–F.10); support function $h_Z(x)=\max_{z\in Z}z\cdot x$ (F.11), support point $s_Z$ (F.12); for a Minkowski difference $h_{A\ominus B}(x)=h_A(x)+h_B(-x)$ (F.13), $s_{A\ominus B}(x)=s_A(x)-s_B(-x)$ (F.14) — never build $Z$ explicitly. Simplex of 3 (2-D) / 4 (3-D) vertices.
**Book errata:** (i) Fig F.1 text (p. 500) says $(0,5),(3,5)$ lie in the negative half-plane of $h=x+y-1$ and $(0,0),(2,2)$ in the positive one — $(0,0)$ gives $-1$ and $(0,5)$ gives $+4$, so $(0,5),(3,5),(2,2)$ are positive and $(0,0)$ negative. (ii) GJK termination (Alg 23 step 4) is printed as $\lVert x_k\rVert=h_Z(-x_k)$; dimensionally it should be $\lVert x_k\rVert^2+h_Z(-x_k)=0$ (i.e. no vertex of $Z$ lies further toward the origin than $x_k$); in practice use a tolerance.
**Exercise idea:** implement the type-A/B half-space test and GJK in NumPy for two triangles (C5 closest-point practice).

## Appendix G Analysis of Algorithms and Complexity Classes (PDF pp. 527–534, book pp. 513–520) — Lessons: A3, A4
**Key definitions:** $O,o,\Omega,\omega,\Theta$ (Def G.1.1) with the running-time ladder constant → log → polylog → linear → polynomial → quasipolynomial $n^{\log^c n}$ → exponential → doubly exponential; alphabets/strings/languages, decide vs accept; P, EXPTIME, NP (poly-time verifiable certificate), reductions, NP-complete/NP-hard, PSPACE(-complete); $P\subseteq NP\subseteq PSPACE\subseteq EXPTIME$. **§G.3 Completeness:** complete (Def G.3.1), **resolution complete** (G.3.2: complete for a fine enough discretisation — grid A*, CAR GRID SEARCH), **probabilistically complete** (G.3.3: success probability → 1 as time → ∞ — PRM/RRT; cannot report non-existence). These three definitions are exactly the vocabulary needed for A3/A4.
**Pitfall:** the Ω definition is printed as "$cf(n)\ge g(n)$"; equivalent to the usual $f(n)\ge c'g(n)$.

## Appendix H Graph Representation and Basic Search (PDF pp. 535–560, book pp. 521–546) — Lessons: A3 (primary), A4
**Big idea:** All the roadmap/cell/grid planners reduce to graph search. Uninformed search (BFS/DFS) vs informed (greedy, A*); optimality (of the path) vs efficiency (nodes expanded); and replanning (D*) when edge costs change while the robot moves.
**Key definitions:** directed/undirected graphs, weights, path/walk, connected, cycle, tree/root/leaf; 4- vs 8-connectivity on grids (Fig H.4); link length (edge count) vs path length. **BFS** = FIFO queue (wavefront planner = BFS; optimal in link length), **DFS** = LIFO stack (fast, non-optimal; Fig H.8–H.9). Greedy = priority queue on $h$.
**A\* (Alg 24, §H.2):** open set $O$ (priority queue), closed set $C$, $\mathrm{Star}(n)$ neighbours, $c(n_1,n_2)$ edge cost, $g(n)$ cost-to-come via back-pointers, $h(n)$ heuristic, $f=g+h$. Pop min-$f$ node, move to $C$, exit if goal, else for each neighbour not in $C$: add to $O$ if new, or re-point its back-pointer if $g(n_{best})+c<g(x)$. **Admissible/optimistic** $h\le h^*$ ⇒ optimal path; Euclidean ≤ grid path length (Fig H.10). Special cases: $f=h$ greedy, $f=g$ **Dijkstra** (§H.2.3 worked open/closed lists, Fig H.14). Grid example §H.2.4: 8-connected, costs 1 / 1.4, obstacle cells cost 10000 (graph includes obstacle cells). §H.2.5: a non-optimistic heuristic ($h(B)=10$ vs true 2) makes A* return a suboptimal path (Fig H.20).
**D\* (§H.3, Algs 25–31):** backward Dijkstra from the goal computes $h(X)$ (cost-to-goal) for all states and back-pointers $b(X)$; the queue is ordered by $k(X)=$ minimum $h$ seen since $X$ was queued. Tags NEW/OPEN/CLOSED. When the robot senses a cost change $r(X,Y)\ne c(X,Y)$, MODIFY-COST re-opens the state and PROCESS-STATE propagates **RAISE** states ($k<h$: cost increased, back-pointers may be stale) and **LOWER** states ($k=h$) until $k_{min}\ge h(X_c)$. Worked gate example (Figs H.21–H.26): gate (4,3) closes, $(3,2)$ raised to 10004.6, then lowered to $7.6=6.2+1.4$ via $(4,1)$.
**§H.4 Optimal plans:** a *policy/universal plan* maps every node to an action; running Dijkstra backward from the goal yields one (precursor to MDP value iteration — H-course link).
**Worked examples worth redoing:** Fig H.11–H.13 A* by hand (cost 5 via E, then 4 via C–K); Dijkstra Fig H.14; non-optimistic Fig H.20.
**Conventions / pitfalls:**
- The book says "highest priority" for the *smallest* $f$ — read "priority" as "min-heap key".
- Alg 24 as printed omits setting $g(x)$/back-pointer when $x$ is first added and does not re-key $x$ in $O$ after a back-pointer update — implement it as standard A* ($g(x)\leftarrow g(n_{best})+c$, parent ← $n_{best}$, decrease-key/push duplicate).
- The informal "A* is optimal" argument in §H.2.2 silently needs an admissible $h$ (and for the closed-set version, a *consistent* $h$: $h(n)\le c(n,n')+h(n')$); with an admissible but inconsistent $h$ and a closed set you can miss the optimum. Grid heuristics (Euclidean, octile) are consistent.
- In D*, $h$ is cost-to-goal (not an A*-style estimate) and the search runs goal→start.

## Appendix I Statistics Primer (PDF pp. 561–565, book pp. 547–551) — Lessons: F8, A5, A6
**Big idea:** The minimum probability needed for Ch 8–9: axioms, conditional probability and Bayes' rule, densities, expectation, covariance, and the multivariate Gaussian.
**Key definitions & results:** Probability axioms (1)–(6) (first three imply the rest; σ-additivity); independence $\Pr(E_1\wedge E_2)=\Pr(E_1)\Pr(E_2)$; **Bayes' rule** $\Pr(E_1|E_2)=\Pr(E_2|E_1)\Pr(E_1)/\Pr(E_2)$ ("the foundation of ch. 9"). Random variable $X:S\to\mathbb R$; CDF $F_X(a)=\Pr(X\le a)$, PMF, PDF with $\Pr(a\le X\le b)=\int_a^bf_X$; $\Pr(X=a)=0$ for continuous $X$; uniform $U(x;a,b)$/$u(x;a,b)$. Random vector; mean $E(X)=\sum x_if_X(x_i)$ or $\int xf_X(x)dx$ (die example 3.5 — the mean need not be a possible outcome); linearity of $E$; variance, covariance $\sigma_{ij}$; covariance matrix $P_X=E[(X-\bar X)(X-\bar X)^T]$. **Multivariate Gaussian (I.1):** $f_X(x)=\frac{1}{\sqrt{(2\pi)^n|P_X|}}\exp\!\big(-\tfrac12(x-\bar X)^TP_X^{-1}(x-\bar X)\big)$.
**Book error (important for F8):** p. 551 states "if $\sigma_{ij}=0$ then $X_i$ and $X_j$ are independent." **False in general** — zero covariance means *uncorrelated*; independence follows only for *jointly Gaussian* variables (counter-example: $X\sim N(0,1)$, $Y=X^2$ has $\mathrm{cov}=0$ but is fully dependent). The KF derivation in Ch 8 is fine because everything there is jointly Gaussian.
**Missing from the primer (add from elsewhere for F8):** marginalisation/total probability, conditional densities, the Gaussian conditioning formula, linear transformation of Gaussians ($AX+b\sim N(A\bar X+b, AP_XA^T)$), MLE. Ch 8 §8.2.3 supplies the linear-transform and product-of-Gaussians results.

## Appendix J Linear Systems and Control (PDF pp. 566–577, book pp. 552–563) — Lessons: F7, R8, A5
**Big idea:** State-space LTI systems, stability via eigenvalues, controllability/observability rank tests, pole placement, and the **Luenberger observer** — which is exactly the deterministic skeleton that the Kalman filter (Ch 8) turns into an optimal stochastic estimator (KF = observer with a time-varying, covariance-optimal gain).
**Key results & equations:**
- Mass–spring–damper (J.1) $m\ddot z=-\gamma\dot z-kz$ → (J.2) $\dot x=\begin{bmatrix}0&1\\-k/m&-\gamma/m\end{bmatrix}x$; general (J.3) $\dot x=Ax$, solution (J.4) $x(t)=e^{A(t-t_0)}x_0$, $e^{At}=\sum A^it^i/i!$ (F7).
- Thm J.2.1 stability via $\mathrm{Re}\,\lambda_i$ (see caveat below); phase-plane figures J.2–J.4 for $\gamma=1,0,-0.4$ ($m=1,k=5$).
- Control system (J.6) $\dot x=Ax+Bu$; **Thm J.3.1 controllability** $\mathrm{rank}[B\ AB\cdots A^{n-1}B]=n$; **Thm J.3.2 eigenvalue (pole) placement** with $u=-Kx$ ⇒ $\dot x=(A-BK)x$; LQR mentioned.
- Output (J.8) $y=Cx$; **Thm J.4.1 observability** $\mathrm{rank}[C;CA;\dots;CA^{n-1}]=n$; duality $(A,C)$ observable ⇔ $(A^T,C^T)$ controllable; minimal triple. **Observer (J.9)** $\dot{\hat x}=A\hat x+Bu+K(y-C\hat x)$, error $\dot e=(A-KC)e$ — choose $K$ to make $A-KC$ Hurwitz (Fig J.5–J.6; example $m=1,k=2,\gamma=0$, $K=[0,2]^T$ gives eigenvalues $-1\pm i$).
- **Discrete time (J.10):** Euler discretisation $F=I+TA$, $G=TB$, $H=C$ ⇒ $x(k+1)=Fx(k)+Gu(k)$, $y(k)=Hx(k)$ — this is the $F,G,H$ notation Ch 8 uses. Thm J.5.1: stability via $|\lambda_i(F)|$; controllability $[G\ FG\cdots F^{n-1}G]$, observability via $(F^T,H^T)$.
**Book errata / caveats:**
- **Thm J.2.1 (and J.5.1) are stated too strongly.** "Stable iff all $\mathrm{Re}\,\lambda_i\le0$" and "unstable iff some $\mathrm{Re}\,\lambda_i>0$" fail for repeated imaginary-axis eigenvalues with non-trivial Jordan blocks: the double integrator $A=\begin{bmatrix}0&1\\0&0\end{bmatrix}$ has $\lambda=0,0$ yet $x(t)=x_0+tv_0$ is unstable. Correct statement: marginally stable iff $\mathrm{Re}\,\lambda_i\le0$ **and** every eigenvalue with $\mathrm{Re}\,\lambda=0$ is semisimple; discrete-time analogue with $|\lambda|=1$. (Asymptotic-stability statements are correct.)
- p. 552: "$F_d=-\gamma\,dz/dt=\gamma\dot z$" — sign typo, $F_d=-\gamma\dot z$.
- p. 554: the series for a constant $A$ is just the matrix-exponential Taylor series (the Peano–Baker series is the time-varying generalisation).
- p. 557: "chapter 8, section 12.3" → chapter 12, §12.3.
- p. 558 state-feedback example: $A-BK$ has $(2,1)$ entry $-(k+k_1)/m$, so the eigenvalues are $\frac{-(\gamma+k_2)\pm\sqrt{(\gamma+k_2)^2-4(k+k_1)m}}{2m}$ (book prints $k-k_1$); stability needs $\gamma+k_2>0$ **and** $k+k_1>0$.
- p. 561 observer example: with $C=[0\ 1]$, $\det(A-KC)=\frac{k}{m}(1-k_1)$, so the discriminant term is $4mk(1-k_1)$, not $4m(k-k_1)$; the numerical example ($k_1=0$) is unaffected.
- p. 562 (J.10) has a stray dot: "$\dot x(k+1)$" → $x(k+1)$. Also note $F=I+TA$ is only first-order accurate; the exact zero-order-hold discretisation is $F=e^{AT}$, $G=\int_0^Te^{A\tau}d\tau\,B$ — use the exact form (or at least small $T$) when building the ASBR Assignment-4 EKF process model from IMU rates.

## Bibliography & Index (PDF pp. 578–616, book pp. 565–603)
Reference list (≈ 430 entries) and index; skimmed only. Useful pointers for this course plan: Thrun–Burgard–Fox *Probabilistic Robotics* lineage for Ch 9; Murray–Li–Sastry [330] for Lie brackets/nonholonomic steering (R-course overlap); Laumond [266] for car planning; Stentz [397] for D*; Gilbert–Johnson–Keerthi [163] for GJK; Reeds–Shepp [360].

---

# DEEP DIVE (outside assigned range, read for the A5/A6 check): Ch 8 Kalman Filtering and Ch 9 Bayesian Methods

## Ch 8 Kalman Filtering (PDF pp. 283–314, book pp. 269–300) — Lessons: A5 (primary), F8, F4, F5
**Big idea:** Dead reckoning loses information every step; sensors inject it back. Any probabilistic estimator is a loop of **predict** (push the belief through the motion model — uncertainty grows) and **update** (fuse a measurement — uncertainty shrinks). The Kalman filter is the special case where everything is linear and Gaussian, so the belief stays Gaussian and the loop reduces to closed-form updates of a mean $\hat x$ and covariance $P$. Choset's derivation is *geometric*: the update is "the point on the set of states consistent with the measurement that is closest to the prediction" — first in Euclidean distance (naive observer), then in Mahalanobis distance (noise-free KF), then with a noisy measurement (full KF).

**Notation (memorise — it differs from most slides/Thrun/ASBR):**

| Choset | Meaning | Thrun / typical slides |
|---|---|---|
| $x(k+1)=F(k)x(k)+G(k)u(k)+v(k)$ (8.1) | process model | $A,B$; $x_t=A_tx_{t-1}+B_tu_t+\epsilon_t$ |
| $y(k)=H(k)x(k)+w(k)$ (8.2) | measurement model | $C$ or $H$; $z_t$ |
| $V(k)$ = cov of $v$ | process-noise covariance | $R_t$ (Thrun) / $Q$ (most others) |
| $W(k)$ = cov of $w$ | measurement-noise covariance | $Q_t$ (Thrun) / $R$ (most others) |
| $\hat x(k_1\mid k_2)$, $P(k_1\mid k_2)$ | estimate at $k_1$ using outputs up to $k_2$ | $\bar\mu_t,\bar\Sigma_t$ (predicted), $\mu_t,\Sigma_t$ |
| $\nu$ | innovation | $z-\hat z$ |
| $S$ | innovation covariance | $S$ |
| $R=PH^TS^{-1}$ | **Kalman gain** | $K$ |

So in Choset **$R$ is the gain, not the measurement noise**, and **$V,W$ are the noise covariances**. Mixing this up with the "$Q$ process / $R$ measurement" convention is the #1 source of bugs.

**Key definitions:** white noise = independent across time (footnote p. 273); $H(k)$ assumed full row rank; observer = reconstructs state from $u,y$ history; Mahalanobis norm $\lVert x\rVert_M^2=x^TP^{-1}x$ ("distance in standard deviations", footnote p. 279).

**Derivation chain (book pp. 274–284):**
1. **Simple observer, no noise** (§8.2.2, Eqs 8.3–8.5): predict $\hat x(k+1|k)=F\hat x(k|k)+Gu$. The measurement confines the state to the hyperplane $\Omega=\{x: Hx=y(k+1)\}$. Shortest correction $\Delta x\perp\Omega$ ⇒ $\Delta x\in\mathrm{col}(H^T)$ (null-space/row-space facts, F4), $\Delta x=H^TK\nu$; enforcing $H(\hat x+\Delta x)=y$ gives $K=(HH^T)^{-1}$ (8.7): **$\hat x^+=\hat x^-+H^T(HH^T)^{-1}(y-H\hat x^-)$** — the minimum-norm (pseudo-inverse) correction. Flaw: errors parallel to $\Omega$ are never corrected.
2. **Gaussian belief, process noise, no measurement noise** (§8.2.3): (8.8) Gaussian PDF; covariance prediction derived from the definition using independence of $v$: **$P(k+1|k)=FP(k|k)F^T+V$ (8.12)**. Update = most-likely point of the predicted Gaussian on $\Omega$ ⇔ minimise $\lVert\Delta x\rVert_M$ ⇒ $\Delta x\perp_M\Omega$ ⇒ $\Delta x=P^-H^TK\nu$ (8.13) ⇒ $K=(HP^-H^T)^{-1}$ ⇒ $\hat x^+=\hat x^-+P^-H^T(HP^-H^T)^{-1}\nu$ (8.14), $P^+=P^--RHP^-$ (8.15). Flaw: $P^+$ becomes singular (perfect measurements).
3. **Full KF** (§8.2.4): project prediction into output space, $\hat y=H\hat x^-$, $\hat W=HP^-H^T$; fuse with the measurement Gaussian $(y,W)$ via **Thm 8.2.1 (product of Gaussians):** $(z_1,C_1)\cdot(z_2,C_2)\propto N(z_3,C_3)$ with $Q=C_1(C_1+C_2)^{-1}$, $z_3=z_1+Q(z_2-z_1)$, $C_3=C_1-QC_1$. The most-likely output is $y^*=\hat y+\hat W(\hat W+W)^{-1}(y-\hat y)$; then do step 2 with $\Omega^*=\{Hx=y^*\}$. Because $y^*-H\hat x^-=\hat W(\hat W+W)^{-1}\nu$ and $\hat W=HP^-H^T$, the $(HP^-H^T)^{-1}$ cancels and the gain becomes $P^-H^T(HP^-H^T+W)^{-1}$.

**Key results — the KF (§8.2.5, Eqs 8.26–8.32):**
$$\text{predict: }\ \hat x(k+1|k)=F(k)\hat x(k|k)+G(k)u(k),\qquad P(k+1|k)=F(k)P(k|k)F(k)^T+V(k)$$
$$\text{update: }\ \nu=y(k+1)-H(k+1)\hat x(k+1|k),\quad S=H P(k+1|k)H^T+W(k+1),\quad R=P(k+1|k)H^TS^{-1}$$
$$\hat x(k+1|k+1)=\hat x(k+1|k)+R\nu,\qquad P(k+1|k+1)=P(k+1|k)-RH(k+1)P(k+1|k)$$
"Large" $R$ ⇒ trust the sensor; "small" $R$ ⇒ trust the prediction. The result is not just "best" but the exact Bayesian posterior for Gaussian prior + linear-Gaussian models; with non-Gaussian noise it is the best *linear* estimator.

**Observability (§8.2.7, Thm 8.2.2):** LTI $(F,H)$ observable iff $\mathrm{rank}[H;HF;\dots;HF^{n-1}]=n$; for observable systems the KF error stays bounded. Dead-reckoning example measures only velocity ⇒ $[0\ 1;0\ 1]$ rank 1 ⇒ position variance grows without bound — not the filter's fault.

**Dead-reckoning example (§8.2.6, Eqs 8.33–8.34; Figs 8.5–8.7):** $x=[x_r,v_r]^T$, $F=\begin{bmatrix}1&T\\0&1\end{bmatrix}$, $G=[0,T/m]^T$, $H=[0\ 1]$; $m=1,T=0.5,W=0.5,V=\begin{bmatrix}0.2&0.05\\0.05&0.1\end{bmatrix}$, $\hat x=[2,4]^T$, $P=\mathrm{diag}(1,2)$, $u=0$, 95 % confidence ellipses. Redo it in NumPy and reproduce the three figures (prediction ellipse grows/shears, 1-D fusion in output space, update squashes the ellipse vertically).

**EKF (§8.3, Eqs 8.35–8.45):** $x(k+1)=f(x,u,k)+v$, $y=h(x,k)+w$. Predict with $f$ itself, covariance with Jacobian $F=\partial f/\partial x|_{\hat x(k|k)}$; innovation with $h$ itself, $\nu=y-h(\hat x(k+1|k))$, gain/covariance with $H=\partial h/\partial x|_{\hat x(k+1|k)}$. Everything else identical to the KF.
- **Range–bearing localisation (§8.3.1):** unicycle $x=[x_r,y_r,\theta_r]$, $u=[u_1,u_2]$ (here *per-step* displacement and rotation — no $T$), $f=[x_r+u_1\cos\theta_r,\ y_r+u_1\sin\theta_r,\ \theta_r+u_2]$, $F=\begin{bmatrix}1&0&-u_1\sin\theta_r\\0&1&u_1\cos\theta_r\\0&0&1\end{bmatrix}$. Landmarks $(x_{\ell j},y_{\ell j})$ known; variable number $p(k)$ of measurements; association map $a(i)$; stacked $h$ and $H$. Range row $[\frac{\hat x_r-x_\ell}{\rho},\frac{\hat y_r-y_\ell}{\rho},0]$, bearing row $[-\frac{\hat y_r-y_\ell}{\rho^2},\frac{\hat x_r-x_\ell}{\rho^2},-1]$ (book writes it in the $1/(1+(\Delta y/\Delta x)^2)$ form).
- **Data association (§8.3.2):** for measurement $i$ and landmark $j$: $\nu_{ij}=y_i-h_i(\hat x^-,j)$, $S_{ij}=H_iP^-H_i^T+W_i$, $\chi^2_{ij}=\nu_{ij}^TS_{ij}^{-1}\nu_{ij}$; pick the $j$ minimising it (nearest neighbour in Mahalanobis distance). Fig 8.8: Nomad Scout EKF track.
- **Range-only (§8.3.3):** delete the bearing rows.

**KF-SLAM (§8.4):**
- **Simple SLAM (§8.4.1)** (Smith–Self–Cheeseman): state $[x_r,y_r,x_{\ell1},y_{\ell1},\dots]^T$; landmarks static so $F=I$, $G=[I_2;0]$, $V=\mathrm{blkdiag}(V_r,0,\dots)$; measurement = landmark position relative to robot, $y_i=[x_{\ell i}-x_r,\ y_{\ell i}-y_r]+w_i$ ⇒ **linear** $H_i$ with $-1$ in columns 1/2 and $+1$ in columns $2i+1/2i+2$; stack all $H_i$, $W=\mathrm{blkdiag}(W_i)$; plain KF solves SLAM.
- **Range–bearing EKF-SLAM (§8.4.2, Eqs 8.46–8.47):** $y_i=[\rho_i,\ \mathrm{atan2}(y_{\ell i}-y_r,x_{\ell i}-x_r)-\theta_r]+w_i$; $H_i$ is dense in the three robot columns and the two columns of landmark $i$, zero elsewhere: range row $[\frac{x_r-x_{\ell}}{\rho},\frac{y_r-y_\ell}{\rho},0,\dots,\frac{x_\ell-x_r}{\rho},\frac{y_\ell-y_r}{\rho},\dots]$, bearing row $[\frac{y_\ell-y_r}{\rho^2},\frac{x_r-x_\ell}{\rho^2},-1,\dots,\frac{y_r-y_\ell}{\rho^2},\frac{x_\ell-x_r}{\rho^2},\dots]$. Gating (8.48): if the minimum $\chi^2$ exceeds a threshold, initialise a **new landmark**.

**Algorithms:** KF loop (8.26–8.32); EKF loop (8.37–8.45); NN data association with χ² gate.

**Worked examples worth redoing:** dead-reckoning KF (p. 285–289) — reproduce Figs 8.5–8.7; range–bearing EKF (p. 291–292) — derive $F$ and $H_i$ yourself; simple SLAM $H_i$ (p. 295).

**Exercises worth doing (book pp. 297–300):**
- **P9 (do this first — it is a miniature of ASBR Assignment 4):** unicycle with $T=0.25$, $\hat x(1|1)=[1,0.5,\pi/4]$, $u=[3,\pi]$, $P=I$, scalar measurement $y=x_1+w$ with variance 0.5, $y(2)=1.7$. My NumPy check (taking $V=0$ since the problem only specifies one variance, and $W=0.5$): $\hat x(2|1)=[1.5303,1.0303,1.5708]$, $P(2|1)=\begin{bmatrix}1.2812&-0.2812&-0.5303\\-0.2812&1.2812&0.5303\\-0.5303&0.5303&1\end{bmatrix}$, $S=1.7812$, $R=[0.7193,-0.1579,-0.2977]^T$, $\hat x(2|2)=[1.6524,1.0035,1.5203]$, $P(2|2)_{11}=0.3596$. (If you also take $V=0.5I$: $\hat x(2|2)=[1.6628,1.0094,1.5314]$.) Note how a measurement of $x_1$ alone corrects $\theta$ through the off-diagonal covariance.
- **P10:** input noise $s_1,s_2$ ⇒ $P^-=FPF^T+G_uMG_u^T$ with $G_u=\partial f/\partial u$, $M=\mathrm{diag}(\sigma_1^2,\sigma_2^2)$ — exactly how IMU noise enters the Assignment-4 EKF.
- **P7 / P8:** derive $P^+=P^--RHP^-$ and prove Thm 8.2.1 (product of Gaussians) — F8 core.
- **P2:** Euler-discretise the mass–spring–damper (links to App J).
- **P3–P6:** geometry of $\Omega$, null space ⟂ row space (F4).
- **P11:** unbiasedness of the KF.
- **P1:** why real noise (range, bearing, wheel mismatch, slip, multipath, gyro drift) is not zero-mean white Gaussian — great discussion question for A5.

**Conventions / pitfalls:**
- Choset's $R$ = gain, $V$ = process noise, $W$ = measurement noise (table above).
- $P^+=P^--RHP^-$ is only valid with the optimal gain and loses symmetry/PSD numerically; in code prefer the **Joseph form** $P^+=(I-RH)P^-(I-RH)^T+RWR^T$ and symmetrise.
- **Angle wrapping:** wrap the bearing innovation $\nu$ (and $\theta$) to $[-\pi,\pi)$ — the book never mentions it.
- EKF Jacobians are evaluated at different points: $F$ at $\hat x(k|k)$, $H$ at $\hat x(k+1|k)$.
- Discretisation: the book's examples use Euler ($F=I+TA$); for IMU integration with large $T$ use exact or higher-order discretisation (App J note).
- "Optimal in the sense that the expected value of the error is minimised" (p. 285) is loose: the KF is unbiased and minimises the *mean-squared* error ($\mathrm{tr}\,P$).

**Book errata found in Ch 8:**
1. **p. 283, $y^*$ sign error:** printed $y^*=\hat y+\hat W(\hat W+W)^{-1}(\hat y-y(k+1))$; by Thm 8.2.1 it must be $(y(k+1)-\hat y)$. The next line also uses $W(k)$ where $W(k+1)$ is meant.
2. **p. 291 bearing model:** $\mathrm{atan2}(y_r-y_\ell,\ x_r-x_\ell)-\theta_r$ is the direction from the *landmark to the robot*, off by π from the usual robot-to-landmark bearing; §8.4.2 (8.46) uses the correct $\mathrm{atan2}(y_\ell-y_r,x_\ell-x_r)-\theta_r$. The Jacobian rows are identical either way, but the predicted measurement differs by π — fatal without angle wrapping/consistency.
3. **p. 286, (8.34):** "$y(k+1)=[0\ 1]x(k)+w(k)=Gx(k)+w(k)$" should be $y(k)=Hx(k)+w(k)$ with $H=[0\ 1]$ ($G$ is the input matrix).
4. **p. 293:** $\chi^2_{ij}=\nu_{ij}S_{ij}^{-1}\nu_{ij}^T$ has the transposes swapped (ν is a column) → $\nu^TS^{-1}\nu$. **p. 297 (8.48)** omits the inverse: $\chi^2=(y_i-h_j)^TS_{ij}^{-1}(y_i-h_j)$.
5. **p. 278:** in the $P(k+1|k)$ expansion the cross term is written $2F(x-\hat x)v^T$; it is $F(x-\hat x)v^T+v(x-\hat x)^TF^T$ (both vanish in expectation, so (8.12) is right).
6. **p. 298 Problem 7:** uses $P_k$ for the *updated* covariance $P(k+1|k+1)$ and a non-symmetric "$-2R\nu(\cdot)^T$" cross term; it is the noise-free derivation (no $+RWR^T$ term).

## Ch 9 Bayesian Methods
(PDF pp. 315–362, book pp. 301–348) — Lessons: A6 (primary), A5, F8, C6 (ICP in scan matching)

**Big idea:** Drop the Gaussian assumption. Keep the same predict/update recursion but represent the belief non-parametrically — as a histogram over a grid or as a weighted particle set — so it can be multimodal (global localisation, kidnapped robot, unknown data association). Then flip the problem: with known poses, mapping is a per-cell Bayes filter in log-odds; with unknown poses (SLAM), sample robot *paths* with a particle filter and compute each particle's map analytically (Rao-Blackwellisation).

**Key definitions:** position tracking vs **global localisation** vs **kidnapped-robot** problem (§9.1); data-association problem (why doors that look alike defeat a single Gaussian, Fig 9.1); posterior/belief $P(x(k)\mid u(0{:}k-1),y(1{:}k))$; prior; **motion model** $P(x(k)\mid u(k-1),x(k-1))$ (transition probability); **observation model** $P(y(k)\mid x(k))$; normaliser $\eta(k)$. Synchronised data order $u(0),y(1),u(1),y(2),\dots$; $u$ may be commands, odometry, or a fused estimate. The map $m$ is implicit background knowledge.

**Key results & equations:**
- **Recursive Bayes filter** (9.1 discrete, 9.2 continuous):
  $$P(x_k\mid u_{0:k-1},y_{1:k})=\eta_k\,P(y_k\mid x_k)\int P(x_k\mid u_{k-1},x_{k-1})\,P(x_{k-1}\mid u_{0:k-2},y_{1:k-1})\,dx_{k-1}$$
  **Prediction** (9.3) = the sum/integral (convolution with the motion model); **update** (9.4) multiply by likelihood and normalise with $\eta_k=\big[\sum_{x}P(y_k\mid x)P(x\mid\cdot)\big]^{-1}$ (9.5–9.6, law of total probability). The KF is the special case with linear-Gaussian models (Problem 2).
- **Derivation (§9.1.3, Eqs 9.7–9.15)** — the three assumptions to be able to state on an exam: (i) Bayes' rule with background knowledge (9.7); (ii) **Markov/conditional independence of measurements**: given $x_k$, $y_k$ is independent of past $y,u$ (9.8); (iii) **Markov motion**: $x_k$ depends only on $x_{k-1},u_{k-1}$ (9.11); (iv) the "future control carries no information about the past state" step $P(x_{k-1}\mid u_{0:k-1},\dots)=P(x_{k-1}\mid u_{0:k-2},\dots)$ (9.13) — the book's two-rooms counter-example shows it is an approximation that holds for small steps.
- **Initial belief:** uniform for global localisation, narrow Gaussian for tracking.

**Representations of the posterior (§9.1.4):**
1. **Kalman/EKF** — unimodal, efficient, high-dim OK, needs known start or unique landmarks; Gaussian mixtures as an extension.
2. **Grid (Markov) localisation** — topological (junctions, doors, GVD) or fine metric grid over $(x,y,\theta)$, 10–30 cm and 2–10° cells (Fig 9.2). **Algorithm 16**: for each step, $P'(x)=\sum_{x'}P(x\mid u,x')P(x')$; $P(x)\leftarrow P(y\mid x)P'(x)$; normalise. Naive prediction is $O(N^2)$; Fox–Burgard–Thrun trick: **shift** each θ-slice by the odometry $(\Delta x,\Delta y)$ (depends on θ) then **convolve** with a separable kernel, e.g. $[0.25,0.5,0.25]$ per axis (9.16), boundary weights $2/3,1/3$, repeated to match motion noise. Statistics: mean by weighted sums, **circular mean** for angle $\hat\phi=\mathrm{atan2}(\sum P_i\sin\phi_i,\sum P_i\cos\phi_i)$ (9.17); mode by argmax (mean of a bimodal belief may be inside an obstacle). AAAI'94 arena demo (Figs 9.3–9.4: 31×22 m, 15 cm/3°, 80 s on a PII; unique peak with 99 % mass after 24 sonar sweeps). Downsides: exponential in dimension; wasted cells → selective updating, tree-based grids.
3. **Particle filter / Monte Carlo localisation** — belief = $N$ samples $(x_j,\omega_j)$. **Algorithm 17 (SIR):** for each step: sample $x_j\sim P(x\mid u,x_j)$; weight $w_j=P(y\mid x_j)$; normalise; **resample**. **Algorithm 18 (low-variance/systematic resampling, O(N)):** draw one $\Delta\sim U(0,1/N]$, pointers $u_j=\Delta+j/N$, walk the cumulative weight sum $c$; every selected particle gets weight $1/N$ (vs O(N log N) binary-search resampling of Isard–Blake).
   - **Odometry motion model** (Fig 9.5, Eqs 9.18–9.21): motion = rotate α, translate d, rotate β; perturb each with Gaussian noise whose std scales with the motion: $\alpha'=\alpha+\alpha\,\mathcal N(\sigma_1)+d\,\mathcal N(\sigma_2)$, $\beta'=\beta+\beta\,\mathcal N(\sigma_3)+d\,\mathcal N(\sigma_4)$, $d'=d+d\,\mathcal N(\sigma_5)+(\alpha+\beta)\,\mathcal N(\sigma_6)$; new pose $[x+d'\cos(\theta+\alpha'),\ y+d'\sin(\theta+\alpha'),\ \theta+\alpha'+\beta']$. Without sensing the cloud grows and bends ("banana", Fig 9.6). Map knowledge: reject samples that pass through obstacles.
   - Mean = weighted average (use circular mean for θ); mode = max weight *before* resampling, or kd-tree / coarse histogram after.
   - Demos: global localisation with 10 000 particles (Fig 9.7: converged after 65 steps), tracking with the Fig 8.1 odometry (Fig 9.8).
   - **Failure recovery / kidnapping:** inject a fraction of uniformly random particles; or monitor $\tilde p=\frac1N\sum_jP(y\mid x_j)$ and re-initialise / adapt the number of random particles (Lenser–Veloso) / smooth $\tilde p$ (Gutmann–Fox — the idea behind augmented MCL).
- **Beam sensor model (§9.1.5):** $P(y\mid x)=P(y\mid d(x))$ with $d(x)$ the ray-cast expected range (9.22). Four effects (Fig 9.9 B21 sonar scan): (1) hit — Gaussian $\mathcal N(y;d,\sigma)$; (2) unmapped object/crosstalk — exponential $\lambda e^{-\lambda y}$ (short readings); (3) random — uniform $\gamma$; (4) max-range — point mass $\delta$. Discretised histogram per expected distance $d_i$ (9.23): $h_{i,j}=\int_{d_j-\Delta/2}^{d_j+\Delta/2}(\alpha_i\mathcal N+\beta_i\lambda_ie^{-\lambda_iy}+\gamma_i)\,dy$ for $j<n-1$, $h_{i,n-1}=\delta_i$, with $\sum_jh_{i,j}=1$ (9.24); parameters by **maximum likelihood** (Figs 9.10–9.11: laser ≪ sonar variance; σ also absorbs map uncertainty). Multiple beams assumed independent, $P(y_{1:m}\mid x)=\prod P(y_i\mid x)$ (9.25) — overconfident, so subsample beams (60 of 181). Speed-ups: precomputed expected-distance lookup table (1 byte/entry, 256×256 histograms); handle non-smooth likelihood near doorways/grazing walls by modelling the variance of the expected distance. Alternatives: endpoint/likelihood-field models (Konolige, Thrun) avoid ray casting.

**Mapping (§9.2):**
- **Occupancy grids with known poses (§9.2.1, Eqs 9.26–9.39, Alg 19):** each cell $m_l$ is a binary variable, cells independent (9.40). Bayes twice + odds ratio ⇒
  $$\mathrm{Odds}(m\mid x_{1:k},y_{1:k})=\frac{\mathrm{Odds}(m\mid x_k,y_k)\ \mathrm{Odds}(m\mid x_{1:k-1},y_{1:k-1})}{\mathrm{Odds}(m)}\quad(9.34)$$
  i.e. **log-odds add** (9.35): $l_k=\log\mathrm{Odds}(m\mid x_k,y_k)-\log\mathrm{Odds}(m)+l_{k-1}$; with prior 0.5 the prior term vanishes (9.36). Recover $P=1-1/(1+e^{l})$ (9.37–9.39). Alg 19 iterates (9.39). **Inverse sensor model** $P(m_l\mid x_k,y_k)$ for a sonar cone (9.41–9.42, Figs 9.12–9.16): prior minus $s(y,\theta)$ for cells well before the return ($d<y-d_1$), linear ramp up through $y\pm d_1$, plateau prior $+s$ until $y+d_2$, linear decay to prior by $y+d_3$, prior beyond; $s(y,\theta)=g(y)\,\mathcal N(\theta;0,\sigma_\theta)$, cone ±π/24 (15° opening), $s\approx0.16$ at 2 m on-axis. Corridor demo (Figs 9.17–9.18), ML map by thresholding at 0.5. Limitations: cell independence, binary cells, beam independence ⇒ overconfidence.
- **Bayesian SLAM (§9.2.2):** families: EKF-SLAM (Ch 8), global optimisation (Lu–Milios spring network = graph SLAM; EM, batch). Full posterior (9.43) $P(x_{1:k},m\mid u,y)=\alpha P(y_k\mid x_k,m)\int P(x_k\mid u_{k-1},x_{k-1})P(x_{1:k-1},m\mid\dots)$. **Incremental scan matching** (9.44): $\hat x_k=\arg\max_x P(y_k\mid x,\hat m_{k-1})P(x\mid u_{k-1},\hat x_{k-1})$ — e.g. **ICP** (C6!) — locally excellent, globally inconsistent because past poses are never revised (Figs 9.19–9.20, 28×28 m, 491 m travelled). **Rao-Blackwellised PF (Murphy; FastSLAM lineage):** factor (9.45–9.47) $P(x_{1:k},m\mid y,u)=P(m\mid x_{1:k},y_{1:k})\,P(x_{1:k}\mid y,u)$; particles = path hypotheses, each with its own map computed analytically (9.48) by §9.2.1; weight = $P(y_k\mid x_j,m^{(j)})$. **Algorithm 20.** Engineering: share map history in trees (Montemerlo; Parr–Eliazar), local-map approximation for constant time per particle, scan-matching-corrected odometry to need fewer particles (Hähnel). Loop closure makes inconsistent particles die out (Figs 9.22–9.23). Graphical model Fig 9.21.

**Algorithms:** 16 (grid localisation), 17 (MCL/SIR), 18 (low-variance resampling), 19 (occupancy-grid update), 20 (RBPF SLAM).

**Worked examples worth redoing:** Fig 9.1 1-D door world (do it as a 1-D histogram filter in NumPy); the low-variance resampler on 5 hand-picked weights; the inverse sonar model plot Fig 9.15.

**Exercises worth doing (book pp. 345–347) — the A6 set:**
- **P6 (histogram filter by hand):** 10-place ring, landmarks at 0, 3, 6, $P(\text{see}\mid\text{landmark})=0.8$, else 0.4; sequence see → +3 → see → +4 → no-see. My NumPy answer (uniform prior, deterministic motion): $P=[0.10,0.075,0.075,0.05,0.15,0.075,0.025,\mathbf{0.30},0.075,0.075]$ — most likely place 7 (start at 0 → 3 → 7). **P7** (nondeterministic motion, 0.8 move / 0.2 stay): belief flattens, max ≈0.15 at place 9 (0.149 at 7). **P11** same with particles.
- **P4–P5:** implement the (9.19–9.21) motion model and full MCL (Algs 17–18) — essentially ASBR Assignment 5.
- **P3:** build the beam-model histograms (9.23).
- **P9:** near-perfect sensors make $P(y\mid x)$ spiky ⇒ particle depletion; need more particles or an inflated sensor σ — classic MCL pitfall.
- **P13:** log-odds arithmetic: $\log\frac{0.8}{0.2}+2\log\frac{1/3}{2/3}=\log4-\log4=0$ per triple ⇒ the cell stays at its prior 0.5 (bounded oscillation).
- **P1, P8:** Bayes with background knowledge; why $y_k\perp y_{1:k-1}\mid x_k,m$ but not without $m$.
- **P2:** rederive the KF from (9.2).

**Conventions / pitfalls:**
- Choset indexes controls from 0 and measurements from 1: $u(k-1)$ moves $x(k-1)\to x(k)$, then $y(k)$ is taken.
- Weights in Alg 17 are *not* multiplied by previous weights because resampling every step resets them to $1/N$; if you resample only when $N_{\text{eff}}=1/\sum w_j^2$ is low, you must use $w_j\leftarrow w_jP(y\mid x_j)$.
- Alg 18 requires *normalised* weights and stays O(N); it keeps all particles when weights are equal (low variance).
- Always average θ with the circular mean; never arithmetic-average angles.
- Multiplying many beam likelihoods underflows — work in log-likelihoods and subtract the max before exponentiating.

**Book errata found in Ch 9:**
1. **p. 319, (9.18):** printed as $x^i:=x^i+[x_r^i+d'\cos(\theta_r^i+\alpha'),\dots]$ — this would add the pose twice. Correct: $x^i:=[x_r^i+d'\cos(\theta_r^i+\alpha'),\ y_r^i+d'\sin(\theta_r^i+\alpha'),\ \theta_r^i+\alpha'+\beta']^T$.
2. **p. 304:** "$P(x(k)\mid u(1{:}k-1),y(1{:}k))$ is usually called the posterior" — index should be $u(0{:}k-1)$; **p. 306:** "different ways to represent the posterior $P(x(k)\mid u(k-1),x(k-1))$" — that is the motion model, not the posterior.
3. **p. 329, (9.30):** "posterior probability that **all cells** of $m$ are free" — the derivation is per cell ($\neg m_l$), not for the whole map.
4. **p. 324:** "$\mathcal N(y,d,\sigma)$ … the variance σ" — σ is the standard deviation.

