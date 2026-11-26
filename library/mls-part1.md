# Murray, Li & Sastry — *A Mathematical Introduction to Robotic Manipulation* (1994) — Part 1 (Ch. 1–4, start of Ch. 5)
Source: `09eb9223-mls94-complete.pdf` (474 PDF pages, free electronic edition from Caltech), PDF pages 1–240 (front matter + book pp. 1–222).

**Page offset (verified at every chapter opener): PDF page = book page + 18.** (Front matter i–xix = PDF 1–18.)

## Chapter → page map (whole book)
| Ch. | Title | Book pp. | PDF pp. | Lessons |
|---|---|---|---|---|
| — | Contents / Preface / Acknowledgements | vii–xix | 7–18 | — |
| 1 | Introduction (history, hands, outline) | 1–18 | 19–36 | context only |
| 2 | Rigid Body Motion | 19–80 | 37–98 | F3, F7, R1, R2, A1, C1 |
| 2.1 | Rigid body transformations | 20–22 | 38–40 | F3, R1 |
| 2.2 | Rotational motion in R³ (SO(3), exp coords, Rodrigues, Euler angles, quaternions) | 22–34 | 40–52 | F3, F7, R1 |
| 2.3 | Rigid motion in R³ (homogeneous coords, twists, screws, Chasles) | 34–51 | 52–69 | R2 |
| 2.4 | Velocity of a rigid body (spatial/body velocity, adjoint) | 51–61 | 69–79 | R2, R5, A1 |
| 2.5 | Wrenches and reciprocal screws | 61–70 | 79–88 | R5 (statics) |
| 2.6–2.8 | Summary / Bibliography / Exercises | 70–80 | 88–98 | R1, R2 |
| 3 | Manipulator Kinematics | 81–154 | 99–172 | R3–R6, A1 |
| 3.2 | Forward kinematics, PoE, workspace | 83–97 | 101–115 | R3, R4 |
| 3.3 | Inverse kinematics, Paden–Kahan subproblems | 97–115 | 115–133 | R6 |
| 3.4 | Manipulator Jacobian, statics, singularities, manipulability | 115–129 | 133–147 | R5 |
| 3.5 | Redundant & parallel manipulators (four-bar, Stewart) | 129–143 | 147–161 | R5, R6 |
| 3.6–3.8 | Summary / Bibliography / Exercises | 143–154 | 161–172 | R3–R6 |
| 4 | Robot Dynamics and Control | 155–210 | 173–228 | R7, R8 |
| 4.2 | Lagrange's equations, inertia, 2-link example, Newton–Euler | 156–168 | 174–186 | R7 |
| 4.3 | Dynamics of open chains (M, C, N; PoE form) | 168–179 | 186–197 | R7 |
| 4.4 | Lyapunov stability theory | 179–189 | 197–207 | R8 |
| 4.5 | Position control & trajectory tracking (computed torque, PD, workspace) | 189–200 | 207–218 | R8 |
| 4.6 | Control of constrained manipulators | 200–206 | 218–224 | R8 |
| 4.7–4.9 | Summary / Bibliography / Exercises | 206–210 | 224–228 | R7, R8 |
| 5 | Multifingered Hand Kinematics (grasp statics, force closure, rolling) | 211–264 | 229–282 | (C8/H-ish; mostly beyond the six courses) |
| 6 | Hand Dynamics and Control | 265–316 | 283–334 | — |
| 7 | Nonholonomic Behavior in Robotic Systems | 317–354 | 335–372 | (A3 background) |
| 8 | Nonholonomic Motion Planning | 355–394 | 373–412 | (A3/A4 background) |
| 9 | Future Prospects | 395–402 | 413–420 | — |
| A | Lie Groups and Robot Kinematics | 403–434 | 421–452 | R1, R2 (advanced) |
| B | Mathematica package for screw calculus | 435–440 | 453–458 | (tools) |
| — | Bibliography / Index | 441–456 | 459–474 | — |

**This file covers:** PDF 1–240 = front matter, Ch. 1, Ch. 2, Ch. 3, Ch. 4 in full, and Ch. 5 §1–§2.2 (to book p. 222).

---

## Front matter & Chapter 1 — Introduction (PDF pp. 1–36, book pp. i–18) — Lessons: context
**Big idea:** MLS is deliberately "more abstract" than Craig/Spong: everything is built on the exponential map, twists and wrenches, so kinematics (PoE), Jacobians and dynamics all fall out of one geometric picture. The preface says a senior course covers Ch. 1–4 in 12 weeks — exactly the RDKDC scope. Prerequisites are linear algebra and some signals & systems; "the student who can master the concepts in Chapter 2 should have no difficulty with the remainder of the book."

