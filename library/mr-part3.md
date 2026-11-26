# Modern Robotics (Lynch & Park, May 2017 preprint) — Part 3: Ch. 11 (from 11.2.2) – Ch. 13, Appendices A–D, Bibliography, Index
Source: 5a2ab312-MR.pdf, PDF pages 431–642 (book pages 413–624). **Offset: PDF page = book page + 18.**

Coverage map of this range:
| Content | PDF pp. | Book pp. |
|---|---|---|
| Ch. 11 Robot Control (end of 11.2.2 overshoot → 11.13 exercises) | 431–478 | 413–460 |
| Ch. 12 Grasping and Manipulation | 479–530 | 461–512 |
| Ch. 13 Wheeled Mobile Robots | 531–582 | 513–564 |
| App. A Summary of Useful Formulas | 583–592 | 565–574 |
| App. B Other Representations of Rotations (Euler, RPY, quaternions, Cayley–Rodrigues) | 593–602 | 575–584 |
| App. C Denavit–Hartenberg Parameters | 603–614 | 585–596 |
| App. D Optimization and Lagrange Multipliers | 615–616 | 597–598 |
| Bibliography / Index | 617–634 / 635–642 | 599–616 / 617–624 |

(Sections 11.1–11.2.2 up to the overshoot formula — control-system overview, first/second-order error dynamics, standard form $\ddot\theta_e+2\zeta\omega_n\dot\theta_e+\omega_n^2\theta_e=0$ (Eq. 11.8), roots $s_{1,2}=-\zeta\omega_n\pm\omega_n\sqrt{\zeta^2-1}$, 2% settling time $\approx 4/(\zeta\omega_n)$ — are on PDF pp. 421–430, i.e. in the previous reader's range; they are restated where needed below.)

---

## Ch. 11 Robot Control (PDF pp. 431–478, book pp. 413–460) — Lessons: R8 (primary), R5 (statics $\tau=J^T\mathcal F$), R7 (dynamics used by controllers), F7 (linear ODEs/characteristic roots), C8 (virtual fixtures ↔ impedance/admittance), A1 (task-space velocity control = resolved-rate)

**Big idea:** A controller turns the *error* between desired and actual state into actuator commands so that the *error dynamics* are a stable linear ODE whose roots you choose. The chapter climbs a ladder: (1) if you can command joint *velocities*, feedforward + PI feedback suffices (and its task-space version is exactly resolved-rate control with a twist-valued error $\log(X^{-1}X_d)$); (2) if you command *torques*, PD/PID alone leaves gravity-induced steady-state error, gravity compensation fixes setpoints (proved by an energy/LaSalle argument), and **computed torque** uses the dynamics model to cancel nonlinearities so the closed loop is a decoupled linear system $\ddot\theta_e+K_d\dot\theta_e+K_p\theta_e=0$; (3) when the task is to *push* rather than *move*, use force control, hybrid motion–force control (projecting wrenches into motion and force subspaces defined by natural constraints), or impedance/admittance control (render a virtual mass–spring–damper).

### 11.2.2 (end) Overshoot (PDF 431 / book 413)
- For underdamped 2nd-order error dynamics with $\theta_e(0)=1,\dot\theta_e(0)=0$, peak time $t_p=\pi/\omega_d$, $\omega_d=\omega_n\sqrt{1-\zeta^2}$, and
  $$\text{overshoot}=e^{-\pi\zeta/\sqrt{1-\zeta^2}}\times100\%.$$
  $\zeta=0.1\to73\%$, $\zeta=0.5\to16\%$, $\zeta=0.8\to1.5\%$. Memorize these three anchors.

### 11.3 Motion control with **velocity** inputs (PDF 431–438, book 413–420)
Assumption: you can command $\dot\theta$ directly (stepper motors, amplifier in velocity mode, or — in practice — a robot's velocity interface such as the UR5's `speedj`, which is what RDKDC/ASBR resolved-rate labs use).

**Key results & equations**
- Eq (11.11) feedforward: $\dot\theta(t)=\dot\theta_d(t)$ (open loop; errors accumulate).
- Eq (11.12) P control: $\dot\theta=K_p\theta_e$, $\theta_e=\theta_d-\theta$. Setpoint ⇒ $\dot\theta_e+K_p\theta_e=0$: first order, time constant $1/K_p$, 2% settling $4/K_p$, no overshoot, zero steady-state error.
  - Constant reference velocity $\dot\theta_d=c$: $\theta_e(t)=c/K_p+(\theta_e(0)-c/K_p)e^{-K_pt}$ ⇒ **steady-state lag $e_{ss}=c/K_p$**. Large $K_p$ is limited by joint velocity limits and by discrete-time servo instability.
- Eq (11.13) PI: $\dot\theta=K_p\theta_e+K_i\int_0^t\theta_e$. For constant $\dot\theta_d$, differentiate ⇒ Eq (11.14) $\ddot\theta_e+K_p\dot\theta_e+K_i\theta_e=0$, i.e. $\omega_n=\sqrt{K_i}$, $\zeta=K_p/(2\sqrt{K_i})$. Stable iff $K_p,K_i>0$. Critical damping: $K_i=K_p^2/4$.
  - Root-locus worked example (PDF 434–435, Fig 11.7): $K_p=20$, increasing $K_i$: overdamped ($K_i=44.4$, $\zeta=1.5$) → critically damped at $K_i=100$ (double root $-10$, settling $0.4$ s) → underdamped ($K_i=400$, $\zeta=0.5$; roots $-10\pm j\sqrt{K_i-100}$, settling time unchanged because real part fixed).
  - PI gives $e_{ss}=0$ for constant-velocity references; not for general $\dot\theta_d(t)$.
- Eq (11.15) **preferred law**: feedforward + PI feedback $\dot\theta=\dot\theta_d+K_p\theta_e+K_i\int\theta_e$. Multi-joint (11.3.2): same with $K_p=k_pI$, $K_i=k_iI$.
- **Task space (11.3.3), Eq (11.16):**
  $$\mathcal V_b(t)=[\mathrm{Ad}_{X^{-1}X_d}]\mathcal V_d(t)+K_pX_e(t)+K_i\int_0^tX_e,\qquad [X_e]=\log(X^{-1}X_d),\qquad \dot\theta=J_b^\dagger(\theta)\mathcal V_b.$$
  - $[\mathrm{Ad}_{X^{-1}X_d}]\mathcal V_d$ re-expresses the desired body twist (in frame $\{d\}$) in the *actual* end-effector frame $\{b\}$; it equals $\mathcal V_d$ when $X=X_d$.
  - $X_e$ is the **body-frame twist that takes $X$ to $X_d$ in unit time** — *not* $X_d-X$ (you cannot subtract on SE(3)).
  - With $\mathcal V_d=0$, $K_i=0$, this is exactly **resolved-rate control** (RDKDC R6 / ASBR A1): $\dot\theta=k_pJ_b^\dagger\,\log(X^{-1}X_d)^\vee$.
- Eq (11.17) minimal coordinates $x\in\mathbb R^m$: $\dot x=\dot x_d+K_p(x_d-x)+K_i\int(x_d-x)$.
- Eq (11.18) hybrid $(R,p)$ representation with velocity $(\omega_b,\dot p)$:
  $\begin{bmatrix}\omega_b\\\dot p\end{bmatrix}=\begin{bmatrix}R^TR_d&0\\0&I\end{bmatrix}\begin{bmatrix}\omega_d\\\dot p_d\end{bmatrix}+K_pX_e+K_i\int X_e$, $X_e=\begin{bmatrix}\log(R^TR_d)^\vee\\p_d-p\end{bmatrix}$.
  **Preprint typo** (PDF 438): printed $\log(R^{\mathrm T}(d)R_d(t))$ — should be $\log(R^{\mathrm T}(t)R_d(t))$.
- Fig 11.10 (PDF 439) worked comparison: stabilize to origin from $R_0=\mathrm{Rot}(\hat z,90^\circ)$, $p_0=(1,1,0)$. Body-twist control (11.16) moves the origin along a **screw (curved) path**; decoupled control (11.18) moves the origin along a **straight line**. Great intuition for why many practical resolved-rate implementations (incl. typical RDKDC/ASBR code that uses position error $p_d-p$ plus axis-angle orientation error) behave differently from the "pure" SE(3) log version.

### 11.4 Motion control with **torque/force** inputs (PDF 438–452, book 420–434)
Single-joint model, Eq (11.19)–(11.22): $\tau=M\ddot\theta+mgr\cos\theta+b\dot\theta=M\ddot\theta+h(\theta,\dot\theta)$. Simulation constants: $M=0.5$ kg m², $m=1$ kg, $r=0.1$ m, $b=0.1$ N m s/rad, $g=0$ (horizontal) or 9.81.

**PID, Eq (11.23):** $\tau=K_p\theta_e+K_i\int\theta_e+K_d\dot\theta_e$. $K_p$ = virtual spring, $K_d$ = virtual damper, $K_i$ removes steady-state error. Pseudocode Fig 11.15 (velocity by backward difference, integral by Euler sum).

**PD, horizontal plane, setpoint** Eq (11.24)–(11.26): $M\ddot\theta_e+(b+K_d)\dot\theta_e+K_p\theta_e=0$ ⇒
$$\zeta=\frac{b+K_d}{2\sqrt{K_pM}},\qquad \omega_n=\sqrt{K_p/M}.$$
Stability: $b+K_d>0$, $K_p>0$. Design: $K_p$ as high as practical (saturation, chattering, unmodeled flexibility, servo rate), then $K_d$ for $\zeta=1$.

**PD under gravity** Eq (11.27): $M\ddot\theta_e+(b+K_d)\dot\theta_e+K_p\theta_e=mgr\cos\theta$ ⇒ rests where $K_p\theta_e=mgr\cos\theta$ — **nonzero steady-state error** unless $\theta_d=\pm\pi/2$.

**PID ⇒ third-order error dynamics** Eq (11.28)–(11.30): treat gravity as constant disturbance $\tau_{dist}$, differentiate: $M\theta_e^{(3)}+(b+K_d)\ddot\theta_e+K_p\dot\theta_e+K_i\theta_e=\dot\tau_{dist}=0$. Characteristic eq $s^3+\frac{b+K_d}{M}s^2+\frac{K_p}{M}s+\frac{K_i}{M}=0$. Routh–Hurwitz-type stability conditions (PDF 443):
$$K_d>-b,\quad K_p>0,\quad \frac{(b+K_d)K_p}{M}>K_i>0.$$
**$K_i$ has an upper bound.** Design: tune PD for good transient, then add small $K_i$; anti-windup clamps the integral. "In practice $K_i=0$ for many robot controllers." Fig 11.13 worked example: $K_d=2$, $K_p=2.205$ (critical damping incl. $b$), $K_i=1$, start at $-\pi/2$, goal 0: PD stalls short of goal; PID reaches it with overshoot; the I-term takes over the gravity-holding torque so the P-term goes to zero.

**Feedforward (model-based) Eq (11.31)–(11.32):** $\tau=\tilde M(\theta_d)\ddot\theta_d+\tilde h(\theta_d,\dot\theta_d)$. Exact only with perfect model and no initial error (Fig 11.17 example: $\tilde r=0.08$ vs true $0.1$).

**Computed torque / inverse dynamics / feedback linearization, Eq (11.33)–(11.35):** choose commanded acceleration $\ddot\theta=\ddot\theta_d+K_d\dot\theta_e+K_p\theta_e+K_i\int\theta_e$ and map through the model:
$$\boxed{\tau=\tilde M(\theta)\Big(\ddot\theta_d+K_p\theta_e+K_i\!\int\!\theta_e\,dt+K_d\dot\theta_e\Big)+\tilde h(\theta,\dot\theta)}$$
- Note: $\tilde M,\tilde h$ evaluated at the **actual** state $(\theta,\dot\theta)$ (feedback), whereas pure feedforward (11.32) evaluates them at the **desired** state.
- With a perfect model the closed loop is $\ddot\theta_e+K_d\dot\theta_e+K_p\theta_e+K_i\int\theta_e=0$ — **unit-mass** error dynamics, so the gains are directly $\omega_n^2=k_p$, $2\zeta\omega_n=k_d$ (critical damping $k_d=2\sqrt{k_p}$), independent of $M$. (Contrast PD on the raw plant, where $M$ appears in $\zeta,\omega_n$.)
- Block diagram Fig 11.18; pseudocode Fig 11.20; Fig 11.19: computed torque tracks better than ff or fb alone with less control effort ($\int\tau^2dt$) than fb alone.
- Multi-joint Eq (11.36)–(11.37): same law with $n$-vectors and positive-definite gain matrices (usually $k_pI,k_iI,k_dI$, $k_i=0$ common).

**Decentralized vs centralized (11.4.2.1–2):** decentralized (independent per-joint PID) is fine when $M(\theta)$ ≈ diagonal: Cartesian/gantry robots, highly geared robots without gravity (reflected motor inertia $G^2I_{rotor}$ dominates), high joint friction.

**PD + gravity compensation, Eq (11.38):** $\tau=K_p\theta_e+K_i\int\theta_e+K_d\dot\theta_e+\tilde g(\theta)$ (no need for full $M,C$).
- Lyapunov/energy proof (Eq 11.39–11.43, PDF 450–451): with zero friction, perfect $\tilde g$, $K_i=0$, setpoint: closed loop $M\ddot\theta+C\dot\theta=K_p\theta_e-K_d\dot\theta$. Error energy $V=\tfrac12\theta_e^TK_p\theta_e+\tfrac12\dot\theta^TM\dot\theta$ gives $\dot V=-\dot\theta^TK_d\dot\theta+\tfrac12\dot\theta^T(\dot M-2C)\dot\theta=-\dot\theta^TK_d\dot\theta\le0$ using **skew-symmetry of $\dot M-2C$** (Prop. 8.1.2). $\dot V=0$ only at $\dot\theta=0$, and there $\theta\ne\theta_d$ implies $\ddot\theta\ne0$, so by **Krasovskii–LaSalle** (Ex 11.12) ⇒ global convergence to $\theta_d$. This is *the* classic R8 proof (also in MLS §4.5 and Spong et al.'s multivariable-control chapter).
- **Preprint typo** in Summary (PDF 468, book 450): "$\tau=K_p\theta_e+K_d\dot\theta+\tilde g(\theta)$" — the damping sign is wrong; should be $-K_d\dot\theta$ (equivalently $+K_d\dot\theta_e$ since $\dot\theta_d=0$), consistent with Eq (11.39).

### 11.4.3 Task-space motion control (PDF 451–452, book 433–434)
- Option 1: convert to joint space via IK, Eq (11.44)–(11.46): $\theta=T^{-1}(X)$, $\dot\theta=J_b^\dagger\mathcal V_b$, $\ddot\theta=J_b^\dagger(\dot{\mathcal V}_b-\dot J_b\dot\theta)$ — needs IK, $J^\dagger$, $\dot J$.
- Option 2: task-space dynamics (Sec. 8.6) $\mathcal F_b=\Lambda(\theta)\dot{\mathcal V}_b+\eta(\theta,\mathcal V_b)$, $\tau=J_b^T\mathcal F_b$, and the **task-space computed torque law (operational-space control)**, Eq (11.47):
  $$\tau=J_b^T(\theta)\Big(\tilde\Lambda(\theta)\big(\tfrac{d}{dt}([\mathrm{Ad}_{X^{-1}X_d}]\mathcal V_d)+K_pX_e+K_i\!\int\!X_e+K_d\mathcal V_e\big)+\tilde\eta(\theta,\mathcal V_b)\Big),$$
  $[X_e]=\log(X^{-1}X_d)$, $\mathcal V_e=[\mathrm{Ad}_{X^{-1}X_d}]\mathcal V_d-\mathcal V$. The feedforward-derivative term ≈ $\dot{\mathcal V}_d$ near the reference.

### 11.5 Force control (PDF 452–455, book 434–437)
- Dynamics with tip wrench, Eq (11.48): $M\ddot\theta+c+g+b+J^T\mathcal F_{tip}=\tau$ ($\mathcal F_{tip}$ = wrench robot applies *to* environment, $J$ and $\mathcal F$ in the same frame). Quasi-static, Eq (11.49): $g(\theta)+J^T\mathcal F_{tip}=\tau$.
- Open-loop force control, Eq (11.50): $\tau=\tilde g(\theta)+J^T\mathcal F_d$ (needs good gravity model and torque control: current control for direct drive; strain-gauge joint-torque loops for geared joints).
- With a 6-axis F/T sensor, PI force control + feedforward + gravity comp, Eq (11.51): $\tau=\tilde g+J^T(\mathcal F_d+K_{fp}\mathcal F_e+K_{fi}\int\mathcal F_e)$, $\mathcal F_e=\mathcal F_d-\mathcal F_{tip}$. Error dynamics (11.52)–(11.53) converge for p.d. gains even with constant disturbance. **No D-term**: force signals are noisy, and there is no integrating dynamics between torque and tip force in the rigid model (footnote 2).
- **Safety**: if nothing resists, the robot accelerates away ⇒ add damping, Eq (11.54): $\tau=\tilde g+J^T(\mathcal F_d+K_{fp}\mathcal F_e+K_{fi}\int\mathcal F_e-K_{damp}\mathcal V)$.

### 11.6 Hybrid motion–force control (PDF 455–459, book 437–441)
- In an $n$-dim task space you may specify $n$ of the $2n$ force/velocity components; never both force and motion in the "same direction". Damper example $B_{env}=\begin{bmatrix}2&1\\1&1\end{bmatrix}$: you may specify $f_1$ and $v_1$ (since $B$ not diagonal), but not $f_1$ and $2v_1+v_2$.
- **Natural vs artificial constraints** (Mason): rigid environment fixes $k$ velocity constraints and $6-k$ wrench constraints (natural); you choose the complementary artificial ones. Worked examples: cabinet door (1 motion freedom, 5 force), chalk on board ($k=1$ force, 5 motion; friction caveat), **eraser table** (PDF 457): natural $\omega_x=\omega_y=v_z=0$, $m_z=f_x=f_y=0$; artificial $m_x=m_y=0$, $\omega_z=0$, $v_x=k_1$, $v_y=0$, $f_z=k_2<0$.
- Constraints as Pfaffian $A(\theta)\mathcal V=0$, $A\in\mathbb R^{k\times6}$ (Eq 11.55). Constrained dynamics Eq (11.56): $\mathcal F=\Lambda\dot{\mathcal V}+\eta+A^T\lambda$, $\mathcal F_{tip}=A^T\lambda$ (desired wrench must lie in col($A^T$)). Differentiated constraint (11.57) $A\dot{\mathcal V}+\dot A\mathcal V=0$. Multipliers Eq (11.58): $\lambda=(A\Lambda^{-1}A^T)^{-1}(A\Lambda^{-1}(\mathcal F-\eta)-A\dot{\mathcal V})$.
- **Projection** Eq (11.59)–(11.60): $P\mathcal F=P(\Lambda\dot{\mathcal V}+\eta)$, $P=I-A^T(A\Lambda^{-1}A^T)^{-1}A\Lambda^{-1}$, rank $n-k$; $P$ picks the wrench part that moves the end-effector tangent to the constraints, $I-P$ the part pushing against them. (Note $P$ is a $\Lambda^{-1}$-weighted, generally non-symmetric projector — "orthogonal" in the dynamically-weighted sense.)
- **Hybrid controller** Eq (11.61): $\tau=J_b^T\big(P\,[\text{motion CT law of 11.47}]+(I-P)[\mathcal F_d+K_{fp}\mathcal F_e+K_{fi}\int\mathcal F_e]+\tilde\eta\big)$. Practical issue: knowing $A(\theta)$ — estimate constraints online, use low gains, or add passive compliance.

### 11.7 Impedance and admittance control (PDF 459–463, book 441–445) — also C8 (virtual fixtures / cooperative control)
- 1-dof target, Eq (11.62)–(11.63): $m\ddot x+b\dot x+kx=f$, impedance $Z(s)=F(s)/X(s)=ms^2+bs+k$ (spring dominates at low freq, mass at high freq). Admittance $Y=Z^{-1}$. Good motion control = **high impedance**; good force control = **low impedance**.
- Task-space goal, Eq (11.64): $M\ddot x+B\dot x+Kx=f_{ext}$ (can also be written with twists/wrenches and exponential coordinates, or decoupled linear/angular).
- **Impedance control** (sense motion → command force; pairs with admittance-type environment), Eq (11.65): $\tau=J^T\big(\tilde\Lambda\ddot x+\tilde\eta-(M\ddot x+B\dot x+Kx)\big)$. Often drop $\tilde\Lambda\ddot x$ and set $M=0$ (accel noisy), use only gravity comp. Hard: rendering stiff environments (high effective gain + delay/quantization ⇒ instability). Best on lightweight backdrivable arms.
- **Admittance control** (sense force with wrist F/T → command motion; pairs with impedance-type environment), Eq (11.66): $\ddot x_d=M^{-1}(f_{ext}-B\dot x-Kx)$, then $\ddot\theta_d=J^\dagger(\ddot x_d-\dot J\dot\theta)$ and inverse dynamics; low-pass filter the force. Hard: rendering low mass. Best on highly geared stiff robots. (JHU context: the Steady-Hand / cooperative-control robots in CIS I/II are admittance-controlled: $\dot x=k\,f$, a pure-damper admittance.)

### 11.8 Low-level joint torque control (PDF 463–465, book 445–447)
Ladder of joint-torque implementations: (1) current control of direct-drive motor $\tau=k_tI$ (inner current loop ~10 kHz, outer ~1 kHz; big motors); (2) geared motor ($Gk_tI$ nominal; backlash, friction uncertainty; harmonic drives remove backlash); (3) geared motor + strain-gauge torque feedback (compensates friction; adds torsional compliance); (4) **series elastic actuator** — deliberately soft spring, torque $=k\Delta\phi$ measured by encoders (safe HRI, shock tolerant; lower bandwidth). Example: NASA Robonaut 2 hip SEA (Fig 11.24).

### 11.9 Other topics (PDF 466–467): robust control, adaptive control, iterative learning control (repetitive tasks, nonparametric), passive compliance / flexible manipulators, variable-impedance actuators.

### 11.10–11.12 Summary, software, notes (PDF 467–470)
- Software: `ComputedTorque(thetalist,dthetalist,eint,g,Mlist,Glist,Slist,thetalistd,dthetalistd,ddthetalistd,Kp,Ki,Kd)`; `SimulateControl(...)` simulates computed torque with a possibly wrong model ($\tilde g$, $\tilde M_{i-1,i}$, $\tilde G_i$).
- References: computed torque 1970s; PD+gravity Takegaki–Arimoto [184]; operational space / resolved acceleration: Luh–Walker–Paul [99], Khatib [74]; natural/artificial constraints Mason [107]; Raibert–Craig hybrid [144]; Duffy's critique of non-invariant "orthogonality" [42]; Hogan impedance [56–58].

**Exercises worth doing (PDF 471–478):**
- 11.2 (5% settling time ⇒ $\approx3/(\zeta\omega_n)$), 11.3–11.4 (solve underdamped responses) — quick F7/R8 drills.
- **11.5** pendulum linearization; upright equilibrium has negative stiffness $-mgL=-20$ N m/rad, so a restoring P gain must exceed $mgL$ — classic exam question.
- **11.6** PD/PID design for a mass-spring-damper incl. 2% settling 0.01 s and steady-state error — matches RDKDC control HW style.
- **11.7 / 11.9 / 11.10 / 11.11** build a 1-dof then 2R simulator: Euler integration, PD/PID tuning, torque saturation, friction, quintic trajectories (Eq 11.67), computed torque with 20% mass error — **best practical R8 project** (and R7 2R dynamics derivation).
- **11.12** LaSalle with $K_p=0$ or $K_d=0$ — tests understanding of the PD+gravity proof.
- 11.13 task-space CT for the 2R ($\Lambda=J^{-T}MJ^{-1}$); 11.15 constraint $x-y=1$ as $A\mathcal V=0$; 11.16 derive projection $P$ (good matrix-algebra drill, F2/F5).
- 11.17 current vs strain-gauge torque loops.

**Conventions / pitfalls**
- Error is always **desired minus actual**: $\theta_e=\theta_d-\theta$, $\mathcal F_e=\mathcal F_d-\mathcal F_{tip}$, $[X_e]=\log(X^{-1}X_d)$ (body frame). Using $\log(X_dX^{-1})$ gives a *space-frame* error, which must be paired with $J_s$, not $J_b$.
- $\mathcal F_{tip}$ = wrench the robot applies **to** the environment, so it enters dynamics as $+J^T\mathcal F_{tip}$ on the LHS.
- Computed torque evaluates the model at the actual state; feedforward at the desired state.
- With computed torque the error dynamics have unit inertia; with plain PD the inertia $M$ enters $\zeta,\omega_n$.
- MR uses $\tau=M\ddot\theta+h$ with $h=c+g+b$ (friction lumped); MLS uses $M\ddot\theta+C\dot\theta+N$.
- MR PID integral gain has an *upper* stability bound $K_i<(b+K_d)K_p/M$.
- Two preprint typos noted above (Eq 11.18, Summary PD+gravity sign).

---

## Ch. 12 Grasping and Manipulation (PDF pp. 479–530, book pp. 461–512) — Lessons: R5 (wrenches, $\tau=J^T\mathcal F$ duality), R2 (twists/wrenches/reciprocity), F6 (linear programming), H-series (grasp analysis background for robot-learning grasping); low priority for the six courses
**Big idea:** Manipulation = contact kinematics + contact force (Coulomb friction) + rigid-body dynamics. Every point contact turns into a *half-space* constraint on twists ($\mathcal F^T\mathcal V\ge0$) and a *cone* of transmissible wrenches. Grasp questions (can the object move? can the contacts resist any disturbance?) become questions about **positive spans** of wrench vectors, testable by a small linear program. Motion and force freedoms are dual: each equality motion constraint at a contact buys one wrench freedom.

**Key definitions (PDF 480–481):** Def 12.1 span / pos (conical) / conv span; $\mathrm{conv}\subseteq\mathrm{pos}\subseteq\mathrm{span}$. Facts: $\mathbb R^n$ needs $n$ vectors to linearly span, **$n+1$ to positively span**.

**Contact kinematics (12.1, PDF 481–498)**
- Distance function $d(q)$; first-order analysis keeps only $\partial d/\partial q$ (normal), not curvature (Eqs 12.1–12.2).
- Normal $\hat n$ points **into body A**; unit normal-force wrench $\mathcal F=([p_A]\hat n,\hat n)$ (space frame, $(m,f)$ ordering). Impenetrability Eq (12.5): $\mathcal F^T(\mathcal V_A-\mathcal V_B)\ge0$; active/roll–slide Eq (12.6) "=0"; fixed B: (12.7) $\mathcal F^T\mathcal V_A\ge0$ (*repelling* if $>0$, *reciprocal* if $=0$ — screw-theory reciprocity!). Rolling/sticking Eq (12.8): $\dot p_A=\dot p_B$. Labels **R** (rolling/sticking), **S** (sliding), **B** (breaking); contact mode = concatenation, ≤ $3^n$ modes.
- Example 12.2 (PDF 484–486): $p=(1,2,0)$, $\hat n=(0,1,0)$ ⇒ $\omega_{Az}-\omega_{Bz}+v_{Ay}-v_{By}\ge0$; rolling adds Eqs (12.10)–(12.12). Example 12.3 hexagon/fingers; Example 12.4 three planar contacts ⇒ polyhedral twist cone.
- Multiple stationary contacts ⇒ homogeneous polyhedral convex cone of feasible twists; moving contacts ⇒ general polytope. Kinematic contact models: frictionless point (1 constraint), point with friction (3), soft finger (4; adds no spin about normal). Line contact ≈ 2 point contacts, plane contact ≈ convex-hull corners.
- **Planar graphical method:** planar twist $(\omega_z,v_x,v_y)$ ↔ center of rotation $(-v_y/\omega_z,\;v_x/\omega_z)$ with sign label; pos-span of two CoRs = segment (same sign) or the complement line through infinity (opposite signs). Contact labels Sl/Sr for planar sliding. Example 12.5: first-order analysis can be fooled by curvature.
- **Form closure (12.1.7):** stationary contacts prevent all motion ⇔ $\mathrm{pos}(\{\mathcal F_i\})=\mathbb R^6$ (or $\mathbb R^3$ planar). **Thm 12.6: ≥4 point contacts planar, ≥7 spatial** for first-order form closure. Exceptional objects (disks, spheres, surfaces of revolution) can't be form-closed. Higher-order (curvature) effects can immobilize with fewer contacts.
- **LP test Eq (12.14)** (PDF 498): $F=[\mathcal F_1\cdots\mathcal F_j]$ full rank and $\exists k$: $Fk=0$, $k_i\ge1$ (min $\mathbf 1^Tk$). Example 12.7 (PDF 498–499) with MATLAB `linprog` code: solution $k=(2,1,2,1)$ ⇒ form closure; moving one finger ⇒ infeasible.
- Grasp quality (12.1.7.3): radius of largest origin-centered ball inside $CF=\{\sum f_i\mathcal F_i, f_i\in[0,f_{i,max}]\}$ (Eq 12.15); fix units with characteristic length $r$ ($m/r$) and origin near the CoM. Choosing contacts: sample then gradient-ascend Qual (project onto surface tangent planes).

**Contact forces & friction (12.2, PDF 502–512)**
- Coulomb Eq (12.16): $\sqrt{f_x^2+f_y^2}\le\mu f_z$, $f_z\ge0$; friction angle $\alpha=\tan^{-1}\mu$; sliding ⇒ on cone edge, opposing slip. $\mu_s\ge\mu_k$, typical 0.1–1. Polyhedral (inscribed 4-edge pyramid $(\pm\mu,0,1),(0,\pm\mu,1)$) approximation underestimates friction (safe for grasping). Planar cones are exact with 2 edges.
- Wrench cone $WC=\mathrm{pos}(\{\mathcal F_i\})$ from friction-cone edges; composite cone = pos of all.
- Planar wrench as arrow: base at $\frac{1}{f_x^2+f_y^2}(m_zf_y,-m_zf_x)$ (PDF 505); **moment labeling** (+/−/±) represents homogeneous planar wrench cones.
- **Force closure** (12.2.3): composite wrench cone = whole wrench space; same rank + LP test using friction-cone-edge wrenches. $\mu=0$ ⇒ force closure ≡ first-order form closure. Planar: **2 frictional contacts suffice** (contacts "see" each other inside both cones); equilateral triangle needs $\mu\ge\tan30^\circ\approx0.577$ (Fig 12.22). Spatial: 2 contacts insufficient (no moment about their line); **Thm 12.8**: 3 non-collinear frictional contacts give spatial force closure iff every friction cone cuts the contact plane $S$ in a planar cone and $S$ is in planar force closure (proof via internal forces $\eta_i$, Eqs 12.17–12.30). Quality: prefer grasps requiring the smallest $\mu$.
- Duality (12.2.4): equality motion constraints = wrench freedoms: B: 0/0, S: 1/1, R: 3/3 (planar 0, 1, 2).

**Manipulation (12.3, PDF 512–519):** dynamics with contacts Eq (12.31) $\mathcal F_{ext}+\sum k_i\mathcal F_i=G\dot{\mathcal V}-[\mathrm{ad}_{\mathcal V}]^TG\mathcal V$; solve by **contact-mode case analysis** (can be ambiguous or inconsistent under rigid-body + Coulomb). Quasistatic Eq (12.32). Examples: 12.9 dynamic grasp (block held by accelerating fingers; RR feasible iff $-5g\le a_x\le-g$); 12.10 meter-stick trick (finger bearing more load sticks; CoM migrates to the midpoint; never falls quasistatically); 12.11 arch stability as LP over 16 cone-edge coefficients; 12.12 peg-in-hole jamming vs wedging.

**Exercises worth doing:** 12.3/12.4 (write contact constraints for frictionless/point-with-friction/soft contact — quick R2 drill), 12.17–12.20 (force-closure with μ), **12.25–12.28 (form/force-closure LP programs)**, 12.21 (table with 4 legs: statically indeterminate normal forces — nice F4 nullspace drill).

**Conventions / pitfalls:** MR orders wrenches $(m,f)$ and twists $(\omega,v)$, all **space frame** in this chapter; normal points into body A. Force closure here is defined purely by contact geometry + friction (Handbook calls it "frictional form closure" and additionally requires internal-force controllability).

---

## Ch. 13 Wheeled Mobile Robots (PDF pp. 531–582, book pp. 513–564) — Lessons: A3 (C-space/planning for car-like robots), A4 (RRT/PRM with nonholonomic local planners), A5/A6 (odometry = motion model for KF/EKF/PF/MCL), A1 (task-space kinematic control), R2 (SE(2) twists, matrix exponential), F7 (integrating a constant twist)
**Big idea:** A wheeled robot's chassis lives in SE(2), $q=(\phi,x,y)$. Omnidirectional robots (omni/mecanum wheels) have no velocity constraint, so they are kinematically just "a point you can push in any direction" — invert a rank-3 wheel matrix $H$. Nonholonomic robots (unicycle, diff-drive, car) have one non-integrable Pfaffian constraint (no sideways slip): they can still reach every configuration (Lie-bracket "parallel parking"), but no continuous time-invariant feedback can stabilize a point (Brockett). Odometry integrates the body twist implied by wheel increments with the SE(2) exponential.

**Key equations**
- Eq (13.1)–(13.2): $\mathcal V_b=(\omega_{bz},v_{bx},v_{by})$ relates to $\dot q$ by a rotation by $-\phi$ on the linear part.
- **Omni wheel kinematics** Eq (13.3)–(13.4): wheel-frame velocity $v=v_{drive}(1,0)+v_{slide}(-\sin\gamma,\cos\gamma)$ ⇒ $u=\frac1r(v_x+v_y\tan\gamma)$; $\gamma=0$ omniwheel, $\pm45^\circ$ mecanum. Eq (13.5)–(13.6): $h_i(\phi)=\frac{1}{r_i\cos\gamma_i}\big[x_i\sin(\beta_i+\gamma_i)-y_i\cos(\beta_i+\gamma_i),\ \cos(\beta_i+\gamma_i+\phi),\ \sin(\beta_i+\gamma_i+\phi)\big]$. Stack: $u=H(\phi)\dot q$ (13.7), $u=H(0)\mathcal V_b$ (13.8); need rank $H(0)=3$. With $m>3$ wheels, $u\notin\mathrm{range}(H(0))$ ⇒ skidding.
- Three-omniwheel model Eq (13.9); four-mecanum (youBot) Eq (13.10): $H(0)=\frac1r\begin{bmatrix}-\ell-w&1&-1\\\ell+w&1&1\\\ell+w&1&-1\\-\ell-w&1&1\end{bmatrix}$.
- Wheel speed limits ⇒ feasible-twist polyhedron with $2m$ faces (Fig 13.6).
- Omni control Eq (13.11): feedforward + PI on $q$, then $u=H(\phi)\dot q$.
- **Nonholonomic models** ($\dot q=G(q)u$, driftless, linear in controls): unicycle (13.12)/(13.13); diff-drive (13.14)/(13.15) with wheel separation $2d$: $\dot\phi=\frac{r}{2d}(u_R-u_L)$, $\dot x=\frac r2(u_L+u_R)\cos\phi$, $\dot y=\frac r2(u_L+u_R)\sin\phi$; car (13.16) $\dot\phi=\frac{\tan\psi}{\ell}v$; steering-angle elimination (13.17) $\psi=\tan^{-1}(\ell\omega/v)$.
- **Canonical model** Eq (13.18): $\dot\phi=\omega$, $\dot x=v\cos\phi$, $\dot y=v\sin\phi$; constraint $\dot x\sin\phi-\dot y\cos\phi=0$. Conversions: unicycle $u_1=v/r,u_2=\omega$; diff-drive $u_L=(v-\omega d)/r$, $u_R=(v+\omega d)/r$. Only the $(v,\omega)$ control *sets* differ (Fig 13.11: unicycle box, diff-drive diamond, car bowtie, forward-only half bowtie).
- **Off-axle reference point** Eq (13.19)–(13.21): $P=(x_r,y_r)$ in {b}; $(v,\omega)=J^{-1}(\dot x_P,\dot y_P)$ — singular iff $x_r=0$ (points on the wheel axle can only move along the heading). Enables "feedback linearization" of a point.
- Controllability (13.3.2): single integrator (13.23), linear $\dot x=Ax+B\nu$ (13.24), **Kalman rank** $[B\ AB\cdots A^{n-1}B]$. **Thm 13.1 (Brockett):** $\mathrm{rank}\,G(0)<\dim q$ ⇒ no continuous time-invariant stabilizing feedback. Defs 13.2–13.3: controllable, STLA, STLC.
- **Lie bracket** Def 13.4/Eq (13.28): $[g_i,g_j]=\frac{\partial g_j}{\partial q}g_i-\frac{\partial g_i}{\partial q}g_j$; net motion of $\epsilon g_i,\epsilon g_j,-\epsilon g_i,-\epsilon g_j$ is $\epsilon^2[g_i,g_j]+O(\epsilon^3)$ (Eqs 13.26–13.27). Same as the twist Lie bracket of Sec 8.2.2 (Ex 13.20: $[A,B]=AB-BA$ for left-invariant fields on SE(2)).
- Example 13.5: $g_1=(0,\cos\phi,\sin\phi)$, $g_2=(1,0,0)$ ⇒ $[g_1,g_2]=(0,\sin\phi,-\cos\phi)$ — sideways "parallel parking"; Example 13.8: $\det[g_1\,g_2\,g_3]=1$ ⇒ full rank.
- **Thm 13.7 (Chow-based):** $\dim\mathrm{Lie}(G)(q)=n$ and $\mathrm{span}(U)=\mathbb R^m$ ⇒ STLA; plus $\mathrm{pos}(U)=\mathbb R^m$ ⇒ STLC. Unicycle/diff-drive/reverse car STLC; forward-only car only STLA. Ex 13.9: unicycle incl. wheel angle still STLC (det $=-r^2$); Ex 13.10: full diff-drive with both wheel angles has Lie rank 4 of 5 ⇒ one hidden holonomic constraint.
- **Optimal paths:** Thm 13.11 Dubins (forward-only): CSC or CC$_\alpha$C ($\alpha>\pi$) — LSL, LSR, RSL, RSR, RLR, LRL. Thm 13.12 Reeds–Shepp: 9 classes, 48 types. Thm 13.13 diff-drive minimum time: only straight lines and spins in place, 40 types (Table 13.1); worked example FRB (4.749) beats LFR (5.103).
- Planning with obstacles: grid planners with extremal controls; RRT/PRM with Dubins/Reeds–Shepp local planner; or plan holonomically then **recursively subdivide** and replace segments by Reeds–Shepp paths (works because STLC + open free space; Fig 13.18).
- **Trajectory tracking:** reference-point P-control (13.29) (can flip heading, Fig 13.19); error coordinates (13.30) $q_e=(\phi-\phi_d,\ R(\phi_d)^T(p-p_d))$ and nonlinear law Eq (13.31):
  $v=(v_d-k_1|v_d|(x_e+y_e\tan\phi_e))/\cos\phi_e$, $\omega=\omega_d-(k_2v_dy_e+k_3|v_d|\tan\phi_e)\cos^2\phi_e$; needs $v_d\ne0$, $|\phi_e|<\pi/2$, nominal controls in int($U$).
- **Odometry (13.4)** — the **motion model** used in A5/A6: $\mathcal V_b=F\Delta\theta$ with $F=H^\dagger(0)$ (Eq 13.32 omni, 13.33 mecanum: $F=\frac r4\begin{bmatrix}-1/(\ell+w)&1/(\ell+w)&1/(\ell+w)&-1/(\ell+w)\\1&1&1&1\\-1&1&-1&1\end{bmatrix}$), diff-drive Eq (13.34) $\omega=\frac{r}{2d}(\Delta\theta_R-\Delta\theta_L)$, $v_x=\frac r2(\Delta\theta_L+\Delta\theta_R)$. Integrate exactly: $T_{bb'}=e^{[\mathcal V_{b6}]}$ ⇒ Eq (13.35): if $\omega_{bz}\ne0$, $\Delta x_b=(v_{bx}\sin\omega_{bz}+v_{by}(\cos\omega_{bz}-1))/\omega_{bz}$, $\Delta y_b=(v_{by}\sin\omega_{bz}+v_{bx}(1-\cos\omega_{bz}))/\omega_{bz}$; rotate to {s} with $\phi_k$ (13.36); $q_{k+1}=q_k+\Delta q$. (Exact arc integration, better than the Euler update $x+=v\cos\phi$ often used in Thrun's motion model.)
- **Mobile manipulation (13.5):** $X=T_{sb}(q)T_{b0}T_{0e}(\theta)$; $\mathcal V_e=[J_{base}\ J_{arm}](u,\dot\theta)$, $J_{base}=[\mathrm{Ad}_{T_{0e}^{-1}T_{b0}^{-1}}]F_6$ with $F_6$ = $F$ padded with zero rows for $(\omega_x,\omega_y,v_z)$; $J_{arm}=J_b$. Control with Eq (13.37) = Eq (11.16) and $(u,\dot\theta)=J_e^\dagger\mathcal V$ (weighted pseudoinverse possible). Example Fig 13.24 (diff-drive + 1R arm).

**Exercises worth doing:** 13.14–13.16 (Pfaffian constraints), **13.18/13.20** (Lie bracket by Taylor expansion and as $AB-BA$ on se(2) — ties R2 to nonholonomy), 13.22, **13.25–13.27** (point tracking, nonlinear tracking, odometry from encoders — A5/A6 motion model practice), 13.23–13.24 (grid/RRT planner for a car — A3/A4), **13.33 youBot capstone** (MR Coursera capstone: odometry + mobile-manipulator Jacobian + feedforward-PI task-space control — superb integrated project for A1/R5/R6).

**Conventions / pitfalls:** MR orders planar twists $(\omega,v_x,v_y)$ and configurations $(\phi,x,y)$ — heading first (most mobile-robot texts and ROS use $(x,y,\theta)$). Wheel separation is $2d$ (not $d$). $H(\phi)$ depends on heading, $H(0)$ does not. Brockett: you can *track trajectories* with smooth feedback but not *stabilize a pose*.

---

## Appendix A Summary of Useful Formulas (PDF pp. 583–592, book pp. 565–574) — Lessons: R1–R8, F3, F5, F7 (formula sheet)
**Big idea:** A one-stop crib sheet for the whole book — ideal as the "allowed formula sheet" template for RDKDC exams, as long as you translate MR's $(\omega,v)$ ordering into MLS's $(v,\omega)$.

**Contents worth copying onto your own sheet (all verified against the page):**
- Ch. 2: dof = freedoms − independent constraints; **Grübler** $\text{dof}=m(N-1-J)+\sum f_i$ ($m=3$ planar, 6 spatial); Pfaffian $A(\theta)\dot\theta=0$.
- Ch. 3 side-by-side SO(3)/SE(3) table (PDF 584–585): $R^{-1}=R^T$; $T^{-1}=\begin{bmatrix}R^T&-R^Tp\\0&1\end{bmatrix}$; **pre-multiply = rotate/translate about space-frame axes, post-multiply = about body-frame axes**; screw axis $\mathcal S=(\omega,v)$ with $v=-\hat s\times q+h\hat s$; skew identities $[\omega]x=-[x]\omega$, $R[\omega]R^T=[R\omega]$; $\dot RR^{-1}=[\omega_s]$, $R^{-1}\dot R=[\omega_b]$, $\dot TT^{-1}=[\mathcal V_s]$, $T^{-1}\dot T=[\mathcal V_b]$; $[\mathrm{Ad}_T]=\begin{bmatrix}R&0\\{[p]}R&R\end{bmatrix}$ (MR order); Rodrigues $e^{[\hat\omega]\theta}=I+\sin\theta[\hat\omega]+(1-\cos\theta)[\hat\omega]^2$; $e^{[\mathcal S]\theta}$ translation part $(I\theta+(1-\cos\theta)[\omega]+(\theta-\sin\theta)[\omega]^2)v$; wrench change of frame $\mathcal F_a=[\mathrm{Ad}_{T_{ba}}]^T\mathcal F_b$.
- Ch. 4 PoE: space $T=e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_n]\theta_n}M$, body $T=Me^{[\mathcal B_1]\theta_1}\cdots e^{[\mathcal B_n]\theta_n}$.
- Ch. 5: $J_{si}=[\mathrm{Ad}_{e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_{i-1}]\theta_{i-1}}}]\mathcal S_i$; $J_{bi}=[\mathrm{Ad}_{e^{-[\mathcal B_n]\theta_n}\cdots e^{-[\mathcal B_{i+1}]\theta_{i+1}}}]\mathcal B_i$; $J_b=[\mathrm{Ad}_{T_{bs}}]J_s$; $\tau=J^T\mathcal F$; manipulability ellipsoid $\mathcal V^T(JJ^T)^{-1}\mathcal V=1$ (axes = eigvecs of $JJ^T$, semi-axes $\sqrt{\lambda_i}$); force ellipsoid $\mathcal F^TJJ^T\mathcal F=1$.
- Ch. 6: law of cosines; **Newton–Raphson IK**: $[\mathcal V_b]=\log(T_{sb}^{-1}(\theta^i)T_{sd})$, $\theta^{i+1}=\theta^i+J_b^\dagger\mathcal V_b$ until $\|\omega_b\|<\epsilon_\omega,\|v_b\|<\epsilon_v$; $J^\dagger=J^T(JJ^T)^{-1}$ (fat, right inverse) or $(J^TJ)^{-1}J^T$ (tall, left inverse).
- Ch. 8: Euler–Lagrange $\tau=\frac{d}{dt}\frac{\partial L}{\partial\dot\theta}-\frac{\partial L}{\partial\theta}$; four equivalent forms $M\ddot\theta+h=M\ddot\theta+c+g=M\ddot\theta+\dot\theta^T\Gamma\dot\theta+g=M\ddot\theta+C\dot\theta+g$, $c_{ij}=\sum_k\Gamma_{ijk}\dot\theta_k$; add $J^T\mathcal F_{tip}$; inertia integrals; Steiner $I_q=I_b+m(q^TqI-qq^T)$; $G_a=[\mathrm{Ad}_{T_{ba}}]^TG_b[\mathrm{Ad}_{T_{ba}}]$; $[\mathrm{ad}_\mathcal V]=\begin{bmatrix}[\omega]&0\\{[v]}&[\omega]\end{bmatrix}$; Newton–Euler single body $\mathcal F_b=G_b\dot{\mathcal V}_b-[\mathrm{ad}_{\mathcal V_b}]^TG_b\mathcal V_b$; KE $\tfrac12\mathcal V^TG\mathcal V$, $\tfrac12\dot\theta^TM\dot\theta$; full **recursive Newton–Euler** forward/backward iterations (gravity as base acceleration $\dot{\mathcal V}_0=(0,-g)$); $M=\sum J_{ib}^TG_iJ_{ib}$; task-space $\Lambda=J^{-T}MJ^{-1}$, $\eta=J^{-T}h-\Lambda\dot JJ^{-1}\mathcal V$; constrained-dynamics projections $P=I-A^T(AM^{-1}A^T)^{-1}AM^{-1}$, $P_{\ddot\theta}=I-M^{-1}A^T(AM^{-1}A^T)^{-1}A$.
- Ch. 9: straight-line joint path; constant-screw path $X(s)=X_{start}\exp(\log(X_{start}^{-1}X_{end})s)$; path-constrained dynamics $m(s)\ddot s+c(s)\dot s^2+g(s)=\tau$. Ch. 13: vector-field Lie bracket.
- **Missing from App. A** (add to your own sheet): Ch. 11 control laws (computed torque, PD+gravity, hybrid projector) and Ch. 10 planning — the appendix has nothing on Chs. 10–12.

