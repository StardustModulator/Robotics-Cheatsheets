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

---

## Deck 3: Hand-Eye Calibration (lecture03.pdf, PDF pp. 1–15), Lessons: A2 (also F3, F4, R1, C2)
**Big idea:** A sensor bolted to a robot (eye-in-hand) or watching it (eye-to-hand) is useless for control until you know the fixed transform $X$ between the sensor frame and a robot frame. You cannot measure $X$ directly, but you can move the robot and watch how the sensor's view of a fixed target changes. Writing the loop closure for two poses gives $AX = XB$. Solve the **rotation first** (closed form, via logs of rotations), then the **translation by linear least squares**. This is the Park & Martin (1994) method, and it is exactly ASBR Assignment 2's AX=XB part.

**Key definitions:**
- Hand-eye calibration (p. 4): find the transform between the sensor's coordinate frame and a coordinate frame on the robot. "With few exceptions" it must be known if sensor measurements drive robot motion.
- Eye-in-hand vs eye-to-hand (p. 3): camera on the gripper vs camera fixed in the world observing the robot.
- "Not just for robots" (p. 5): any tracked sensor, for example an optically tracked endoscope. The navigation system tracks the scope and registers it to the patient's CT, and hand-eye calibration relates the endoscope *images* to the CT. (Links to CIS I.)

**Key results & equations:**
- Data collection (p. 6): (1) move robot to pose $E_1$; (2) measure sensor pose $S_1$; (3) move to $E_2$; (4) measure $S_2$. $E_i$ comes from forward kinematics; $S_i$ is the sensor's pose measured with respect to the "world" (the calibration target, a checkerboard in the figure).
- Loop closure and derivation (p. 7):
  $$E_1 X S_1 = E_2 X S_2 \;\Rightarrow\; X S_1 S_2^{-1} = E_1^{-1}E_2 X \;\Rightarrow\; AX = XB,$$
  with $A = E_1^{-1}E_2$ (relative robot motion), $B = S_1 S_2^{-1}$ (relative sensor motion), $X$ the robot↔sensor transform.
- Families of solvers (p. 8): closed form (quaternion, angle-axis, dual quaternion), iterative (nonlinear least squares, convex optimisation), probabilistic.
- Block form (p. 9): $\begin{bmatrix}R_A&t_A\\0&1\end{bmatrix}\begin{bmatrix}R_X&t_X\\0&1\end{bmatrix}=\begin{bmatrix}R_X&t_X\\0&1\end{bmatrix}\begin{bmatrix}R_B&t_B\\0&1\end{bmatrix}$ splits into
  - rotation: $R_A R_X = R_X R_B$
  - translation: $R_A t_X + t_A = R_X t_B + t_X$.
- Matrix log (p. 10): $R(\omega,\theta)=e^{\hat\omega\theta}$; $\widehat{\log R} = \frac{\theta}{2\sin\theta}(R-R^T)$ with $\theta=\cos^{-1}\!\big(\frac{\operatorname{tr}R-1}{2}\big)$.
- Key identity (p. 11): with $\alpha_i=\log R_{A_i}$, $\beta_i=\log R_{B_i}$ (3-vectors), $R_AR_X=R_XR_B \Rightarrow \alpha_i = R_X\beta_i$. (Reason: $R_A = R_X R_B R_X^T$, and conjugation rotates the axis.)
- **Two motions, exact** (p. 11): $\mathcal A = [\alpha_1\;\alpha_2\;\alpha_1\times\alpha_2]$, $\mathcal B = [\beta_1\;\beta_2\;\beta_1\times\beta_2]$, $\mathcal A = R_X\mathcal B \Rightarrow \boxed{R_X = \mathcal A\mathcal B^{-1}}$. (The cross-product column works because $R(a\times b) = Ra\times Rb$.)
- Translation from two motions (p. 12): $(R_A - I)t_X = R_X t_B - t_A$, stacked:
  $\begin{bmatrix}R_{A_1}-I\\R_{A_2}-I\end{bmatrix}t_X = \begin{bmatrix}R_Xt_{B_1}-t_{A_1}\\R_Xt_{B_2}-t_{A_2}\end{bmatrix}$, solved by least squares.
- **N motions** (pp. 13–14): $\boxed{R_X = (M^TM)^{-1/2}M^T}$, with $M=\sum_{i=1}^N \beta_i\alpha_i^T$. $(M^TM)^{-1/2}$ via eigendecomposition: $M^TM = Q\Lambda Q^{-1}$ (symmetric positive definite), so $(M^TM)^{-1/2} = Q\,\mathrm{diag}(1/\sqrt{\lambda_1},1/\sqrt{\lambda_2},1/\sqrt{\lambda_3})\,Q^{-1}$.
- N-motion translation (p. 15): $\begin{bmatrix}I-R_{A_1}\\\vdots\\I-R_{A_N}\end{bmatrix}t_X=\begin{bmatrix}t_{A_1}-R_Xt_{B_1}\\\vdots\\t_{A_N}-R_Xt_{B_N}\end{bmatrix}$ (3N×3 least squares; same equation as p. 12 with both sides negated).

**Algorithm (Park & Martin, as on the slides):**
```
input: robot poses E_1..E_{N+1} (FK), sensor poses S_1..S_{N+1}
for each pair i: A_i = E_i^{-1} E_{i+1};  B_i = S_i S_{i+1}^{-1}
    alpha_i = vee(log R_{A_i});  beta_i = vee(log R_{B_i})
         # theta = acos((tr R - 1)/2); log R = theta/(2 sin theta) (R - R^T)
if N == 2:  R_X = [a1 a2 a1×a2] [b1 b2 b1×b2]^{-1}
else:       M = Σ beta_i alpha_i^T ;  R_X = (M^T M)^{-1/2} M^T   (eig of M^T M)
stack C = [I - R_{A_i}],  d = [t_{A_i} - R_X t_{B_i}]
t_X = argmin ||C t - d||  (pseudo-inverse / lstsq)
return X = [R_X t_X; 0 1]
```