**Content (brief):**
- §1 Brief history: teleoperators at Argonne/Oak Ridge → Devol's 1954 "programmed articulated transfer device" → Unimation (first robot at GM 1961) → Stanford arm, WAVE language (1973), Cincinnati Milacron T³ (1974), PUMA (1978), SCARA (1979), CMU direct-drive arm (Asada 1981); 1980s research on recursive Newton–Euler/Lagrangian computation, computed torque, hybrid/compliant control, flexible joints.
- §2 Multifingered hands: limits of grippers (lack of dexterity, few grasps, big arm motions for small end-effector motions, no fine force control); prosthetic history (Berlichingen 1509; Childress' four classes: cosmetic, passive, body-powered, externally powered); research hands — Salisbury (Stanford/JPL, 3 fingers × 3 DOF, cable driven), Utah/MIT (4 × 4 DOF, pneumatic tendons), NYU planar hand, Berkeley Styx.
- §3 Outline: Ch. 2 rigid motion via exponential map/screws; Ch. 3 PoE kinematics (Brockett [12]) instead of Denavit–Hartenberg, Paden–Kahan subproblems, Jacobian columns = twist axes; Ch. 4 Lagrangian dynamics + Lyapunov + computed torque and PD control; Ch. 5–6 grasping and hand dynamics; Ch. 7–8 nonholonomic systems.

**Conventions / pitfalls:** The book avoids numerical exercises on purpose — you are expected to make up numbers from a real robot (for RDKDC: the UR5).

---

## Chapter 2 — Rigid Body Motion (PDF pp. 37–98, book pp. 19–80) — Lessons: F3, F7, R1, R2, R5 (statics), A1, C1
**Big idea:** A rigid motion is an element of SE(3), and every element of SE(3) is the exponential of a *twist* — a 6-vector $\xi=(v,\omega)$ that is simultaneously (i) the generator of a screw motion (Chasles), (ii) the instantaneous velocity of a rigid body, and (iii) via the dual pairing, the partner of a *wrench* $F=(f,\tau)$ (Poinsot). MLS chooses exponential (canonical) coordinates because they are global and geometric: a revolute joint *is* a zero-pitch twist, a prismatic joint *is* an infinite-pitch twist, and the next chapter's PoE formula is just a product of such exponentials. The chapter's real lesson is the **two velocities**: spatial $\hat V^s=\dot g g^{-1}$ and body $\hat V^b=g^{-1}\dot g$, related by the 6×6 adjoint.

### §2.1 Rigid body transformations (PDF 38–40, book 20–22) — F3, R1
**Key definitions:**
- *Rigid body*: distance between any two particles is constant. *Rigid motion*: continuous family $g(t):O\to\mathbb R^3$; *rigid displacement*: a single map $g$.
- Points vs. (free) vectors: $v=q-p$; induced action on vectors $g_*(v)=g(q)-g(p)$.
- **Def 2.1 (rigid body transformation):** $g:\mathbb R^3\to\mathbb R^3$ with (1) $\|g(p)-g(q)\|=\|p-q\|$ and (2) $g_*(v\times w)=g_*(v)\times g_*(w)$. Condition (2) excludes reflections like $(x,y,z)\mapsto(x,y,-z)$.
- Consequence (polarization identity $v_1^Tv_2=\tfrac14(\|v_1+v_2\|^2-\|v_1-v_2\|^2)$): inner products preserved ⇒ orthonormal right-handed frames map to orthonormal right-handed frames. All frames are right-handed ($z=x\times y$).

### §2.2 Rotational motion in R³ (PDF 40–52, book 22–34) — F3, F7, R1
**Key definitions:**
- $R_{ab}=[x_{ab}\;y_{ab}\;z_{ab}]$: columns = B's axes written in A. $SO(n)=\{R: RR^T=I,\ \det R=+1\}$ (2.2). $\det R=r_1^T(r_2\times r_3)=+1$ for right-handed frames.
- Group axioms (closure, identity, inverse $R^{-1}=R^T$, associativity) — SO(3) is the *rotation group*; it is also the *configuration space* of a body rotating about a fixed point.
- Coordinate change: $q_a=R_{ab}q_b$; composition **$R_{ac}=R_{ab}R_{bc}$ (2.3)**.
- Hat map **(2.4)–(2.5):** $\hat a=\begin{bmatrix}0&-a_3&a_2\\a_3&0&-a_1\\-a_2&a_1&0\end{bmatrix}$, $a\times b=\hat a b$. $so(n)=\{S:S^T=-S\}$ (2.10), a vector space identified with $\mathbb R^3$.
- *Exponential coordinates* $\omega\theta\in\mathbb R^3$ (the "canonical coordinates" of SO(3)); *equivalent axis representation*.

**Key results & equations:**
- **Lemma 2.1:** $R(v\times w)=(Rv)\times(Rw)$ (2.6); $R\hat w R^T=(Rw)^\wedge$ (2.7).
- **Prop 2.2:** rotations preserve distance and orientation (are rigid body transformations).
- Rotation about $\omega$ at unit speed: $\dot q=\omega\times q=\hat\omega q$ (2.8) ⇒ $q(t)=e^{\hat\omega t}q(0)$; $R(\omega,\theta)=e^{\hat\omega\theta}$ (2.9).
- **Lemma 2.3:** $\hat a^2=aa^T-\|a\|^2I$ (2.12), $\hat a^3=-\|a\|^2\hat a$ (2.13).
- **Rodrigues' formula (2.14):** $e^{\hat\omega\theta}=I+\hat\omega\sin\theta+\hat\omega^2(1-\cos\theta)$, $\|\omega\|=1$. For $\|\omega\|\ne1$: $e^{\hat\omega\theta}=I+\frac{\hat\omega}{\|\omega\|}\sin(\|\omega\|\theta)+\frac{\hat\omega^2}{\|\omega\|^2}(1-\cos(\|\omega\|\theta))$ (Ex. 12).
- **Prop 2.4:** $e^{\hat\omega\theta}\in SO(3)$ (proof: $(e^{\hat\omega\theta})^{-1}=e^{-\hat\omega\theta}=e^{\hat\omega^T\theta}=(e^{\hat\omega\theta})^T$; det = +1 by continuity from $\theta=0$).
- **Prop 2.5 (exp is surjective onto SO(3)) — the log map, constructive:** expanded form (2.16) (with $v_\theta=1-\cos\theta$). Then
  - $\operatorname{tr}R=1+2\cos\theta$ ⇒ **$\theta=\cos^{-1}\!\big(\tfrac{\operatorname{tr}R-1}{2}\big)$ (2.17)**;
  - **$\omega=\frac{1}{2\sin\theta}\,(r_{32}-r_{23},\ r_{13}-r_{31},\ r_{21}-r_{12})$ (2.18)** if $\theta\neq0$.
  - $R=I$ ⇒ $\theta=0$, $\omega$ arbitrary. Otherwise exactly two solutions with $\theta\in[0,2\pi)$: $(\omega,\theta)$ and $(-\omega,2\pi-\theta)$.
- **Theorem 2.6 (Euler):** every $R\in SO(3)$ is a rotation about a fixed axis $\omega$ by $\theta\in[0,2\pi)$. Non-uniqueness + "singularity of the equivalent axis representation" at $R=I$.
- **ZYZ Euler angles (2.19):** $R_{ab}=R_z(\alpha)R_y(\beta)R_z(\gamma)$ (rotations about *current/body* axes ⇒ post-multiply). Third column $=(c_\alpha s_\beta,\ s_\alpha s_\beta,\ c_\beta)$, third row $=(-s_\beta c_\gamma,\ s_\beta s_\gamma,\ c_\beta)$. **Inverse (2.20)** for $s_\beta\neq0$: $\beta=\operatorname{atan2}(\sqrt{r_{31}^2+r_{32}^2},\,r_{33})$, $\alpha=\operatorname{atan2}(r_{23}/s_\beta,\,r_{13}/s_\beta)$, $\gamma=\operatorname{atan2}(r_{32}/s_\beta,\,-r_{31}/s_\beta)$. Singular at $R=I$ ($(\alpha,0,-\alpha)$ all give $I$).
- ZYX (Fick) = yaw–pitch–roll: $R_{ab}=R_z(\psi)R_y(\theta)R_x(\phi)$; YZX = Helmholtz. Both nonsingular at $I$ but singular elsewhere (ZYX at pitch $\theta=\pm\pi/2$; the book only writes $-\pi/2$). "Singularities can never be eliminated in any 3-dimensional representation of SO(3)" (topological fact; like charting a sphere).
- **Quaternions:** $Q=(q_0,\vec q)$, $Q\cdot P=(q_0p_0-\vec q\cdot\vec p,\ q_0\vec p+p_0\vec q+\vec q\times\vec p)$; conjugate $Q^*=(q_0,-\vec q)$; $Q^{-1}=Q^*/\|Q\|^2$. Unit quaternion of $e^{\hat\omega\theta}$: $Q=(\cos\frac\theta2,\ \omega\sin\frac\theta2)$; $Q_{ac}=Q_{ab}\cdot Q_{bc}$; inverse map $\theta=2\cos^{-1}q_0$, $\omega=\vec q/\sin(\theta/2)$. Global (no singularity), 2-to-1 cover (Ex. 6c). Rotation of a point: vector part of $QXQ^*$ $=(q_0^2-\vec q\cdot\vec q)x+2q_0(\vec q\times x)+2(x\cdot\vec q)\vec q$ (Ex. 6b).

### §2.3 Rigid motion in R³ (PDF 52–69, book 34–51) — R2, C1
**Key definitions:**
- $SE(3)=\{(p,R):p\in\mathbb R^3,R\in SO(3)\}=\mathbb R^3\times SO(3)$ (2.21). $g_{ab}=(p_{ab},R_{ab})$ = configuration of B relative to A; $q_a=p_{ab}+R_{ab}q_b$ (2.22); on vectors $g_*(v)=Rv$ (vectors are only rotated).
- **Homogeneous coordinates:** points $\bar q=(q,1)$, vectors $\bar v=(v,0)$; $\bar g=\begin{bmatrix}R&p\\0&1\end{bmatrix}$ (2.23). Syntax rules: vector±vector = vector, point+vector = point, point−point = vector, point+point meaningless.
- Composition **$\bar g_{ac}=\bar g_{ab}\bar g_{bc}=\begin{bmatrix}R_{ab}R_{bc}&R_{ab}p_{bc}+p_{ab}\\0&1\end{bmatrix}$ (2.24)**; inverse $\bar g^{-1}=\begin{bmatrix}R^T&-R^Tp\\0&1\end{bmatrix}$.
- **Twist** $\hat\xi=\begin{bmatrix}\hat\omega&v\\0&0\end{bmatrix}\in se(3)$ (2.29); **twist coordinates $\xi=(v,\omega)\in\mathbb R^6$ — linear part FIRST** (2.30); $\vee$/$\wedge$ operators (2.30)–(2.31). Revolute: $v=-\omega\times q$ (2.26) for $q$ on the axis; prismatic: $\hat\xi=\begin{bmatrix}0&v\\0&0\end{bmatrix}$ (2.28).
- **Screw (Def 2.2):** axis $l$, pitch $h$, magnitude $M$: rotate $\theta=M$ about $l$, then translate $h\theta$ along $l$; $h=\infty$ ⇒ pure translation by $M$. *Unit twist*: $\|\omega\|=1$, or $\omega=0,\|v\|=1$.

**Key results & equations:**
- **Prop 2.7:** every $g\in SE(3)$ preserves distances and cross products.
- **Prop 2.8 (exp: se(3)→SE(3)):**
  - $\omega=0$: $e^{\hat\xi\theta}=\begin{bmatrix}I&v\theta\\0&1\end{bmatrix}$ (2.32).
  - $\omega\ne0$ ($\|\omega\|=1$): **$e^{\hat\xi\theta}=\begin{bmatrix}e^{\hat\omega\theta}&(I-e^{\hat\omega\theta})(\omega\times v)+\omega\omega^Tv\theta\\0&1\end{bmatrix}$ (2.36).** Proof trick: conjugate by $g=(\omega\times v, I)$ (2.33) to get $\hat\xi'=\begin{bmatrix}\hat\omega&h\omega\\0&0\end{bmatrix}$, $h=\omega^Tv$ (2.34), then use $e^{g\hat\xi g^{-1}}=ge^{\hat\xi}g^{-1}$ (2.35, Ex. 8b).
- **Interpretation (2.37)/(2.45):** $e^{\hat\xi\theta}$ is a *relative motion* mapping points from initial to final coordinates *in the same frame*; $g_{ab}(\theta)=e^{\hat\xi\theta}g_{ab}(0)$ — the exponential **pre-multiplies** the initial configuration when $\xi$ is expressed in A.
- **Prop 2.9 (exp is surjective onto SE(3)) — the SE(3) log:** $R=I$ ⇒ $\xi=(p/\|p\|,0)$, $\theta=\|p\|$. $R\ne I$ ⇒ get $(\omega,\theta)$ from Prop 2.5, then solve **(2.38)** $[(I-e^{\hat\omega\theta})\hat\omega+\omega\omega^T\theta]\,v=p$; $A=(I-e^{\hat\omega\theta})\hat\omega+\omega\omega^T\theta$ is invertible for $\theta\in(0,2\pi)$ (the two pieces have orthogonal null spaces; Ex. 9).
- **Screw ↔ twist:** screw motion $g=\begin{bmatrix}e^{\hat\omega\theta}&(I-e^{\hat\omega\theta})q+h\theta\omega\\0&1\end{bmatrix}$ (2.40) equals $e^{\hat\xi\theta}$ with **$\xi=(-\omega\times q+h\omega,\ \omega)$**. Pure rotation: $\xi=(-\omega\times q,\omega)$; pure translation: $\xi=(v,0)$, $g=(\theta v, I)$ (2.41).
- **Screw coordinates of a twist (2.42)–(2.44):** pitch $h=\dfrac{\omega^Tv}{\|\omega\|^2}$; axis $l=\{\dfrac{\omega\times v}{\|\omega\|^2}+\lambda\omega\}$ (or $\{\lambda v\}$ if $\omega=0$); magnitude $M=\|\omega\|$ (or $\|v\|$).
- **Prop 2.10:** every screw is generated by a unit twist times $M$. Any point $q'=q+\lambda\omega$ on the axis gives the same twist; points on the axis translate by $h\theta$.
- **Theorem 2.11 (Chasles):** every rigid motion = rotation about an axis + translation parallel to it.

**Worked examples worth redoing:**
- Ex 2.1 (p. 38–39): rotation about a z-line through $(0,l_1,0)$; homogeneous matrix by inspection.
- **Ex 2.2 (p. 43–45, PDF 61–63):** log of an *absolute* configuration gives an ugly twist $\xi=\big(\tfrac{l_1-l_2}{2},\ \tfrac{(l_1+l_2)\sin\alpha}{2(1-\cos\alpha)},\ 0,0,0,1\big)$; the *relative* transform $g_{ab}(\alpha)g_{ab}(0)^{-1}$ gives the clean $\xi=(l_1,0,0,0,0,1)$. Teaches: exp coordinates of $g_{ab}$ ≠ joint twist; the joint twist generates the *relative* motion. (Verified the algebra: uses $\sin^2\alpha=(1-\cos\alpha)(1+\cos\alpha)$.)
- **Ex 2.3 (p. 49–50):** zero-pitch twist $\xi=(l_1,0,0,0,0,1)$; $e^{\hat\xi\theta}$ has translation $(l_1\sin\theta,\ l_1(1-\cos\theta),0)$; then $g_{ab}(\theta)=e^{\hat\xi\theta}g_{ab}(0)$ — the template for the PoE formula.

### §2.4 Velocity of a rigid body (PDF 69–79, book 51–61) — R2, R5, A1
**Key definitions:**
- **Lemma 2.12:** $\dot RR^{-1}$ and $R^{-1}\dot R$ are skew-symmetric.
- Spatial angular velocity **$\hat\omega^s_{ab}=\dot R_{ab}R_{ab}^{-1}$ (2.48)**; body angular velocity **$\hat\omega^b_{ab}=R_{ab}^{-1}\dot R_{ab}$ (2.49)**; **$\omega^b_{ab}=R_{ab}^{-1}\omega^s_{ab}$ (2.50)**. Point velocity: $v_{q_a}=\omega^s_{ab}\times q_a$ (2.51), $v_{q_b}=\omega^b_{ab}\times q_b$ (2.52).
- **Spatial velocity (2.53):** $\hat V^s_{ab}=\dot g_{ab}g_{ab}^{-1}$, $V^s_{ab}=\begin{bmatrix}v^s_{ab}\\\omega^s_{ab}\end{bmatrix}=\begin{bmatrix}-\dot R_{ab}R_{ab}^Tp_{ab}+\dot p_{ab}\\(\dot R_{ab}R_{ab}^T)^\vee\end{bmatrix}$. $v_{q_a}=\hat V^s_{ab}q_a=\omega^s_{ab}\times q_a+v^s_{ab}$ (2.54).
  **Interpretation:** $v^s_{ab}$ is NOT the velocity of B's origin; it is the velocity of the (possibly imaginary) body point currently passing through the origin of the spatial frame.
- **Body velocity (2.55):** $\hat V^b_{ab}=g_{ab}^{-1}\dot g_{ab}$, $V^b_{ab}=\begin{bmatrix}R_{ab}^T\dot p_{ab}\\(R_{ab}^T\dot R_{ab})^\vee\end{bmatrix}$; $v_{q_b}=\hat V^b_{ab}q_b=\omega^b_{ab}\times q_b+v^b_{ab}$ (2.56). **Interpretation:** $v^b_{ab}$ = velocity of B's origin relative to A, *expressed in the current body frame* (not "velocity relative to the body", which is zero).
- **Adjoint (2.57)–(2.58):** $V^s_{ab}=\mathrm{Ad}_{g_{ab}}V^b_{ab}$, **$\mathrm{Ad}_g=\begin{bmatrix}R&\hat pR\\0&R\end{bmatrix}$** (for $\xi=(v,\omega)$ ordering). Componentwise: $\omega^s=R\omega^b$, $v^s=R v^b+p\times(R\omega^b)$. **$\mathrm{Ad}_g^{-1}=\begin{bmatrix}R^T&-R^T\hat p\\0&R^T\end{bmatrix}=\mathrm{Ad}_{g^{-1}}$.**
- Frame-free versions: $\hat V^s=\dot gg^{-1}$ (2.59), $\hat V^b=g^{-1}\dot g$ (2.60), **$V^s=\mathrm{Ad}_gV^b$ (2.61)**.

**Key results & equations:**
- **Lemma 2.13:** $g\hat\xi g^{-1}$ is a twist with coordinates $\mathrm{Ad}_g\xi$.
- **Velocity of a screw motion (§4.3):** if $g_{ab}(\theta)=e^{\hat\xi\theta}g_{ab}(0)$ then $\hat V^s_{ab}=\hat\xi\dot\theta$ (constant twist!), and $V^b_{ab}=\mathrm{Ad}^{-1}_{g_{ab}(0)}\xi\,\dot\theta$. Uses $\frac{d}{dt}e^{\hat\xi\theta}=\hat\xi\dot\theta e^{\hat\xi\theta}$ (Ex. 8c). If $g_{ab}(0)=I$, $V^s=V^b=\xi\dot\theta$.
- **Prop 2.14:** $V^s_{ac}=V^s_{ab}+\mathrm{Ad}_{g_{ab}}V^s_{bc}$. **Prop 2.15:** $V^b_{ac}=\mathrm{Ad}^{-1}_{g_{bc}}V^b_{ab}+V^b_{bc}$. Special cases: A,B fixed to each other ⇒ $V^s_{ac}=\mathrm{Ad}_{g_{ab}}V^s_{bc}$ (2.62), $V^b_{ac}=V^b_{bc}$ (2.63) (body velocity independent of which inertial frame).
- **Moving a screw (2.64):** if a joint twist is displaced by a fixed $g$, $\xi'=\mathrm{Ad}_g\xi$, $\hat\xi'=g\hat\xi g^{-1}$ — "of tremendous importance" for Ch. 3 Jacobians.
- **Lemma 2.16:** $V^b_{ab}=-V^s_{ba}$; $V^b_{ab}=-\mathrm{Ad}_{g_{ba}}V^b_{ba}$.

**Worked examples worth redoing:**
- Ex 2.4 (p. 53): 1-DOF rotation; $\omega^s=\omega^b=(0,0,\dot\theta)$ (rotation about z commutes).
- **Ex 2.5 (p. 56–57):** 1-DOF arm, $p=(-l_2\sin\theta,\ l_1+l_2\cos\theta,\ l_0)$. $V^s$: $v^s=(l_1\dot\theta,0,0)$ — the velocity of a body point at the spatial origin; $V^b$: $v^b=(-l_2\dot\theta,0,0)$, always along body $-x$. Best example for internalising spatial vs. body velocity.
- **Ex 2.6 (p. 60):** two-link arm via Prop 2.14: $V^s_{ac}=(0,0,0,0,0,1)\dot\theta_1+(l_1\cos\theta_1,\ l_1\sin\theta_1,0,0,0,1)\dot\theta_2$ — columns add linearly: a preview of the spatial Jacobian.

### §2.5 Wrenches and reciprocal screws (PDF 79–88, book 61–70) — R5 (statics), C8/grasping
**Key definitions:**
- **Wrench** $F=(f,\tau)\in\mathbb R^6$ — **force first, then moment** (mirrors $(v,\omega)$). $F_b$ = wrench applied at B's origin, components in B.
- Power pairing: $\delta W=V^b_{ab}\cdot F_b=v\cdot f+\omega\cdot\tau$; work $W=\int V^b\cdot F_b\,dt$. Equivalent wrenches = same work for every motion.
- **Wrench transformation (2.65)–(2.66):** $F_c=\mathrm{Ad}^T_{g_{bc}}F_b$, i.e. $f_c=R_{bc}^Tf_b$, $\tau_c=-R_{bc}^T\hat p_{bc}f_b+R_{bc}^T\tau_b$ (extra torque $-p_{bc}\times f_b$). $F_a=\mathrm{Ad}^T_{g_{ba}}F_b$ = equivalent wrench at A's origin (not merely re-expressing components). Add wrenches only after moving them to a common point/frame.
- **Spatial/body wrench (2.67):** $F^b=\mathrm{Ad}_g^TF^s$; power invariant $V^b\cdot F^b=V^s\cdot F^s$.
- Ex 2.7 (grasp preview): $F_o=\sum_i\mathrm{Ad}^T_{g^{-1}_{oc_i}}F_{c_i}$.
- **Wrench along a screw (2.68):** $F=M(\omega,\ -\omega\times q+h\omega)$ ($h$ finite), $F=M(0,\omega)$ ($h=\infty$) — note the *order is swapped* vs. a twist: for a wrench the direction sits in the first (force) slot.
- **Theorem 2.17 (Poinsot):** any wrench system = force along an axis + torque about that axis. Screw coordinates of a wrench (2.69)–(2.71): $h=\dfrac{f^T\tau}{\|f\|^2}$, axis through $q=\dfrac{f\times\tau}{\|f\|^2}$ in direction $f$ (or $\tau$ if $f=0$), $M=\|f\|$ (or $\|\tau\|$). Zero-pitch twist = pure rotation; zero-pitch wrench = pure force.
- **Def 2.3 / reciprocal screws:** $F\cdot V=0$. Reciprocal product **(2.72)** $S_1\odot S_2=M_1M_2\big((h_1+h_2)\cos\alpha-d\sin\alpha\big)$; **Prop 2.18**: reciprocal ⇔ $S_1\odot S_2=0$. In twist coordinates $V_1\odot V_2=v_1^T\omega_2+v_2^T\omega_1$ (frame invariant, Ex. 17a).
- **Prop 2.19:** dim(screw system) + dim(reciprocal system) = 6. (E.g., 3 normal point forces leave ≥3-dim space of unresisted twists.)

### §2.6–2.8 Summary, bibliography, exercises (PDF 88–98, book 70–80)
Summary (p. 70–72) restates everything above — a good one-page review sheet. Bibliography credits Paden (geometry of twists) and Brockett (exponential coordinates).

**Exercises worth doing (book pp. 73–79, PDF 91–97):**
- **Ex 3** (eigenvalues of $\hat\omega$ are $0,\pm i$; of $R$ are $1,e^{\pm i\theta}$; axis = eigenvector of 1) — R1, used for log map sanity checks.
- **Ex 4** ($R\hat\omega R^T=(R\omega)^\wedge$; basis of so(3)) — R1 core identity (it is what makes Ad work).
- Ex 5 (Cayley parameters $R=(I-\hat a)^{-1}(I+\hat a)$) — nice alternative chart; F3.
- **Ex 6** (unit quaternions: group, $QXQ^*$, double cover, op counts, $\dot Q\cdot Q^*=(0,\omega/2)$) — F3/R1; matches CIS I and ASBR quaternion usage.
- **Ex 8** (matrix exponential: convergence, $ge^\Lambda g^{-1}=e^{g\Lambda g^{-1}}$, $\frac{d}{dt}e^{\Lambda\theta}$) — F7, essential.
- **Ex 9** (projection-map proof that SE(3) log exists) — R2.
- **Ex 10–11** (SO(2), SE(2), planar twists $\xi=(q_y,-q_x,1)$, pole of a planar motion, planar adjoint $\begin{bmatrix}R&(p_y,-p_x)^T\\0&1\end{bmatrix}$) — R2/A1, good quick practice.
- **Ex 13** (twist in another frame $\xi_b=\mathrm{Ad}^{-1}_{g_{ab}}\xi_a$ vs. moved screw $\xi'_a=\mathrm{Ad}_g\xi_a$) — R2; the classic confusion between "change of frame" and "move the screw".
- **Ex 14** ($\mathrm{Ad}_g^{-1}=\mathrm{Ad}_{g^{-1}}$, $\mathrm{Ad}_{g_1g_2}=\mathrm{Ad}_{g_1}\mathrm{Ad}_{g_2}$) — R2 must-do.
- **Ex 15** (prove Prop 2.15), **Ex 16** (2-DOF arm: $g$, $V^s$, $V^b$, check with point velocity) — R2/R3; directly mirrors RDKDC HW.
- Ex 17 (frame invariance of reciprocal product; inner product of twists NOT invariant; reciprocal bases) — R5 statics/grasp.
- **Ex 18 (hybrid velocity $V^h=(\dot p,\omega^s)=\mathrm{blkdiag}(R,R)V^b$)** — important bridge to Lynch–Park/Craig/"geometric Jacobian" conventions; A1 resolved-rate.

**Conventions / pitfalls (Ch. 2):**
1. **Twist ordering $\xi=(v,\omega)$** — linear first. Lynch & Park (Modern Robotics) use $\mathcal V=(\omega,v)$, so their $[\mathrm{Ad}_T]=\begin{bmatrix}R&0\\ \hat pR&R\end{bmatrix}$ is the block-swap of MLS's $\begin{bmatrix}R&\hat pR\\0&R\end{bmatrix}$. Same for wrenches: MLS $F=(f,\tau)$, MR $\mathcal F=(m,f)$.
2. $g_{ab}$ = pose of B **in** A; maps B-coordinates to A-coordinates. $R_{ab}$'s columns are B's axes in A.
3. Euler angles about moving axes ⇒ post-multiply ($R_z(\alpha)R_y(\beta)R_z(\gamma)$); about fixed axes ⇒ pre-multiply.
4. Spatial linear velocity $v^s\neq\dot p$; $v^s=\dot p-\omega^s\times p=\dot p+p\times\omega^s$. Body linear velocity $v^b=R^T\dot p$. The "hybrid" $(\dot p,\omega^s)$ (Ex. 18) is what many textbooks call the end-effector velocity.
5. $\mathrm{Ad}$ moves velocities **forward** ($V^s=\mathrm{Ad}_gV^b$); wrenches move with the **transpose, backward** ($F^b=\mathrm{Ad}_g^TF^s$).
6. Log map: $\theta=\pi$ makes (2.18) degenerate ($\sin\theta=0$, $R=R^T$) — must use $R=I+2\hat\omega^2$, i.e. $\omega\omega^T=(R+I)/2$; $\theta=0$ ⇒ axis arbitrary. The book does not spell out the $\theta=\pi$ case.
7. Exercise 6(b): the rotated point is the vector part of $QXQ^*$ $=(q_0^2-\vec q\cdot\vec q)\vec x+2\big(q_0(\vec q\times\vec x)+(\vec x\cdot\vec q)\vec q\big)$ — note the factor 2 multiplies *both* last terms (text extraction loses the big parenthesis; verified visually on PDF p. 92).
8. Exercise 12 prints the denominator as $\|\omega^2\|$; it means $\|\omega\|^2$ (as on p. 28).

---

## Chapter 3 — Manipulator Kinematics (PDF pp. 99–172, book pp. 81–154) — Lessons: R3, R4, R5, R6, A1
**Big idea:** For an open chain of revolute/prismatic joints you need only *two* frames — base S and tool T — plus one twist per joint written in S at the reference configuration. Forward kinematics is then $g_{st}(\theta)=e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_n\theta_n}g_{st}(0)$ (PoE). Differentiating the PoE gives the Jacobian *by inspection*: column $i$ of the spatial Jacobian is joint $i$'s twist carried to its current position by the joints before it. Inverse kinematics is reduced, by applying the PoE to cleverly chosen points (on axes, at intersections) and by taking norms, to three geometric Paden–Kahan subproblems. Singularities are rank drops of a matrix whose columns are screws, so they can be recognised geometrically (collinear axes, parallel coplanar axes, four concurrent axes).

### §3.1 Introduction (PDF 99–100, book 81–82)
- **Lower pairs** (six joint types = subgroups of SE(3)): revolute, prismatic, helical (screws with $h=0,\infty$, finite $h\ne0$); cylindrical (2 DOF); spherical (3 DOF, actuated version = 3 intersecting revolutes $R=e^{\hat\omega_1\theta_1}e^{\hat\omega_2\theta_2}e^{\hat\omega_3\theta_3}$, Euler-angle-like with the same singularities); planar (SE(2)).

### §3.2 Forward kinematics (PDF 101–115, book 83–97) — R3, R4
**Key definitions:**
- Joints numbered 1..n from the base; joint $i$ connects link $i-1$ to link $i$; link 0 = base. Joint space $Q=T^p\times\mathbb R^r$ ($p$ revolute in $S^1$, $r$ prismatic); DOF = number of joints.
- Sign convention (p. 84): angle about a directed axis is positive by the right-hand rule (book phrases it "clockwise as viewed along the direction of the axis", i.e. looking from behind the arrow — equivalent to counter-clockwise viewed from the arrow tip).
- Base frame **S** (fixed to link 0; letter S to avoid confusion with "body"), tool frame **T**; forward kinematics $g_{st}:Q\to SE(3)$.
- Classical chain: $g_{st}(\theta)=g_{sl_1}(\theta_1)g_{l_1l_2}(\theta_2)\cdots g_{l_{n-1}l_n}(\theta_n)g_{l_nt}$ (3.1).

**Key results & equations:**
- **PoE formula (3.3):** $\boxed{g_{st}(\theta)=e^{\hat\xi_1\theta_1}e^{\hat\xi_2\theta_2}\cdots e^{\hat\xi_n\theta_n}\,g_{st}(0)}$, with revolute $\xi_i=(-\omega_i\times q_i,\ \omega_i)$, prismatic $\xi_i=(v_i,0)$, all in S at $\theta=0$. Derivation (3.2): move the distal joint first (constant axes), or the proximal first and use $\xi_2'=\mathrm{Ad}_{e^{\hat\xi_1\theta_1}}\xi_2$, $e^{\hat\xi_2'\theta_2}=e^{\hat\xi_1\theta_1}e^{\hat\xi_2\theta_2}e^{-\hat\xi_1\theta_1}$ — same answer; the twists must be *ordered* from base to tool, but the result is independent of the order in which joints physically move.
- Footnote: MLS writes $-\omega\times q$ rather than $q\times\omega$ because it also makes sense in the planar case.
- **Choice of base frame / reference configuration:** putting S at T's reference pose gives $g_{st}(0)=I$ (Ex 3.3); joint offsets choose the reference configuration — pick one where axis points are simple.
- **Relation to Denavit–Hartenberg (3.6)–(3.10):** DH link transform $g_{l_{i-1}l_i}=\begin{bmatrix}c\phi_i&-s\phi_ic\alpha_i&s\phi_is\alpha_i&a_ic\phi_i\\ s\phi_i&c\phi_ic\alpha_i&-c\phi_is\alpha_i&a_is\phi_i\\0&s\alpha_i&c\alpha_i&d_i\\0&0&0&1\end{bmatrix}$ (= $\mathrm{Rot}_z(\phi_i)\mathrm{Trans}_z(d_i)\mathrm{Trans}_x(a_i)\mathrm{Rot}_x(\alpha_i)$, classic/distal DH). Writing $g_{l_{i-1}l_i}=e^{\hat\xi_{i-1,i}\theta_i}g_{l_{i-1}l_i}(0)$ (3.7) and conjugating gives **$\xi_i=\mathrm{Ad}_{g_{l_0l_{i-1}}(0)}\,\xi_{i-1,i}$ (3.10)** — the PoE twist is the local joint twist pushed to the base frame. Authors' verdict: building twists directly is easier; PoE uses only two frames and is "a superior alternative" to DH.
- **Workspace:** complete $W=\{g_{st}(\theta)\}\subset SE(3)$ (3.11); reachable $W_R=\{p(\theta)\}\subset\mathbb R^3$ (3.12); dextrous $W_D=\{p:\forall R\ \exists\theta,\ g_{st}(\theta)=(p,R)\}$ (3.13). Spherical wrist with T at the wrist centre ⇒ $W_D=W_R$, $W=W_R\times SO(3)$ (only if no offset).

