# Modern Robotics (Lynch & Park, May 2017 preprint) — Part 2: end of Ch 5 → start of Ch 11
Source: 5a2ab312-MR.pdf, PDF pages 216–430 (book pages 198–412). Page offset: **PDF page = book page + 18**.
All page numbers below are **book** page numbers (add 18 for the PDF page).

Coverage map: §5.4 (tail: manipulability measures, force ellipsoid) · §5.5–5.8 (summary, software, exercises) · **Ch 6 Inverse Kinematics** (pp. 219–244) · **Ch 7 Closed Chains** (pp. 245–270) · **Ch 8 Dynamics of Open Chains** (pp. 271–324) · **Ch 9 Trajectory Generation** (pp. 325–352) · **Ch 10 Motion Planning** (pp. 353–402) · **Ch 11 Robot Control** §11.1–11.2.1 (pp. 403–412).

---

## §5.4 (tail) Manipulability & force ellipsoids; §5.5–5.8 Summary/Software/Exercises (pp. 198–218) — Lessons: R5
**Big idea:** The Jacobian maps the unit sphere of joint rates to an ellipsoid of end-effector velocities; the same Jacobian (transposed) maps the unit sphere of joint torques to a *force* ellipsoid whose axes are the same directions but with reciprocal lengths. What is easy to move in is hard to push in, and vice versa — the product of the two ellipsoid volumes is constant.

**Key definitions & results**
- Manipulability ellipsoid: $\dot q^T A^{-1}\dot q = 1$ with $A = JJ^T$; principal axes = eigenvectors $v_i$ of $A$, semi-axis lengths $\sqrt{\lambda_i}$ (Fig 5.13, p. 198).
- Split $J$ into $J_\omega$ (top 3 rows, angular) and $J_v$ (bottom 3 rows, linear) because units differ → two 3-D ellipsoids with $A=J_\omega J_\omega^T$ and $A=J_vJ_v^T$. For the linear ellipsoid prefer the **body** Jacobian $J_b$ (velocity of the end-effector origin, not of the point at the {s} origin) (p. 198).
- Scalar measures (p. 198–199):
  - $\mu_1(A)=\sqrt{\lambda_{max}/\lambda_{min}}\ge 1$ (isotropy; 1 is ideal, → ∞ at singularity)
  - $\mu_2(A)=\lambda_{max}/\lambda_{min}=\mu_1^2$ = **condition number** of $A=JJ^T$
  - $\mu_3(A)=\sqrt{\lambda_1\lambda_2\cdots}=\sqrt{\det A}$ ∝ volume; **larger is better** (unlike $\mu_1,\mu_2$).
- Force ellipsoid (p. 199): from $\tau=J^TF$ and $\|\tau\|=1$: $f^T JJ^T f = f^T B^{-1} f = 1$ with $B=(JJ^T)^{-1}=A^{-1}$. Same axes, semi-axes $1/\sqrt{\lambda_i}$. $V_A V_B$ = const, so near a singularity $V_A\to 0$, $V_B\to\infty$.
- Summary (p. 200–201): $J_{si}=\mathrm{Ad}_{e^{[S_1]\theta_1}\cdots e^{[S_{i-1}]\theta_{i-1}}}(S_i)$, $J_{bi}=\mathrm{Ad}_{e^{-[B_n]\theta_n}\cdots e^{-[B_{i+1}]\theta_{i+1}}}(B_i)$; $J_s=[\mathrm{Ad}_{T_{sb}}]J_b$; $\tau=J_b^TF_b=J_s^TF_s$. Common 6-dof singularities: (i) two collinear revolute axes; (ii) three coplanar parallel revolute axes; (iii) four revolute axes through a common point; (iv) four coplanar revolute axes; (v) six revolute axes intersecting a common line.
- Software: `JacobianBody(Blist,thetalist)`, `JacobianSpace(Slist,thetalist)`.

**Exercises worth doing (pp. 202–218)**
- **5.2, 5.3** (planar statics $\tau=J^TF$; 4R singularities) — direct RDKDC HW style.
- **5.7, 5.8, 5.11, 5.12** (RRP/RPR/3R/RRRP space & body Jacobians, statics, torque-limited max force) — good R5 drill.
- **5.10** — derives the SO(3) "analytic Jacobian" $\omega_b = A(r)\dot r$, $A(r)=I-\frac{1-\cos\|r\|}{\|r\|^2}[r]+\frac{\|r\|-\sin\|r\|}{\|r\|^3}[r]^2$ (p. 208). Important for R1/R5 (exp-coordinate rates vs angular velocity).
- **5.14** — prismatic axis normal to plane of two parallel revolute axes ⇒ singular.
- **5.22** — recursive link twists $\mathcal V_{k+1}$ from $\mathcal V_k$ (prep for Newton–Euler, Ch 8).
- **5.23–5.26** — program 2R manipulability/force ellipses and $\mu_1,\mu_2,\mu_3$ (5.23(b): ratio depends only on $\theta_2$); UR5 (5.25) and WAM (5.26) ellipsoids. **Very close to the JHU RDKDC UR5 manipulability assignment.**

**Pitfalls:** $\mu_2$ is the condition number of $JJ^T$, *not* of $J$ (cond($J$) $=\mu_1$). Mixed angular/linear $J$ has unit-inconsistent eigenvalues — split it.

---

## Ch 6 Inverse Kinematics (pp. 219–244) — Lessons: R6 (main), F6, F4, A1
**Big idea:** Forward kinematics is a function; inverse kinematics is not — there may be zero, finitely many (generic 6-dof: up to 16 for general 6R), or infinitely many (redundant) solutions. Special geometries (PUMA, Stanford: spherical wrist) decouple into an inverse-position problem for the wrist center plus an inverse-orientation (Euler-angle) problem. Everything else is solved numerically with Newton–Raphson, where the clever step is defining the "error" as the **body twist that would carry the current frame to the goal in unit time**, $[\mathcal V_b]=\log(T_{sb}^{-1}T_{sd})$.

### 6.0 Planar 2R (pp. 219–221)
- FK Eq (6.1): $x=L_1c_1+L_2c_{12}$, $y=L_1s_1+L_2s_{12}$. Workspace (for $L_1>L_2$): annulus $[L_1-L_2, L_1+L_2]$; 0/1/2 solutions for exterior/boundary/interior.
- `atan2(y,x)` ∈ $(-\pi,\pi]$ (four-quadrant).
- Law of cosines: $\beta=\cos^{-1}\frac{L_1^2+L_2^2-x^2-y^2}{2L_1L_2}$ (interior angle at elbow, $\beta\in[0,\pi]$), $\alpha=\cos^{-1}\frac{x^2+y^2+L_1^2-L_2^2}{2L_1\sqrt{x^2+y^2}}$, $\gamma=\mathrm{atan2}(y,x)$.
- **Righty:** $\theta_1=\gamma-\alpha,\ \theta_2=\pi-\beta$. **Lefty:** $\theta_1=\gamma+\alpha,\ \theta_2=\beta-\pi$.
- Note: book states "if $x^2+y^2$ lies outside $[L_1-L_2,L_1+L_2]$" — it should be $\sqrt{x^2+y^2}$ (the distance, not its square) (p. 221). 3-link planar chains are redundant for position → infinitely many solutions.

### 6.1 Analytic IK (pp. 221–226)
FK form: $T(\theta)=e^{[S_1]\theta_1}\cdots e^{[S_6]\theta_6}M$.

**6.1.1 6R PUMA-type** (zero position: axis 1 along $\hat z_0$, axis 2 along $-\hat y_0$, axis 3 ∥ axis 2, wrist axes 4,5,6 along $\hat z_0,\hat y_0,\hat x_0$ intersecting at the wrist center; link lengths $a_2,a_3$).
- Wrist center $p=(p_x,p_y,p_z)$. Zero offset: $\theta_1=\mathrm{atan2}(p_y,p_x)$; second solution $\theta_1+\pi$ with $\theta_2\to\pi-\theta_2$. **Singular when $p_x=p_y=0$** (wrist center on axis 1; Fig 6.4) → infinitely many $\theta_1$.
- Shoulder offset $d_1$: $\theta_1=\phi-\alpha$, $\phi=\mathrm{atan2}(p_y,p_x)$, $\alpha=\mathrm{atan2}(d_1,\sqrt{r^2-d_1^2})$; second: $\theta_1=\pi+\mathrm{atan2}(p_y,p_x)+\mathrm{atan2}(-\sqrt{p_x^2+p_y^2-d_1^2},d_1)$.
- Elbow: $\cos\theta_3=\frac{p_x^2+p_y^2+p_z^2-d_1^2-a_2^2-a_3^2}{2a_2a_3}=D$, $\theta_3=\mathrm{atan2}(\pm\sqrt{1-D^2},D)$ (use atan2, not acos).
- $\theta_2=\mathrm{atan2}(p_z,\sqrt{r^2-d_1^2})-\mathrm{atan2}(a_3s_3,a_2+a_3c_3)$.
- 4 position solutions (lefty/righty × elbow up/down), Fig 6.5.
- Orientation, Eq (6.2): $e^{[S_4]\theta_4}e^{[S_5]\theta_5}e^{[S_6]\theta_6}=e^{-[S_3]\theta_3}e^{-[S_2]\theta_2}e^{-[S_1]\theta_1}XM^{-1}$; with $\omega_4=\hat z,\omega_5=\hat y,\omega_6=\hat x$ the rotation part $R=\mathrm{Rot}(\hat z,\theta_4)\mathrm{Rot}(\hat y,\theta_5)\mathrm{Rot}(\hat x,\theta_6)$ = **ZYX Euler angles** (Appendix B). Wrist gives 2 solutions generically → up to 8 total.

