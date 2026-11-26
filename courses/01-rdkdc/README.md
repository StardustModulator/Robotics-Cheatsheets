# EN.530.646 — Robot Devices, Kinematics, Dynamics, and Control (RDKDC)

> Self-study dossier for an incoming JHU Robotics MSE student, researched 2026-11-26.
> **How sources are marked:** a URL after a claim is the source for it. **(unverified)** means the claim is
> plausible but I could not confirm it from a primary source. **(from search snippet)** means a search engine
> summarized the page and I could not open the page myself. Most `*.jhu.edu` pages could not be opened from
> this research environment, so the catalog, schedule and advising-manual facts below come from search
> snippets. Public GitHub repos *were* readable, so the lab and project details are first-hand.

---

## 1. Course identity

| Field | Value | Source / confidence |
|---|---|---|
| Number / title | EN.530.646 *Robot Devices, Kinematics, Dynamics, and Control* (students and staff call it "RDKDC" or "Robot DKDC") | JHU catalog, Fall 2026 Lab 0/1 handouts (first-hand) |
| Department | Mechanical Engineering; the labs run in LCSR (Wyman 170) | Lab 0 handout (first-hand) |
| Credits | 4 | ME catalog and Robotics MSE manual (from search snippet) [1][2] |
| Instructor (now) | **Jin Seob Kim, Ph.D.**, Associate Teaching Professor, ME Dept./LCSR. He signs the Fall 2026 handouts. His Fall 2025 handouts say "Senior Lecturer". | Lab 0/Lab 1 Fall 2026 PDFs in a public student repo [10]; Fall 2025 Lab 0 `.tex` [11] |
| Instructor (historical) | **Noah J. Cowan.** He taught it for years (Fall 2014–2017 and later), redeveloped it in Fall 2015 around a UR5, and used a Lie-group approach. The current lab handouts still say "originally developed by Prof. Noah Cowan". | Cowan CV and course page (from search snippet) [3][4]; Lab 1 footnote (first-hand) |
| Recent / upcoming terms | Spring 2026: Cowan, TTh 12:00–1:15. **Fall 2026: Kim, TTh 10:30–11:45, labs TBA.** Spring 2027 (anticipated): Kim, TTh 12:00–1:15. Coursicle lists Spring 2027 sections at 10:30, 12:00 and 18:00. | ME graduate course-schedule page and Coursicle (from search snippet) [5][6] |
| Offered | Historically in the fall. The course now appears in **both fall and spring**. A TA page also mentions a Summer 2025 term **(unverified)**. | [5][7] |
| Prerequisites | Recommended rather than formally required: multivariable calculus, classical physics, linear algebra, ODEs. One catalog note also mentions dynamics and feedback control. | Semester.ly, advising manual, catalog (from search snippet) [2][8] |
| Grading | Weekly homework, MATLAB labs on a UR5, a midterm, a final exam and a final project. Homework, exams and labs+project are roughly equal shares. No late work without prior approval. | JHU public syllabus on HelioCampus (from search snippet; exact percentages **unverified**) [9] |
| AI policy (Fall 2026) | The lab handouts say LLM/GenAI tools are **not permitted unless stated otherwise**. | Lab 1 Fall 2026 PDF (first-hand) [10] |
| Downstream | EN.530.707 *Robot System Programming* requires EN.530.646 and EN.601.463/663 (or instructor permission). | ME MSE manuals 2023–25 (from search snippet) [12] |
| Scheduling caution | Older ME manuals and the ME tracks page **strongly recommend not taking EN.530.646 and EN.601.663 (ASBR) in the same semester**. The 2026–27 manual has a section titled "Recommendation on EN.530.646 RDKDC and EN.601.463/663 ASBR" whose text I could not read. **Check it before you register.** | [12][13] |

**Official description** (paraphrased from the catalog, advising manual and public syllabus [1][2][9]): a
graduate-level introduction to the mechanics of robotic systems. It covers (1) the geometry and mathematical
representation of rigid-body motion, (2) forward and inverse kinematics of articulated arms, (3) the
manipulator Jacobian and its uses, (4) analytical computation of arm dynamics, (5) manipulator control and,
as time permits, (6) trajectory generation, actuation and design issues. Students program a UR5 robot, first
in simulation (RViz) and then on hardware.

---

## 2. Reconstructed week-by-week syllabus

No public lecture schedule exists. **The table below is reconstructed (unverified as a schedule)** from:

- the catalog topic list;
- textbook order (MLS Ch. 2 → 3 → 4);
- cross-references inside the labs:
  - Lab 1 (out **10/20/2026**) cites *"formulas you derived in homework 3"* for Euler-angle inversion;
  - Lab 3 cites *"one of the singular configurations of the UR5 identified in HW6 is when θ3 = 0"* and *"Chapter 3, Section 4.4 of MLS"* for manipulability;
- student repos dated Nov–Dec, which place Lab 3 and the final project at the end of the term.

The schedule assumes about 14 weeks of TTh lectures, weekly homework and one midterm.

| Wk | Topic | MLS | Modern Robotics (MR) | Assessed in |
|---|---|---|---|---|
| 1 | Course intro; rigid-body transformations; SO(3), rotation matrices | 2.1, 2.2.1 | 2.1–2.3, 3.1, 3.2.1 | HW1 |
| 2 | so(3), hat/vee, exponential coordinates, Rodrigues, log map | 2.2.2 | 3.2.2–3.2.3 | HW2 |
| 3 | Other rotation parametrizations: Euler angles (ZYZ/XYZ), RPY, quaternions, singularities of Euler angles | 2.2.3 | App. B | HW3 → **Lab 1** (EULERXYZ/INV) |
| 4 | SE(3), homogeneous coordinates, twists, se(3), exponential of a twist, screws, Chasles | 2.3 | 3.3.1, 3.3.3 | HW4 → **Lab 1** (SKEW6, EXPCF, FINV) |
| 5 | Rigid-body velocity: spatial vs. body velocity, adjoint, velocity of a screw motion | 2.4 | 3.3.2 | HW5 → **Lab 2** (frame composition) |
| 6 | Wrenches, reciprocal screws; **forward kinematics by product of exponentials**; workspace | 2.5, 3.2 | 3.4, 4.1 | HW5–6 |
| 7 | **DH parameters** (separate handout, see §4), DH vs. PoE; UR5 FK both ways | (not in MLS) | App. C | HW6 (UR5 FK/Jacobian/singularities) |
| 8 | Inverse kinematics: Paden–Kahan subproblems 1–3, closed-form 6R; UR5's 8 solutions | 3.3 | 6.1 | HW7 (unverified) |
| 9 | Manipulator Jacobian: spatial and body Jacobians, end-effector forces / statics | 3.4.1–3.4.2 | 5.1–5.2 | **Lab 3** |
| 10 | Singularities, manipulability; redundant and parallel manipulators | 3.4.3–3.4.4, 3.5 | 5.3–5.4, 7 | **Lab 3** |
| 11 | Differential IK: resolved-rate, Jacobian transpose (gradient), Newton–Raphson | 3.4 (+ notes) | 6.2–6.3 | **Lab 3**, final project |
| 12 | Lagrangian dynamics: kinetic/potential energy, inertia tensors, two-link example | 4.1–4.2 | 8.1–8.2 | HW |
| 13 | Open-chain dynamics M(θ), C(θ,θ̇), N(θ); Lyapunov stability | 4.3–4.4 | 8.1.2–8.1.3, 8.3–8.5 | HW |
| 14 | Control: PD + gravity, computed torque, workspace control; trajectory generation (if time); review | 4.5 | 9.1–9.3, 11.1–11.4 | Final exam; **final project demo** |