**Worked examples worth redoing:** derive p. 7 yourself from the diagram (which side each $X$ goes on decides whether $B = S_1S_2^{-1}$ or $S_1^{-1}S_2$); derive $\alpha = R_X\beta$ from $R_A = R_XR_BR_X^T$; compute $\log R$ for a 90° rotation about z by hand; build the 6×3 translation system for 2 motions.

**Exercises worth doing:** generate a random $X$, random $A_i$, set $B_i = X^{-1}A_iX$, add noise, and recover $X$ with both the 2-motion and N-motion formulas (this is Assignment 2). Show why you need **at least two motions with non-parallel rotation axes** (one motion leaves a rotation about $\alpha_1$ undetermined, and $\mathcal B$ becomes singular if $\beta_1\parallel\beta_2$). Show $R_A - I$ is rank 2, so one motion cannot fix $t_X$.

**What the student must be able to do by hand:** derive $AX=XB$ from the pose loop; split it into rotation and translation equations; compute $\theta$ and $\log R$; assemble $\mathcal A,\mathcal B$ or $M$; write the stacked least-squares system for $t_X$; explain why the rotation must be solved first (the translation equation is linear in $t_X$ only once $R_X$ is known).

**Conventions / pitfalls:**
- $A$ and $B$ are **relative** motions: $A = E_1^{-1}E_2$, $B = S_1S_2^{-1}$. Here $S_i$ is the transform that takes target points into the sensor frame in the loop $E X S$ (base → flange → sensor → target), so the sensor side is $S_1S_2^{-1}$, not $S_1^{-1}S_2$. If your sensor gives target→camera the other way round, invert first.
- $\log R$ is singular at $\theta=0$ (no motion: discard such pairs) and at $\theta=\pi$ ($\sin\theta=0$); choose motions with moderate, well-spread rotation angles.
- $(M^TM)^{-1/2}M^T$ fails if $M$ is rank-deficient (all rotation axes parallel). With noise it can return det = −1 in degenerate cases; the SVD form $R_X = VU^T$ from $M=U\Sigma V^T$ (with a det fix) is equivalent and safer.
- The slides call it "polar decomposition"; it is the same orthogonal Procrustes solution used for point-cloud registration (CIS I C2, Arun/Horn).

---

## Deck 4: "Potential Functions" (week03.pdf, PDF pp. 1–8), Lessons: A3 (C-space part)
**What is actually in it:** Despite the title slide "Potential Functions", the 8 pages only cover **configuration space**: definition, the three fundamental issues, the 2R arm's torus C-space, and how a workspace obstacle maps into C-space. There is **no** attractive/repulsive potential, gradient descent, local-minimum, wavefront or navigation-function content in this file. It looks like the first section of a longer week-4 deck. Study potential fields themselves from Choset ch. 4 (see the corrections section).

**Big idea:** Plan in the space of *configurations*, not the workspace. In C-space the robot is a single point, and obstacles become (often strangely shaped) forbidden regions. Even a round obstacle near a 2R arm becomes a band across the whole $q_1$ range plus lobes (p. 7). The C-space of a 2R arm is a torus $S^1\times S^1$ that wraps around both horizontally and vertically, so the shortest path may go "through the edge" of the $[0,360)^2$ square.

**Key definitions:**
- Configuration (p. 2): "a *complete* specification of the position of every point in the system". C-space = the space of all configurations (formalism due to Lozano-Pérez, 1979). Example question on the slide: for a robot that only translates in the plane, what is a sufficient representation? (Answer: $(x,y)\in\mathbb{R}^2$.)
- Three fundamental issues (p. 3): (1) how to describe configurations (what is the space; relate C-space to workspace by kinematics); (2) how configurations relate to controllable inputs (holonomic vs non-holonomic, effect of dynamics); (3) how obstacles are represented in C-space: **explicitly via inverse kinematics, or implicitly inside the algorithm** (the latter is what sampling planners do with a collision checker).
- 2R arm (p. 4): C-space topology $S^1\times S^1$ (2-torus). Workspace = annulus with inner radius $L_1-L_2$ and outer radius $L_1+L_2$.

**Key results & equations:**
- 2R forward kinematics (p. 4, corrected): $x = L_1\cos q_1 + L_2\cos(q_1+q_2)$, $y = L_1\sin q_1 + L_2\sin(q_1+q_2)$.
- Workspace points A and B ↔ C-space points (p. 5); the straight segment between them in C-space is a valid joint interpolation (p. 5 dashed line). The obstacle image in C-space (p. 7) blocks that segment, so you need a planner.
- p. 8 (Ken Goldberg figure): a 2-link path among 5 polygonal obstacles and the corresponding coloured C-obstacles on $[0,360]^2$, with the path drawn as dots threading between them.

**Algorithms:** none on these slides.

**Worked examples worth redoing:** p. 5–7: for a 2R arm with given $L_1,L_2$ and a disc obstacle, compute the C-obstacle by brute force (grid over $q_1,q_2$, test each link segment against the disc) and plot it. This is the classic exam / homework exercise for A3.

**What the student must be able to do by hand:** state the definition of configuration and C-space; give C-space topology for simple robots (planar translating robot $\mathbb{R}^2$; planar rigid body $\mathbb{R}^2\times S^1$; 2R arm $T^2$; 3D rigid body $\mathbb{R}^3\times SO(3)$); sketch the workspace annulus; qualitatively sketch a C-obstacle; explain wrap-around.