**6.1.2 Stanford-type (RRPRRR)** (p. 225–226): $\theta_1=\mathrm{atan2}(p_y,p_x)$, $\theta_2=\mathrm{atan2}(s,r)$ with $r^2=p_x^2+p_y^2$, $s=p_z-d_1$; second solution $\theta_1+\pi$, $\theta_2=\pi-\mathrm{atan2}(s,r)$; $(\theta_3+a_2)^2=r^2+s^2\Rightarrow \theta_3=\sqrt{p_x^2+p_y^2+(p_z-d_1)^2}-a_2$ (discard negative root).

### 6.2 Numerical IK (pp. 226–232)
**6.2.1 Newton–Raphson** for $g(\theta)=0$: $\theta^{k+1}=\theta^k-\left(\frac{\partial g}{\partial\theta}(\theta^k)\right)^{-1}g(\theta^k)$; stop e.g. when $|g(\theta^k)-g(\theta^{k+1})|/|g(\theta^k)|\le\epsilon$.

**6.2.2 Coordinate version.** $g(\theta)=x_d-f(\theta)$. Taylor Eq (6.3) → Eq (6.4) $J(\theta^0)\Delta\theta=x_d-f(\theta^0)$ → Eq (6.5) $\Delta\theta=J^{-1}(x_d-f(\theta^0))$ → Eq (6.6) $\Delta\theta=J^\dagger(\theta^0)(x_d-f(\theta^0))$.
- Converges to the root whose **basin of attraction** contains the guess; near a plateau ($J\approx0$) the step is huge and may diverge (Fig 6.7).
- Don't form $J^{-1}$: solve with LU / `A\b`.
- **Pseudoinverse** properties (p. 229): $y^*=J^\dagger z$ is (a) the min-norm exact solution if one exists (fat $J$, redundant), or (b) the least-squares minimizer of $\|Jy-z\|$ (tall $J$ or singular). Full rank: $J^\dagger=J^T(JJ^T)^{-1}$ (fat, $n>m$, **right inverse** $JJ^\dagger=I$); $J^\dagger=(J^TJ)^{-1}J^T$ (tall, $n<m$, **left inverse** $J^\dagger J=I$).

**Algorithm (coordinate):**
```
init: x_d, guess θ0, i=0
e = x_d − f(θi)
while ||e|| > ε:
    θ(i+1) = θi + J†(θi) e ;  i += 1 ;  e = x_d − f(θi)
```
**Algorithm (body-twist, = MR `IKinBody`, p. 230–231):**
```
init: T_sd, guess θ0, i=0
[V_b] = log( T_sb(θi)^{-1} T_sd )          # = log T_bd
while ||ω_b|| > ε_ω  or  ||v_b|| > ε_v:
    θ(i+1) = θi + J_b†(θi) V_b ;  i += 1 ;  recompute V_b
```
- Key insight: $x_d-f(\theta)$ ↔ a *velocity* that reaches the goal in unit time; on SE(3) that is the body twist $\mathcal V_b$ with $[\mathcal V_b]=\log T_{bd}$, **not** $T_{sd}-T_{sb}$.
- Space form (`IKinSpace`): $\mathcal V_s=[\mathrm{Ad}_{T_{sb}}]\mathcal V_b$, step $\theta^{i+1}=\theta^i+J_s^\dagger\mathcal V_s$; tolerances then interpreted in the space frame.
- Practical: seed with the previous timestep's solution (or an approximate analytic solution, e.g. imperfect PUMA) and let $T_{sd}$ change slowly relative to IK rate.

**Worked example to redo — Example 6.1 (p. 231–232):** 2R, $L_1=L_2=1$, goal $\theta_d=(30°,90°)$, $(x,y)=(0.366,1.366)$; $M$ = translation (2,0,0), $B_1=(0,0,1,0,2,0)$, $B_2=(0,0,1,0,1,0)$; guess $(0,30°)$, $\epsilon_\omega=0.001$, $\epsilon_v=10^{-4}$. Iterates: $(34.23°,79.18°)\to(29.98°,90.22°)\to(30.00°,90.00°)$ — converges in 3 steps (quadratic convergence visible). Teaching point: first $v_{xb}$ is **positive** although the goal origin is in $-\hat x_b$, because $\mathcal V_b$ is a screw motion (rotation about a screw axis), not straight-line translation.

### 6.3 Inverse velocity kinematics (pp. 232–234) — R6 resolved-rate
- Option 1: differentiate IK numerically $\dot\theta=(\theta_d(k\Delta t)-\theta((k-1)\Delta t))/\Delta t$ (a feedback law).
- Option 2 (Eq 6.7, **resolved-rate**): $\dot\theta=J^\dagger(\theta)\mathcal V_d$, with $\mathcal V_d=T_{sd}^{-1}\dot T_{sd}$ (body, use $J_b$) or $\dot T_{sd}T_{sd}^{-1}$ (space, use $J_s$). Open-loop drifts → add position feedback (Ch 11).
- Redundant ($n>6$): $J^\dagger$ gives min $\|\dot\theta\|$ among the $(n-6)$-dim solution set.
- Weighted: minimize $\frac12\dot\theta^TM(\theta)\dot\theta+\nabla h^T\dot\theta$ s.t. $J\dot\theta=\mathcal V_d$. FONC: $J^T\lambda=M\dot\theta+\nabla h$, $\mathcal V_d=J\dot\theta$. Solution:
  $\dot\theta=G\mathcal V_d+(I-GJ)M^{-1}\nabla h$, $\lambda=B\mathcal V_d+BJM^{-1}\nabla h$, with $B=(JM^{-1}J^T)^{-1}$, $G=M^{-1}J^TB$.
  - **Sign check:** with this cost the null-space term is $+(I-GJ)M^{-1}\nabla h$ as printed; note that *minimising* $\nabla h^T\dot\theta$ should push *down* the gradient, so the physically sensible null-space term is $-(I-GJ)M^{-1}\nabla h$. Solving the KKT system yourself gives $\dot\theta=M^{-1}(J^T\lambda-\nabla h)$ ⇒ $\dot\theta=G\mathcal V_d-(I-GJ)M^{-1}\nabla h$ and $\lambda=B\mathcal V_d+BJM^{-1}\nabla h$. **The preprint's $\dot\theta$ expression has a sign error on the null-space term** (the $\lambda$ expression is consistent with the corrected $\dot\theta$). Use the minus sign in code.
  - $\lambda$ is a task-space wrench: $B\mathcal V_d$ "dynamic" part, $BJM^{-1}\nabla h$ static (e.g. gravity) part. $B$ is the task-space inertia (cf. Ch 8 $\Lambda$).
  - $h\equiv0$: $\dot\theta=M^{-1}J^T(JM^{-1}J^T)^{-1}\mathcal V_d$ = **mass-weighted pseudoinverse** (minimum kinetic energy).
- General null-space form (not in the book but standard and consistent with it): $\dot\theta=J^\dagger\mathcal V_d+(I-J^\dagger J)\dot\theta_0$.

### 6.4 Closed loops (p. 234)
Redundant-robot IK (numerical or rate-level) is generally **not cyclic**: a closed task-space loop $T_{sd}(0)=T_{sd}(t_f)$ need not return $\theta(t_f)=\theta(0)$. Extra conditions needed for repeatability.

### 6.5–6.7 Summary, software, notes (pp. 235–236)
- `IKinBody(Blist,M,T,thetalist0,eomg,ev)` / `IKinSpace(Slist,...)` → `[thetalist, success]` (fails after max iterations).
- General 6R: up to **16** solutions (Lee–Liang; Raghavan–Roth). Closed-form for more general chains via **Paden–Kahan subproblems** [Murray–Li–Sastry ref 122, Paden 129] — the book only mentions them; for RDKDC use MLS Ch 3.
- Summary typo (p. 235): iteration printed as "$\dot\theta^{i+1}=\theta^i+J^\dagger V$" — should be $\theta^{i+1}$ (no dot).

