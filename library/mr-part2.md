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