**Pitfalls:** MR's $\mathrm{Ad}$ has $[p]R$ in the **lower-left** (because twists are $(\omega,v)$); MLS's has $\hat pR$ in the **upper-right** (twists $(v,\omega)$). Same object, permuted blocks.

---

## Appendix B Other Representations of Rotations (PDF pp. 593–602, book pp. 575–584) — Lessons: R1, F3, C1–C2 (Horn's quaternion registration), A1–A2 (hand-eye with quaternions), A5 (EKF orientation states), F7
**Big idea:** Exponential coordinates are the "natural" chart on SO(3), but every 3-parameter chart has singularities somewhere. Euler/RPY angles are intuitive but suffer gimbal lock when the middle angle aligns axes 1 and 3; unit quaternions trade one extra (constrained) number for a smooth, singularity-free double cover $S^3\to SO(3)$ with a cheap product; Cayley–Rodrigues parameters give rational formulas and a neat composition law.

### B.1 Euler angles (PDF 593–597)
- **ZYX Euler angles** = successive rotations about **body** axes $\hat z_b$ (α), $\hat y_b$ (β), $\hat x_b$ (γ): $R=\mathrm{Rot}(\hat z,\alpha)\mathrm{Rot}(\hat y,\beta)\mathrm{Rot}(\hat x,\gamma)$ (post-multiplication = body axes). Eq (B.1)/(B.2):
  $$R=\begin{bmatrix}c_\alpha c_\beta&c_\alpha s_\beta s_\gamma-s_\alpha c_\gamma&c_\alpha s_\beta c_\gamma+s_\alpha s_\gamma\\ s_\alpha c_\beta&s_\alpha s_\beta s_\gamma+c_\alpha c_\gamma&s_\alpha s_\beta c_\gamma-c_\alpha s_\gamma\\ -s_\beta&c_\beta s_\gamma&c_\beta c_\gamma\end{bmatrix}$$