**Conventions / pitfalls:**
- Slide typo, p. 4: the FK is printed as $[L_1\cos q_1;\,L_1\cos q_1] + [L_1\cos(q_1+q_2);\,L_1\cos(q_1+q_2)]$. The y-row should use $\sin$, and the second term should use $L_2$. The inner radius is $|L_1-L_2|$ (it is 0 if $L_1=L_2$).
- On pp. 5–7 the $q_1$ axis is labelled 0–180 while $q_2$ goes 0–360, so the figure only shows part of the torus. Don't conclude that $q_1$ is limited to 180°.
- Angles on these slides are in degrees; the torus identifies 0 and 360 on both axes.

---

## Deck 5: Sampling-Based Motion Planning I: Roadmaps and PRM (week05.pdf, PDF pp. 1–37, dated 10/5/2023), Lessons: A4 (and A3 for the roadmap definition)
**How it differs from week06:** both files have the same title slide, but **week05 is the PRM deck** (roadmap idea → roadmap definition → PRM algorithm → implementation choices → probabilistic-completeness proof). Week06 (next section) covers single-query tree planners. **Neither file is a graph-based planning deck** (visibility graph, GVD, cell decomposition, Minkowski sums). That material is *not* in the uploads, even though the official schedule lists it for week 5. Many slides are credited "CS 336, G.D. Hager (w/slides from S. LaValle, E. Plaku)".

**Big idea:** Building C-free explicitly is PSPACE-complete and exponential in dimension, so "no practical algorithms" exist (p. 2). The trick is never to build C-free. Instead, **sample** configurations, keep the collision-free ones, connect nearby ones with a dumb local planner, and search the resulting graph, the *roadmap*. All knowledge of obstacles comes from a collision checker, which is the "implicit in algorithm" option of week03 p. 3. PRM is a **multi-query** planner (build once, answer many start/goal queries); EST and RRT are for **single queries** (p. 17).

**Key definitions:**
- Workspace → C-space recap (p. 2): a simple workspace obstacle becomes a complex C-obstacle; the robot becomes a point; the swept volume becomes a 1-D curve.
- Roadmap idea (pp. 3–14): "a crude approximation of the collision-free space. We don't know which configurations collide, but we do have a few that do not." Every query uses the approximation instead of the real (unknown) free space, so you accept paths that are not optimal (p. 14).
- **Roadmap definition** (p. 15): a set of trajectories $f(t,q_A,q_B)$ such that any $q_{start},q_{goal}\in Q_{free}$ can be connected by a path, with three ingredients:
  - **Accessibility:** a path from $q_{start}$ to some $q'\in RM$;
  - **Departability:** a path from some $q''\in RM$ to $q_{goal}$;
  - **Connectivity:** a path in RM between $q'$ and $q''$.
- Why probabilistic methods work (p. 26): they don't construct C-free; they rely on local sampling (so they need efficient collision detection) and "dumb" local planners; they are often **probabilistically complete** ("the probability of a correct solution can be as high as desired with enough time").
- Probabilistic completeness (p. 33): if $a,b$ can be connected by a free path, then $\lim_{n\to\infty}\Pr[(a,b)\text{ FAILURE}]=0$, with $n$ = number of roadmap samples.

**Algorithms (as on the slides):**

*PRM, "The Complete Algorithm"* (p. 25, Kavraki, Švestka, Latombe, Overmars 1996):
```
0. Initialization:  add q_init and q_goal to roadmap vertex set V
1. Sampling: repeat several times
       q <- SAMPLE()
       if ISCOLLISIONFREE(q): add q to V
2. Connect samples: for each pair of neighboring samples (q_a, q_b) in V x V
       path <- GENERATELOCALPATH(q_a, q_b)
       if ISCOLLISIONFREE(path): add (q_a, q_b) to edge set E
3. Graph search: search (V, E) for a path from q_init to q_goal
```
Narrated version (pp. 20–24): sample points → discard samples in collision → connect neighbouring samples with straight-line segments → discard segments in collision → the result is the roadmap → graph-search it. Monte-Carlo motivation (p. 19): "How would you approximate π?" (794 points inside the circle vs 206 outside).

*Practical issues* (p. 27): the graph may be disconnected because C-free is disconnected or because "we didn't try hard enough (what is a hard case?)", i.e. narrow passages. The distance and local planner must be specified. To answer a query: connect start and goal like ordinary nodes, graph-search, optionally smooth. Queries can also add nodes to the graph.

*Neighbours* (p. 28): uniform sampling, but rotations must be sampled "fairly". **kd-tree:** recursively choose a plane that splits S about evenly (usually a coordinate dimension), store it at the node, recurse on $S_l,S_r$. It needs $O(dn)$ storage, is built in $O(dn\log n)$, and a query costs $O(n^{1-1/d}+m)$ for $m$ neighbours, which is asymptotically linear for large $d$. Alternative: **cell-based hashing** of each point into a grid cell.

*Distance functions* (p. 29): the distance should reflect how likely the local planner is to fail (close means likely to succeed). Ideally it would be the swept area, which is too hard to compute, so heuristics are used: Euclidean distance on an embedding of C-space, or a weighted combination of translational and rotational distance. Efficiency depends strongly on the embedding.

*Local planner* (p. 30): fast and simple. **Subdivision** on the straight C-space line: split in half, check the midpoint for collision, recurse on the halves until segments are short.

*Point robot* (p. 31): SAMPLE draws $x\sim U(\min_x,\max_x)$ and $y\sim U(\min_y,\max_y)$; the sample check is point-in-polygon; the local path is a straight segment; the path check is segment–polygon intersection.
*Serial chain* (p. 32): $\theta_i\sim U(-\pi,\pi)$. The sample check places the chain by **forward kinematics** and tests for collision. Local path: $\text{path}:[0,1]\to Q$, $\text{path}(t)=(1-t)q_a+t\,q_b$. The path check is either **incremental** (march along the path) or **subdivision** (bisect).

