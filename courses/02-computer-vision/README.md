# 02 — Computer Vision (for Robotics) at JHU

> Study guide for an incoming JHU **MSE in Robotics** student.
>
> **Conventions:** Anything marked **(unverified)** could not be confirmed from a primary source during research. Section numbers marked **(TOC from memory)** should be checked against the free PDF at <https://szeliski.org/Book>. During research, `lcsr.jhu.edu` and `e-catalogue.jhu.edu` could not be fetched directly from the research sandbox, so facts from those sites come from search-engine snippets of those pages.

---

## 1. Course identity

### 1.1 Bottom line: which course is it?

JHU has **no course titled exactly "Computer Vision for Robotics."** Nothing turned up in the catalog, the CS course listings or the LCSR advising-manual snippets.

The exact title "Computer Vision for Robotics" belongs to the **University of Toronto's ROB501** (Prof. Jonathan Kelly). That is a very geometric course, which may explain the name the student remembers. See §1.3.

The course JHU Robotics MSE students actually take is almost certainly:

> **EN.601.461 / EN.601.661 — Computer Vision** (Dept. of Computer Science; 601.661 is the graduate section; 3 credits)

Why this one:
- It is the standard JHU CV course. The **Robotics MSE advising manuals (2023-24, 2025-26 and 2026-27) list 601.661 Computer Vision among the MSE electives**, per search snippets of the PDFs.
- It is historically run out of **LCSR's CIRL lab** (Greg Hager). The CS course listing links its syllabus at `cirl.lcsr.jhu.edu/Vision_Syllabus`.
- Its official description centers on **camera modelling and 3-D geometry from stereo and motion**. That matches the student's "geometric image things" description.
- The CS dept's AI master's track groups **601.661 Computer Vision, 601.663 Algorithms for Sensor-Based Robotics and 530.646 RDKDC** under "Vision and Robotics." Those are the same courses as 01, 02 and 05 in this repo, which suggests this is the intended course set.

If the student's schedule says something different, check the alternatives in §1.3, especially **EN.520.665 Machine Perception** and **EN.520.681 Perception for Embodied AI**.

### 1.2 EN.601.461/661 Computer Vision: details

| Field | Value | Source |
|---|---|---|
| Number | EN.601.461 (undergrad), EN.601.661 (grad). Formerly 600.361/461 and 600.661 | CS listings, semester.ly, CIRL syllabus page |
| Credit rule | "Students may receive credit for at most one of 601.461/661/761" | CS Spring 2026 listing, e-catalogue |
| Area | CSCI-APPL (Applications) | cs.jhu.edu fall25/fall26 listings |
| Recent instructors | **Fall 2026: K. Katyal & G. Hager**. Spring 2026: Katyal (661). Fall 2025: Katyal & Hager (461). Fall 2024: Katyal (661). Fall 2021: **Simon Leonard**. Fall 2022: also taught by the owner of kdk132.github.io. Past terms also list C. Jones, W. Shen, H. Ali, Hager | cs.jhu.edu/fall26, /spring26, /fall25, /fall24; TA-eval PDF cs.jhu.edu/~singchun; coursicle |
| Kapil Katyal | Co-teaches the online EP version 605.661 with Greg Hager (Spring 2024 syllabus). Reported to be at JHU APL **(unverified)** | apps.ep.jhu.edu/syllabus/spring-2024/605.661.81 |
| Greg Hager | Mandell Bellmore Professor of CS, LCSR founding member, author of the course's long-running notes | Wikipedia; cs.jhu.edu/~hager |
| Offered | Usually Fall, sometimes Spring (Spring 2023 and Spring 2026 listings exist) | coursicle, cs.jhu.edu |
| Prerequisites (listing) | "intro programming, linear algebra, prob/stat". The Fall 2024 listing adds: "**Data Structures, Linear Algebra, Probability, Calc II required; Statistics, Machine Learning, Calc III, numerical optimization and Python strongly recommended**" | cs.jhu.edu/fall24, /fall17 |
| Fall 2026 grad prereq | One snippet suggests 601.661 needs "at least one graduate-level CS course in AI/ML … or instructor permission." The snippet was jumbled, so this may belong to an adjacent course **(unverified)** | cs.jhu.edu/fall26 |

**Official description (catalog):**
> "This course gives an overview of fundamental methods in computer vision from a computational perspective. Methods studied include: camera systems and their modelling, computation of 3-D geometry from binocular stereo, motion, and photometric stereo, and object recognition, image segmentation, and activity analysis. Elements of machine vision and biological vision are also included."

The EP twin (605.661) adds that "elements of machine learning and deep learning are also included." Its homework is "written and programming assignments" plus a **semester-long project**.

### 1.3 Other candidate courses

| Course | Dept | What it is | Fit to "geometric CV for robotics" |
|---|---|---|---|
| **EN.601.461/661 Computer Vision** | CS / LCSR | Classic CV: cameras, calibration, stereo, motion, recognition, plus some DL | **Best match** |
| **EN.520.665 Machine Perception** | ECE | Older versions (R. Chellappa, last listed Fall 2022): edge detectors, SIFT/LBP, NNs for image/video, **structure from motion**. The current catalog text (per snippet) is robot-sensing oriented: cameras, lidar, event cameras, IMUs, tactile | Good match if the student is in the **Perception & Cognitive Systems** track |
| **EN.520.681 Perception for Embodied AI** | ECE | New. Added to the Robotics manual in Jan 2026. Camera/lidar/event/IMU/tactile models, foundation models, learning-based methods, neural fields "in vision-based robotic systems" | Plausible "CV for robotics", but more learning-heavy. Instructor not found **(unverified)** |
| EN.601.783 Vision as Bayesian Inference | CS (Alan Yuille) | Advanced, probabilistic/ML view of vision | Not geometric |
| EN.601.483/683 Generative Vision; EN.601.492 Learning-based Vision | CS | ViTs, VAEs/GANs/diffusion, NeRF | Not geometric |
| EN.520.614/615 Image Processing & Analysis I/II; EN.520.638 Deep Learning | ECE | Listed in older Perception-track lists | Image processing, not multi-view geometry |
| 605.661 Computer Vision (EP, online) | Engineering for Professionals | **Online twin of 601.661**, taught by Katyal & Hager, Szeliski textbook, project worth 30% | Same content. Useful because its syllabus is public |
| 525.665 Machine Perception (EP, online) | EP ECE | Feature detection, stereovision, SfM, DL object detection (Fall 2025: Adam Watkins) | Similar topics, but not an on-campus MSE course |
| U. Toronto **ROB501 "Computer Vision for Robotics"** | Not JHU | Image formation, camera models and calibration, features, stereo, SfM/3D reconstruction, a little DL. Szeliski is the main text | **Exact title match**, so the student may be mixing up names. Good free reference (student notes below) |

**Robotics MSE track context:** The 2026-27 manual has a **"3.5.5 Perception and Cognitive Systems Track — Choose four (4) courses from the following list."** The visible snippets list 520.665 and 520.681. Older manuals list 520.614, 520.615 and 520.638. 601.661 appears in the elective lists. Confirm against the PDF (link in §10).

**Action for the student:** Check SIS for the exact section and instructor. If it is 601.661 with Katyal/Hager, everything below applies directly. If it is 520.665 or 520.681, §§5-9 still cover roughly 80% of the geometric core. Add more on lidar/IMU models and learned perception.

