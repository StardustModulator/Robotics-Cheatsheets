# 00 — Foundations (Phase 0, Days 1–5, ~80 hours)

> Student is a **CS engineering graduate and former Associate Software Engineer**, but chooses to start
> **every topic from zero** so there are **no holes**. Rule: cover everything from first principles and skip
> nothing, but move **fast** through programming/tooling (familiar territory) and spend the saved time on
> **math rigor** (linear algebra, calculus, probability), the real bottleneck for these six courses.
> Every foundation topic below is learned **by building a small robotics thing**, so that
> the math is already "in the hands" when the six courses use it.

## Why each foundation matters (where it shows up later)

| Foundation | RDKDC | CV | DL | Robot Learning | Sensor-Based (ASBR) | CIS I |
|---|---|---|---|---|---|---|
| Python + NumPy | labs (MATLAB/Python) | OpenCV assignments | every HW | every HW | every PA | PA1–PA5 |
| Vectors, matrices, matrix multiply | everything | camera projection | layers = matmuls | policies | transforms | frames |
| Rotations / rigid transforms | SO(3), SE(3), twists | extrinsics [R\|t] | — | robot state | transforms, quaternions | frame math F = [R, p] |
| Linear systems, least squares | IK, Jacobian pseudo-inverse | DLT, calibration | linear regression | — | calibration AX=XB | pivot calibration |
| Eigen / SVD | manipulability ellipsoid | 8-point algo, E decomposition | PCA | — | point registration | point-cloud registration (Arun) |
| Matrix exponential | **core** (e^{ξ̂θ}) | — | — | — | Lie groups | — |
| Derivatives, chain rule, Jacobian | velocity kinematics | bundle adjustment | **backprop** | policy gradient | EKF linearization | error propagation |
| Gradient descent / Newton | numerical IK | nonlinear refinement | **training** | RL optimization | optimization-based planning | ICP |
| ODEs, simulation | dynamics & control | — | — | simulators | motion models | — |
| Probability, Bayes, Gaussians | — | RANSAC, noise | losses = likelihoods | stochastic policies | **Kalman / particle filters** | error analysis |
| PyTorch + autograd | — | CNNs for vision | **core** | **core** | — | — |

## Mac setup (M1 Max, 64 GB) — Day 1, first hour

1. **Homebrew** → `brew install git wget cmake`
2. **Python env manager:** `brew install uv` (fast) or Miniforge (conda for arm64). One env per course later; start with `robo`:
   `uv venv ~/envs/robo && source ~/envs/robo/bin/activate`
   `uv pip install numpy scipy matplotlib jupyterlab ipykernel sympy pandas opencv-python torch torchvision mujoco gymnasium`
3. **Editor:** VS Code + Python + Jupyter extensions.
4. **Check PyTorch GPU:** `python -c "import torch; print(torch.backends.mps.is_available())"` → `True`.
5. **Git/GitHub:** configure name/email, clone this repo.
6. **Later, only when needed:** MATLAB (free via JHU license once enrolled; MATLAB Onramp is free now),
   ROS 2 via **Docker** or **RoboStack** (conda-forge ROS on osx-arm64). Native ROS on macOS is painful, so don't try it.

## Day-by-day (each day ≈ 16 h: ~6 h watch/read, ~9 h build, ~1 h write notes + commit)

### Day 1 — Python from zero (fast, complete) + Git + start Linear Algebra
- **Learn (~6 h, rigorous but quick given SWE background):** the whole language end-to-end: types, control flow,
  functions (args/kwargs, closures, lambdas), data structures & complexity, comprehensions, generators,
  classes/dataclasses/dunder methods, modules/packages, exceptions, file I/O, type hints, `pytest`,
  virtual envs, Jupyter, profiling (`timeit`, `cProfile`), Git branching/merging. Python-specific idioms
  (mutability, copy vs view, `is` vs `==`, late binding) matter later for NumPy/PyTorch bugs.
- **Then (~4 h):** begin 3Blue1Brown *Essence of Linear Algebra* ch. 1–5 (pulled forward from Day 2).
- **Resources:** Harvard **CS50P** (lectures 0–6, watch at 1.5×) — https://cs50.harvard.edu/python/ ;
  official tutorial https://docs.python.org/3/tutorial/ ; *Missing Semester* (MIT) shell + git lectures https://missing.csail.mit.edu/
- **Build:**
  1. `Robot` class with `x, y, heading`, `move(d)`, `turn(θ)`; print its path.
  2. Grid world: read a map from a text file, find a path with BFS (first taste of planning, used in ASBR).
  3. Push both to a `practice/` folder on a separate branch.

