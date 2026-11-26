# Lynch & Park, *Modern Robotics: Mechanics, Planning, and Control* (May 2017 preprint) — Part 1 (Ch. 1–5)
Source: `5a2ab312-MR.pdf`, PDF pages 1–215 (front matter i–xviii; book pp. 1–197). For completeness of Ch. 5 the reader also skimmed PDF 216–219 (book 198–201: rest of §5.4 manipulability + §5.5 summary); exercises of Ch. 5 (book 202–218) belong to the next reader.

**Page offset (main text): PDF page = book page + 18.** (Book p. 1 = PDF 19.)

## Chapter → PDF page map (whole book)
| Part | Book pp. | PDF pp. | Lessons |
|---|---|---|---|
| Front matter: Forewords (Brockett, Mason), Preface | ix–xvi | 11–18 | — |
| Ch 1 Preview | 1–10 | 19–28 | overview |
| Ch 2 Configuration Space (dof, Grübler, topology, holonomic/nonholonomic, task space/workspace) | 11–58 | 29–76 | A3, R3 |
| Ch 3 Rigid-Body Motions (SO(3), so(3), exp/log, SE(3), twists, screws, wrenches) | 59–136 | 77–154 | F3, F7, R1, R2, A1, C1 |
| Ch 4 Forward Kinematics (PoE space/body, URDF) | 137–170 | 155–188 | R3, R4 |
| Ch 5 Velocity Kinematics & Statics (J_s, J_b, statics, singularities, manipulability) | 171–218 | 189–236 | F5, R5, A1 |
| Ch 6 Inverse Kinematics (PUMA, Stanford analytic; Newton–Raphson; inverse velocity) | 219–244 | 237–262 | R6, A1 |
| Ch 7 Kinematics of Closed Chains | 245–270 | 263–288 | (R4 ext.) |
| Ch 8 Dynamics of Open Chains (Lagrange, Newton–Euler, actuation) | 271–324 | 289–342 | R7 |
| Ch 9 Trajectory Generation | 325–352 | 343–370 | R8, A3 |
| Ch 10 Motion Planning (C-obstacles, graph search, grids, RRT/PRM, potential fields) | 353–402 | 371–420 | A3, A4 |
| Ch 11 Robot Control (PD, computed torque, force, hybrid, impedance) | 403–460 | 421–478 | R8 |
| Ch 12 Grasping and Manipulation | 461–512 | 479–530 | — |
| Ch 13 Wheeled Mobile Robots (incl. odometry, mobile manipulation) | 513–564 | 531–582 | A5/A6 context |
| App A Summary of Useful Formulas | 565–574 | 583–592 | R1–R5 cheat sheet |
| App B Other Rotation Representations (Euler ZYX, RPY, quaternions, Cayley–Rodrigues) | 575–584 | 593–602 | F3, R1 |
| App C Denavit–Hartenberg (+ D–H ↔ PoE) | 585–596 | 603–614 | R4 |
| App D Optimization and Lagrange Multipliers | 597–598 | 615–616 | F6 |
| Bibliography / Index | 599– / 617– | 617– / 635–642 | — |

---

## Front matter (PDF pp. 11–18, book ix–xvi) — Lessons: context
**Big idea:** Brockett's foreword frames the whole book: a kinematic chain is naturally written with "exponential coordinates of the second kind", $X = e^{A_1x_1}e^{A_2x_2}\cdots$ — this *is* the product-of-exponentials (PoE) formula of Ch. 4 — and the identity $Pe^{A}P^{-1}=e^{PAP^{-1}}$ is the workhorse behind the adjoint map and the space/body PoE equivalence. The preface says the book is "modern screw theory" via Lie groups (Brockett 1983), aimed at undergrads, and explicitly points to Murray–Li–Sastry (MLS, ref [122]) as the more advanced graduate treatment — i.e., MR is the friendlier twin of the RDKDC text.
**Notes:** Ch. 2–6 are the "minimum essentials" to be covered in order. Software (MATLAB/Python/Mathematica "MR library") at modernrobotics.org; reading the code is encouraged.

## Ch. 1 Preview (PDF pp. 19–28, book 1–10) — Lessons: overview of R1–R8, A3–A4
**Big idea:** A robot = rigid links + joints + actuators + sensors. The book's spine: C-space (Ch 2) → rigid-body motion representation (Ch 3) → FK via PoE (Ch 4) → Jacobian/statics/singularities (Ch 5) → IK (Ch 6) → closed chains (7) → dynamics (8) → trajectories (9) → planning (10) → control (11) → grasping (12) → mobile robots (13).
**Key points:** MR will use *implicit* representations (rotation matrices, 9 numbers/6 constraints) rather than minimal coordinates; exponential coordinates $\hat\omega\theta$ with $\theta\in[0,\pi]$; twists = "spatial velocities", wrenches = "spatial forces"; PoE eliminates link frames (only base {s} and end-effector {b} needed), D–H relegated to App. C. Jacobian maps joint rates to end-effector *twist*; singularities = rank loss; manipulability ellipsoid. Ch 11 key principle: you cannot independently control both motion and force in the same direction.

## Ch. 2 Configuration Space (PDF pp. 29–76, book 11–58) — Lessons: A3 (C-space), R3/R4 (dof counting), F7 (constraints)
**Big idea:** "Where is the robot?" is answered by its configuration — a point in C-space, whose dimension is the dof. Count dof as freedoms minus independent constraints (Grübler). But dimension is not everything: the *topology* (shape) of C-space — $\mathbb R^n$ vs $S^1$ vs $T^n$ vs $S^2$ — determines whether a global singularity-free minimal parametrization can even exist, which is why MR (and MLS) use implicit representations like rotation matrices. Velocity constraints split into holonomic (integrable; shrink C-space) and nonholonomic (non-integrable; shrink only the feasible velocities).

**Key definitions:**
- Def 2.1: configuration = position of every point of the robot; dof = min number of real coordinates; C-space = n-dim space of all configurations.
- Rigid body: planar 3 dof, spatial 6 dof (argument via 3 non-collinear points A,B,C: 3+2+1).
- Joints (Table 2.1, p.17/PDF 35): R, P, H: f=1 (spatial c=5; planar R,P c=2); C, U: f=2 (c=4); S: f=3 (c=3). Every joint connects exactly two links.
- Open chain (serial) vs closed chain (any loop; ground counts as a link).
- Topology: two spaces equivalent if continuously deformable without cutting/gluing. $S^1$, $\mathbb R^1$ (=E^1), closed interval $[a,b]$ are distinct; open interval ≅ line. $S^1\times\cdots\times S^1 = T^n \neq S^n$.
- Explicit parametrization (n numbers, has singularities if topology ≠ $\mathbb R^n$, e.g. latitude/longitude at the poles), atlas of charts, implicit representation (embed in higher-dim Euclidean space + constraints; singularity-free).
- Holonomic constraints $g(\theta)=0$, $g:\mathbb R^n\to\mathbb R^k$ (loop-closure equations) → C-space is an $(n-k)$-dim surface. Pfaffian constraints $A(\theta)\dot\theta=0$. Nonholonomic = Pfaffian not integrable.
- Task space (driven by the task, e.g. $\mathbb R^2$ for plotting, SE(3) default) vs workspace (configurations the end-effector can reach; driven by robot structure).

