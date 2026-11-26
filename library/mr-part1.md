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