### Day 2 — NumPy + Matplotlib + Linear Algebra I (vectors & matrices as transformations)
- **Learn:** arrays, shapes, broadcasting, indexing, `@` matmul; vectors, dot product, norms,
  matrices as linear maps, matrix multiplication = composition, determinant (area scaling),
  inverse, solving Ax = b, rank, 2D rotation matrices.
- **Resources:** 3Blue1Brown *Essence of Linear Algebra* ch. 1–9 — https://www.3blue1brown.com/topics/linear-algebra ;
  NumPy absolute beginners guide https://numpy.org/doc/stable/user/absolute_beginners.html ;
  *Mathematics for Machine Learning* (Deisenroth, free PDF) ch. 2 — https://mml-book.github.io/
- **Build:**
  1. Animate a **2-link planar arm**: compute joint positions with 2D rotation matrices, plot with Matplotlib, sweep joint angles. (This is forward kinematics, used in RDKDC.)
  2. Plot its **workspace** by sampling joint angles.
  3. Transform a 2D shape by several matrices and show det = area ratio.

### Day 3 — Linear Algebra II (the "robotics core")
- **Learn:** basis/span/null space, orthogonal matrices (Rᵀ R = I, det R = +1), 3D rotations
  (about x, y, z; composition order), homogeneous transforms (4×4), eigenvalues/eigenvectors,
  symmetric matrices, **least squares** (normal equations, pseudo-inverse), **SVD**, condition number.
- **Resources:** 3B1B ch. 10–16; Gilbert Strang MIT 18.06 selected lectures (least squares, eigen, SVD) —
  https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/ ; Steve Brunton *SVD* series (YouTube);
  MML book ch. 3–4.
- **Build:**
  1. Rotate and translate a 3D cube with 4×4 homogeneous transforms; chain 3 frames (world→base→tool).
  2. Least-squares line/plane fit to noisy points (normal equations vs `np.linalg.lstsq`).
  3. **SVD image compression**, then **point-set registration**: given points P and R·P+t+noise, recover R, t with SVD (Arun's method). This is CIS I PA1 and ASBR registration in miniature.
  4. Eigen-decomposition of a 2×2 covariance: draw the ellipse.

### Day 4 — Calculus, Optimization, ODEs, Matrix Exponential
- **Learn:** derivatives, chain rule, partial derivatives, gradient, **Jacobian matrix**, Taylor
  expansion (linearization), gradient descent, Newton's method, constrained intuition (Lagrange
  multipliers), ODEs ẋ = f(x), Euler & RK4 integration, **matrix exponential** e^{At} and why
  e^{ω̂θ} is a rotation.
- **Resources:** 3B1B *Essence of Calculus*; Khan Academy multivariable calculus (gradient,
  Jacobian units); MML book ch. 5 (vector calculus) & ch. 7 (optimization);
  Modern Robotics ch. 3.2.3 (preview of matrix exponential of rotations).
- **Build:**
  1. Numerical vs analytic **Jacobian of the 2-link arm**; plot manipulability as you move.
  2. **Inverse kinematics by Newton–Raphson** (and by gradient descent) for the 2-link arm: drag a target, arm follows.
  3. Simulate a **pendulum** with Euler vs RK4 and add a PD controller to hold it upright (RDKDC control preview).
  4. Compute `scipy.linalg.expm` of a skew-symmetric matrix, verify it's a rotation, and compare with Rodrigues' formula.

### Day 5 — Probability & Statistics + PyTorch basics
- **Learn:** random variables, PMF/PDF, expectation/variance, joint/marginal/conditional,
  **Bayes' rule**, independence, **Gaussian** (1D & multivariate), covariance, sampling,
  maximum likelihood, law of large numbers. PyTorch: tensors, `requires_grad`, autograd,
  `nn.Module`, optimizer loop, `device="mps"`.
- **Resources:** *Seeing Theory* (visual probability) https://seeing-theory.brown.edu/ ;
  Harvard Stat 110 selected lectures (Blitzstein) https://projects.iq.harvard.edu/stat110 ;
  MML book ch. 6; *Probabilistic Robotics* ch. 2 (Bayes filter, a preview for ASBR);
  PyTorch "Learn the Basics" https://pytorch.org/tutorials/beginner/basics/intro.html
- **Build:**
  1. **1D hallway robot localization** with a discrete Bayes filter (move + sense updates). This is the seed of Kalman and particle filters.
  2. 1D **Kalman filter** tracking a noisy position.
  3. Monte-Carlo estimate of π; fit a Gaussian by MLE; sample a 2D Gaussian and recover its covariance.
  4. **Linear + logistic regression in PyTorch** on MPS with autograd, compared with your NumPy closed form.