**Key results & equations:**
- Eq (2.1)–(2.3): dof = (sum of freedoms of bodies) − (number of independent constraints).
- **Prop 2.2 / Eq (2.4) Grübler:** $\text{dof} = m(N-1) - \sum_{i=1}^J c_i = m(N-1-J) + \sum_{i=1}^J f_i$, m=3 planar, m=6 spatial, N links *including ground*, J joints. Only a **lower bound** if joint constraints are not independent.
- Examples: four-bar 3(4−1−4)+4 = 1; slider-crank 1; kR planar chain k; five-bar 2; Stephenson/Watt six-bar 1; overlapping joints (Ex 2.5) = 3; parallelogram (Ex 2.6) Grübler gives 0 but true is 1 (redundant constraints); Delta robot (Ex 2.7) 6(17−1−21)+9+36 = 15 (only 3 visible at end-effector; 12 are link torsions); Stewart–Gough 6×UPS (Ex 2.8) 6(14−1−18)+6+12+18 = 6 (with SPS legs: 12, extra leg spins).
- C-space topologies: planar rigid body $\mathbb R^2\times S^1$; PR arm $\mathbb R^1\times S^1$; 2R arm $T^2$; mobile base + 2R arm $\mathbb R^2\times T^3$; spatial rigid body $\mathbb R^3\times S^2\times S^1$ (MR's informal description; the rigorous answer is $\mathbb R^3\times SO(3)$ — see pitfalls).
- Eq (2.5)–(2.8): differentiating $g(\theta(t))=0$ gives $\frac{\partial g}{\partial\theta}(\theta)\dot\theta = 0$ — a Pfaffian constraint with $A=\partial g/\partial\theta$ (this is exactly the "Jacobian of the constraint" from F5).
- Rolling coin (Fig 2.11, Eq 2.9–2.10): $q=(x,y,\phi,\theta)\in\mathbb R^2\times T^2$, $\begin{bmatrix}1&0&0&-r\cos q_3\\0&1&0&-r\sin q_3\end{bmatrix}\dot q = 0$; non-integrable (proof by trying to construct $g_1$) → nonholonomic; coin still reaches all of its 4-D C-space.
- Workspaces (Fig 2.12): 2R equal links 3 and 3R equal links 2 have the same workspace (disk) but different C-spaces; planar 2R vs spherical 2R have the same C-space ($T^2$) but different workspaces (disk vs sphere surface). 3R wrist: workspace = orientations, C-space $T^3$. SCARA (Ex 2.9) task space $\mathbb R^3\times S^1$; spray painting (Ex 2.10) $\mathbb R^3\times S^2$.

**Worked examples worth redoing:** Ex 2.3–2.8 (pp. 18–23 / PDF 36–41) — Grübler on four-bar, five-bar, six-bars, Delta, Stewart; Ex 2.6 is the canonical "Grübler is only a lower bound" case. Rolling-coin non-integrability argument (p. 32 / PDF 50).

**Exercises worth doing (PDF 56–76):** 2.1 (dof of a rigid body in n-D: $n + n(n-1)/2$); 2.2–2.4 (human arm, closed chain when both hands grip the wheel); 2.7 (n SRS arms holding an object); 2.8 (leg dof needed for a 6-dof parallel platform); 2.13 (6×SS); 2.14 (3×UPU — Grübler says 3, physical model moves more: classic redundancy trap); 2.17 (surgical parallel robot with remote-center point A — directly ASBR/CIS-relevant RCM concept); 2.25 (four-bar C-space plot); 2.26 (2R workspace and free C-space with barriers → A3 C-obstacles); 2.29 (topologies); 2.31–2.32 (diff-drive Pfaffian constraints, holonomic vs nonholonomic test).

**Conventions / pitfalls:**
- N counts ground; a point where 3 links meet is **two** joints (Ex 2.5).
- Grübler fails for parallelograms, spherical/planar sub-mechanisms (e.g., spherical four-bar Ex 2.16 needs m=3), and special geometries → lower bound only.
- MR's convention: dof = dimension of C-space, *not* dimension of feasible velocities (footnote 7) — so a nonholonomic rolling coin has 4 dof, not 2.
- "$\mathbb R^3\times S^2\times S^1$" for a rigid body is a counting description; SO(3) is not globally $S^2\times S^1$ (it is $\mathbb{RP}^3$). MR footnote 4 uses this to explain why 3-parameter Euler/RPY angles have singularities. Do not quote $S^2\times S^1$ as the topology of SO(3) in an exam.
- Unit quaternions double-cover SO(3) (footnote 5).
- Vectors are column vectors in computations; written as tuples $(1,2,3)$ otherwise.

## Ch. 3 Rigid-Body Motions (PDF pp. 77–154, book 59–136) — Lessons: F3, F7, R1, R2, A1, A2, C1
**Big idea:** Represent a rigid body's configuration *implicitly* as a matrix $T=(R,p)\in SE(3)$ (16 numbers, 10 constraints) because (i) it has no singularities and (ii) the same matrix does three jobs: represent a configuration, change the frame of a vector/frame, and displace a vector/frame. Velocities live in a flat vector space: a twist $\mathcal V=(\omega,v)\in\mathbb R^6$. Integrating a constant twist (a screw motion) for unit time reaches any configuration (Chasles–Mozzi), so $T=e^{[\mathcal S]\theta}$ — the exponential coordinates $\mathcal S\theta\in\mathbb R^6$. Every SE(3) concept is the 6-D analogue of an SO(3) one: $T\leftrightarrow R$, $\mathcal S\leftrightarrow\hat\omega$, $\mathcal V=\mathcal S\dot\theta\leftrightarrow\omega=\hat\omega\dot\theta$, $\mathcal S\theta\leftrightarrow\hat\omega\theta$. Forces pair with velocities into wrenches $\mathcal F=(m,f)$, and power invariance $\mathcal V^T\mathcal F$ gives the wrench transformation for free.

### 3.0–3.1 Vectors, frames, planar preview (PDF 77–86, book 59–68)
- Free vector **v** (coordinate-free) vs its representation $v\in\mathbb R^n$ in a chosen frame. Points likewise.
- **"All frames in this book are stationary, inertial frames"** (boxed, p. 61/PDF 79): the body frame {b} is the *stationary* frame instantaneously coincident with the moving body frame. All frames right-handed.
- Planar: $(P,p)$ composes as $R=PQ$, $r=Pq+p$ (Eq 3.8–3.9) = change of frame; the same formulas with (P,p) and (R,r) in the same frame = displacement (Eq 3.10–3.11). Three uses: (a) represent configuration, (b) change reference frame, (c) displace.
- Planar screw: any planar displacement = rotation about a fixed point s (translation ⇒ s at infinity). Example Fig 3.5: $\mathcal S=(\omega,v_x,v_y)=(1,2,0)$ and exponential coordinates $\mathcal S\theta=(\pi/2,\pi,0)$. Twist $\mathcal V=\mathcal S\dot\theta$.

### 3.2 Rotations and angular velocities (PDF 86–107, book 68–89)
**Key definitions:**
- $R=[\hat x_b\ \hat y_b\ \hat z_b]$ (columns = body axes in {s}), Eq (3.16). Six constraints $R^TR=I$ (3.17–3.19) + $\det R=+1$ (3.21; right-handed).
- Def 3.1 **SO(3)**; Def 3.2 SO(2) $=\{\begin{bmatrix}c&-s\\s&c\end{bmatrix}\}$. Groups: closure, associativity, identity, inverse (matrix Lie groups, footnote 2).
- Def 3.7: $[x]=\begin{bmatrix}0&-x_3&x_2\\x_3&0&-x_1\\-x_2&x_1&0\end{bmatrix}$, $[x]=-[x]^T$; the set is **so(3)** (Lie algebra = all $\dot R$ at $R=I$, footnote 3).
**Key results & equations:**
- Prop 3.3 $R^{-1}=R^T$; Prop 3.4 closure; Prop 3.5 associative, not commutative (SO(2) commutes); Prop 3.6 $\|Rx\|=\|x\|$.
- Subscript cancellation: $R_{ab}R_{bc}=R_{ac}$ (3.22), $R_{ab}p_b=p_a$, $R_{de}=R_{ed}^{-1}=R_{ed}^T$.
- Rot(x̂,θ), Rot(ŷ,θ), Rot(ẑ,θ) explicit (p. 74/PDF 92) and general Rot($\hat\omega$,θ) (= Eq 3.52). $\mathrm{Rot}(\hat\omega,\theta)=\mathrm{Rot}(-\hat\omega,-\theta)$.
- **Eq (3.23)–(3.24): pre-multiply = rotate about a fixed-frame axis: $R_{sb'}=RR_{sb}$; post-multiply = rotate about a body-frame axis: $R_{sb''}=R_{sb}R$.** Rotating a vector: $v'=Rv$ (axis interpreted in the vector's frame).
- Angular velocity $\mathrm w=\hat{\mathrm w}\dot\theta$ (3.25); $\dot{\hat x}=\mathrm w\times\hat x$ etc. (3.26–3.28); $\dot R=[\omega_s]R$ (3.29, 3.33).
- **Prop 3.8: $R[\omega]R^T=[R\omega]$** (3.31).
- **Prop 3.9: $\dot RR^{-1}=[\omega_s]$ (3.37), $R^{-1}\dot R=[\omega_b]$ (3.38)**; $\omega_b=R^T\omega_s$ (3.35); generally $\omega_c=R_{cd}\omega_d$. $\omega_s$ is independent of the choice of {b}; $\omega_b$ independent of {s}. $\omega_b$ is *not* "relative to a moving frame".
- Linear ODE review (§3.2.3.1, PDF 98–100): $\dot x=Ax\Rightarrow x(t)=e^{At}x_0$, series (3.42); **Prop 3.10**: (a) $\frac{d}{dt}e^{At}=Ae^{At}=e^{At}A$; (b) $A=PDP^{-1}\Rightarrow e^{At}=Pe^{Dt}P^{-1}$; (c) $AB=BA\Rightarrow e^Ae^B=e^{A+B}$; (d) $(e^A)^{-1}=e^{-A}$. → F7.
- Derivation of Rodrigues: $\dot p=\hat\omega\times p=[\hat\omega]p$ ⇒ $p(\theta)=e^{[\hat\omega]\theta}p(0)$; use $[\hat\omega]^3=-[\hat\omega]$ and sin/cos series.
- **Prop 3.11 / Eq (3.51) Rodrigues: $\mathrm{Rot}(\hat\omega,\theta)=e^{[\hat\omega]\theta}=I+\sin\theta[\hat\omega]+(1-\cos\theta)[\hat\omega]^2$.** $e^{[\hat\omega]\theta}R$ = rotate about fixed-frame axis; $Re^{[\hat\omega]\theta}$ = about body axis.
- Trace identity $\operatorname{tr}R=1+2\cos\theta$ (3.54); $R-R^T=2\sin\theta[\hat\omega]$ (3.53).
**Algorithm (Matrix log on SO(3), p. 87–88 / PDF 105–106):** given $R$, return $\theta\in[0,\pi]$, $\hat\omega$:
```
(a) if R == I:            theta = 0, omega_hat undefined
(b) elif tr R == -1:      theta = pi; omega_hat = any feasible of
        (1/sqrt(2(1+r33))) * (r13, r23, 1+r33)      (3.58)
        (1/sqrt(2(1+r22))) * (r12, 1+r22, r32)      (3.59)
        (1/sqrt(2(1+r11))) * (1+r11, r21, r31)      (3.60)
      (-omega_hat is also a solution)
(c) else:                 theta = arccos((tr R - 1)/2) in (0,pi)
                          [omega_hat] = (R - R^T) / (2 sin theta)   (3.61)
```
At θ=π, $R=I+2[\hat\omega]^2$ is symmetric (3.55); $\hat\omega_i=\pm\sqrt{(r_{ii}+1)/2}$ (3.56) with off-diagonal sign fixing (3.57). Geometric picture: SO(3) = solid ball of radius π with antipodal surface points identified (Fig 3.13); unique log inside the ball.
**Worked examples:** Ex 3.12 (p. 84/PDF 102): $\hat\omega_1=(0,0.866,0.5)$, θ=30° → R numeric; exp coords (0, 0.453, 0.262). Figure 3.7 frames {a},{b},{c} (p. 72/PDF 90) for subscript cancellation drills. Fig 3.9: Rot(ẑ,90°) applied in fixed vs body frame.

### 3.3 Rigid-body motions and twists (PDF 107–126, book 89–108)
**Key definitions:**
- Def 3.13 **SE(3)**: $T=\begin{bmatrix}R&p\\0&1\end{bmatrix}$; Def 3.14 SE(2). Homogeneous coordinates $[x^T\ 1]^T$; "$Tx$" means $Rx+p$ (3.65).
- Body twist $\mathcal V_b=(\omega_b,v_b)$ (3.70), matrix form $[\mathcal V_b]=T^{-1}\dot T=\begin{bmatrix}[\omega_b]&v_b\\0&0\end{bmatrix}\in se(3)$ (3.69, 3.71). $v_b=R^T\dot p$ = velocity of the {b} origin expressed in {b}.
- Spatial twist $\mathcal V_s=(\omega_s,v_s)$, $[\mathcal V_s]=\dot TT^{-1}$ (3.72, 3.74), **$v_s=\dot p-\omega_s\times p$ (3.73) = velocity of the (imaginary, infinitely large) body point currently at the {s} origin, expressed in {s}** — *not* $\dot p$.
- **Def 3.20 adjoint:** $[\mathrm{Ad}_T]=\begin{bmatrix}R&0\\ [p]R&R\end{bmatrix}\in\mathbb R^{6\times6}$; $\mathcal V'=[\mathrm{Ad}_T]\mathcal V$ ⇔ $[\mathcal V']=T[\mathcal V]T^{-1}$.
- Screw axis {q, ŝ, h}: pitch h = linear/angular speed. Twist about it: $\mathcal V=(\hat s\dot\theta,\ -\hat s\dot\theta\times q+h\hat s\dot\theta)$ (p. 103/PDF 121). From a general $\mathcal V$ with $\omega\neq0$: $\hat s=\omega/\|\omega\|$, $\dot\theta=\|\omega\|$, $h=\hat\omega^Tv/\dot\theta$, q chosen to account for the part of v ⊥ ŝ.
- **Def 3.24 (normalized) screw axis** $\mathcal S=(\omega,v)$ with either (i) $\|\omega\|=1$, $v=-\omega\times q+h\omega$, or (ii) $\omega=0$, $\|v\|=1$ (pure translation, infinite pitch). $\mathcal V=\mathcal S\dot\theta$. Change of frame $\mathcal S_a=[\mathrm{Ad}_{T_{ab}}]\mathcal S_b$.
**Key results & equations:**
- Prop 3.15 $T^{-1}=\begin{bmatrix}R^T&-R^Tp\\0&1\end{bmatrix}$ (3.64); Props 3.16–3.17 group, non-commutative; Prop 3.18 isometry (preserves distances and angles).
- Subscript cancellation $T_{ab}T_{bc}=T_{ac}$, $T_{ab}v_b=v_a$, $T_{de}=T_{ed}^{-1}$.
- Rot($\hat\omega$,θ) as 4×4 and Trans(p). **Eq (3.66) fixed-frame: $T_{sb'}=TT_{sb}=\mathrm{Trans}(p)\mathrm{Rot}(\hat\omega,\theta)T_{sb}$** (rotate about $\hat\omega_s$ — moves the {b} origin — then translate by p in {s}); **Eq (3.67) body-frame: $T_{sb''}=T_{sb}T$** (translate p in {b}, then rotate about the new body axis). Numerical check Fig 3.15.
- $[\mathcal V_b]=T^{-1}[\mathcal V_s]T$ (3.75), $[\mathcal V_s]=T[\mathcal V_b]T^{-1}$ (3.76).
- **Prop 3.21:** $[\mathrm{Ad}_{T_1}][\mathrm{Ad}_{T_2}]=[\mathrm{Ad}_{T_1T_2}]$ (3.77), $[\mathrm{Ad}_T]^{-1}=[\mathrm{Ad}_{T^{-1}}]$ (3.78).
- **Prop 3.22:** $\mathcal V_s=[\mathrm{Ad}_{T_{sb}}]\mathcal V_b$ (3.83); $\mathcal V_b=[\mathrm{Ad}_{T_{bs}}]\mathcal V_s=\begin{bmatrix}R^T&0\\-R^T[p]&R^T\end{bmatrix}\mathcal V_s$ (3.84); generally $\mathcal V_c=[\mathrm{Ad}_{T_{cd}}]\mathcal V_d$. $\mathcal V_s$ independent of {b}; $\mathcal V_b$ independent of {s}.
- Identities used: $[\omega]p=-[p]\omega$; $[\omega][x]=([x][\omega])^T$.
- **Prop 3.25 / Eq (3.86)–(3.88) exponential of a screw:** for $\|\omega\|=1$,
  $e^{[\mathcal S]\theta}=\begin{bmatrix}e^{[\omega]\theta}&G(\theta)v\\0&1\end{bmatrix}$, $G(\theta)=I\theta+(1-\cos\theta)[\omega]+(\theta-\sin\theta)[\omega]^2$ (3.87). For $\omega=0$, $\|v\|=1$: $e^{[\mathcal S]\theta}=\begin{bmatrix}I&v\theta\\0&1\end{bmatrix}$ (3.89).
- Chasles–Mozzi (constructive proof): every $T$ has a log $[\mathcal S]\theta\in se(3)$ (3.90).
**Algorithm (Matrix log on SE(3), p. 106 / PDF 124):** given $T=(R,p)$ find $\theta\in[0,\pi]$ (for rotation) and $\mathcal S$:
```
(a) if R == I:  omega = 0, v = p/||p||, theta = ||p||
(b) else: (omega, theta) = MatrixLog3(R);  v = G^{-1}(theta) p, where
    G^{-1}(theta) = (1/theta) I - (1/2)[omega] + (1/theta - (1/2) cot(theta/2)) [omega]^2   (3.91–3.92)
```
(Proof of 3.92 = Exercise 3.14.) Note: if R = I but p = 0 too, T = I and θ = 0.
**Worked examples worth redoing:**
- Ex 3.19 (p. 95–97/PDF 113–115): mobile manipulator + ceiling camera; solve $T_{ce}=(T_{ad}T_{db}T_{bc})^{-1}T_{ad}T_{de}$ — classic frame-chaining (CIS C1, ASBR A1). (Erratum in text: it says "$T_{bc}$ represents {b} relative to {c}" on p. 92 — by the book's own convention it is {c} relative to {b}.)
- Ex 3.23 (p. 101–102/PDF 119–120): car chassis rotating about point r: $v_s=r_s\times\omega_s=(-2,-4,0)$, $v_b=(2.8,4,0)$; verify $\mathcal V_s=[\mathrm{Ad}_{T_{sb}}]\mathcal V_b$.
- Ex 3.26 (p. 106–108/PDF 124–126): planar screw from $T_{sb}$ (30°) to $T_{sc}$ (60°): $T_{sc}T_{sb}^{-1}=e^{[\mathcal S]\theta}$, $\mathcal S=(0,0,1,3.37,-3.37,0)$, θ=π/6, axis through q=(3.37,3.37). Teaches the *space-frame* displacement $T_{sc}=e^{[\mathcal S]\theta}T_{sb}$.

### 3.4 Wrenches (PDF 126–129, book 108–111)
- Moment $m_a=r_a\times f_a$; **wrench $\mathcal F_a=(m_a,f_a)\in\mathbb R^6$ — moment first** (3.93). Wrenches add only when expressed in the same frame. Pure moment: f = 0.
- Power invariance $\mathcal V_b^T\mathcal F_b=\mathcal V_a^T\mathcal F_a$ (3.94) ⇒ **Prop 3.27: $\mathcal F_b=[\mathrm{Ad}_{T_{ab}}]^T\mathcal F_a$ (3.95/3.97), $\mathcal F_a=[\mathrm{Ad}_{T_{ba}}]^T\mathcal F_b$ (3.96/3.98).** Note the subscript pattern is the *transpose of the opposite* adjoint compared with twists ($\mathcal V_a=[\mathrm{Ad}_{T_{ab}}]\mathcal V_b$ but $\mathcal F_a=[\mathrm{Ad}_{T_{ba}}]^T\mathcal F_b$).
- Spatial wrench $\mathcal F_s$, body wrench $\mathcal F_b$.
- **Ex 3.28 (p. 110–111/PDF 128–129):** hand (0.5 kg) + apple (0.1 kg), g=10: $\mathcal F_f=[\mathrm{Ad}_{T_{hf}}]^T\mathcal F_h+[\mathrm{Ad}_{T_{af}}]^T\mathcal F_a=(0,0,-0.75\,\text{Nm},0,-6\,\text{N},0)$. Great F/T-sensor exercise.

### 3.5–3.7 Summary table, software, notes (PDF 129–133)
Summary table (p. 112–113/PDF 130–131) is the best one-page SO(3)/SE(3) cheat sheet in the book. Software (MR library): `RotInv, VecToso3, so3ToVec, AxisAng3, MatrixExp3, MatrixLog3, RpToTrans, TransToRp, TransInv, VecTose3, se3ToVec, Adjoint, ScrewToAxis, AxisAng, MatrixExp6, MatrixLog6`. Notes: exp coords also called Euler–Rodrigues parameters; Brockett [20] identified screw theory with the Lie group SE(3); derivatives of exp/log in [92,129,131,122(=MLS)].

### Exercises worth doing (PDF 134–154)
- **3.1 & 3.16** (full drill: draw frames, $R_{sa},R_{sb}$, inverses w/o inverse, pre- vs post-multiply interpretation, change coordinates, twist in another frame, log by hand, exp of given coords) — the single best R1/R2/F3 self-test.
- 3.5, 3.6, 3.7, 3.8 (logs incl. θ=π case and why naive $\sqrt{(r_{ii}+1)/2}$ signs are wrong).
- 3.10, 3.43 (project a matrix to SO(3) → F4 SVD/orthogonal Procrustes; C2 registration).
- 3.11 (when $e^Ae^B=e^{A+B}$; physical meaning for twists: commuting screws).
- **3.12(c) and 3.25 — $AR=RB$ / $AX=XB$ hand-eye calibration** (camera on arm, poses A, B; existence condition on $\alpha,\beta$: equal rotation angles; minimum k for uniqueness = 2 with non-parallel axes) → **ASBR A2**.
- 3.13 (eigenvalues of R: $\{1, \mu\pm i\nu\}$) → F4.
- 3.14 (prove $G^{-1}$), 3.15 (fixed- vs body-frame sequences), 3.17–3.23 (frame chaining word problems), 3.24 (screw joint of pitch 2), 3.26–3.30 (draw screw axes; cube screw motions), 3.31 (wrench in another frame), 3.32 (left-invariant distance), 3.33–3.35 (F7 linear ODEs and variation of constants), 3.36–3.41 (Euler ZXZ, 2-R wrist reachability, quaternions, Cayley), 3.42–3.49 (implement the library; SE(2) exp/log).

### Conventions / pitfalls (Ch. 3) — incl. MR vs MLS (RDKDC text)
- **Twist ordering:** MR $\mathcal V=(\omega,v)$ (angular on top). **MLS $\xi=(v,\omega)$** (linear on top). Every 6×6 matrix (Ad, Jacobian rows) is block-permuted between the books.
- **Adjoint:** MR $[\mathrm{Ad}_T]=\begin{bmatrix}R&0\\[p]R&R\end{bmatrix}$; **MLS $\mathrm{Ad}_g=\begin{bmatrix}R&\hat pR\\0&R\end{bmatrix}$** (for (v,ω)). Same operator, different ordering.
- **Wrench ordering:** MR $\mathcal F=(m,f)$; **MLS $F=(f,\tau)$**. Transformation rule same structure: MR $\mathcal F_a=[\mathrm{Ad}_{T_{ba}}]^T\mathcal F_b$; MLS $F_a=\mathrm{Ad}^T_{g_{ba}}F_b$.
- **Notation:** MR uses brackets $[\omega]$, $[\mathcal V]$, $[\mathcal S]$ and $T$; MLS uses hats $\hat\omega$, $\hat\xi$ and $g$. *Careful: in MR, a hat means "unit vector"* ($\hat\omega$ is a unit axis, not a skew matrix!). Mixing the two conventions is the #1 source of errors.
- **Revolute screw axis:** MR $\mathcal S=(\omega,-\omega\times q)=(\omega,q\times\omega)$; MLS $\xi=(-\omega\times q,\omega)$. Same numbers, swapped blocks. Prismatic: MR $(0,v)$, MLS $(v,0)$.
- **Terminology:** MR "screw axis" $\mathcal S$ = normalized twist (‖ω‖=1 or ω=0,‖v‖=1); "twist" $\mathcal V=\mathcal S\dot\theta$ = a velocity. MLS calls the se(3) element $\hat\xi$ a "twist" and $\xi\theta$ the exponential coordinates. MR's "spatial twist" $\mathcal V_s$ = MLS "spatial velocity" $V^s_{st}$; MR "body twist" $\mathcal V_b$ = MLS $V^b_{st}$. Definitions agree: $\dot TT^{-1}$ and $T^{-1}\dot T$.
- **SE(3) exponential formula:** MR writes the translation as $G(\theta)v$ (3.87); MLS writes $(I-e^{\hat\omega\theta})(\omega\times v)+\omega\omega^Tv\theta$. They are algebraically equal for ‖ω‖=1 — useful check.
- MR restricts the log's θ to [0, π]; at θ=π two antipodal solutions. The SE(3) log algorithm case (a) has $\theta=\|p\|$ (distance), not an angle.
- Interpretation of $v_s$: not the velocity of the {b} origin. Students routinely write $v_s=\dot p$ — wrong unless p=0.
- Pre-multiplication = fixed/space-frame operation; post-multiplication = body-frame operation — for both R and T.
- MR orders wrench and twist identically (rotational part first) so that power = $\mathcal V^T\mathcal F$ with matched slots ($\omega\cdot m+v\cdot f$).

## Ch. 4 Forward Kinematics (PDF pp. 155–188, book 137–170) — Lessons: R3, R4, (A1)
**Big idea:** Treat every joint as a screw motion applied to *all links outboard of it*. Put the robot at its zero (home) position, record the end-effector pose M and each joint's screw axis, and FK is just a product of matrix exponentials: $T(\theta)=e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_n]\theta_n}M$. No link frames, revolute and prismatic joints handled uniformly, and the screw axes are exactly the Jacobian columns at home (Ch. 5). The body form $T=Me^{[\mathcal B_1]\theta_1}\cdots e^{[\mathcal B_n]\theta_n}$ follows from $Me^{M^{-1}PM}=e^{P}M$.

**Key definitions:**
- FK = map $\theta\mapsto T_{sb}(\theta)$. Example 3R planar (Eq 4.1–4.3): $x=\sum L_i\cos(\cdot)$, $y=\sum L_i\sin(\cdot)$, $\phi=\theta_1+\theta_2+\theta_3$; or chain of link transforms $T_{04}=T_{01}T_{12}T_{23}T_{34}$ (4.4–4.5) — the D–H style.
- D–H: minimal (3n structural params + n joint values) but needs strict link-frame rules (App. C). PoE: 6n numbers for n screw axes (non-minimal) but no link frames needed; MR's preferred choice.
- Zero/home position: all θ=0, with positive direction of every joint specified. M = $T_{sb}$ at home.
- Revolute joint screw (space frame): $\omega_i$ = unit vector along positive axis, $v_i=-\omega_i\times q_i$ with $q_i$ any point on the axis (in {s}). Prismatic: $\omega_i=0$, $v_i$ = unit direction of positive translation. Helical (pitch h): $v_i=-\omega_i\times q_i+h\omega_i$.

**Key results & equations:**
- Planar 3R intro (p. 139–140/PDF 157–158): $\mathcal S_3=(0,0,1,0,-(L_1+L_2),0)$ by "turntable" visualization: spinning about joint 3 at 1 rad/s, the point at {0}'s origin moves at $L_1+L_2$ in −ŷ. Build up $T_{04}=e^{[\mathcal S_3]\theta_3}M$ (4.7), then $e^{[\mathcal S_2]\theta_2}e^{[\mathcal S_3]\theta_3}M$ (4.8), then all three (4.10).
- **Eq (4.14) space-form PoE:** $T(\theta)=e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_{n-1}]\theta_{n-1}}e^{[\mathcal S_n]\theta_n}M$. Needs (a) M, (b) $\mathcal S_1..\mathcal S_n$ in {s} at home, (c) θ.
- **Eq (4.16) body-form PoE:** $T(\theta)=Me^{[\mathcal B_1]\theta_1}\cdots e^{[\mathcal B_n]\theta_n}$, with $[\mathcal B_i]=M^{-1}[\mathcal S_i]M$, i.e. **$\mathcal B_i=[\mathrm{Ad}_{M^{-1}}]\mathcal S_i$** (screw axes in the end-effector frame at home). Derived via $e^{M^{-1}PM}=M^{-1}e^PM$ (from Prop 3.10(b)).
- Ordering intuition (p. 149–150/PDF 167–168): in space form, M is moved first by the most *distal* joint working inward; a proximal joint's $\mathcal S_i$ is unaffected by distal motion. In body form, M is moved first by joint 1 working outward; a distal $\mathcal B_i$ is unaffected by proximal motion. Hence both sets of axes need only be found at home.

**Worked examples worth redoing (screw-axis tables):**
- Ex 4.1 3R spatial (Fig 4.3, PDF 161–162): M=[[0,0,1,L1],[0,1,0,0],[−1,0,0,−L2]]; $\omega$=(0,0,1),(0,−1,0),(1,0,0); v=(0,0,0),(0,0,−L1),(0,L2,0). (Text's [S3] matrix vs table: v3=(0,−L2,0) in the narrative but table lists (0,L2,0); the matrix [S3] shows −L2 in row 2 → check: $v_3=-\omega_3\times q_3=-(1,0,0)\times(0,0,-L_2)=(0,-L_2,0)$. **The table entry "(0, L2, 0)" is a typo; correct is (0,−L2,0).**)
- Ex 4.2 3R planar: $\mathcal S_i$ as 3-vectors $(\omega_z,v_x,v_y)$: (1,0,0),(1,0,−L1),(1,0,−(L1+L2)); M ∈ SE(2).
- **Ex 4.3 6R (Fig 4.4) space form & Ex 4.6 same robot body form** (PDF 163–164, 168): M = Trans(0,3L,0); S: ω=(0,0,1),(0,1,0),(−1,0,0)×3,(0,1,0), v=(0,0,0)×3,(0,0,L),(0,0,2L),(0,0,0). B: v=(−3L,0,0),(0,0,0),(0,0,−3L),(0,0,−2L),(0,0,−L),(0,0,0). Compare to see $\mathcal B_i=[\mathrm{Ad}_{M^{-1}}]\mathcal S_i$.
- Ex 4.4 RRPRRR (Fig 4.5): prismatic joint 3 has ω=0, v=(0,1,0).
- **Ex 4.5 UR5 (Fig 4.6, PDF 165–166)** — directly R4: W1=109, W2=82, L1=425, L2=392, H1=89, H2=95 mm. M=[[−1,0,0,L1+L2],[0,0,1,W1+W2],[0,1,0,H1−H2]]. Space axes: ω=(0,0,1),(0,1,0),(0,1,0),(0,1,0),(0,0,−1),(0,1,0); v=(0,0,0),(−H1,0,0),(−H1,0,L1),(−H1,0,L1+L2),(−W1,L1+L2,0),(H2−H1,0,L1+L2). Check: θ2=−π/2, θ5=π/2 → T = [[0,−1,0,0.095],[1,0,0,0.109],[0,0,1,0.988]] (m). **Note MR's UR5 zero pose = arm stretched horizontally along x̂s**, not the D–H zero used by the UR controller/RDKDC `ur5FwdKin`.
- Ex 4.7 Barrett WAM 7R (Fig 4.8, PDF 169–170): body-frame axes $\mathcal B_i$; redundancy (1-D self-motion manifold); zero configuration is singular; check T at θ=(0,π/4,0,−π/4,0,−π/2,0).

### 4.2 URDF (PDF 170–176, book 152–158)
- XML tree: **joints** (parent, child, type ∈ {revolute, continuous, prismatic, fixed}, `origin` = xyz + rpy of child frame in parent frame at θ=0, `axis` = unit vector *in the child frame*), **links** (mass, CoM origin, 6 unique inertia entries). Only tree-structured robots (no Stewart platforms). RPY in URDF = roll about fixed x̂, then pitch about fixed ŷ, then yaw about fixed ẑ. Full UR5 URDF listing (offsets 0.089159, 0.13585, −0.1197/0.425, 0.39225, 0.093, 0.09465, 0.0823 m) → R4/A7 (ROS 2).
- Note URDF FK = link-frame chain (like D–H, Eq 4.4), but with arbitrary frame placement.

### 4.3–4.5 Summary / software / notes
- Software: `FKinBody(M,Blist,thetalist)`, `FKinSpace(M,Slist,thetalist)`.
- D–H summary: $T_{0,n+1}=T_{01}(\theta_1)\cdots T_{n-1,n}(\theta_n)T_{n,n+1}$, 4 params per link (minimal). Notes: PoE due to Brockett [20]; D–H conventions vary (some align joint axis with x̂) so frames + params must be given together; "no compelling reason to prefer D–H".

**Exercises worth doing (PDF 178–188):** 4.2 (SCARA: M, S, B, and check FKinSpace = FKinBody), 4.3–4.6 (convert given S↔B tables incl. **4.5 UR5 body axes** — matches RDKDC UR5 labs), 4.7–4.15 (more chains; 4.14 RPH and 4.15 HRR include a **helical joint**, pitch h), 4.16 (re-defining the zero position: $M'$, $A_i'$), 4.17–4.18 (snake robot and closed loop via PoE + inverses), 4.19 (PoE of the form $M_1e^{[A_1]\theta_1}M_2\cdots$ ↔ D–H; R4 bridge), 4.21 (find D–H params for given T).

**Conventions / pitfalls (Ch. 4), incl. MR vs MLS:**
- MR space PoE $T=e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_n]\theta_n}M$ ≡ **MLS $g_{st}(\theta)=e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_n\theta_n}g_{st}(0)$** — identical structure; only the twist stacking differs ((ω,v) vs (v,ω)) and $M\equiv g_{st}(0)$.
- MLS (RDKDC) mainly uses the space form; MR gives the **body form** $Me^{[\mathcal B_1]\theta_1}\cdots$ explicitly (MLS obtains it only implicitly via $\mathrm{Ad}_{g_{st}(0)}^{-1}$ in the body Jacobian).
- Common error: using $v=\omega\times q$ (sign flip). Correct: $v=-\omega\times q=q\times\omega$.
- q can be *any* point on the axis — choose one that zeros components.
- The screw axes must be taken at the zero configuration, all in the same frame.
- Joint order matters: space-form product is ordered 1…n left-to-right then M on the right; body form M on the left.
- UR5: MR's zero pose and axis directions differ from the D–H zero pose used in RDKDC; never mix tables from the two.