---

## 2. Lecture-by-lecture syllabus (reconstructed)

No current (2024-26) week-by-week schedule for 601.461/661 is public. The outline below combines three sources:
- (a) the official description;
- (b) the public structure of the Hager course at `ugrad.cs.jhu.edu/~cs461`: weekly schedule starting with **cameras and radiometry**, **Exam 1 around week 7** (snippet mentions **calibration**) and **Exam 2 around week 13**, "face finding" as a topic;
- (c) Hager's archived lecture-note titles (*Projective Geometry / Calibration*, *Motion*, *Computational Stereo*);
- (d) the EP 605.661 Spring 2024 topic list (motion and optical flow, ML basics, intro to DL);
- (e) older JHU versions (Vidal 2013/14: features, descriptors, matching, optical flow, affine SfM).

Treat the ordering as **(unverified)**.

| Wk | Topic | Key content | Likely source |
|---|---|---|---|
| 1 | Intro; image formation and radiometry | Pinhole model, lenses, sensors, color, radiometry basics | cs461 page ("cameras and radiometry") |
| 2 | Image processing I | Convolution/correlation, smoothing, Gaussian, derivatives, Fourier intuition, pyramids | Standard (Szeliski Ch. 3) |
| 3 | Edges, corners, lines | Canny, Harris/Shi-Tomasi, Hough transform | Standard |
| 4 | Projective geometry and camera model | Homogeneous coords, P = K[R\|t], 2D/3D transforms, vanishing points | Hager "Projective Geometry" notes |
| 5 | Camera calibration | DLT for P, Zhang's method, lens distortion, extrinsics | Hager "GeometryCalibration" notes |
| 6 | Features and matching | SIFT, ORB, descriptors, ratio test, RANSAC, homography estimation, mosaics | Vidal 2013; standard |
| 7 | **Exam 1** (around here) | Through calibration and features | cs461 schedule snippet |
| 8 | Two-view geometry | Epipolar geometry, F and E, 8-point algorithm, rectification | Standard |
| 9 | Stereo | Disparity, block matching, SGM idea, depth from disparity, photometric stereo | Catalog ("binocular stereo … photometric stereo"); Hager "Computational Stereo" |
| 10 | Motion | Optical flow (Lucas-Kanade, Horn-Schunck), tracking (KLT), motion models | Hager "Motion" notes (2012); EP 605.661 |
| 11 | Structure from motion | Triangulation, PnP, incremental SfM, bundle adjustment, affine factorization (Tomasi-Kanade), VO/SLAM link | Vidal 2014 "Affine SfM"; standard |
| 12 | Recognition and segmentation (classical + ML) | Clustering/segmentation, classifiers, face detection (Viola-Jones), "face finding" | Catalog; cs461 snippet |
| 13 | **Exam 2** (around here) | | cs461 schedule snippet |
| 13-14 | Deep learning for vision | CNNs, detection/segmentation networks, possibly learned features/depth, transformers | EP 605.661 ("intro to deep learning") |
| 14-15 | Activity analysis, biological vision; **final project presentations** | | Catalog; grading scheme |

---

## 3. Textbook mapping

**Textbooks used by the JHU course:**
- **Primary (current):** Szeliski, *Computer Vision: Algorithms and Applications*, **2nd ed., Springer 2022**. Free PDF at <https://szeliski.org/Book>. Confirmed for EP 605.661 (Katyal/Hager), and listed on the CIRL syllabus page.
- **Listed as additional references on the CIRL syllabus page:** Forsyth & Ponce, *Computer Vision: A Modern Approach*; **Hartley & Zisserman, *Multiple View Geometry in Computer Vision* (2nd ed.)**; Ma, Soatto, Košecká & Sastry, *An Invitation to 3-D Vision*.
- The course is moving to Hager's **online notes on the CIRL site** (`cirl.lcsr.jhu.edu/introduction-to-computer-vision/`).

| Topic | Szeliski 2nd ed. (TOC from memory) | Hartley & Zisserman 2nd ed. | Other |
|---|---|---|---|
| Homogeneous coords, 2D/3D transforms | §2.1 Geometric primitives & transformations | Ch. 2 (2D projective geometry), Ch. 3 (3D) | MaSKS Ch. 2-3 |
| Camera model, P = K[R\|t] | §2.1.4-2.1.6 (3D-to-2D projection, lens distortion) | **Ch. 6 Camera models** | FPCV "Image Formation" |
| Photometry, sensors, color | §2.2-2.3 | — | FPCV "Image Sensing" |
| Filtering, convolution, pyramids, Fourier | **Ch. 3** (§3.2 linear filtering, §3.4 Fourier, §3.5 pyramids) | — | FPCV "Image Processing" |
| Least squares, robust estimation, LM | Ch. 8 alignment, **Appendix A** (linear algebra / numerics) | **Ch. 4 Estimation (DLT, normalization, RANSAC, Sampson)**, App. 6 (LM, sparse LM) | |
| Edges, corners, lines, Hough | **§7.1 points & patches (Harris, SIFT)**, §7.2 edges, §7.4 lines/vanishing points | Ch. 8 (single-view, vanishing points) | |
| Feature matching, RANSAC | §7.1.3 matching, §8.1 (RANSAC, robust LS) | §4.7 RANSAC | |
| Homography, panoramas | **§8.1 pairwise alignment, §8.2 stitching, §8.4 compositing** | Ch. 4 (homography), Ch. 13 (scene planes) | |
| Camera calibration (DLT, Zhang) | **§11.1 geometric intrinsic calibration** | **Ch. 7 Computation of P**, Ch. 8 | Zhang 2000 paper |
| PnP / pose estimation | **§11.2 pose estimation** | Ch. 7 | |
| Epipolar geometry, F/E, 8-point | **§11.3 two-frame SfM**, §12.1 epipolar geometry | **Ch. 9 (F), Ch. 11 (computing F)** | MaSKS Ch. 5 |
| Triangulation | §11.2.4 or §11.3 (triangulation) | **Ch. 12 Structure computation** | |
| Projective reconstruction / self-calibration | §11.3 | Ch. 10, Ch. 19 | |
| Multi-view SfM, factorization, BA | **§11.4 multi-frame SfM (factorization, bundle adjustment)** | **Ch. 18 N-view methods (BA, affine factorization)** | MaSKS Ch. 11 |
| SLAM / visual odometry | **§11.5 SLAM** | — | Scaramuzza & Fraundorfer VO tutorial |
| Stereo, rectification, disparity | **Ch. 12** (§12.1 rectification, §12.3-12.5 dense correspondence, local/global methods, §12.6 deep stereo, §12.7 MVS) | Ch. 9 + §11.12 rectification | |
| Optical flow, tracking | **Ch. 9** (§9.1 translational alignment / LK, §9.3 optical flow) | — | Baker & Matthews "LK 20 years on" |
| Photometric stereo, shape-from-X | §13.1 shape from X | — | FPCV "Photometric Stereo" |
| Segmentation | §7.5 | — | |
| Deep learning basics, CNNs | **Ch. 5** (§5.3 deep NNs, §5.4 CNNs) | — | CS231n notes |
| Recognition, detection, segmentation nets | **Ch. 6** (§6.2 classification, §6.3 detection, §6.4 semantic segmentation) | — | |
| NeRF / neural rendering | §14.6 | — | |

---