- **Algorithm B.1.1 (inverse):** if $r_{31}\ne\pm1$: $\beta=\mathrm{atan2}(-r_{31},\sqrt{r_{11}^2+r_{21}^2})$ (B.3), $\alpha=\mathrm{atan2}(r_{21},r_{11})$ (B.4), $\gamma=\mathrm{atan2}(r_{32},r_{33})$ (B.5). (Second branch: negative square root ⇒ $\beta\in[90^\circ,270^\circ]$.) If $r_{31}=-1$: $\beta=\pi/2$, one-parameter family, e.g. $\alpha=0,\gamma=\mathrm{atan2}(r_{12},r_{22})$ (only $\gamma-\alpha$ determined). If $r_{31}=1$: $\beta=-\pi/2$, $\alpha=0,\gamma=-\mathrm{atan2}(r_{12},r_{22})$ (only $\alpha+\gamma$ determined). I re-derived both degenerate cases: correct. (Minor inconsistency: the stated range $\beta\in[-\pi/2,\pi/2)$ excludes the $\beta=\pi/2$ that case (b) returns.)
- **Wrist-mechanism interpretation** (Fig B.2): ZYX angles are the joint angles of a 3R wrist with axes z, y, x at zero; gimbal lock (Fig B.3) at $\beta=\pm90^\circ$ when axes 1 and 3 align = **wrist singularity** (cf. UR5 $\theta_5=0$, R5).
- **ZYZ Euler angles**, Eq (B.6): $R=\mathrm{Rot}(\hat z,\alpha)\mathrm{Rot}(\hat y,\beta)\mathrm{Rot}(\hat z,\gamma)$ with last column $(c_\alpha s_\beta,s_\alpha s_\beta,c_\beta)$ and bottom row $(-s_\beta c_\gamma,s_\beta s_\gamma,c_\beta)$; singular at $\beta=0,\pi$.
- General rule (B.7): any $\mathrm{Rot}(a_1,\alpha)\mathrm{Rot}(a_2,\beta)\mathrm{Rot}(a_3,\gamma)$ with $a_1\perp a_2$, $a_2\perp a_3$ is a valid parametrization (first/third angles range $2\pi$, middle range $\pi$).

