# Cross-Course Concept Map: learn once, use many times

> The six courses share a small number of mathematical "engines." Master each engine once,
> deeply, then recognise it in every course. Notation differs between courses, so the
> **convention traps** below are where students lose points.

| # | Engine | Foundations | RDKDC | CV | DL | Robot Learning | ASBR | CIS I |
|---|---|---|---|---|---|---|---|---|
| 1 | **Rigid transforms** SO(3), SE(3), homogeneous 4×4, quaternions | F3 | **R1** core (exp coords, twists, adjoint) | extrinsics [R\|t], camera pose | — | robot state, EE pose | wk 2, A1 | **C1** frames F=[R,p], composition, inverse |
| 2 | **Jacobian / velocity kinematics** | F4 | **R3** space/body Jacobian, singularities, manipulability | — | — | IK for A1 pick-and-place | **A1** resolved-rate on UR5e | HW4 virtual fixtures (robot control) |
| 3 | **Matrix calculus / chain rule** | F4 | Lagrangian dynamics | bundle adjustment, reprojection Jacobians | **D1 backprop** (∂L/∂W = (∂L/∂P)Xᵀ) | policy loss gradients | **EKF** linearization | error propagation (HW1–3) |
| 4 | **Least squares, pseudo-inverse, SVD** | F3 | numerical IK, damped least squares | **DLT**, Zhang calibration, 8-point, E decomposition | linear regression, PCA | — | hand-eye (Park–Martin) | **SVD registration (Arun)**, pivot calibration, Bernstein fit |
| 5 | **Calibration** | — | tool/base frame setup | **camera intrinsics/distortion** | — | camera–robot calibration for SO-101 | **hand-eye AX=XB** | **pivot**, EM **distortion** correction |
| 6 | **Iterative optimization** (GD, Newton, Gauss-Newton, LM) | F4 | Newton-Raphson IK | nonlinear refinement, BA | **SGD/Adam training** | BC / diffusion training | optimization-based planning | **ICP**, deformable reg (PA5), HW4 constrained opt |
| 7 | **Probability & estimation** | F5 | — | RANSAC, noise models | losses = negative log-likelihood, VAE ELBO | stochastic policies, **STEP** statistics | **Bayes/KF/EKF/PF** | sequential Gaussian estimation (HW2), covariance propagation (HW3), FRE/TRE |
| 8 | **Search & spatial data structures** | F1 BFS | — | feature matching (kd-tree, ANN) | — | — | **A\*, Dijkstra, PRM/RRT/EST**, nearest neighbor | **closest point on mesh, kd / bounding-sphere trees** (PA3) |
| 9 | **Generative modelling** | F5 | — | — | **VAE, GAN, DDPM** | **Diffusion Policy, flow matching** | — | statistical shape model (PA5) |
| 10 | **Sequence models / transformers** | — | — | ViT (light) | **RNN, LSTM, attention, GPT** | **ACT, VLAs (π0, SmolVLA)**, action tokenization | — | — |
| 11 | **Feedback control** | F4 pendulum PD | **PD, computed torque, resolved-rate** | — | — | PID servos (SO-101) | resolved-rate | virtual fixtures |
| 12 | **Dynamics / simulation (ODEs)** | F4 | **Lagrangian M(q)q̈ + C q̇ + g = τ** | — | — | sim rollouts | motion models in KF | — |

## Recommended "engine-first" order (built into STUDY_PLAN.md)
1 → 4 → 3 → 2 → 7 → 6 → 5 → 8 → 11/12 → 9 → 10

## Convention traps (write these on your cheat sheets)
- **Twist ordering:** MLS + Cowan-era code: ξ = (v, ω). Modern Robotics: V = (ω, v). **Fall 2026 RDKDC Lab 1 handout: [ω; v].** Confirm in the first lecture.
- **DH:** RDKDC's UR5 IK handout (Keating) uses **standard (Spong-style) DH**, not Craig's modified DH. Modern Robotics covers DH only in Appendix C; MLS uses PoE throughout.
- **Space vs body Jacobian:** J_b = Ad_{g⁻¹} J_s. Lab 3 uses the **body** Jacobian.
- **Frame notation:** CIS writes F_AB / F = [R, p]; RDKDC writes g_ab ∈ SE(3); CV writes world→camera [R | t]. Same objects, different names.
- **Camera coords:** OpenCV uses x right, y down, z forward; robotics (ROS REP-103) uses x forward, y left, z up.
- **Quaternions:** (w, x, y, z) vs (x, y, z, w) ordering differs between libraries (Eigen / SciPy / ROS).
- **Rotation vs frame transform:** "rotate the point" vs "change coordinates of the point" are inverses of each other.

## Academic-integrity map (affects how we study)
| Course | GenAI policy found in research |
|---|---|
| RDKDC | Fall 2026 handouts ban GenAI tools unless stated |
| ASBR | AI tools banned; misconduct → F |
| CIS I | No AI-generated code (2025 PA3/PA4 handouts); no prior solutions; no redistribution of materials |
| DL | Not found: check syllabus |
| Robot Learning | Coding agents allowed as a *method* in A3; general policy: check syllabus |
| CV | Not found: check syllabus |

→ Hence the standing rule: **the student writes all code**; Claude teaches and reviews.