## 4. Assignments, projects, exams

### What is documented

**Grading (Hager-format cs461 page):**
- **5-6 homework assignments, one exam, one final project**.
- Weights: **about 50% homework / 25% exam / 25% final project**.
- Homework due Wednesday midnight (online).
- The schedule snippet shows **two exam slots** (week 7, week 13). The number of exams may vary by term **(unverified)**.

**EP twin 605.661 (Katyal/Hager, Spring 2024):**
- Written + programming assignments, a **semester-long project worth 30%**, Szeliski textbook.
- Optional weekly Python notebooks (per one search summary, **unverified**).

**Older Vidal-era JHU vision course (2013):**
- Final project in **teams of two** with a **6-page report and 10-minute presentation**. Older format; may not apply now.

**Languages and tools (from past student repos):**
- **Fall 2017** (kvswim/kv_jhu_cv): 100% **Python**, HW1-HW3. A leaked HW1 spec has students write Python functions such as gray-to-binary thresholding (on homework-resale sites; do not use).
- **Fall 2020** (dlezcan1/computer-vision-fall-2020, a fork of the course repo): assignments as **Jupyter notebooks**, run locally or on **Google Colab**, submitted via **Gradescope/Blackboard**.
- **Expect Python + NumPy + OpenCV + Matplotlib, and probably PyTorch for the DL part.** The PyTorch use is **(unverified)** for the current term, but the EP Deep Learning for CV course (525.733) expects OpenCV/Python.

### Likely homework themes (reconstructed, unverified)
1. Image basics: thresholding, connected components, binary image moments/orientation. The Fall 2017 HW1 pattern is a classic "Shapiro-Stockman"-style binary vision assignment.
2. Filtering and edges: convolution, Canny/Hough line detection.
3. Features + RANSAC: Harris/SIFT matching, homography estimation, image mosaic.
4. Calibration / two-view geometry: estimate P or K, compute F with the normalized 8-point algorithm, draw epipolar lines, triangulate.
5. Stereo / optical flow: block-matching disparity, Lucas-Kanade.
6. Deep learning: train or fine-tune a CNN classifier/detector in PyTorch.

**Final project:** student-chosen, often robotics-flavored given LCSR, e.g. tracking for surgical tools, VO, 3D reconstruction, learned detection. **(unverified)**

**Exams:** expect derivation-style questions, e.g.:
- "derive the epipolar constraint"
- "how many points for F/E/H and why"
- "compute depth from disparity"
- "what does RANSAC iteration count depend on"
- "apply a 3x3 filter by hand"
- "show the Harris matrix"

This is inferred from the topic list **(unverified)**.

---

## 5. Core concept checklist with key equations

Notation: homogeneous image point **x** = (u, v, 1)^T. World point **X** = (X, Y, Z, 1)^T. `≃` means equal up to scale. `[t]×` is the skew-symmetric cross-product matrix.

### 5.1 Image formation
- [ ] **Pinhole projection:** `λ x = K [R | t] X`, with `P = K[R|t]` (3×4, 11 DOF).
- [ ] **Intrinsics:** `K = [[f_x, s, c_x],[0, f_y, c_y],[0,0,1]]`, with `f_x = f / pixel_width`. Skew s ≈ 0 for modern cameras.
- [ ] **Extrinsics:** `X_cam = R X_world + t`. The camera center is `C = -R^T t`.
- [ ] **Normalized coordinates:** `x̂ = K^{-1} x`.
- [ ] **Radial-tangential (Brown-Conrady) distortion**, which is OpenCV's model with k1, k2, p1, p2, k3, applied to normalized coords (x, y), r² = x² + y²:
  - `x_d = x(1 + k1 r² + k2 r⁴ + k3 r⁶) + 2 p1 x y + p2 (r² + 2x²)`
  - `y_d = y(1 + k1 r² + k2 r⁴ + k3 r⁶) + p1 (r² + 2y²) + 2 p2 x y`
- [ ] Vanishing points: `v = K d`, where d is the 3D direction. Horizon line = `K^{-T} n`.
- [ ] Rotation parameterizations: rotation matrix, axis-angle (Rodrigues), quaternion. `SO(3)`, `SE(3)`. Know `cv2.Rodrigues`.

### 5.2 Image processing
- [ ] **Convolution:** `(f * h)[m,n] = Σ_k Σ_l f[k,l] h[m-k, n-l]`. Correlation omits the flip. Linear and shift-invariant.
- [ ] **Gaussian:** `G_σ(x,y) = (1/2πσ²) exp(-(x²+y²)/2σ²)`. Separable, so it costs O(k) instead of O(k²) per pixel.
- [ ] Derivative filters (Sobel). Gradient magnitude/orientation. **Laplacian of Gaussian / DoG**.
- [ ] **Image calculus** (the math under Canny, Harris, LK, KLT, photometric alignment):
  - Image as a function `I(x,y)`; gradient `∇I = (I_x, I_y)`, magnitude `‖∇I‖`, direction `atan2(I_y, I_x)`; directional derivative `∇I·u`.
  - Differentiate after smoothing: `∂(G_σ * I)/∂x = (∂G_σ/∂x) * I` (derivative of Gaussian, DoG filter); finite differences amplify noise.
  - Hessian `H = [[I_xx, I_xy],[I_xy, I_yy]]` (Hessian/DoH blob detector, edge rejection in SIFT); Laplacian `∇²I = I_xx + I_yy` = trace of H.
  - First-order Taylor `I(x+Δ) ≈ I(x) + ∇I(x)ᵀΔ` gives the Harris structure tensor (SSD of a shifted window ≈ `Δᵀ M Δ`) and the LK equations; chain rule through a warp `W(x;p)`: `∂I(W(x;p))/∂p = ∇I · ∂W/∂p` (Lucas–Kanade / inverse-compositional alignment).
  - Zero crossings of the second derivative = edges (Marr–Hildreth); Canny = maxima of the first derivative along the gradient direction.
- [ ] Fourier: the convolution theorem `F{f*h} = F·H`. Aliasing and the reason to blur before downsampling. Gaussian/Laplacian pyramids.
- [ ] Nonlinear filters: median, bilateral. Morphology (erode/dilate).
- [ ] **Canny:** smooth, gradient, non-max suppression, hysteresis thresholding.
- [ ] **Hough lines:** `ρ = x cos θ + y sin θ`, voting in (ρ, θ).

### 5.3 Features
- [ ] **Harris:** structure tensor `M = Σ_w [[I_x², I_x I_y],[I_x I_y, I_y²]]`. Response `R = det M − k (tr M)²` with k ≈ 0.04-0.06. Shi-Tomasi uses `min(λ1, λ2)`.
- [ ] **SIFT:** DoG scale-space extrema, keypoint refinement and edge rejection, dominant orientation, 4×4×8 = 128-D gradient histogram descriptor.
- [ ] **ORB:** FAST + oriented rBRIEF, binary, **Hamming** distance. Fast enough for robotics (ORB-SLAM).
- [ ] Matching: nearest neighbor + **Lowe ratio test** (d1/d2 < 0.7-0.8), cross-check, FLANN/kd-tree.