**Worked examples worth redoing:**
- **Ex 3.1 SCARA (p. 87–89):** $\omega_{1,2,3}=\hat z$, $q_1=0$, $q_2=(0,l_1,0)$, $q_3=(0,l_1+l_2,0)$ ⇒ $\xi_1=(0,0,0,0,0,1)$, $\xi_2=(l_1,0,0,0,0,1)$, $\xi_3=(l_1+l_2,0,0,0,0,1)$, $\xi_4=(0,0,1,0,0,0)$; $g_{st}(0)=(I,(0,l_1+l_2,l_0))$. Result (3.4): $R=\mathrm{Rot}_z(\theta_1+\theta_2+\theta_3)$, $p=(-l_1s_1-l_2s_{12},\ l_1c_1+l_2c_{12},\ l_0+\theta_4)$.
- **Ex 3.2 Elbow manipulator (p. 89–91):** 6R with shoulder ($\omega_1=\hat z$, $\omega_2=-\hat x$ through $(0,0,l_0)$), elbow, spherical wrist. Twists: $\xi_2=(0,-l_0,0,-1,0,0)$, $\xi_3=(0,-l_0,l_1,-1,0,0)$, $\xi_4=(l_1+l_2,0,0,0,0,1)$, $\xi_5=(0,-l_0,l_1+l_2,-1,0,0)$, $\xi_6=(-l_0,0,0,0,1,0)$; $p(\theta)=(-s_1(l_1c_2+l_2c_{23}),\ c_1(l_1c_2+l_2c_{23}),\ l_0-l_1s_2-l_2s_{23})$. **Closest textbook analogue to the UR5 PoE homework.**
- Ex 3.3 SCARA with $g_{st}(0)=I$ (3.5) — same robot, different twists; compare with Ex 3.1.
- Ex 3.4 planar 3-link workspace: $W_R$ = annulus $[l_1-l_2-l_3,\ l_1+l_2+l_3]$; $W_D$ shrinks by $2l_3$ on both edges because T is offset from the "wrist".