Midterm: probably after kinematics, around weeks 7–8 **(unverified)**. Final exam: cumulative, with heavy
dynamics content **(unverified)**.

---

## 3. Textbook chapter mapping

**Texts:**
- **MLS:** Murray, Li & Sastry, *A Mathematical Introduction to Robotic Manipulation*, CRC 1994. The free PDF is on Murray's Caltech site, and the book is now also open access (CC BY) via OAPEN / Taylor & Francis [20][21].
- **MR:** Lynch & Park, *Modern Robotics*, CUP 2017, updated 2019. Free preprint PDF, videos and code [22][23][24].

MLS section numbers are from my knowledge of the 1994 edition; check them against the PDF. The Lab 3 handout's
"Ch. 3 §4.4 = manipulability" matches this numbering.

| Topic | MLS | MR |
|---|---|---|
| Rigid-body transformations, SO(3) properties | §2.1, §2.2.1 | §3.1, §3.2.1 |
| Exponential coordinates for rotation, Rodrigues, log | §2.2.2 | §3.2.3 |
| Euler angles, RPY, quaternions | §2.2.3 | Appendix B |
| Homogeneous transforms, SE(3) | §2.3.1 | §3.3.1 |
| Twists, exponential coordinates for rigid motion | §2.3.2 | §3.3.2–3.3.3 |
| Screws, Chasles' theorem | §2.3.3 | §3.3.2 (screw interpretation) |
| Spatial/body velocity, adjoint | §2.4 | §3.3.2 |
| Wrenches, reciprocal screws | §2.5 | §3.4 |
| Lie-group view (optional) | Appendix A | — |
| **Forward kinematics, PoE** | §3.2 (3.2.2 PoE, 3.2.3 workspace) | Ch. 4: §4.1.1 space form, §4.1.2 examples (incl. a UR5 example), §4.1.3 body form; §4.2 URDF |
| **DH parameters** | Not taught. MLS deliberately uses PoE instead (see §4). | **Appendix C** (frame assignment, why 4 parameters, DH FK, examples, PoE↔DH relation) |
| **Inverse kinematics, Paden–Kahan** | §3.3 (3.3.3 subproblems, 3.3.4 solving IK with them) | §6.1 (analytic, PUMA/Stanford-type) |
| Numerical IK (Newton–Raphson) | Only sketched | §6.2 |
| Inverse velocity kinematics / resolved rate | §3.4 (end-effector velocity) | §6.3 |
| **Spatial & body Jacobian** | §3.4.1 | §5.1.1–5.1.4 |
| Statics (τ = Jᵀ F) | §3.4.2 | §5.2 |
| **Singularities** | §3.4.3 | §5.3 |
| **Manipulability** | §3.4.4 (σ_min, σ_min/σ_max, det J) | §5.4 (ellipsoids, μ₁, μ₂, μ₃) |
| Redundant / parallel manipulators | §3.5 | Ch. 7 |
| **Lagrangian dynamics** | §4.2 (4.2.1 formulation, 4.2.2 inertia, 4.2.3 two-link planar example) | §8.1 |
| Newton–Euler (single body) | §4.2.4 | §8.2–8.3 |
| **Open-chain dynamics M, C, N** | §4.3 (Lagrange for open chains; closed form via PoE) | §8.1.2–8.1.3, §8.4 |
| Lyapunov stability | §4.4 | (used in Ch. 11) |
| **PD, computed torque, workspace control** | §4.5 | §11.4 (11.4.2 computed torque, 11.4.3 task space) |
| Trajectory generation | — | Ch. 9 |
| Constrained manipulators / hybrid force | §4.6 | §11.5–11.6 |

**Notation trap (important).** MLS writes a twist as **ξ = (v, ω)**, linear part first, so its adjoint is
`Ad_g = [R, p̂R; 0, R]`. MR writes **V = (ω, v)**, angular part first, so `[Ad_T] = [R, 0; [p]R, R]`.

- Cowan-era course code uses MLS ordering. For example, `RevoluteTwist(q,w)` returns `[cross(q,w); w]` and `getXi` returns `[v; w]` [14].
- **The Fall 2026 Lab 1 handout defines `SKEW6` on ξ = [ω; v]**, angular first. A Fall 2026 student's code follows that convention [10].

Confirm your instructor's convention on day 1 and do not mix the two.

---

## 4. DH parameters: what JHU uses and how DH relates to PoE

### 4.1 What the course actually uses (evidence)

MLS has no DH treatment. The authors call DH "cumbersome and nonintuitive" and use PoE instead [21][25]. The
DH material in RDKDC comes from a **course handout**:

- **"M.E. 530.646 — UR5 Inverse Kinematics", Ryan Keating (TA), 2014, updated by Noah J. Cowan, 2016.** It assigns DH frames to the UR5 using **standard (distal, Spong-style) DH**, gives the table below, and derives the closed-form IK: θ1, then θ5, θ6, then θ3 (elbow up/down), θ2, θ4, for **8 solutions**. The PDF is in a public student repo [14b] and on SlideShare [15]. The course's `ur5InvKin.m` (author: TA Long Qian) is "adapted from Ryan Keating's 'UR5 Inverse Kinematics' paper". Its `DH.m` builds `Trans_z(d)·Rot_z(θ)·Trans_x(a)·Rot_x(α)`, which is the standard (distal) convention [14].
- **The final-project handout (Cowan, 2017)** says `ur5InvKin.m` is **provided**, returns a 6×8 matrix of solutions, and "computes the solutions following the Denavit–Hartenberg convention described in [Keating]". Students must define the constant transforms from Keating's frame "0" to the course's "S" (base) frame, and from frame "6" to the "T" (tool) frame [14b]. Student repos from 2023–2025 contain `ur5FwdKin_DH.m`, `compute_FK_DH.m` and `test_DH*.m`, so the DH↔course-frame bridge is still part of the project [16].
- The Keating handout follows the same approach as K. P. Hawkins' Georgia Tech tech report, *Analytic Inverse Kinematics for the UR-5/UR-10* (2013) [17]. That is useful as a second explanation.
- Whether Kim's lectures use Spong *Robot Modeling and Control* Ch. 3 or Craig *Introduction to Robotics* Ch. 3 (modified DH) for DH theory is **(unverified)**. Given the handout and code, learn **standard DH first** and know how modified DH differs.

