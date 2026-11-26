# 05 — Algorithms for Sensor-Based Robotics (EN.601.463 / EN.601.663)

> **Verified 2026-11-26** against the student's uploaded copy of the official CIRL course page: description, credits, instructor, meetings, lab (Wyman 170, Ubuntu 24.04 + ROS 2 Humble), **primary book = Choset *Principles of Robot Motion***, other resources (Thrun *Probabilistic Robotics*, LaValle), prerequisites (linear algebra, calculus, **C++ and MATLAB**), grading (47/25/25/3), Fall 2026 dates, weekly schedule & readings, and policies (no teamwork, no online/AI tools → F) all confirmed. Slide decks and textbooks are being distilled into `library/`.
>
> **Status of this file:** research compiled 2026-11-26. Everything below is either cited to a URL or
> marked **(unverified)**. During the research session the course hosts (`cirl.lcsr.jhu.edu`,
> `cs.jhu.edu`, `lcsr.jhu.edu`, `jhu.semester.ly`, `web.archive.org`) were **blocked by the sandbox
> network proxy**, so page content was reconstructed from search-engine extracts of those pages plus
> **primary evidence from public student GitHub repos** (which were cloned and read). Open the CIRL course
> page yourself once and diff it against Section 2. Week numbers and readings can shift between terms.

---

## 1. Course identity

| Field | Value | Source |
|---|---|---|
| Numbers | **EN.601.463** (undergrad) / **EN.601.663** (grad, the one MSE students take). Old numbers: **600.436 / 600.636**, older still **CS 336** (Hager era). | cs.jhu.edu course listings; Hager slides header "600.436/600.636 G.D. Hager S. Leonard" |
| Credits | 3 | CIRL course page |
| Instructor (current) | **Simon Leonard** (LCSR / CS). Listed for Spring 2023, Spring 2025, Spring 2026 and Fall 2026 offerings. | cs.jhu.edu/spring26, cs.jhu.edu/fall26, semester.ly |
| Instructor (historical) | **Gregory D. Hager** taught it as CS 336 / 600.436; slides from the transition years are co-branded "G.D. Hager, S. Leonard". | cs.jhu.edu/~hager/Teaching/cs336/…, cs.jhu.edu/~sleonard/lecture03.pdf |
| Offered | It appears to run **both semesters**: Fall 2025 (student repo dated Nov 2025), Spring 2026 (CS listing, midterm reported as Mar 5 2026), Fall 2026 (course page dates Sep–Dec 2026). | see Sections 4 and 10 |
| Meetings | Tue/Thu 12:00–1:15 pm, Hodson 210. The Spring 2026 listing also shows a Monday 4:30–7 pm section. | CIRL page; cs.jhu.edu/spring26 |
| Lab | **Wyman 170**, 12 desktops with **Ubuntu 24.04 + ROS 2 "Humble"**, as the page states. Humble officially targets Ubuntu 22.04, so the distro may actually be Jazzy **(unverified)**. | CIRL page (via search extract) |
| Role in MSE Robotics | **One of the 2 required MSE core courses**, together with **EN.530.646 RDKDC**. Dual-listed courses must be taken at the 600 level, so take **601.663**. | LCSR Robotics MSE advising manuals 2023-24, 2025-26 |
| Prereqs stated | "Rudimentary understanding of linear algebra, calculus, and the ability to program in **C++ and Matlab**." The CS listing says: Data Structures (601.226), linear algebra, calculus (Calc III), probability/statistics. | CIRL page; cs.jhu.edu/spring26, fall26 |
| Description | "Surveys the development of robotic systems for navigating in an environment from an algorithmic perspective. It will cover basic kinematics, configuration space concepts, motion planning, and localization and mapping. It will describe these concepts in the context of the **ROS** software system, and will present examples relevant to mobile platforms, manipulation, robotics surgery, and human-machine systems." | CIRL page / JHU catalog |
| Grading | **5 assignments 47%**, **midterm 25%**, **final 25%**, **demonstrations 3%**. "All students will need to use **real robots** by the end of the term to earn their final mark." | CIRL page |
| AI policy | Online tools "(AI-based or otherwise) such as ChatGPT or Copilot are **not permitted**". Misconduct is reported and "at least sanctioned by an 'F' course grade." | CIRL page |

### Where the lecture material lives (exact URLs)

