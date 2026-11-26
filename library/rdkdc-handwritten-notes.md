# RDKDC handwritten lecture notes (`robot-devices.pdf`, 105 pages), Fall 2017 (Cowan)
Source: `RobotDevicesKinematicsDynamicsAndControl/robot-devices.pdf`. All 105 pages were read as images (the text layer is unusable). Page numbers below are **PDF pages**.

## Whose notes, which term
- **A past student's notes from Prof. Noah Cowan's Fall 2017 offering.** They are not the lecturer's notes, and they are not the current student's.
- **Lecturer:** p. 73 says "Noah's fix on unit", so the lecturer is Noah J. Cowan. The notes cite MLS as "course text" (p. 2) and use MATLAB (p. 34, 74) and a Mathematica notebook (p. 64).
- **Term:** p. 53 says "Final 12/21". 21 Dec 2017 was a Thursday. The PDF metadata says it was created on 2017-12-24 on iOS 11.2.1, an iOS version released in December 2017.
- **Why a student wrote them:**
  - They contain student to-dos ("\*Look up gimbal lock", p. 21) and reading assignments ("Reading: Implicit Function Theorem", p. 33; "Read Principle of Least Action by Feynman", p. 95; "MIT Linear Alg G.S." = Strang, p. 71).
  - They refer to problem sets ("Pf: P.S. 1", p. 14; "In Prob Set 4 you solved for θ₁, θ₂", p. 80).
  - They contain about 25 copying slips that a lecturer would not write (see §B′).
- **Format:**
  - 99 pages from a tablet notebook.
  - 6 phone scans of paper pages: pp. 57–58 and 102–105.
  - p. 1 is a cover and p. 56 is blank.
- **Coverage:** the notes stop at Lyapunov stability definitions (p. 105). There is **no manipulator dynamics (M, C, N), no PD or computed-torque control, no DH lecture, no UR5 IK, no trajectory generation and no ROS**.

**Notation used in the notes:**
- MLS twist order $\xi=(v,\omega)$ throughout (p. 36: "ξ = (v, ω)").
- Hat and vee are overloaded for so(3) and se(3).
- Body-frame error $g_{t^*t}=g_{st^*}^{-1}g_{st}$ (p. 69).
- The final-project report reviewed in `rdkdc-final-project.md` uses the same conventions.

---

## A. Page-by-page walk, grouped by lecture
Lecture boundaries are inferred from "Recap" headers and topic restarts. **L#** numbers are my own numbering.

### L1 Math preliminaries (pp. 2–11). Lessons: F2–F4, F7
**Covered:**
- **Points vs vectors.** Points can be subtracted but not added. MLS writes points in Roman type ($p,q$) and vectors in bold.
- **Linear vector space (LVS):** the 8 axioms (p. 3).
- **Norms:**
  - the axioms;
  - the $\|\cdot\|_1$, $\|\cdot\|_2$, $\|\cdot\|_\infty$ and $\|\cdot\|_p$ norms;
  - a sketch of the unit balls (p. 4).
- **Inner product:** $\langle x,y\rangle=x^Ty$.
- **Coordinates:** a physical vector needs a basis $\{i,j,k\}$ to get coordinates; a point needs a frame $\{O,i,j,k\}$. $\sqrt{p_1^2+p_2^2+p_3^2}$ only means $\|p-O\|$.
- **Linear maps:** the theorem "$f$ is linear iff $f(\alpha x_1+\beta x_2)=\alpha f(x_1)+\beta f(x_2)$", proved in both directions (p. 6).
- **Linear independence, span, column space, basis, dimension** (pp. 7–10).

**Worked examples:**
- **Solution space of an ODE (pp. 8–9).** The solutions of $\ddot x+3\dot x+2x=0$ form an LVS with basis $\{e^{-t},e^{-2t}\}$. The notes show closure, linear independence, and the coordinates $a=[a_1;a_2]$ of a solution in that basis.
- **Matrix facts (p. 10):**
  - $\det M=0$ means singular;
  - skew-symmetric $M=-M^T$;
  - the cross product as $\hat x y$.
- **Eigenvalues of $\hat x$ (p. 11):** $\det(\lambda I-\hat x)=\lambda(\lambda^2+\|x\|^2)$, so $\lambda\in\{0,\pm j\|x\|\}$.

**MLS / MR:** MLS §2.1 intro (book pp. 19–22) for points and vectors. No MR equivalent; this is Foundations F2–F4 material (Strang).

### L1 (cont.) Rigid transformations and SO(n) (pp. 11–14). Lessons: R1
**Covered ("Follow MLS"):**
- **Definitions:** rigid body, rigid motion, rigid displacement. Induced map on vectors $g_*(v)=g(p_0+v)-g(p_0)$.
- **Def. of a rigid transformation:**
  1. it preserves length;
  2. it preserves cross products, $g_*(v\times w)=g_*v\times g_*w$, which makes it orientation-preserving.
- **Facts:**
  - $g_*$ is linear;
  - it preserves inner products (polarization identity $v\cdot w=\tfrac14(\|v+w\|^2-\|v-w\|^2)$).
- **Translations** $g(p)=p+v_0$.
- **$SO(n)=\{R:R^TR=I,\ \det R=+1\}$.** $R^TR=I$ preserves distance; $\det R=+1$ preserves orientation.
- **Proofs:**
  - $\|Rp-Rq\|=\|p-q\|$ is proved in the notes;
  - $R(v\times w)=(Rv)\times(Rw)$ is "left to P.S. 1".

**MLS / MR:** MLS §2.1–2.2.1, book pp. 20–24 (Def. 2.1, Lemma 2.1). MR §3.2.1.

### L1 (cont.) Course overview through a planar 2R arm (pp. 14–17). Lessons: R3, R5, R6 preview
**Covered:**
- **Course map:** (1) kinematics: forward, inverse, differential; (2) dynamics; (3) control of serial-link, fully actuated manipulators.
- **FK of the planar 2R arm:**
  - the kinematic map $f:T^2\to SE(2)$, or "slightly inaccurately" $f:\mathbb R^2\to\mathbb R^3$;
  - $f(\theta)=(l_1c_1+l_2c_{12},\ l_1s_1+l_2s_{12},\ \theta_1+\theta_2)$;
  - the workspace is an annulus with radii $|l_1-l_2|$ and $l_1+l_2$.
- **IK:** posed for a 3-D pose with only 2 joints, it is ill-posed (codomain dimension 3 > domain dimension 2). So the notes restrict to the position map $\bar f:\mathbb R^2\to\mathbb R^2$.
- **Differential kinematics:** by the chain rule, $\dot{\bar x}=J(\theta)\dot\theta$ with $J=\partial\bar f/\partial\theta$.
- p. 17 has blank "Dynamics: Lagrange equation" and "Controls" headings.

**MLS / MR:** MLS §3.3 eq. (3.16) (planar 2-link IK) and §3.4. MR §5.0 intro and §6.0 (planar 2R).