**Exercises worth doing (pp. 236–244)**
- **6.1** analytic IK of planar 3R ($L=3,2,1$, $(4,2,0)$) — classic; 2 solutions generically.
- **6.7** Newton basin-of-attraction sketch (jumping over the near root).
- **6.8** 2-D Newton on $g=(x^2-4,y^2-9)$ from (1,1); 4 roots.
- **6.9** instrument `IKinBody` to print the iteration table (goal (90°,120°)) — exactly the kind of thing JHU RDKDC asks.
- **6.10** numerical IK of a 3R wrist on SO(3) (log on SO(3) instead of SE(3)).
- **6.12 (UR5 with IKinSpace)**, **6.13 (WAM with IKinBody)** — matches RDKDC UR5 IK labs; note solutions may thrash and violate joint limits; wrap to [0,2π).
- **6.14** proof of Lagrange multipliers via the fundamental theorem of linear algebra (F6).
- **6.15** block-matrix inverse → closed-form equality-constrained QP $\min\frac12x^TQx+c^Tx$ s.t. $Hx=b$ (F6; same structure as §6.3).

**Conventions / pitfalls**
- MR $\mathcal V=(\omega,v)$ (angular first) — opposite to Murray–Li–Sastry $\xi=(v,\omega)$. A $J^\dagger$ step mixing conventions silently fails.
- $\|\omega_b\|$ and $\|v_b\|$ are thresholded separately (different units).
- Newton–Raphson IK = Gauss–Newton when $J^\dagger$ is used on non-square $J$; it is **not** gradient descent/J-transpose (that is $\Delta\theta=\alpha J^T e$, which MR does not present).
- Near singularities $J^\dagger$ blows up — MR offers no damping; damped least squares $J^T(JJ^T+\lambda^2I)^{-1}$ is the standard fix (outside MR).

---

## Ch 7 Kinematics of Closed Chains (pp. 245–270) — Lessons: R5 (singularities, statics), R6 (IK contrast); peripheral to JHU courses
**Big idea:** For parallel mechanisms the roles of forward and inverse kinematics swap: IK (platform pose → actuator values) is usually easy and unique, while FK (actuators → pose) is a polynomial system with many solutions. Closed chains have passive joints and loop-closure constraints, so velocity analysis needs a *constraint Jacobian* in addition to the forward Jacobian, and new kinds of singularities appear.

**Key definitions**
- Closed chain = any chain with ≥1 loop; parallel mechanism = fixed + moving platform joined by legs. Exactly actuated vs **redundantly actuated** (more actuators than dof) (p. 245–246).
- Stewart–Gough platform (6×SPS, prismatic actuated) — motion simulator & 6-axis F/T sensor; Delta robot (3 dof, platform stays parallel, light moving parts).