The material is on the **CIRL** site (Computational Interaction and Robotics Lab, Hager's lab), not the
CIIS wiki. `ciis.lcsr.jhu.edu` (DokuWiki) hosts **CIS I/II** (Taylor). No `doku.php` page for 463/663 was
found by any search.

- Course home page (schedule, policies, dates): **https://cirl.lcsr.jhu.edu/sensorbasedrobotics/**
  (also reachable as `https://cirl.lcsr.jhu.edu/SensorBasedRobotics`)
- Archived 2013 schedule (Hager era, includes bug algorithms and hand-eye calibration):
  **https://cirl.lcsr.jhu.edu/sensorbasedrobotics/sensor_course_schedule_2013**
- Lecture PDFs hosted on Simon Leonard's CS home directory (indexed by search engines):
  - https://www.cs.jhu.edu/~sleonard/lecture03.pdf — *Hand-Eye Calibration* (600.436/636, Hager & Leonard)
  - https://www.cs.jhu.edu/~sleonard/week04.pdf — *Collision Detection* / C-space (600.436/636)
  - https://www.cs.jhu.edu/~sleonard/week06.pdf — *Sampling-Based Motion Planning* (dated 10/5/2023; PRM, RRT, EST; slides from LaValle and Plaku)
  - https://www.cs.jhu.edu/~sleonard/week08.pdf — *Kalman Filters for Mapping and Localization* (600.436/636, includes the sensing/world-model intro: wheel slip, wheel diameter, steering alignment)
  - https://www.cs.jhu.edu/~sleonard/Chap8-Kalman-Mapping.pdf — Kalman filters for mapping and localization (CS 336 version, derived from Choset's CMU 16-735 Ch. 8 slides; EKF range-bearing localization, data association)
  - https://www.cs.jhu.edu/~sleonard/cs436/roadmaps.pdf — *RoadMap Methods* (2015; visibility graph, sweep-line, trapezoidal and Morse decompositions)
  - https://www.cs.jhu.edu/~hager/Teaching/cs336/Notes/W4-ConfigSpace.pdf — Hager's *Configuration Space* notes (slides credited to Choset, Dodds, Mocha; includes manipulator Jacobian)
  - Week-1 intro deck (user upload, unofficial): https://www.scribd.com/document/424930347/lecture01-pdf
- Instructor's course robot description (UR5e + Robotiq 2F-85 + camera, ROS 2):
  **https://github.com/simonleonard/asbr_description** ("ASBR robot description for the UR5e with gripper and camera")

> The pattern `week0N.pdf` suggests more decks exist (week01, week02, week05, week07, week09–12) at
> `https://www.cs.jhu.edu/~sleonard/weekNN.pdf`. Search engines did not index them, so these URLs are **(unverified)**.
> Try them directly in a browser.

### Name collisions (avoid studying the wrong course)
- **UT Austin ME 397 "Algorithms for Sensor-Based Robotics"** (screw theory, PoE, manipulability,
  pivot calibration). Many GitHub repos called "ASBR" belong to it, e.g. `swanbeck/asbr_toolbox`, which
  says so in its README. `Maxim-Gurevich/Algorithms-for-Sensor-Based-Robotics` contains CIS-style pivot
  calibration data, which suggests the same course **(unverified)**.
- **CS 336/436 "Algorithms for Sensor-based Robotics"** decks by E. Plaku (Catholic U.) show up in
  searches (e.g. people.csail.mit.edu/jbarry/.../Lec_CSpace.pdf). The content overlaps but it is a different course.
- **NYU ECE-GY 5223 "Sensor Based Robotics"** is unrelated.

---

## 2. Lecture-by-lecture syllabus (reconstructed)

Primary source: CIRL course page schedule (Fall 2026 version, via search extracts). Slide links come
from the PDFs above. The mapping of decks to weeks is my inference from deck titles and dates.

| Wk | Topic (as on CIRL page) | Content details | Suggested reading (CIRL page) | Slides |
|---|---|---|---|---|
| 1 | **Introduction** | Two core problems: motion planning (collision-free trajectories) and localization & mapping. Emphasis on algorithms, analysis, and implementation on real sensor data. | Choset et al. *Principles of Robot Motion* (PoRM) Ch. 3; Murray-Li-Sastry (MLS) Ch. 2; Siciliano & Khatib *Springer Handbook of Robotics* Ch. 1 | Scribd lecture01 (unofficial) |
| 2 | **Rigid body motion and velocity** | "Linear algebra used to describe relative 3D motion and velocity between rigid bodies": SO(3), SE(3), homogeneous transforms, exponential coordinates, twists, adjoint, (spatial/body) Jacobian. This feeds **Assignment 1**. | Search extracts attach PoRM Ch. 7 / LaValle Ch. 5 here, which look mis-attributed by the extractor **(unverified)**. Use MLS Ch. 2–3 or Modern Robotics Ch. 3–5. | — (try `week02.pdf`) |
| 3 | **Hand-eye calibration** | Eye-in-hand vs eye-to-hand frames; AX = XB; Lie-group (log map) solution; least squares over N motions. Feeds **Assignment 2**. | **Park & Martin, "Robot Sensor Calibration: Solving AX=XB on the Euclidean Group," IEEE T-RA 10(5):717–721, 1994** | `lecture03.pdf` |
| 4 | **Potential fields** | Attractive and repulsive potentials; "implementation of sensor constraints as potentials"; gradient descent; local minima. Older terms put C-space and collision detection around here. | PoRM Ch. 4 (my mapping; the page line is empty) | `week04.pdf` (Collision Detection / C-space), Hager `W4-ConfigSpace.pdf` |
| 5 | **Graph-based path planning** | Roadmaps: visibility graph, sweep-line, trapezoidal / Morse cell decomposition, (GVD); graph search (Dijkstra / A*). | PoRM (Ch. 5–6); de Berg et al. *Computational Geometry* | `cs436/roadmaps.pdf` |
| 6 | **Sample-based path planning** | PRM (Kavraki 1996), RRT, **EST**, single- vs multi-query, local planner, collision checking along edges. Feeds **Assignment 3**. | PoRM **Ch. 7**; LaValle *Planning Algorithms* **Ch. 5** | `week06.pdf` (10/5/2023) |
| 7 | **Review + Midterm** | Midterm Fall 2026: **Oct 20, 2026**. Spring 2026: reported **Mar 5, 2026**. | — | — |
| 8 | **Kalman filter** (dynamic linear systems) | Sensing and world models, odometry errors, KF predict/update. | PoRM Ch. 8 (my mapping); *Probabilistic Robotics* (PR) Ch. 3 | `week08.pdf`, `Chap8-Kalman-Mapping.pdf` |
| 9 | **Extended Kalman filter** (non-linear) | Linearization, Jacobians, EKF localization (range-bearing, GPS/IMU). Feeds **Assignment 4**. | PR Ch. 3.3, 7.4 (my mapping) | `Chap8-Kalman-Mapping.pdf` |
| 10–12 | **Bayesian filtering, multiple hypotheses, particle filter** | Bayes filter, histogram/grid localization, multi-hypothesis tracking, Monte Carlo localization, beam and likelihood-field sensor models. Feeds **Assignment 5**. | PoRM Ch. 9; PR Ch. 2, 4, 6, 8 (my mapping) | — (try `week10.pdf`…) |
| 13 | **Review** | — | — | — |
| — | **Final exam** | Fall 2026: **Dec 22, 2026** | — | — |

**Topics in your expected list that the current schedule does *not* visibly cover** (treat them as
optional or bridge material, likely covered in CIS I or Computer Vision instead): quaternion-heavy
math beyond the transforms you need, **ICP / point registration** (core in CIS I), **SLAM (EKF-SLAM,
graph SLAM), occupancy-grid mapping** (may appear briefly in the weeks 10–12 slides; Hager's Chap8 deck
covers mapping), **UKF**, **camera calibration** (Computer Vision), **optimization-based planning**
(not listed). **Bug algorithms** appear in the 2013 schedule but not in the current one.

---

## 3. Textbook and resource mapping per topic

Abbreviations: **PoRM** = Choset, Lynch, Hutchinson, Kantor, Burgard, Kavraki, Thrun, *Principles of Robot
Motion* (MIT Press 2005, the course's primary text). **PR** = Thrun, Burgard, Fox, *Probabilistic Robotics*
(2005). **LaV** = LaValle, *Planning Algorithms* (free). **MR** = Lynch & Park, *Modern Robotics* (free
preprint). **MLS** = Murray, Li, Sastry (free PDF). **Sic** = Siciliano et al., *Robotics: Modelling,
Planning and Control* / Springer Handbook.

| Topic | PoRM | PR | LaValle | Modern Robotics | Other |
|---|---|---|---|---|---|
| Rigid transforms, SO(3)/SE(3), twists, Jacobian | App. (+Ch. 3.5 C-space of rigid bodies) | — | Ch. 3.2–3.3, 4.2 | **Ch. 3, 4, 5** | **MLS Ch. 2–3**; Sic Ch. 2–3 |
| Quaternions | App. | — | Ch. 4.2.2 | Ch. 3 (brief) | Solà, *Quaternion kinematics for the error-state KF* (arXiv 1711.02508) |
| Hand-eye AX=XB | — | — | — | — | Park & Martin 1994; Tsai & Lenz 1989; Horaud & Dornaika 1995; Strobl & Hirzinger 2006 |
| Configuration space, Minkowski sums | **Ch. 3** | — | **Ch. 4**, 4.3 | **Ch. 10.1–10.2** | de Berg Ch. 13 |
| Bug algorithms | **Ch. 2** | — | Ch. 12.3 | — | — |
| Potential functions, navigation functions, wavefront | **Ch. 4** | — | Ch. 8.4 | **Ch. 10.6** | Khatib 1986 |
| Roadmaps (visibility, GVD), cell decomposition | **Ch. 5, 6** | — | **Ch. 6** | Ch. 10.3–10.4 | de Berg Ch. 6, 15 |
| Graph search (Dijkstra, A*) | App. H | — | **Ch. 2** | **Ch. 10.2.4** | Russell & Norvig Ch. 3 |
| Sampling-based (PRM, RRT, EST, RRT*) | **Ch. 7** | — | **Ch. 5** | **Ch. 10.5** | Karaman & Frazzoli 2011 (RRT*) |
| Optimization-based planning | — | — | Ch. 8 (feedback), 14 | Ch. 10.7 (nonlinear opt.) | CHOMP / TrajOpt papers |
| Bayes filter | Ch. 9 | **Ch. 2** | Ch. 11 | — | — |
| KF / EKF / UKF | **Ch. 8** | **Ch. 3** | — | — | Labbe, *Kalman and Bayesian Filters in Python* |
| Particle filter, MCL | **Ch. 9** | **Ch. 4, 8.3** | — | — | — |
| Motion and sensor models (odometry, beam, likelihood field) | Ch. 8–9 | **Ch. 5, 6** (likelihood field §6.4) | — | Ch. 13 (wheeled kinematics) | — |
| Occupancy grids, SLAM (EKF / graph / FastSLAM) | Ch. 8.4, 9 | **Ch. 9, 10, 11, 13** | — | — | Stachniss *Robot Mapping* course |
| Point registration, ICP | — | — | — | — | Arun et al. 1987; Horn 1987; Besl & McKay 1992 (overlap with **CIS I**) |
| Sensors (lidar, cameras, IMU, GPS) | Ch. 8 | Ch. 6 | — | — | Sic Ch. 5 |

---

## 4. Assignments, projects, exams (in detail)

### 4.1 Format (all evidence-backed)
- **5 programming assignments, C++ in ROS 2** (rclcpp, colcon, launch.py, Gazebo via `ros_gz`), plus
  **MATLAB** for the hand-eye solver. Submitted through **GitHub** (dscl.lcsr.jhu.edu lab page, via search
  extract). Real-robot demonstrations count for 3%.
- 2026 due dates: **A1 Sep 24 · A2 Oct 8 · A3 Nov 5 · A4 Nov 19 · A5 Dec 10**. Midterm Oct 20, final Dec 22.
- The robot platform is a **UR5e + Robotiq 2F-85 + camera** (instructor package `asbr_description`) for
  A1–A3, and a **Clearpath Jackal (j100)** in Gazebo for A4 (Fall 2025). The ROS 1 era (2017) used a **Husky**.

### 4.2 Assignment-by-assignment (reconstructed from student repos; the handouts themselves were not found)

**A1: Cartesian trajectory generation (UR5e Jacobian, resolved rate).** Evidence: `amal-araweelo/sbr/assignment1` (2026, header "EN.601.463/663 Assignment #1 Cartesian trajectory generation"). Older ROS 1 version: `iwilkes1/asbr_assignment1` (2015, 3×3 position Jacobian).
- Implement `ForwardKinematics(q1..q6) → E (4×4)`, `ForwardKinematicsInverse`, `AdjointTransformationInverse(E) → Ad (6×6)`, `Jacobian(q) → J (6×6)` (space Jacobian) for the UR5e (constants in the code: 0.425, 0.3922, 0.1333, 0.0997 and 0.2746; the last presumably adds a gripper/tool offset to d6 = 0.0996 **(unverified)**).
- Node subscribes to `JointState` and a `geometry_msgs/Point` set-point. It loops in 1 mm increments: $v_b$ = unit translation × step, $J_b = \mathrm{Ad}_{E^{-1}} J_s$, $\dot q = J_b^{-1}[v_b;0]$, then integrates and publishes `trajectory_msgs/JointTrajectory` on `/ur5e_controller/joint_trajectory`. It warns when $|\det J| < 10^{-9}$ (singularity).
- Skills: PoE/DH FK, adjoint, space vs body Jacobian, resolved-rate control, singularities.

**A2: Hand-eye calibration (AX = XB).** Evidence: `amal-araweelo/sbr/assignment2` (2026); 2017 ROS 1 version used ArUco markers (`ChengdaJi/SensorBasedRobotics/src/sbr/assignment2`).
- Gazebo (ros_gz) scene: UR5e with camera plus a spawned **AR-tag** model. Move the robot (rqt joint trajectory controller) and record N pairs with `ros2 run tf2_ros tf2_echo` of base→hand ($E_{bh}$) and sensor→tag ($E_{sc}$) as rows `[tx ty tz qx qy qz qw]`.
- MATLAB: `generatedata.m` (synthetic test: random SE(3) via a normalized Gaussian quaternion; $E_{sc} = X^{-1}E_{bh}^{-1}E_{bc}$), `axxb_closedform.m` (3 poses → 2 motions; $R_X = [\alpha_1\ \alpha_2\ \alpha_1\times\alpha_2][\beta_1\ \beta_2\ \beta_1\times\beta_2]^{-1}$), `solveRx.m` (**Park–Martin LS**: $M=\sum\beta_i\alpha_i^T$, $R_X=(M^TM)^{-1/2}M^T$), `solveTx.m` (stacked $(I-R_{A_i})t_X = t_{A_i}-R_Xt_{B_i}$), and `axxb.m` (N poses). The "expert challenge" compares the result to ground truth from `tf2_echo gripper_pick camera_link` and reports translation error in mm.
- **Trap noted in the starter code:** ROS quaternions are `[x y z w]` but MATLAB `quat2rotm` wants `[w x y z]`.

**A3: Sampling-based motion planner as a MoveIt 2 planner plugin.** Evidence: `amal-araweelo/sbr/assignment3` (2026, `ASBRContext : planning_interface::PlanningContext`, `asbr_planning.yaml` registers `ASBRPlanner` for group `manipulator`, plus a `bugatti.scene` obstacle scene and FLANN for nearest neighbours).
- 2026 variant = **EST (Expansive Space Trees)**. TODO functions: `select_config_from_tree(w)` (sample a node with probability ∝ weight, where weight decreases with node degree), `sample_nearby(q)` (Gaussian perturbation of all 6 joints), `find_nearest_configuration`, `is_local_path_collision_free(q, q')` (discretized interpolation + `state_collides`), `search_path` (follow parent pointers), and `est(q_init, q_goal)`. That student built a **bidirectional** EST.
- 2017 variant = **PRM** (`make_vertices(N)`, `make_edges(V)`, `search_accessibility`, `search_departability`, graph search). The algorithm changes between terms, so be fluent in **PRM, RRT, RRT-Connect and EST**.

**A4: Extended Kalman filter, GPS + IMU fusion for a mobile robot.** Evidence: `Deck-Wang416/asbr_assignment4` (Fall 2025, ROS 2, Clearpath **Jackal j100**, `worlds/assignment4.sdf` landscape); 2017 ROS 1 version on a Husky (`ChengdaJi/.../assignment4`, "This is a homework about the Extended Kalman Filter").
- State $[x, y, z, \text{roll}, \text{pitch}, \text{yaw}]$. Fill in the process covariance, measurement covariance, the system function (integrate body velocity/angular velocity into position and RPY), the **system Jacobian**, the **GPS observation function** (state → lat/lon/alt via meters-to-degrees scale) and its 3×3 Jacobian, and the **IMU observation** (state → orientation) and its Jacobian. Files: `ekf.cpp`, `ekf_models.cpp`, `system_pdf.cpp`, `measurement_pdf.cpp`, `ekf_node.cpp`.
- Much of the grade-relevant work is **covariance tuning** and getting the Jacobians right (2017 solution used symbolic-generated Jacobians).

**A5: Particle filter / Monte Carlo localization (likelihood-field sensor model).** Evidence: 2017 ROS 1 `cji7-assignment5.tgz` (Husky + SICK LMS laser, `amcl_node.cpp`, `likelihoodfield.cpp`). The 2025/2026 versions were not found, so their content is **(unverified)**.
- Implement $p(z\mid s, m)$ for a laser scan: transform each beam endpoint (laser→robot→world), look up the distance to the nearest obstacle, score with $w_{hit}\,\mathcal N(d;0,\sigma_{hit}^2) + w_{rand}/z_{max}$, multiply over beams. Parameters: `sigma_hit, z_max, w_hit, w_rand`, `laser_pose`.

### 4.3 Exams
- Midterm (week 7) covers rigid motion, hand-eye, potential fields, graph and sampling planners. Final covers everything, weighted to KF/EKF/Bayes/PF. Each exam is 25%.
- No past exams were found publicly. Expect **pen-and-paper derivations** (Jacobians, KF update by hand on 1-D/2-D examples, Minkowski sums, A* traces, PRM/RRT pseudo-code, AX=XB setup) **(unverified, inferred from slide content and the grading split)**.

---

## 5. Core concept checklist with key equations

Tick each item only after you can **derive it on paper and code it in under 30 minutes**.

### 5.1 Rigid-body math
- [ ] $R\in SO(3)$: $R^TR=I$, $\det R=1$. $T=\begin{bmatrix}R&p\\0&1\end{bmatrix}\in SE(3)$, $T^{-1}=\begin{bmatrix}R^T&-R^Tp\\0&1\end{bmatrix}$. Compose frames: $T_{ac}=T_{ab}T_{bc}$.
- [ ] Rodrigues / exp map: $R=e^{[\omega]\theta}=I+\sin\theta[\omega]+(1-\cos\theta)[\omega]^2$, with $\|\omega\|=1$.
- [ ] Log map: $\theta=\arccos\frac{\mathrm{tr}R-1}{2}$, $[\omega]=\frac{R-R^T}{2\sin\theta}$ (handle $\theta\approx0$ and $\theta\approx\pi$). $\log(R)^\vee=\theta\omega$ is the "$\alpha$" in Park–Martin.
- [ ] Quaternion $q=(w,\mathbf v)=(\cos\frac\theta2,\ \sin\frac\theta2\,\omega)$, $\|q\|=1$; $q$ and $-q$ are the same rotation (double cover).
  Product: $q_1\otimes q_2=(w_1w_2-\mathbf v_1\!\cdot\!\mathbf v_2,\ w_1\mathbf v_2+w_2\mathbf v_1+\mathbf v_1\times\mathbf v_2)$. Rotate: $(0,p')=q\otimes(0,p)\otimes q^*$. Ordering: ROS/Eigen-coeffs `[x y z w]`, MATLAB/SciPy-scalar-first differ. SLERP for interpolation.
- [ ] Twist $\mathcal V=(\omega,v)$ (MR ordering). Adjoint $\mathrm{Ad}_T=\begin{bmatrix}R&0\\[p]R&R\end{bmatrix}$. With MLS ordering $(v,\omega)$: $\mathrm{Ad}_g=\begin{bmatrix}R&\hat pR\\0&R\end{bmatrix}$.
- [ ] PoE FK: $T(q)=e^{[S_1]q_1}\cdots e^{[S_n]q_n}M$. Space Jacobian column $J_{s,i}=\mathrm{Ad}_{e^{[S_1]q_1}\cdots e^{[S_{i-1}]q_{i-1}}}S_i$. Body Jacobian $J_b=\mathrm{Ad}_{T^{-1}}J_s$.
- [ ] Resolved rate: $\dot q=J^{-1}\mathcal V$, or $J^\dagger\mathcal V$, or damped LS $J^T(JJ^T+\lambda^2I)^{-1}\mathcal V$. Singularity when $\det J\to0$ (UR5e: wrist, elbow, shoulder singularities).

### 5.2 Calibration and registration
- [ ] **Hand-eye** $AX=XB$, with $A_i$ = relative robot (hand) motion and $B_i$ = relative sensor motion. The convention used in A2: $A_i=E_1^{-1}E_{i+1}$, $B_i=S_1S_{i+1}^{-1}$.
  Rotation: $R_AR_X=R_XR_B$ ⇒ $\alpha_i=R_X\beta_i$ with $\alpha=\log(R_A)^\vee$, $\beta=\log(R_B)^\vee$. Translation: $(R_A-I)\,t_X=R_Xt_B-t_A$.
- [ ] **Park–Martin (1994)** least squares: $M=\sum_i\beta_i\alpha_i^T$, $R_X=(M^TM)^{-1/2}M^T$. Then stack $C=[I-R_{A_i}]$, $d=[t_{A_i}-R_Xt_{B_i}]$ and solve $t_X=C^{\dagger}d$. Needs **≥2 motions with non-parallel rotation axes**.
- [ ] **Tsai–Lenz (1989)**: modified Rodrigues $P=2\sin\frac\theta2\,n$. Solve $\mathrm{skew}(P_{A_i}+P_{B_i})\,P'_X=P_{B_i}-P_{A_i}$ by LS, then $P_X=\frac{2P'_X}{\sqrt{1+\|P'_X\|^2}}$, then translation LS as above. Other families: dual-quaternion (Daniilidis 1999), Horaud–Dornaika, nonlinear joint refinement. OpenCV `cv2.calibrateHandEye` implements TSAI, PARK, HORAUD, ANDREFF, DANIILIDIS.
- [ ] **Point registration (Arun 1987 / SVD)**: $\bar a,\bar b$ centroids, $H=\sum(a_i-\bar a)(b_i-\bar b)^T=U\Sigma V^T$, $R=V\,\mathrm{diag}(1,1,\det(VU^T))\,U^T$, $t=\bar b-R\bar a$. **Horn (1987)**: $R$ comes from the max-eigenvalue eigenvector (unit quaternion) of a 4×4 symmetric $N(H)$.
- [ ] **ICP (Besl & McKay 1992)**: repeat {nearest-neighbour correspondences (k-d tree) → Arun → apply → check Δerror} until convergence. It finds only a local minimum, so it needs a good initial guess. Variants: point-to-plane, outlier trimming.
- [ ] **Linear least squares**: $\min\|Ax-b\|^2$ ⇒ $x=(A^TA)^{-1}A^Tb$ (prefer QR/SVD in code). Weighted: $(A^TWA)^{-1}A^TWb$. Nonlinear: Gauss–Newton $(J^TJ)\Delta=-J^Tr$, Levenberg–Marquardt adds $\lambda I$.
- [ ] Camera model (bridge to CV): $s\,\tilde u=K[R\,|\,t]\tilde X$; Zhang calibration from a checkerboard; the AR-tag pose in A2 comes from PnP.

### 5.3 Configuration space
- [ ] $\mathcal C$ = set of all robot configurations; $\mathcal C_{obs}=\{q\mid\mathcal A(q)\cap\mathcal O\neq\emptyset\}$, $\mathcal C_{free}=\mathcal C\setminus\mathcal C_{obs}$. Dimension = DOF; topology: planar rigid body $\mathbb R^2\times S^1$, 2R arm $T^2$ (torus), free-flying body $SE(3)$.
- [ ] **Minkowski sum** for a translating robot: $\mathcal C_{obs}=\mathcal O\oplus(-\mathcal A)=\{o-a\mid o\in\mathcal O,a\in\mathcal A\}$. For convex polygons, merge edges sorted by angle in $O(n+m)$. The result is still convex.
- [ ] 2-link arm C-space: rasterize $(\theta_1,\theta_2)$ and mark collisions via FK + segment/obstacle intersection. Obstacles look "warped" in $T^2$, and wrap-around matters.
- [ ] Collision detection: discretize edges at a resolution ε, or use conservative advancement; bounding volumes (AABB/OBB/spheres); GJK for convex shapes; FCL is what MoveIt uses.

### 5.4 Planning algorithms
- [ ] **Bug 1 / Bug 2 / Tangent bug**: Bug1 path ≤ $d(q_s,q_g)+1.5\sum P_i$; Bug2 ≤ $d+\frac12\sum n_iP_i$. Complete for point robots in 2-D.
- [ ] **Potential fields** (PoRM Ch. 4):
  $U_{att}=\tfrac12\zeta d^2(q,q_g)$ for $d\le d^*_g$, else $d^*_g\zeta d-\tfrac12\zeta(d^*_g)^2$, so $\nabla U_{att}=\zeta(q-q_g)$ (quadratic region).
  $U_{rep}=\tfrac12\eta\big(\tfrac1{D(q)}-\tfrac1{Q^*}\big)^2$ if $D(q)\le Q^*$, else 0; $\nabla U_{rep}=\eta\big(\tfrac1{Q^*}-\tfrac1{D}\big)\tfrac1{D^2}\nabla D(q)$.
  Descent $q_{k+1}=q_k-\alpha\nabla U(q_k)$. **Local minima** → fix with a wavefront (brushfire) planner, navigation functions (Rimon–Koditschek), or random walks. For arms, use workspace forces on control points mapped by $\tau=\sum_iJ_i^T(q)F_i$. "Sensor constraints as potentials" means $D(q)$ comes from range sensors.
- [ ] **Visibility graph**: nodes are polygon vertices plus start and goal, edges are mutually visible pairs. Gives the shortest path in 2-D; naive $O(n^3)$, rotational sweep $O(n^2\log n)$. **Trapezoidal decomposition** via sweep line in $O(n\log n)$; **Boustrophedon/Morse** for coverage; **GVD** for maximum clearance.
- [ ] **Dijkstra**: priority queue on $g$, $O((V+E)\log V)$. **A\***: $f=g+h$. **Admissible** $h\le h^*$ ⇒ optimal (tree search). **Consistent** $h(n)\le c(n,n')+h(n')$ ⇒ optimal without reopening closed nodes (graph search), and consistent implies admissible. Grid heuristics: Manhattan (4-conn), octile/Euclidean (8-conn). Weighted A* ($\epsilon h$) gives $\epsilon$-suboptimal solutions. D*/D* Lite for replanning.
- [ ] **PRM** (multi-query):
  ```
  V ← ∅; E ← ∅
  while |V| < N: q ← sample(C); if free(q): V ← V ∪ {q}
  for q in V: for q' in kNN(q, V) or r-ball(q):
      if local_path_free(q, q'): E ← E ∪ {(q, q')}
  query: connect q_s, q_g to V ("accessibility"/"departability"), run A*/Dijkstra
  ```
- [ ] **RRT** (single-query):
  ```
  T ← {q_s}
  repeat: q_rand ← sample (goal-bias p≈0.05–0.1)
          q_near ← nearest(T, q_rand); q_new ← steer(q_near, q_rand, step)
          if local_path_free(q_near, q_new): add q_new with parent q_near
          if dist(q_new, q_g) < tol and free: return path via parents
  ```
  **RRT-Connect**: two trees, EXTEND one and CONNECT the other greedily, then swap. **RRT\***: choose the lowest-cost parent within radius $r_n=\gamma(\log n/n)^{1/d}$, then **rewire** neighbours through $q_{new}$ when cheaper. Asymptotically optimal.
- [ ] **EST** (the 2026 A3): pick a tree node with probability ∝ $w(q)$, where $w$ ≈ 1/(1+deg) or the inverse of neighbourhood density; sample $q'$ near it (Gaussian); add it if the edge is free; try to connect the two trees. Prefers sparsely explored regions.
- [ ] Properties: **probabilistic completeness** (PRM/RRT/EST) vs **resolution completeness** (grid A*) vs **completeness** (visibility graph, exact cell decomposition). Narrow-passage problem → Gaussian/bridge sampling. Smoothing: shortcutting.
- [ ] Optimization-based (bridge): minimize $\int\|\ddot q\|^2+\lambda\,c_{obs}(q)$ (CHOMP/TrajOpt/STOMP). Know the idea only.

### 5.5 State estimation
- [ ] **Bayes filter** (PR Ch. 2):
  $\overline{bel}(x_t)=\int p(x_t\mid u_t,x_{t-1})\,bel(x_{t-1})\,dx_{t-1}$ (predict),
  $bel(x_t)=\eta\,p(z_t\mid x_t)\,\overline{bel}(x_t)$ (update). Markov assumption. Histogram/grid filter = discretized Bayes.
- [ ] **Kalman filter** ($x_t=Ax_{t-1}+Bu_t+\epsilon$, $\epsilon\sim\mathcal N(0,Q)$; $z_t=Hx_t+\delta$, $\delta\sim\mathcal N(0,R)$):
  Predict $\bar x=Ax+Bu$, $\bar P=APA^T+Q$.
  Update $S=H\bar PH^T+R$, $K=\bar PH^TS^{-1}$, $x=\bar x+K(z-H\bar x)$, $P=(I-KH)\bar P$ (Joseph form: $(I-KH)\bar P(I-KH)^T+KRK^T$).
  1-D intuition: $K=\frac{\bar\sigma^2}{\bar\sigma^2+\sigma_z^2}$. Check consistency with NIS/NEES.
- [ ] **EKF**: $\bar x=f(x,u)$, $F=\partial f/\partial x|_{x}$, $\bar P=FPF^T+GQG^T$ (or $V M V^T$ for control noise, PR notation); $H=\partial h/\partial x|_{\bar x}$; innovation $z-h(\bar x)$ (**wrap angles!**).
  Unicycle: $x'=x+v\Delta t\cos\theta$, $y'=y+v\Delta t\sin\theta$, $\theta'=\theta+\omega\Delta t$; $F=\begin{bmatrix}1&0&-v\Delta t\sin\theta\\0&1&v\Delta t\cos\theta\\0&0&1\end{bmatrix}$.
  Range-bearing to landmark $(m_x,m_y)$: $r=\sqrt{\delta_x^2+\delta_y^2}$, $\phi=\mathrm{atan2}(\delta_y,\delta_x)-\theta$; $H=\begin{bmatrix}-\delta_x/r&-\delta_y/r&0\\ \delta_y/r^2&-\delta_x/r^2&-1\end{bmatrix}$.
  GPS: $h$ = position scaled to lat/lon (a near-constant Jacobian), as in A4. IMU: $h$ = orientation (RPY).
- [ ] **UKF** (bridge): $2n+1$ sigma points $\chi_0=\mu$, $\chi_{\pm i}=\mu\pm(\sqrt{(n+\lambda)\Sigma})_i$, $\lambda=\alpha^2(n+\kappa)-n$; $w_0^m=\lambda/(n+\lambda)$, $w_0^c=w_0^m+(1-\alpha^2+\beta)$, $w_i=1/(2(n+\lambda))$. No Jacobians needed.
- [ ] **Particle filter / MCL** (PR Ch. 4, 8): for each particle, sample $x_t^{[m]}\sim p(x_t\mid u_t,x_{t-1}^{[m]})$ and set $w^{[m]}=p(z_t\mid x_t^{[m]})$. Normalize. **Low-variance (systematic) resampling**: $r\sim U[0,1/M]$, select at $U_m=r+(m-1)/M$ on the CDF. Resample when $N_{eff}=1/\sum w^2<M/2$. Use log-weights to avoid underflow. Add random particles for kidnapping (augmented MCL); KLD-sampling adapts $M$.
- [ ] **Sensor models** (PR Ch. 6): beam model = mixture $z_{hit},z_{short},z_{max},z_{rand}$; **likelihood field** $q=\prod_k\big(z_{hit}\,\mathcal N(\text{dist}_k;0,\sigma_{hit}^2)+z_{rand}/z_{max}\big)$, computed with a precomputed distance transform (this is A5).
- [ ] Multiple hypotheses: Gaussian mixtures / MHT for ambiguous data association (week 10–12 topic).
- [ ] **Mapping / SLAM** (bridge): occupancy log-odds $l_{t,i}=l_{t-1,i}+\log\frac{p(m_i\mid z_t,x_t)}{1-p(m_i\mid z_t,x_t)}-l_0$. **EKF-SLAM**: state $[x,y,\theta,m_1,\dots,m_N]$, $O(N^2)$ update, landmark initialization by inverse measurement. **Graph SLAM**: $\min_x\sum e_{ij}^T\Omega_{ij}e_{ij}$, Gauss–Newton $H\Delta x=-b$, sparse. **FastSLAM**: Rao-Blackwellized PF.

---

## 6. Difficulty, student reviews and tips

- **No public reviews were found** for 601.463/663: no Reddit threads, and semester.ly evaluations were not reachable. Anything below is inferred from the assignments and syllabus **(unverified as "student opinion")**.
- **Where the difficulty is.** The math is moderate (linear algebra plus probability). The workload comes from **C++ + ROS 2 + MoveIt 2 + Gazebo plumbing**: building with colcon, writing launch files, tf2, plugin registration (`assignment3_plugin.xml`), and the controller topics. A student comment in the 2026 A3 code says "URL in assignment does not work: moveit.ros.org/documentation/source-code-api", so expect to read MoveIt 2 source and API docs yourself.
- **Real robots.** Demos on a real UR5e are required for the final mark, so lab time in Wyman 170 is a scheduling constraint.
- **MATLAB** appears in A2 (hand-eye) even though the rest is C++. Python is not mentioned on the course page.
- **AI tools are banned** (an F for misconduct). Your preparation must leave you able to write these from scratch yourself, which is the point of the plan below.
- Practical tips seen in student code:
  1. Quaternion ordering bugs (ROS `xyzw` vs MATLAB `wxyz`).
  2. Use `\` and `/` (or `np.linalg.solve`), not `inv()`, for relative transforms $E_1^{-1}E_2$.
  3. Collision-check edges at a fine resolution (one student needed about 100 interpolation steps per edge for a 6-DOF arm "to rarely fail").
  4. Generate long FK/Jacobian/EKF-Jacobian expressions **symbolically** (SymPy/MATLAB Symbolic) and verify them numerically with finite differences.
  5. EKF success is mostly **covariance tuning**. Plot innovations.
  6. Test hand-eye on **synthetic data first** (the course itself provides `generatedata.m`).
- Overlaps you can exploit:
  - **RDKDC (530.646)** covers SE(3), twists, PoE and Jacobians (A1 math).
  - **CIS I (601.455/655)** covers frames, point-set registration (Arun/Horn), pivot calibration and ICP-like matching.
  - **Computer Vision** covers camera calibration and PnP (the AR-tag pose in A2).

  Learn each topic once and reuse it.

---

## 7. Best free resources

| Resource | Use it for | URL |
|---|---|---|
| CIRL course page (official schedule) | dates, readings, policies | https://cirl.lcsr.jhu.edu/sensorbasedrobotics/ |
| Leonard / Hager slide PDFs | course-specific notation | see Section 1 list |
| **LaValle, *Planning Algorithms*** (free) | C-space Ch. 4, sampling Ch. 5, combinatorial Ch. 6, discrete search Ch. 2 | https://lavalle.pl/planning/ (mirror: http://planning.cs.uiuc.edu/) |
| **Lynch & Park, *Modern Robotics*** (free preprint + videos) | Ch. 3–5 (rigid motion, FK, Jacobian), Ch. 10 (motion planning) | https://hades.mech.northwestern.edu/index.php/Modern_Robotics |
| Modern Robotics Coursera, Course 4 (Motion Planning) | Ch. 10 videos | https://www.coursera.org/learn/modernrobotics-course4 |
| **Murray–Li–Sastry** (free PDF) | Ch. 2 rigid motion (a CIRL Week-1 reading) | https://www.cds.caltech.edu/~murray/mlswiki/ |
| **Cyrill Stachniss**, *Robot Mapping* (Freiburg WS 2013/14) + newer Bonn lectures | KF/EKF/UKF, PF, EKF-SLAM, graph SLAM, ICP: the best video explanations | https://www.ipb.uni-bonn.de/robot-mapping/ · YouTube: https://www.youtube.com/@CyrillStachniss · teaching index: https://www.informatik.uni-freiburg.de/~stachnis/teaching.html |
| **PythonRobotics** (Atsushi Sakai) | reference Python for EKF/PF/histogram localization, A*, D*, potential field, PRM, RRT, RRT*, ICP, FastSLAM, N-joint arm navigation with obstacles (C-space) | https://github.com/AtsushiSakai/PythonRobotics · docs: https://atsushisakai.github.io/PythonRobotics/ |
| Choset CMU 16-735 slides | the original source of many course slides (bug, potential fields, roadmaps, KF) | https://www.cs.cmu.edu/~motionplanning/ (e.g. `lecture/Chap4-Potential-Field_howie.pdf`) |
| **OMPL** tutorials and Python bindings | PRM/RRT/EST/RRT* reference implementations, benchmarking | https://ompl.kavrakilab.org/tutorials.html |
| **MoveIt 2** docs, planner plugin example | A3 plumbing (PlanningContext/PlannerManager) | https://moveit.picknik.ai/main/doc/examples/creating_moveit_plugins/plugin_tutorial.html (unverified path; start at https://moveit.picknik.ai/) |
| ROS 2 docs (tutorials: rclcpp, tf2, launch) | assignment plumbing | https://docs.ros.org/en/jazzy/Tutorials.html (switch distro to `humble` if the lab uses it) |
| R. Labbe, *Kalman and Bayesian Filters in Python* (free Jupyter book) | KF/EKF/UKF/PF intuition and code | https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python |
| J. Solà, *A micro Lie theory for state estimation in robotics* | log/exp maps and Jacobians on SO(3)/SE(3) | https://arxiv.org/abs/1812.01537 |
| J. Solà, *Quaternion kinematics for the error-state Kalman filter* | quaternion conventions, IMU EKF | https://arxiv.org/abs/1711.02508 |
| OpenCV `calibrateHandEye` docs | cross-check your AX=XB solver | https://docs.opencv.org/4.x/d9/d0c/group__calib3d.html |
| Park & Martin 1994 (the CIRL week-3 reading) | AX=XB on SE(3) | IEEE T-RA 10(5):717–721, DOI 10.1109/70.326576 |
| Horaud & Dornaika, *Hand-Eye Calibration* (arXiv version) | survey/derivations | https://arxiv.org/abs/2311.12655 |

---

## 8. Gaps and prerequisites to fill (for you specifically)

| Gap | Why it matters here | Minimal fix |
|---|---|---|
| **C++** (you rated yourself weak) | All 5 assignments are C++ (raw arrays `double J[6][6]`, `std::vector`, `<random>`, classes) | 1 evening: C++ refresher (references, const, STL vectors, `std::mt19937`, `std::normal_distribution`). Port one Python mini-project (RRT) to C++. |
| **ROS 2** (weak) | Nodes, topics, `JointTrajectory`, tf2, launch.py, colcon, Gazebo `ros_gz`, MoveIt 2 plugins | On the Mac use **Docker** (`osrf/ros:jazzy-desktop` or humble) and do the official beginner client-library tutorials. Then build `simonleonard/asbr_description` and launch `ur5e_robotiq.launch.py`. |
| **MATLAB** | A2 solver is MATLAB | MATLAB Onramp (free); you get a JHU license on enrollment. The Python versions you write port 1:1. |
| Linear algebra: SVD, matrix square root, log map | Arun/ICP, Park–Martin $(M^TM)^{-1/2}$, Jacobians | Covered in Phase 0. Verify with `scipy.linalg.sqrtm`, `logm`, `svd`. |
| Probability: Gaussians, Bayes, marginalization | KF/EKF/PF derivations | Phase 0 probability + PR Ch. 2–3 |
| Lie groups (SO(3)/SE(3) exp/log, adjoint) | A1, A2 | RDKDC overlap: MR Ch. 3 + Solà micro-Lie paper §1–4 |
| Computational geometry basics | Minkowski sums, visibility, sweep line | PoRM Ch. 3, 5 slides; LaValle Ch. 6 |

---

## 9. Build-first project sequence (do in order; you write all code)

**Assumptions:** you already saw the basics. Split each project roughly 3 parts reading/video, 11 parts building, 2 parts
paper derivations and notes. Code is **Python (NumPy/SciPy/Matplotlib)** for intuition. On Project 5 you
port one piece to **C++/ROS 2** because that is what gets graded. Put each mini-project in its own folder
on a code branch; this branch keeps notes only. Each project ends with a **"closed-book check"**: re-derive the
project's equations on paper, without notes or AI, since AI is banned in the course.

Do these **after** the foundations math and the RDKDC rigid-body projects. Project 1 then becomes
consolidation, not new learning.

### Project 1: Rigid motion → resolved-rate → hand-eye (mirrors A1 + A2)
1. **`se3.py`** (2 h): `hat`, `vee`, `expSO3` (Rodrigues), `logSO3` (with edge cases), `expSE3`, `Ad(T)`, quaternion ↔ R (both orderings), `q_mul`, `slerp`. Unit tests: `expSO3(logSO3(R)) == R` on 1,000 random R, and compare against `scipy.spatial.transform.Rotation`.
2. **UR5e FK + Jacobian** (4 h): build PoE FK for the UR5e (screw axes from the MR book's UR5 example, with UR5e link lengths 0.1625/0.425/0.3922/0.1333/0.0997/0.0996). Compute $J_s$, then $J_b=\mathrm{Ad}_{T^{-1}}J_s$. **Check $J$ against finite differences.** Print $\det J$ near the wrist singularity ($q_5=0$).
3. **Resolved-rate straight line** (2 h): reproduce the A1 loop: 1 mm steps toward a Cartesian goal, $\dot q=J_b^{-1}[v_b;0]$. Then switch to damped least squares and plot joint trajectories through a near-singularity.
4. **Hand-eye on synthetic data** (4 h): port `generatedata` (random SE(3), N = 10 poses, add noise of 1 mm / 0.5°). Implement **Park–Martin closed-form (2 motions)**, **Park–Martin LS (N motions)** and **Tsai–Lenz**, and cross-check with `cv2.calibrateHandEye`. Plot rotation and translation error vs. noise and vs. N. Show that it fails when all rotation axes are parallel.
5. **Arun SVD + ICP in 2-D/3-D** (2 h; short if already done in CIS I): register two noisy point sets, then ICP with `scipy.spatial.cKDTree`. Show convergence from a good start and failure from a 90° start.
6. Closed-book check (2 h): derive $J_b=\mathrm{Ad}_{T^{-1}}J_s$, the AX=XB rotation/translation split, Park–Martin $R_X$, and Arun with the reflection fix.

### Project 2: C-space, potentials, graph search (mirrors weeks 4–5)
1. **Minkowski C-obstacle** (2 h): convex polygon robot translating among convex obstacles. Implement the $O(n+m)$ edge-merge Minkowski sum of $\mathcal O\oplus(-\mathcal A)$ and plot it.
2. **2-link arm C-space** (3 h): rasterize $T^2$ at 1° resolution using FK + segment–polygon intersection. Render workspace and C-space side by side, and click a C-space point to show the arm pose.
3. **A\* and Dijkstra on grids** (3 h): 8-connected, octile heuristic. Count expansions for Dijkstra vs A* vs weighted A* (ε = 2). Then run A* on the **2-link C-space torus** with wrap-around neighbours.
4. **Potential field** (3 h): 2-D point robot, quadratic/conic attractive + $Q^*$-limited repulsive field computed with a distance transform (`scipy.ndimage.distance_transform_edt`) as "sensor constraints". Show a local minimum (U-shaped obstacle), then fix it with a **wavefront** planner.
5. **Visibility graph** (2 h): polygons → visible vertex pairs → Dijkstra → shortest path. Compare its length with the grid A* path.
6. Closed-book (3 h): hand-trace A* on a 5×5 grid; prove that a consistent heuristic is admissible; sketch the Minkowski sum for a triangle and a square; derive $\nabla U_{rep}$; Bug1/Bug2 bounds.

### Project 3: Sampling-based planning (mirrors A3)
1. **PRM** (2 h) in 2-D, then on the 2-link arm, using a shared `collision_free(q)` and `local_path_free(q, q', eps)`. Implement A3-2017 vocabulary: `make_vertices`, `make_edges` (k-NN), accessibility/departability, Dijkstra.
2. **RRT + goal bias** (2 h), then **RRT-Connect** (2 h). Animate the tree growth.
3. **EST, bidirectional** (2 h), mirroring the 2026 A3 API: `select_config_from_tree(w)` with $w=1/(1+\deg)$, `sample_nearby` (Gaussian σ), `find_nearest_configuration`, `is_local_path_collision_free`, `search_path`. Compare RRT, RRT-Connect and EST on a **narrow-passage** map (success rate and time over 50 seeds).
4. **RRT\*** (2 h): choose-parent + rewire with $r_n=\gamma(\log n/n)^{1/d}$. Plot path cost vs. iterations against RRT. Add path shortcutting.
5. **6-DOF taste** (2 h): run RRT-Connect on the UR5e FK from Project 1 with sphere-approximated links vs a box obstacle. Optionally compare with OMPL's Python bindings (`pip install ompl` if a wheel exists for your platform **(unverified)**).
6. Closed-book (2 h): write PRM / RRT / RRT* / EST pseudo-code from memory; state completeness types; explain the narrow-passage problem and Gaussian/bridge sampling.

### Project 4: Bayes, KF, EKF (mirrors A4)
1. **Histogram (grid) Bayes filter** (2 h): 1-D corridor with doors; predict by convolution, update by a likelihood, normalize. Watch a multimodal belief collapse.
2. **Linear KF** (2 h): constant-velocity 2-D tracker; plot the 3σ ellipse; compare with the 1-D gain intuition; implement the Joseph form; NIS test.
3. **EKF landmark localization** (4 h): unicycle + range-bearing to known landmarks, with analytic $F$ and $H$ verified by finite differences. Include angle wrapping and a data-association toggle (known vs nearest-neighbour with Mahalanobis gating).
4. **GPS + IMU EKF** (3 h): mirror A4. State $[x,y,z,r,p,y]$; process = integrate body twist; GPS $h$ = position scaled to lat/lon; IMU $h$ = RPY. Tune $Q$ and $R$ and plot innovations. Optionally compare with PythonRobotics `gps_imu_fusion`.
5. **UKF** (1 h, optional): swap the EKF predict/update for sigma points on the landmark problem and compare.
6. Closed-book (2 h): derive Bayes filter → KF (Gaussian product); compute $H$ for range-bearing; do one hand-computed 1-D KF cycle.

### Project 5: Particle filter, mapping, ICP + port to C++/ROS 2 (mirrors A5, consolidates the course)
1. **MCL with a likelihood field** (4 h): occupancy-grid map with a simulated 2-D lidar (ray casting). Precompute the distance transform; scoring $w_{hit}\mathcal N(d;0,\sigma^2)+w_{rand}/z_{max}$ in log space; odometry motion model; **low-variance resampling**, $N_{eff}$ trigger. Test global localization and recovery from a kidnapping (augmented MCL).
2. **Occupancy grid mapping** (2 h): log-odds inverse sensor model with known poses.
3. **Scan-matching ICP odometry** (1 h): consecutive lidar scans → ICP → compare drift with wheel odometry (reuses Project 1 ICP). Optional: a 10-landmark EKF-SLAM toy (1 h).
4. **C++/ROS 2 port** (5 h, Docker on the Mac):
   - Build `simonleonard/asbr_description` and launch the UR5e.
   - Write an rclcpp node that subscribes to `/joint_states` and publishes a `JointTrajectory` to `/ur5e_controller/joint_trajectory` (A1 skeleton).
   - Port your Python RRT/EST core (`std::vector<double>` vertices, `<random>`) into a plain C++ library with a unit test. That is the code you will drop into the MoveIt `PlanningContext` in A3.
5. Closed-book exam simulation (2 h): a 2-hour mixed paper set made from Section 5 checkboxes (one item from each of: Jacobian/adjoint, AX=XB, Minkowski/C-space, A* trace, sampling pseudo-code, KF cycle, EKF Jacobian, PF resampling).

**Definition of done for this course in the sprint:** every Section 5 box is ticked; Python mini-projects
1–5 run end to end with plots; one C++ ROS 2 node runs in Docker; you can explain each A1–A5 TODO
function from Section 4 without looking.

---

## 10. Sources

Official / course
- CIRL course page: https://cirl.lcsr.jhu.edu/sensorbasedrobotics/ (and https://cirl.lcsr.jhu.edu/SensorBasedRobotics)
- CIRL 2013 schedule: https://cirl.lcsr.jhu.edu/sensorbasedrobotics/sensor_course_schedule_2013
- JHU CS course listings: https://www.cs.jhu.edu/spring26/ · https://www.cs.jhu.edu/fall26/ · https://www.cs.jhu.edu/spring25/ · https://www.cs.jhu.edu/spring23/
- semester.ly entries: https://jhu.semester.ly/c/EN.601.663 · https://jhu.semester.ly/course/EN.601.663/Fall/2023
- LCSR Robotics MSE advising manuals: https://lcsr.jhu.edu/wp-content/uploads/2026/03/2026-2027-Robotics-Advising-Manual.pdf · https://lcsr.jhu.edu/wp-content/uploads/2025/10/2025-2026-Robotics-Advising-Manual-August-2025.pdf · https://lcsr.jhu.edu/wp-content/uploads/2025/10/2023-2024-Robotics-Advising-Manual-9.12.23.pdf
- LCSR Robotics minor (lists EN.601.463): https://lcsr.jhu.edu/education/undergraduate-minors/robotics-minor/
- DSCL lab page naming 601.463/663 and GitHub submission: https://dscl.lcsr.jhu.edu/?p=2354

Lecture PDFs
- https://www.cs.jhu.edu/~sleonard/lecture03.pdf
- https://www.cs.jhu.edu/~sleonard/week04.pdf
- https://www.cs.jhu.edu/~sleonard/week06.pdf
- https://www.cs.jhu.edu/~sleonard/week08.pdf
- https://www.cs.jhu.edu/~sleonard/Chap8-Kalman-Mapping.pdf
- https://www.cs.jhu.edu/~sleonard/cs436/roadmaps.pdf
- https://www.cs.jhu.edu/~hager/Teaching/cs336/Notes/W4-ConfigSpace.pdf
- https://www.scribd.com/document/424930347/lecture01-pdf

Student / instructor code (cloned and read for Section 4)
- https://github.com/amal-araweelo/sbr ("EN.601.663 Algorithms for Sensor-based Robotics", 2026: A1–A3)
- https://github.com/Deck-Wang416/asbr_assignment4 (Fall 2025 A4, EKF, Jackal)
- https://github.com/ChengdaJi/SensorBasedRobotics (2017 ROS 1: A2 hand-eye/ArUco, A3 PRM MoveIt plugin, A4 EKF Husky, A5 AMCL likelihood field)
- https://github.com/iwilkes1/asbr_assignment1 (2015 A1, ROS 1)
- https://github.com/simonleonard/asbr_description (instructor's UR5e + Robotiq + camera description)
- Not this course: https://github.com/swanbeck/asbr_toolbox (UT Austin ME 397); https://github.com/Maxim-Gurevich/Algorithms-for-Sensor-Based-Robotics (likely UT Austin, unverified)

Textbooks and papers
- Choset et al., *Principles of Robot Motion*, MIT Press 2005: https://mitpress.mit.edu/9780262033275/principles-of-robot-motion/
- Thrun, Burgard, Fox, *Probabilistic Robotics*: http://www.probabilistic-robotics.org/
- LaValle, *Planning Algorithms*: https://lavalle.pl/planning/
- Lynch & Park, *Modern Robotics*: https://hades.mech.northwestern.edu/index.php/Modern_Robotics
- Murray, Li, Sastry: https://www.cds.caltech.edu/~murray/mlswiki/
- Park & Martin 1994, IEEE T-RA 10(5):717–721 (DOI 10.1109/70.326576)
- Tsai & Lenz 1989, IEEE T-RA 5(3):345–358
- Arun, Huang, Blostein 1987, IEEE PAMI 9(5):698–700; Horn 1987, JOSA A 4(4):629–642
- Besl & McKay 1992, IEEE PAMI 14(2):239–256
- Karaman & Frazzoli 2011 (RRT*): https://arxiv.org/abs/1105.1186
- Horaud & Dornaika, Hand-eye calibration: https://arxiv.org/abs/2311.12655
- Hand-eye overview: https://en.wikipedia.org/wiki/Hand%E2%80%93eye_calibration_problem

Free learning resources
- PythonRobotics: https://github.com/AtsushiSakai/PythonRobotics · https://atsushisakai.github.io/PythonRobotics/
- OMPL tutorials: https://ompl.kavrakilab.org/tutorials.html
- Stachniss Robot Mapping: https://www.ipb.uni-bonn.de/robot-mapping/ · https://www.informatik.uni-freiburg.de/~stachnis/teaching.html · https://www.youtube.com/@CyrillStachniss
- Choset CMU 16-735 slides: https://www.cs.cmu.edu/~motionplanning/
- Labbe KF book: https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python
- Solà micro Lie theory: https://arxiv.org/abs/1812.01537 · quaternion kinematics: https://arxiv.org/abs/1711.02508
- Modern Robotics Course 4 (Coursera): https://www.coursera.org/learn/modernrobotics-course4

### Open questions / to verify once you have network access to JHU hosts
1. Exact readings for weeks 2 and 8–12 on the CIRL page (search extracts were jumbled).
2. Whether `week01/02/05/07/09–12.pdf` exist at `cs.jhu.edu/~sleonard/`.
3. The 2026 A4/A5 handouts (the A4 robot was a Jackal in 2025; the A5 content for 2025/26 is unverified).
4. The lab ROS 2 distro (the page says Ubuntu 24.04 + Humble, a combination Humble does not officially support).
5. Which semester you will actually take it in (Fall vs Spring offering) and that term's dates.