## Ch. 5 Velocity Kinematics and Statics (PDF pp. 189–219 read; chapter spans PDF 189–236, book 171–218) — Lessons: F5, R5, A1, (R6, R8)
**Big idea:** Column i of the Jacobian is simply *the screw axis of joint i at the current configuration*, i.e. the twist produced by $\dot\theta_i=1$. With PoE this falls out with no differentiation: in the space frame, joint i's home screw $\mathcal S_i$ is carried by the proximal joints 1…i−1 (an adjoint); in the body frame, $\mathcal B_i$ is seen through the distal joints i+1…n. The same matrix, transposed, maps end-effector wrenches to joint torques (power conservation). Rank loss = singularity (frame-independent), and $JJ^T$ gives the manipulability ellipsoid; its inverse gives the force ellipsoid — easy-motion directions are hard-force directions.

### Intro: 2R arm, ellipsoids, statics preview (PDF 189–195, book 171–177)
- Minimal coordinates: $x=f(\theta)\Rightarrow\dot x=\frac{\partial f}{\partial\theta}\dot\theta=J(\theta)\dot\theta$, $J\in\mathbb R^{m\times n}$.
- 2R planar (Eq 5.1): $J=\begin{bmatrix}-L_1s_1-L_2s_{12}&-L_2s_{12}\\L_1c_1+L_2c_{12}&L_2c_{12}\end{bmatrix}$; $v_{tip}=J_1\dot\theta_1+J_2\dot\theta_2$ (5.2); singular when $\theta_2=0$ or π (columns collinear). With $L_1=L_2=1$: $J(0,\pi/4)=\begin{bmatrix}-0.71&-0.71\\1.71&0.71\end{bmatrix}$, $J(0,3\pi/4)=\begin{bmatrix}-0.71&-0.71\\0.29&-0.71\end{bmatrix}$.
- Joint-velocity square → parallelogram of tip velocities (Fig 5.2); unit circle ("iso-effort") → **manipulability ellipse** (Fig 5.3); isotropy via $\ell_{max}/\ell_{min}$.
- Statics: $f_{tip}^Tv_{tip}=\tau^T\dot\theta\ \forall\dot\theta$ ⇒ **$\tau=J^T(\theta)f_{tip}$ (5.3)**; nonsingular square J ⇒ $f_{tip}=J^{-T}\tau$ (5.4) (virtual-work justification, footnote 2). Torque box → force polygon (Fig 5.4); **force ellipsoid** via $J^{-T}$ (Fig 5.5). Axes of force and manipulability ellipsoids are aligned with reciprocal semi-axis lengths (Fig 5.6). At a singularity the velocity ellipsoid collapses to a segment while the force ellipsoid becomes infinitely long along the lost direction (suitcase-with-straight-arm intuition).