### §3.3 Inverse kinematics (PDF 115–133, book 97–115) — R6
**Planar 2-link (§3.1, p. 97–98):** $r=\sqrt{x^2+y^2}$; **$\theta_2=\pi\pm\alpha$, $\alpha=\cos^{-1}\frac{l_1^2+l_2^2-r^2}{2l_1l_2}$ (3.16)**; $\theta_1=\operatorname{atan2}(y,x)\pm\beta$, $\beta=\cos^{-1}\frac{r^2+l_1^2-l_2^2}{2l_1r}$, same sign choice as for $\alpha$ (checked: $l_1=l_2=1$, target $(1,1)$ gives $(\theta_1,\theta_2)=(0,\pi/2)$ and $(\pi/2,-\pi/2)$). Lessons: split into subproblems; each subproblem has 0/1/many solutions; out-of-workspace ⇒ some subproblem fails; carry every branch.

**Paden–Kahan subproblems (§3.2, p. 99–103, PDF 117–121)** — "geometrically meaningful and numerically stable":

**Subproblem 1 — rotate a point onto another, $e^{\hat\xi\theta}p=q$ (3.17)** ($\xi$ zero-pitch, unit).
1. Pick $r$ on the axis; $u=p-r$, $v=q-r$ ⇒ $e^{\hat\omega\theta}u=v$ (3.18).
2. Project: $u'=u-\omega\omega^Tu$, $v'=v-\omega\omega^Tv$.
3. Solvable iff $\omega^Tu=\omega^Tv$ and $\|u'\|=\|v'\|$ (3.19).
4. **$\theta=\operatorname{atan2}\big(\omega^T(u'\times v'),\ u'^Tv'\big)$.** If $u'=0$ ($p=q$ on the axis) — infinitely many solutions.