### 5.4 Robust estimation and fitting
- [ ] **Least squares:** `min ||Ax − b||²` gives `x = (AᵀA)^{-1}Aᵀb`. **Homogeneous** `min ||Ah||` s.t. `||h|| = 1` gives the **last right singular vector of A (SVD)**.
- [ ] **RANSAC iterations:** `N = log(1 − p) / log(1 − w^s)`, where p = success probability, w = inlier ratio, s = minimal sample size. Examples: H: s = 4, F: 8 (or 7), E: 5, PnP: 3 (+1), line: 2.
- [ ] **Hartley normalization** (translate to centroid, scale so mean distance is √2). Essential for DLT and the 8-point algorithm.
- [ ] Errors: algebraic vs geometric (reprojection) vs **Sampson** error. Robust losses (Huber, Cauchy).
- [ ] **Gauss-Newton / Levenberg-Marquardt:** `(JᵀJ + λ I) δ = −Jᵀ r`.

### 5.5 Homography (plane-to-plane, or pure rotation)
- [ ] `x' ≃ H x`, with H 3×3, 8 DOF, so **4 point correspondences**.
- [ ] DLT rows per correspondence: `[ -xᵀ, 0ᵀ, u' xᵀ ; 0ᵀ, -xᵀ, v' xᵀ ] h = 0`. Solve by SVD.
- [ ] **Planar scene:** `H = K' (R − t nᵀ / d) K^{-1}`. **Pure rotation:** `H = K R K^{-1}`. This is why panoramas need rotation about the optical center.
- [ ] Warping: inverse mapping + bilinear interpolation. Blending (feathering, multiband).

### 5.6 Camera calibration
- [ ] **DLT for P:** ≥ 6 3D-2D correspondences (non-coplanar), 2 equations each. SVD gives P. Decompose `P = K[R|t]` via **RQ decomposition** of the left 3×3 block.
- [ ] **Zhang's method (planar checkerboard):**
  - Each view gives `H = K [r1 r2 t]`.
  - Constraints: `h1ᵀ ω h2 = 0` and `h1ᵀ ω h1 = h2ᵀ ω h2`, with `ω = K^{-T}K^{-1}` (the image of the absolute conic).
  - So ≥ 3 views give a closed-form K. Then get extrinsics, then refine everything (including distortion) by LM on reprojection error.
- [ ] Reprojection RMS: aim for < 0.5 px. OpenCV: `cv2.findChessboardCorners`, `cv2.cornerSubPix`, `cv2.calibrateCamera`, `cv2.undistort`.

### 5.7 Two-view epipolar geometry
- [ ] **Epipolar constraint:** `x'ᵀ F x = 0`. F is 3×3, rank 2, 7 DOF. Epipolar lines are `l' = F x` and `l = Fᵀ x'`. Epipoles satisfy `F e = 0` and `Fᵀ e' = 0`.
- [ ] **Essential matrix:** `E = [t]× R`, and `x̂'ᵀ E x̂ = 0` in normalized coords. `F = K'^{-T} E K^{-1}` and `E = K'ᵀ F K`. E has 5 DOF, singular values (σ, σ, 0).
- [ ] **Normalized 8-point algorithm:**
  1. Normalize points.
  2. Each correspondence gives one row `[u'u, u'v, u', v'u, v'v, v', u, v, 1]`.
  3. SVD gives f.
  4. **Enforce rank 2** by zeroing the smallest singular value.
  5. Denormalize: `F = T'ᵀ F̂ T`.
- [ ] 7-point algorithm (cubic in α). **Nistér 5-point** for calibrated E (`cv2.findEssentialMat`).
- [ ] **Decompose E:**
  - `E = U diag(1,1,0) Vᵀ`, `W = [[0,−1,0],[1,0,0],[0,0,1]]`.
  - `R ∈ {U W Vᵀ, U Wᵀ Vᵀ}`, `t = ±u3`. That gives **4 solutions**.
  - Pick the one with points in front of both cameras (**cheirality**). Translation is recovered **only up to scale** (monocular scale ambiguity).
  - In OpenCV: `cv2.recoverPose`.
- [ ] **Rectification:** warp both images so epipolar lines become horizontal scanlines.

### 5.8 Triangulation, stereo, depth
- [ ] **Linear triangulation (DLT):** from `x × (P X) = 0`, stack `u p3ᵀ − p1ᵀ` and `v p3ᵀ − p2ᵀ` for both views, then SVD. Refine by minimizing reprojection error. Also know the midpoint method. `cv2.triangulatePoints`.
- [ ] **Rectified stereo:** `Z = f B / d`, with disparity `d = x_L − x_R` and baseline B. Depth error `δZ ≈ Z² δd / (f B)`, so error grows quadratically with range.
- [ ] Matching costs: SSD, SAD, NCC, census. Block matching vs **SGM** (`cv2.StereoSGBM`). Left-right consistency check. Occlusions.
- [ ] Learned stereo/depth (concept): cost volumes, RAFT-Stereo, monocular depth nets.

### 5.9 Pose estimation, SfM, VO
- [ ] **PnP:** given n ≥ 3 (4 for a unique solution) 3D-2D matches and K, find R, t. P3P (minimal, up to 4 solutions), EPnP, iterative LM. **RANSAC-PnP** (`cv2.solvePnPRansac`).
- [ ] **Incremental SfM pipeline:**
  1. Features + matching.
  2. Two-view init via E.
  3. Triangulate.
  4. Register a new image via PnP.
  5. Triangulate new points.
  6. **Bundle adjustment**.
  7. Repeat. (COLMAP)
- [ ] **Bundle adjustment:** `min_{P_i, X_j} Σ_{ij} ρ( || x_ij − π(K_i, R_i, t_i, X_j) ||² )`. Solved by LM exploiting the sparse **Schur complement** (cameras vs points).
- [ ] **Tomasi-Kanade affine factorization:** the centered measurement matrix W (2m×n) has rank 3, `W = M S` via SVD.
- [ ] **Monocular VO loop:** track features (KLT), then compute E (5-point + RANSAC), then recoverPose, then resolve scale (from GT or a stereo baseline), then chain poses. Drift; loop closure is SLAM.
- [ ] Projective ambiguity (uncalibrated reconstruction is only up to a 4×4 H) vs metric reconstruction (calibrated, up to similarity).

### 5.10 Motion and optical flow
- [ ] **Brightness constancy:** `I(x+u, y+v, t+1) = I(x,y,t)`, which linearizes to **`I_x u + I_y v + I_t = 0`**. One equation, two unknowns gives the **aperture problem**.
- [ ] **Lucas-Kanade:** assume constant flow in a window. Solve `[[ΣI_x², ΣI_xI_y],[ΣI_xI_y, ΣI_y²]] [u v]ᵀ = −[ΣI_xI_t, ΣI_yI_t]ᵀ`. This is the Harris matrix, so it is well-conditioned at corners. Use image pyramids for large motion (`cv2.calcOpticalFlowPyrLK`).
- [ ] **Horn-Schunck:** global smoothness regularizer. Farnebäck dense flow. RAFT (deep).
- [ ] Motion field vs optical flow. Focus of expansion. Time-to-contact `τ = Z / Ż`.