### 5.1 Manipulator Jacobian (PDF 196–208, book 178–190)
**Key definitions:**
- Space Jacobian: $\mathcal V_s=J_s(\theta)\dot\theta$; body Jacobian $\mathcal V_b=J_b(\theta)\dot\theta$ — "geometric" Jacobians mapping to twists (not to $\dot x$).
- **Def 5.1 (space):** with $T=e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_n]\theta_n}M$ (5.9), $J_{s1}=\mathcal S_1$ and **$J_{si}(\theta)=[\mathrm{Ad}_{e^{[\mathcal S_1]\theta_1}\cdots e^{[\mathcal S_{i-1}]\theta_{i-1}}}]\mathcal S_i$ (5.11)**, i=2…n. Depends only on $\theta_1..\theta_{i-1}$.
- **Def 5.4 (body):** with $T=Me^{[\mathcal B_1]\theta_1}\cdots e^{[\mathcal B_n]\theta_n}$ (5.16), $J_{bn}=\mathcal B_n$ and **$J_{bi}(\theta)=[\mathrm{Ad}_{e^{-[\mathcal B_n]\theta_n}\cdots e^{-[\mathcal B_{i+1}]\theta_{i+1}}}]\mathcal B_i$ (5.18)**, i=n−1…1. Depends only on $\theta_{i+1}..\theta_n$.
**Key results & equations:**
- Derivation (PDF 196–197): $[\mathcal V_s]=\dot TT^{-1}=[\mathcal S_1]\dot\theta_1+e^{[\mathcal S_1]\theta_1}[\mathcal S_2]e^{-[\mathcal S_1]\theta_1}\dot\theta_2+\cdots$ → vector form (5.6)–(5.8). Body analogue (5.13)–(5.15) from $T^{-1}\dot T$. Uses $\frac{d}{dt}e^{A\theta}=Ae^{A\theta}\dot\theta$ and $(e^{A\theta})^{-1}=e^{-A\theta}$.
- Practical recipe: $J_{si}=(\omega_{si},\ -\omega_{si}\times q_{si})$ with the *current* axis direction and a *current* point on axis i (prismatic: $(0,\hat v)$). Same as finding $\mathcal S_i$ but at arbitrary θ.
- Visualization (§5.1.3, Fig 5.9): $J_{s3}=[\mathrm{Ad}_{T_{ss''}}]\mathcal S_3$ with $T_{ss''}=e^{[\mathcal S_1]\theta_1}e^{[\mathcal S_2]\theta_2}$; $J_{b3}=[\mathrm{Ad}_{T_{b''b}}]\mathcal B_3$, $T_{bb''}=e^{[\mathcal B_4]\theta_4}e^{[\mathcal B_5]\theta_5}$.
- **§5.1.4: $J_b(\theta)=[\mathrm{Ad}_{T_{bs}}]J_s(\theta)$ (5.22), $J_s(\theta)=[\mathrm{Ad}_{T_{sb}}]J_b(\theta)$ (5.23)**, $T_{sb}=T(\theta)$; hence equal rank.
- **§5.1.5 Analytic Jacobian** $\dot q=J_a(\theta)\dot\theta$ for minimal coordinates (e.g. Euler angles, exp coords). Footnote 3: the "hybrid" geometric Jacobian (position-rate + angular velocity) depends on both frames — this is the "geometric Jacobian" of Spong/Siciliano, *not* MR's.
- **Ex 5.5:** $q=(r,x)$ with $r=\hat\omega\theta$; $\dot x=R_{sb}J_v\dot\theta$; $\omega_b=A(r)\dot r$, $A(r)=I-\frac{1-\cos\|r\|}{\|r\|^2}[r]+\frac{\|r\|-\sin\|r\|}{\|r\|^3}[r]^2$ (derivation Ex 5.10); **$J_a=\begin{bmatrix}A^{-1}(r)&0\\0&R_{sb}\end{bmatrix}J_b$ (5.25)**. ($A(r)$ is the right Jacobian of SO(3); singular when ‖r‖ = 2π, never inside the radius-π ball.)
- §5.1.6 inverse velocity preview: n=6, full rank ⇒ $\dot\theta=J^{-1}\mathcal V$; n<6 can't reach arbitrary twists; n>6 redundant (n−6 internal motions, e.g. your elbow with palm fixed). Details in §6.3.
**Worked examples worth redoing:**
- **Ex 5.2 RRRP (Fig 5.7, PDF 198–199):** $J_s=\begin{bmatrix}0&0&0&0\\0&0&0&0\\1&1&1&0\\0&L_1s_1&L_1s_1+L_2s_{12}&0\\0&-L_1c_1&-L_1c_1-L_2c_{12}&0\\0&0&0&1\end{bmatrix}$.
- **Ex 5.3 RRPRRR / Stanford-type (Fig 5.8, PDF 199–201):** axis directions by composing Rot(ẑ,θ1)Rot(x̂,−θ2)…; wrist center $q_w$; $J_s=\begin{bmatrix}\omega_{s1}&\omega_{s2}&0&\omega_{s4}&\omega_{s5}&\omega_{s6}\\0&-\omega_{s2}\times q_2&v_{s3}&-\omega_{s4}\times q_w&-\omega_{s5}\times q_w&-\omega_{s6}\times q_w\end{bmatrix}$. "Obtained the entire Jacobian without differentiating the FK."