### L2 Recap, groups, non-commutativity, rotation representations (pp. 18–21). Lessons: R1, R6 preview
**Covered:**
- **Recap (p. 18):** FK $f:Q\to SE(n)$. Then **resolved-rate in lecture 2**: to get a desired velocity $V^*$, command $\dot\theta=J^{-1}(\theta)V^*$, which needs linearly independent columns.
- **Group axioms** (pp. 17, 19), with the "book rotated 90° about x, then about y" picture showing that rotations do not commute (pp. 19–20).
- **Rotation matrices:**
  - pros: composition by multiplication ($x\mapsto R_2R_1x$), $R^{-1}=R^T$;
  - cons: 9 numbers for 3 DOF.
- **Euler angles:** yaw–pitch–roll $R=R_z(\psi)R_y(\theta)R_x(\phi)$. Pros: 3 numbers. Cons: singularities.

**MLS / MR:** MLS §2.2.1, §2.2.3 (book pp. 22–24, 31–33). MR §3.2.1 and App. B.1–B.2.

### L3 Quaternions, group actions, semidirect product, homogeneous coordinates (pp. 21–26). Lessons: R1, R2
**Covered:**
- **Recap table (p. 21):**
  - SO(3): 9 numbers, no singularities, easy to compose;
  - Euler angles: 3 numbers, singularities;
  - unit quaternion: 4 numbers, "slightly redundant".
- **Unit quaternion:** $Q=(\cos\tfrac\theta2,\ \omega\sin\tfrac\theta2)$. $SO(3)\cong S^3/\{\pm\}$ (antipodal points identified, with a sketch).
- **Group action $f:G\times X\to X$:** identity action and associativity.
  - Example: $V^n$ acting on $\mathbb R^n$ by point + vector.
  - Non-example: $f(a,x)=a+x+1$ on $(\mathbb R,+)$ fails both axioms.
- **Direct product** $(g_1,h_1)(g_2,h_2)=(g_1g_2,h_1h_2)$. Applied to $SO(3)\times V^3$ it gives $(R_1R_2,v_1+v_2)$, which is wrong for rigid motions.
- **Worked example (p. 24):** "yaw 90°, move 1 m along x" in both orders. The true results are $[2,0,0]$ and $[1,1,0]$; the direct product predicts $[2,0,0]$ for both.
  - Roll→pitch→yaw about world axes is the same as yaw→pitch→roll in nested (body) frames.
  - $g_1(g_2(p))=R_1R_2p+(R_1v_2+v_1)$.
- **Semidirect product (p. 25):** $(R_1,v_1)(R_2,v_2)=(R_1R_2,\ R_1v_2+v_1)$, so $SE(3)=SO(3)\ltimes\mathbb R^3$. It works because SO(3) acts on $V^3$.
- **Homogeneous coordinates:** points $[p;1]$, vectors $[v;0]$. "Point + point" gives a last entry of 2, which is not allowed. $\bar g=[R\ v;0\ 1]$, with $\bar g\bar p=[Rp+v;1]$ and $\bar g\bar w=[Rw;0]$.

**MLS / MR:**
- MLS §2.2.3 (quaternions, pp. 33–34), §2.3.1 (homogeneous coordinates, pp. 34–39).
- MLS App. A §A.1–A.2 (groups, actions, book pp. 403–415).
- MR §3.3.1, App. B.3.

### L4 Recap, degrees of freedom by constraint rank, matrix exponential intro (pp. 26–33). Lessons: R1, R3 (dof), F5, F7
**Covered:**
- **Recap (p. 26):** LVS, Euclidean space, groups, group actions, semidirect product. The homogeneous map $\sigma:(R,v)\mapsto[R\ v;0\ 1]$ is a **homomorphism**. MATLAB indexing: `R=g(1:3,1:3)`, `v=g(1:3,4)`.
- **Axis–angle** $(\omega,\theta)$ with $\|\omega\|=1$, $\theta\in[0,2\pi)$.
- **Degrees of freedom (pp. 27–32):**
  - DOF is locally the dimension of the configuration space, i.e. the minimum number of parameters.
  - With $n$ parameters and $m$ independent constraints: if $\operatorname{rank}J=m$, there are $r=n-m$ DOF. In general $r=n-\operatorname{rank}(\partial f/\partial x)$.
- **Examples:**
  - $S^2$: $J=[2x_1\ 2x_2\ 2x_3]$, so 2 DOF.
  - Great circle $S^2\cap\{x^Tv=0\}$: $J=[2x^T;v^T]$ has rank 2, so 1 DOF.
  - Cylinder $\|(I-vv^T)x\|^2=\alpha^2$: 2 DOF. The notes also show $(I-vv^T)^2=I-vv^T$.
  - **Key subtlety (p. 32):** the line ($\alpha=0$) written as $\|(I-vv^T)x\|^2=0$ has zero gradient on the set, so the counting argument fails. Written as $(I-vv^T)x=0$ it has rank 2, so 1 DOF. "Rank is a robust property."
- **Matrix calculus:** $\partial(x^TPx)/\partial x=2x^TP$; $\partial f/\partial x$ is $q\times p$; $\nabla\varphi=(\partial\varphi/\partial x)^T$.
- **Matrix exponential (pp. 28–30):**
  - the series for $e^{At}$; $\frac{d}{dt}e^{At}=Ae^{At}=e^{At}A$; in general $e^{At}B\ne Be^{At}$.
  - Rotation about a fixed axis: $\dot p=\omega\times p=\hat\omega p$, so $p(t)=e^{\hat\omega t}p_0$.
  - Geometric picture of repeated cross products $\hat\omega^kq$, leading to Rodrigues.
- **Skew/symmetric split (p. 33):** $M=\tfrac12(M-M^T)+\tfrac12(M+M^T)$. Hat $\wedge:\mathbb R^3\to so(3)$ and vee $\vee$.

**MLS / MR:**
- MLS §2.2.2 (book pp. 26–31, Lemma 2.3, Rodrigues (2.14)).
- MR §2.1–2.3 (dof, implicit representations) and §3.2.3.
- The "rank of the constraint Jacobian" argument is not in the README or the library notes (see §C).

### L5 Two facts, exp/log on SO(3), twists and screws (pp. 34–39). Lessons: R1, R2
**Covered:**
- "Two facts proved": $R\hat vR^T=(Rv)^\wedge$ (p. 34).
- $a^b=e^{b\ln a}$; the matrix log inverts exp on the image of exp.
- **MATLAB demo:** `A=rand(3,3); eA=expm(A); Atest=logm(eA); A-Atest`.
- For $A=\hat\omega\theta$ with $\|\omega\|=1$: $e^{\hat\omega\theta}=I+\sin\theta\,\hat\omega+(1-\cos\theta)\hat\omega^2$ is always a rotation. $\exp:so(3)\to SO(3)$ is onto, and the log is not one-to-one.
- **Twists (pp. 35–36):**
  - a point rotating about the line $\{q+\alpha\omega\}$: $\dot p=\omega\times(p-q)$;
  - $\hat\xi=[\hat\omega\ v;0\ 0]$ with $v=-\omega\times q$;
  - $\dot{\bar p}=\hat\xi\bar p$, so $\bar p(t)=e^{\hat\xi t}\bar p_0$;
  - $se(3)$, twist coordinates $\xi=(v,\omega)$.