### B.2 Roll–pitch–yaw (PDF 598)
- XYZ RPY = rotate γ about **fixed** $\hat x$, then β about fixed $\hat y$, then α about fixed $\hat z$ ⇒ pre-multiplication ⇒ $R=\mathrm{Rot}(\hat z,\alpha)\mathrm{Rot}(\hat y,\beta)\mathrm{Rot}(\hat x,\gamma)$, Eq (B.8) — **identical matrix to ZYX Euler**. "Body ZYX = fixed XYZ in reverse order." Aircraft convention: x forward, z down, y along the wing.
- **RDKDC Lab 1 note:** `EULERXYZ` in the lab is a different sequence (XYZ); derive its inverse the same way (find the entry that is $\pm\sin$ of the middle angle, use atan2 pairs for the outer angles, handle the $\cos(\text{middle})=0$ gimbal case).

### B.3 Unit quaternions (PDF 599–600) — **high priority for CIS I and ASBR**
- Definition Eq (B.9): for $R=e^{[\hat\omega]\theta}$, $\theta\in[0,\pi]$: $q=(q_0,q_1,q_2,q_3)=(\cos\frac\theta2,\ \hat\omega\sin\frac\theta2)\in S^3$. **Scalar-first** (Hamilton) convention.
- From $R$, Eqs (B.10)–(B.11): $q_0=\tfrac12\sqrt{1+r_{11}+r_{22}+r_{33}}$, $(q_1,q_2,q_3)=\frac{1}{4q_0}(r_{32}-r_{23},\ r_{13}-r_{31},\ r_{21}-r_{12})$. **Preprint typo:** third entry printed "$r_{21}-212$" — should be $r_{21}-r_{12}$. **Numerical caveat (not in the book):** this formula divides by $q_0$, which → 0 as θ → π; robust code (Shepperd's method, used by Eigen/scipy) picks the largest of $q_0,\dots,q_3$ from the diagonal first.
- To $R$, Eq (B.12) (verified numerically against Rodrigues):
  $$R=\begin{bmatrix}q_0^2+q_1^2-q_2^2-q_3^2&2(q_1q_2-q_0q_3)&2(q_0q_2+q_1q_3)\\2(q_0q_3+q_1q_2)&q_0^2-q_1^2+q_2^2-q_3^2&2(q_2q_3-q_0q_1)\\2(q_1q_3-q_0q_2)&2(q_0q_1+q_2q_3)&q_0^2-q_1^2-q_2^2+q_3^2\end{bmatrix}$$
  Axis = direction of $(q_1,q_2,q_3)$, angle $=2\cos^{-1}q_0$. **Double cover:** $q$ and $-q$ give the same $R$.
- **Product** Eqs (B.13)–(B.15): embed $q\mapsto Q=\begin{bmatrix}q_0+iq_1&q_2+iq_3\\-q_2+iq_3&q_0-iq_1\end{bmatrix}$ (an SU(2) matrix), multiply $N=QP$, read off $n$:
  $n_0=q_0p_0-q_1p_1-q_2p_2-q_3p_3$, $n_1=q_0p_1+p_0q_1+q_2p_3-q_3p_2$, $n_2=q_0p_2+p_0q_2-q_1p_3+q_3p_1$, $n_3=q_0p_3+p_0q_3+q_1p_2-q_2p_1$ — i.e. the Hamilton product $n_0=q_0p_0-\vec q\cdot\vec p$, $\vec n=q_0\vec p+p_0\vec q+\vec q\times\vec p$, and $R(n)=R_qR_p$ (verified numerically). **Preprint typo:** $Q$ is printed with entry "$q_2+ip_3$"; it must be $q_2+iq_3$ (with the typo the product is wrong).
- Not in MR but needed for C2/A2 (see Horn 1987, MLS §2.2.3): conjugate $q^*=(q_0,-\vec q)$ is the inverse; rotate a vector by $(0,R\vec x)=q\otimes(0,\vec x)\otimes q^*$; quaternion product as matrix: $q\otimes p=Q_L(q)p=Q_R(p)q$ — the left/right 4×4 matrices are what Horn's registration and Daniilidis' dual-quaternion hand-eye methods build on; $\|q_a-q_b\|$ vs. $|q_a\cdot q_b|$ as rotation distance; SLERP for interpolation. **Convention trap:** Eigen's `Quaterniond(w,x,y,z)` constructor is scalar-first but `.coeffs()` stores $(x,y,z,w)$; ROS `geometry_msgs/Quaternion` and scipy `as_quat()` are scalar-last.

### B.4 Cayley–Rodrigues parameters (PDF 600–602)
- Definition (B.16): $r=\hat\omega\tan\frac\theta2$ (stretches the radius-π ball to all of $\mathbb R^3$; singular at θ = π).
- **Preprint sign error (verified numerically):** (B.17) $R=(I-[r])(I+[r])^{-1}$ and (B.18) $[r]=(I-R)(I+R)^{-1}$ give the rotation by **−θ** (i.e. they correspond to $r=-\hat\omega\tan\frac\theta2$), contradicting (B.16) and the book's own (B.21)/(B.22). Consistent forms: $R=(I+[r])(I-[r])^{-1}$, $[r]=(R-I)(R+I)^{-1}$. Also (B.19)–(B.20) for tr R = −1 are not valid as printed: $I-R$ is singular for **every** $R\in SO(3)$ (eigenvalue 1) and $-(\cdot)$ of a rotation has det −1. Near θ = π use quaternions instead.
- Correct, verified formulas: (B.21) $R=\frac{(1-r^Tr)I+2rr^T+2[r]}{1+r^Tr}$ (the text says "Equation (B.18) can be explicitly computed" — it means (B.17)); (B.22) $[r]=\frac{R-R^T}{1+\mathrm{tr}R}$; (B.23) $1+\mathrm{tr}R=\frac{4}{1+r^Tr}$; (B.24) $R-R^T=\frac{4[r]}{1+r^Tr}$.
- **Composition** (B.25): $r_3=\frac{r_1+r_2+r_1\times r_2}{1-r_1^Tr_2}$ for $R_3=R_1R_2$ (verified). Alternative with $s=r/\sqrt{1+r^Tr}$, $\|s\|=\sin\frac\theta2$ (= quaternion vector part): $R=I+2\sqrt{1-s^Ts}[s]+2[s]^2$ (B.27), $s_3=s_1\sqrt{1-s_2^Ts_2}+s_2\sqrt{1-s_1^Ts_1}+s_1\times s_2$ (B.28).
- Angular velocity (B.29)–(B.30) (verified by finite differences): $\omega_s=\frac{2}{1+\|r\|^2}(r\times\dot r+\dot r)$, $\omega_b=\frac{2}{1+\|r\|^2}(-r\times\dot r+\dot r)$; accelerations (B.31)–(B.32).

**Exercises worth doing:** none in the appendix; instead (i) implement ZYX/ZYZ/XYZ forward+inverse with gimbal handling (= RDKDC Lab 1), (ii) implement quat↔R (Shepperd), Hamilton product, and check $R(q\otimes p)=R(q)R(p)$, (iii) verify (B.25) numerically.

**Conventions / pitfalls:** Euler (body, post-multiply) vs RPY (fixed, pre-multiply) — same matrix, reversed reading order. Quaternion scalar-first in MR/MLS/Horn; scalar-last in ROS/scipy. $q$ and $-q$ are the same rotation (flip sign before averaging/interpolating so $q_a\cdot q_b\ge0$).

---

## Appendix C Denavit–Hartenberg Parameters (PDF pp. 603–614, book pp. 585–596) — Lessons: **R4** (primary), R3, R5
**Big idea:** DH buys a minimal description (4 numbers per joint instead of 6) by forcing every link frame to obey strict placement rules, so that each neighbouring transform uses only x- and z-axis rotations/translations. PoE buys freedom (any base/tool frame, no link frames at all) at the price of 6 numbers per joint. MR teaches the **modified (Craig, proximal) DH convention** — *not* the standard (Spong, distal) convention used in the RDKDC UR5 IK handout. The two describe the same robot with the same four physical quantities, but place frames on different joint axes and shift the $a,\alpha$ indices by one.

### C.1 Assigning link frames (MR = modified/proximal DH) (PDF 603–606)
- Links 0 (ground) … n; frame {i} on link i; FK Eq (C.1) $T_{0n}=T_{01}(\theta_1)\cdots T_{n-1,n}(\theta_n)$.
- Rules (Fig C.1): **$\hat z_i$ along joint axis i** (MR/Craig) — vs Spong: $z_i$ along joint axis i+1. Origin of {i−1} where the common perpendicular of axes i−1 and i meets axis i−1; $\hat x_{i-1}$ along the common perpendicular, pointing from axis i−1 to axis i; $\hat y=\hat z\times\hat x$.
- Parameters (PDF 605): $a_{i-1}$ = length of the common perpendicular (**link length**, not the physical length); $\alpha_{i-1}$ = angle from $\hat z_{i-1}$ to $\hat z_i$ about $\hat x_{i-1}$ (**link twist**); $d_i$ = distance from the intersection of $\hat x_{i-1}$ with $\hat z_i$ to the origin of {i}, along $\hat z_i$ (**link offset**); $\phi_i$ = angle from $\hat x_{i-1}$ to $\hat x_i$ about $\hat z_i$ (**joint angle**). Revolute: $\phi_i$ variable; prismatic: $d_i$ variable, $\hat z$ along positive translation (Fig C.2).
- Special cases: intersecting axes ⇒ $a=0$, $\hat x_{i-1}=\pm$ normal to the plane of the two axes (either sign; changes the sign of α). Parallel axes ⇒ 1-parameter family of common normals; choose the one giving most zero parameters. Ground frame: usually coincide with {1} at zero ⇒ $a_0=\alpha_0=d_1=0$ (revolute) or $a_0=\alpha_0=\phi_1=0$ (prismatic). **Not every base/tool frame is DH-admissible** (Fig C.3: a pure translation along $\hat y$ cannot be written with x/z moves only) — exactly why the RDKDC project needs fixed transforms $g_{S0}$ and $g_{6T}$ around the Keating DH chain.

### C.2 Why four parameters suffice (PDF 607–608)
Rules remove 2 of 6 freedoms (no y-motions); Denavit–Hartenberg proved 4 is minimal. Some authors put the joint axis along $\hat x$ — always state your convention with a frame drawing.

### C.3 Forward kinematics (PDF 608–609)
**Modified DH link transform** (verified numerically that the closed form equals the product):
$$T_{i-1,i}=\mathrm{Rot}(\hat x,\alpha_{i-1})\mathrm{Trans}(\hat x,a_{i-1})\mathrm{Trans}(\hat z,d_i)\mathrm{Rot}(\hat z,\phi_i)=\begin{bmatrix}c\phi_i&-s\phi_i&0&a_{i-1}\\ s\phi_ic\alpha_{i-1}&c\phi_ic\alpha_{i-1}&-s\alpha_{i-1}&-d_is\alpha_{i-1}\\ s\phi_is\alpha_{i-1}&c\phi_is\alpha_{i-1}&c\alpha_{i-1}&d_ic\alpha_{i-1}\\0&0&0&1\end{bmatrix}$$
(Eqs C.2–C.5 are the four factors.) **Preprint typo:** (C.5) prints $\mathrm{Rot}(\hat z,\phi_i)$ with entries $\cos\phi_{i-1},\sin\phi_{i-1}$ — should be $\phi_i$. Steps (a)/(b) commute and (c)/(d) commute (same axis). The text also writes "$T_{i,i-1}$" in places where $T_{i-1,i}$ is meant (PDF 603, 609).

**Standard (Spong/distal) DH, for comparison (used by Keating's UR5 IK handout, Corke `DHRobot`, UR's own table):**
$$A_i=\mathrm{Rot}_z(\theta_i)\mathrm{Trans}_z(d_i)\mathrm{Trans}_x(a_i)\mathrm{Rot}_x(\alpha_i)=\begin{bmatrix}c\theta_i&-s\theta_ic\alpha_i&s\theta_is\alpha_i&a_ic\theta_i\\ s\theta_i&c\theta_ic\alpha_i&-c\theta_is\alpha_i&a_is\theta_i\\0&s\alpha_i&c\alpha_i&d_i\\0&0&0&1\end{bmatrix}$$
| | Standard (Spong) | Modified (MR App. C / Craig) |
|---|---|---|
| $z_i$ lies on | joint axis i+1 | joint axis i |
| Frame i sits at | distal end of link i | proximal end of link i |
| Row i uses | $\theta_i,d_i,a_i,\alpha_i$ | $\alpha_{i-1},a_{i-1},d_i,\theta_i$ |
| Order | rotate/translate about z, **then** x | rotate/translate about x, **then** z |
| Joint i rotates about | $z_{i-1}$ | $z_i$ |
**Conversion recipe (same robot):** take the standard table, keep the $d_i,\theta_i$ columns in place and shift the $(a,\alpha)$ columns **down one row** (row i of the modified table gets $a_{i-1},\alpha_{i-1}$ from row i−1 of the standard table; row 1 gets $a_0=\alpha_0=0$). Tool frames then differ by $\mathrm{Trans}_x(a_n)\mathrm{Rot}_x(\alpha_n)$ (zero for the UR5). **UR5 modified-DH table** (derived here, verified to give FK identical to the standard table to machine precision over 100 random configurations):
| i | $\alpha_{i-1}$ | $a_{i-1}$ (m) | $d_i$ (m) | $\theta_i$ |
|---|---|---|---|---|
| 1 | 0 | 0 | 0.089159 | θ1 |
| 2 | π/2 | 0 | 0 | θ2 |
| 3 | 0 | −0.425 | 0 | θ3 |
| 4 | 0 | −0.39225 | 0.10915 | θ4 |
| 5 | π/2 | 0 | 0.09465 | θ5 |
| 6 | −π/2 | 0 | 0.0823 | θ6 |

### C.4 Examples (PDF 609–611)
- Ex C.1 3R spatial chain (Fig 4.3): rows $(\alpha_{i-1},a_{i-1},d_i,\phi_i)$ = (0,0,0,θ1), (90°,L1,0,θ2−90°), (−90°,L2,0,θ3). Note the **joint-angle offset** −90°: the DH zero ≠ the robot's PoE zero. Always check offsets.
- Ex C.2 RRRP (Fig C.4): (0,0,0,θ1), (90°,0,0,θ2), (0,L2,0,θ3+90°), (90°,0,θ4,0) — prismatic variable in the d column.
- Ex C.3 6R arm with ZYZ-type wrist (Fig C.5): (0,0,0,θ1), (90°,0,0,θ2), (0,L1,0,θ3+90°), (90°,0,L2,θ4+180°), (90°,0,0,θ5+180°), (90°,0,0,θ6).

### C.5 Relation between PoE and DH (PDF 611–613) — the key derivation for R4
- Revolute: $M_i=\mathrm{Rot}(\hat x,\alpha_{i-1})\mathrm{Trans}(\hat x,a_{i-1})\mathrm{Trans}(\hat z,d_i)$ (C.6), $\mathrm{Rot}(\hat z,\theta_i)=e^{[A_i]\theta_i}$ with $A_i=(0,0,1,0,0,0)$ (C.7); prismatic: swap the $\hat z$ factors, $M_i=\mathrm{Rot}(\hat x,\alpha_{i-1})\mathrm{Trans}(\hat x,a_{i-1})\mathrm{Rot}(\hat z,\phi_i)$ (C.8), $A_i=(0,0,0,0,0,1)$ (C.9). Then $T_{0n}=M_1e^{[A_1]\theta_1}M_2e^{[A_2]\theta_2}\cdots M_ne^{[A_n]\theta_n}$ (C.10).
- Using $Me^PM^{-1}=e^{MPM^{-1}}$ repeatedly ⇒ (C.11) $T_{0n}=e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_n]\theta_n}M$ with $M=M_1\cdots M_n$ (C.13) and, **correctly**, $[\mathcal S_i]=(M_1\cdots M_i)[A_i](M_1\cdots M_i)^{-1}$, i.e. $\mathcal S_i=[\mathrm{Ad}_{M_1\cdots M_i}]A_i$.
  **Preprint index error:** (C.12) prints $(M_1\cdots M_{i-1})[A_i](M_1\cdots M_{i-1})^{-1}$ — off by one; the book's own expansion on the line above (C.11) uses $M_1[A_1]M_1^{-1}$ and $(M_1M_2)[A_2](M_1M_2)^{-1}$. Verified numerically: with $M_1\cdots M_i$ the PoE equals the DH FK to $5\times10^{-16}$; with $M_1\cdots M_{i-1}$ it does not. Physical meaning (C.14)–(C.15): $A_i$ = joint-i screw seen from link frame {i}; $\mathcal S_i$ = same screw seen from {0} at the zero configuration, i.e. $\mathcal S_i=[\mathrm{Ad}_{T_{0i}(0)}]A_i$.