**Key results & equations: PRM completeness** (pp. 33–37)
- Setup (p. 34): path $\gamma:[0,1]\to Q_{free}$, $\gamma(0)=a$, $\gamma(1)=b$; $\mathrm{clr}(\gamma)$ is the minimum distance to any obstacle; $\mu$ is volume; $B_\rho(x)$ is the ball of radius $\rho$; with uniform sampling $\Pr(x\in A)=\mu(A)/\mu(Q_{free})$.
- Failure depends on (p. 33): (1) path length, (2) the path's distance to obstacles, (3) the number of roadmap configurations.
- **Theorem** (p. 36), with $Q_{free}$ an open subset of $[0,1]^d$ and a straight-line local connector:
  $$\Pr[(a,b)\text{ SUCCESS}] \ge 1-\Big\lceil\tfrac{2L}{\rho}\Big\rceil e^{-\sigma\rho^d n},\qquad \sigma=\frac{\mu(B_1(\cdot))}{2^d\,\mu(Q_{free})},$$
  where $L$ is the path length and $\rho=\mathrm{clr}(\gamma)$.
- **Proof sketch** (p. 37): $\rho>0$. Let $m=\lceil 2L/\rho\rceil$ and cover $\gamma$ with $m$ balls $B_{\rho/2}(q_i)$, $q_1=a,\dots,q_m=b$. Any $y_i\in B_{\rho/2}(q_i)$ and $y_{i+1}\in B_{\rho/2}(q_{i+1})$ both lie in $B_\rho(q_i)\subset Q_{free}$, so the segment $\overline{y_iy_{i+1}}$ is free. Let $I_i$ indicate that some sample lies in $B_{\rho/2}(q_i)$. Union bound: $\Pr[\text{FAIL}]\le\sum_i\Pr[I_i=0]$, with $\Pr[I_i=0]=(1-\mu(B_{\rho/2})/\mu(Q_{free}))^n$ and $\mu(B_{\rho/2})/\mu(Q_{free})=(\rho/2)^d\mu(B_1)/\mu(Q_{free})=\sigma\rho^d$. So $\Pr[\text{FAIL}]\le m(1-\sigma\rho^d)^n\le m\,e^{-\sigma\rho^dn}$, using $1-x\le e^{-x}$. ∎
- p. 35 figure: big ball of radius $\rho$ around $x_i$ containing the two $\rho/2$ balls around $x_i$ and $x_{i+1}$ (spacing $\rho/2$ along $\gamma$), with samples $y_i,y_{i+1}$ joined by a straight segment.

**Worked examples worth redoing:** p. 31 point-robot PRM in 2D (code it: sample, point-in-polygon, segment–polygon, Dijkstra/A*); p. 32 2R or 3R chain PRM with FK-based collision checking; reproduce the p. 37 bound and **plug in numbers** (for example $d=2$, $L=1$, $\rho=0.05$, $\mu(Q_{free})=0.8$: how many samples for 99% success?). That plug-in is a likely midterm question.

**Exercises worth doing:** implement subdivision vs incremental path checking and compare collision-check counts; build a kd-tree and verify the build and query costs; construct a narrow-passage map and watch PRM fail ("what is a hard case?").

**What the student must be able to do by hand:** write PRM pseudocode from memory; define accessibility, departability and connectivity; distinguish multi-query (PRM) from single-query (EST, RRT); state probabilistic completeness; reproduce the ball-covering proof and evaluate the bound; explain why the bound worsens with small clearance $\rho$ (narrow passages), large $d$, and long $L$; name kd-tree complexities.

**Conventions / pitfalls:**
- C-space is $Q$, free space $Q_{free}$ (Choset ch. 7 notation), and configurations are $q$. The proof assumes $Q_{free}\subset[0,1]^d$ (normalised). Angles wrap, so interpolate on the circle for revolute joints in real code. The slides' linear interpolation ignores wrap-around.
- p. 2 says "PSPACE-complete"; p. 17 says "PSPACE-hard (Reif and Canny)". Reif (1979) proved hardness and Canny (1988) gave a PSPACE algorithm, so both statements are consistent.
- p. 37 claims the $I_i$ are independent "because of uniform sampling". The union bound needs no independence; what matters is that the $n$ samples are i.i.d.
- p. 33 "for any (a,b)" really means any pair connectable by a path with positive clearance.

---

## Deck 6: Sampling-Based Motion Planning II: sampling strategies, EST, RRT, expansiveness (week06.pdf, PDF pp. 1–54, dated 10/5/2023), Lessons: A4
**How it differs from week05 (verified by rendering both PDFs and hashing every page):** week06 pp. 1–32 are **pixel-identical** to week05 pp. 1–32 (roadmap intro, PRM, neighbours, distance, local planner, point-robot and serial-chain PRM; see Deck 5). Week06 then adds **pp. 33–44** (narrow-passage sampling strategies, single-query tree planners: EST, bidirectional trees, RRT, goal bias), repeats the PRM completeness analysis on **pp. 45–49** (pp. 45–48 identical to week05 pp. 33–36; p. 49 is the proof sketch with one extra typo), and adds **pp. 50–54** (expansive spaces, narrow-corridor example, summary). So week06 is a superset of week05 and is the full "sampling-based planning" lecture; week05 is the first-day cut. **Neither is the graph-based (visibility / GVD / cell decomposition / Minkowski) deck.**

**Big idea:** Uniform sampling fails in **narrow passages**, because the chance of putting a sample inside one is proportional to its tiny volume. There are two responses. (1) Bias PRM sampling toward obstacle boundaries and gaps (OBPRM, Gaussian, bridge, visibility-based, dilated obstacles). (2) For single queries, don't map all of C-free: **grow trees** from start (and goal). EST pushes the frontier by preferring sparsely surrounded nodes; RRT pulls the tree toward random samples, which gives it a *Voronoi bias* toward unexplored space. Completeness of these planners depends on the "expansiveness" of the space rather than on an unknown path length.