### 5.11 Photometric
- [ ] Lambertian: `I = ρ n · s`. **Photometric stereo:** ≥ 3 known lights give `n` and ρ via least squares.
  - Per pixel, stack `I = S g` with `S` (k×3) the light directions and `g = ρ n`; solve `g = (SᵀS)⁻¹SᵀI` (k ≥ 3, lights not coplanar). Albedo `ρ = ‖g‖`, normal `n = g/‖g‖`.
  - Drop shadowed/saturated pixels (or weight them) before solving; specular highlights break the Lambertian model.
  - Depth from normals: `z_x = −n_x/n_z`, `z_y = −n_y/n_z`; integrate by least squares (Poisson) or **Frankot–Chellappa** (FFT projection onto integrable surfaces).
  - Uncalibrated lights → generalized bas-relief ambiguity (awareness only). Related: shape from shading (one image, ill-posed).

### 5.12 Recognition and deep learning
- [ ] Classical: bag of visual words, HOG + SVM, Viola-Jones (Haar + integral image + AdaBoost cascade) for faces. k-means / mean-shift / graph-cut segmentation.
- [ ] **Convolution layer output size:** `(W − K + 2P)/S + 1`. Parameters `= K·K·C_in·C_out (+C_out)`. Receptive field. Pooling, ReLU, BatchNorm, softmax + cross-entropy, SGD/Adam, backprop.
- [ ] Architectures: LeNet, AlexNet, VGG, **ResNet** (skip connections), U-Net (segmentation), ViT.
- [ ] Detection: R-CNN family vs YOLO/SSD. **IoU**, NMS, mAP. Segmentation: semantic vs instance (Mask R-CNN).
- [ ] Transfer learning / fine-tuning a pretrained backbone in PyTorch. Data augmentation.
- [ ] Robotics-flavored learned geometry (awareness only): SuperPoint/SuperGlue/LightGlue, learned depth, NeRF / 3D Gaussian splatting.

### 5.13 "How many points?" cheat table

| Problem | Unknown DOF | Minimal correspondences |
|---|---|---|
| 2D line | 2 | 2 points |
| Affine 2D | 6 | 3 |
| Homography | 8 | **4** |
| Fundamental F | 7 | **7** (8 linear) |
| Essential E | 5 | **5** |
| Camera P (DLT) | 11 | **6** (5.5) |
| PnP (K known) | 6 | **3** (P3P; 4 to disambiguate) |
| Triangulate X | 3 | 2 views |

---

## 6. Difficulty, reviews, tips

**Public reviews are sparse.** RateMyCourses shows **0 reviews** for EN601 661. No Reddit threads were found, and Coursicle/semester.ly evaluations were not retrievable. The Fall 2021 TA evaluation PDF (Simon Leonard's offering) shows the 661 section had about **43 students**, with a TA mean score of 4.18. That tells you nothing about difficulty.

**Inferred difficulty (unverified, based on listed prerequisites and structure):**
- **Math-heavy in the middle third.** Projective geometry, SVD-based estimation and epipolar derivations are where students without strong linear algebra struggle. The Fall 2024 listing explicitly calls "Calc III, numerical optimization and Python strongly recommended."
- **Homework is about half the grade** and is programming-heavy (notebooks). Time sink: debugging coordinate conventions (row/col vs x/y, homogeneous normalization, OpenCV BGR, `float` vs `uint8`).
- **The project counts 25-30%.** Picking a robotics project early (e.g. VO or tag-based pose for a robot arm) pays off and links to RDKDC/ASBR.

**Tips:**
1. **Master SVD.** Nearly every estimation problem is "build A, take the last column of V."
2. **Always normalize** points before DLT/8-point, and **always check reprojection error** of what you estimate.
3. Write small **unit tests with synthetic data**: make a random R, t, K and 3D points, project them, add noise, and check you recover R, t, K. This is the fastest way to debug geometry.
4. Keep a personal "conventions" header: OpenCV camera frame (x right, y down, z forward), `R, t` = world→camera, pixel (u, v) = (col, row).
5. Overlap with **EN.601.663 Algorithms for Sensor-Based Robotics** (course 05 in this repo): rotations, SE(3), least squares, point-cloud registration and pose estimation. Study them together.
6. Academic integrity: past-student repos exist (2017, 2020). Do **not** copy them. Use public courses (CMU 16-385, CS231A) for practice instead.

---

## 7. Best free resources

### Books / notes (free)
- **Szeliski, *Computer Vision: Algorithms and Applications*, 2nd ed.** (free PDF): <https://szeliski.org/Book>
- **Hartley & Zisserman, *Multiple View Geometry***: the book is not free. Sample chapters and the MATLAB functions are on the authors' page: <https://www.robots.ox.ac.uk/~vgg/hzbook/>
- **Stanford CS231A course notes** (Hata & Savarese): camera models, calibration, epipolar geometry, SfM. Concise and exactly on target: <https://web.stanford.edu/class/cs231a/> (syllabus: <https://web.stanford.edu/class/cs231a/syllabus.html>)
- **Shree Nayar, First Principles of Computer Vision**: YouTube lectures + free monographs (image formation, sensing, processing, edges, features, SfM, stereo, photometric stereo): <https://fpcv.cs.columbia.edu/> (monographs: <https://fpcv.cs.columbia.edu/Monographs>)
- **Hager's JHU CV notes** (the course's own lineage):
  - <https://cirl.lcsr.jhu.edu/introduction-to-computer-vision/>
  - <https://cirl.lcsr.jhu.edu/introduction-to-computer-vision/computational-stereo>
  - PDFs: <https://www.cs.jhu.edu/~hager/Public/teaching/cs461/Notes/GeometryCalibration.pdf>, <https://www.cs.jhu.edu/~hager/Public/teaching/cs461/Notes/LectWeek10MotionNotes.pdf>
- Scaramuzza & Fraundorfer, *Visual Odometry Tutorial*, Parts I and II (IEEE RAM 2011/2012). Widely mirrored free PDFs **(URL not verified here)**.
- U. Toronto ROB501 student notes ("Computer Vision for Robotics", Fall 2025): <https://tylertian123.github.io/notes/2025_fall/rob501_robo_cv/all_lecs.pdf> and aid sheet <https://tylertian123.github.io/notes/2025_fall/rob501_robo_cv/aidsheet.pdf>

### Lecture videos
- **Cyrill Stachniss — Photogrammetry I & II / Photogrammetric Computer Vision (Bonn)**. Best video treatment of camera models, DLT, Zhang, epipolar geometry, RANSAC, BA, VO:
  - <https://www.ipb.uni-bonn.de/photogrammetry-i-ii/>
  - <https://www.ipb.uni-bonn.de/online-training-pcv/>
  - "5 Minutes with Cyrill" (short clips, including the 8-point algorithm): <https://www.ipb.uni-bonn.de/5min/index.html>
- **Shree Nayar FPCV** (YouTube channel linked from fpcv site). Short, very clear.
- **CMU 16-385 Computer Vision** (Gkioulekas): slides + Python homework (homography/panorama, two-view geometry, stereo, optical flow, CNNs). Its assignment set is the closest public analogue to the JHU course:
  - <https://www.cs.cmu.edu/~16385/> (S2020)
  - <https://www.cs.cmu.edu/~16385/s19/>
  - <https://www.cs.cmu.edu/~16385/s18/>
- Georgia Tech CS 6476 (Hays): <https://faculty.cc.gatech.edu/~hays/compvision2025fall>
- Cornell CS5670: <https://www.cs.cornell.edu/courses/cs5670/2025fa/>
- UMD ENPM673 "Perception for Autonomous Robots" (robotics-flavored CV: calibration, VO, lane detection): <https://mage.umd.edu/enpm673>