- **Standard-DH version (what you need for Keating's UR5 table):** write $A_i=e^{[\hat z]\theta_i}N_i$ with $N_i=\mathrm{Trans}_z(d_i)\mathrm{Trans}_x(a_i)\mathrm{Rot}_x(\alpha_i)$; then $\mathcal S_i=[\mathrm{Ad}_{N_1\cdots N_{i-1}}](0,0,1,0,0,0)$ (axis $z_{i-1}$ of frame $T_{0,i-1}(0)$: $\omega_i$ = its z-column, $q_i$ = its origin, $v_i=-\omega_i\times q_i$), $M=N_1\cdots N_n$. For the UR5 (computed here, $(\omega,v)$ order, Keating base frame): $\mathcal S_1=(0,0,1,0,0,0)$, $\mathcal S_2=(0,-1,0,0.0892,0,0)$, $\mathcal S_3=(0,-1,0,0.0892,0,0.425)$, $\mathcal S_4=(0,-1,0,0.0892,0,0.8173)$, $\mathcal S_5=(0,0,-1,0.1092,-0.8173,0)$, $\mathcal S_6=(0,-1,0,-0.0055,0,0.8173)$; $M$ = rotation $[\,e_1,\ e_3,\ -e_2\,]$ (columns), position $(-0.81725,-0.19145,-0.00549)$. Max error vs DH over 100 random θ: $5\times10^{-16}$.
- **Link to MR Example 4.5 (UR5 PoE):** with $W_1=d_4$, $W_2=d_6$, $L_1=|a_2|$, $L_2=|a_3|$, $H_1=d_1$, $H_2=d_5$, MR's PoE model equals $\mathrm{Rot}_z(\pi)\,T^{DH}_{06}(\theta)$ exactly (verified, error $6\times10^{-16}$): **MR's {s} is Keating/UR's DH frame {0} rotated 180° about z, MR's end-effector frame {b} is DH frame {6}, and the joint angles/zero configuration coincide.** So for RDKDC: $g_{S0}=\mathrm{Rot}_z(\pi)$ if your course base frame matches MR's (verify against the lab's frame drawings), and $g_{6T}$ = the tool offset.