**Subproblem 2 — rotate about two *intersecting* axes, $e^{\hat\xi_1\theta_1}e^{\hat\xi_2\theta_2}p=q$ (3.20)** (note: $\xi_2$ acts first).
1. If axes coincide → Subproblem 1 with $\theta_1+\theta_2=\theta$. Else ($\omega_1\times\omega_2\ne0$): find $c$ with $e^{\hat\xi_2\theta_2}p=c=e^{-\hat\xi_1\theta_1}q$ (3.21).
2. $r$ = intersection point; $u=p-r$, $v=q-r$, $z=c-r$: $e^{\hat\omega_2\theta_2}u=z=e^{-\hat\omega_1\theta_1}v$ ⇒ $\omega_2^Tu=\omega_2^Tz$, $\omega_1^Tv=\omega_1^Tz$ (3.23), $\|u\|=\|z\|=\|v\|$.
3. Write $z=\alpha\omega_1+\beta\omega_2+\gamma(\omega_1\times\omega_2)$ (3.24):
   $\alpha=\dfrac{(\omega_1^T\omega_2)\,\omega_2^Tu-\omega_1^Tv}{(\omega_1^T\omega_2)^2-1}$, $\beta=\dfrac{(\omega_1^T\omega_2)\,\omega_1^Tv-\omega_2^Tu}{(\omega_1^T\omega_2)^2-1}$,
   $\gamma^2=\dfrac{\|u\|^2-\alpha^2-\beta^2-2\alpha\beta\,\omega_1^T\omega_2}{\|\omega_1\times\omega_2\|^2}$ (from (3.25)).