### 5.2 Statics of open chains (PDF 208–209, book 190–191)
- Power: joints = (motion) + end-effector; at equilibrium $\tau^T\dot\theta=\mathcal F_b^T\mathcal V_b$ ⇒ **$\tau=J_b^T(\theta)\mathcal F_b=J_s^T(\theta)\mathcal F_s$, or frame-free $\tau=J^T(\theta)\mathcal F$ (5.26).** Here $\mathcal F$ is the wrench the robot *applies*; an external wrench $-\mathcal F$ is resisted. Gravity torques must be added separately (footnote 5).
- $\mathcal F=J^{-T}\tau$ only if $J^T$ is 6×6 invertible. Redundant (n>6): even with end-effector "in concrete", τ may cause internal motion (needs dynamics). n<6, rank n: robot cannot actively produce wrenches in $\mathrm{Null}(J^T)$ (dimension 6−n) but resists any wrench there without moving (motorized door example).

### 5.3 Singularity analysis (PDF 209–214, book 191–196)
- **Kinematic singularity:** θ where $J(\theta)$ is not of maximal rank. Lost mobility directions come with the ability to resist wrenches in those directions.
- $\operatorname{rank}J_s=\operatorname{rank}J_b$ because $[\mathrm{Ad}_{T_{sb}}]$ is always invertible. Singularities independent of choice of fixed frame ($T'=PT\Rightarrow J_b'=J_b$) and end-effector frame ($T'=TQ\Rightarrow J_s'=J_s$) (Fig 5.10).
- Five classic 6-dof singular geometries (use $J_s$ with a clever {s}): **I** two collinear revolute axes ($J_{s1}=J_{s2}$); **II** three coplanar parallel revolute axes (e.g. elbow fully stretched); **III** four revolute axes through a common point (wrist center above shoulder; v-parts all zero with origin there); **IV** four coplanar revolute axes (only 3 nonzero rows); **V** six revolute axes intersecting a common line (z-row of v is 0).

