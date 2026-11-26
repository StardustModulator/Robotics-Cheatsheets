# Study Plan

## How to learn (every topic)
1. Pick the thing to build.
2. Learn just enough theory to start.
3. Build it yourself. Write all the code; AI-written code is banned in RDKDC, ASBR and CIS I coursework.
4. When stuck, learn that theory properly.
5. Verify numerically (finite differences, a known library, test data).
6. Write the key equations from memory, then add them to the notes.

Work on two subjects at a time; switching between them helps retention. Sleep about 7 hours.

## Order
Foundations → RDKDC + CIS I + ASBR (same rigid-body math) alongside Deep Learning → Computer Vision → Robot Learning (needs Deep Learning first).

---

## 0. Foundations
**Learn:** Python + NumPy · linear algebra (rotations, homogeneous transforms, least squares, eigenvalues, SVD) · calculus (chain rule, gradient, Jacobian, Taylor, Lagrange multipliers, matrix calculus) · ODEs + matrix exponential · probability (Bayes, Gaussians, MLE) · PyTorch · later: C++/Eigen, ROS 2, MATLAB.
**Build:** animated 2-link arm · SVD point registration · Newton IK · pendulum with PD control · 1D Bayes/Kalman filter · regression in PyTorch on MPS.
**Where:**
- Python: CS50P (cs50.harvard.edu/python), NumPy beginner guide
- Linear algebra: 3Blue1Brown *Essence of Linear Algebra* → selected MIT 18.06SC lectures (Strang): systems, multiplication/inverse, basis, four subspaces, orthogonality, projections/least squares, Gram–Schmidt, determinants, eigenvalues, diagonalization, e^{At}, symmetric/positive-definite, SVD
- Calculus: 3Blue1Brown *Essence of Calculus* → selected MIT 18.02 / Khan multivariable: dot/cross product, partials, gradient, chain rule, linearization, max-min, Lagrange → MIT 18.S096 *Matrix Calculus for ML*
- Probability: Seeing Theory, selected Harvard Stat 110
- Reference book: *Mathematics for Machine Learning* (mml-book.github.io)
- PyTorch: pytorch.org "Learn the Basics" · C++: learncpp.com + Eigen docs · ROS 2 Jazzy: docs.ros.org in an Ubuntu 24.04 VM (UTM) or Docker · MATLAB Onramp