**Key definitions:**
- Narrow corridor problem (pp. 33–34): the probability of finding a path is related to the *joint visibility* area under uniform sampling. "Few samples will be available in here."
- **Sampling strategies** (pp. 33–34):
  - *Bridge planner:* sample randomly; for pairs **in collision**, check whether the midpoint (or a point at a random distance) is free. ("Somehow use generalized Voronoi diagrams.")
  - *Visibility-based sampling:* keep a configuration only if it **cannot be connected to any existing component** (a guard) **or connects two existing components** (a connector).
  - *Obstacle-based PRM (OBPRM):* find samples in obstacles (uniform), pick a random direction $v$, search along $v$ for a free configuration.
  - *Gaussian sampling:* generate a random sample, then a Gaussian sample around it; keep the sample only if the Gaussian one is in collision. (Strictly, keep the *free* one of a free/colliding pair; see pitfalls.)
  - *Dilated obstacles:* allow some interpenetration to find paths more easily, then "fix up" later.
- Single-query planners (p. 38): ESTs and RRTs also work for **nonholonomic and kinodynamic** planning, since such problems are initial-condition dependent (they require integration), which makes an offline graph hard. Generally build two trees, from start and from goal, that grow toward each other; a link between them gives the path. The key design choice is a sampling strategy that focuses on unexplored free space and moves toward start or goal. For completeness you must show the algorithm eventually covers the whole space.
- **Expansiveness** (p. 50, Hsu-Latombe-Motwani): it relates the "difficulty" of the space to the number of samples, which avoids dependence on the unknown path length.
  - $\varepsilon$-good: $\forall x\in Q_{free},\ \mu(\text{reach}(x))\ge\varepsilon\,\mu(Q_{free})$ (every point sees at least an $\varepsilon$ fraction).
  - $\text{lookout}_\beta(S)$ is the set of points of $S$ that see at least a $\beta$ fraction of $Q_{free}\setminus S$.
  - $(\varepsilon,\alpha,\beta)$-expansive: $\varepsilon$-good, and for every connected $S\subseteq Q_{free}$, $\mu(\text{lookout}_\beta(S))\ge\alpha\,\mu(S)$. Intuitively, "how easy it is to choose points that link up the space".
  - Pictures (pp. 51–52): an hourglass obstacle pair. Point $x$ in the gap sees a wedge on the other side (reachability). "Only a small subset of A can see a large fraction of points in B", so $\alpha$ is small and the space is poorly expansive.
- Narrow-corridor example (p. 53): two $W\times W$ rooms joined by a corridor of length $W$ and width $w$. "Given n random samples, what are the odds the resulting graph is connected?" **This looks like an exam or homework question.** Sketch: total area $\approx 2W^2 + wW$; a sample lands in the corridor with $p\approx wW/(2W^2+wW)\approx w/(2W)$; with visibility-limited connections you need enough corridor samples to chain across, so $\Pr[\text{connected}]$ falls off like $(1-p)^n$ in the "no corridor sample" term.

**Algorithms (as on the slides):**

*Obstacle sampling* (p. 35, Amato, Bayazit, Dale, Jones, Vallejo, WAFR 1998): "move samples in collision outside the obstacle boundary".
```
GENERATECOLLISIONFREECONFIG
1: q_a <- uniform random config
2: if ISCONFIGCOLLISIONFREE(q_a): return q_a
4: else
5:   q_b <- uniform random config
6:   path <- GENERATEPATH(q_a, q_b)
7:   for t = delta to |path| by delta:
8:     if ISCONFIGCOLLISIONFREE(path(t)): return path(t)
10: return null
```
*Bridge sampling* (p. 36, Hsu, Jiang, Reif, Sun, ICRA 2003): "create a bridge between samples in collision".
```
1-2: q_a, q_b <- uniform random configs
3-4: ok_a, ok_b <- ISCONFIGCOLLISIONFREE(q_a), (q_b)
5: if ok_a == false and ok_b == false:
6:   path <- GENERATEPATH(q_a, q_b)
7:   q <- path(0.5 |path|)            # midpoint
8:   if ISCONFIGCOLLISIONFREE(q): return q
10: return null
```
*Tree search framework* (p. 39):
```
TREESEARCHFRAMEWORK(q_init, q_goal)
1: T <- ROOTTREE(q_init)
2: while q_goal not reached:
3:   q <- SELECTCONFIGFROMTREE(T)
4:   ADDTREEBRANCHFROMCONFIG(T, q)
```
The critical issues: how to select a configuration, and how to add a branch.