### Code / tools
- OpenCV Python tutorials (calibration, features, homography, epipolar, stereo, optical flow): <https://docs.opencv.org/4.x/d6/d00/tutorial_py_root.html>
- **KITTI Odometry** dataset: <https://www.cvlibs.net/datasets/kitti/eval_odometry.php>
- COLMAP (reference SfM): <https://colmap.github.io/>
- PyTorch tutorials: <https://pytorch.org/tutorials/>. Stanford CS231n notes (CNNs): <https://cs231n.github.io/>

---

### Student's chosen coursework spine: CS231A + CS231n + Szeliski 2e
The student plans to cover CV with **Stanford CS231A** (3D/geometry), **Stanford CS231n** (deep learning for vision) and **Szeliski 2e**, implementing classical methods from scratch in NumPy and deep models in PyTorch. Map:

| Topic | CS231A | CS231n | Szeliski 2e | Build here |
|---|---|---|---|---|
| Linear algebra (SVD, homogeneous coords, rotations/quaternions) | linear algebra review; camera models | — | App. A; §2.1 | Foundations F3; Project 1 |
| Filtering, image calculus, Canny, Harris | (feature detection lectures) | — | §3.2, §7.1–7.2 | Project 2 |
| RANSAC homographies, stitching | fitting & matching | — | §8.1–8.2 | Project 2 |
| Camera calibration (DLT, Zhang) | camera models, calibration notes | — | §11.1 | Project 1 |
| Single-view metrology, vanishing points | single-view metrology notes | — | §11.1.1 | §5.1 checklist |
| Epipolar geometry, 8-point, triangulation, SfM | epipolar geometry, SfM notes | — | §11.2–11.4 | Project 3 |
| Dense stereo | stereo systems notes | — | Ch. 12 | Project 4 |
| Photometric stereo | active/volumetric stereo (light-based methods) | — | §13.1 | Project 4 item 6 |
| Optical flow (Lucas–Kanade) | optical flow lecture | — | §9.1, §9.3 | Project 4 |
| CNNs, ResNet | — | CNN + architectures lectures; A1–A2 | §5.3–5.4 | DL Project 3; Project 6 |
| U-Net, segmentation, detection | — | detection & segmentation lecture | §6.3–6.4 | Project 6 |
| ViTs | — | attention & transformers lecture; A3 | §5.5 | Project 6 |

Use the current CS231A/CS231n pages for exact lecture and problem-set numbers; they change by year. Do their problem sets as practice (CS231A PS1–PS4, CS231n A1–A3); never look up public solutions.

## 8. Gaps and prerequisites to fill

| Prereq | Why it matters | Quick fix |
|---|---|---|
| **Linear algebra: SVD, eigen, null space, rank, least squares, RQ/QR** | Every estimator (DLT, F, H, triangulation, Zhang) | Strang Ch. 6-7, or the Szeliski App. A refresher; implement `null_space(A)` via SVD |
| **3D rotations / rigid transforms** (SO(3), SE(3), Rodrigues, quaternions) | Extrinsics, PnP, VO; shared with RDKDC/ASBR | Lynch & Park *Modern Robotics* Ch. 3 |
| Projective geometry intuition (homogeneous coords, points at infinity, lines as 3-vectors, `l = x1 × x2`) | Homographies, epipolar lines, vanishing points | H&Z Ch. 2 (first 15 pages) |
| Nonlinear least squares (Gauss-Newton, LM, Jacobians) | Calibration refinement, BA | Szeliski App. A.3; Stachniss BA lecture |
| Probability: Gaussians, robust estimation | RANSAC analysis, noise models | — |
| **Python/NumPy fluency** (broadcasting, vectorization), OpenCV I/O, Matplotlib | All homework | Do Project 0 warm-up |
| Signals: convolution, Fourier, sampling/aliasing | Filtering, pyramids | FPCV "Image Processing" monographs |
| ML/DL basics (backprop, CNNs, PyTorch training loop) | Last third of the course + project. Overlaps with course 03 in this repo | CS231n modules 1-2 |

---

## 9. Build-first project sequence (do in order; you write all code)

**Philosophy:** each step is one **working mini-project**, with theory pulled in only as needed. Rough budget per project: about 1.5 h video/reading, 4-5 h coding, 30 min writing a one-page "equations + gotchas" note into this folder.

**Setup (do it before Project 1, about 30 min):**

```bash
python -m venv cv && source cv/bin/activate
pip install numpy scipy matplotlib opencv-contrib-python jupyter torch torchvision
```

Print a checkerboard (e.g. 9×6 inner corners, measure the square size in mm) and tape it flat to a rigid board.

**Golden rule for every project:** implement the core math **yourself in NumPy first**, then compare against the OpenCV built-in.

### Project 1: Image formation + calibrate your phone camera
- **Watch/read (about 1.5 h):**
  - Stachniss "camera parameters / DLT / Zhang" videos, or FPCV "Image Formation" + "Camera Calibration".
  - Szeliski §2.1, §11.1.
  - CS231A notes "Camera Models" + "Calibration".
- **Build:**
  1. Write `project(K, R, t, X)` and a synthetic test: random K, R, t and points, then project.
  2. **DLT camera resection** from synthetic 3D-2D pairs (with Hartley normalization) → `P`, then RQ → `K, R, t`. Verify you recover the ground truth under noise.
  3. **Real calibration:**
     - Take 15-25 phone photos of the checkerboard at varied tilts and distances. Lock focus if possible.
     - `findChessboardCorners` + `cornerSubPix` → `calibrateCamera`.
     - Report `K`, distortion coeffs and RMS reprojection error.
     - `undistort` a photo with straight lines and compare side by side.
  4. **Zhang's method from scratch (required, not a stretch):** DLT homography per view → build the `v_ij` rows → SVD for `b` (ω) → closed-form `K` → per-view `[r1 r2 t]` (re-orthonormalize R by SVD) → estimate `k1, k2` linearly → refine all parameters with `scipy.optimize.least_squares` on reprojection error. Compare `K`, distortion and RMS with OpenCV.
  5. Stretch: draw a 3D cube on the checkerboard (`solvePnP` + `projectPoints`). This is your first AR and PnP demo.
- **Self-check:**
  - Why does Zhang need ≥ 3 views?
  - What is `C` in terms of R, t?
  - What does a big k1 do to straight lines?

### Project 2: Filtering, features, RANSAC → panorama stitcher
- **Watch/read:**
  - FPCV "Image Processing" + "Edge Detection" + "SIFT".
  - Szeliski §3.2, §7.1, §8.1-8.2.
  - CMU 16-385 homography lecture.
- **Build:**
  1. NumPy `conv2d` (correlate vs convolve), Gaussian kernel, Sobel. Verify against `cv2.filter2D`.
  2. **Canny from scratch:** derivative-of-Gaussian gradients → non-max suppression along the quantized gradient direction → double threshold → hysteresis (connected components or BFS). Compare with `cv2.Canny`.
  3. **Harris detector** from scratch (structure tensor → R → NMS). Overlay on an image.
  4. SIFT/ORB via OpenCV, plus BFMatcher + ratio test.
  5. **Homography by DLT** (normalized) **+ your own RANSAC loop**. Compute N from the formula. Compare with `cv2.findHomography(..., cv2.RANSAC)`.
  6. **Stitch 3 phone photos** (rotate about the camera center). Inverse-warp with `cv2.warpPerspective` onto a common canvas, then simple feather blending.
  7. Stretch: **cylindrical warp** using your Project 1 K. Explain why `H = K R K^{-1}` for pure rotation.