**Key results & equations**
- **3×RPR planar** (p. 247–249): $d_i=p+R_{sb}b_i-a_i$ (Eq 7.1); $s_i^2=(p_x+b_{ix}c_\phi-b_{iy}s_\phi-a_{ix})^2+(p_y+b_{ix}s_\phi+b_{iy}c_\phi-a_{iy})^2$ (Eq 7.2). IK trivial; FK via tangent half-angle $t=\tan(\phi/2)$ ($\sin\phi=2t/(1+t^2)$, $\cos\phi=(1-t^2)/(1+t^2)$) → a single **6th-order polynomial ⇒ up to 6 FK solutions**. Singular configuration: legs equal and shortest (Fig 7.3) — platform may snap CW or CCW.
- **Stewart–Gough** (p. 249–250): $s_i^2=(p+Rb_i-a_i)^T(p+Rb_i-a_i)$, $i=1..6$; IK direct; FK = 12 eqs (6 legs + $R^TR=I$) in 12 unknowns; general 6–6 platform has **up to 40** FK solutions (Raghavan–Roth; Husty's algorithm) (p. 262–263).
- **General parallel mechanism** (p. 251): loop closure $T_1(\theta)=T_2(\phi)=T_3(\psi)$ (Eqs 7.3–7.4): 24 equations, 12 independent ⇒ dof $d=m+n+p-12$ (Grübler-like).
- **Stewart Jacobian via statics** (p. 253–254): leg force $f_i=\hat n_i\tau_i$ along the leg (spherical joints transmit no moment); moment taken about the *fixed* base joint $q_i$; $\mathcal F_s=\sum_i(q_i\times\hat n_i,\ \hat n_i)\tau_i=J_s^{-T}\tau$, i.e. $J_s^{-1}=\begin{bmatrix}-\hat n_1\times q_1&\cdots\\ \hat n_1&\cdots\end{bmatrix}^T$ — the **inverse** Jacobian (rows = leg line coordinates) comes for free; the forward one needs an inversion. Same duality $\tau=J^T\mathcal F$ as open chains.
- **General differential kinematics** (p. 254–256): $\dot T_iT_i^{-1}$ equal ⇒ $J_1\dot\theta=J_2\dot\phi$, $J_2\dot\phi=J_3\dot\psi$ (7.10–7.11) ⇒ $[H_a\ H_p][\dot q_a;\dot q_p]=0$ (7.13–7.14); if $H_p$ invertible, $\dot q_p=-H_p^{-1}H_a\dot q_a$ (7.15); then $\mathcal V_s=J_a(q)\dot q_a$ with $J_a=J_1\,[e_1^T; g_2^T;\dots;g_5^T]$ (7.21).

**Singularity classes (§7.3, pp. 256–261)** — four-bar ($\phi$ vs $\theta$ C-space curve with **bifurcation points**, Eq 7.22–7.25) and five-bar (2-D C-space surface in $\mathbb R^4$, Eqs 7.26–7.28):
1. **Configuration-space singularity**: $\mathrm{rank}\,H(q)<p$ (self-intersection of C-space; independent of actuator choice and end-effector).
2. **Actuator singularity**: $\mathrm{rank}\,H_p(q)<p$; *nondegenerate* (actuators can't be moved independently; may crush/buckle) vs *degenerate* (locking actuators doesn't rigidify). Removable by relocating actuators. C-space singularities = intersection of all actuator singularities over all actuator choices.
3. **End-effector singularity**: rank loss of $\partial f/\partial q_a$ for $f(q_a)=T_{sb}$ (Eq 7.30); depends on end-effector placement, not on actuators.

**Exercises worth doing:** 7.1 (3×RPR IK/FK/singularity), 7.2(c) (statics $\tau\to$ planar wrench), 7.4 (six-bar $H_a,H_p$ and $J_a$ — best drill for the $H$ machinery), 7.7 (Delta robot FK/IK/Jacobian).

**Conventions / pitfalls**
- Book p. 257 says a four-bar solution "will fail to exist if $\gamma^2\le\alpha^2+\beta^2$" — this is reversed: the $\cos^{-1}$ in Eq (7.22) requires $|\gamma|\le\sqrt{\alpha^2+\beta^2}$, so **no solution when $\gamma^2>\alpha^2+\beta^2$** (preprint typo).
- "Inverse Jacobian is easy" for Stewart ⇒ Stewart singularities are where that 6×6 line matrix loses rank (lines linearly dependent).

---

## Ch 8 Dynamics of Open Chains (pp. 271–324) — Lessons: R7 (main), R8 (passivity, task-space dynamics), F7 (ODE integration), F5
**Big idea:** $\tau=M(\theta)\ddot\theta+h(\theta,\dot\theta)$ (Eq 8.1) — linear in $\ddot\theta$, quadratic in $\dot\theta$, trigonometric in $\theta$. Lagrange gets it from one scalar (energy) and is great by hand for ≤3 dof; Newton–Euler gets the *same* equations by propagating twists out to the tip and wrenches back to the base, which is $O(n)$ and is how software (and MR's `InverseDynamics`) does it. Everything about inertia — of a link, of the arm, felt at the end-effector — is "kinetic energy is frame-independent" in disguise.

**Forward vs inverse dynamics:** forward = $\ddot\theta=M^{-1}(\tau-h)$ (Eq 8.2, simulation); inverse = $\tau$ from $(\theta,\dot\theta,\ddot\theta)$ (Eq 8.1, control).

### 8.1 Lagrangian formulation (pp. 272–282)
- Generalized coordinates $q$, generalized forces $f$ with $f^T\dot q$ = power. $\mathcal L=\mathcal K-\mathcal P$; **Eq (8.3)** $f=\frac{d}{dt}\frac{\partial\mathcal L}{\partial\dot q}-\frac{\partial\mathcal L}{\partial q}$.
- Particle on vertical line: $f=m\ddot x+mg$ (Eqs 8.4–8.6).
- **2R planar arm with point masses at link tips, gravity $-\hat y$ (Fig 8.1; Eqs 8.9–8.10, p. 275)** — *redo by hand; this is the canonical R7 exam problem*:
  $M=\begin{bmatrix}m_1L_1^2+m_2(L_1^2+2L_1L_2c_2+L_2^2) & m_2(L_1L_2c_2+L_2^2)\\ m_2(L_1L_2c_2+L_2^2) & m_2L_2^2\end{bmatrix}$,
  $c=\begin{bmatrix}-m_2L_1L_2s_2(2\dot\theta_1\dot\theta_2+\dot\theta_2^2)\\ m_2L_1L_2s_2\dot\theta_1^2\end{bmatrix}$,
  $g=\begin{bmatrix}(m_1+m_2)L_1g\,c_1+m_2gL_2c_{12}\\ m_2gL_2c_{12}\end{bmatrix}$.
- Same terms via $f_i=m_ia_i$ (Eqs 8.11–8.12) and moments $r\times f$ about each joint. **Centripetal** terms ∝ $\dot\theta_i^2$, **Coriolis** ∝ $\dot\theta_i\dot\theta_j$ ($i\neq j$); $\ddot\theta=0$ does *not* mean zero acceleration of the masses. Fig 8.2 intuition: at $(0,\pi/2)$ the Coriolis acceleration $(0,-2L_2\dot\theta_1\dot\theta_2)$ of $m_2$ passes through joint 2 (no torque there) but needs negative torque at joint 1 (skater pulling in arms).
- **General form** (Eqs 8.14–8.17): $\mathcal K=\frac12\dot\theta^TM(\theta)\dot\theta$; $\tau_i=\sum_jm_{ij}\ddot\theta_j+\sum_{j,k}\Gamma_{ijk}\dot\theta_j\dot\theta_k+\partial\mathcal P/\partial\theta_i$, **Christoffel symbols of the first kind** $\Gamma_{ijk}=\frac12\left(\frac{\partial m_{ij}}{\partial\theta_k}+\frac{\partial m_{ik}}{\partial\theta_j}-\frac{\partial m_{jk}}{\partial\theta_i}\right)$. Forms: $M\ddot\theta+c+g$, $M\ddot\theta+\dot\theta^T\Gamma\dot\theta+g$ (8.18), $M\ddot\theta+C(\theta,\dot\theta)\dot\theta+g$ with **Coriolis matrix** $c_{ij}=\sum_k\Gamma_{ijk}\dot\theta_k$ (8.19).
- **Prop 8.1 (passivity / skew symmetry):** $\dot M-2C$ is skew-symmetric (with $C$ built from Christoffel symbols as above — *not* for an arbitrary factorization of $c$). Used for stability proofs of PD+gravity / passivity controllers (§11.4.2.2) → R8.
- **8.1.3 Mass matrix intuition** (pp. 279–281): unit ball of $\ddot\theta$ maps through $M$ to a torque ("mass") ellipsoid — axes = eigvecs of $M$, semi-axes = eigenvalues (not square roots). 2R with $L=m=1$: $M(0,90°)=\begin{bmatrix}3&1\\1&1\end{bmatrix}$, $M(0,150°)=\begin{bmatrix}1.27&0.13\\0.13&1\end{bmatrix}$ (Fig 8.3). **End-effector mass matrix** Eq (8.21): $\Lambda=J^{-T}MJ^{-1}$ from $\frac12\dot\theta^TM\dot\theta=\frac12\mathcal V^T\Lambda\mathcal V$ (8.20). Fig 8.4: at (0,90°) $\Lambda=\mathrm{diag}(1,2)$ — pushing in $y$ feels both masses, in $x$ only $m_2$; at (0,150°) force (1,0) gives acceleration (0.4,0.35) (not parallel). Valid only at zero velocity. Relevance: haptics (light links).

### 8.2 Single rigid body (pp. 283–291)
- CoM frame: $\sum m_ir_i=0$. $\ddot p_i=\dot v_b+[\dot\omega_b]r_i+[\omega_b]v_b+[\omega_b]^2r_i$.
- **Linear (8.22):** $f_b=m(\dot v_b+[\omega_b]v_b)$ (velocity-product term because $v_b$ is in a rotating frame). **Euler (8.23):** $m_b=I_b\dot\omega_b+[\omega_b]I_b\omega_b$, $I_b=-\sum m_i[r_i]^2$. $\mathcal K_{rot}=\frac12\omega_b^TI_b\omega_b$.
- Inertia entries & integrals (8.24): $I_{xx}=\int(y^2+z^2)\rho\,dV$, $I_{xy}=-\int xy\rho\,dV$, etc. Principal axes = eigenvectors of $I_b$; principal-axis Euler eqs (8.25) $I_{xx}\dot\omega_x+(I_{zz}-I_{yy})\omega_y\omega_z$, …
- Fig 8.5 table: box $I_{xx}=m(w^2+h^2)/12$ …; cylinder (axis $z$) $I_{xx}=I_{yy}=m(3r^2+h^2)/12$, $I_{zz}=mr^2/2$; ellipsoid $I_{xx}=m(b^2+c^2)/5$ ….
- **Rotation of inertia (8.26):** $I_c=R_{bc}^TI_bR_{bc}$. **Steiner (Thm 8.2, Eq 8.27):** $I_q=I_b+m(q^Tq\,I-qq^T)$; parallel-axis $I_d=I_{cm}+md^2$ (8.28). Composite bodies: express each in a common frame, then sum.
- **Twist–wrench form** (8.29–8.40): spatial inertia $\mathcal G_b=\mathrm{diag}(I_b,mI)$ (8.32), $\mathcal K=\frac12\mathcal V_b^T\mathcal G_b\mathcal V_b$ (8.33), momentum $\mathcal P_b=\mathcal G_b\mathcal V_b$ (8.34). **Lie bracket** (Def 8.3, Eq 8.37–8.38): $[\mathrm{ad}_{\mathcal V}]=\begin{bmatrix}[\omega]&0\\ [v]&[\omega]\end{bmatrix}$, $\mathrm{ad}_{\mathcal V_1}(\mathcal V_2)=[\mathrm{ad}_{\mathcal V_1}]\mathcal V_2$ (6-D cross product; matrix commutator $[\mathcal V_1][\mathcal V_2]-[\mathcal V_2][\mathcal V_1]$). Def 8.4: $\mathrm{ad}^T_{\mathcal V}(\mathcal F)=(-[\omega]m-[v]f,\ -[\omega]f)$.
  **Eq (8.40):** $\boxed{\mathcal F_b=\mathcal G_b\dot{\mathcal V}_b-[\mathrm{ad}_{\mathcal V_b}]^T\mathcal G_b\mathcal V_b}$ (rotational part = (8.41) $m_b=I_b\dot\omega_b-[\omega_b]^TI_b\omega_b$).
- **Other frames** (8.42–8.43): $\mathcal G_a=[\mathrm{Ad}_{T_{ba}}]^T\mathcal G_b[\mathrm{Ad}_{T_{ba}}]$ (generalized Steiner); the equation keeps the same form in any frame.

### 8.3 Newton–Euler inverse dynamics (pp. 291–294) — MR `InverseDynamics`
Setup: frame {i} at CoM of link i; {0} base; {n+1} end-effector fixed in {n}. Home configurations $M_{i-1,i}=M_{i-1}^{-1}M_i$; screw axis of joint i in {i}: $\mathcal A_i=\mathrm{Ad}_{M_i^{-1}}(\mathcal S_i)$; $T_{i,i-1}(\theta_i)=e^{-[\mathcal A_i]\theta_i}M_{i,i-1}$.
```
Init: V_0 = 0 ; dV_0 = (0, −g)   # gravity as fictitious upward base acceleration
      F_{n+1} = F_tip  (wrench the end-effector applies to the environment, in {n+1})
Forward i=1..n:
  T_{i,i-1} = exp(−[A_i] θ_i) M_{i,i-1}                     (8.50)
  V_i  = Ad_{T_{i,i-1}}(V_{i-1}) + A_i θ̇_i                    (8.51)
  V̇_i = Ad_{T_{i,i-1}}(V̇_{i-1}) + ad_{V_i}(A_i) θ̇_i + A_i θ̈_i   (8.52)
Backward i=n..1:
  F_i = Ad^T_{T_{i+1,i}}(F_{i+1}) + G_i V̇_i − ad^T_{V_i}(G_i V_i)   (8.53)
  τ_i = F_i^T A_i                                              (8.54)
```
- Derivation of the velocity-product term: $\frac{d}{dt}[\mathrm{Ad}_{T_{i,i-1}}]\mathcal V_{i-1}=[\mathrm{ad}_{\mathcal V_i}]\mathcal A_i\dot\theta_i$ (p. 292). Free-body (8.48): $\mathcal G_i\dot{\mathcal V}_i-\mathrm{ad}^T_{\mathcal V_i}(\mathcal G_i\mathcal V_i)=\mathcal F_i-\mathrm{Ad}^T_{T_{i+1,i}}(\mathcal F_{i+1})$. Only the component along $\mathcal A_i$ is supplied by the actuator; the other 5 by joint structure.

### 8.4 Closed form (pp. 294–298)
- **Eq (8.57):** $M(\theta)=\sum_{i=1}^nJ_{ib}^T(\theta)\mathcal G_iJ_{ib}(\theta)$, $J_{ib}$ = body Jacobian of CoM frame {i} padded with zeros to $6\times n$ (proves $\mathcal K=\frac12\dot\theta^TM\dot\theta$, Eq 8.56). **Best way to compute M by hand for R7.**
- Stacked matrices $\mathcal A,\mathcal G,[\mathrm{ad}_{\mathcal V}],[\mathrm{ad}_{\mathcal A\dot\theta}],\mathcal W(\theta)$ (8.60–8.64), nilpotent $\mathcal W^n=0$, $\mathcal L=(I-\mathcal W)^{-1}$ lower-block-triangular with $[\mathrm{Ad}_{T_{ij}}]$ (8.72). Result:
  $\tau=M\ddot\theta+c+g+J^T\mathcal F_{tip}$ (8.77), $M=\mathcal A^T\mathcal L^T\mathcal G\mathcal L\mathcal A$ (8.78), $c=-\mathcal A^T\mathcal L^T(\mathcal G\mathcal L[\mathrm{ad}_{\mathcal A\dot\theta}]\mathcal W+[\mathrm{ad}_{\mathcal V}]^T\mathcal G)\mathcal L\mathcal A\dot\theta$ (8.79), $g=\mathcal A^T\mathcal L^T\mathcal G\mathcal L\dot{\mathcal V}_{base}$ (8.80).

### 8.5 Forward dynamics & simulation (pp. 298–300) — F7
- Solve $M\ddot\theta=\tau-h-J^T\mathcal F_{tip}$ (8.81). $h$ = RNEA with $\ddot\theta=0,\mathcal F_{tip}=0$; $M$ via (8.57) **or n RNEA calls** with $g=0,\dot\theta=0,\mathcal F_{tip}=0$, $\ddot\theta=e_i$ → column i.
- Euler integration (state $q_1=\theta,q_2=\dot\theta$): $\theta[k+1]=\theta[k]+\dot\theta[k]\delta t$, $\dot\theta[k+1]=\dot\theta[k]+\ddot\theta[k]\delta t$; RK4 better.

### 8.6 Task-space (operational-space, Khatib) dynamics (pp. 300–301) — R8
$\mathcal F=\Lambda(\theta)\dot{\mathcal V}+\eta(\theta,\mathcal V)$ (8.89), $\Lambda=J^{-T}MJ^{-1}$ (8.90), $\eta=J^{-T}h(\theta,J^{-1}\mathcal V)-\Lambda\dot JJ^{-1}\mathcal V$ (8.91). Needs invertible $J$; still depends on $\theta$ (IK not unique), not only on end-effector pose.

### 8.7 Constrained dynamics (pp. 301–303)
Pfaffian $A(\theta)\dot\theta=0$, $A\in\mathbb R^{k\times n}$; workless constraint forces $\tau_{con}=A^T\lambda$. $\tau=M\ddot\theta+h+A^T\lambda$ (8.93); $\lambda=(AM^{-1}A^T)^{-1}(AM^{-1}(\tau-h)-A\ddot\theta)$ (8.97); **$P\tau=P(M\ddot\theta+h)$**, $P=I-A^T(AM^{-1}A^T)^{-1}AM^{-1}$ (8.98–8.99, rank $n-k$); forward: $P_{\ddot\theta}\ddot\theta=P_{\ddot\theta}M^{-1}(\tau-h)$, $P_{\ddot\theta}=M^{-1}PM=I-M^{-1}A^T(AM^{-1}A^T)^{-1}A$ (8.100–8.101). Leads to hybrid motion–force control (§11.6).
- Preprint wording slip (p. 302): "for the writing robot, there is an *equality* constraint: the robot can only apply pushing forces" — should read **inequality** (unilateral contact).

### 8.8–8.9 URDF, motors, gearing, friction (pp. 303–315)
- URDF `<inertial>`: mass, origin (CoM frame w.r.t. joint frame), inertia (6 upper-triangular entries); joint `origin`, `axis`.
- DC motor: $\tau=k_tI$, $P_{heat}=I^2R$, voltage Eq (8.102) $V=k_tw+IR+L\,dI/dt$ ($k_e=k_t$ numerically in SI); **back-emf** $k_tw$. Speed–torque line $w=V/k_t-(R/k_t^2)\tau$; no-load speed $w_0=V_{max}/k_t$; stall torque $\tau_{stall}=k_tV_{max}/R$; continuous region $|\tau|\le\tau_{cont}$; quadrants 2/4 = motor acting as damper. 
- Gearing: $w_{gear}=w_{motor}/G$, $\tau_{gear}=\eta G\tau_{motor}$. **Reflected (apparent) inertia** $G^2I_{rotor}$ — large G makes $M(\theta)$ diagonal-dominant ⇒ decoupled joints (justifies independent-joint PID). Example 2R with motors: $G=10$: $M=\begin{bmatrix}4.13+2c_2&1.01+c_2\\1.01+c_2&1.13\end{bmatrix}$; $G=100$: $\begin{bmatrix}16.5+2c_2&1.13+c_2\\1.13+c_2&13.5\end{bmatrix}$ (p. 311).
- RNEA with rotors (Eqs 8.103–8.111): extra rotor bodies spinning at $G_i\dot\theta_i$; $\tau_{i,motor}=\tau_{i,gear}/G_i+\mathcal R_i^T(\mathcal G_{iR}\dot{\mathcal V}_{iR}-\mathrm{ad}^T_{\mathcal V_{iR}}(\mathcal G_{iR}\mathcal V_{iR}))$; current command $I_{com}=\tau_{motor}/(\eta k_t)$. Direct-drive robots = no gearing.
- Friction models (Fig 8.13): viscous $b\dot\theta$, Coulomb $b_s\,\mathrm{sgn}(\dot\theta)$, static+viscous, static>kinetic, Stribeck. Joint/link flexibility (harmonic drive flexspline) adds states.

### 8.11 Software
`ad`, `InverseDynamics(θ,θ̇,θ̈,g,Ftip,Mlist,Glist,Slist)`, `MassMatrix`, `VelQuadraticForces`, `GravityForces`, `EndEffectorForces`, `ForwardDynamics`, `EulerStep`, `InverseDynamicsTrajectory`, `ForwardDynamicsTrajectory`. Note MR's code takes `Mlist` = $M_{i-1,i}$ and `Slist` in the base frame.

**Exercises worth doing (pp. 321–324):** 8.1 (inertia formulas), 8.2 (dumbbell, composite inertia + Steiner), **8.4 (Furuta pendulum Lagrangian — R7 exam-style)**, 8.6 ($\partial J_i/\partial\theta_j=\mathrm{ad}_{J_i}(J_j)$ for $i>j$ (space) — analytic $\dot J$), 8.9 (inertia matching $G=\sqrt{I_{link}/I_{rotor}}$), 8.11/8.13 (efficient $h$ and $M$), **8.15 (UR5 falling under gravity — simulation lab)**.

**Conventions / pitfalls**
- MR wrench order $\mathcal F=(m,f)$ (moment first), consistent with $\mathcal V=(\omega,v)$; MLS/Spong differ.
- $\mathcal F_{tip}$ in MR = wrench **applied by** the robot **to** the environment, so it enters as $+J^T\mathcal F_{tip}$.
- Gravity enters RNEA as base acceleration $\dot{\mathcal V}_0=(0,-g)$ with $g$ the gravity vector (e.g. $(0,0,-9.81)$), so $\dot v_0=+9.81\hat z$.
- p. 287 typo: "columns of $R_{bc}$ correspond to the eigen**values** of $I_b$" → eigen**vectors**.
- $C(\theta,\dot\theta)$ is not unique; skew-symmetry of $\dot M-2C$ holds for the Christoffel choice.

---

## Ch 9 Trajectory Generation (pp. 325–352) — Lessons: R8 (reference trajectories for control), A1 (resolved-rate needs a $\mathcal V_d(t)$), F7
**Big idea:** Trajectory = **path** (geometry, $\theta(s)$, $s\in[0,1]$) ∘ **time scaling** ($s(t)$, $t\in[0,T]$). Separating them lets you plan geometry first (Ch 10) and then decide how fast to go — from simple polynomial/trapezoid profiles to the Bobrow/Shin–McKay time-optimal algorithm that rides the actuator limits in the $(s,\dot s)$ phase plane.

**Key definitions & equations**
- Eq (9.1) $\dot\theta=\frac{d\theta}{ds}\dot s$; Eq (9.2) $\ddot\theta=\frac{d\theta}{ds}\ddot s+\frac{d^2\theta}{ds^2}\dot s^2$. Both $\theta(s)$ and $s(t)$ must be $C^2$.
- **Joint-space straight line** (9.3–9.5): $\theta(s)=\theta_{start}+s(\theta_{end}-\theta_{start})$ — stays inside box joint limits (convex). Task-space straight lines may leave the reachable set or pass near singularities (huge joint rates) (Fig 9.1).
- **SE(3) "straight lines"**: $X_{start}+s(X_{end}-X_{start})\notin SE(3)$. Options:
  - **Screw path** Eq (9.6): $X(s)=X_{start}\exp(\log(X_{start}^{-1}X_{end})\,s)$ (post-multiply: twist is in the {start} frame). Constant screw axis; origin follows a helix, not a line. (MR `ScrewTrajectory`.)
  - **Decoupled** Eqs (9.7–9.8): $p(s)=p_{start}+s(p_{end}-p_{start})$, $R(s)=R_{start}\exp(\log(R_{start}^TR_{end})s)$ — origin straight, constant body-frame rotation axis. (MR `CartesianTrajectory`.)

**Time scalings (§9.2.2)**
| profile | formula | peaks | smoothness |
|---|---|---|---|
| **Cubic** (9.9–9.13) | $s=\frac{3}{T^2}t^2-\frac{2}{T^3}t^3$ | $\dot s_{max}=\frac{3}{2T}$ at $T/2$; $\ddot s_{max}=\frac{6}{T^2}$ at $t=0$, $-\frac6{T^2}$ at $T$ | accel jumps at ends → infinite jerk |
| **Quintic** (Ex 9.5, Fig 9.4) | $s=10\tau^3-15\tau^4+6\tau^5$, $\tau=t/T$ | $\dot s_{max}=\frac{15}{8T}$; $|\ddot s|_{max}=\frac{10}{\sqrt3\,T^2}$ (at $\tau=\frac{3\mp\sqrt3}{6}$) | zero end accel; finite jerk; higher peak velocity than cubic |
| **Trapezoidal** (9.16–9.24) | accel $a$ for $t_a=v/a$, coast $v$, decel $-a$ | — | accel steps at $0,t_a,T-t_a,T$ |
| **S-curve** (Fig 9.6) | 7 stages: +J, a, −J, coast, −J, −a, +J | — | bounded jerk |
- Cubic in joint space: $\dot\theta_{max}=\frac{3}{2T}(\theta_{end}-\theta_{start})$, $\ddot\theta_{max}=\frac{6}{T^2}(\theta_{end}-\theta_{start})$ → check against limits or solve for min $T$.
- Trapezoid: limits $|(\theta_{end}-\theta_{start})v|\le\dot\theta_{limit}$, $|(\theta_{end}-\theta_{start})a|\le\ddot\theta_{limit}$ (9.14–9.15); with max $v,a$ it is the **fastest straight-line motion** (Ex 9.8). If $v^2/a>1$ the coast phase disappears → triangular **bang-bang**. Three ways to fix two of $(v,a,T)$:
  - $v,a$ ($v^2/a\le1$): $T=\frac{a+v^2}{va}$;
  - $v,T$ ($1<vT\le2$): $a=\frac{v^2}{vT-1}$;
  - $a,T$ ($aT^2\ge4$): $v=\frac12(aT-\sqrt{a}\sqrt{aT^2-4})$.

**9.3 Via points (pp. 334–336)** — per joint $\beta$, cubic per segment $j$ with endpoint positions/velocities (Eq 9.25):
$a_{j0}=\beta_j$, $a_{j1}=\dot\beta_j$, $a_{j2}=\frac{3\beta_{j+1}-3\beta_j-2\dot\beta_j\Delta T_j-\dot\beta_{j+1}\Delta T_j}{\Delta T_j^2}$, $a_{j3}=\frac{2\beta_j+(\dot\beta_j+\dot\beta_{j+1})\Delta T_j-2\beta_{j+1}}{\Delta T_j^3}$ (9.26–9.29). Velocity-continuous but not accel-continuous (use quintics with via accelerations for that). Example: vias (0,0),(0,1),(1,1),(1,0) at T=0,1,2,3, velocities (0,0),(1,0),(0,−1),(0,0) (Fig 9.7–9.8). B-splines don't pass through vias but stay in their convex hull (good for limits/obstacles).

**9.4 Time-optimal time scaling (pp. 336–345)** — Bobrow et al. / Shin–McKay 1985.
- Path-constrained dynamics Eq (9.32–9.33): $m(s)\ddot s+c(s)\dot s^2+g(s)=\tau$ with $m(s)=M\frac{d\theta}{ds}$, $c(s)=M\frac{d^2\theta}{ds^2}+\frac{d\theta}{ds}^T\Gamma\frac{d\theta}{ds}$, $g(s)=g(\theta(s))$.
- Each actuator bound (9.35) → $L_i(s,\dot s)\le\ddot s\le U_i(s,\dot s)$ (sign of $m_i$ decides which bound is which; $m_i=0$ is a **zero-inertia point**) (9.36); $L=\max_iL_i$, $U=\min_iU_i$ (9.37).
- **Phase plane:** motion cone at each $(s,\dot s)$; inadmissible region where $L>U$, bounded by the **velocity limit curve** $\dot s_{lim}(s)$. $T=\int_0^1\dot s^{-1}(s)\,ds$ (9.39) ⇒ keep $\dot s$ as high as possible ⇒ always at $U$ or $L$ (bang-bang), choose switch points.
- **Algorithm (pp. 341–342):** (1) $S=\{\}$, start $(0,0)$. (2) Integrate $\ddot s=L$ *backward* from (1,0) → curve F. (3) Integrate $\ddot s=U$ forward from $(s_i,\dot s_i)$ until it crosses F (switch acc→dec, done) or hits the velocity-limit curve at $(s_{lim},\dot s_{lim})$. (4) Binary search on $\dot s\in[0,\dot s_{lim}]$ at $s_{lim}$ for the $L$-curve that just touches the limit curve tangentially → $(s_{tan},\dot s_{tan})$. (5) Integrate $L$ backward from there to intersect $A_i$ → switch acc→dec. (6) $(s_{tan},\dot s_{tan})$ = switch dec→acc; go to 3. Variation (§9.4.3): construct the limit curve explicitly and find tangency points directly.
- Caveats (§9.4.4): static posture maintenance assumed; no "islands" of inadmissible states; zero-inertia singular arcs cause chattering (use tangent accel); discontinuous accelerations; saturating actuators leaves no torque margin for feedback.

**Software (p. 346–347):** `CubicTimeScaling(Tf,t)`, `QuinticTimeScaling(Tf,t)`, `JointTrajectory(θs,θe,Tf,N,method∈{3,5})` (N rows, Δt = Tf/(N−1)), `ScrewTrajectory(Xs,Xe,Tf,N,method)`, `CartesianTrajectory(Xs,Xe,Tf,N,method)`.

**Exercises worth doing (pp. 348–352):** 9.3 (twist & $\dot{\mathcal V}$ along a screw path — feeds resolved-rate/feedforward control), **9.4 (min T for a cubic under joint vel/acc limits)**, **9.5 (derive quintic)**, 9.7 (min order for zero vel/acc/jerk = 7th order), 9.10–9.12 (trapezoid feasibility conditions), **9.18 (1 kHz via-point cubic generator — lab-style)**, 9.21–9.22 (phase-plane reasoning), 9.25 (plot motion cones for 2R).

**Conventions / pitfalls**
- Preprint typo in (9.36): uses $c(s),g(s)$ where the $i$-th components $c_i(s),g_i(s)$ are meant.
- Cubic via-point formula needs *both* endpoint velocities; if users give only positions, choose heuristic via velocities (Fig 9.7b smoother than 9.7a).
- Time scaling a task-space path still needs IK/resolved-rate to get joint commands.

---

## Ch 10 Motion Planning (pp. 353–402) — Lessons: A3 (C-space, potential fields, graph search), A4 (PRM/RRT/RRT*), F6 (optimization planning)
**Big idea:** Plan for a *point* in C-space among C-obstacles. Exact (complete) representations of $\mathcal C_{free}$ are hopeless beyond toy problems, so practical planners trade guarantees for scalability: grids (resolution-complete, optimal w.r.t. the grid, exponential in $n$) → sampling (probabilistically complete, scales to high $n$, satisficing) → potential fields (reactive, fast, local minima) → trajectory optimization (smooth, near-optimal, needs a good initial guess). In practice: global search + smoothing.

### 10.1 Overview (pp. 353–358)
- C-space $\mathcal C$, free space $\mathcal C_{free}$ (no penetration, no joint-limit violation); controls $u\in\mathcal U\subset\mathbb R^m$; state $x=(q,v)$ for 2nd-order systems, $x=q$ when inputs are velocities; $\dot x=f(x,u)$ (10.1), $x(T)=x(0)+\int_0^Tf\,dt$ (10.2); $\mathcal X_{free}=\{x\mid q(x)\in\mathcal C_{free}\}$.
- **Problem:** find $T$ and $u:[0,T]\to\mathcal U$ with $x(T)=x_{goal}$ and $q(x(t))\in\mathcal C_{free}\ \forall t$ (assumes a tracking feedback controller and an accurate geometric model).
- Problem variants: **path** (purely geometric $q(s)$, "piano mover's", then time scale per Ch 9) vs **motion** planning; $m=n$ vs $m<n$ (car: $n=3,m=2$); online vs offline; optimal ($J=\int L(x,u)dt$; $L=1$ time-optimal, $L=u^Tu$ min-effort) vs satisficing; exact vs approximate ($\|x(T)-x_{goal}\|<\epsilon$); with/without obstacles.
- Planner properties: **multiple-query vs single-query**; **anytime**; **complete** (finds a solution in finite time if one exists, reports failure otherwise) ⊃ **resolution complete** (complete at the discretization) ⊃ **probabilistically complete** (P(success)→1 as time→∞); computational complexity (often exponential in $n$).
- Methods map: complete (§10.3), grid (§10.4), sampling (§10.5), potential fields (§10.6), nonlinear optimization (§10.7), smoothing (§10.8).

### 10.2 Foundations (pp. 358–368)
**C-obstacles (10.2.1):** $\mathcal C=\mathcal C_{free}\cup\mathcal C_{obs}$; joint limits are obstacles; disconnected components of $\mathcal C_{free}$ ⇒ no path.
- 2R arm: C-space is a **torus** $T^2$ drawn as a square with wrap-around edges; obstacles A,B,C split $\mathcal C_{free}$ into 3 components (Fig 10.2).
- Circular translating robot: grow obstacles by the robot radius (Figs 10.3–10.4; two obstacles may merge and block a gap).
- Polygonal translating robot: slide the robot around the obstacle tracing the reference point (= Minkowski sum of obstacle with the reflected robot; Fig 10.5). Translating+rotating: 3-D C-space $\mathbb R^2\times S^1$, C-obstacle = stack of 2-D slices (Fig 10.6) — rarely represented exactly.

**Distance & collision (10.2.2):** signed $d(q,\mathcal B)$ ($>0$ free, $=0$ contact, $<0$ penetration). GJK for convex bodies (unions of convex pieces). Sphere approximations must be **conservative** (cover the object): $d(q,\mathcal B)=\min_{i,j}\|r_i(q)-b_j\|-R_i-B_j$. Path segments: sample finely and grow the robot so free consecutive samples ⇒ free swept volume.

**Graphs (10.2.3):** nodes/edges; directed vs undirected; weighted (positive costs) vs unweighted; **tree** = digraph with no cycles and ≤1 parent per node (root, leaves). Adjacency matrix $A\in\mathbb R^{N\times N}$ or neighbour lists.

**A\* search (10.2.4, Algorithm 10.1, p. 367):**
```
OPEN ← {start}; past_cost[start]=0, others ∞
while OPEN not empty:
    current ← pop min est_total_cost from OPEN; add to CLOSED
    if current ∈ goal: return path via parent[]
    for nbr of current not in CLOSED:
        tent = past_cost[current] + cost[current,nbr]
        if tent < past_cost[nbr]:
            past_cost[nbr]=tent; parent[nbr]=current
            insert/move nbr in OPEN with est_total_cost = past_cost[nbr] + heuristic_cost_to_go(nbr)
return FAILURE
```
- Heuristic must be **optimistic** (admissible, $\le$ true cost-to-go), e.g. straight-line distance; goal test when a node is *popped* (not when generated) guarantees optimality. Exact heuristic ⇒ minimal expansions.
- **Dijkstra** = A\* with $h\equiv0$; **BFS** = Dijkstra with unit costs; **weighted/suboptimal A\*** with $\eta h$, $\eta>1$: faster, not optimal (anytime: decrease $\eta$ toward 1).

### 10.3 Complete planners (pp. 368–369)
Roadmap $R$ with **reachability** (trivial path from any free $q$ to $R$) and **connectivity** (one component of $R$ per component of $\mathcal C_{free}$). Polygon translating among polygons: **visibility graph** (nodes = C-obstacle vertices, edges between mutually visible vertices, Euclidean weights) + A\* gives the true shortest path, which grazes obstacles (Fig 10.9). Ex 10.8: edges ending at concave vertices are never needed.

### 10.4 Grid methods (pp. 369–378)
- $k^n$ grid points. A\* on a grid: 4- vs 8-connectivity; diagonal cost $\sqrt2$ (integer approx 5 and 7); **Manhattan** heuristic if only axis-aligned moves; add nbr only if the step is collision-free. **Resolution-complete**, shortest subject to allowed moves. 2R example $k=32$ (11.25°, 1024 cells) (Fig 10.10).
- **Wavefront planner** (Fig 10.11): BFS from the goal labels every reachable cell with its distance; then descend greedily — a local-minimum-free potential (multiple-query for a fixed goal).
- Curse of dimensionality: $k=100$: $n=3$ → $10^6$, $n=5$ → $10^{10}$, $n=7$ → $10^{14}$ nodes.
- **Multi-resolution** (10.4.1): subdivide only obstacle-touching cells into $2^n$ children (quadtree $n=2$, octree $n=3$); Fig 10.12 uses 10 cells instead of 64.
- **Motion constraints** (10.4.2): directed graphs. *Wheeled robots* (Algorithm 10.2, p. 375): discretize controls $(v,\omega)$ (unicycle / diff-drive / car sets, Fig 10.14), integrate each for $\Delta t$, Dijkstra by path cost; prune nodes landing in an already-**occupied** C-space grid cell; MAXCOUNT cap; grid cells just large enough that any control for $\Delta t$ leaves the cell. Penalizing reversals changes the car path (Fig 10.15). *Robot arm in state space*: either path-plan then time-scale (Ch 9; not time-optimal) or plan directly with accelerations on a grid $\sum c\,a_i\hat e_i$ intersected with feasible set $A(q,\dot q)$ — reachable states lie on a lattice ($\dot q=a k_v\Delta t$, $q=q_0+a k_p\Delta t^2/2$); BFS gives resolution-complete **time-optimal** trajectories; cost grows by $r^{n\tau}$ when refining controls by $r$ and timestep by $\tau$ (e.g. $2^{3\cdot4}=4096$); use a conservative $\tilde A(q,\dot q)\subset A$.

### 10.5 Sampling methods (pp. 378–386) — A4
Ingredients: sampler, free-space checker, nearest-neighbour function, local planner. Probabilistically complete; satisficing.

**RRT (Algorithm 10.3, p. 379):**
```
T ← {x_start}
while |T| < max:
    x_samp ← sample X  (≈uniform, slight goal bias)
    x_nearest ← nearest node of T to x_samp
    local planner: motion x_nearest → x_new toward x_samp (step d)
    if motion collision-free: add x_new with edge from x_nearest
        if x_new ∈ X_goal: return SUCCESS
return FAILURE
```
- Uniform samples "pull" the tree into unexplored Voronoi regions (Fig 10.17: random-node random-motion trees don't explore). Samples need only be **dense** → "rapidly-exploring dense trees" (RDTs), can be deterministic.
- Sampling on $\mathbb R^n$, $T^n$, $\mathbb R^2\times S^1$ is easy; uniform on SO(3) is not. Dynamic systems: C-space × bounded velocity box.
- **Distance metric** is a design choice: mixed units, motion constraints (car: the config 2 m straight behind is "closest", Fig 10.18); weighted sum of components; kd-trees/hashing for NN.
- **Local planners:** straight line (kinematic), discretized controls (pick the end state closest to $x_{samp}$), Reeds–Shepp curves (cars).
- **Bidirectional RRT:** trees from start and goal; reach an exact goal state; connection may be inexact → patch by smoothing.
- **RRT\*** (p. 383): when inserting $x_{new}$, choose its parent among $X_{near}$ minimizing *total* cost-from-start (collision-free), then **rewire** each $x\in X_{near}$ through $x_{new}$ if cheaper. Asymptotically optimal; still probabilistically complete. Plain RRT run longer does *not* converge to optimal (edges never change). Requires exact steering (no motion constraints) in the basic form.

**PRM (Algorithm 10.4, p. 385)** — multiple-query, undirected roadmap; needs an exact, symmetric local planner (kinematic problems):
```
for i=1..N: q_i ← sample C_free; add to R
for i=1..N: for q in k nearest neighbours of q_i:
    if collision-free local path q↔q_i and no edge yet: add edge
query: connect q_start, q_goal to nearest roadmap nodes; search with A*
```
- Why it scales: the fraction of C-space visible to a local planner doesn't shrink exponentially with $n$. Sample more densely near obstacles to capture **narrow passages**; or deterministic multi-resolution sampling. Node degree can exceed $k$ (Fig 10.20).
- MR does **not** cover EST, lazy PRM, PRM\* — get those from Choset/LaValle for A4.

### 10.6 Virtual potential fields (pp. 386–392) — A3
- $F=-\partial\mathcal P/\partial q$. Attractive: $\mathcal P_{goal}=\frac12(q-q_{goal})^TK(q-q_{goal})$, $F_{goal}=K(q_{goal}-q)$. Repulsive (10.3): $\mathcal P_{\mathcal B}=\frac{k}{2d^2(q,\mathcal B)}$, $F_{\mathcal B}=\frac{k}{d^3}\frac{\partial d}{\partial q}$. Total = sum; minimum need not be exactly at $q_{goal}$; saturate $\mathcal P$, $F$ near obstacles.
- Fig 10.21: global min near goal, one spurious **local minimum**, four **saddles** (saddles are harmless — measure-zero attraction; local minima are the problem).
- Control options: (10.4) force + damping (as a "ball rolling with friction on the potential surface"), (10.5) **kinematic** $\dot q=F(q)$ (no oscillation).
  - **Sign check:** the book prints $u=F(q)+B\dot q$ with "$B$ positive definite dissipates energy". With $B\succ0$ that term *injects* energy; the dissipative law is $u=F(q)-B\dot q$ (equivalently $+B\dot q$ with $B$ negative definite). Same issue in §10.6.4 "$u=F_{proj}(q)+B\dot q$".
- Range-limited repulsion: $U_{\mathcal B}=\frac k2\left(\frac{d_{range}-d}{d_{range}\,d}\right)^2=\frac k2\left(\frac1d-\frac1{d_{range}}\right)^2$ for $d<d_{range}$, else 0 (classic Khatib FIRAS form).
- **Navigation functions** (10.6.2): smooth; bounded max (e.g. 1) on obstacle boundaries; **unique minimum at $q_{goal}$**; **Morse** (non-degenerate Hessian at critical points) ⇒ almost all initial conditions converge. Rimon–Koditschek: constructive for **sphere worlds**, extended by diffeomorphism to **star worlds**. Wavefront is the discrete analogue.
- **Workspace potential** (10.6.3): robot control points $f_i(q)$, obstacle boundary points $c_j$; $\mathcal P'_{ij}=\frac{k}{2\|f_i(q)-c_j\|^2}$; Cartesian repulsive force $F'_{ij}=\frac{k}{\|f_i-c_j\|^4}(f_i-c_j)\in\mathbb R^3$; generalized force $F_{ij}=J_i^T(q)F'_{ij}$ with $J_i=\partial f_i/\partial q$ (virtual work).
  - **Notation slip in the preprint (p. 390):** the displayed $F'_{ij}$ already contains $(\partial f_i/\partial q)^T$ yet is labelled $\in\mathbb R^3$ and then multiplied by $J_i^T$ again. Use the corrected pair above.
- Wheeled robots (10.6.4): project onto the constraint-tangent space, $F_{proj}=[I-A^T(AA^T)^{-1}A]F$ (kinematic); dynamic case via §8.7.
- 10.6.5: use the potential as A\*'s heuristic — search escapes local minima.

### 10.7 Nonlinear optimization (pp. 392–393) — F6
Find $u(t),q(t),T$ minimizing $J$ s.t. dynamics (10.8), input limits (10.9), $q\in\mathcal C_{free}$ (10.10), boundary conditions (10.11–10.12). Discretize constraints at collocation points; parametrize (a) $q(t)$ only (inverse dynamics gives $u$; **not** valid for $m<n$), (b) $u(t)$ only (integrate dynamics — shooting), (c) both (collocation; must be mutually consistent). Bases: polynomials $u_i=\sum a_jt^j$, Fourier, splines, wavelets, piecewise-constant. Solvers: SQP (gradient), simulated annealing / Nelder–Mead / GA (gradient-free). Local optimum only; need a good initial guess.

### 10.8 Smoothing (p. 394)
(i) Optimize with the planner's path as initial guess, e.g. $J=\frac12\int\dot u^T\dot u\,dt$ (min torque-change, cf. human motor control); (ii) **subdivide-and-reconnect** / shortcutting with the local planner.

**Exercises worth doing (pp. 398–402):** 10.1–10.3 (topology, connected components on the torus), 10.5 (C-obstacle via convex hull/Minkowski), 10.6 (sphere distance without square roots — compare squared distances), **10.9–10.11 (A\* vs inflated heuristic vs Dijkstra on random graphs)**, **10.12–10.13 (2R C-obstacle by sampling + A\* grid planner — classic ASBR assignment)**, 10.14 (Algorithm 10.2), **10.15–10.17 (RRT and PRM on occupancy grids — ASBR A4)**, **10.18 (workspace potential field for a 2R arm with control points; find a local minimum)**.

**Conventions / pitfalls**
- A\* as written (CLOSED nodes are never reopened) is optimal for **consistent** heuristics ($h(n)\le c(n,n')+h(n')$); merely admissible heuristics can need node re-opening. Euclidean/Manhattan distances on grids are consistent, so the book's examples are fine — but the summary's "optimistic ⇒ optimal" is the tree-search statement.
- RRT\* "near" radius shrinks like $(\log N/N)^{1/d}$ (Karaman–Frazzoli); MR leaves $X_{near}$ unspecified.
- Torus wrap-around must be handled in grid neighbours and in RRT distances for revolute joints.

---

## Ch 11 Robot Control — §11.1–11.2.1 and start of §11.2.2 (pp. 403–412; range ends mid-§11.2.2) — Lessons: R8, F7
**Big idea:** The controller turns a task specification (motion, force, hybrid, impedance) into joint torques. A robot cannot independently control motion *and* force in the same direction. Design starts by asking what *error dynamics* the closed loop should have; for linear error dynamics, stability = all characteristic roots in the open left half plane, and second-order behaviour is fully described by $\omega_n$ and $\zeta$.

- **11.1 Overview** (pp. 404–405): sensors (encoders/potentiometers/resolvers, tachometers, joint and wrist F/T); 100s–1000s Hz loops; velocity often from filtered finite differences; inner current/torque loop in the amplifier ⇒ treat "controller outputs torque" (Fig 11.1).
- **11.2 Error dynamics:** $\theta_e=\theta_d-\theta$. Unit error response: $\theta_e(0)=1$, higher derivatives 0. Metrics: steady-state error $e_{ss}$; overshoot $=\frac{\theta_{e,min}-e_{ss}}{\theta_e(0)-e_{ss}}\times100\%$; 2% settling time (Fig 11.2).
- **Linear error dynamics** (11.1): $a_p\theta_e^{(p)}+\dots+a_0\theta_e=c$; homogeneous if $c=0$; companion form $\dot x=Ax$, $x(t)=e^{At}x(0)$ (F7). Characteristic polynomial (11.3) $s^p+a'_{p-1}s^{p-1}+\dots+a'_0$. Necessary: all $a'_i>0$; sufficient for $p=1,2$; $p=3$ also needs $a'_2a'_1>a'_0$ (Routh–Hurwitz).
- Mass–spring–damper (11.4) $m\ddot\theta_e+b\dot\theta_e+k\theta_e=f$; $m\to0$: (11.5) $b\dot\theta_e+k\theta_e=f$.
- **First order** (11.6–11.7): $\theta_e(t)=e^{-t/\mathfrak t}\theta_e(0)$, time constant $\mathfrak t=b/k$; 37% at $\mathfrak t$; 2% settling $=3.91\mathfrak t\approx4\mathfrak t$; no overshoot.
- **Second order** (11.8–11.10): $\ddot\theta_e+2\zeta\omega_n\dot\theta_e+\omega_n^2\theta_e=0$, $\omega_n=\sqrt{k/m}$, $\zeta=b/(2\sqrt{km})$; roots $-\zeta\omega_n\pm\omega_n\sqrt{\zeta^2-1}$; stable iff $\zeta\omega_n>0$ and $\omega_n^2>0$.
  - Overdamped $\zeta>1$: $c_{1,2}=\frac12\pm\frac{\zeta}{2\sqrt{\zeta^2-1}}$; slow root dominates settling.
  - Critically damped $\zeta=1$: $(1+\omega_nt)e^{-\omega_nt}$.
  - Underdamped $\zeta<1$: $\omega_d=\omega_n\sqrt{1-\zeta^2}$, $\theta_e=(\cos\omega_dt+\frac{\zeta}{\sqrt{1-\zeta^2}}\sin\omega_dt)e^{-\zeta\omega_nt}$; settling ≈ $4/(\zeta\omega_n)$; peak at $t_p=\pi/\omega_d$ (range ends here, p. 412; overshoot formula $e^{-\pi\zeta/\sqrt{1-\zeta^2}}$ follows on p. 413).
  - Root-locus intuition (Fig 11.5): further left ⇒ faster settling; further from real axis ⇒ more overshoot/oscillation.
- **Pitfall/correction (p. 408):** the book says $\dot x=Ax$ converges "if the matrix $A$ is negative definite, i.e., all eigenvalues … have negative real components". The correct term is **Hurwitz** (all eigenvalues in the open LHP). Negative definiteness is a property of the symmetric part and is neither necessary for a companion matrix nor what is meant.
- The rest of Ch 11 (PID/computed torque §11.3–11.4 incl. passivity-based stability using Prop 8.1, task-space control, force, hybrid, impedance) is in the next part.