### C.6 Final comparison (PDF 613–614)
DH: minimal (4/joint), but frames are constrained, conventions vary (z- vs x-axis on joint), revolute vs prismatic bookkeeping differs, and **ill-conditioned** for nearly parallel axes (the common normal jumps) ⇒ bad for calibration. PoE: base/tool arbitrary, no link frames, uniform R/P treatment, Jacobian columns = current joint screws; only cost is 6 numbers per joint. Authors' verdict: "little practical reason" to use DH for open chains (but industry IK papers and the UR5 handout use it — so you must read both).

**Exercises worth doing:** (1) Redo Ex C.1–C.3 tables from the figures. (2) **UR5 three ways**: standard DH (Keating), modified DH (table above), PoE (MR Ex 4.5); check all agree up to $\mathrm{Rot}_z(\pi)$ — exactly the RDKDC `ur5FwdKin_DH` vs `ur5FwdKin` bridge. (3) Code both the (corrected) C.12 conversion and the standard-DH conversion.

**Conventions / pitfalls:** don't copy MR's $T_{i-1,i}$ matrix into a standard-DH table (the $a,\alpha$ indices and multiplication order differ); watch joint-angle offsets (θ2−90°, θ4+180° …); intersecting axes ⇒ sign choice for x and α; parallel axes ⇒ free choice of d; DH zero configuration may differ from the vendor's zero.