- **"Cool kid formula" (p. 36):** $e^{\hat\xi\theta}=I_4+\theta\hat\xi+(1-\cos\theta)\hat\xi^2+(\theta-\sin\theta)\hat\xi^3$ for $\|\omega\|=1$.
- **Twist exponential by cases (pp. 37–38):**
  - $\hat\xi^n=[\hat\omega^n\ \hat\omega^{n-1}v;0\ 0]$.
  - $\omega=0$: $[I\ v\theta;0\ 1]$.
  - $\|\omega\|=1$: the upper-right block is $d=[I\theta+(1-\cos\theta)\hat\omega+(\theta-\sin\theta)\hat\omega^2]v$. This is the MR form; MLS (2.36) writes it as $(I-e^{\hat\omega\theta})(\omega\times v)+\omega\omega^Tv\theta$.
- **Screw $\{l,h,M\}$ (axis, pitch, magnitude):**
  - a point moves to $q+e^{\hat\omega\theta}(p-q)+h\theta\omega$;
  - $h=\infty$ is a pure translation $[I\ \omega M;0\ 1]$;
  - otherwise $g=[e^{\hat\omega\theta}\ (I-e^{\hat\omega\theta})q+h\theta\omega;0\ 1]$;
  - any point on the axis gives the same $g$ (p. 39);
  - screw → twist: $\xi=(-\omega\times q+h\omega,\ \omega)$.

**MLS / MR:** MLS §2.2.2 (Props 2.4–2.5), §2.3.2–2.3.3 (book pp. 39–51, eqs. (2.32)–(2.44)). MR §3.2.3, §3.3.2–3.3.3 (eq. 3.88).

### L6 Recap, screw↔twist proof, Chasles, angular velocity (pp. 40–45). Lessons: R2
**Covered:**
- **Recap (p. 40):** proves that the screw form equals MLS (2.36) when $v=-\omega\times q+h\omega$. The notes expand $(I-e^{\hat\omega t})(-\hat\omega^2q)$ as a series and fold it back into $(I-e^{\hat\omega t})q$ (pp. 40–41).
- Zero pitch gives pure rotation; infinite pitch gives pure translation. Normalize unit twists by $\|\omega\|=1$, or by $\omega=0,\|v\|=1$.
- **Chasles (p. 42):**
  - example: a 90° turn with rise $d$ gives $h=2d/\pi$;
  - proof sketch: (1) exp is onto SE(3); (2) every twist has a screw motion (MLS Prop 2.10).
- **Angular velocity (pp. 42–44):**
  - differentiate $R^TR=I$, so $R^T\dot R$ is skew;
  - $\omega^b=(R^T\dot R)^\vee$ and $\omega^s=(\dot RR^T)^\vee$;
  - $\hat\omega^s=R\hat\omega^bR^T$, so $\omega^s=R\omega^b$.
- **Passive vs active view (pp. 44–45):** passive is a change of coordinates, $p_a=g_{ab}p_b$; active moves points, $p\mapsto gp$.
- **A point fixed in B:**
  - $v_a=\dot R_{ab}p_b=\hat\omega^s p_a$;
  - the body velocity is **not** $\dot p_b\ (=0)$ but $\hat\omega^b p_b$.

**MLS / MR:** MLS §2.3.3 (Thm 2.11, Chasles), §2.4.1 (book pp. 51–53, eqs. (2.48)–(2.52)). MR §3.2.2, §3.3.3.1.

### L7 Spatial/body velocity, adjoint, velocity composition (pp. 46–52). Lessons: R2, R5
**Covered:**
- **Velocities (pp. 46–47):**
  - $\hat V^s=\dot gg^{-1}$, so $V^s=(-\dot RR^Tp+\dot p,\ (\dot RR^T)^\vee)$;
  - $\hat V^b=g^{-1}\dot g$, so $V^b=(R^T\dot p,\ (R^T\dot R)^\vee)$;
  - $v_{q_a}=\hat\omega^sq_a+v^s$.
- **Recap (p. 48):**
  - they are related by a similarity: $\hat V^b=g^{-1}\hat V^sg$;
  - "see MLS pp. 55–56": $V^s=\mathrm{Ad}_gV^b$ with $\mathrm{Ad}_g=[R\ \hat pR;0\ R]$;
  - $\mathrm{Ad}_{g^{-1}}=[R^T\ -(R^Tp)^\wedge R^T;0\ R^T]$, which equals MLS's $-R^T\hat p$;
  - $\mathrm{Ad}_{g^{-1}}=(\mathrm{Ad}_g)^{-1}$.
- **Velocity of a point expressed in the instantaneous body frame:** $v_{q_b}=g_{ab}^{-1}v_{q_a}=\hat V^b_{ab}q_b$ (p. 49).
- **Velocity of a screw motion (p. 50):** with $g(t)=e^{\hat\xi\theta(t)}g(0)$, $V^s=\xi\dot\theta$ and $V^b=(\mathrm{Ad}_{g(0)^{-1}}\xi)\dot\theta$.
- **Frame-invariance table (pp. 50–51):**

| | change of spatial frame (fixed $g_{ab}$) | change of body frame (fixed $g_{bc}$) |
|---|---|---|
| spatial velocity | $V^s_{ac}=\mathrm{Ad}_{g_{ab}}V^s_{bc}$ (changes) | invariant |
| body velocity | invariant | $V^b_{ac}=\mathrm{Ad}_{g_{bc}^{-1}}V^b_{ab}$ (changes) |

- **Composition of moving frames (pp. 51–52):**
  - $V^s_{ac}=V^s_{ab}+\mathrm{Ad}_{g_{ab}}V^s_{bc}$;
  - $V^b_{ac}=V^b_{bc}+\mathrm{Ad}_{g_{bc}^{-1}}V^b_{ab}$;
  - a screw transforms as $\xi'=\mathrm{Ad}_g\xi$.
- **Overview of part 1 (pp. 52–53):** points/vectors, rigid transformations, groups, representations, velocities, adjoints, DOF. Also a homomorphism example $\sigma:SO(2)\to SO(3)$. "**Final 12/21**" is written here.

**MLS / MR:** MLS §2.4.2–2.4.3 (book pp. 53–61; (2.53)–(2.61); Props 2.14–2.15; Ex. 2.5). MR §3.3.2 (Prop 3.22 and the adjoint).