**UR5 standard DH table** (Keating 2014/2016 [14b], same values as Universal Robots' published table [18]):

| i | aᵢ (m) | αᵢ | dᵢ (m) | θᵢ |
|---|---|---|---|---|
| 1 | 0 | π/2 | 0.089159 | θ1 |
| 2 | −0.425 | 0 | 0 | θ2 |
| 3 | −0.39225 | 0 | 0 | θ3 |
| 4 | 0 | π/2 | 0.10915 | θ4 |
| 5 | 0 | −π/2 | 0.09465 | θ5 |
| 6 | 0 | 0 | 0.0823 | θ6 |

Notes on the table:
- Corke's Robotics Toolbox `models.DH.UR5` uses d1 = **0.089459**, which looks like a typo for 0.089159 [19].
- Cowan-era student code uses rounded values (0.0892 / 0.425 / 0.392 / 0.1093 / 0.09475 / 0.0825) [14].
- The **UR5e** has different values: d1 = 0.1625, a2 = −0.425, a3 = −0.3922, d4 = 0.1333, d5 = 0.0997, d6 = 0.0996, with the same α column [18] (values from UR's table as I know it; verify). The Fall 2026 Lab 0 warns that **UR5 and UR5e kinematics differ, so code for one does not run on the other** [10].

### 4.2 Standard vs. modified DH

| | Standard / distal (Denavit–Hartenberg 1955; Spong; Corke `DHRobot`; Keating/JHU) | Modified / proximal (Craig; Corke `DHRobot` with `mdh`) |
|---|---|---|
| Frame i attached at | **distal** end of link i (on joint axis i+1) | **proximal** end of link i (on joint axis i) |
| zᵢ along | joint axis i+1 | joint axis i |
| Link transform | `A_i = Rot_z(θᵢ)·Trans_z(dᵢ)·Trans_x(aᵢ)·Rot_x(αᵢ)` | `ⁱ⁻¹T_i = Rot_x(α_{i−1})·Trans_x(a_{i−1})·Rot_z(θᵢ)·Trans_z(dᵢ)` |
| Index shift | aᵢ, αᵢ belong to row i | a_{i−1}, α_{i−1} appear in row i, so the a/α columns shift down one row |

Standard DH link matrix:
```
A_i = [ cθ  -sθ·cα   sθ·sα   a·cθ ]
      [ sθ   cθ·cα  -cθ·sα   a·sθ ]
      [ 0     sα      cα      d   ]
      [ 0     0       0       1   ]
T_0^n = A_1 A_2 ... A_n
```

### 4.3 Worked mini-procedure (standard DH)

1. Number the joints 1..n and draw each joint axis.
2. Set **z_{i−1} along joint axis i** (z0 along joint 1, …, z5 along joint 6).
3. Choose the origin of frame i where the **common normal** between z_{i−1} and z_i meets z_i.
   - If the axes intersect, use the intersection point.
   - If they are parallel, choose any point; pick one that makes d = 0.
4. Set **x_i along the common normal**, pointing from z_{i−1} to z_i. If the axes intersect, use x_i = ±(z_{i−1} × z_i). Then y_i = z_i × x_i.
5. Read off the four parameters of each row:
   - **aᵢ** = distance from z_{i−1} to z_i along x_i;
   - **αᵢ** = angle from z_{i−1} to z_i about x_i;
   - **dᵢ** = distance from x_{i−1} to x_i along z_{i−1};
   - **θᵢ** = angle from x_{i−1} to x_i about z_{i−1}. This is the joint variable for a revolute joint; for a prismatic joint dᵢ is the variable.
6. Multiply the link matrices: T₀ⁿ = A₁⋯Aₙ. Pre-multiply by a base transform and post-multiply by a tool transform to reach your world frame (S) and tool frame (T). In the JHU project this is g_ST = g_S0 · T₀⁶(θ) · g_6T.
7. **Sanity check:** at θ = 0, draw the frames and compare T₀ⁿ(0) with the drawing. Then compare against PoE (§4.4) for random θ.

### 4.4 The DH ↔ PoE relationship

- **PoE (MLS §3.2, MR §4.1)** needs only **two frames**, base S and tool T, plus one twist per joint, all expressed **at the zero configuration**:

  `g_st(θ) = e^{ξ̂₁θ₁} e^{ξ̂₂θ₂} ⋯ e^{ξ̂ₙθₙ} g_st(0)`

  For a revolute joint, ξᵢ = (−ωᵢ × qᵢ, ωᵢ), where ωᵢ is a unit axis and qᵢ is any point on that axis. For a prismatic joint, ξᵢ = (vᵢ, 0).
- **DH** places **n+1 link frames** under strict rules so that each joint needs **4 numbers** instead of 6. Each Aᵢ(θᵢ) is a relative transform between neighbouring frames.
- **Converting DH → PoE** (always possible):
  1. Set all joint variables to zero and compute T₀^{i−1}(0) for each i.
  2. The twist axis is ωᵢ = z-column of T₀^{i−1}(0), and the point is qᵢ = origin of T₀^{i−1}(0).
  3. Then g_st(0) = T₀ⁿ(0), with base and tool offsets added if needed.

  I implemented this for the UR5 table above and checked that PoE equals DH to **3.9×10⁻¹⁶ over 100 random configurations** (script in §10, Project 2). The resulting space-frame axes are:
  - ω₁ = (0,0,1); ω₂ = ω₃ = ω₄ = (0,−1,0); ω₅ = (0,0,−1); ω₆ = (0,−1,0)
  - M = g_st(0) has position (−0.81725, −0.19145, −0.00549)

  This matches the usual UR5 picture: the arm lies along −x₀ at zero in Keating's frame, and along +x in the course's S frame.
- **PoE → DH** is *not* one-to-one. Malik, Henderson & Prazenica (IEEE RA-L 2017, doi:10.1109/LRA.2017.2723470) give an algorithm [26][25].
- **Why each camp prefers its method:**
  - PoE handles revolute and prismatic joints the same way.
  - PoE has no special cases for parallel or intersecting axes, and is not ill-conditioned when consecutive axes are nearly parallel (a calibration issue with DH).
  - PoE's Jacobian columns come straight from adjoints.
  - DH is compact (4 parameters per joint), is the industry and URDF-adjacent lingua franca, and is the input format for most closed-form IK papers. That is why JHU uses it for UR5 IK.

---

## 5. Assignments, labs, project and exams

### 5.1 Homework
Weekly written problem sets, mostly MLS-style derivations and proofs [9]. Inferred themes:
- **HW3:** Euler-angle forward and inverse formulas.
- **HW6:** UR5 kinematics, including a UR5 parameter chart and singularities such as θ3 = 0 [14][16].

Course Hero lists a Fall 2026 homework from Kim, but the content is restricted [27]. No public problem sets
were found. Treat **MLS end-of-chapter exercises (Ch. 2–4)** and **MR end-of-chapter exercises** as the
closest proxies.

### 5.2 Labs (UR5/UR5e, ROS, MATLAB). Details are first-hand from public student repos.

**Toolchain**
- Fall 2025 and Fall 2026 use **Ubuntu 24.04 + ROS 2 Jazzy + MATLAB** (ROS Toolbox and Robotics System Toolbox) [10][11].
- **Fall 2025 offered starter code in MATLAB *and* Python. Fall 2026 is MATLAB-only.**
- The course ROS 2 package `rdkdc` is public: https://github.com/Warrior-hound/rdkdc_jazzy [28]. It provides `ros2 launch rdkdc ur5_simulation.launch.py` / `ur5e_simulation.launch.py`, a hardware interface launch, and the MATLAB classes `ur5_interface` and `tf_frame`.
- Before 2025 (Cowan era) the course used ROS 1 + MATLAB, with a `ur5_interface.m` wrapper and RViz. Projects were submitted on the LCSR GitLab ("git-teach", `git.lcsr.jhu.edu`) [14b][29].

**`ur5_interface` API** (Jazzy version, from the public repo [28]):
- `get_current_joints`, `move_joints(theta, time_interval)` (6×N waypoints, joint-space interpolation)
- `move_joints_vel`, `get_current_transformation(target, source)`
- `switch_to_pendant_control`, `switch_to_ros_control`, `enable_freedrive`
- `activate_pos_control`, `activate_vel_control`
- `tf_frame(base, name, g)` with `.move_frame` and `.read_frame` for RViz frame visualization

Older versions also had `init_gripper`, `open_gripper` and `close_gripper`.

**Lab sequence**

| Lab | Content | Functions students write |
|---|---|---|
| **Lab 0** | Install Ubuntu 24.04, ROS 2 Jazzy and MATLAB, or use the 12 Wyman 170 workstations; run the simulation | — |
| **Lab 1** (Fall 2026: out 10/20, due ~10/27) | Task 1: script the UR5(e) through ≥4 joint configurations in RViz. Task 2: rotation and homogeneous matrices with plotting helpers (`plotr`, `plotp3/4`, `plotv3/4`, `plotf`), showing Euler-angle singularity and 2π-ambiguity behaviour numerically | `ROTX/Y/Z`, `EULERXYZ`, `EULERXYZINV` (with gimbal-lock warning), `SKEW3`, `EXPCR` (Rodrigues), `SKEW6`, `EXPCF` (twist exponential), `FINV` (structured SE(3) inverse, no `inv`) [10] |
| **Lab 2** | Frame composition and visualization in RViz with `tf_frame`: random SE(3) transforms built from RPY, composed and checked against TF [16] (details **partly unverified**) | composition / inverse checks |
| **Lab 3** | UR5 differential kinematics and resolved-rate control (see list below) [16] | `ur5FwdKin`, `ur5BodyJacobian`, `manipulability`, `getXi`, `ur5RRcontrol` |

Lab 3 requirements, from a public 2025 student README that paraphrases the handout [16]:
- `ur5FwdKin(q)` builds g_st with PoE. Test it by placing a `tf_frame` at your FK result and checking that it sits on the simulated tool.
- `ur5BodyJacobian(q)` must be tested against a central-difference Jacobian. Column i ≈ (g⁻¹ ∂g/∂qᵢ)^∨, after "twistifying" (taking the skew part) and reporting ‖J_approx − J‖.
- `manipulability(J, 'sigmamin'|'detjac'|'invcond')` per MLS §3.4.4. Plot all three measures for θ3 ∈ (−π/4, π/4) around the θ3 = 0 singularity.
- `getXi(g)` is the matrix log of SE(3). Test it with `expm(ξ̂) == g` to machine precision.
- `ur5RRcontrol(g_desired, K, ur5)` implements **q_{k+1} = q_k − K·T_step·J_b(q_k)⁻¹·ξ_k**, where ξ_k = log(g_d⁻¹ g_st(q_k))^∨.
  - Stop when ‖v_k‖ and ‖ω_k‖ fall below thresholds (start around 5 cm and 15°).
  - **Abort and return −1 near a singularity.**
  - Return the final position error in cm.

### 5.3 Final project (team, real UR5 demo + report + video)

- **2017–2021 version (Cowan):** "teach" start and target poses by jogging the robot, then **pick-and-place a cube** with a gripper using three controllers [14b][30]:
  1. **IK-based:** use the provided `ur5InvKin`, choose the best of 8 solutions, and track a Cartesian path.
  2. **Resolved-rate (RR):** pull the error twist back through J⁻¹.
  3. **Gradient / Jacobian-transpose (JT):** use Jᵀ instead of J⁻¹.

  Teaching should use a manipulability measure to pick a good configuration. The minimum task earns roughly a B/B+; an "extra" (a creative task, hybrid control, etc.) is needed for an A.
- **~2022–2025 version: "place-and-draw".** Teach start and end points with the pendant, then draw lines or shapes with a pen tool (the pen-tip offset is a constant g_6T), again with IK, RR and JT. Extras included writing "RDKDC" and tic-tac-toe [16][31].
- **Deliverables:** a single repo with main script `ur5_project.m` that lets the user choose the control mode; a PDF report covering workflow, new math (e.g. the JT/gradient derivation) and workload split; and a 2–4 min YouTube video [14b].

### 5.4 Exams
A midterm and a final [9]. Expect hand derivations: twists, exponentials, adjoints, PoE FK, Jacobians,
Paden–Kahan, Lagrangian dynamics of 2–3-link arms, stability and control laws. Format details **(unverified)**.

---

## 6. Core concept checklist and key equations
(MLS ordering ξ = (v, ω) unless marked MR.)

**Rotations, SO(3)**
- [ ] SO(3) = {R ∈ ℝ³ˣ³ : RᵀR = I, det R = +1}. Composition order: a fixed (space) frame premultiplies; the body frame postmultiplies.
- [ ] hat/vee: ω̂ = [[0,−ω₃,ω₂],[ω₃,0,−ω₁],[−ω₂,ω₁,0]], so ω̂p = ω × p. Also R ω̂ Rᵀ = (Rω)^.
- [ ] **Rodrigues** (‖ω‖ = 1): e^{ω̂θ} = I + ω̂ sin θ + ω̂²(1 − cos θ).
- [ ] **Log SO(3)**:
  - θ = arccos((tr R − 1)/2) and ω̂ = (R − Rᵀ)/(2 sin θ);
  - θ = 0: ω is arbitrary;
  - θ = π: take ω from the column of (R + I)/2.
- [ ] Euler XYZ / ZYZ forward and inverse formulas; the singularity (gimbal lock) is at pitch ±π/2 for XYZ and at θ = 0, π for ZYZ. Quaternion ↔ R.

**Rigid motions, SE(3)**
- [ ] g = (p, R) = [R p; 0 1], with inverse g⁻¹ = [Rᵀ −Rᵀp; 0 1] (this is Lab 1's `FINV`).
- [ ] Twist ξ = (v, ω), and ξ̂ = [ω̂ v; 0 0] ∈ se(3).
  - Revolute joint: ξ = (−ω × q, ω).
  - Prismatic joint: ξ = (v, 0).
  - Pitch h = ωᵀv / ‖ω‖²; ξ = (−ω × q + hω, ω).
- [ ] **Twist exponential** (‖ω‖ = 1):
  - e^{ξ̂θ} = [e^{ω̂θ}, (I − e^{ω̂θ})(ω × v) + ωωᵀvθ; 0, 1].
  - If ω = 0: [I, vθ; 0, 1].
  - MR form: p = (Iθ + (1 − cos θ)[ω] + (θ − sin θ)[ω]²) v.
- [ ] Log of SE(3) (`getXi`): first get (ω, θ) from R, then v = A⁻¹p with
  A⁻¹ = (1/θ)I − ½ω̂ + (1/θ − ½ cot(θ/2)) ω̂² (unit ω; MR §3.3.3).
- [ ] Chasles: every rigid motion is a screw motion.

**Velocities and the adjoint**
- [ ] Spatial velocity V^s = (ġ g⁻¹)^∨; body velocity V^b = (g⁻¹ ġ)^∨; V^s = Ad_g V^b.
- [ ] **Adjoint** (MLS): Ad_g = [R p̂R; 0 R], Ad_g⁻¹ = [Rᵀ −Rᵀp̂; 0 Rᵀ], and g ξ̂ g⁻¹ = (Ad_g ξ)^.
  MR (ω first): [Ad_T] = [R 0; [p]R R].
- [ ] Wrenches F = (f, τ) transform by Ad_gᵀ (power pairing VᵀF is invariant).

**Forward kinematics**
- [ ] **PoE:** g_st(θ) = e^{ξ̂₁θ₁}⋯e^{ξ̂ₙθₙ} g_st(0). MR body form: T = M e^{[B₁]θ₁}⋯e^{[Bₙ]θₙ}, with Bᵢ = Ad_{M⁻¹} Sᵢ.
- [ ] DH: T = ∏ Rot_z(θ)Trans_z(d)Trans_x(a)Rot_x(α) (§4).

**Jacobians**
- [ ] **Spatial Jacobian:** J^s = [ξ₁, ξ₂′, …, ξₙ′], with ξᵢ′ = Ad_{(e^{ξ̂₁θ₁}⋯e^{ξ̂_{i−1}θ_{i−1}})} ξᵢ.
- [ ] **Body Jacobian:** J^b = [ξ₁†, …, ξₙ†], with ξᵢ† = Ad⁻¹_{(e^{ξ̂ᵢθᵢ}⋯e^{ξ̂ₙθₙ} g_st(0))} ξᵢ.
- [ ] J^s = Ad_g J^b. V^b = J^b θ̇. Statics: τ = (J^b)ᵀ F^b.
- [ ] Analytic Jacobian vs. geometric Jacobian (MR §5.1.5). Know that RR control uses the **body** Jacobian with the body error twist.

**Singularities and manipulability**
- [ ] Singular when rank J < 6.
- [ ] UR5 singularity classes:
  - **shoulder:** wrist centre on the axis of joint 1;
  - **elbow:** θ3 = 0 or π;
  - **wrist:** θ5 = 0 or π, which aligns axes 4 and 6.
- [ ] MLS measures: σ_min(J); inverse condition number σ_min/σ_max; det J.
  MR measures on A = JJᵀ: μ₁ = √(λmax/λmin), μ₂ = λmax/λmin, μ₃ = √det A (∝ ellipsoid volume).

**Inverse kinematics**
- [ ] **Paden–Kahan subproblems** (MLS §3.3.3):
  1. **SP1** — rotate point p about axis ξ to reach q. With u = p − r, v = q − r, u′ = u − ωωᵀu, v′ = v − ωωᵀv: θ = atan2(ωᵀ(u′ × v′), u′ᵀv′).
  2. **SP2** — rotate about two intersecting axes: 0, 1 or 2 solutions.
  3. **SP3** — rotate p about ξ to a distance δ from q: θ = θ₀ ± arccos((‖u′‖² + ‖v′‖² − δ′²)/(2‖u′‖‖v′‖)), where δ′² = δ² − |ωᵀ(p − q)|².
- [ ] UR5 analytic IK (Keating): 2 (shoulder) × 2 (wrist) × 2 (elbow) = **8 solutions**. Choose among them by joint limits, closeness to the current q, and manipulability.
- [ ] **Newton–Raphson IK** (MR §6.2): θ ← θ + J_b†(θ) V_b, where [V_b] = log(T_sb(θ)⁻¹ T_sd). Iterate until ‖ω_b‖ < ε_ω and ‖v_b‖ < ε_v.
- [ ] **Resolved-rate (JHU Lab 3):** q_{k+1} = q_k − K T_step J_b(q_k)⁻¹ ξ_k, with ξ_k = log(g_d⁻¹ g_st(q_k))^∨.
- [ ] **Jacobian transpose / gradient:** q_{k+1} = q_k − K T_step J_bᵀ ξ_k. This is gradient descent on ½‖ξ‖², so it is robust near singularities but slow.
- [ ] **Damped least squares:** Δq = Jᵀ(JJᵀ + λ²I)⁻¹ e. Redundant arms: q̇ = J†V + (I − J†J) q̇₀.

**Dynamics**
- [ ] Lagrangian L = T − V, giving d/dt ∂L/∂θ̇ − ∂L/∂θ = τ.
  - Kinetic energy T = ½ θ̇ᵀ M(θ) θ̇, with M(θ) = Σᵢ Jᵢᵀ ℳᵢ Jᵢ (link body Jacobians and generalized inertias, MLS §4.3).
- [ ] **M(θ)θ̈ + C(θ, θ̇)θ̇ + N(θ, θ̇) = τ.** MR writes M θ̈ + c(θ, θ̇) + g(θ) = τ.
  - Christoffel: C_ij = ½ Σ_k (∂M_ij/∂θ_k + ∂M_ik/∂θ_j − ∂M_kj/∂θ_i) θ̇_k.
  - Properties: M = Mᵀ > 0; **Ṁ − 2C is skew-symmetric**.
- [ ] Be able to derive the two-link planar arm by hand (MLS §4.2.3).
- [ ] Newton–Euler recursive inverse dynamics (MR §8.3), as an awareness topic.

**Control**
- [ ] Lyapunov stability, LaSalle (MLS §4.4).
- [ ] **PD + gravity compensation (set-point):** τ = −K_v θ̇ − K_p(θ − θ_d) + N(θ). Asymptotically stable, proved with V = ½θ̇ᵀMθ̇ + ½eᵀK_p e plus LaSalle.
- [ ] **Computed torque:** τ = M(θ)(θ̈_d − K_v ė − K_p e) + C θ̇ + N. This gives ë + K_v ė + K_p e = 0, which is exponentially stable for K_p, K_v > 0.
- [ ] PD + feedforward, and workspace (task-space) control (MLS §4.5.4; MR §11.4.3).
- [ ] Trajectory generation (MR Ch. 9): cubic and quintic time scaling, trapezoidal profiles, straight lines in joint space vs. SE(3) (screw vs. decoupled).

---

## 7. Difficulty and student reviews

**Reviews**
- **Semester.ly** gives an average rating of about **4.36/5** under Cowan (from search snippet) [8].
  - Praise: lectures and course material.
  - Main complaints: **heavy assignment load** and **slow feedback**.
  - Advice: background in **ROS, C++ and linear algebra helps**.
- I found no Reddit threads specific to this course.

**What drives the difficulty** (inferred from the materials):
- The math is proof-heavy, Lie-group style (MLS), not cookbook.
- The lab stack (Linux + ROS 2 + MATLAB + real hardware) has setup friction.
- Lab 0 notes that VMs work only for simulation, not for hardware, and that WSL and Docker are unsupported.
- The final project is team-based, with a live demo that must be robust to TA-chosen poses.

**Practical tips**
1. **Install Ubuntu 24.04 natively (or dual-boot) before term**, then install ROS 2 Jazzy, `rdkdc_jazzy` and MATLAB with the ROS and Robotics System Toolboxes. Fall 2026 is MATLAB-only, so practise MATLAB even if you prefer Python.
2. Learn **one twist ordering** and write a unit test for it (§3 trap).
3. **Test-driven lab code:**
   - check every `exp` against `expm`;
   - check every log by round-tripping;
   - check every Jacobian by central differences (Lab 3 grades exactly this).
4. Keep `pause()` after `move_joints`. The robot replaces targets rather than queueing them [10].
5. Handle singularities explicitly: RR control must abort with −1.
6. Name files exactly as specified (`ROTZ.m`, not `rotz.m`) and keep the submission folder flat. Gradescope deducts points for structure [10].
7. Do not post course handouts publicly. They are marked "not to be posted, shared or distributed" [10].
8. GenAI is not allowed on labs unless stated.
9. Avoid pairing with ASBR (EN.601.663) in the same term unless the current manual says otherwise [12][13].

---

## 8. Best free resources

| Resource | Why | URL |
|---|---|---|
| MLS free PDF (Murray) | Primary text, Ch. 2–4 | https://www.cds.caltech.edu/~murray/books/MLS/pdf/mls94-complete.pdf (wiki: https://www.cds.caltech.edu/~murray/mlswiki/) |
| MLS open access (CC BY) | Alternative copy | https://library.oapen.org/handle/20.500.12657/112144 |
| Modern Robotics book (free preprint) | Second text, clearest exposition | https://hades.mech.northwestern.edu/index.php/Modern_Robotics · PDF https://hades.mech.northwestern.edu/images/2/25/MR-v2.pdf |
| MR video lectures (Lynch, ~5–10 min each) | Fastest way through Ch. 2–6, 8, 9, 11 | https://modernrobotics.northwestern.edu · Coursera "Modern Robotics" specialization, courses 1–4 (audit) https://www.coursera.org/specializations/modernrobotics |
| MR code library (Python/MATLAB/Mathematica) | `MatrixExp6`, `MatrixLog6`, `Adjoint`, `FKinSpace/Body`, `JacobianSpace/Body`, `IKinBody/Space`, `MassMatrix`, `InverseDynamics`, `ComputedTorque`, `CubicTimeScaling`… (`pip install modern_robotics`) | https://github.com/NxRLab/ModernRobotics |
| `mr_urdf_loader` | Builds M, Slist, Mlist, Glist from a URDF (UR5 + PyBullet demo) | https://github.com/tjdalsckd/mr_urdf_loader |
| Peter Corke Robotics Toolbox for Python | `rtb.models.DH.UR5()` (standard DH), `ETS`, URDF models, Swift/PyPlot visualization; good for checking your DH | https://github.com/petercorke/robotics-toolbox-python |
| Corke Robot Academy: DH lessons | Short, visual DH explanation | https://robotacademy.net.au/lesson/denavit-hartenberg-notation/ |
| Berkeley EECS C106A | Also built on MLS; lecture notes and labs (UR/Sawyer/Baxter) map well | https://pages.github.berkeley.edu/EECS-106/fa24-site/ |
| Northwestern ME 449 | MR-based course with assignments (e.g. UR5 IK/dynamics) | https://hades.mech.northwestern.edu/index.php/ME_449_Robotic_Manipulation_(Archive_Fall_2016) |
| Keating, *UR5 Inverse Kinematics* (JHU 530.646 handout) | **The DH/IK source used in RDKDC** | https://www.slideshare.net/slideshow/ur5-ik/74812361 ; copy in https://github.com/yorgoon/ur5_Final_Project (`UR5_Inverse_Kinematics.pdf`) |
| Hawkins, *Analytic IK for UR-5/UR-10* | Second derivation of UR IK | https://repository.gatech.edu/entities/publication/5fe1645c-bf7d-49ac-8c0e-6516603091eb |
| Universal Robots DH parameters | Official UR3/5/10/e-series tables | https://www.universal-robots.com/articles/ur/application-installation/dh-parameters-for-calculations-of-kinematics-and-dynamics/ |
| Spong-style DH chapter (Duke mirror) | Standard-DH chapter with exercises (appears to be a chapter of Spong et al.; **unverified**) | https://users.cs.duke.edu/~brd/Teaching/Bio/asmb/current/Papers/chap3-forward-kinematics.pdf |
| Course ROS 2 sim package (public) | Run the actual RDKDC RViz sim before class | https://github.com/Warrior-hound/rdkdc_jazzy |
| Past student code (study, don't copy) | Lab 1/Lab 3/final project structure | https://github.com/jqyang3108/Robot-Devices-Kinematics-Dynamics-and-Control-RDKDC · https://github.com/vai-bhav-m/rdkdc_s25_project · https://github.com/ojas200/rdkdc_lab_2025 · https://github.com/aabhasjain27/RDKDCFinalProject |
| Past project write-ups | What an A project looks like | https://kinjmshah.github.io/portfolio/rdkdc · https://seyi-roboticist.github.io/projects/ur5-place-and-draw · https://david-andrew.github.io/projects/drawbot |

Paid but standard references (no free PDF):
- Spong, Hutchinson & Vidyasagar, *Robot Modeling and Control*, Ch. 3 (standard DH) and Ch. 6–8 (dynamics and control).
- Craig, *Introduction to Robotics*, Ch. 3 (modified DH).
- Siciliano et al., *Robotics: Modelling, Planning and Control*.

---

## 9. Prerequisite gaps to fill

| Area | Minimum needed | Quick fix |
|---|---|---|
| Linear algebra | Matrix products and inverses, orthogonal matrices, determinants, eigen-decomposition, **SVD** (manipulability, pseudoinverse, DLS), rank and null space (singularities, redundancy), skew-symmetric matrices, cross product as a matrix | 3Blue1Brown *Essence of Linear Algebra*; Strang 18.06 lectures on SVD and pseudoinverse |
| Matrix exponential / ODEs | e^{At} as the solution of ẋ = Ax, power series, linear ODE stability (eigenvalues of ë + K_v ė + K_p e = 0), phase portraits | MLS Ch. 2 derives e^{ω̂θ} from ṗ = ω × p; any ODE text, 2nd-order linear systems |
| Multivariable calculus | Partial derivatives and chain rule (Jacobians, Lagrange's equations), gradients | — |
| Classical mechanics | Kinetic and potential energy, inertia tensor, parallel-axis theorem, Lagrange's equations for 1–2-DOF systems | MLS §4.2; MR §8.1.1 |
| Control | Feedback, PD control, Lyapunov idea | MR Ch. 11 videos; MLS §4.4 |
| Tools | Linux shell, ROS 2 basics (launch, topics, TF, RViz), MATLAB matrix syntax, git | ROS 2 Jazzy tutorials; MATLAB Onramp (free) |

---

## 10. Build-first project sequence (do in order; you write all code)

**Setup**
- Python 3 with `numpy`, `scipy`, `matplotlib`, `sympy` and `modern_robotics` (`roboticstoolbox-python` is optional).
- Code everything yourself first. Use the MR library **only as a test oracle**.
- Each project is ~6–8 h: **~1.5 h theory** (MR videos + skim the MLS sections), **~5 h build**, **~0.5 h recall** (write the project's equations from memory on one page).

**Suggested layout:** `rdkdc_selfstudy/{so3.py, se3.py, ur5.py, jacobian.py, ik.py, dyn.py, tests/}`.
Write pytest tests as you go: they become your Lab 1/Lab 3 tests later.

### Project 1: SO(3)/SE(3) toolkit (= JHU Lab 1 in Python)
- **Watch:** MR Ch. 3 videos (3.1–3.3.3). **Skim:** MLS §2.1–2.3.
- **Build:**
  - `rotx/roty/rotz`, `eulerxyz` and `eulerxyz_inv`. Detect pitch = ±π/2 and warn, and show that `eulerxyz_inv(eulerxyz([4π,0,0])) ≠ [4π,0,0]`.
  - `hat3/vee3`, `exp_so3` (Rodrigues, handling θ = 0), `log_so3` (handling θ = 0 and θ = π).
  - `hat6/vee6`, `exp_se3`, `log_se3`, `finv` (structured inverse), `adjoint`.
- **Verify:**
  - 1000 random round trips: `exp(log(g)) == g`.
  - Compare against `mr.MatrixExp6`/`MatrixLog6`, **remembering the ordering swap**.
  - Check `adjoint(g) @ xi == vee(g @ hat(xi) @ inv(g))`.
- **Visualize:** a matplotlib 3-D `plot_frame(g)`. Animate e^{ξ̂θ} for θ ∈ [0, 2π] for a screw with pitch 0.1 and watch the helix.

### Project 2: UR5 forward kinematics, DH vs. PoE
- **Watch:** MR Ch. 4 videos and MR Appendix C. **Read:** MLS §3.2, Keating handout p.1–2 (DH table), and a DH explainer (Corke).
- **Build:**
  - `fk_dh(q)` from the Keating/UR table (standard DH).
  - `fk_poe(q)` using twists derived at q = 0 (procedure in §4.4).
  - Then a second PoE set in the **course S/T frames**: the MR UR5 example in §4.1.2, with the arm along +x, which matches Cowan-era code [14].
  - Find the constant g_S0 and g_6T such that `fk_poe_course(q) == g_S0 @ fk_dh(q) @ g_6T`. This is exactly the final-project bridge.
- **Verify:** max |Δ| < 1e-12 over 100 random q. Optionally compare with `rtb.models.DH.UR5().fkine(q)` and note its d1 typo.
- Starter check, which I ran (prints max error ≈ 4e-16):
  ```python
  import numpy as np
  a=[0,-0.425,-0.39225,0,0,0]; d=[0.089159,0,0,0.10915,0.09465,0.0823]
  al=[np.pi/2,0,0,np.pi/2,-np.pi/2,0]
  def A(a,al,d,th):
      ct,st,ca,sa=np.cos(th),np.sin(th),np.cos(al),np.sin(al)
      return np.array([[ct,-st*ca,st*sa,a*ct],[st,ct*ca,-ct*sa,a*st],[0,sa,ca,d],[0,0,0,1]])
  # twists at q=0: w_i = z-axis of T_0^{i-1}(0), q_i = its origin, xi_i = (-w x q, w); M = T_0^6(0)
  T=np.eye(4); xis=[]
  for i in range(6):
      w,p=T[:3,2],T[:3,3]; xis.append(np.r_[-np.cross(w,p),w]); T=T@A(a[i],al[i],d[i],0)
  M=T  # then g(q) = prod exp(hat(xi_i) q_i) @ M  must equal prod A_i(q_i)
  ```
- **Stretch:** install ROS 2 Jazzy, clone `rdkdc_jazzy`, run `ros2 launch rdkdc ur5_simulation.launch.py`, and compare your FK with TF `base_link → tool0`.

### Project 3: Jacobians, singularities, manipulability (= JHU Lab 3, part 1)
- **Watch:** MR Ch. 5 videos. **Read:** MLS §3.4.
- **Build:**
  - `jacobian_space(q)` and `jacobian_body(q)` via adjoints (both MLS formulas); check `J_s == Ad(g) @ J_b`.
  - `jacobian_body_fd(q)` by central differences: column i = vee(skew-part(g⁻¹ ∂g/∂qᵢ)). Report ‖J_fd − J_b‖.
  - `manipulability(J, 'sigmamin'|'detjac'|'invcond')`.
- **Experiments:**
  1. Plot the three measures for θ3 ∈ [−π/4, π/4] with the other joints fixed. All three dip at θ3 = 0.
  2. Find the wrist singularity (θ5 = 0) and the shoulder singularity numerically by plotting σ_min.
  3. Draw the linear-velocity manipulability ellipsoid at a few poses.

### Project 4: Inverse kinematics + resolved-rate control (= JHU Lab 3, part 2 + final project, in sim)
- **Watch:** MR Ch. 6 videos. **Read:** MLS §3.3 (Paden–Kahan) and Keating IK p.2–end.
- **Build:**
  - Paden–Kahan SP1 and SP3, with unit tests on random rotations.
  - `ur5_ik_analytic(g)`: port Keating, return a 6×8 array, and verify `fk(ik(g)[:,k]) == g` for all valid k.
  - `ik_newton(g_d, q0)` (MR §6.2).
  - A simulated controller loop `rr_control(g_d, q0, K, T_step, mode in {'inv','transpose','dls'})` that integrates q and logs ‖v‖, ‖ω‖ and σ_min. It must **abort near singularity** and return the final error in cm, matching the Lab 3 spec.
- **Mini final project (sim):**
  1. "Teach" two poses, given as matrices.
  2. Draw a straight 10 cm line with the pen tip (a constant g_6T offset) using all three methods.
  3. Plot the tool path, joint trajectories and error vs. iteration.
  4. Compare convergence speed and singularity behaviour of J⁻¹, Jᵀ and DLS.

### Project 5: Dynamics and control
- **Watch:** MR Ch. 8.1 and 11.4 videos (Ch. 9 time-scaling if time allows). **Read:** MLS §4.2–4.5.
- **Build:**
  - With **SymPy**, derive the 2-link planar arm: T, V, M(θ), C(θ, θ̇) via Christoffel symbols, and g(θ). Check numerically that **Ṁ − 2C is skew-symmetric**.
  - Simulate with RK4 or `scipy.integrate.solve_ivp` and compare four controllers on a step and a sinusoidal reference:
    1. pure PD;
    2. PD + gravity compensation;
    3. computed torque;
    4. computed torque with a 20 % mass error (robustness).
  - Plot tracking error and torque.
- **Bridge to the UR5:** use `mr.MassMatrix`, `mr.InverseDynamics` and `mr.ComputedTorque` with UR5 inertial parameters (from `mr_urdf_loader` or the MR Coursera capstone files) and confirm your 2-link code against `mr.InverseDynamics` on a 2-link model.
- **Close-out:** cubic and quintic time scaling (MR Ch. 9) to feed smooth trajectories to Project 4's controllers.

**Spaced review** (15–30 min each, no new material):
- Redo the §6 equations from memory 2, 7 and 14 days after finishing.
- Before term: (re)install Ubuntu 24.04 + ROS 2 Jazzy + MATLAB (with the ROS and Robotics System Toolboxes), and port the Project 1 functions to MATLAB with the exact Lab 1 names (`ROTX`, `EULERXYZINV`, `SKEW3`, `EXPCR`, `SKEW6`, `EXPCF`, `FINV`).

---

## 11. Sources

**Primary / first-hand** (public GitHub repos, read via `git clone`):
- [10] Fall 2026 student lab repo with instructor Lab 0 and Lab 1 PDFs (Kim): https://github.com/Vncntmhlr/Robot-Devices-Kinematics-Dynamics-and-Control-Labs
- [11] Fall 2025 setup materials (`main.tex` Lab 0, `Lab1.pdf`, Docker templates): https://github.com/Warrior-hound/RDKDC
- [28] Course ROS 2 Jazzy package `rdkdc`: https://github.com/Warrior-hound/rdkdc_jazzy
- [14] 2022 student repo (PoE FK, body Jacobian, `getXi`, RR control, `DH.m`, course `ur5InvKin.m`): https://github.com/jqyang3108/Robot-Devices-Kinematics-Dynamics-and-Control-RDKDC
- [14b] Final project handout (Cowan, 2017) and Keating *UR5 Inverse Kinematics* (2014/2016) PDFs: https://github.com/yorgoon/ur5_Final_Project
- [16] Later student repos (Lab 3 spec paraphrase, DH test files, place-and-draw projects): https://github.com/ojas200/rdkdc_lab_2025 · https://github.com/vai-bhav-m/rdkdc_s25_project · https://github.com/Shibaken0618/RDKDC_final_project · https://github.com/YunpuZhang/RDKDC · https://github.com/aabhasjain27/RDKDCFinalProject · https://github.com/N-racer13/RDKDC · https://github.com/daisy43097/RDKDCFinalProject
- [19] Corke Robotics Toolbox UR5 DH model: https://github.com/petercorke/robotics-toolbox-python (`src/roboticstoolbox/models/DH/UR5.py`)
- [24] MR code library: https://github.com/NxRLab/ModernRobotics

**Via search snippets** (pages not directly readable from this environment):
- [1] JHU catalog, EN.530 courses: https://e-catalogue.jhu.edu/course-descriptions/mechanical_engineering/
- [2] Robotics MSE advising manuals: https://lcsr.jhu.edu/wp-content/uploads/2026/03/2026-2027-Robotics-Advising-Manual.pdf · https://lcsr.jhu.edu/wp-content/uploads/2026/07/2026-2027-Robotics-Advising-Manual-July.6.2026-UPDATE.pdf
- [3] Cowan CV: https://limbs.lcsr.jhu.edu/wp-content/uploads/2014/07/cowan_cv.pdf
- [4] Cowan courses page: https://limbs.lcsr.jhu.edu/people/cowan/courses/
- [5] ME graduate course schedules: https://me.jhu.edu/education/graduate-studies/course-schedules/
- [6] Coursicle: https://www.coursicle.com/jhu/courses/EN530/646/
- [7] TA teaching page: https://ruiruihuangannie.github.io/teaching/me-rdkdc
- [8] Semester.ly: https://jhu.semester.ly/c/EN.530.646 · https://jhu.semester.ly/course/EN.530.646/Fall/2023
- [9] JHU public syllabus (HelioCampus): https://assessment.jhu.edu/index.cfm/page/AefisCourse.publicSyllabus?courseid=11499
- [12] ME MSE advising manuals: https://me.jhu.edu/wp-content/uploads/2026/06/2026-27-MSE-Graduate-Advising-Manual.pdf · https://me.jhu.edu/wp-content/uploads/2024/07/2024-25-MSE-Graduate-Advising-Manual.pdf
- [13] ME MSE tracks: https://me.jhu.edu/education/graduate-studies/masters-program-original/mse-mechanical-engineering-tracks/
- [15] Keating UR5 IK on SlideShare: https://www.slideshare.net/slideshow/ur5-ik/74812361
- [17] Hawkins UR IK report: https://repository.gatech.edu/entities/publication/5fe1645c-bf7d-49ac-8c0e-6516603091eb
- [18] Universal Robots DH parameters: https://www.universal-robots.com/articles/ur/application-installation/dh-parameters-for-calculations-of-kinematics-and-dynamics/
- [20] MLS PDF (Murray): https://www.cds.caltech.edu/~murray/books/MLS/pdf/mls94-complete.pdf
- [21] MLS open access: https://library.oapen.org/handle/20.500.12657/112144 · https://www.taylorfrancis.com/books/oa-mono/10.1201/9781315136370/
- [22] Modern Robotics wiki and PDF: https://hades.mech.northwestern.edu/index.php/Modern_Robotics · https://hades.mech.northwestern.edu/images/2/25/MR-v2.pdf
- [23] MR Coursera: https://www.coursera.org/specializations/modernrobotics
- [25] PoE vs. DH overview: https://en.wikipedia.org/wiki/Product_of_exponentials_formula
- [26] MR Appendix C (Cambridge): https://www.cambridge.org/core/books/modern-robotics/denavithartenberg-parameters/149F3F9C7F864415D590AA6AECFF9AFA
- [27] Course Hero listing (restricted): https://www.coursehero.com/sitemap/schools/1030-Johns-Hopkins-University/courses/25150095-EN535RDKDC/
- [29] LCSR GitLab lab-repo forks (Keating): https://git.lcsr.jhu.edu/rkeatin3/me530646labs/-/forks
- [30] Final project reports: https://ckocabalkanli.github.io/Can/links/RDKDC.pdf · https://david-andrew.github.io/docs/drawbot/final_report.pdf · https://stevenleon99.github.io//files/rdkdc/rdkdcfinalproject.pdf
- [31] Place-and-draw write-ups: https://kinjmshah.github.io/portfolio/rdkdc · https://seyi-roboticist.github.io/projects/ur5-place-and-draw
- Berkeley EECS C106A (MLS-based): https://pages.github.berkeley.edu/EECS-106/fa24-site/
- Corke Robot Academy DH: https://robotacademy.net.au/lesson/denavit-hartenberg-notation/