---

## Appendix D Optimization and Lagrange Multipliers (PDF pp. 615–616, book pp. 597–598) — Lessons: F6 (primary), F4 (least squares), R6 (pseudoinverse / redundancy resolution), R7 & 11.6 (Lagrange multipliers for constrained dynamics), C2 (constrained least squares in registration)
**Big idea:** Unconstrained minimum ⇒ zero gradient (and nonnegative curvature). Equality constraints $g(x)=0$ ⇒ at a regular minimum the objective's gradient is a combination of constraint gradients: $\nabla f+\frac{\partial g}{\partial x}^T\lambda=0$. Least squares and equality-constrained QPs are solved in closed form — and they are exactly how the pseudoinverses of Ch. 6 arise.

**Key results & equations**
- 1-D: $f'(x^*)=0$, $f''(x^*)\ge0$; n-D: $\nabla f(x^*)=0$ (necessary).
- Overdetermined least squares $\min\tfrac12\|Ax-b\|^2$, $A\in\mathbb R^{m\times n}$, $m>n$: normal equations (D.1) $A^TAx=A^Tb$; rank $n$ ⇒ $x^*=(A^TA)^{-1}A^Tb$ (= left pseudoinverse). (Printed expansion "$\tfrac12x^TA^TAx-2b^TAx+b^Tb$" is missing parentheses: it is $\tfrac12(x^TA^TAx-2b^TAx+b^Tb)$.)
- Equality constraints $g:\mathbb R^n\to\mathbb R^m$, regular point (rank $\partial g/\partial x=m$): **Lagrange condition (D.2)** $\nabla f(x^*)+\frac{\partial g}{\partial x}^T(x^*)\lambda^*=0$ plus $g(x^*)=0$: $n+m$ equations in $n+m$ unknowns.
- Equality-constrained QP: $\min\tfrac12x^TQx+c^Tx$ s.t. $Ax=b$ ($Q\succ0$, $A$ full row rank $m\le n$). KKT: $Qx+A^T\lambda=-c$, $Ax=b$. With $B=(AQ^{-1}A^T)^{-1}$, $G=Q^{-1}A^TB$:
  $$x^*=Gb-(I-GA)Q^{-1}c,\qquad \lambda^*=-(Bb+BAQ^{-1}c).$$
  **Preprint sign errors (verified numerically against a direct KKT solve):** the book prints $x=Gb+(I-GA)Q^{-1}c$ and $\lambda=Bb+BAQ^{-1}c$; both $c$-terms (and λ's overall sign) are wrong. Also "$Q\in\mathbb R^n$" should be $Q\in\mathbb R^{n\times n}$.
- **Why it matters (connections):** with $Q=I$, $c=0$: $x^*=A^T(AA^T)^{-1}b$ = minimum-norm solution = right pseudoinverse ⇒ $\dot\theta=J^\dagger\mathcal V$ for redundant arms (R6); with $Q=W$ ⇒ weighted pseudoinverse $W^{-1}J^T(JW^{-1}J^T)^{-1}$ (used for wheel/joint weighting in 13.5); with $c=-W\dot\theta_0$ ⇒ null-space secondary objective. Same algebra gives $\lambda=(A\Lambda^{-1}A^T)^{-1}(\dots)$ in hybrid control (Eq 11.58) and the constrained dynamics in Ch. 8.

**Pitfalls:** necessary conditions only (no inequality/KKT complementarity, no second-order sufficiency; see a real optimization text for F6); regularity (constraint qualification) matters.

---

## Bibliography and Index (PDF pp. 617–642, book pp. 599–624)
Bibliography of ~190 references (pp. 599–616); Index (pp. 617–624). Skimmed for completeness. References cited in the chapters above that are worth knowing: [74] Khatib unified motion/force (operational-space) control; [99] Luh–Walker–Paul resolved-acceleration control; [107] Mason natural/artificial constraints; [144] Raibert & Craig hybrid control; [56–58] Hogan impedance control; [184] Takegaki & Arimoto PD+gravity; [122] Murray–Li–Sastry (MLS); [177] Spong et al.; [171] Siciliano et al.; [32] Craig; [34] Denavit & Hartenberg (1955) original DH paper; [19] Brockett stabilization theorem; [28] Chow; [41] Dubins; [146] Reeds & Shepp; [5] Balkcom & Mason diff-drive time-optimal; [89] three-finger spatial force closure; [149–151] Rimon & Burdick second-order mobility.

---

## Errata found in this range of the May 2017 preprint (all checked against the rendered page; numerical ones verified in Python)
| Where (PDF / book p.) | Printed | Correct |
|---|---|---|
| Eq (11.18), PDF 438 / 420 | $\log(R^T(d)R_d(t))$ | $\log(R^T(t)R_d(t))$ |
| Summary bullet, PDF 468 / 450 | $\tau=K_p\theta_e+K_d\dot\theta+\tilde g(\theta)$ | $\tau=K_p\theta_e-K_d\dot\theta+\tilde g(\theta)$ (= $+K_d\dot\theta_e$ for setpoints), as in Eq (11.39) |
| Eq (B.11), PDF 599 / 581 | $r_{21}-212$ | $r_{21}-r_{12}$ |
| Eq (B.13), PDF 600 / 582 | $Q$ entry $q_2+ip_3$ | $q_2+iq_3$ |
| Eqs (B.17)–(B.18), PDF 600 / 582 | $R=(I-[r])(I+[r])^{-1}$, $[r]=(I-R)(I+R)^{-1}$ | inconsistent with (B.16)/(B.21)/(B.22) (gives −θ); use $R=(I+[r])(I-[r])^{-1}$, $[r]=(R-I)(R+I)^{-1}$ |
| Eqs (B.19)–(B.20), PDF 601 / 583 | alternatives for tr R = −1 | invalid as printed ($I-R$ singular for all $R\in SO(3)$; $-(\text{rotation})\notin SO(3)$) |
| Text before (B.21), PDF 601 / 583 | "Equation (B.18) can be explicitly computed as" | (B.17) |
| Eq (C.5), PDF 608 / 590 | $\cos\phi_{i-1},\sin\phi_{i-1}$ | $\cos\phi_i,\sin\phi_i$ |
| Eq (C.12), PDF 612 / 594 | $(M_1\cdots M_{i-1})[A_i](M_1\cdots M_{i-1})^{-1}$ | $(M_1\cdots M_i)[A_i](M_1\cdots M_i)^{-1}$ |
| App. D QP solution, PDF 616 / 598 | $x=Gb+(I-GA)Q^{-1}c$, $\lambda=Bb+BAQ^{-1}c$ | $x=Gb-(I-GA)Q^{-1}c$, $\lambda=-(Bb+BAQ^{-1}c)$; also $Q\in\mathbb R^{n\times n}$ |

---

## Corrections & upgrades for the knowledge base
- **[R4] "Learn from: … MR Appendix C"** → MR App. C uses **modified (Craig/proximal) DH**: $T_{i-1,i}=\mathrm{Rot}_x(\alpha_{i-1})\mathrm{Trans}_x(a_{i-1})\mathrm{Trans}_z(d_i)\mathrm{Rot}_z(\phi_i)$ with $\hat z_i$ on joint axis i (PDF 604–608), whereas the lesson (correctly) teaches standard Spong DH for Keating's handout → add a warning: "MR App. C is the *modified* convention; read it for concepts (C.1 special cases, C.2, C.5–C.6), but do not copy its link matrix or tables into the standard-DH UR5 code." Add the conversion rule (shift the $a,\alpha$ columns down one row) and the UR5 modified-DH table from this file.
- **[R4] "Craig's modified DH orders the operations differently"** → incomplete: the frames are also *placed differently* ($z_i$ on axis i vs axis i+1) and the $a,\alpha$ parameters carry index i−1 (PDF 604–605) → rephrase: "Modified DH attaches frame i to the proximal end of link i (z_i on joint i), uses $(\alpha_{i-1},a_{i-1},d_i,\theta_i)$ and multiplies x-moves before z-moves."
- **[R4] Build step "compare DH with your PoE"** → upgrade with exact facts (verified here): MR Example 4.5's UR5 PoE (with UR's exact numbers $W_1=0.10915$, $W_2=0.0823$, $L_1=0.425$, $L_2=0.39225$, $H_1=0.089159$, $H_2=0.09465$) equals $\mathrm{Rot}_z(\pi)\,T^{DH}_{06}(\theta)$ to $6\times10^{-16}$ — same joint angles, same tool frame. Give the conversion formula for standard DH: $\mathcal S_i=[\mathrm{Ad}_{N_1\cdots N_{i-1}}]\hat z$, $M=N_1\cdots N_n$, $N_i=\mathrm{Trans}_z(d_i)\mathrm{Trans}_x(a_i)\mathrm{Rot}_x(\alpha_i)$.
- **[R4] if MR C.5 is cited for DH→PoE** → its Eq (C.12) has an index error; the correct screw is $\mathcal S_i=[\mathrm{Ad}_{M_1\cdots M_i}]A_i$ (PDF 612). Add a self-test: "Why can't every base frame be a DH frame?" (Fig C.3: no y-translations, PDF 607).
- **[R4] PoE vs DH trade-off** → add MR C.6's point that DH is **ill-conditioned for nearly parallel axes** (UR5 joints 2–4 are parallel!), which is why calibration uses PoE/other models (PDF 613).
- **[R8] sign convention** → the lesson uses $e=\theta-\theta_d$ (MLS); MR uses $\theta_e=\theta_d-\theta$ (PDF 422, 432). Both lesson formulas are internally correct; add a one-line note so students don't mix signs when reading MR ch. 11.
- **[R8] "stabilizes a set-point (proof via a Lyapunov function)"** → precise statement (PDF 450–451): $V=\tfrac12\theta_e^TK_p\theta_e+\tfrac12\dot\theta^TM\dot\theta$ gives only $\dot V=-\dot\theta^TK_d\dot\theta\le0$ (negative *semi*-definite, using skew-symmetry of $\dot M-2C$); convergence needs **Krasovskii–LaSalle** (Ex 11.12). Assumes zero friction and exact gravity model. → fix wording to "Lyapunov + LaSalle".
- **[R8] computed torque** → add: (i) MR's form includes an optional integral term and evaluates $\tilde M,\tilde h$ at the **actual** state (Eq 11.35/11.37); (ii) the error dynamics are unit-mass, so pick $k_d=2\sqrt{k_p}$ for critical damping, $\omega_n=\sqrt{k_p}$, 2% settling $\approx 4/\sqrt{k_p}$; (iii) PID on a raw joint needs $0<K_i<(b+K_d)K_p/M$ (Eq 11.30 conditions, PDF 443) — an upper bound students often miss; (iv) gravity-only PD leaves steady-state error $K_p\theta_e=mgr\cos\theta$ (Eq 11.27).
- **[R8] missing topics** → add short sections (all MR ch. 11): task-space computed torque $\tau=J_b^T(\tilde\Lambda(\dots)+\tilde\eta)$ (Eq 11.47); force control with damping (Eq 11.54); hybrid motion–force with $P=I-A^T(A\Lambda^{-1}A^T)^{-1}A\Lambda^{-1}$ (Eq 11.60–11.61); impedance vs admittance control (Eq 11.65–11.66). These are the "workspace control" topics listed in the course README for week 14.
- **[R8] "Learn from: MLS ch. 4.5 · MR ch. 11"** → sharpen to "MR 11.1–11.2 (error dynamics, PDF 422–431), 11.4.1–11.4.2 (PID, computed torque, PD+gravity proof, PDF 439–451), 11.4.3 (task space, PDF 451–452); MR Ex 11.7, 11.9–11.12 as the simulation project".
- **[R6/A1] resolved-rate** → cite MR Eq (11.16) as the continuous-time controller behind the lab's discrete resolved-rate law ($\mathcal V_b=k_p\log(X^{-1}X_d)^\vee$, $\dot\theta=J_b^\dagger\mathcal V_b$), and Fig 11.10 for why SE(3)-log control moves the origin on a screw path while decoupled $(R,p)$ control moves it straight (PDF 437–439). The lesson's sign ($\hat\xi=\log(g_d^{-1}g)$ with a minus) is consistent with MR's $\log(X^{-1}X_d)$ with a plus.
- **[R1] "Euler angles … hit gimbal lock, which exponential coordinates and quaternions avoid"** → exponential coordinates are also a 3-parameter chart with singular/ambiguous points (θ = π antipodal ambiguity; log numerically sensitive near θ = 0, MR B.3 p. 581); only unit quaternions (4 numbers, double cover) are globally singularity-free → reword.
- **[R1/F3/C2/A2] quaternion formulas** → add MR (B.9)–(B.15) with the two typo fixes ($r_{21}-r_{12}$; $q_2+iq_3$) and the scalar-first vs scalar-last warning (Eigen/ROS/scipy).
- **[F6] Lagrange multipliers / equality-constrained QP** → if MR App. D is cited, use the corrected solution $x^*=Gb-(I-GA)Q^{-1}c$, $\lambda^*=-(Bb+BAQ^{-1}c)$ (PDF 616).
- **[A5/A6] odometry motion model** → MR 13.4 (PDF 564–566) gives the exact SE(2)-exponential odometry update (Eq 13.35) — a nice complement to Thrun's odometry model; link it from A5/A6.