- **Self-check:**
  - Minimum matches for H?
  - RANSAC iterations needed for 50% inliers at 99%?
  - Why normalize?

### Project 3: Two-view geometry → two-view SfM
- **Watch/read:**
  - Stachniss "epipolar geometry / F / E / 8-point / triangulation".
  - CS231A notes "Epipolar Geometry".
  - H&Z Ch. 9 (skim), Ch. 11.1-11.3, Ch. 12.2.
  - Szeliski §11.3.
- **Build** (use two photos of a textured static scene taken with your calibrated phone, or the Middlebury/ETH3D two-view pairs):
  1. Match features (SIFT + ratio test).
  2. **Normalized 8-point F** from scratch + rank-2 enforcement + RANSAC with Sampson distance. Plot **epipolar lines** in both images and check that points lie on them.
  3. `E = Kᵀ F K`. Re-project E onto (σ, σ, 0). **Decompose into 4 (R, t)** and choose by cheirality. Compare with `cv2.findEssentialMat` + `cv2.recoverPose`.
  4. **Linear triangulation** (your own DLT) → 3D point cloud. Plot with Matplotlib 3D (or export to `.ply` and view in MeshLab/Open3D).
  5. Compute the mean reprojection error. Stretch: refine with `scipy.optimize.least_squares` (a tiny **two-view bundle adjustment**).
- **Self-check:**
  - Why is the reconstruction only up to scale?
  - What is the rank of F and why?
  - Where are the epipoles?

### Project 4: Stereo depth + optical flow
- **Watch/read:**
  - FPCV "Stereo" + "Optical Flow".
  - Szeliski §12.1-12.4, §9.1, §9.3.
  - Hager "Computational Stereo" + "Motion" notes.
- **Build:**
  1. **Rectified stereo:**
     - Take a Middlebury pair (or KITTI stereo). Implement **SSD/NCC block matching** in NumPy (vectorized via shifted images) → disparity map.
     - Compare with `cv2.StereoSGBM`.
     - Convert to depth with `Z = f B / d`. Plot the error vs range intuition.
  2. Add a left-right consistency check. Visualize the occlusion holes.
  3. **Lucas-Kanade from scratch** (single level, then pyramidal) on corners from your Harris detector. Compare with `cv2.calcOpticalFlowPyrLK`.
  4. **KLT tracker on a phone video:** detect `goodFeaturesToTrack`, track frame-to-frame, re-detect when the count drops. Draw tracks. This is the front end for Project 5.
  5. Stretch: Farnebäck dense flow, HSV-coded visualization. Rectify your own phone stereo pair with `stereoRectify`.
  6. **Photometric stereo in NumPy:** render a synthetic Lambertian sphere under 4–8 known lights first (exact answer known), then use a real dataset (e.g. DiLiGenT, or the classic CMU/Harvard photometric-stereo image sets). Recover albedo and normal maps by per-pixel least squares with a shadow mask, then integrate depth with Frankot–Chellappa. Report the mean angular normal error on the sphere.
- **Self-check:**
  - Derive the optical-flow constraint equation.
  - When is LK ill-conditioned? (Hint: Harris.)
  - Why does depth error grow as Z²?

### Project 5: Monocular visual odometry on KITTI + PnP + DL refresher
- **Watch/read:**
  - Scaramuzza & Fraundorfer VO tutorial Part I.
  - Szeliski §11.2, §11.4-11.5.
  - Stachniss "Bundle Adjustment".
- **Build (morning, about 4 h): monocular VO** on KITTI Odometry sequence 00, grayscale left camera (`P0` from `calib.txt` gives K):
  1. FAST/ORB detection → KLT tracking (from Project 4).
  2. `findEssentialMat` (RANSAC) → `recoverPose` → **scale from ground-truth poses** (the standard teaching trick; state it explicitly).
  3. Chain `T_k = T_{k-1} · T_{k,k-1}`.
  4. Plot the x-z trajectory vs ground truth. Compute the drift.
  5. Stretch A: **stereo VO**. Triangulate from the left/right pair, then PnP-RANSAC (`solvePnPRansac`) on the next frame. No scale ambiguity.
  6. Stretch B: a small sliding-window BA with `scipy.optimize.least_squares` + Jacobian sparsity.
- **Build (afternoon, about 2 h): DL refresher.**
  - Fine-tune a pretrained ResNet-18 on a tiny dataset (e.g. CIFAR-10 subset) in PyTorch.
  - Or run a pretrained detector (torchvision Faster R-CNN). Compute IoU and NMS by hand on its outputs.
  - Compute output sizes and parameter counts for 3 conv layers by hand.
- **Wrap-up (1 h):**
  - Fill in the §5 checklist boxes.
  - Write a one-page sheet of every equation in §5 from memory.
  - Revisit whatever you could not derive.

### Project 6: Deep learning for vision in PyTorch (ResNet, U-Net, ViT, detection, segmentation)
Prerequisite: DL course Project 3 (`courses/03-ml-deep-learning/README.md` §9), which covers the training loop, CNN basics and CIFAR ablations. Do not repeat those; this project is about vision architectures and dense/structured outputs.
- **Watch/read:** CS231n lectures on CNN architectures, detection & segmentation, attention/transformers; Szeliski §5.3–5.5, §6.3–6.4; papers: ResNet (He 2016), U-Net (Ronneberger 2015), ViT (Dosovitskiy 2021), Faster R-CNN (Ren 2015), Mask R-CNN (He 2017), DETR (Carion 2020) skim.
- **Build:**
  1. **ResNet-18 from scratch** (BasicBlock, downsampling shortcut, BN); match the parameter count of `torchvision.models.resnet18`; train on CIFAR-10.
  2. **U-Net from scratch** for semantic segmentation (e.g. Oxford-IIIT Pets trimaps or a small tabletop/robot dataset): encoder–decoder with skip concatenation, CE + Dice loss, report mIoU.
  3. **ViT from scratch** (patch embedding as a strided conv, [CLS] token, learned position embeddings, Pre-LN transformer blocks); train a tiny ViT on CIFAR-10 and compare with ResNet-18 at equal compute; note the data-hunger without augmentation.
  4. **Detection:** implement IoU, NMS and mAP@0.5 yourself; fine-tune `torchvision` Faster R-CNN on a small dataset (e.g. Penn-Fudan pedestrians); evaluate with your own mAP and check against `torchmetrics`.
  5. **Instance segmentation:** fine-tune Mask R-CNN on the same data; compare semantic (U-Net) vs instance masks.
- **Self-check:** why do residual connections help (gradient of `x + F(x)`)? Why does U-Net need skips? Parameter count of a ViT block? What does NMS do to recall when objects overlap? Anchor-based vs DETR set prediction?

### Optional micro-sessions (20–30 min each, any time later)
- AprilTag/ArUco pose estimation (`cv2.aruco`). Directly useful for robot arms (links to RDKDC/ASBR).
- Run COLMAP on 30 photos of an object. Compare with your Project 3 pipeline.
- Read a SuperPoint/LightGlue README and try the demo on your Project 2 images.
- Hand-solve 5 exam-style problems (§4 list).

### Deliverables to keep in this folder (suggested)

