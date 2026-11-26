# 30-Day Master Study Plan (sprint start: 2026-11-26)

> Budget: **16–17 h/day × 30 days ≈ 480 h.** Student: CS engineering grad, former SWE, covering
> everything **from first principles** (no holes). Machine: M1 Max 64 GB.
> Style: **application-first**: pick the build target, learn just enough theory to start,
> build, hit a wall, learn the theory properly, finish. Never stall more than 45 min on one thing:
> log it in `math-gaps.md` and move on.
> **Rule: the student writes all code** (see `PROGRESS.md` → Standing rules). Claude teaches, designs, reviews.

## Codes used below
Each code points to a day-plan inside a course file (section "self-study plan"):

| Code | Course file | Course-days |
|---|---|---|
| **F1–F5** | `courses/00-foundations/README.md` | Python · LinAlg I · LinAlg II · Calculus/ODE/expm · Probability/PyTorch |
| **R1–R5** | `courses/01-rdkdc/README.md` §10 | SO(3)/SE(3) toolkit · UR5 FK (DH vs PoE) · Jacobians · IK + resolved-rate · Dynamics & control |
| **V1–V5** | `courses/02-computer-vision/README.md` §9 | Calibration · Features/RANSAC/panorama · Two-view SfM · Stereo + flow · Visual odometry |
| **D1–D6** | `courses/03-ml-deep-learning/README.md` §9 | Autograd · NumPy MLP · CNNs · RNN→Transformer · Generative/SSL · Robotics capstone |
| **H1–H5** | `courses/04-hands-on-robot-learning/README.md` §9 | BC from scratch · Diffusion/flow → Diffusion Policy · ACT · Perception+IK, VLA tokens · VLA fine-tune (+RL) |
| **A1–A5** | `courses/05-algorithms-sensor-based-robotics/README.md` §9 | Rigid motion/resolved-rate/hand-eye · C-space/potentials/graph search · Sampling planners · KF/EKF · PF + ICP + C++/ROS 2 port |
| **C1–C5** | `courses/06-cis1/README.md` §9 | Frames+registration · Pivot cal + PA1 · Distortion + PA2 · Closest point + PA3 · ICP PA4 (+PA5) |

## Daily template (~16.5 h)
| Time | Block | What |
|---|---|---|
| 07:00–07:30 | Warm-up | Flashcard review (Anki) of yesterday + 3 equations written from memory |
| 07:30–14:30 | **Block A** (7 h) | Track 1: ~2 h learn → ~4.5 h build → 30 min "explain it back" |
| 14:30–15:15 | Break | Food, walk (no screens) |
| 15:15–22:15 | **Block B** (7 h) | Track 2: same pattern |
| 22:15–23:30 | Evening | Write notes/cheat-sheet lines into the course file, update `PROGRESS.md`, commit + push, plan tomorrow |
| 23:30–07:00 | Sleep | ~7 h. Non-negotiable: memory consolidation happens here |

Two different courses per day (interleaving) beats one course for a full day. Pairs are chosen so that
one block's math feeds the other.

---

## Phase 0: Foundations (Days 1–5) — full days, see `courses/00-foundations/`
| Day | Date | Focus |
|---|---|---|
| 1 | Nov 27 | **F1** Mac setup · Python end-to-end · Git · start 3B1B linear algebra |
| 2 | Nov 28 | **F2** NumPy/Matplotlib · Linear algebra I → animated 2-link arm |
| 3 | Nov 29 | **F3** Linear algebra II: 3D rotations, homogeneous transforms, least squares, eigen, **SVD** → SVD point registration |
| 4 | Nov 30 | **F4** Calculus, Jacobians, gradient descent/Newton, ODEs, **matrix exponential** → arm IK, pendulum PD control |
| 5 | Dec 1 | **F5** Probability, Bayes, Gaussians, MLE · PyTorch + autograd on MPS → hallway Bayes filter, 1D Kalman |

Exit test at end of Day 5 (in foundations file). Also: **MATLAB Onramp** (2 h, free) on the evening of Day 5.

## Phase 1: Shared core — kinematics, registration, DL basics (Days 6–12)
| Day | Date | Block A | Block B | Why this pairing |
|---|---|---|---|---|
| 6 | Dec 2 | **R1** SO(3)/SE(3) toolkit (Lab-1 functions) | **D1** Autograd from scratch + HW1 math | Both: "derivatives of compositions" |
| 7 | Dec 3 | **R2** UR5 FK: DH (Keating handout) vs PoE | **D2** NumPy MLP on MNIST + optimizers | |
| 8 | Dec 4 | **C1** Frames + SVD registration | **D3** PyTorch CNNs on CIFAR (HW4-style ablation) | C1 reuses R1 toolkit |
| 9 | Dec 5 | **R3** Jacobians, singularities, manipulability | **C2** Pivot calibration + full PA1 | |
| 10 | Dec 6 | **A1** Rigid motion → resolved-rate → hand-eye AX=XB | **V1** Image formation + calibrate phone camera | Calibration day: hand-eye + camera |
| 11 | Dec 7 | **R4** IK (8 UR5 solutions) + resolved-rate control | **V2** Filters, Harris, RANSAC → panorama | |
| 12 | Dec 8 | **C++ crash course** (Block A: syntax, memory, classes, STL, CMake, **Eigen**) | **Checkpoint**: redo week's weakest item; port SO(3)/SE(3) toolkit to C++/Eigen | ASBR assignments are C++ |