## Best-reference map
- **[R8]** → MR 11.2.2 overshoot & 11.3 velocity-input control (PDF 431–438); **11.4.1–11.4.2 PID / feedforward / computed torque / PD+gravity Lyapunov–LaSalle proof (PDF 439–451)**; 11.4.3 task-space CT (PDF 451–452); Summary (PDF 467–469, mind the sign typo); Exercises 11.5–11.7, 11.9–11.12 (PDF 471–476); software `ComputedTorque`, `SimulateControl` (PDF 469).
- **[R8 extension: force/hybrid/impedance]** → MR 11.5–11.7 (PDF 452–463); 11.8 joint-torque hardware & SEAs (PDF 463–465).
- **[R4]** → MR App. C (PDF 603–614): C.1 rules & special cases, C.2 Fig C.3, C.3 link matrix (modified DH!), C.4 examples, **C.5 DH→PoE (fix C.12)**, C.6 comparison; plus this file's standard-vs-modified table and UR5 conversions.
- **[R1 / F3]** → MR App. B (PDF 593–602): ZYX/ZYZ Euler & inverse algorithm (B.1.1), RPY (B.2), **unit quaternions (B.3, PDF 599–600)**, Cayley–Rodrigues (B.4).
- **[C1/C2/A2 quaternion background]** → MR B.3 (PDF 599–600) for definitions/product; then Horn 1987 for registration.
- **[R1–R8 formula sheet]** → MR App. A (PDF 583–592).
- **[F6 / F4]** → MR App. D (PDF 615–616) — least squares, Lagrange multipliers, equality-constrained QP (fix signs).
- **[R6 redundancy / weighted pseudoinverse]** → App. D QP + 13.5 weighted pseudoinverse remark (PDF 568).
- **[A1 task-space kinematic control]** → MR Eq 11.16 (PDF 437), 13.5 mobile manipulation + Ex 13.33 youBot capstone (PDF 566–582).
- **[A3/A4 nonholonomic planning]** → MR 13.3.3 (PDF 555–560): Dubins/Reeds–Shepp/diff-drive optimal paths, RRT/grid with nonholonomic local planners, recursive subdivision.
- **[A5/A6 motion model]** → MR 13.4 odometry (PDF 564–566).
- **[C8 cooperative/virtual-fixture robots]** → MR 11.7 impedance vs admittance control (PDF 459–463).
- **[R5 statics / wrench duality]** → MR 12.1–12.2 contact wrenches, form/force closure LP (PDF 481–512) (optional depth).