```
02-computer-vision/
  README.md            <- this file
  day1_calibration/    calib.ipynb, K.npy, dist.npy, undistort_before_after.png
  day2_panorama/       harris.py, ransac_homography.py, panorama.jpg
  day3_two_view/       eight_point.py, triangulate.py, cloud.ply
  day4_stereo_flow/    block_match.py, lk.py, klt_tracks.mp4
  day4_stereo_flow/    + photometric_stereo.py, normals.png, depth.png
  day5_vo/             mono_vo.py, traj_vs_gt.png
  day6_dl_vision/      resnet18.py, unet.py, vit.py, detection_eval.py
```

---

## 10. Sources

**JHU course identity / listings**
- JHU CS Fall 2026 courses (601.461/661, Katyal & Hager): <https://www.cs.jhu.edu/fall26/>
- JHU CS Spring 2026 courses (601.661 Katyal; syllabus link; credit rule): <https://www.cs.jhu.edu/spring26/>
- JHU CS Fall 2025 courses (601.461 Katyal & Hager): <https://www.cs.jhu.edu/fall25/>
- JHU CS Fall 2024 courses (601.661 Katyal; prerequisites text): <https://www.cs.jhu.edu/fall24/>
- JHU CS Fall 2017 courses (prereqs): <https://www.cs.jhu.edu/fall17/>
- EN.601 catalog (description, credit restriction): <https://e-catalogue.jhu.edu/course-descriptions/computer_science_601/> and PDF <https://e-catalogue.jhu.edu/course-descriptions/computer_science_601/computer_science_601.pdf>
- Catalog search 601.461 / 601.661: <https://e-catalogue.jhu.edu/search/?P=EN.601.461>, <https://e-catalogue.jhu.edu/search/?P=EN.601.661>
- Semester.ly: <https://jhu.semester.ly/c/EN.601.661>, <https://jhu.semester.ly/c/EN.601.461>, <https://jhu.semester.ly/c/EN.600.661>
- Coursicle: <https://www.coursicle.com/jhu/courses/EN601/661/>, <https://www.coursicle.com/jhu/courses/EN601/461/>
- CIRL syllabus page (Hager; textbooks): <https://cirl.lcsr.jhu.edu/vision_syllabus/>
- Hager-format course page (grading, schedule): <https://www.ugrad.cs.jhu.edu/~cs461>
- CIRL online notes: <https://cirl.lcsr.jhu.edu/introduction-to-computer-vision/>
- Hager lecture PDFs:
  - <https://www.cs.jhu.edu/~hager/Public/teaching/cs461/CVIntroLecture.pdf>
  - <https://www.cs.jhu.edu/~hager/Public/teaching/cs461/Notes/GeometryCalibration.pdf>
  - <https://www.cs.jhu.edu/~hager/Public/teaching/cs461/Notes/LectWeek10MotionNotes.pdf>
- Older JHU vision course (Vidal): <http://www.vision.jhu.edu/teaching/vision/vision13/>, <http://www.vision.jhu.edu/teaching/vision/vision14/>
- Fall 2021 TA evaluations (instructor Simon Leonard): <https://www.cs.jhu.edu/~singchun/doc/Fall2021-461-TA.pdf>, <https://www.cs.jhu.edu/~singchun/doc/Fall2021-661-TA.pdf>
- Fall 2022 instructor teaching page: <https://kdk132.github.io/teaching>
- RateMyCourses JHU EN601: <https://www.ratemycourses.io/jhu/department/en601>
- CS AI master's track ("Vision and Robotics" group): <https://www.cs.jhu.edu/academic-programs/graduate-studies/masters-computer-science/ai-masters-track/>
- Wikipedia, Gregory D. Hager: <https://en.wikipedia.org/wiki/Gregory_D._Hager>

**EP online twin**
- 605.661 course page: <https://ep.jhu.edu/courses/605661-computer-vision/>
- 605.661 Spring 2024 syllabus (Katyal & Hager, Szeliski, project 30%): <https://apps.ep.jhu.edu/syllabus/spring-2024/605.661.81>
- 525.665 Machine Perception (EP): <https://apps.ep.jhu.edu/syllabus/spring-2025/525.665.81>, <https://apps.ep.jhu.edu/syllabus/fall-2025/525.665.81>
- 525.733 Deep Learning for CV (EP): <https://apps.ep.jhu.edu/syllabus/spring-2024/525.733.8VL2>

**Robotics MSE manuals / alternatives**
- 2026-27 Robotics Advising Manual (July 6, 2026 update): <https://lcsr.jhu.edu/wp-content/uploads/2026/07/2026-2027-Robotics-Advising-Manual-July.6.2026-UPDATE.pdf>
- 2026-27 manual (March): <https://lcsr.jhu.edu/wp-content/uploads/2026/03/2026-2027-Robotics-Advising-Manual.pdf>
- 2025-26 manual: <https://lcsr.jhu.edu/wp-content/uploads/2025/10/2025-2026-Robotics-Advising-Manual-August-2025.pdf>
- 2023-24 manual: <https://lcsr.jhu.edu/wp-content/uploads/2025/10/2023-2024-Robotics-Advising-Manual-9.12.23.pdf>
- Robotics MSE program page: <https://lcsr.jhu.edu/education/robotics-masters-program/robotics-mse-program/>
- EN.520 catalog (520.665, 520.681): <https://e-catalogue.jhu.edu/course-descriptions/electrical___computer_engineering/>
- Coursicle EN520 665: <https://www.coursicle.com/jhu/courses/EN520/665/>
- U. Toronto ROB501 calendar entry: <https://engineering.calendar.utoronto.ca/node/23459>

**Past student repos (reference only; do not copy)**
- <https://github.com/kvswim/kv_jhu_cv> (Fall 2017, Python)
- <https://github.com/dlezcan1/computer-vision-fall-2020> (Fall 2020, Jupyter/Colab)

**Free learning resources**
- Szeliski 2nd ed.: <https://szeliski.org/Book>, Springer: <https://www.springer.com/978-3-030-34371-2>
- Hartley & Zisserman: <https://www.robots.ox.ac.uk/~vgg/hzbook/>
- Stanford CS231A: <https://web.stanford.edu/class/cs231a/syllabus.html>
- Columbia FPCV: <https://fpcv.cs.columbia.edu/>
- Bonn photogrammetry (Stachniss): <https://www.ipb.uni-bonn.de/photogrammetry-i-ii/>, <https://www.ipb.uni-bonn.de/online-training-pcv/>, <https://www.ipb.uni-bonn.de/5min/index.html>, <https://www.ipb.uni-bonn.de/teaching>
- CMU 16-385: <https://www.cs.cmu.edu/~16385/>, <https://www.cs.cmu.edu/~16385/s19/>
- GaTech CS 6476: <https://faculty.cc.gatech.edu/~hays/compvision2025fall>
- UMD ENPM673: <https://mage.umd.edu/enpm673>
- ROB501 student notes: <https://tylertian123.github.io/notes/2025_fall/rob501_robo_cv/all_lecs.pdf>
- OpenCV Python tutorials: <https://docs.opencv.org/4.x/d6/d00/tutorial_py_root.html>
- KITTI odometry: <https://www.cvlibs.net/datasets/kitti/eval_odometry.php>
- COLMAP: <https://colmap.github.io/>
- CS231n: <https://cs231n.github.io/>