### L8 Joints and forward kinematics: classical frames vs PoE (pp. 53–58). Lessons: R3, R4 (mention only)
**Covered:**
- **Scope:** "this class" is serial-link open chains with 1-DOF joints.
- **Joints:**
  - 1 DOF: revolute, prismatic, helical (all screw motions);
  - 2 DOF: cylindrical;
  - 3 DOF: planar, spherical ("lower pairs").
- **FK goal:** $g:Q\to SE(3)$, with $Q=\mathbb R^n$ (technically $T^n$). The notes classify $n<6$ / $n=6$ / $n>6$ (see §B′ on the term used for $n<6$).
- **Classical (Craig) approach (p. 54):** link frames, passive link-to-link transforms. Worked 3-joint example with frames S, 1, 2, 3, T:
  - $g_{s1}=[R_z(\theta_1),(0,0,l_1)]$;
  - $g_{12}=[R_z(\theta_2),(0,l_2,l_3)]$;
  - $g_{23}=[I,(0,l_4,0)]$;
  - $g_{3T}=[R_x(-\tfrac\pi2)R_z(\theta_3),(0,l_5,0)]$;
  - $g_{st}=g_{s1}g_{12}g_{23}g_{3T}$.
- **DH:** the only DH content is the one-liner "(D-H came up a standard procedure.)" (p. 55).
- **PoE, active view (pp. 55–57).** p. 57 is a scanned "Correction" page. Steps:
  1. place {S} and {T};
  2. pick $\theta=0$ and compute $g_{st}(0)$;
  3. find the $\xi_i$ at $\theta=0$ in {S}: revolute $(-\omega_i\times q_i,\omega_i)$, prismatic $(v_j,0)$;
  4. $g_{st}(\theta)=e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_n\theta_n}g_{st}(0)$.
- **"Does the order matter?" (p. 58):**
  - moving joint 1 first carries axis 2 to $\xi_2'=\mathrm{Ad}_{e^{\hat\xi_1\theta_1}}\xi_2$;
  - using $e^{ABA^{-1}}=Ae^BA^{-1}$, $e^{\hat\xi_2'\theta_2}e^{\hat\xi_1\theta_1}g(0)=e^{\hat\xi_1\theta_1}e^{\hat\xi_2\theta_2}g(0)$;
  - so the reverse order with *moved* axes equals the PoE with *home* axes.
  - This is exactly the bug in the final-project report (see `rdkdc-final-project.md`).

**MLS / MR:** MLS §3.1–3.2.2 (book pp. 81–93, PoE (3.3)), §3.2.3 for DH (pp. 93–95). MR §4.1.1, App. C.

### L9 Jacobians and singularities (pp. 59–64). Lessons: R5
**Covered:**
- **Analytic ("easy") Jacobian:**
  - pose as $r=(x,y,z,\alpha,\beta,\gamma)$, $\dot r=(\partial f/\partial q)\dot q$;
  - "**Euler angles introduce artificial singularities!**", so use $V^b_{st}=J^b_{st}\dot q$ or $V^s_{st}=J^s_{st}\dot q$.
- **Spatial Jacobian derivation (pp. 60–61):**
  - differentiate the PoE in $\dot g g^{-1}$;
  - $V^s=\sum_i\dot\theta_i\xi_i'$ with $\xi_i'=\mathrm{Ad}_{e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_{i-1}\theta_{i-1}}}\xi_i$;
  - $J^s=[\xi_1\ \xi_2'\cdots\xi_n']$;
  - $J^b=\mathrm{Ad}_{g_{st}^{-1}}J^s$. "See book for $J^b$."
- **Singularity:**
  - a configuration where $J$ drops below its usual rank;
  - for $n=6$, solve $\det J(q)=0$;
  - for $n<6$, singular when rank $<n$;
  - for $n>6$ (redundant: dexterity, sub-tasks such as collision avoidance), singular when rank $<6$.
- **Two physical effects** (p. 62): loss of instantaneous end-effector DOF, and inability to resist certain end-effector wrenches with joint torques.
- **Geometric singularity tests** for all-revolute arms, $J=[-\omega_i\times q_i;\omega_i]$:
  1. two collinear axes: $\xi_1-\xi_2=0$ (or $\xi_1+\xi_2=0$);
  2. three parallel coplanar axes;
  3. four axes through one point;
  4. **"not in book": four parallel, not necessarily coplanar, axes.** The notes say to follow a Mathematica notebook: subtract the last column from the others; the upper-left block becomes $\hat\omega[a\ b\ c]$, which is rank-deficient.

**MLS / MR:** MLS §3.4.1 (book pp. 115–118, (3.54)–(3.56)), §3.4.3 (Ex. 3.11–3.13, pp. 124–127). MR §5.1.1–5.1.5, §5.3.

### L10 What Jacobians are for: Cartesian control and resolved rate (pp. 65–71). Lessons: R6, R8 (kinematic control)
**Covered:**
- **Point model** $\dot x=u$, goal $x^*$:
  - **"CS approach", open loop:** plan $x_p(t)=\alpha(t)x^*+(1-\alpha(t))x_0$ with a smooth $\alpha$ from 0 to 1 and set $u=\dot x_p$;
  - **"EE approach", feedback:** $u=k(x^*-x)$, so $\dot e=-ke$ and $e(t)=e^{-kt}e_0$;
  - units $[k]=1/\mathrm s$; pick $\tau=1/k$ short enough (p. 66).
- **Resolved rate for a 3-DOF arm (p. 67):**
  - model $\dot q=u$;
  - law $u=kJ^{-1}(q)(x^*-x)$, giving $\dot x=k(x^*-x)$;
  - "must worry about singularities";
  - discrete time: $q_{k+1}=q_k+\Delta T\,kJ^{-1}(x^*-x)$. Two tuning knobs, $\Delta T$ and $k$ (p. 68).
- **SE(3) resolved rate (pp. 68–71), for a UR5-like 6R sketch with a target frame {T\*}:**
  - group error $g_{t^*t}=g_{st^*}^{-1}g_{st}$ (want $\to I$);
  - at $t=0$, $g_{t^*t}=e^{\hat\xi_0\theta}$;
  - goal: stay on the same screw while $\theta(t)\to0$;
  - because $g_{st^*}$ is constant, $V^b_{t^*t}=V^b_{st}=J^b_{st}\dot q$;
  - choose $V^b=-k\xi$ with $\xi=\xi_0\theta$ re-measured every step; then $\xi(t)=e^{-kt}\xi_0\theta_0$ and $g_{t^*t}\to I$;
  - **$\dot q=-k\,(J^b_{st})^{-1}\xi$**.
- **Aside (p. 70), speed-saturated variant:** $V=-k\,\xi/(\|\xi\|+\epsilon)$. This gives roughly constant speed far from the goal and $-(k/\epsilon)\xi$ near it.

