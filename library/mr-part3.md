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
- Lyapunov/energy proof (Eq 11.39–11.43, PDF 450–451): with zero friction, perfect $\tilde g$, $K_i=0$, setpoint: closed loop $M\ddot\theta+C\dot\theta=K_p\theta_e-K_d\dot\theta$. Error energy $V=\tfrac12\theta_e^TK_p\theta_e+\tfrac12\dot\theta^TM\dot\theta$ gives $\dot V=-\dot\theta^TK_d\dot\theta+\tfrac12\dot\theta^T(\dot M-2C)\dot\theta=-\dot\theta^TK_d\dot\theta\le0$ using **skew-symmetry of $\dot M-2C$** (Prop. 8.1.2). $\dot V=0$ only at $\dot\theta=0$, and there $\theta\ne\theta_d$ implies $\ddot\theta\ne0$, so by **Krasovskii–LaSalle** (Ex 11.12) ⇒ global convergence to $\theta_d$. This is *the* classic R8 proof (also in MLS Ch. 4 and Spong Ch. 8).
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
- References: computed torque 1970s; PD+gravity Takegaki–Arimoto [184]; operational space Khatib [99]; natural/artificial constraints Mason [107]; Raibert–Craig hybrid [144]; Duffy's critique of non-invariant "orthogonality" [42]; Hogan impedance [56–58].

**Exercises worth doing (PDF 471–478):**
- 11.2 (5% settling time ⇒ $\approx3/(\zeta\omega_n)$), 11.3–11.4 (solve underdamped responses) — quick F7/R8 drills.
- **11.5** pendulum linearization, stability of upright with P control ($K_p>mgL$) — classic exam question.
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