## Exit test for Phase 0 (must pass before Phase 1)
- [ ] Write from memory: 3D rotation about z, a 4×4 homogeneous transform, its inverse.
- [ ] Explain eigenvectors and SVD in one sentence each; use SVD to solve a registration problem.
- [ ] Derive the 2-link arm Jacobian by hand; implement Newton IK.
- [ ] State Bayes' rule; implement a discrete Bayes filter.
- [ ] Train a model in PyTorch with autograd on MPS.
- [ ] Comfortable with Python classes, NumPy broadcasting, Matplotlib plots, git commit/push.

## Extra foundation modules scheduled later in the master plan
- **MATLAB Onramp** (free, ~2 h), evening of Day 5. RDKDC labs are MATLAB (+ ROS Toolbox); ASBR A2 used MATLAB.
- **C++ crash course**, Day 12: syntax, references/pointers, RAII, classes, templates basics, STL, CMake, **Eigen**.
  Resources: learncpp.com (chapters 0–13 selectively), Eigen "Getting started" + "Geometry module".
  Build: port your SO(3)/SE(3) toolkit to C++/Eigen with unit tests.
- **ROS 2 day**, Day 18: Ubuntu 24.04 ARM VM (UTM free / Parallels) or Docker on the M1; ROS 2 **Jazzy**
  beginner CLI + client-library tutorials (nodes, topics, services, actions, tf2, launch, colcon);
  run RDKDC's public sim package and MoveIt 2 tutorials; Gazebo basics.

## Ongoing (during Phases 1–3)
- Keep a `math-gaps.md` list; patch gaps just-in-time (≤ 45 min each) rather than up front.
- Later, when the courses need them: C++ basics (for ROS 2 / performance), MATLAB Onramp (2 h, for RDKDC labs), ROS 2 tutorials.

## Decision (Day 0): do NOT watch all of MIT 18.06 + 18.02 — curated path instead
Full 18.06 (34 lectures) + 18.02 (35 lectures) ≈ 57 h of video, 100 h+ with problem sets ≈ 20% of the
whole 480 h sprint, and most of 18.02 (multiple integrals, line integrals, Green/Stokes/divergence theorems)
is **never used** in these six courses. Rule: **visual intuition first → targeted rigorous lectures →
immediately implement in NumPy** (watching ≠ learning; coding the idea is the test).

### Linear algebra (~10 h video at 1.25–1.5× + ~10 h practice)
1. 3Blue1Brown *Essence of Linear Algebra*, all 16 chapters (~3.5 h): intuition.
2. Strang **MIT 18.06SC** (https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/), only these topics:
   geometry of linear equations · matrix multiplication & inverses · independence/basis/dimension ·
   four fundamental subspaces · orthogonality · **projections & least squares** · orthogonal matrices & Gram–Schmidt ·
   determinants (one lecture) · **eigenvalues/eigenvectors** · diagonalization & powers · **differential equations & e^{At}** ·
   **symmetric & positive-definite matrices** · **SVD**. (~14 lectures)
3. For each: do 3–5 problems from the 18.06SC recitation/problem sets **and** verify numerically in NumPy.
4. Skip for now: Markov matrices, Fourier series, complex matrices/FFT, linear transformations lecture (covered by 3B1B), pseudo-inverse lecture (covered in practice).

### Calculus (~6 h video + ~4 h matrix calculus + ~6 h practice)
1. 3Blue1Brown *Essence of Calculus* (~3 h): single-variable intuition, chain rule, Taylor.
2. MIT **18.02SC** (Auroux) / Khan Academy multivariable (Grant Sanderson), only:
   vectors, dot & **cross product** · partial derivatives · gradient & directional derivative · **chain rule** ·
   linearization/Taylor · max-min & second-derivative test · **Lagrange multipliers**. (~8 lectures)
   Skip: double/triple integrals, line integrals, Green/Stokes/divergence, Kepler, parametric curves beyond basics.
3. **Matrix calculus** (what backprop, EKF and IK actually use): MIT **18.S096 "Matrix Calculus for Machine Learning and Beyond"**
   (Edelman & Johnson, OCW) first ~4 lectures + *Mathematics for Machine Learning* ch. 5.
4. Practice: derive by hand and check with finite differences / `torch.autograd`.

### Rule for the rest of the sprint
If a course later needs a topic we skipped (e.g., integrals for probability densities), patch it just-in-time (≤ 45 min).
