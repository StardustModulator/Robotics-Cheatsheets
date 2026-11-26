# JHU ASBR (EN.601.463/663) lecture slides, Part 1: Weeks 1–6
Source: Simon Leonard / G.D. Hager slide decks (uploads): `1092bddb-lecture01.pdf` (24 pp), `9a09f8bf-lecture02.pdf` (40 pp), `0554a276-lecture03.pdf` (15 pp), `d5132eef-week03.pdf` (8 pp), `cea0e059-week05.pdf` (37 pp), `dea4ec92-week06.pdf` (54 pp). Every page was read visually.

> These are slides, not a textbook. Several slides are embedded videos (blank boxes in the PDF). I note those as "video" so nobody hunts for content that isn't there. Where a slide has a typo I flag it under *Conventions / pitfalls*.

---

## Deck 1: Introduction and Background (lecture01.pdf, PDF pp. 1–24), Lessons: A1 (context), A3, A4, A5, A6, A7 (overview)
**Big idea:** The course is about two problems: **motion planning** (known map, known start and goal: find a collision-free path) and **localization and mapping** (no prior map: use sensing and motion to estimate both the map and the robot's pose). Everything is treated as the *static* planning problem: the world does not change and "our own momentum does not matter" (p. 18), so dynamics stays out of the planner. The emphasis is on algorithms, their analysis, and implementing them on real sensor data.

**Key definitions:**
- *Motion planning* (p. 3): given a mechanism, a known environment, and a known start and goal, compute inputs (for example a joint trajectory) that move the robot from start to goal without collision.
- *Basic path planning problem statement* (p. 5): "compute a continuous sequence of collision-free robot configurations connecting the initial and goal configurations". Inputs: (1) geometry of the environment, (2) geometry and kinematics of the robot, (3) initial and goal configurations. Output: a collision-free path.
- *Localization and mapping / SLAM* (pp. 3, 10): simultaneously compute a map and the robot's location in it, a "chicken and egg" problem (you need a map to localize and a pose to map).
- Assumptions questioned on p. 6: perfect sensors (what information, uncertainty), perfect control (what controls, uncertainty), perfect thinking (complete world knowledge, can you process everything).

**Key results & equations:** none (motivational deck).

**Algorithms:** none written out. Pp. 11–16 show a **Monte Carlo localization** (particle filter) sequence on an occupancy-grid map: particles (red) start spread over the whole free space, the laser scan (blue rays) is applied, and the particles collapse to a tight cluster at the true pose within a few updates. This is the motivating picture for A6. Pp. 7 (Kavraki probabilistic methods / drug design), 8 (car parking with kinematic constraints, Tesla / Otago), 9 (dynamics: golf, pitching, cricket bowling), 15 (localization) are videos or images only.

**Course logistics worth knowing (pp. 19–24):**
- Textbook (p. 20): Choset et al., *Principles of Robot Motion*, MIT Press 2005. Other books (p. 21): Latombe 1991; LaValle *Planning Algorithms* 2006 (free online); Thrun, Burgard, Fox *Probabilistic Robotics* 2006; Murphy *Intro to AI Robotics*.
- Trends (p. 22): classical (mid-70s: exact models, no sensing) → reactive (mid-80s: no models, good sensing) → hybrids (90s) → probabilistic robotics (mid-90s: inaccurate models and sensors, integrated).
- Grading (p. 23, "Spring 2026"): 1 midterm 25%, 1 final 25%, 5 assignments + 3 demonstrations 50% (3 UR5 assignments in Wyman 170 = 27%, 2 Jackal/Husky assignments = 20%, demos 3%). Canvas for submission.
- Assignment tool matrix (p. 23), columns = Assignment #1–#5, rows ROS / Gazebo / C++ / Matlab / Demo. Demo row: UR5, UR5, UR5, Jackal, Jackal. Colours (no legend on the slide; red probably means "main tool", yellow "used", green "not needed"): A1 ROS red, Matlab yellow; A2 ROS yellow, Matlab red; A3 ROS yellow, C++ red; A4 ROS yellow, C++ yellow, Matlab red; A5 ROS yellow, C++ yellow. Gazebo is green in every column.
- Fall 2026 (p. 24): 5 UR5s (+5 more), 12 workstations, no curve, no senior option. A+ ≥ 97, A 94–97, A− 90–94, B+ 87–90, B 84–87, B− 80–84, C+ 77–80, C 74–77, C− 70–74, D+ 67–70, D 60–67.

**Worked examples worth redoing:** none.
**Exercises worth doing:** none. *Possible exam question:* "State the path-planning problem: inputs, output, and what is assumed" (p. 5–6, 18).

**What the student must be able to do by hand:** state the two key problems precisely; list the planner's inputs and outputs; explain why SLAM is chicken-and-egg; explain what "static planning" excludes.

**Conventions / pitfalls:** The course says planning is static, but the robot labs use a real UR5 and a Jackal, so ROS (1 or 2 depending on the year) and C++ skills are graded through the assignments (A7 lesson).

---

## Deck 2: Robot Kinematics (lecture02.pdf, PDF pp. 1–40), Lessons: F3, R1, R2, R3, R5, A1
**Big idea:** Kinematics is a map between two spaces. Joint space is just $\mathbb{R}^N$, so plain linear algebra works there (you can add joint vectors, $q_C = q_A + q_B$). Cartesian space is SE(3), so you need rigid-body transforms. Forward kinematics composes transforms along the chain; inverse kinematics is messy, so for motion you go *to velocities*. The manipulator Jacobian maps joint rates to the tool's spatial velocity, and inverting a matrix "is much easier than computing the inverse kinematics" (p. 38). That sentence motivates resolved-rate control in Assignment 1/2.

**Key definitions:**
- Frame notation (p. 3): ${}^B R_T$ is the orientation of the Tool frame T with respect to the Base frame B, and ${}^B t_T$ is the position of T with respect to B. Forward kinematics: $[{}^B R_T, {}^B t_T] = f(q)$. Inverse kinematics: $q = f^{-1}([{}^B R_T, {}^B t_T])$.
- Joints (p. 2): revolute (rotate about an axis), prismatic (translate along an axis). ISO 8373 requires industrial robots to have at least 3 axes (p. 4).
- $SE(2) = \{(t,R): t\in\mathbb{R}^2, R\in SO(2)\} = \mathbb{R}^2\times SO(2)$ (p. 6); $SE(3)=\mathbb{R}^3\times SO(3)$ (p. 7). $R\in SO(n)$ iff $RR^T=I$ and $\det R = +1$.
- Elementary rotations (p. 8): $R_x(\phi)$, $R_y(\beta)$, $R_z(\alpha)$, standard right-handed forms (note $R_y$ has $+\sin\beta$ in the (1,3) entry). "Be careful of commutations: $R_xR_yR_z \neq R_zR_yR_x$."
- Representations (p. 9): quaternion, Euler angles, axis/angle, Rodrigues. All are the same object, a 3×3 orthogonal matrix with det +1. The slide quotes MLS: no 3-parameter representation of SO(3) is free of singularities.
- Homogeneous coordinates (pp. 10–11): append 1. The affine map ${}^A P = {}^A R_B\,{}^B P + {}^A t_B$ becomes linear: $\begin{bmatrix}{}^A P\\1\end{bmatrix} = {}^A E_B \begin{bmatrix}{}^B P\\1\end{bmatrix}$ with ${}^A E_B = \begin{bmatrix}{}^A R_B & {}^A t_B\\ 0 & 1\end{bmatrix}$. Chaining: ${}^A E_C = {}^A E_B\,{}^B E_C$ ("convenient", vs nested affine maps "annoying").
- Frame-assignment guidelines (p. 15): DH, modified DH and Hayati are "conventions, not laws", kept for legacy reasons (4 numbers per link instead of 6). (1) Choose base and tool frames to make your life easy; (2) go from base to tool, usually one frame per actuator; (3) align each frame with its joint axis. Traditionally that is z, but any axis works. Example figure: Barrett WAM frames.

**Key results & equations:**
- 2-link planar FK (pp. 16–19): ${}^A E_B(q_1) = \begin{bmatrix}R(q_1) & {}^A t_B\\0&1\end{bmatrix}$ (translation constant, rotation depends on $q_1$), ${}^B E_C(q_2)$ similarly; ${}^A P = {}^A E_B\,{}^B E_C\,{}^C P = {}^A E_C\,{}^C P$.
- 3D two-revolute-z example (p. 20):
  $${}^A E_C = \begin{bmatrix} R_z(q_1)R_z(q_2) & {}^A t_B + R_z(q_1)\,{}^B t_C\\ 0 & 1\end{bmatrix}.$$
- IK 2D (p. 22): with one joint, $q$ appears only in ${}^A R_B$, so read it off (example: entries 0.7071 give $q = 45^\circ$). IK 3D (p. 23): find $q_1,q_2$ matching ${}^A E_C$ above; symbolic solutions get messy, a numerical solution (Newton's method) is more general. **"The inverse kinematics is NOT the inverse of the forward kinematics ($({}^A E_B)^{-1}$)"**, which is a likely true/false exam item.
- Rotational velocity (pp. 26–27): from ${}^A p(t) = {}^A R_B(t)\,{}^B p$, $v = {}^A\dot R_B\,{}^B p = ({}^A\dot R_B\,{}^A R_B^{-1})\,{}^A p$. ${}^A\dot R_B\,{}^A R_B^{-1}$ is skew-symmetric and defines the **spatial angular velocity** $\hat\omega$, with hat map $\hat a = \begin{bmatrix}0&-a_z&a_y\\a_z&0&-a_x\\-a_y&a_x&0\end{bmatrix}$.
- Rigid-body velocity (p. 28): $v_{{}^A p} = ({}^A\dot E_B\,{}^A E_B^{-1})\,{}^A p$ and
  $${}^A\dot E_B\,{}^A E_B^{-1} = \begin{bmatrix}{}^A\dot R_B\,{}^A R_B^T & -{}^A\dot R_B\,{}^A R_B^T\,{}^A t_B + {}^A\dot t_B\\ 0&0\end{bmatrix}.$$
- **Spatial velocity** (p. 29): ${}^A\hat V^s_B = {}^A\dot E_B\,{}^A E_B^{-1}$, with $v^s = -\dot R R^T t + \dot t$, $\hat\omega^s = \dot R R^T$, and $V^s = \begin{bmatrix}v^s\\ \omega^s\end{bmatrix}$.
- Spatial linear velocity interpretation (p. 30, quoting MLS): $v^s$ is **not** the velocity of the body-frame origin. It is the velocity of the (possibly imaginary) body-fixed point currently passing through the spatial-frame origin.
- **Body velocity** (pp. 31–32): ${}^A\hat V^b_B = {}^A E_B^{-1}\,{}^A\dot E_B = \begin{bmatrix}R^T\dot R & R^T\dot t\\0&0\end{bmatrix}$, so $v^b = R^T\dot t$ and $\hat\omega^b = R^T\dot R$. "Most intuitive: this is your velocity with respect to yourself."
- **Body → spatial (adjoint)** (p. 33, boxed):
  $$\begin{bmatrix}{}^A v^s_B\\{}^A\omega^s_B\end{bmatrix} = \begin{bmatrix}{}^A R_B & {}^A\hat t_B\,{}^A R_B\\ 0 & {}^A R_B\end{bmatrix}\begin{bmatrix}{}^A v^b_B\\{}^A\omega^b_B\end{bmatrix}.$$
  Use case: you want to rotate or drive the tool about or along an axis given *in the tool frame*. Convert to base-frame velocity with this, then to joint rates.
- Philosophy (p. 34): "A point is meaningless without one coordinate frame; a velocity is meaningless without two coordinate frames." ${}^A\tilde P = {}^A E_B\,{}^B\tilde P$ is the same point seen from A and from B; $V^s = \mathrm{Ad}\,V^b$ is the same velocity seen from A and from B.
- **Manipulator Jacobian** (pp. 36–37): ${}^B\hat V^s_T = {}^B\dot E_T(q(t))\,{}^B E_T^{-1}(q(t))$. By the chain rule,
  $${}^B\hat V^s_T = \sum_{i=1}^N \Big(\frac{\partial\,{}^B E_T}{\partial q_i}\,{}^B E_T^{-1}(q)\Big)\dot q_i,$$
  which is rewritten as $\begin{bmatrix}{}^B v^s_T\\{}^B\omega^s_T\end{bmatrix} = J(q)\,\dot q$ with $J$ 6×N and configuration dependent. Column $i$ is $\big(\frac{\partial E}{\partial q_i}E^{-1}\big)^\vee$, the contribution of joint $i$ to the tool's spatial velocity.
- Inverse Jacobian (p. 39): to move the tool with a velocity expressed **in the base frame**, $\dot q = J^{-1}(q)\begin{bmatrix}{}^B v^s_T\\{}^B\omega^s_T\end{bmatrix}$.
- Singularities (p. 40): if $J$ has no inverse, then either (A) **no solution**, the velocity is impossible (stretched arm cannot move along its own axis), or (B) **infinitely many solutions**, some joints move without changing the tool velocity (collinear axes: $\dot q_1 = -\dot q_3$ gives $[v;\omega]=0$).

**Algorithms (as presented):**
1. *FK by chaining:* assign base and tool frames → one frame per joint from base to tool → write each ${}^{i-1}E_i(q_i)$ (constant translation, joint-dependent rotation for revolute) → multiply.
2. *IK:* analytic for trivial cases; otherwise Newton's method (numerical). The deck names it but gives no steps.
3. *Velocity control (implied):* desired tool velocity (body frame) → Adjoint → spatial velocity → $\dot q = J^{-1}V^s$.

**Worked examples worth redoing:** p. 16–19 (2R planar: find ${}^A P$ given ${}^C P$; do it symbolically); p. 20 (two z-revolute 3D chain: derive the boxed ${}^A E_C$); p. 22 (read $q$ from R); p. 28 (expand $\dot E E^{-1}$ block by block; good exercise); p. 33 (derive the adjoint from $\hat V^s = E\,\hat V^b E^{-1}$); p. 40 (find the two singular configurations and the null-space motion).

**Exercises worth doing:** derive $J$ for the p. 20 robot column by column from $\frac{\partial E}{\partial q_i}E^{-1}$; show $\dot RR^T$ is skew (differentiate $RR^T=I$); verify $V^s = \mathrm{Ad}_E V^b$ numerically. These match ASBR Assignment 1/2 (UR5 FK, Jacobian, resolved-rate).

**What the student must be able to do by hand:** build ${}^A E_C$ for a 2–3 joint chain; invert a homogeneous transform; compute $\hat\omega$ from $\dot R R^T$; write $V^s$ and $V^b$ from $E,\dot E$; apply the 6×6 adjoint; write $J$'s columns for a simple arm; identify singular configurations and say whether they are "no solution" or "infinitely many solutions".

**Conventions / pitfalls:**
- **Transform symbol is $E$ (Euclidean), not $g$ (MLS) or $T$ (Lynch-Park):** ${}^A E_B$ with leading superscript = reference frame, trailing subscript = frame described.
- **Twist ordering is $[v;\omega]$ (linear first)**, the same as MLS and the opposite of Lynch & Park's $[\omega; v]$.
- **The Jacobian taught is the *spatial* Jacobian** ($J$ maps $\dot q$ to ${}^B V^s_T$, the tool's spatial velocity in the base frame). The body Jacobian is never defined explicitly, but body velocities are introduced so that tool-frame commands can be converted with the adjoint.
- **Adjoint as written:** $\mathrm{Ad} = \begin{bmatrix}R & \hat t R\\0&R\end{bmatrix}$ ($\hat t R$ in the *upper-right* because $v$ comes first). In $[\omega;v]$ ordering it becomes $\begin{bmatrix}R&0\\ \hat t R & R\end{bmatrix}$.
- Slide typos: p. 19 writes ${}^A E_C = \begin{bmatrix}{}^A R_C & {}^A t_B\\0&1\end{bmatrix}$; it should be ${}^A t_C$. On pp. 16–18 the base frame's x-axis is labelled "${}^B x$"; it should be ${}^A x$. On p. 27 ${}^A R_b^{-1}$ uses a lowercase b. On p. 9 "det$(R^T)=+1$" is correct but unusual phrasing.
- $J^{-1}$ only exists for N = 6 and nonsingular configurations. For the UR5 near singularities you need damped least squares or a pseudo-inverse (not on these slides; see RDKDC R5/R6).