*EST* (p. 40, Hsu, Rock, Motwani, Latombe 1999), "push the tree frontier":
- Weight $w(q)$: a running estimate of how important it is to expand from $q$; $w(q)=\frac{1}{1+\deg(q)}$, or $1/(1+\#\text{neighbours near }q)$, or a combination.
- SELECTCONFIGFROMTREE: pick $q$ with probability $w(q)/\sum_{q'\in T}w(q')$.
- ADDTREEBRANCHFROMCONFIG: $q_{near}\leftarrow$ sample a collision-free configuration *near* $q$; path from $q$ to $q_{near}$; if the path is free, add $q_{near}$ and $(q,q_{near})$.

*Bidirectional trees* (p. 41):
```
BITREE(q_init, q_goal)
1: T_init <- tree rooted at q_init
2: T_goal <- tree rooted at q_goal
3: while solution not found:
4:   add new branch to T_init
5:   add new branch to T_goal
6:   attempt to connect neighboring configurations from the two trees
7:   if successful, return path q_init -> q_goal
```
Advantages: more efficient than one tree; growth slows down much later; fewer nodes per tree; each tree explores a different region. Different planners can grow each tree (for example RRT for one and EST for the other).

*RRT, short form* (p. 42): "RRT sampling distribution converges to uniform, which implies probabilistic completeness."
1. Choose a sample $q_{rand}$ in free space. 2. Find $q_{near}$, the closest node in $T$ to $q_{rand}$. 3. Try a point $q_{new}$ one step-size from $q_{near}$ toward $q_{rand}$; if it is collision-free, add edge $(q_{near},q_{new})$. A variation (**RRT-Connect**-style) repeatedly moves toward $q_{rand}$ as far as possible. Sampling choice: purely random guarantees completeness but is inefficient; always using $q_{goal}$ is efficient but has local minima; "the solution is to alternate randomly between these two".

*RRT, formal* (p. 43, LaValle & Kuffner 1999), "pull the tree toward random samples"; it relies on nearest neighbours and a metric $\rho: Q\times Q\to\mathbb{R}^{\ge 0}$ and gives Voronoi bias:
```
RRT(q_init, q_goal)
1: T <- tree rooted at q_init
2: while solution not found:
3:   q_rand <- random sample
4:   q_near <- nearest configuration in T to q_rand (metric rho)
5:   path <- path (not necessarily collision free) from q_near to q_rand
6:   if ISSUBPATHCOLLISIONFREE(path, 0, step):
7:     q_new <- path(step)
8:     add q_new and edge (q_near, q_new) to T
9:   if rho(q_new, q_goal) ~ 0:
10:    return path from root to q_new
```
*Goal-bias RRT* (p. 44): BASICRRT ignores $q_{goal}$, and the tree is pulled in random directions. GOALBIASRRT sets $q_{rand}=q_{goal}$ with probability $p$ and samples $Q$ uniformly otherwise; $p\approx 0.05$ is common.

**Key results & equations:** PRM completeness theorem and proof, repeated (pp. 45–49); see Deck 5. Summary (p. 54): PRM ("PRS" on the slide) is the basic multi-query planner, made efficient with sampling and evaluation tricks; EST and RRT are single-query and "generate trees and merge them"; analysis covers completeness in general, a sampling-based proof, and ideas based on the environment's structure (expansiveness).

**Worked examples worth redoing:** run bridge sampling and Gaussian sampling by hand on the p. 34 two-bar corridor (which samples survive?); simulate RRT for 5 iterations on paper with a given $q_{rand}$ sequence (step-size truncation, nearest-node choice); compute EST selection probabilities for a small tree with given degrees; the p. 53 corridor connectivity question.

**Exercises worth doing:** implement RRT with goal bias for a 2D point and a 2R arm (that is the ASBR planning assignment in spirit); compare uniform PRM and bridge-sampling PRM on a narrow corridor (success rate vs n); implement BiRRT with a CONNECT step.

**What the student must be able to do by hand:** write RRT (both versions) and EST from memory; explain the Voronoi bias (a node's selection probability is proportional to the volume of its Voronoi cell, so frontier nodes get extended); explain goal bias and the value of $p$; explain multi- vs single-query trade-offs and why trees suit kinodynamic problems; describe four narrow-passage samplers and what each keeps; define $\varepsilon$-good, lookout and $(\varepsilon,\alpha,\beta)$-expansive; argue qualitatively why the hourglass space is hard.

**Conventions / pitfalls:**
- Slide typos: p. 42 step 3 says "toward $q_{new}$", which should be "toward $q_{rand}$". "The key is how to sample $q_{new}$" means $q_{rand}$, and "$q_{new}=q_{goal}$" means $q_{rand}=q_{goal}$. p. 43 writes the metric as $\rho:Q\times Q\leftarrow\mathbb{R}^{\ge0}$ (arrow reversed), and $\rho$ is **also** the clearance in the completeness proof, so don't mix them up. p. 49 drops the $n$ in the middle term ($me^{-\sigma\rho^d}$ should be $me^{-\sigma\rho^dn}$). p. 54 "PRS" means PRM.
- Gaussian sampling, as usually defined (Boor, Overmars, van der Stappen 1999), keeps the **free** member of a pair where exactly one is in collision. The slide's wording ("keep the sample if the Gaussian sample is in collision") works only if you also require the first sample to be free.
- Basic RRT is probabilistically complete but **not asymptotically optimal**. RRT* (rewiring) and PRM* are not on these slides, though Notion lesson A4 covers RRT*.
- "RRT sampling distribution converges to uniform" refers to the distribution of tree vertices, which converges to the sampling distribution. That property is what gives probabilistic completeness.

---

## Cross-deck summary: the instructor's conventions (use these in ASBR answers)
| Item | Leonard's convention (deck / page) |
|---|---|
| Homogeneous transform | ${}^A E_B = \begin{bmatrix}{}^A R_B & {}^A t_B\\0&1\end{bmatrix}$: pose of B in A; maps ${}^B P\mapsto{}^A P$ (L02 pp. 10–11) |
| Frame names | B = base, T = tool (L02 p. 3); E = robot FK and S = sensor/target pose in hand-eye (L03 p. 6) |
| Twist / velocity ordering | $V=[v;\ \omega]$, **linear first** (MLS style) (L02 pp. 29, 32) |
| Spatial velocity | $\hat V^s = \dot E E^{-1}$; $v^s = -\dot RR^Tt+\dot t$ (L02 p. 29) |
| Body velocity | $\hat V^b = E^{-1}\dot E$; $v^b = R^T\dot t$ (L02 p. 32) |
| Adjoint | $V^s = \begin{bmatrix}R & \hat tR\\0&R\end{bmatrix}V^b$ (L02 p. 33) |
| Jacobian | **Spatial** manipulator Jacobian, 6×N: $[{}^Bv^s_T;{}^B\omega^s_T] = J(q)\dot q$; column $i = (\partial_{q_i}E\,E^{-1})^\vee$ (L02 pp. 36–37) |
| Resolved rate | $\dot q = J^{-1}(q)V^s$ (base-frame velocity); a tool-frame command goes through Ad first (L02 pp. 33, 39) |
| Hand-eye | $E_1XS_1=E_2XS_2$, $A=E_1^{-1}E_2$, $B=S_1S_2^{-1}$; rotation first, then translation LS (L03 pp. 7–15) |
| Rotation log | $\theta=\cos^{-1}((\operatorname{tr}R-1)/2)$, $\widehat{\log R}=\frac{\theta}{2\sin\theta}(R-R^T)$ (L03 p. 10) |
| Planning notation | $Q$, $Q_{free}$, $q_{init}$, $q_{goal}$, $q_{rand}$, $q_{near}$, $q_{new}$, tree $\mathcal T$, roadmap $(V,E)$ (Choset ch. 7 / Plaku slides) |

**Likely exam items seen on the slides:** "IK is NOT the inverse of FK" (L02 p. 23); the two kinds of Jacobian singularity (L02 p. 40); $v^s$ is not the velocity of the body origin (L02 p. 30); derive $AX=XB$ (L03 p. 7); Park–Martin steps (L03 pp. 9–15); C-space definition and torus wrap-around (W03 pp. 2–7); PRM pseudocode and accessibility/departability/connectivity (W05 pp. 15, 25); the PRM completeness bound and proof (W05 pp. 33–37); multi- vs single-query; RRT/EST pseudocode and goal bias $p\approx0.05$ (W06 pp. 40–44); bridge, OBPRM and Gaussian samplers (W06 pp. 33–36); expansiveness definitions (W06 p. 50); "Given n random samples, what are the odds the graph is connected?" (W06 p. 53).

---

## Corrections & upgrades for the knowledge base
- **[A1, Notion]** Notion: "Forward kinematics → **body Jacobian** → $\dot\theta=J^{-1}V$", Learn from "MR ch. 5". Slides (L02 pp. 36–39) derive and use the **spatial** Jacobian, ${}^BV^s_T=J\dot q$ with $V=[v;\omega]$ (MLS ordering), and convert tool-frame (body) commands with $\mathrm{Ad}=\begin{bmatrix}R&\hat tR\\0&R\end{bmatrix}$ (p. 33). Fix: say "spatial (base-frame) Jacobian; body velocities mapped by the adjoint", state the $[v;\omega]$ ordering, and add "Leonard lecture02 (Robot Kinematics) pp. 25–40 · MLS ch. 2.4 & 3.4" ahead of MR ch. 5 (MR uses $[\omega;v]$).
- **[A1/R2, README §5.1 line 176]** The README lists the MR ordering $(\omega,v)$ first. ASBR slides use $(v,\omega)$ (L02 pp. 29, 32–33), so make MLS ordering primary for ASBR and keep MR as the alternative.
- **[A2, Notion]** The content matches the slides ($R_X=(M^TM)^{-1/2}M^T$, $M=\sum\beta_i\alpha_i^T$, translation equation). Add: (a) the explicit relative-motion definitions $A=E_1^{-1}E_2$, $B=S_1S_2^{-1}$ from the loop $E_1XS_1=E_2XS_2$ (L03 p. 7); (b) the 2-motion closed form $R_X=[\alpha_1\ \alpha_2\ \alpha_1\times\alpha_2][\beta_1\ \beta_2\ \beta_1\times\beta_2]^{-1}$ (p. 11), which is what `axxb_closedform.m` implements; (c) computing $(M^TM)^{-1/2}=Q\Lambda^{-1/2}Q^T$ by eigendecomposition (p. 14); (d) the stacked N-motion system $[I-R_{A_i}]t_X=[t_{A_i}-R_Xt_{B_i}]$ (p. 15). Learn from: "Leonard lecture03 pp. 6–15".
- **[A2, README §5.2 line 181]** The README writes $A_i=E_1^{-1}E_{i+1}$, $B_i=S_1S_{i+1}^{-1}$ (anchored to pose 1). The slides define a single pair $A=E_1^{-1}E_2$, $B=S_1S_2^{-1}$. Both anchored and consecutive pairs satisfy $AX=XB$, but anchored pairs share pose-1 noise. Note this and keep the $S_1S_2^{-1}$ order (not $S_1^{-1}S_2$), which depends on $S$ mapping target to sensor in the loop $EXS$.
- **[A3, Notion + README §2 rows 4–5]** The uploaded "Potential Functions" deck (`week03.pdf`, 8 pp.) contains **only C-space** (definition, three issues, 2R torus, C-obstacle of a disc), with no potential-field equations. The Notion $U_{att}$/$U_{rep}$ formulas therefore cannot be verified against Leonard's slides; keep "Choset ch. 4" as the source. The schedule's week-5 **graph-based planning** (visibility graph, GVD, cell decomposition, Minkowski sums, Choset ch. 5–6) is **not in any uploaded deck**, and Notion A3 has no visibility/GVD/cell-decomposition/Minkowski content (it substitutes Dijkstra/A*). Fix: add a short visibility-graph + trapezoidal decomposition + GVD + Minkowski-sum section to A3 (sources: Choset ch. 5–6, README §5.3–5.4, `cs436/roadmaps.pdf`), and change "Learn from: Leonard's week 4–5 slides" to "Leonard week03 'Potential Functions' (C-space only) + Choset ch. 3–6".
- **[A3, Notion]** "The C-space has one dimension per joint" is fine for arms. Add the slide definition, "a complete specification of the position of every point in the system" (W03 p. 2), the torus topology and wrap-around (pp. 4–7), and "obstacles: explicit via IK or implicit in the algorithm" (p. 3).
- **[A4, Notion]** Notion lists RRT* among lecture topics. RRT* is **not in the slides**; the slides cover PRM, OBPRM/Gaussian/bridge/visibility sampling, EST, bidirectional trees, RRT, goal-bias RRT, the PRM completeness bound and expansiveness (W05/W06). Fix: mark RRT* as an extension and add the **completeness theorem** $\Pr[\text{success}]\ge1-\lceil 2L/\rho\rceil e^{-\sigma\rho^dn}$ with $\sigma=\mu(B_1)/(2^d\mu(Q_{free}))$ (W05 pp. 36–37), the **EST weight** $w=1/(1+\deg q)$ and selection $\propto w$ (W06 p. 40), goal bias $p\approx0.05$ (p. 44), narrow-passage samplers (pp. 33–36), kd-tree costs $O(dn\log n)$ build and $O(n^{1-1/d}+m)$ query (W05 p. 28), and $(\varepsilon,\alpha,\beta)$-expansiveness (W06 p. 50). Learn from: "Leonard week06 (superset of week05) pp. 15–54".
- **[A4, README §1 line 44]** "week06.pdf: PRM, RRT, EST" is correct. Add that `week05.pdf` is pages 1–32 of week06 plus the completeness proof (identical pages, verified by render hash), so there is **no separate graph-based deck**; the two uploads are two cuts of one lecture.
- **[R1, Notion RDKDC]** "Euler angles … hit gimbal lock, which **exponential coordinates** and quaternions avoid." The slide (L02 p. 9) quotes MLS: "singularities can never be eliminated in any 3-dimensional representation of SO(3)." Exponential coordinates are 3-parameter, so they also have singular or non-unique points (the log is undefined in axis at $\theta=0$ and two-valued at $\theta=\pi$; the Notion self-test even says so). Fix: "quaternions (4 numbers) avoid singularities; exponential coordinates avoid gimbal lock in the Euler sense but are still singular at $\theta=0,\pi$."
- **[R2/R5, Notion RDKDC]** The conventions match ASBR's: $(v,\omega)$ and $\mathrm{Ad}=[R\ \hat pR;0\ R]$, consistent with L02 p. 33. Add a cross-reference noting that ASBR writes $E$ for $g$ and defines $J$ via $\partial_{q_i}E\,E^{-1}$ instead of PoE twists (same matrix as $J^s$).
- **[A7 / grading, Notion callout + README §8 line 297]** Notion says "5 C++/ROS 2 assignments (47%)" and the README says "All 5 assignments are C++". Lecture01 p. 23 gives 3 UR5 assignments (27%) + 2 Jackal/Husky assignments (20%) + demos (3%) = 50%, midterm 25%, final 25%, and its tool matrix marks **Matlab** as the dominant tool (red) for **A2 and A4** and C++ for A3, with ROS in A1. The legend is not printed, so treat this as "Matlab is significant in A2 (and possibly A4)". The 27/20 UR5/mobile split is new information. Grade cutoffs (p. 24): A ≥ 94, A− ≥ 90, B+ ≥ 87 … no curve.
- **[A1 slides typo]** L02 p. 19 writes ${}^AE_C$ with ${}^At_B$ in the translation; it should be ${}^At_C$. W03 p. 4 prints the 2R FK with cos in both rows and $L_1$ twice; correct is $x=L_1c_1+L_2c_{12}$, $y=L_1s_1+L_2s_{12}$. W06 p. 42 "toward $q_{new}$" should read "toward $q_{rand}$". W06 p. 49 drops the $n$ in the exponent. Don't copy these into lessons.
- **[README §2 row 2]** "(try `week02.pdf`)": the week-2 deck is `lecture02.pdf` "Robot Kinematics" (40 pp.: SE(2)/SE(3), homogeneous transforms, FK/IK, spatial/body velocity, adjoint, spatial Jacobian, singularities). Fill in that row. Row 1 (Intro): the deck is now uploaded, so replace the Scribd pointer with `lecture01.pdf`.
- **[README §4.3 exam guesses]** Add the slide-derived likely items listed above: the PRM bound plug-in, expansiveness definitions, narrow corridor connectivity, the two singularity types, $v^s$ interpretation, and IK ≠ FK$^{-1}$.

## Best-reference map
- [A1] lecture02 pp. 6–11 (SE(3), homogeneous transforms), pp. 15–20 (FK by chaining), pp. 22–23 (IK), **pp. 26–34 (spatial and body velocity, adjoint)**, **pp. 36–40 (spatial Jacobian, inverse, singularities)**.
- [R1/R2/F3] lecture02 pp. 7–9 (SO(3), elementary rotations, representations), pp. 26–27 (hat map, $\dot RR^T$); lecture03 p. 10 (exp/log).
- [A2] **lecture03 pp. 6–15** (whole Park–Martin pipeline); p. 7 is the must-memorise derivation.
- [A3] week03 pp. 2–8 (C-space, torus, C-obstacles); week05 p. 2 (workspace vs C-space, complexity); lecture01 pp. 5–6, 18 (problem statement, static assumption). Potential fields and graph-based roadmaps are **not in these slides**: use Choset ch. 4–6.
- [A4] week05/week06 pp. 15 (roadmap definition), 25 (PRM), 27–32 (implementation choices), 33–37 / 45–49 (completeness proof); **week06 pp. 33–44** (narrow-passage samplers, EST, BiTree, RRT, goal bias), pp. 50–53 (expansiveness, corridor example), p. 54 (summary).
- [A6] lecture01 pp. 11–16 (MCL particle-cloud convergence pictures: motivation only).
- [A7] lecture01 p. 23 (assignment/tool matrix, UR5 vs Jackal split).