**MLS / MR:**
- MLS §3.4.1 eq. (3.57) (book pp. 117–118) is the continuous resolved-rate law.
- MR §6.3 and §11.3.3 (task-space velocity control), and §6.2 for the related Newton–Raphson step.
- The JHU Lab 3 law $q_{k+1}=q_k-KT_{step}J_b^{-1}\xi_k$ with $\xi_k=\log(g_d^{-1}g_{st})^\vee$ is exactly this (README §5.2).

### L11 Statics by virtual work, wrenches; MATLAB tips (pp. 71–74). Lessons: R5
**Covered:**
- **Generalized forces** are defined by virtual work, $\delta W=\sum\tau_i\delta\theta_i$. At the end effector, $\delta W=F\cdot\delta x$.
- **Planar case:** from $F^TJ\delta\theta=\tau^T\delta\theta$ for all $\delta\theta$ ("cannot mindlessly cancel $\delta\theta$"), $\tau=J^TF$.
- **Wrenches** $F^b=(f^b,\tau^b)$; "see chapter 2".
- **"Noah's fix on unit":** $\delta W=F^b\cdot V^b_{st}\,\delta t$. The twist times $\delta t$ is the virtual displacement, not a velocity.
- **Result:** $\tau=(J^b_{st})^TF^b$.
- **MATLAB tips (p. 74, lab-related):**
  - compute a 6×1 twist from $g$; the line reads roughly "$\xi$ = g0 + Xi(g)", with the left part illegible;
  - `gtest = expm(hat(xi))`, then `gtest\g` should be $\approx I$ (mldivide);
  - `B = A\C` is preferred to `inv(A)*C`.

**MLS / MR:** MLS §3.4.2 (eqs. (3.59)–(3.60)), §2.5 (wrenches, pp. 61–66). MR §5.2, §3.4.

### L12 Manipulability and the SVD (pp. 75–78). Lessons: R5, F2–F4
**Covered:**
- **Ellipsoid picture:** $V=J\dot\theta$ maps the unit sphere of joint rates to an ellipsoid with semi-axes $\sigma_i=\sqrt{\lambda_i(J^TJ)}$ (sorted). The level sets of $\dot\theta^TJ^TJ\dot\theta$ are ellipsoids.
- **Three measures (p. 76), the same as MLS and Lab 3:**
  1. $\sigma_{\min}$;
  2. inverse condition number $\sigma_{\min}/\sigma_{\max}$;
  3. $\det J$: only for $n=6$, "not great numerically", equal to the product of the eigenvalues of $J$.
  - Aside: for a 7-DOF arm, $J=U\Sigma V^T$ with $V$ 7×7.
- **Sketch (p. 77):** a stretched-out planar arm has a degenerate (line) ellipse, which is the singularity.
- **"Clean-up SVD" (pp. 77–78):**
  - $J=U[\bar\Sigma\,|\,0]V^T$ for $n\ge6$;
  - $U$ holds the eigenvectors of $JJ^T$ (eigenvalues $\sigma_i^2$); $V$ those of $J^TJ$ (eigenvalues $\sigma_i^2$ plus $n-6$ zeros);
  - $JJ^T=U\bar\Sigma^2U^T$.
- **2×2 picture:** rotate by $V^T$, stretch by $\Sigma$, rotate by $U$; $y=\sigma_1a_1u_1+\sigma_2a_2u_2$.
- **Range and kernel from the SVD (pp. 85–86):** a rank-$t$ matrix has range $\operatorname{span}\{u_1..u_t\}$ and kernel $\operatorname{span}\{v_{t+1}..v_n\}$.

**MLS / MR:** MLS §3.4.4 (book pp. 127–129), App. A Ex. A.26–A.27. MR §5.4.

### L13 Jacobian-transpose (gradient) control and the start of IK (pp. 79–82). Lessons: R6
**Covered:**
- **Laws compared:**
  - resolved rate: $u=-k(J^b_{st})^{-1}\xi$ with $e^{\hat\xi}=g_{st^*}^{-1}g_{st}$;
  - "turns out we can also use" **$u=-k(J^b_{st})^T\xi$** (transpose, not inverse).
- **Motivation, 3-DOF position case:**
  - $V(\theta)=\tfrac12\|f(\theta)-x^*\|^2$;
  - $\dot\theta=-k\nabla_\theta V=-kJ^T\nabla_xV=-kJ^T(x-x^*)$;
  - sketch of level sets in $\theta$-space.
- **Inverse kinematics (p. 80):**
  - "in Prob Set 4 you solved for $\theta_1,\theta_2$ when $x\in W$";
  - general method for 6-DOF arms with a spherical wrist "and some others": Paden–Kahan subproblems, "three s.p. in MLS".
- **SP1 (p. 81):**
  - $\theta=\operatorname{atan2}(\omega^T(u'\times v'),\ u'^Tv')$;
  - $u=p-r$, $v=q-r$, $u'=u-\omega\omega^Tu$, $v'=v-\omega\omega^Tv$;
  - a solution exists iff $\omega^Tu=\omega^Tv$ and $\|u'\|=\|v'\|$.
- **SP2 (two intersecting axes) and SP3 (rotate to distance $\delta$):** only stated, with "0, 1 or 2 solutions (see book)". No derivation, no UR5 IK and no 8-solution discussion.

**MLS / MR:** MLS §3.3.2–3.3.3 (book pp. 99–103, (3.17)–(3.30)). MR §6.1–6.2. MR does not present $J^T$ control (see `mr-part2.md`).