4. 0, 1 or 2 real $\gamma$ ⇒ $c=r+z$; then **Subproblem 1 twice**: $e^{\hat\xi_2\theta_2}p=c$ and $e^{-\hat\xi_1\theta_1}q=c$ (i.e. $e^{\hat\xi_1\theta_1}c=q$). Geometrically: intersection of two circles.

**Subproblem 3 — rotate to a given distance, $\|q-e^{\hat\xi\theta}p\|=\delta$ (3.26)**.
1. $r$ on axis; $u=p-r$, $v=q-r$ ⇒ $\|v-e^{\hat\omega\theta}u\|^2=\delta^2$ (3.27).
2. Project: $u',v'$ as above; **$\delta'^2=\delta^2-|\omega^T(p-q)|^2$**.
3. $\theta_0=\operatorname{atan2}(\omega^T(u'\times v'),\ u'^Tv')$ (3.28).
4. Law of cosines in the plane: **$\theta=\theta_0\pm\cos^{-1}\!\Big(\dfrac{\|u'\|^2+\|v'\|^2-\delta'^2}{2\|u'\|\|v'\|}\Big)$ (3.29)** — 0, 1 or 2 solutions (circle of radius $\|u'\|$ vs circle of radius $\delta'$).

(Extra subproblems in the exercises: **2′** non-intersecting axes (Ex 5), **4** two axes to two given distances (Ex 6), **5** translation to a given distance (Ex 7).)

**Reduction tricks (§3.3, p. 104):** (i) apply both sides to a point **on an axis** to kill that joint ($e^{\hat\xi\theta}p=p$); (ii) subtract a point **on (the intersection of) proximal axes** and take **norms** (rigid motions preserve distance) to kill the proximal joints ⇒ Subproblem 3.

**Algorithms (worked IK):**
```
Elbow manipulator (Ex 3.5, p. 104–106): e^{ξ1θ1}…e^{ξ6θ6} = gd gst(0)^{-1} =: g1      (3.30)
 1. θ3:  p_w = wrist centre, p_b = shoulder intersection.
         ||e^{ξ3θ3} p_w − p_b|| = ||g1 p_w − p_b||          → Subproblem 3        (3.33)
 2. θ1,θ2: e^{ξ1θ1} e^{ξ2θ2} (e^{ξ3θ3} p_w) = g1 p_w        → Subproblem 2        (3.34)
 3. θ4,θ5: g2 := e^{-ξ3θ3} e^{-ξ2θ2} e^{-ξ1θ1} gd gst(0)^{-1};  p on ξ6 axis, not on ξ4,ξ5
         e^{ξ4θ4} e^{ξ5θ5} p = g2 p                           → Subproblem 2        (3.36)
 4. θ6:  p not on ξ6 axis: e^{ξ6θ6} p = e^{-ξ5θ5}…e^{-ξ1θ1} gd gst(0)^{-1} p  → Subproblem 1  (3.37)
 ≤ 8 solutions (2 × 2 × 2). Pattern: position of wrist centre first, then wrist.

SCARA (Ex 3.6, p. 106–107):
 1. θ4 = z − l0 (read off p(θ)).
 2. g1 := gd gst(0)^{-1} e^{-ξ4θ4};  p on ξ3, q on ξ1:  ||e^{ξ2θ2} p − q|| = ||g1 p − q||  → SP3 for θ2  (3.40)
 3. θ1: e^{ξ1θ1}(e^{ξ2θ2} p') = g1 p'  (p' on ξ3)                                            → SP1
 4. θ3: e^{ξ3θ3} = e^{-ξ2θ2} e^{-ξ1θ1} gd gst(0)^{-1} e^{-ξ4θ4}, apply to p off the ξ3 axis   → SP1   (3.41)
 ≤ 2 solutions.
```
**General IK (§3.4, p. 108–114):** for arms without intersecting axes, use elimination theory (Lee–Liang; Raghavan–Roth; Manocha–Canny). Ex 3.7 shows *dialytic elimination* (multiply by monomials until #equations = #monomials, then $\det A(x_1)=0$). For a general 6R: rewrite with DH as $g_{l_2l_3}g_{l_3l_4}g_{l_4l_5}=g^{-1}_{l_1l_2}g^{-1}_{l_0l_1}g_dg^{-1}_{l_6t}g^{-1}_{l_5l_6}$ (3.43) so cols 3–4 are free of $\theta_6$; build 14 equations (Lee–Liang's $p,l,p\cdot p,p\cdot l,p\times l,(p\cdot p)l-2(p\cdot l)p$) (3.47), eliminate, use half-angle substitution $s_i=\frac{2x_i}{1+x_i^2}$, $c_i=\frac{1-x_i^2}{1+x_i^2}$, end with a **degree-16 polynomial in $x_3=\tan(\theta_3/2)$**; θ6 by Subproblem 1. **At most 16 IK solutions for any 6-DOF spatial chain (sharp bound).** Numerical drawbacks → generalized eigenvalue formulation (Manocha–Canny).

### §3.4 The manipulator Jacobian (PDF 133–147, book 115–129) — R5, R6, A1
**Key definitions & results:**
- Spatial velocity of the end-effector $\hat V^s_{st}=\dot g_{st}g_{st}^{-1}=\sum_i\big(\frac{\partial g_{st}}{\partial\theta_i}g_{st}^{-1}\big)\dot\theta_i$ (3.51) ⇒ **spatial manipulator Jacobian** $J^s_{st}(\theta)=\big[(\frac{\partial g_{st}}{\partial\theta_1}g_{st}^{-1})^\vee\cdots(\frac{\partial g_{st}}{\partial\theta_n}g_{st}^{-1})^\vee\big]$ (3.52), $V^s_{st}=J^s_{st}\dot\theta$.
- **PoE form (3.53)–(3.54):** $\boxed{J^s_{st}(\theta)=[\xi_1\ \xi_2'\ \cdots\ \xi_n'],\quad \xi_i'=\mathrm{Ad}_{(e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_{i-1}\theta_{i-1}})}\,\xi_i}$. Column $i$ depends only on $\theta_1..\theta_{i-1}$; it is joint $i$'s twist in its *current* position, in base coordinates — computable by inspection ($\xi_i'=(-\omega_i'\times q_i',\ \omega_i')$).
- **Body Jacobian (3.55):** $\boxed{J^b_{st}(\theta)=[\xi_1^\dagger\cdots\xi_n^\dagger],\quad \xi_i^\dagger=\mathrm{Ad}^{-1}_{(e^{\hat\xi_i\theta_i}\cdots e^{\hat\xi_n\theta_n}g_{st}(0))}\,\xi_i}$ — joint twists in the *current tool frame*; $V^b_{st}=J^b_{st}\dot\theta$. Note $g_{st}(0)$ appears explicitly (choose $g_{st}(0)=I$ to simplify) and the product **includes joint $i$ itself** (harmless since $\mathrm{Ad}_{e^{\hat\xi_i\theta_i}}\xi_i=\xi_i$).
- **(3.56):** $J^s_{st}=\mathrm{Ad}_{g_{st}}J^b_{st}$.
- Point velocities: $v_{q^b}=(J^b\dot\theta)^\wedge q^b$, $v_{q^s}=(J^s\dot\theta)^\wedge q^s$; tool-origin velocity $\dot p=R_{st}v^b_{st}=\hat V^s_{st}\,\bar p$, i.e. $\dot p=v^s+\omega^s\times p$.
- **Resolved-rate idea (3.57):** $\dot\theta=(J^s_{st})^{-1}V^s_{st}(t)$; pick a workspace path $g(t)$ from $g_1$ to $g_2$, compute $\hat V^s=\dot gg^{-1}$ and integrate — moves the robot without solving IK (A1, R6).
- **Manipulator Jacobian ≠ analytic Jacobian** $\partial f/\partial\theta$ of a local parameterisation $x=f(\theta)$ (p. 120–121): the latter's columns are not twists and it inherits representation singularities (e.g. of Euler angles) that are artifacts.

**Statics (§4.2, p. 121–123) — R5:**
- Virtual work $\int\dot\theta\cdot\tau\,dt=\int V^b_{st}\cdot F_t\,dt$ ⇒ **$\tau=(J^b_{st})^TF_t$ (3.59)**, **$\tau=(J^s_{st})^TF_s$ (3.60)** — wrench must be expressed consistently with the Jacobian.
- Two questions: torques needed to *resist* a given wrench (always (3.59)) vs. wrench *produced* by given torques (needs square full-rank $J$). Redundant ⇒ internal motions; deficient ⇒ some wrenches can't be applied but are *resisted structurally* when $F\in\mathcal N(J^T)$.
- **Ex 3.10 SCARA:** $\mathcal N((J^s)^T)$ spanned by pure moments about $x$ and $y$ — the arm can neither apply nor feel them.

**Singularities (§4.3, p. 123–127) — R5:** configuration where $J$ drops rank. Near a singularity required joint rates blow up; at a singularity wrenches in $\mathcal N(J^T)$ are balanced by structure, and wrenches "in the singular direction" cannot be generated. Three classic 6-DOF cases (each proved with a different technique):
- **Ex 3.11 two collinear revolute axes** ($\omega_1=\pm\omega_2$, $\omega_i\times(q_1-q_2)=0$): column operation makes a zero column. Typical: spherical wrist with joints 4 and 6 aligned (θ5 = 0).
- **Ex 3.12 three parallel coplanar revolute axes:** change frame by Ad (invertible, rank-preserving) so the columns become $(0,0,0,0,0,1)$, $(\pm y_2,0,0,0,0,\pm1)$, $(\pm y_3,0,0,0,0,\pm1)$ — dependent. Elbow arm at its fully extended reference configuration.
- **Ex 3.13 four revolute axes through one point:** reciprocal-screw argument — any pure force through the point is reciprocal to all four twists ⇒ reciprocal system has dim 3 ⇒ the four twists span ≤ 3. (Inverse elbow arm.)
- Also: joint limits remove motion directions; multiple singularity types can coexist (elbow arm held vertically has all three yet keeps 4 DOF).

**Manipulability (§4.4, p. 127–129) — R5:** prefer the **body Jacobian** (independent of base frame). Measures: $\mu_1=\sigma_{\min}(J)$ (Ex 3.14); $\mu_2=\sigma_{\min}/\sigma_{\max}$ = inverse condition number (Ex 3.15); $\mu_3=\det J$ = volume of velocity ellipsoid, product of singular values (Ex 3.16; can be large even when $\sigma_{\min}$ is small). Alternative dextrous workspace $W_D'=\{g_{st}(\theta):\mu_i(\theta)\neq0\}\subset SE(3)$ (3.61). (Yoshikawa's $\sqrt{\det JJ^T}$ is not named, but Ex 3.10/3.11/3.12 build the ellipsoid picture.)

**Worked examples worth redoing:**
- **Ex 3.8 SCARA spatial Jacobian (p. 118):** columns $(0,0,0,0,0,1)$, $(l_1c_1,l_1s_1,0,0,0,1)$, $(l_1c_1+l_2c_{12},\ l_1s_1+l_2s_{12},0,0,0,1)$, $(0,0,1,0,0,0)$ — by inspection from $q_i'$. Check $(J^s\dot\theta)^\wedge p=\dot p$.
- **Ex 3.9 Stanford arm (p. 119–120):** $J^s=[\,\xi_1,\ \xi_2',\ (v_3',0),\ \xi_4',\xi_5',\xi_6']$ with $\omega_2'=(-c_1,-s_1,0)$, $v_3'=(-s_1c_2,\ c_1c_2,\ -s_2)$, wrist centre $q_w'=(-(l_1+\theta_3)s_1c_2,\ (l_1+\theta_3)c_1c_2,\ l_0-(l_1+\theta_3)s_2)$, $\omega_4'=(-s_1s_2,c_1s_2,c_2)$, … (3.58). Shows a prismatic column and that no differentiation is needed.

### §3.5 Redundant and parallel manipulators (PDF 147–161, book 129–143) — R5, R6
- **Redundant:** $n>p$ ($p=3$ planar, 6 spatial); $J\in\mathbb R^{p\times n}$. **Self-motion manifold** $\{\theta:g_{st}(\theta)=g_d\}$ (3.62); internal motions $J\dot\theta=0$. Minimum-norm rates **$\dot\theta=J^\dagger V$, $J^\dagger=J^T(JJ^T)^{-1}$** (Moore–Penrose). Statics $\tau=J^TF$ (3.63) still holds at static equilibrium.
- **Ex 3.17** planar 3R holding $(x,y)$: $\partial p/\partial\theta$ (3.64) and its null vector $V_N=(l_2l_3s_3,\ -l_2l_3s_3-l_1l_3s_{23},\ l_1l_2s_2+l_1l_3s_{23})$.
- **Parallel (closed-chain):** structure/loop equation $e^{\hat\xi_{11}\theta_{11}}\cdots g_{st}(0)=e^{\hat\xi_{21}\theta_{21}}\cdots g_{st}(0)$ (3.65). **Gruebler's formula (3.66):** $F=6N-\sum_i(6-f_i)=6(N-g)+\sum f_i$ (3 instead of 6 for planar; valid only for independent constraints). IK of a parallel robot is easy (solve each chain); FK is hard. Velocity: $V^s_{st}=J_1^s\dot\Theta_1=J_2^s\dot\Theta_2$ (3.67)–(3.69); rank $\rho=\dim\bigcap_iR(J_i)$ (3.70). Two singularity types: **kinematic** (ρ drops) and **actuator** (actuated joints can't generate some wrench).
- **Four-bar (§5.3):** planar Gruebler $F=3(3-4)+4=1$ (spatial version gives −2 — exercise). Planar revolute twist $\xi=(q_y,-q_x,1)$. Passive-joint rearrangement $\xi_{11}\dot\theta=[-\xi_{12}'\ \xi_{21}'\ \xi_{22}']\dot\alpha$ (3.71); singular when the three passive joints are collinear; *uncertainty configuration* (Fig. 3.20b) has two independent admissible velocities.
- **Stewart platform (§5.4):** spherical joint $g(R)=\begin{bmatrix}R&(I-R)q\\0&1\end{bmatrix}$, velocity spanned by $(-e_i\times q,e_i)$ (never singular as a passive joint); structure eq. (3.72); **IK: $\theta_i=\|q_{s_i}-g_{st}q_{t_i}\|$**; Jacobians (3.73) nonsingular for $\theta_i\ne0$; **actuator singularity** when platform and base are coplanar (leg forces all in-plane) or two legs collinear. Ex 3.18 planar Stewart: three leg force lines concurrent ⇒ cannot generate a torque about that point.

### §3.6–3.8 Summary / bibliography / exercises (PDF 161–172, book 143–154)
Summary (p. 143–144) is a perfect one-page R3/R5/R6 crib (PoE, workspaces, SP1–3, $J^s$, $J^b$, $\tau=J^TF$, three singularity types, redundancy, structure equation). Bibliography: PoE due to Brockett; ≤16 IK solutions (Primrose; Lee–Liang constructive; Manseur–Doty example with 16).

**Exercises worth doing (book pp. 146–153):**
- **Ex 3** (four 3-DOF arms, Fig. 3.23: FK, IK via Paden–Kahan, $J^s$, $J^b$) — R3/R5/R6 bread-and-butter; mirrors RDKDC HW.
- **Ex 4** (elbow, inverse elbow, Stanford, Rhino: FK, IK, Jacobians, singularities, workspaces, # IK solutions) — the most exam-like problem in the book; do (i) the elbow arm in full as UR5 rehearsal.
- **Ex 5 Subproblem 2′** (non-intersecting axes), **Ex 6 Subproblem 4**, **Ex 7 Subproblem 5** — extend the IK toolbox; SP2′ is needed for UR5-type offsets.
- Ex 8 (spatial velocity independent of tool frame) — R2 insight.
- **Ex 9–12** (SVD, $\mu_3=\prod\sigma_i$, image of the unit ball = ellipsoid, task-ellipsoid manipulability) — F4/R5 manipulability ellipsoid.
- **Ex 13** (isotropic points of a 2-link arm: condition number 1) — R5 classic.
- **Ex 14** (Euler-angle singularities via "Jacobian" of $e^{\hat z\alpha}e^{\hat y\beta}e^{\hat z\gamma}$) — R1/R5 link; shows representation singularities.
- Ex 15–17 (more singularity types: 4 coplanar revolutes, 6 axes meeting a line, prismatic ⟂ two parallel coplanar revolutes), **Ex 19** (singular ⇒ ∃ wrench needing no torque; it is reciprocal to all columns) — R5.
- Ex 20–22 (slider-crank, variable-geometry truss, Stewart/U-joint Gruebler) — parallel mechanisms, optional.

**Conventions / pitfalls (Ch. 3):**
1. PoE twists are written **in the base frame at θ = 0**; the body Jacobian columns are in the **current tool frame**. Do not mix.
2. Spatial Jacobian's linear part is NOT $\dot p$: $\dot p=v^s+\omega^s\times p$. The "geometric Jacobian" of Spong/Craig ($[\dot p;\omega^s]$) equals $\mathrm{blkdiag}(R,R)J^b$ (hybrid velocity, Ex 2.18).
3. In Subproblem 2 the **second** exponential acts on $p$ first; $c$ is the intermediate point, and you solve $e^{\hat\xi_2\theta_2}p=c$ and $e^{-\hat\xi_1\theta_1}q=c$.
4. Subproblem 3's δ must be projected: $\delta'^2=\delta^2-(\omega^T(p-q))^2$ (easy to forget).
5. **Erratum, Eq. (3.41), p. 107 (PDF 125):** printed as $e^{\hat\xi_3\theta_3}=e^{-\hat\xi_2\theta_2}e^{-\hat\xi_1\theta_1}g_dg_{st}^{-1}(\theta)e^{-\hat\xi_4\theta_4}$; it should be $g_{st}^{-1}(0)$ (consistent with (3.39)).
6. Minor typos in §3.4 general IK: Eq. (3.45) EQ5 "$-c_3r_3$" should read $-c_3r_2$ and EQ6 "$\mu_3$" should read $\mu_2$ (by symmetry with EQ2/EQ3); Step 8 refers to "Example 3.4" but means Example 3.7. Summary item 7 says "admissible forces" where §5.2 defines rank via admissible *velocities*.
7. Ex 3.14 says "the corresponding eigenvector"; it means the singular vector (right singular vector for joint direction, left for twist direction).
8. Exercises 15–17 write revolute twists as $(q_i\times\omega_i,\omega_i)$ — identical to $(-\omega_i\times q_i,\omega_i)$.