### 5.4 Manipulability (PDF 214–217, book 196–199) and 5.5 Summary (PDF 218–219)
- For $\|\dot\theta\|=1$ and (assumed) invertible/full-rank J: $\dot q^T(JJ^T)^{-1}\dot q=1$ (5.27) — ellipsoid with $A=JJ^T$; principal axes = eigenvectors $v_i$ of A, semi-axes $\sqrt{\lambda_i}$ (Fig 5.13); volume ∝ $\sqrt{\lambda_1\cdots\lambda_m}=\sqrt{\det(JJ^T)}$. (Equivalently: semi-axes = singular values of J, axes = left singular vectors — F4 link.)
- Split $J=\begin{bmatrix}J_\omega\\J_v\end{bmatrix}$ (rotational rows on top in MR!) — separate angular ($A=J_\omega J_\omega^T$) and linear ($A=J_vJ_v^T$) ellipsoids because units differ. Prefer $J_b$ for the linear ellipsoid (velocity of the end-effector origin, not of the point at {s} origin).
- Scalar measures: **$\mu_1=\sqrt{\lambda_{max}/\lambda_{min}}\ge1$** (isotropy, ratio of semi-axes = condition number of J), **$\mu_2=\lambda_{max}/\lambda_{min}$** (condition number of $A=JJ^T$), **$\mu_3=\sqrt{\lambda_1\lambda_2\cdots}=\sqrt{\det A}$** (volume; larger is better; this is Yoshikawa's manipulability $w$). $\mu_1,\mu_2\to\infty$ at singularities.
- **Force ellipsoid:** $\|\tau\|=1$, $\tau=J^T\mathcal F$ ⇒ $\mathcal F^TJJ^T\mathcal F=1$, $B=(JJ^T)^{-1}=A^{-1}$: same axes, semi-axes $1/\sqrt{\lambda_i}$; $V_AV_B$ = constant, so increasing $\mu_3(A)$ decreases $\mu_3(B)$.
- Software: `JacobianBody(Blist,thetalist)`, `JacobianSpace(Slist,thetalist)`.
- Notes (p. 201): the Jacobian columns are simply the configuration-dependent screw axes — a key advantage of PoE.

**Exercises worth doing:** (Ch. 5 exercises are on PDF 220–236, outside this reader's range; see part 2.) From the text: redo the 2R Jacobians and ellipses at θ=(0,π/4),(0,3π/4); derive Ex 5.2 and 5.3 Jacobians yourself; verify $J_b=[\mathrm{Ad}_{T_{bs}}]J_s$ numerically with the MR library.

**Conventions / pitfalls (Ch. 5), incl. MR vs MLS:**
- MR $J_s$ = **MLS spatial manipulator Jacobian $J^s_{st}$** — same columns $\xi_i'=\mathrm{Ad}_{e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_{i-1}\theta_{i-1}}}\xi_i$, but rows ordered (v; ω) in MLS vs (ω; v) in MR. MR $J_b$ = **MLS body Jacobian $J^b_{st}$** whose columns MLS writes $\xi_i^\dagger=\mathrm{Ad}^{-1}_{e^{\hat\xi_i\theta_i}\cdots e^{\hat\xi_n\theta_n}g_{st}(0)}\xi_i$ using *space* twists; MR writes the same thing with body screws $\mathcal B_i$ — algebraically identical since $\mathcal B_i=[\mathrm{Ad}_{M^{-1}}]\mathcal S_i$.
- Statics in MLS: $\tau=(J^b_{st})^TF^b$ with $F=(f,\tau)$; in MR $\tau=J_b^T\mathcal F_b$ with $\mathcal F=(m,f)$ — consistent only if J rows and wrench slots use the same ordering. Mixing an MLS-ordered J with an MR-ordered wrench silently swaps forces and moments.
- **$J_s$ linear part is not the end-effector point velocity** ($v_s$ = velocity of the point at {s} origin). For resolved-rate control of the tool tip use $J_b$ (or rotate $J_b$'s linear rows by $R_{sb}$ — giving $\dot p$), not the bottom rows of $J_s$.
- "Geometric Jacobian" means different things: MR = space/body twist Jacobians; Spong/Siciliano/Craig = hybrid $(\dot p,\omega_s)$ Jacobian (MR footnote 3). The analytic Jacobian differs by $A(r)$ / Euler-rate maps and has *representation* singularities that are not kinematic singularities.
- MR's manipulability derivation writes $J^{-1}$, but the ellipsoid formula with $A=JJ^T$ holds for any full-row-rank (redundant) J.
- MLS derives $J^s_{st}$ from $\hat V^s=\sum_i(\partial g/\partial\theta_i\,g^{-1})\dot\theta_i$; MR's derivation (5.6) is the same computation written with adjoints.