### L14 Dynamical systems, state space, taxonomy (pp. 82–88). Lessons: R7, F7
**Covered:**
- **State-space form:** $\dot x=f(x,u)$, $x\in\mathbb R^n$, $u\in\mathbb R^p$.
- **Mass–spring–damper:** free-body diagram; $M\ddot x+B\dot x+kx=u$; state $z=[x;\dot x]$; $\dot z=[0\ 1;-k/m\ -B/m]z+[0;1/m]u$. Any $w=Pz$ with $P$ nonsingular is also a valid state.
- **Def. of state:** the minimum information at $t_0$ that, with future inputs, predicts the evolution.
- **Delay** $v(t)=u(t-T)$ needs an **infinite-dimensional state**, namely $u$ on $[t_0-T,t_0)$. Laplace: $e^{-sT}$, with a first-order Padé approximation (p. 84).
- **Taxonomy (pp. 85, 87–88):**
  - I. linear $\dot x=Ax+Bu$ (superposition) vs nonlinear (e.g. $\dot x=x^2$). Variation of constants: $x(t)=e^{At}x_0+\int_0^te^{A(t-\tau)}Bu\,d\tau$.
  - II. autonomous (time-invariant: time shifts of solutions are solutions) vs non-autonomous.
  - III. mechanical systems (obey Newton's laws; "we focus on these", usually autonomous and nonlinear).
  - IV. "∞ others".
- **Two Jacobians (p. 86):**
  - ① chain-rule Jacobian $\dot x=J\dot\theta$, with coordinates on both sides;
  - ② manipulator Jacobian $V=J\dot\theta$, with twist coordinates;
  - for a planar 3R arm, relate $(\dot x,\dot y,\dot\theta)$ to $(g^{-1}\dot g)^\vee$;
  - $\omega^s=J(\alpha,\beta,\gamma)[\dot\alpha;\dot\beta;\dot\gamma]$, the Euler-rate map.

**MLS / MR:** MLS §4.4 intro (book p. 179). MR §8.5 (forward dynamics), §11.2 (error dynamics). This is mostly beyond both books (see §C).

### L15 Particle dynamics, holonomic constraints, generalized coordinates (pp. 89–96). Lessons: R7
**Covered:**
- **Point masses:** $F=m\ddot p$ in an inertial frame.
- **Centripetal example (pp. 89–90):** a mass on a stiff rod; $F=mv^2/l$ is real and inward; "no outward force on the mass".
- **Rigidly connected particles:**
  - constraints $\|p_i-p_j\|^2-l_{ij}^2=0$, or generally holonomic $h_j(p_1..p_n)=0$;
  - constraint forces lie along $\Gamma_j=\nabla h_j\in\mathbb R^{3n}$.
- **Worked example, massless rod:** $\Gamma=2[p-q;\ q-p]$ (p. 92).
- **Stacked Newton equations (MLS (4.2)–(4.3)):**
  - $F_{ext}=\operatorname{diag}(m_iI)\ddot p+\sum_j\lambda_j\Gamma_j$;
  - Lagrange multipliers $\lambda_j$;
  - $3n+k$ unknowns and $3n+k$ equations.
- **"Better idea!" (p. 94):** generalized coordinates $p_i=f_i(\theta_1..\theta_m)$ with $m=3n-k$, which is equivalent to $h_j(p)=0$.
  - Pendulum: $p=l(\cos\theta,\sin\theta)$; the converse direction uses $\theta=\operatorname{atan2}(y,x)$.
- **Recap (p. 95), point on a line in 3-D:**
  - $h=(e_3^Tp,\ n^T(p-q))$, $\partial h/\partial p=[e_3^T;n^T]$;
  - two coordinate choices: an angle $\theta$, or distance $d$ along $\hat t=n\times e_3/\|n\times e_3\|$;
  - equivalence proved both ways (p. 96).

**MLS / MR:** MLS §4.1–4.2.1 (book pp. 155–158, (4.1)–(4.5)). MR §8.1.1 (and §8.7 for constrained dynamics).

### L16 Principle of least action, Euler–Lagrange, examples (pp. 95–101). Lessons: R7
**Covered:**
- "Read *Principle of Least Action* by Feynman" (p. 95).
- **Pendulum:**
  - $T=\tfrac12ml^2\dot\theta^2$, $V=mgl\sin\theta$ ($\theta$ from the horizontal);
  - $L=T-V$, action $S=\int L\,dt$.
- **Variation:** $\delta S=\int(\partial L/\partial\theta-\frac{d}{dt}\partial L/\partial\dot\theta)\delta\theta\,dt=0$ gives the Euler–Lagrange equations. With external forces, $\frac{d}{dt}\partial L/\partial\dot\theta_i-\partial L/\partial\theta_i=\Upsilon_i$ (MLS's $\Upsilon$).
- **"Obligatory example", mass–spring–damper:** $m\ddot x+kx=\Upsilon$, and the free-body diagram gives $\Upsilon=-B\dot x$.
- **Coordinate choice:** "Specific equations depend on the generalized coordinates; what the system does does not." Change of variables commutes with forming Lagrange's equations. Example: $(x,y)$ vs $(\rho,\theta)$.
- **Bead on a curve $y=f(x)$ (pp. 100–101):** the result should be $m(1+f'^2)\ddot x+mf'f''\dot x^2+mgf'=0$. The notes contain sign and factor slips (see §B′).

**MLS / MR:** MLS §4.2.1 (Thm 4.1, (4.5)), Ex. 4.1. MR §8.1.1. Neither book derives E–L from the action; the notes do.

### L17 Lyapunov stability (pp. 102–105, paper scans). Lessons: R8
**Covered:**
- **Model:** $\dot x=f(x)$. $M(q)\ddot q+C\dot q+N=0$ becomes this form with $x=[q;\dot q]$; adding an input gives $\dot x=f(x,u)$.
- **"Many (many!)" definitions:** input-to-state, input-output, linear (linearization), Lyapunov.
- **Equilibrium:** $f(x^*)=0$; shift coordinates so that $x^*=0$.
- **Definitions (p. 103):**
  - stable in the sense of Lyapunov ("start close, stay close", $\epsilon$–$\delta$);
  - asymptotically stable (stable plus attractive);
  - exponentially stable, $\|x(t)\|\le me^{-\alpha t}\|x_0\|$; the largest $\alpha$ is the convergence rate.
- **Examples (pp. 103–105):**
  - $\ddot y+y=0$ is stable but not asymptotically stable ($\|x\|=|A|$, choose $\delta=\epsilon$);
  - $m\ddot y+b\dot y+ky=0$ with $m,k,b>0$ is asymptotically stable (spiral and node phase portraits);
  - $\dot x=ax$ with $a>0$ is unstable.
- **Locally positive definite function:** $V(0)=0$ and $V\ge\alpha(\|x\|)$ on a ball, with $\alpha$ strictly increasing.
- **First Lyapunov theorem:** stated loosely (see §B′). **The notes end here.**

**MLS / MR:** MLS §4.4 (book pp. 179–189; Defs 4.1–4.6, Thm 4.4, Table 4.1; LaSalle Thm 4.7 is not reached). MR has no Lyapunov section; energy arguments appear in §11.4.

---

## B. Inferred lecture order and what it says about the real course

**Order (Fall 2017):**
1. math preliminaries;
2. rigid maps and SO(n);
3. 2R overview (FK, IK, Jacobian);
4. groups and rotation representations;
5. group actions, semidirect SE(3), homogeneous coordinates;
6. DOF by constraint rank, matrix exp;
7. exp/log, twists, screws;
8. Chasles, angular velocity;
9. spatial/body velocity, adjoint, composition (overview: "Final 12/21");
10. joints, classical FK vs PoE;
11. Jacobians, singularities;
12. Cartesian and resolved-rate control (point, then SE(3));
13. statics, wrenches;
14. manipulability, SVD;
15. $J^T$ (gradient) control;
16. Paden–Kahan IK;
17. state space and taxonomy;
18. constrained particles, Lagrange multipliers;
19. least action, Euler–Lagrange;
20. Lyapunov definitions.

That is about 17 recorded lectures. Fall 2017 had about 26, so roughly the last 3–4 weeks (open-chain dynamics, control) and some missed sessions are not in the notebook **(inferred)**.

**Differences from the reconstructed syllabus in `courses/01-rdkdc/README.md` §2:**
1. **Week 1 is abstract math, not SO(3).** The course opens with LVS axioms, norms, linear maps, bases, and a whole-course preview through the 2R arm (FK, IK, $J$, and even $\dot\theta=J^{-1}V^*$ in lecture 2).
2. **The group-theoretic approach is core, not optional.** Groups, group actions, direct vs semidirect product and homomorphisms are taught before twists (README lists "Lie-group view (optional) — App. A").
3. **A lecture on DOF by constraint rank** (implicit-function-theorem style) has no slot in the README.
4. **Euler angles and quaternions get less than one lecture,** not a full week (README wk 3). The Euler XYZ formulas are presumably left to homework (README: HW3).
5. **Wrenches are not taught with Ch. 2.** They appear only after the Jacobian, as statics by virtual work (README wk 6 pairs wrenches with PoE).
6. **There is no DH lecture.** DH gets one line (p. 55), consistent with DH arriving only through the Keating handout (README wk 7 schedules a DH lecture, unverified).
7. **The Jacobian and its uses come before IK.** The order is Jacobian → singularities → resolved rate → statics → manipulability → $J^T$ control → Paden–Kahan. The README has IK in wk 8, *before* the Jacobian (wk 9–11).
8. **IK is short:** SP1 formula, SP2/SP3 statements, and a reference to PS4 (2-link IK). There is no UR5 8-solution IK in lecture.
9. **Kinematic control is a lecture topic,** with open-loop vs feedback, time constant, discrete-time gains and a speed-saturated variant. It is not just a Lab 3 detail.
10. **Dynamics starts with systems theory** (state, delays, linear/autonomous taxonomy), then constrained particles and Lagrange multipliers, then least action → Euler–Lagrange. The README starts dynamics at Lagrangian $M,C,N$ (wk 12).
11. **Lyapunov definitions come before any controller,** and the notes stop there. PD, computed torque and trajectory generation are not recorded; in Fall 2017 they were at most squeezed into the last lectures.
12. **Homework evidence:**
    - PS1 included $R(v\times w)=(Rv)\times(Rw)$;
    - **PS4 included 2R IK** (README infers HW3 = Euler angles, HW6 = UR5);
    - singularity example 4 used a Mathematica notebook.
13. **Exam:** final on 21 Dec 2017. No midterm is mentioned; p. 52's "Overview" may be a midterm review **(unverified)**.

## B′. Errors in the notes (checked against MLS; do not copy)
| PDF p. | Notes say | Correct (source) |
|---|---|---|
| 2 | $\alpha\cdot x\in F$ (closure) | $\alpha x\in X$ |
| 3 | $(\alpha+\beta)=\alpha x+\beta x$ | $(\alpha+\beta)x=\ldots$ |
| 4 | $\|x\|_p=\sqrt[p]{\sum x_i^p}$ | $\big(\sum|x_i|^p\big)^{1/p}$ |
| 21 | unit quaternion: $\alpha^2+\|q\|=1$ | $\alpha^2+\|q\|^2=1$ (MLS §2.2.3) |
| 22 | $i^2=1$ | $i^2=-1$ |
| 30 | $\hat\omega^3q=-\|\omega\|^3\hat\omega q$ | $\hat\omega^3=-\|\omega\|^2\hat\omega$ (MLS Lemma 2.3, (2.13)) |
| 30 | even $k$: $\hat\omega^k=\pm\|\omega\|^{k-1}\hat\omega^2$ | $\pm\|\omega\|^{k-2}\hat\omega^2$ |
| 30 | $e^{\hat\omega}=I+\sin\theta\,\hat\omega+(1-\cos\theta)\hat\omega^2$, $\theta=\|\omega\|$ | needs $\hat\omega/\theta$ and $\hat\omega^2/\theta^2$ unless $\|\omega\|=1$ (MLS (2.14), Ex. 2.12) |
| 37 | $\exp(\hat\xi\theta)=\ldots+(\theta-\sin\theta)\hat\xi^2$ | last term $\hat\xi^3$ (p. 36 has it right) |
| 39 | "let $q_2=p+\lambda\omega$" | $q_2=q+\lambda\omega$ (another point on the axis) |
| 40 | "set $v=-(\omega\times q+h\omega)$" | $v=-\omega\times q+h\omega$ (MLS §2.3.3) |
| 42 | "Charles theorem" | Chasles' theorem (MLS Thm 2.11) |
| 46 | "$\hat V^s$ = spatial body velocity" | spatial velocity |
| 50 | $\hat V^b=\dot gg^{-1}$ | $\hat V^b=g^{-1}\dot g$ (the result $V^b=\mathrm{Ad}_{g(0)^{-1}}\xi\dot\theta$ is right) |
| 52 | $V^b_{ac}=V^b_{ac}+\ldots$ | $V^b_{ac}=V^b_{bc}+\mathrm{Ad}_{g_{bc}^{-1}}V^b_{ab}$ (listed correctly below it) |
| 54 | $n<6$ = "under-actuated robot" | $n<6$ joints is a *kinematically deficient* arm; "underactuated" conventionally means fewer actuators than DOF |
| 55 | $g_{st}(\theta)=e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_n\theta_n}$ | $\cdots g_{st}(0)$ (MLS (3.3); the notes fix this on p. 57) |
| 67 | $J\in\mathbb R^{2\times3}$ | $3\times3$ ($x,q\in\mathbb R^3$) |
| 68 vs 69 | error $g_{st}(g^*_{st})^{-1}=e^{\theta\hat\xi_e}$ | use the **body** error $g_{st^*}^{-1}g_{st}$ with $J^b$ (as p. 69 and Lab 3 do); the right-inverse form pairs with $J^s$ |
| 84 | $e^{-sT}=\frac{1-sT/2}{1+sT/2}$ | "≈" (first-order Padé), not equality |
| 85 | $x(t)=e^{-At}x_0$ | $e^{At}x_0$ |
| 97 | the trajectory "minimizes $S$" | $S$ is **stationary** ($\delta S=0$), not necessarily minimal |
| 100 | $L=\tfrac12m[\dot x^2+(f'\dot x)^2]+mgf$ | $\ldots-mgf(x)$ |
| 100 | $\partial L/\partial x=m(f'\dot x)f''-mgf'$; $\tfrac{d}{dt}\partial L/\partial\dot x=\ldots+2mf'\dot xf''$ | $mf'f''\dot x^2-mgf'$ and $\ldots+2mf'f''\dot x^2$ |
| 101 | final gravity term reads "$mg f(x)$" | $mgf'(x)$ |
| 102 | "consider a time-variant system $\dot x=f(x)$ (not $f(x,t)$)" | time-**in**variant (autonomous) |
| 105 | "$V$ l.p.d. and $\dot V\le0$ (neg definite) ⇒ stability" | $\dot V\le0$ is negative *semi*definite; MLS Thm 4.4: $V$ lpdf and $-\dot V\ge0$ locally ⇒ stable; $-\dot V$ lpdf ⇒ asymptotically stable |

**Checked and correct:**
- eigenvalues of $\hat x$ (p. 11);
- the $\mathrm{Ad}_{g^{-1}}$ block (p. 48; equals MLS's $-R^T\hat p$);
- $J^b=\mathrm{Ad}_{g_{st}^{-1}}J^s$ (MLS (3.56));
- SP1 (MLS (3.17));
- $\tau=(J^b)^TF^b$ (MLS (3.59));
- the manipulability measures (MLS §3.4.4);
- the PoE order argument (p. 58);
- the four singularity tests (MLS Ex. 3.11–3.13, plus the extra case).

---

## C. In these notes but missing or thin in `courses/01-rdkdc/README.md` and the MLS/MR library notes
1. **LVS axioms, norms, bases via an ODE solution space** as lecture content (README §9 lists linear algebra only as a prerequisite).
2. **Groups, actions, direct vs semidirect product, homomorphism $\sigma$** taught early. The worked "yaw 90° + 1 m" example shows why $SO(3)\times\mathbb R^3$ with the direct product is wrong. (MLS App. A is covered in `mls-part2.md` but labelled advanced; the README calls it optional.)
3. **DOF by the rank of the constraint Jacobian,** with the rank-deficient trap (the same line described by $\|(I-vv^T)x\|^2=0$ vs $(I-vv^T)x=0$) and "read the implicit function theorem". MR Ch. 2 has Grübler and implicit representations, but not this test.
4. **Frame-invariance table and velocity composition:** $V^s_{ac}=V^s_{ab}+\mathrm{Ad}_{g_{ab}}V^s_{bc}$ and $V^b_{ac}=V^b_{bc}+\mathrm{Ad}_{g_{bc}^{-1}}V^b_{ab}$. The README checklist only has $V^s=\mathrm{Ad}_gV^b$.
5. **PoE order argument** (reverse order with moved axes = PoE with home axes) via $e^{ABA^{-1}}=Ae^BA^{-1}$.
6. **Fourth singularity test, "not in book":** four parallel, not necessarily coplanar, revolute axes.
7. **Kinematic control as theory:**
   - open-loop path vs feedback;
   - time constant $1/k$ and units of $k$;
   - discrete-time law with two knobs;
   - the SE(3) derivation showing the error **stays on one screw** and decays as $e^{-kt}$;
   - speed-saturated $V=-k\xi/(\|\xi\|+\epsilon)$.
8. **Statics via virtual work** with the $V^b\delta t$ "unit fix", and the **$J^T$ law as gradient descent** on $\tfrac12\|x-x^*\|^2$, derived in full. The README has a one-liner; MR omits $J^T$ control.
9. **SVD details:** $U$/$V$ as eigenvectors of $JJ^T$/$J^TJ$; range and kernel from the SVD; the 7-DOF case.
10. **Systems-theory block:**
    - definition of state;
    - delays need infinite state (Padé);
    - linear/nonlinear and autonomous taxonomy;
    - variation-of-constants formula.
11. **Constrained-particle dynamics** with Lagrange multipliers and $3n+k$ equations, and the move to generalized coordinates ($m=3n-k$) with an equivalence proof.
12. **Least-action derivation** of Euler–Lagrange; coordinate-invariance of Lagrange's equations; bead-on-a-curve example.
13. **Lyapunov definitions in detail** (stable, asymptotic, exponential with convergence rate; lpdf), with the three canonical examples. The README has a single checkbox.
14. **MATLAB idioms the lecturer pushes:**
    - `expm`/`logm` round trip;
    - test a twist log with `expm(hat(xi))\g ≈ I`;
    - prefer `A\C` to `inv(A)*C`.

**In the README but not in these notes** (expect them from other sources):
- DH and the UR5 table;
- UR5 8-solution IK;
- Euler XYZ formulas;
- reciprocal screws;
- Newton–Raphson and damped least squares;
- open-chain $M,C,N$, Christoffel symbols, $\dot M-2C$;
- the two-link arm dynamics;
- LaSalle;
- PD+gravity, computed torque, workspace control;
- trajectory generation;
- ROS.

---

## D. Best-reference map (notes pages → book sections)
- **[F2–F4]** notes pp. 2–11, 33, 75–78, 85–86 (LVS, norms, bases, skew/symmetric split, SVD) → Strang; MLS §2.1.
- **[F7]** notes pp. 28–30, 34, 82–88 (matrix exponential, state space, variation of constants) → MLS §2.2.2, Ex. 2.8; MR §8.5.
- **[R1]** notes pp. 11–14, 19–22, 26–35:
  - rigid maps, SO(n);
  - rotation representations, quaternion $S^3/\pm$;
  - Rodrigues and the log, MATLAB expm/logm;
  - → MLS §2.1–2.2 (book pp. 19–34), App. A §A.1–A.2; MR §3.2, App. B.
- **[R2]** notes pp. 22–26, 35–52:
  - semidirect SE(3), homogeneous coordinates;
  - twists, screws, Chasles;
  - spatial/body velocity, adjoint, composition;
  - → MLS §2.3–2.4 (book pp. 34–61); MR §3.3.
- **[R3]** notes pp. 53–58: joints, classical frames vs PoE, PoE order → MLS §3.1–3.2.2 (book pp. 81–93); MR §4.1.
- **[R4]** notes: **nothing beyond p. 55's one line**. Use the Keating UR5 handout, MLS §3.2.3 (pp. 93–95) and MR App. C.
- **[R5]** notes pp. 59–64, 71–78:
  - $J^s$, $J^b$;
  - singularities plus 4 tests;
  - statics by virtual work;
  - manipulability, SVD;
  - → MLS §3.4, §2.5 (book pp. 115–129, 61–66); MR §5.1–5.4.
- **[R6]** notes pp. 15–18, 65–71, 79–82:
  - 2R IK;
  - point and SE(3) resolved rate;
  - $J^T$ gradient control;
  - Paden–Kahan SP1–3;
  - → MLS §3.3, eq. (3.57); MR §6.1–6.3, §11.3.3.
- **[R7]** notes pp. 82–101:
  - state space;
  - constrained particles, Lagrange multipliers, generalized coordinates;
  - least action, Euler–Lagrange, examples;
  - → MLS §4.1–4.2.1 (book pp. 155–158); MR §8.1.1.
  - For $M,C,N$ the notes have nothing; use MLS §4.2.3–4.3 and MR §8.1.2–8.1.3.
- **[R8]** notes pp. 65–71 (kinematic feedback, $e^{-kt}$) and 102–105 (Lyapunov definitions and examples) → MLS §4.4 (book pp. 179–189). For PD and computed torque the notes have nothing; use MLS §4.5 and MR §11.4.