## 1. RDKDC: Robot Devices, Kinematics, Dynamics & Control (EN.530.646)
**Learn:** SO(3), SE(3), skew matrices, Rodrigues, twists, exponential coordinates, adjoint · forward kinematics by product of exponentials **and** DH (standard/Spong) · space & body Jacobians, singularities, manipulability · inverse kinematics (UR5's 8 analytic solutions, Newton, resolved-rate, Jacobian-transpose) · Lagrangian dynamics M(q), C(q,q̇), g(q) · PD and computed-torque control.
**Build:** rotation/transform function library → UR5 forward kinematics both ways (must match) → body Jacobian checked by finite differences → resolved-rate controller that stops near singularities → "place-and-draw" project in simulation.
**Where:** Murray–Li–Sastry ch. 2–4 (the only required text) · Lynch & Park *Modern Robotics* ch. 3–6, 8, 9, 11 + Coursera videos (your copy is the 2019 edition: `library/mr-2019-edition.md`) · Cowan 2017 lecture notes (`library/rdkdc-handwritten-notes.md`) · MR Appendix C + Keating's *UR5 Inverse Kinematics* handout for DH · course sim package `github.com/Warrior-hound/rdkdc_jazzy`.
**Watch out:** twist order is (v, ω) in MLS but (ω, v) in Modern Robotics and the current lab handout. Spring 2026 grading: 45% closed-book quizzes + midterms built from homework, 25% final; drill MLS exercises by hand.

## 2. CIS I: Computer Integrated Surgery I (EN.601.655)
**Learn:** frames F = [R, p] · point-cloud registration (SVD/Arun, quaternion/Horn) · pivot calibration · EM-tracker distortion correction (Bernstein polynomials) · closest point on a triangle mesh · k-d / bounding-sphere trees · ICP · deformable registration with a statistical shape model · error propagation, covariance, FRE/TRE · virtual fixtures · navigation and surgical robots.
**Build:** the PA1 → PA2 → PA3 → PA4 (→ PA5) pipeline, each validated against the debug data with a short report; error-propagation problems by hand.
**Where:** CIIS wiki `ciis.lcsr.jhu.edu/doku.php?id=courses:455-655` (slides, PA handouts, data) · Arun 1987, Horn 1987, Besl & McKay 1992, Fitzpatrick (FRE/TRE).
**Watch out:** the grade is mostly code quality + report + validation; never use public solution repos.

## 3. ASBR: Algorithms for Sensor-Based Robotics (EN.601.663)
**Learn:** rigid motion & velocity · resolved-rate control · hand-eye calibration AX = XB (Park–Martin) · C-space & collision checking · potential fields · A*/Dijkstra · PRM, RRT, RRT*, EST · Bayes filter, Kalman filter, EKF, particle filter (Monte Carlo localization).
**Build:** prototype each in Python → port to C++/ROS 2: resolved-rate on a UR5e · hand-eye on synthetic data · A* and potential fields on a grid · RRT/PRM for a 2-link arm · EKF fusing GPS + IMU · particle-filter localization.
**Where:** the course slides (your uploads; notes in `library/asbr-slides-part*.md`) · **Choset *Principles of Robot Motion* (primary text)** · LaValle *Planning Algorithms* (notes: `library/lavalle-part*.md`) · papers: Park–Martin, MonoSLAM, Lozano-Pérez, Kavraki PRM (`library/asbr-papers.md`) · Thrun *Probabilistic Robotics* · Modern Robotics ch. 10 · Cyrill Stachniss lectures (YouTube).
**Watch out:** assignments are C++/ROS 2/MoveIt 2; AI use = F.

## 4. Deep Learning: Machine Learning: Deep Learning (EN.601.682)
**Learn:** linear/logistic regression, softmax + cross-entropy, losses as likelihoods · backprop in matrix form · SGD, momentum, Adam · initialization, batch norm, dropout, regularization · CNNs, ResNets, transfer learning · RNN/LSTM, attention, transformers · VAE, GAN, diffusion · self-supervised learning.
**Build:** gradient derivations by hand (LaTeX) → autograd engine from scratch → NumPy MLP on MNIST with gradient checks → PyTorch CNN on CIFAR-100 with an ablation table → mini-GPT → VAE + DDPM → a small robotics project.
**Where:** Karpathy *Neural Networks: Zero to Hero* · Prince *Understanding Deep Learning* (free) · d2l.ai · Stanford CS231n notes + assignments · Goodfellow *Deep Learning* as reference.

## 5. Computer Vision (EN.601.661, confirm number on your schedule)
**Learn:** math (SVD, homogeneous coordinates, rotations/quaternions, image calculus) · pinhole model K[R | t], lens distortion, calibration (DLT, Zhang) · filtering, edges (Canny), Harris, SIFT/ORB · RANSAC, homography · epipolar geometry, F and E, 8-point, triangulation, PnP · stereo depth · optical flow (Lucas–Kanade) · photometric stereo · visual odometry, bundle adjustment · PyTorch: CNNs, ResNet, U-Net, ViT, detection, segmentation.
**Build:** calibrate your phone camera (Zhang from scratch) → Canny/Harris + RANSAC panorama stitcher → two-view 3D reconstruction (8-point) → dense stereo + Lucas–Kanade + photometric stereo → monocular visual odometry on KITTI → ResNet/U-Net/ViT from scratch + detection/segmentation fine-tuning. Classical math in NumPy first, then compare with OpenCV.
**Where:** spine = **Stanford CS231A** (geometry) + **CS231n** (deep learning) + **Szeliski 2e** (free); map in `courses/02-computer-vision/README.md` §7 · Hartley & Zisserman *Multiple View Geometry* · Cyrill Stachniss photogrammetry lectures · Shree Nayar *First Principles of Computer Vision* · CMU 16-385.

## 6. Hands-on Robot Learning (EN.601.698)
**Learn:** robot basics (servos, PID, kinematics) · teleoperation & data collection · LeRobot datasets · behavior cloning, covariate shift, DAgger · action chunking & ACT · diffusion & flow matching → Diffusion Policy · VLAs (RT-1/2, Octo, π0/π0.5, SmolVLA), action tokenization (FAST) · world models · statistical evaluation (STEP). Optional: RL basics (Q-learning, PPO).
**Build:** behavior cloning from scratch on a LeRobot dataset → diffusion/flow matching on a 2D toy → Diffusion Policy on PushT → ACT on ALOHA sim → fine-tune SmolVLA.
**Where:** course site `hands-on-robot-learning.github.io` (slides, handouts, demos) · Hugging Face LeRobot docs/repo · paper list in `courses/04-hands-on-robot-learning/README.md` · Stanford CS224R / Berkeley CS285 for RL.
**Watch out:** this is an imitation-learning course on real SO-101 arms, not an RL course; Deep Learning is a prerequisite.

---

## To confirm
- Computer Vision course number on your schedule.
- With an advisor: RDKDC and ASBR in the same semester (older manuals advise against it).
- Make the GitHub repo private (Settings → Danger Zone).