## Phase 2: Advanced (Days 13–23)
| Day | Date | Block A | Block B | Notes |
|---|---|---|---|---|
| 13 | Dec 9 | **D4** RNN/LSTM → attention → mini-GPT | **C3** Bernstein distortion correction + PA2 | |
| 14 | Dec 10 | **R5** Lagrangian dynamics, M/C/g, computed-torque & PD | **A2** C-space, potential fields, A*/Dijkstra | |
| 15 | Dec 11 | **V3** Two-view geometry: 8-point, E, triangulation | **A3** PRM / RRT / RRT* / EST | |
| 16 | Dec 12 | **D5** VAE, GAN, **DDPM**, SimCLR | **C4** Closest point on mesh + tree + PA3 | D5 is prerequisite for H2 |
| 17 | Dec 13 | **A4** Bayes filter, KF, EKF (GPS+IMU) | **V4** Stereo depth + Lucas–Kanade | |
| 18 | Dec 14 | **ROS 2 day**: Ubuntu 24.04 ARM VM (UTM/Parallels) or Docker; ROS 2 Jazzy tutorials (nodes, topics, services, tf2, launch) | Run RDKDC `rdkdc_jazzy` sim; MoveIt 2 basics; Gazebo | Needed by RDKDC labs + all ASBR assignments |
| 19 | Dec 15 | **C5** ICP (PA4) + PA5 overview | **A5** Particle filter, ICP; port one assignment to C++/ROS 2 | |
| 20 | Dec 16 | **D6** Robotics capstone + midterm-style derivation drill | **H1** LeRobot datasets + behavior cloning from scratch | DL done → robot learning starts |
| 21 | Dec 17 | **V5** Visual odometry on KITTI + PnP | **H2** Diffusion & flow matching → Diffusion Policy (PushT) | |
| 22 | Dec 18 | **H3** ACT + STEP evaluation statistics | **H4** Perception + IK pick-and-place (A1-style), FAST tokenization | |
| 23 | Dec 19 | **H5** SmolVLA fine-tune (+ optional RL half day) | **CIS homework drill**: error propagation (HW1–3 style), virtual fixtures (HW4 style) | |

## Phase 3: Integration, exam prep, readiness (Days 24–30)
| Day | Date | Block A | Block B |
|---|---|---|---|
| 24 | Dec 20 | **RDKDC final project rehearsal** (sim): place-and-draw with IK, resolved-rate, J-transpose | **ASBR A1/A2 rehearsal in C++/ROS 2** |
| 25 | Dec 21 | **CV+DL capstone**: learned detector + PnP pose of an object from your webcam | **Robot-learning capstone**: collect sim demos → train ACT/Diffusion Policy → evaluate with STEP |
| 26 | Dec 22 | **Gap day**: work through `math-gaps.md` top items | **Gap day** continued / redo weakest course-day |
| 27 | Dec 23 | **RDKDC exam prep**: MLS/MR exercises, timed, closed-book | **ASBR exam prep**: KF/EKF/PF/planning by hand, timed |
| 28 | Dec 24 | **DL exam prep**: HW1–3-style derivations (backprop, softmax/KL grads) timed | **CV exam prep**: projection, F/E, triangulation by hand |
| 29 | Dec 25 | **CIS prep**: registration/pivot/error-propagation problems; review PA report format | **HRL prep**: paper skim of must-read list; explain BC→DAgger→ACT→Diffusion→VLA story aloud |
| 30 | Dec 26 | **One-page cheat sheet per course** (6 pages) committed to repo | **Semester readiness**: environments verified (VM/ROS 2, MATLAB, LeRobot, PyTorch), open questions list, rest |

## Checkpoints (be honest; adjust the plan, don't skip them)
- **End of Day 5:** foundations exit test. If < 80%, take Day 12 Block B for remediation.
- **End of Day 12:** can you derive the UR5 FK by PoE and DH from scratch, register two point clouds, and train a CNN? If not, use Day 26 to fix.
- **End of Day 19:** all CIS PA1–PA4 pipelines working on public data; all ASBR algorithms working in Python.
- **End of Day 23:** all 31 course-days done. Days 24–30 are integration/exam prep only.

## Rules for slipping
1. If a block overruns, finish the **build** and cut the theory to its checklist; patch on Day 26.
2. Never push two days back. Absorb slippage into the evening block or Day 26.
3. Any time a course-day's build works, write the 5 most important equations/insights into that course file under "My notes".
