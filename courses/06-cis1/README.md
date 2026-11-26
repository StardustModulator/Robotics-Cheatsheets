# EN.601.455/655 Computer Integrated Surgery I (CIS I): Self-Study Cheatsheet

> **How this was researched (read first).** The official CIIS wiki (`ciis.lcsr.jhu.edu`) and `www.cs.jhu.edu` were not reachable from the research environment. The network blocked them. The facts below come from three places:
> 1. The **Fall 2025 course materials** themselves: the organization slides, lecture PDFs, HW1-HW4, the PA1/PA2 and PA3/PA4/PA5 handouts, PA5 notes, the PA expectations sheet and the student data zips. I read them from a public GitHub mirror (see Sources [S6]). Its owner says they were downloaded from the Fall 2025 schedule page on 2026-10-05, with the file names unchanged. Several decks in that download are already stamped "Fall 2026" (calibration, rigid 3D-3D), so they are the current versions.
> 2. Search-engine snippets of the wiki pages (Fall 2021/2022/2024/2025/2026 schedules and the course home page).
> 3. Course-adjacent public pages (Taylor's home page, the cs.jhu.edu/cista mirror, and the `cispa` Python template).
>
> Anything I could not confirm against one of these is marked **(unverified)**. The exact week-by-week Fall 2025/2026 calendar could not be fetched, so the lecture order in Section 2 is a reconstruction.
>
> **Academic-integrity warning (from the course's own rules).** The rules say: *"You may not use the results of previous years' programming assignments or the work of other students or the results of any AI code generation systems on this year's assignments"* (PA3/PA4 handout, Fall 2025). They also forbid *"canned subroutines for solving point-cloud-to-point-cloud registration transformations or for performing calibrations"*. Course materials may not be redistributed (organization slides, "Use of Course Materials").
> - **Get the slides and data from the official wiki**, not from mirrors.
> - **Do not read public solution repos.** Many exist on GitHub.
> - **Write all the code yourself.** Self-study code written now is for learning. Before reusing any of it in a graded PA, ask the TA (unverified whether it is allowed).

---

## 1. Course identity

| Item | Value | Source |
|---|---|---|
| Number | EN.601.455 (undergrad) / EN.601.655 (grad). Formerly 600.445/645. Same content for both. | Org slides 2025 [S2]; [S1] |
| Title | Computer Integrated Surgery I (CIS I) | [S1] |
| Instructor | **Russell H. Taylor**, John C. Malone Professor of Computer Science (joint: ME, Radiology, Surgery, ENT). rht@jhu.edu. Guest lecturers throughout. | [S2] |
| TA (Fall 2025) | Bohua Wan | [S2] |
| Semester | **Fall only.** Tu/Th 1:30-2:45 pm, Hackerman B17 (2025, and Fall 2026 per the wiki snippet). The cs.jhu.edu/fall26 listing shows TuTh 3-4:15 for 601.655, which conflicts with the wiki. **(Unverified which is right.)** | [S2], [S1], [S13] |
| Credits | 4 (CIS minor checklist / catalogue listing) | [S12] |
| Platforms | Course wiki (schedule, slides, handouts, data). Piazza (Q&A). Canvas (Panopto lecture recordings, not public). Gradescope (all submissions). | [S2] |
| Textbook | Handouts on the web site. Recommended: *Computer-Integrated Surgery: Technology and Clinical Applications*, eds. Taylor, Lavallée, Burdea, Mösges, MIT Press 1996. | [S2] |
| Prerequisites | "No hard and fast rules". Coordinate transformations and linear approximations are used heavily. Calculus is assumed. **Linear algebra is highly recommended.** Probability/statistics helps. Familiarity with basic data structures is important. Programs in C, C++, MATLAB or Python ("we will need to be able to run your programs"). Handout examples are in pseudo-code and C/C++. | [S2] |
| Teams | Teams of **2 strongly recommended**. 1 is allowed but discouraged. **3 is not permitted.** Partners may change between assignments. Both partners get the same grade, and the report must say who did what. | [S2], [S3] |
| Follow-on | **CIS II (EN.601.456/656, Spring)**: advanced seminar plus lectures plus **student team projects**. Emphasis on project management and presentations. Ends with a project showcase (Best Project / "Sisyphus" awards). 601.452 is the no-project variant. | [S2], [S14], [S15] |

**Description (paraphrased from the catalogue/CIS-minor text):** computer-based techniques, systems and applications that use quantitative information from medical images and sensors to help clinicians at every stage of treatment. The course covers the basics of CIS:
- image segmentation, registration and modeling
- robotics
- safety
- human-machine interfaces

It adds clinical application case studies, often given by clinicians and system builders, plus outcomes and economics and regulatory topics [S2], [S12].

**Official URLs**
- Course home (current, Fall 2026): https://ciis.lcsr.jhu.edu/doku.php?id=courses:455-655:455-655
- Fall 2026 schedule: https://ciis.lcsr.jhu.edu/doku.php?id=courses:455-655:2026:fall-2026-schedule
- Fall 2025 schedule (the complete set used here): https://ciis.lcsr.jhu.edu/doku.php?id=courses:455-655:2025:fall-2025-schedule
- Older schedules: `...courses:455-655:2024:fall-2024-schedule`, `...2022:fall-2022-schedule`, `...2021:fall-2021-schedule`. Pre-2021: https://www.cs.jhu.edu/cista/455 (e.g., `455-schedule-2020.html`).
- Course numbering note: https://ciis.lcsr.jhu.edu/doku.php?id=courses
- CIS II: https://ciis.lcsr.jhu.edu/doku.php?id=courses:456
- Taylor home page: https://www.cs.jhu.edu/~rht/

---

## 2. Lecture-by-lecture syllabus (reconstructed)

**What is verified:**
- **Deck contents:** I read every deck listed below. The topics come from the slide titles.
- **Approximate placement:** search snippets of the Fall 2022/2024 schedules show this pattern:
  - intro in week 1
  - segmentation and modeling begin in late September
  - "Begin Calibration" in week 4 and "Finish Calibration" in week 5, together with the least-squares and interpolation reviews
  - "Registration (Part 1)" after segmentation
  - The Registration Part 1 deck is dated 10/7/25.

**What is not verified:** the exact dates and order for 2025/2026. Wiki slide URLs follow the DokuWiki pattern `https://ciis.lcsr.jhu.edu/lib/exe/fetch.php?media=courses:455-655:lectures:<file>`. That pattern is confirmed by indexed files such as `2021_cis_i_organization.pdf` and `segmentation_-_2021.pdf`. Per-file links built from it are **(unverified)**. Always start from the schedule page.

| # (approx.) | Topic | Slide file(s) on the Fall 2025 schedule | Key content (from the slides) |
|---|---|---|---|
| 1 | Organization | `2025_cis_i_organization.pdf` | Rules, grading formula, teams, Gradescope/Piazza, 2024 grade histogram |
| 1-2 | Introductory lecture | `2025_introductory_lecture.pdf` | CIS paradigm: model → plan → execute → assess. Surgical CAD/CAM vs surgical assistants. Information-intensive OR. Examples: sinus surgery, snake robots, microsurgery/retina, da Vinci "current dominant paradigm" |
| 2-3 | **Frames & error propagation** | `frames.pdf` (61 slides); supplementary tutorial `frame_transformations.pdf` (B. Killeen, "Frame Transformations in CIS: A Graphical Introduction", reference graphs) | Points, vectors, frames F=[R,p], composition, sk(·) cross-product matrix. 3D rotations: canonical, exponential/axis-angle, quaternions. Homogeneous coordinates. Frame from 3 point pairs. Pointer calibration. Kinematic chains. **"Small" frames ΔF ≈ [I+sk(α), ε]**. Errors and sensitivity, the rotation triple product. **"Standard form" linearized error expressions**. Error of composition and of inverse. Error propagation in kinematic chains. Parametric sensitivity. Mahalanobis distance. Multivariate Gaussian error models |
| ~3 | Guest: technology in neurosurgery | `20240924_cis_technologyneurosurgery.pdf` | Neurosurgery subspecialties, endoscopic endonasal approach, navigation systems, intra-op CT drawbacks, video-based navigation, **TRE**, history (PUMA, Neuromate), SEEG, DBS, LITT |
| ~3-4 | **3D localizers / tracking** | `localizers.pdf`; `lecture7-tracking.pdf` (borrowed from Stanford ME 328, Okamura) | Mechanical linkages (passive/active), ultrasound, **electromagnetic (NDI Aurora, how magnetic tracking works)**, optical active/passive (**NDI Polaris**, reflective markers), stereo vs monocular tracking, tracking requirements for display vs robot control |
| 4-5 | **Calibration** | `calibration.pdf` (Fall 2026 version) | Pointing-device (**pivot**) calibration. Linear parameter estimation / linear least squares. 2-link robot and ROBODOC wrist calibration. Linearization. Camera/projection calibration without and with distortion. Rotation part via quaternions, displacement part. "Mapping the space". **C-arm/X-ray image intensifier dewarping: grid-groove detection, 5th-order Bernstein polynomial fit, two-pass scanline dewarp**. Two-plane method |
| 5 | Least-squares review | `leastsquaresreview.pdf` | Orthogonal transformations, Householder, **SVD**, least-squares adjustment (Lawson & Hanson) |
| 5 | Interpolation & deformations review | `interpolationreview.pdf` (updated Sept 2026) | Linear/barycentric/bilinear interpolation (with uncertainty). **Bézier and Bernstein polynomials**, de Casteljau. B-splines, de Boor. Regular-grid N-D interpolation. **"Example: 3D calibration of distortion"** (the PA2 method). Shepard's inverse-distance weighting. Radial basis functions |
| 5-6 | **Segmentation & modeling** | `segmentation_-_2023.pdf`, `Segmentation - 2020.pdf`, `Segmentation-2 (Rev D).pdf` (the latter appear to be Prof. Jerry Prince's material, unverified) | Thresholding, partial volume. Deformable surfaces (parametric, geometric/level-set, topology-preserving TGDM). Template-based and deep-learning segmentation. Surface representations (FEV lists, tetrahedral meshes). Contour tiling. **Marching cubes**. Laplacian smoothing. Model simplification. Part 2: LDA, SVM, random walker, graph cuts/MRF (α-β swap, α-expansion), multi-atlas segmentation, neural nets/CNNs |
| 6 | Surface simplification | `gueziecsimp.pdf` | Guéziec edge-collapse simplification with tolerance volumes (femur, carotid examples) |
| 6-7 | **Registration Part 1** | `registration_part_1.pdf` (dated 10/7/25) | Why registration matters. Taxonomy. Features and distance functions. **Fiducial (paired-point) registration**. **Surface registration and ICP, step by step**. **"Outline of a practical ICP code"**: thresholds η_n, termination tests. Robust pose estimation. **Generalized-ICP**. Distance maps |
| 6-7 | Rigid 3D-3D calculations | `lecture-rigid-3d-3d-calculations` (Fall 2026 version) | Minimizing rigid registration error. Centroid subtraction. Iterative rotation solve via small-angle linearization. **Arun-Huang-Blostein SVD (with the det fix)**. **Horn / Besl-McKay quaternion 4x4 eigenproblem**. Quaternion↔R conversions (note the scalar-first vs scalar-last caution; cisst puts the scalar last). Similarity (rigid + scale). Registration from line pairs. Probabilistic estimation |
| 7 | **Finding point pairs** | `lecture-finding-point-pairs` | Closest point in dense clouds (cells). **FindClosestPoint(a,[p,q,r])** (least-squares λ,μ plus edge projection). Bounding boxes and **bounding spheres** (sphere-center construction). Octrees of bounding spheres. **k-d trees** of bounded things. **Covariance ("principal-direction") trees**, including for triangle meshes (C++-style pseudo-code) |
| 8 | **Registration Part 2** | `registration_part_2.pdf` | Feature-based **2D-3D** registration (contour-based, Levenberg-Marquardt, constrained linearized LS, robust M-estimators). **Coherent Point Drift**. Multimodal registration. **IMLP / G-IMLOP** (Billings: anisotropic noise, oriented points). Ultrasound-assisted registration. Intensity-based energy functions, **mutual information**. Rigid 3D/2D with **DRRs** (periacetabular osteotomy). Orbital-search initialization. CNN-assisted, differentiable DRR (ProST, Cong Gao) |
| 8 | CPD tutorial | `coherent_point_drift_registration.pdf` | EM view of registration: rigid, affine and deformable CPD; fast implementations |
| 9 | **Registration Part 3: deformable** | `registration_part_3.pdf` | Deformable warping from point matches. **Thin-plate splines** and other RBFs. **Statistical atlases and PCA** (shape and intensity). Atlas construction and correspondences. Deformable template matching. Leave-out validation. Density atlases. **2D/3D shape+intensity registration**. Model completion from partial CT + X-rays. **C-arm distortion** statistics |
| 9 | Statistical atlases (guest) | `Gouthami_StatisticalAtlas_CIS446-converted.pptx.pdf`, `AtlasValidation - Chintalapani 2010.pptx.pdf`, `Miccai_2011_TALK_Sharmi.pdf` | Bone shape/intensity atlases, pelvis modes, hip osteotomy guidance (BGS), atlas extrapolation. **This is the background for PA5.** |
| 10 | **Medical robots part 1** | `medical_robots_part_1.pdf` | Closed-loop information. Surgical CAD/CAM. Image-guided radiotherapy. Precision augmentation and surgical assistants. **Jacobian motion control**, parallel-link robots. **Steady-Hand robot** (admittance control, stapedotomy example). **Virtual fixtures as constrained optimization** (LARS robot DOF, keep tool tip near a point). **Typical mid-level control loop**. Forbidden half-space, path following. LHS vs RHS Jacobians. **Remote-center-of-motion example** |
| 11 | **Medical robots part 2** | `medical_robots_part_2.pdf` | Robot-assisted sinus surgery with anatomy-derived boundary constraints. 5 basic geometric constraints. Suturing constraints. Soft constraints. Linear vs nonlinear constrained optimization. **Signed-distance-field (SDF) virtual fixtures**: spring model, variable admittance. Snake robots / master-slave controllers |
| 12 | **Medical robots part 3: autonomy** | `medical_robots_part_3_-_autonomy.pdf` | Human-machine partnership, levels of autonomy, surgical CAD/CAM. Radiotherapy planning automation. 3D-US beating-heart. **Steady-hand microsurgery / retinal surgery** (force-limited membrane peeling, vein cannulation with OCT). Co-robotic ultrasound. Skull-base drilling |
| 12 | **Robotic joint replacement (case study)** | `robotic_joint_replacement_surgery.pdf`, `Robotic Joint Replacement Surgery.pptx.pdf`, videos (`Robodoc1.mpg`, `Pinless.mpg`, …), reading `Robodoc903.pdf` | THR/TKR. **ROBODOC planning, pin-based vs pinless (surface) registration**, results and clinical trials, Mako. Commercial lessons |
| 13 | Craniofacial surgery (case study) | `craniofacial_surgery.pdf` | Simulation, rapid prototyping, osteotomy ranking, navigation plus passive manipulation aid, robot-assisted mandibular/orthognathic/dental |
| 13 | **Safety: Therac-25** | `therac.pdf`, `therac-25.pdf`, `therac_updated_mit.pdf` | Accident history, race condition, Leveson & Turner lessons |
| 13-14 | Other guest/research talks | `guestlecture_cis1_connolly.pdf` (tumor-bed inspection, mass-spec/photoacoustic), `tx-stable-us-spie.pdf` (stabilized US imaging, out-of-plane CNN), `max_li_iros_2020_presentation.pdf`, `defense_presentation_cong_gao_-_handouts.pdf` (2D/3D X-ray registration), `Stindel Summer School 2007.pdf`, `taylor_2025.pdf` (radiotherapy dose algorithm talk; unrelated title) | Breadth topics |

**Topics you asked about that have no dedicated deck in the Fall 2025 download:**
- **Imaging physics (CT/MRI/US/fluoro).** It appears only inside other decks: intro, segmentation, registration part 2 (which cites *The Essential Physics of Medical Imaging*), calibration (C-arm), the US talks and the neurosurgery guest lecture.
- **Fitzpatrick FRE/TRE theory.** It is not a dedicated deck. TRE appears in the neurosurgery talk, and HW3 does full covariance propagation for a navigation system.
- Imaging basics may be covered in a guest lecture that I could not see **(unverified)**.

---

## 3. Programming assignments PA1-PA5 (Fall 2025 handouts)

### 3.1 General rules and grading (all PAs)

**Languages and libraries**
- C, C++, Python or MATLAB; anything else needs TA permission.
- Numerical libraries (least squares, Cartesian math, e.g., NumPy, Numerical Recipes, the JHU **cisst** library https://github.com/jhu-cisst/cisst) are allowed and **must be cited**.
- **Not allowed:** packages that "essentially solve the problem", such as canned point-cloud registration or calibration routines.
- **AI code generation is banned** (PA3/PA4 2025 handout wording).

**Deliverables (two files on Gradescope)**
1. A PDF report of about 5-8 pages with the signed cover sheet. It covers:
   - the mathematical approach and algorithm steps
   - a program-structure overview (hierarchy chart or table)
   - the validation approach: unit tests on self-made synthetic data plus the debug sets
   - a results discussion with error tables for debug vs reference
   - a **tabular summary of the unknown-set results**
   - who did what, and citations (Taylor's slides must be cited, or points come off)
2. A ZIP/TAR containing:
   - `PROGRAMS/` with all source files, `README.TXT` (one line per file), an executable or run instructions, and relative data paths or a command-line data-dir option
   - `OUTPUT/` with the output files in the exact required format

**Data and dates**
- Each PA ships **debug** data sets with the instructor's outputs (and "answer"/auxiliary files) plus **unknown** sets that you must solve.
- PAs are due **before class** on the due date.
- Late penalty: -10 points per day; not accepted after 4 days (org slides).

**Rubric (points)**

| Component | PA1/PA2 | PA3/PA4/PA5 |
|---|---|---|
| Program: design and structure | 20 | 20 |
| Program: reusability and modularity | 10 | 10 |
| Program: documentation clarity | 10 | 10 |
| Results: correctness and completeness | 15 | 20 |
| Report: formulation and algorithm | 15 | 15 |
| Report: program overview | 10 | 10 |
| Report: validation | 10 | 5 |
| Report: results discussion | 10 | 10 |

**Starter template:** the course links a Python template, `cispa` (B. Killeen, 2022): https://github.com/benjamindkilleen/cispa. It uses conda, click, numpy and pytest. The schedule also lists MATLAB and C/C++ sample folder structures (`cispa_matlab_kaitlyn.zip`, `cispa_c_cpp.zip`).

**The PAs build on each other.** You start with a frame library and point-set registration, then add pivot calibration, distortion correction, closest point on a mesh, ICP and finally deformable ICP. *Write PA1's math library carefully; you reuse it four times.*

### 3.2 PA1: Basic transformations and pivot calibration

**Scenario.** A stereotactic navigation system uses an **EM tracker** with large (up to several mm) but repeatable **distortion** and noise of 0-0.3 mm. An accurate **optical tracker** on a tripod **jiggles** slightly between frames. The other pieces are:
- A **calibration object** with N_C EM markers at known positions `c_i` and N_A LEDs at known positions `a_i`.
- N_D LEDs at known positions `d_i` on the EM base.
- A dimpled **calibration post** at an unknown position.
- An **EM-tracked pointer** (N_G markers) and an **optically tracked pointer** (N_H LEDs), both with unknown marker geometry.

**Tasks**
1. Write a Cartesian math package: points, rotations, frames.
2. Implement 3D point-set to point-set registration.
3. Implement pivot calibration.
4. For each calibration frame k, compute the expected EM marker positions:
   - `F_D[k]` such that `D_j = F_D · d_j` (optical tracker ↔ EM base)
   - `F_A[k]` such that `A_j = F_A · a_j` (optical tracker ↔ calibration object)
   - **`C_i^expected = F_D⁻¹ · F_A · c_i`**, then output it.
5. **EM pivot calibration.**
   - Define the probe's local marker model from frame 1: `Ḡ = mean_j G_j`, `g_j = G_j − Ḡ`.
   - For each frame, register `g → G` to get `F_G[k]`.
   - Solve the stacked LS system to get `t_G` (tip in the probe frame) and `p_dimple` (post in EM coordinates).
6. **Optical pivot calibration.**
   - First map each frame's H LEDs into EM coordinates with that frame's `F_D[k]⁻¹`. This handles the tripod jiggle.
   - Then pivot-calibrate as in step 5.

**Files (Fall 2025 data, `pa_1_student_data.zip`)**
- Debug sets a-g; unknown sets h, i, j, k (2025 zip). The handout table lists only h and i.
- Debug sets switch error sources on one at a time:

| Debug set | Error sources |
|---|---|
| a | none |
| b | EM noise |
| c | EM distortion |
| d | optical jiggle |
| e, f, g | combinations |

| File | Header | Body |
|---|---|---|
| `pa1-X-calbody.txt` | `N_D, N_A, N_C, name` (e.g., `8, 8, 27`) | N_D lines `d_i`, N_A lines `a_i`, N_C lines `c_i` (x,y,z, comma-separated, mm) |
| `pa1-X-calreadings.txt` | `N_D, N_A, N_C, N_frames, name` (e.g., `8,8,27,8`) | Per frame: N_D `D_i`, N_A `A_i`, N_C `C_i` |
| `pa1-X-empivot.txt` | `N_G, N_frames, name` (e.g., `6, 12`) | Per frame: N_G `G_i` |
| `pa1-X-optpivot.txt` | `N_D, N_H, N_frames, name` (e.g., `8, 6, 12`) | Per frame: N_D `D_i`, then N_H `H_i` |
| **`pa1-X-output1.txt`** (you write it) | `N_C, N_frames, name` | Line 2: EM-pivot post position. Line 3: optical-pivot post position. Then per frame N_C lines of `C_i^expected` |
| `pa1-X-auxilliary1.txt` (provided, sic) | — | Simulation parameters: distortion factor, EM noise, jiggle factor, actual vs estimated post positions, per-marker C errors. **Great for debugging.** |

### 3.3 PA2: Distortion calibration, registration and navigation

**Pipeline**
1. From calbody/calreadings, compute `C_expected[k]` for every frame (PA1 code). In PA2 there are many more frames, e.g., 125 in `pa2-debug-f`, sampling the workspace on a grid.
2. Fit a **distortion-correction function** `P(·)` mapping measured EM positions to expected ones. The method "described in class" is a **3D tensor-product Bernstein polynomial** fit by least squares (see §5.6).
3. Dewarp the EM pivot data with `P` and **redo the EM pivot calibration**. This gives an improved `t_G`.
4. From the `em-fiducials` frames (probe tip touching each CT fiducial), compute the fiducial positions `B_j = F_G[j] · t_G` in EM coordinates, using dewarped G's.
5. Compute **`F_reg`** by point-set registration of `B_j → b_j`, where the `b_j` are the CT fiducial coordinates.
6. For each `EM-nav` frame: dewarp the G's, compute `F_G`, and output **`v = F_reg · F_G · t_G`** (the tip in CT coordinates).

**Extra files**

| File | Header | Body |
|---|---|---|
| `pa2-X-ct-fiducials.txt` | `N_B, name` (e.g., `6`) | N_B lines `b_i` (CT coordinates) |
| `pa2-X-em-fiducialss.txt` (sic, double "s" in 2025 data) | `N_G, N_B, name` | N_B frames of N_G `G_i` |
| `pa2-X-EM-nav.txt` | `N_G, N_frames, name` (e.g., `6,4`) | Frames of `G_i` |
| **`pa2-X-output2.txt`** (you write it) | `N_frames, name` | One line per nav frame: tip `v` in CT |
| `pa2-X-auxilliary2.txt` (provided) | — | Estimated registration `F_reg` and its error `F_reg⁻¹ · F_reg,est` |

- Debug sets a-f; unknown sets g-j (2025).
- An `output1` file is also provided for some sets to help teams whose PA1 struggled.

**Validation tips**
- Use the debug sets to isolate the error sources (noise, distortion, jiggle).
- Hold out calibration frames to choose the polynomial degree. A careful public write-up found degree ≈ 4-5 sufficient; slides use 5th order for C-arm dewarping.
- Compute FRE on the fiducials.

### 3.4 PA3: Matching step of ICP (closest point on a triangle mesh)

**Scenario (shared with PA4 and PA5)**
- A bone surface from CT is given as a **triangle mesh**: 1568 vertices and 3135 triangles in the 2025 data.
- Optical rigid body **B** is screwed into the bone.
- Rigid body **A** is a pointer whose tip touches surface points.
- Each body file gives LED positions in body coordinates plus the tip position.
- The sample file gives LED readings `a_{i,k}`, `b_{i,k}` (plus dummy LEDs) per sample k.

**Pipeline**
1. Read the mesh (vertex array plus triangle index array) and the body files.
2. For each sample k, register to get `F_{A,k}` and `F_{B,k}`. Then compute **`d_k = F_{B,k}⁻¹ · F_{A,k} · A_tip`**, the pointer tip in the bone-body frame.
3. With **`F_reg = I`**, set `s_k = F_reg · d_k` and find the **closest point `c_k`** on the mesh.
4. Implement **brute-force linear search** first and debug it rigorously.
5. Then implement a **faster spatial structure**: bounding spheres or boxes, an octree, a k-d tree or a covariance tree. **Validate it against brute force.**

**Files**

| File | Format |
|---|---|
| `ProblemX-BodyY.txt` (X=3,4,5; Y=A,B) | `N_markers name`; N_markers lines `x y z` (body coordinates); 1 line tip `x y z`. 2025: 6 LEDs per body. |
| `ProblemXMesh.sur` | `N_vertices`; vertex lines; `N_triangles`; lines `i1 i2 i3 n1 n2 n3`. The n's are neighbour triangles opposite each vertex (−1 = none; not needed). |
| `PA3-X-{Debug,Unknown}-SampleReadingsTest.txt` | Header `N_S = N_A + N_B + N_D, N_samps, name, 0` (e.g., `16, 15, …`). Per sample: N_A A-LEDs, N_B B-LEDs, N_D dummy LEDs. |
| **`pa3-X-Output.txt`** | `N_samps name`; per sample: `d_x d_y d_z  c_x c_y c_z  ‖d_k − c_k‖` |

- 2025 sets: debug A-F, unknown G, H, J.
- Some sets have 0.5 mm marker noise (per the instructor log files in the data zip, which also act as an answer key).
- The data zip is named `2025_pa345_student_data.zip` on the schedule page.

### 3.5 PA4: Full rigid ICP

- Start from `F_reg = I` (or a better initial guess).
- Iterate:
  1. `s_k = F_reg · d_k`
  2. find the closest `c_k`
  3. reject outliers with a threshold `η_n`
  4. re-estimate `F_reg` by point-set registration (`d_k → c_k`)
  5. test for termination
- You must document your stopping rule and outlier handling. See the lecture's "practical ICP code" in §5.8.
- **Output** `pa4-X-Output.txt`: per sample `s_x s_y s_z  c_x c_y c_z  ‖s_k − c_k‖`.
- 2025 sets: debug A-F plus "Demo-Fast" / "Demo-Slow" variants of A and B; unknown G, H, J, K.
- Some sets have 0.1 mm noise. The instructor's ICP took roughly 2-100 iterations (per log notes).

### 3.6 PA5 (optional): Deformable registration to a statistical shape model

**Grading role.** If handed in, PA5 replaces your lowest remaining grade (see §4).

**Input.** An atlas file `Problem5Modes.txt` with:
- header `Nvertices=1568 Nmodes=6`
- "Mode 0" = mean shape (the same vertices as the mesh)
- Modes 1…M = per-vertex displacements

The deformed vertex is **`m_k = m_{0,k} + Σ_{m=1}^{M} λ_m m_{m,k}`**. The sample header's extra field gives the number of modes to use.

**Method (handout).** It is based loosely on the active shape/appearance models of Cootes & Taylor.
1. Run rigid ICP (PA4) to get `F_reg^(0)`.
2. For each match `c_k` on triangle {s,t,u}, compute barycentric coordinates (ζ, ξ, ψ). Then
   - `q_{m,k} = ζ m_{m,s} + ξ m_{m,t} + ψ m_{m,u}`
   - `c_k = q_{0,k} + Σ λ_m q_{m,k}`
3. **Option A (alternating).** With `s_k` fixed, solve the LS problem `s_k ≈ q_{0,k} + Σ λ_m q_{m,k}` for Λ. Update the mesh and the bounding boxes; there is no need to rebuild the tree fully. Re-match, and repeat until stalled. Then re-estimate `F_reg` with PA4, and alternate.
4. **Option B (combined linearized).** Solve `ΔF ≈ [I+sk(α), ε]` and Λ jointly from `s_k + α×s_k + ε ≈ q_{0,k} + Σ λ_m q_{m,k}` (Eq. 6-7 of the handout). Then turn α into a **proper rotation** (not a skew matrix), update, re-match and iterate.

Taylor used A then B. Alternatives are allowed if they are explained.

**Output** `pa5-X-Output.txt`:
- header `N_samps name N_modes`
- line 1: the λ_1…λ_M (4 decimals)
- then PA4-format lines

**Readings cited in the handout:**
- Cootes & Taylor, ECCV 2000
- Cootes, Edwards & Taylor (active appearance models), IPMI 1999
- Yao & Taylor (multi-layer flexible mesh template matching): IJPRAI 2003 and SPIE MI 2003
- Yao & Taylor, statistical density atlas to X-ray, CAOS 2002
- Fleute & Lavallée, MICCAI 1999

**Notes deck:** `PA5 notes`.

### 3.7 Written homeworks (Fall 2025: 4 "homeworks" = take-home exams)

Rules:
- Open book, notes and web, with citations.
- Alone or with your partner only. You may **not split the questions**; both partners must be able to explain every answer.
- PDF on Gradescope.
- More than 100 points are available, capped at 100.
- Earlier years had 6 homeworks (2021-22 syllabus snippet).

| HW | Scenario | Skills tested |
|---|---|---|
| **HW1** | **Computer-assisted pelvic osteotomy** with NDI Aurora 6-DOF sensors on pointer, osteotome and pelvis; 3 fiducial pins | Frame-chain formulas in `[R,p]` components. Error models `F* = ΔF_B F ΔF_x`. Linearization into **normal form** (α, ε on the right; `sk(α)p → −sk(p)α`). Registration error bounds with norm constraints f(Δb) ≤ σ: Euclidean/L1/∞/Mahalanobis, as quadratic forms in η=[α;ε]. Covariance propagation `C_Ct`. Periacetabular osteotomy with preformed plates: hole positions, drill-axis angle error, accuracy vs registration error, screw-hole clearance |
| **HW2** | Sequential Gaussian estimation | Bayesian / Kalman-like updates for the linear model `Bβ + Dδ = φ`. **Re-calibrating a pivot** (linearize `F_k a − b`, matrix form, noise covariance). Pose covariance from noisy markers. **Interpolation error bounds** with a gradient bound |
| **HW3** | **Design a surgical navigation system**: optical tracker, user-designed marker bodies and pointer, CT segmentation of spheres | Linearized error chains for `p_nav = f(F_ptr, F_A, F_AC)`. Covariance of pointer pose and tip, `C_nav`. Then a **design question**: choose marker geometries to meet accuracy, evaluated numerically, with a small text file submitted. *AI tools allowed for symbolic algebra only with full disclosure* |
| **HW4** | **Robot virtual fixtures**: robot holding a tool, force sensor, CT-registered anatomy with **signed distance fields** | Mid-level control loop. Joint position/velocity/acceleration limits as linear constraints. Tool-pose incremental kinematics `η_RT = J Δq`. Rate limits. Kinematically deficient (5-DOF) vs redundant (7-8 DOF) robots. **Hand-over-hand admittance (cooperative) control** with SDF-based forbidden regions |

---

## 4. Exams and grading

- **No in-class midterm or final.** The 4 homeworks *are* the take-home exams. Possible individual in-class quizzes are mentioned (2025 org slides).
- **Drops:** drop one of HW1-3 **or** one of PA1-3. **HW4 and PA4 are mandatory**; don't do a cursory job. If you score below 60 on one, Taylor "may throw out the lowest of the other assignments instead".
- **PA5 (optional)** replaces the lowest remaining grade, except that PA1 and PA2 can't both be dropped.
- **Formula:** `grade = max(0.4·P + 0.6·H, 0.6·P + 0.4·H)`, where P = PA average and H = HW average.
- Letter cutoffs are "usually fairly close to 90-80-70".
- **2024 distribution (org slide):** the histogram is overwhelmingly A-range across 455 and 655. The extracted counts read roughly "A+ 2 2, A 16 19, A- 6, B+ 2, B 3 12, B- 1, C 1 1, F 1". The per-section column assignment is garbled in extraction, so it is **(unverified)**. Taylor: "The grading formula is very forgiving if students put in the work… don't panic after the first couple assignments."
- **Ethics:** cases go to the Ethics Board; conviction usually means failing the course.

---

## 5. Core concept checklist with key equations

Notation follows Taylor: `F = [R, p]`, `F·x = R x + p`, and `sk(a)` is the skew matrix with `sk(a) b = a × b`.

### 5.1 Frames
- Composition: `F_1 F_2 = [R_1 R_2, R_1 p_2 + p_1]`.
- Inverse: `F⁻¹ = [Rᵀ, −Rᵀ p]`.
- Naming: `F_AB` maps B-coordinates to A-coordinates. Draw the **reference graph** (Killeen tutorial) and chain along a path; going against an arrow means inverting.
- **Rodrigues / axis-angle:** `R = rot(n, θ) = I + sin θ sk(n) + (1 − cos θ) sk(n)²`.
  - Angle: `θ = acos((tr R − 1)/2)`.
  - Axis from `R − Rᵀ = 2 sin θ sk(n)`.
- **Unit quaternion** `q = [s, v] = [cos(θ/2), sin(θ/2) n]`.

  ```
  R(q) = [[s²+vx²−vy²−vz², 2(vxvy − s vz), 2(vxvz + s vy)],
          [2(vxvy + s vz), s²−vx²+vy²−vz², 2(vyvz − s vx)],
          [2(vxvz − s vy), 2(vyvz + s vx), s²−vx²−vy²+vz²]]
  ```

  Watch the scalar-first vs scalar-last ordering (cisst puts it last).

### 5.2 Small motions and error propagation
- Small rotation: `R(α) ≈ I + sk(α)`, so `ΔF ≈ [I + sk(α), ε]`.
- Point through a perturbed frame: `F ΔF x ≈ F x + R(α × x + ε) = F x + R(−sk(x) α + ε)`.
- Left-multiplied error `ΔF F x`: `Δ ≈ α × (F x) + ε`.
- **Normal form:** write every error as `Σ M_i α_i + Σ N_j ε_j`, using `sk(a) b = −sk(b) a` and dropping second-order terms (αα, αε).
- Composition error: `(F_1 ΔF_1)(F_2 ΔF_2) = F_1 F_2 (F_2⁻¹ ΔF_1 F_2) ΔF_2`, with `F_2⁻¹ ΔF_1 F_2 = [R_2ᵀ ΔR_1 R_2, R_2ᵀ(ΔR_1 p_2 + Δp_1 − p_2)] ≈ [I + sk(R_2ᵀ α_1), R_2ᵀ(α_1 × p_2 + ε_1)]`. Derive it yourself; this is exactly HW1-style work.
- **Covariance:** if `Δx = J η` with `η ~ N(0, C_η)`, then `C_x = J C_η Jᵀ`.
- Mahalanobis distance: `d² = Δxᵀ C⁻¹ Δx`.
- Gaussian conditioning: `E[x_1 | x_2] = μ_1 + C_12 C_22⁻¹ (x_2 − μ_2)` and `cov = C_11 − C_12 C_22⁻¹ C_21`. HW2 and HW3 rely on this.
- Information fusion: `C_new = (Σ C_k⁻¹)⁻¹`, `μ_new = C_new Σ C_k⁻¹ μ_k`.

### 5.3 Point-cloud registration: find F with b_i ≈ F a_i
**Arun-Huang-Blostein SVD (1987)**
1. Centroids `ā`, `b̄`; demeaned `ã_i = a_i − ā`, `b̃_i = b_i − b̄`.
2. `H = Σ ã_i b̃_iᵀ` (3×3), with SVD `H = U S Vᵀ`.
3. `R = V diag(1, 1, det(V Uᵀ)) Uᵀ`. The det fix prevents reflections.
4. `p = b̄ − R ā`.

**Horn / Besl-McKay quaternion method**
- With the same H, let `Δ = [H₂₃ − H₃₂, H₃₁ − H₁₃, H₁₂ − H₂₁]ᵀ` and

  ```
  G = [[ tr(H),  Δᵀ                ],
       [ Δ,      H + Hᵀ − tr(H) I₃ ]]   (4×4 symmetric)
  ```

- The unit eigenvector of the **largest eigenvalue** is `q = [s, v]`; convert it to R.
- Similarity variant: add a scale factor.

**Iterative alternative (Taylor slides):** linearize `R ≈ R_0 (I + sk(α))`, solve the LS problem for α, and re-orthonormalize.

Using `scipy.spatial.transform.Rotation.align_vectors` would violate the "no canned registration" rule.

### 5.4 Pivot calibration
- For each pose `F_k = [R_k, p_k]` of the probe with its tip in a fixed dimple: `R_k t_tip + p_k = p_dimple`.
- Stack all frames:

  ```
  [ R_1  −I ]              [ −p_1 ]
  [  ⋮    ⋮ ] [t_tip   ] =  [  ⋮   ]
  [ R_K  −I ] [p_dimple]    [ −p_K ]
  ```

- Solve by least squares (SVD/QR). The residual RMS indicates quality.
- The system is well-conditioned only if the probe is rotated about ≥ 2 axes. Tilt range drives the error.
- The probe model comes from frame 1 (`g_j = G_j(1) − Ḡ(1)`). A refinement is a Procrustes mean over frames.

### 5.5 Tracking systems
- **Optical (Polaris-type):** IR stereo; passive reflective spheres or active LEDs; line-of-sight required; sub-mm accuracy.
- **EM (Aurora-type):** field generator plus small coils; no line-of-sight; distorted by metal (repeatable) and noise.
- **Mechanical:** passive or active linkages.
- **Ultrasound** and monocular vision.
- Tool pose comes from registering the known marker geometry to the measured marker positions (§5.3), every frame.

### 5.6 Distortion correction with Bernstein polynomials (PA2)
1. Pick a bounding box `[q_min, q_max]` from the **measured** calibration data and scale: `u = (q − q_min) / (q_max − q_min) ∈ [0,1]³`.
2. 1-D basis: `B_{N,k}(u) = C(N,k) u^k (1 − u)^{N−k}`, k = 0…N (non-negative, sums to 1).
3. 3-D basis: `F_{ijk}(u) = B_{N,i}(u_x) B_{N,j}(u_y) B_{N,k}(u_z)`, giving `(N+1)³` terms (216 for N = 5).
4. Model: `p_true ≈ Σ_{ijk} c_{ijk} F_{ijk}(u(q_measured))`.
5. Build `A` with one row per measured point and one column per basis function, and `P` with the `C_expected` rows (n×3). Solve `A C ≈ P` by LS for the coefficients C ((N+1)³ × 3).
6. Apply: `p = F(u(q)) C`. Use the **same scale box** at application time and clamp or flag points outside it.
7. Pick N by hold-out or cross-validation. Fitting noise with a high N is the usual failure.

### 5.7 Closest point on a triangle [p, q, r] (PA3)
1. Solve `a − p ≈ λ (q − p) + μ (r − p)` by LS (2×2 normal equations). Then `c = p + λ(q−p) + μ(r−p)`.
2. If `λ ≥ 0, μ ≥ 0, λ + μ ≤ 1`, c is the answer.
3. Otherwise project c onto the edge that the violated condition points to:
   - `λ < 0` → segment (r, p)
   - `μ < 0` → segment (p, q)
   - `λ + μ > 1` → segment (q, r)
4. **ProjectOnSegment(c, p, q):** `λ = ((c−p)·(q−p)) / ((q−p)·(q−p))`, `λ* = max(0, min(λ, 1))`, `c* = p + λ*(q − p)`.

A pitfall with obtuse triangles: checking only one edge can be wrong. Projecting onto all three edges and keeping the closest is safe.

### 5.8 Acceleration structures
- **Bounding box test:** check a triangle only if `a` is inside its box grown by the current `bound`.
- **Bounding sphere:** let (a, b) be the longest edge, `f = (a+b)/2`, `u = a − f`, `v = c − f`, `d = (u×v)×u`. Then `γ = (v² − u²) / (2 d·(v − u))`, `λ = max(0, γ)`, center `q = f + λd`, radius `ρ = ‖q − a‖`. Skip triangle k if `‖p − q_k‖ − ρ_k ≥ ‖p − r_best‖`.
- **Trees** (octree / k-d / covariance trees): store bounding spheres or boxes of "things" (triangles). Split at the centroid, or along the principal axis for covariance trees.
- Search: descend if the node box, grown by `bound`, contains the query; update `bound` as better points are found.
- A good initial `bound` speeds things up: use the distance to the previous match's triangle.
- **PA5:** update node bounds after the mesh deforms; there is no need to rebuild.
- k-d tree background: Bentley 1975; Friedman-Bentley-Finkel 1977.

### 5.9 ICP (Besl & McKay 1992; Taylor's practical outline)

```
init: F_0, threshold η_0 (large), tree T over mesh
loop n = 0,1,...:
  match:   for each q_k: c_k = FindClosestPoint(F_n·q_k, T, bound = ‖F_n q_k − c_k_prev‖)
           keep pair if ‖c_k − F_n q_k‖ < η_n
  update:  F_{n+1} = BestRigid(A = kept q_k, B = kept c_k)
           e_k = b_k − F_{n+1} a_k;  σ_n = sqrt(Σe·e)/N;  ε_max;  ε_n = Σ‖e_k‖/N
  adjust:  η_{n+1} ≈ 3 ε_n (start large, shrink; loosen if #matches collapses)
  stop if  σ, ε, ε_max below tolerances AND γ ≤ ε_n/ε_{n−1} ≤ 1 (γ ≈ 0.95) for several iterations
```

- ICP converges only locally, so it needs a decent initial guess.
- Variants include point-to-plane, **Generalized-ICP** (Segal et al. 2009), **IMLP/G-IMLOP** (Billings & Taylor, anisotropic noise), **CPD** (Myronenko & Song, probabilistic) and distance maps.

### 5.10 FRE vs TRE (Fitzpatrick, West & Maurer 1998; standard theory)
- **FLE** is the fiducial localization error.
- **FRE** is the RMS residual at the fiducials after registration: `⟨FRE²⟩ ≈ (1 − 2/N) ⟨FLE²⟩` for N fiducials in 3D.
- **TRE** at target r: `⟨TRE²(r)⟩ ≈ (⟨FLE²⟩/N) · (1 + (1/3) Σ_{k=1}^{3} d_k² / f_k²)`. Here `d_k` is the distance of r from principal axis k of the fiducial configuration, and `f_k` is the RMS distance of the fiducials from that axis.
- **FRE is not a good predictor of TRE.** To reduce TRE: spread the fiducials widely, keep the target near their centroid, and use more fiducials.
- This was not found as a standalone CIS I slide in the materials I read. The course's own HW3 instead propagates covariances explicitly. Learn both.

### 5.11 Deformable registration and SSMs
- **PCA shape model:** `x = x̄ + Σ λ_m φ_m`, with mode variance `σ_m²` (eigenvalues). Optionally regularize λ by `Σ λ_m² / σ_m²`.
- **Thin-plate splines / RBFs:** warp from landmark matches.
- **Joint rigid + modes linearized step (PA5):** solve
  `[−sk(s_k)  I  −Q_k] [α; ε; λ] ≈ q_{0,k} − s_k`, where `Q_k = [q_{1,k} … q_{M,k}]`.
  Stack over k, solve by LS, then set `R ← exp(sk(α)) R` (a proper rotation).

### 5.12 Surgical robotics
- **Surgical CAD/CAM** (e.g., ROBODOC: CT plan → pin-based or pinless surface registration → robot mills the cavity) vs **surgical assistants**:
  - teleoperated: da Vinci
  - cooperative "**steady hand**": admittance `ẋ_cmd = K · f_handle` with JHU Steady-Hand/Eye robots, LARS
- **RCM (remote center of motion):** the tool shaft must pass through a fixed port point `p_rcm` (trocar or entry point). It can be built mechanically (parallelogram linkages, e.g., LARS, da Vinci; Taylor's RCM robots) or enforced in software as a virtual fixture.
  - Tool axis: `x(t) = p_tip + t·d`.
  - Constraint: `‖(p_rcm − p_tip) × d‖ ≤ δ`, linearized in Δq via the Jacobian.
- **Virtual fixtures as constrained optimization (Taylor/Kapoor/Li):**

  ```
  Δq_cmd = argmin Σ_i w_i ‖W_i (J_i Δq − Δx_i,des)‖²
           s.t. H J Δq ≥ h (linearized geometric constraints),
                q_min ≤ q + Δq ≤ q_max, velocity/acceleration limits
  ```

  Solve the QP every control cycle in a **mid-level control loop**: read state → safety checks → build objective/constraints → solve → command.
  - Forbidden regions via **signed distance fields**: `SDF(p) ≥ d_thresh` linearized as `∇SDFᵀ J_p Δq ≥ d_thresh − SDF(p)`.
- **Safety:** Therac-25 lessons (Leveson & Turner): software race conditions, overreliance on software interlocks, poor error messages and incident response.

### 5.13 Imaging and segmentation basics (light coverage in CIS I)
- **CT:** X-ray attenuation, Hounsfield units; bone is easy to threshold.
- **MRI:** soft-tissue contrast, possible geometric distortion.
- **Ultrasound:** real-time, speckle, needs a tracked probe plus calibration.
- **Fluoroscopy/C-arm:** 2D projection with **image-intensifier distortion**, needing Bernstein/grid dewarping and intrinsic calibration. 2D/3D registration uses DRRs.
- **Segmentation:** thresholding, partial volume, deformable models/level sets, graph cuts, multi-atlas, CNNs.
- **Surfaces:** marching cubes → triangle mesh → Laplacian smoothing → simplification.

---

## 6. Difficulty and student reviews/tips

**Workload.** Taylor's own slide: *"This course is a lot of work and includes some difficult material… students who do the work tend to do well, even if they get stuck on one assignment. The grading formula is very forgiving."*

**Student reviews** (older aggregated reviews on jhu.semester.ly for 600.445/645, surfaced via search; not current) [S16]:
- Work-intensive; the time commitment was the least liked aspect.
- Recommended to anyone interested in the field.
- Guest lectures were praised.
- Math-intensive; success depends on strong linear algebra.
- Some complaints about inconsistent or harsh grading in the grad section.

Treat these as **(unverified, dated)**.

**Practical tips** (from the rubrics and handouts):
- **Results are only 15-20% of a PA grade. Report plus code quality is 80-85%.** Write the validation section well: unit tests with synthetic known-answer data, tables of error vs debug outputs, and a discussion of each error source.
- **Debug sets isolate error sources.** Use them deliberately (noise-only, distortion-only, jiggle-only).
- The `auxilliary` files reveal the true post positions and registrations for debug sets, so use them.
- Handle file-format quirks: mixed comma and space separators, trailing fields, `fiducialss`, file names like `PA3-A-Debug-SampleReadingsTest.txt` vs the handout's spelling.
- Build a **tested frame/registration library in week 1**; everything depends on it.
- Brute-force closest point first, then the tree. Assert that both agree on random queries.
- Partner choice: Taylor recommends complementary skills (math-strong plus coding-strong).
- Homeworks are long symbolic derivations. Practice "normal-form" linearization until it is mechanical.
- Submit **before class** on the due date; late work loses 10 points per day.

---

## 7. Best free resources and reading list

**Official (start here)**
- Course wiki, schedule pages and all slides/handouts/data: links in §1.
- Taylor's slide decks named in §2. The most important for PAs are `frames`, `calibration`, `interpolationreview`, `lecture-rigid-3d-3d-calculations`, `registration_part_1`, `lecture-finding-point-pairs`, `registration_part_3` and `PA5 notes`.
- B. Killeen, *Frame Transformations in CIS: A Graphical Introduction* (2022), on the schedule as `frame_transformations.pdf`.
- `cispa` Python template: https://github.com/benjamindkilleen/cispa
- cisst libraries: https://github.com/jhu-cisst/cisst

**Papers cited in the slides and handouts**
- K. S. Arun, T. S. Huang, S. D. Blostein, "Least-squares fitting of two 3-D point sets," IEEE PAMI 9(5):698-700, 1987.
- B. K. P. Horn, "Closed-form solution of absolute orientation using unit quaternions," JOSA A 4(4):629-642, 1987.
- P. Besl, N. McKay, "A method for registration of 3-D shapes," IEEE PAMI 14(2), 1992.
- S. Rusinkiewicz, M. Levoy, "Efficient variants of the ICP algorithm" (linked as `fasticp_paper.pdf`): http://www.cs.princeton.edu/~smr/papers/fasticp/fasticp_paper.pdf
- A. Segal, D. Haehnel, S. Thrun, "Generalized-ICP," RSS 2009: http://www.robots.ox.ac.uk/~avsegal/resources/papers/Generalized_ICP.pdf
- S. Billings, E. Boctor, R. H. Taylor, "Iterative Most-Likely Point Registration (IMLP)," PLOS ONE 10(3), 2015: https://doi.org/10.1371/journal.pone.0117688
- S. Billings, R. Taylor, "Generalized IMLOP," IJCARS 10, 2015.
- A. Sinha et al., "The deformable most-likely-point paradigm," Medical Image Analysis 55, 2019.
- A. Myronenko, X. Song, "Point Set Registration: Coherent Point Drift," IEEE PAMI 2010 (the CPD deck).
- T. Cootes, C. Taylor (and Edwards): active appearance / statistical models, ECCV 2000, IPMI 1999 (PA5 handout).
- J. Yao, R. H. Taylor, deformable atlas and mesh template papers, 2002-2003 (PA5 handout).
- M. Fleute, S. Lavallée, MICCAI 1999 (PA5 handout).
- Readings on the 2025 schedule:
  - `Robodoc903.pdf` (ROBODOC paper; likely Taylor et al., IEEE TRA 1994 "An image-directed robotic system for precise orthopaedic surgery", unverified)
  - `Generalized_ICP.pdf`
  - `scattered_point_interpolation…pdf`
  - Stanford **CS229 linear algebra review** (`cs229-linalg.pdf`): https://cs229.stanford.edu/section/cs229-linalg.pdf
- N. Leveson, C. Turner, "An investigation of the Therac-25 accidents," IEEE Computer 1993.

**Suggested extras (not confirmed as assigned)**
- J. M. Fitzpatrick, J. B. West, C. R. Maurer, "Predicting error in rigid-body point-based registration," IEEE TMI 17(5), 1998 (FRE/TRE).
- R. H. Taylor, D. Stoianovici, "Medical robotics in computer-integrated surgery," IEEE TRA 19(5), 2003. A great overview of the whole course in one paper.
- R. H. Taylor, A. Menciassi, G. Fichtinger, P. Fiorini, P. Dario, "Medical Robotics and Computer-Integrated Surgery," *Springer Handbook of Robotics*, 2nd ed., 2016.
- A. Kapoor, M. Li, R. H. Taylor, "Constrained control for surgical assistant robots," ICRA 2006 (virtual fixtures as QP).
- K. Lynch, F. Park, *Modern Robotics* (free PDF, http://modernrobotics.org): ch. 3 (SO(3)/SE(3), exponential coordinates).
- J. Bushberg et al., *The Essential Physics of Medical Imaging* (cited in the slides; not free) for CT/MRI/US/fluoro physics.
- Open3D / 3D Slicer, useful only for *checking* your own ICP on toy data. Never submit them as your implementation.

---

## 8. Gaps and prerequisites to fill before the course

| Gap | Why it matters | Quick fix |
|---|---|---|
| Linear algebra: SVD, eigen-decomposition, least squares (normal equations vs QR/SVD), conditioning | Registration, pivot calibration, Bernstein fit, PA5 LS | CS229 linalg notes; Taylor's `leastsquaresreview` |
| Rotations: SO(3), skew matrices, axis-angle/exponential map, quaternions, small-angle approximations | Every PA and every HW | `frames.pdf`; Modern Robotics ch. 3 |
| Linearization and error propagation in normal form | HW1-HW3 are mostly this | Redo the `frames.pdf` "Errors & sensitivity" examples by hand |
| Multivariate Gaussians: conditioning, fusion, covariance propagation `J C Jᵀ` | HW2/HW3 | Wikipedia "Multivariate normal distribution" (the HW2 handout points there) |
| Computational geometry: barycentric coordinates, point-segment/triangle distance, bounding volumes, k-d/octrees | PA3-PA5 performance and correctness | `lecture-finding-point-pairs`; implement and test |
| Polynomial bases: Bernstein/Bézier, B-splines | PA2 | `interpolationreview` |
| Robot kinematics: Jacobians, redundancy, constrained QP (e.g., `scipy.optimize`/cvxpy/quadprog) | HW4, Medical Robots lectures | RDKDC/ASBR material; Kapoor 2006 |
| Medical imaging physics (CT HU, MRI, US, fluoro distortion) | Context, guest lectures, CIS II projects | *Essential Physics of Medical Imaging* skim; the segmentation decks |
| Engineering hygiene: pytest, modular code, file I/O parsing, reproducible CLI, technical report writing (LaTeX) | 80% of PA points | `cispa` template structure |

---

## 9. Compressed application-first self-study plan (~5 study days within the 30-day sprint)

**Goal:** arrive with a **personal, tested NumPy toolkit** (frames → registration → pivot → Bernstein → closest-point/tree → ICP) and fluency in linearized error algebra.

**Data**
- Download from the Fall 2025/2026 schedule page: `pa_1_student_data.zip`, `pa_2_student_data.zip`, `2025_pa345_student_data.zip`, the PA handouts and the `PA5 notes`.
- If the wiki is unreachable, generate synthetic data (a recipe is below) and validate against known ground truth.
- Do **not** consult public solution repos.
- Re-check the integrity rules about reusing pre-course code once the semester starts.

### Day 1: Frames + registration (≈ 6 h)
1. Read `frames.pdf` (rotations, small frames, error propagation) and the Killeen frame tutorial (1.5 h).
2. Code `frames.py`:
   - `Frame(R, p)`: `@` composition, `inv()`, `apply(points)`
   - `rot(axis, angle)`, `skew()`, quaternion↔R
   - unit tests: `F @ F.inv() == I`, `R Rᵀ = I`, `det R = 1`, round-trip quaternion↔R
3. Code `register(a, b)` with Arun SVD (det fix) **and** the Horn quaternion method. Test on random rigid transforms and noise, both methods agreeing, and a coplanar/reflection case.
4. Pen and paper: derive `Δp_tip` for `p = F_1⁻¹ F_2 t` with `F* = F ΔF`, in normal form. This is the HW1 skill.

### Day 2: Pivot calibration + PA1 end-to-end (≈ 6 h)
1. Read the `calibration.pdf` pivot section and the PA1 handout (1 h).
2. Code a tolerant file parser (comma or whitespace; extra header fields) and `pivot(frames_of_markers)`. The probe model comes from frame 1 with the centroid subtracted; build the stacked `[R_k | −I]` LS. Return `t_tip`, `p_dimple` and the residual RMS.
3. PA1 pipeline:
   - compute `C_expected = F_D⁻¹ F_A c` per frame
   - EM pivot
   - optical pivot, after mapping H into EM coordinates with `F_D⁻¹` per frame
   - write `output1` exactly in the handout format
4. Validate against the debug `output1` and `auxilliary1`. Tabulate the max/RMS error per debug set a-g and explain each set by its error sources.

### Day 3: Distortion correction + navigation (PA2) + error intuition (≈ 6 h)
1. Read `interpolationreview` (Bernstein, "3D calibration of distortion") (1 h).
2. Code `bernstein_basis(u, N)` (vectorized `(n, (N+1)³)` matrix) and `fit_distortion(measured, expected, N)` → (box, coeffs) plus `correct(q)`. Choose N ∈ {2..6} by holding out whole frames.
3. Run the PA2 pipeline (dewarp → re-pivot → `B_j` → `F_reg` → nav tips) and validate against `output2` / `auxilliary2`.
4. Monte Carlo (30 min): add Gaussian FLE to the fiducials. Compare the empirical TRE at the nav points with the Fitzpatrick prediction, and plot FRE vs TRE (they are uncorrelated).

### Day 4: Closest point on a mesh + acceleration (PA3) (≈ 6 h)
1. Read `lecture-finding-point-pairs` and the PA3/PA4 handout (1 h).
2. Code `closest_point_on_triangle(a, p, q, r)`: LS for λ, μ, then edge projection. Test against a dense random sampling of each triangle, including obtuse and degenerate ones.
3. Code brute-force `closest_point_on_mesh` (vectorize over triangles with NumPy).
4. Then a **bounding-sphere array** filter and a **box-tree / k-d tree** over triangle bounding spheres. Assert equality with brute force on 10k random queries and time both.
5. PA3: register A and B per sample, compute `d_k = F_B⁻¹ F_A A_tip`, take `F_reg = I`, write `pa3-X-Output.txt`, and compare with the debug outputs.

### Day 5: ICP (PA4) + overview of PA5, robotics and HWs (≈ 6-7 h)
1. Read the `registration_part_1` ICP outline (30 min).
2. Code ICP:
   - η_n threshold (start large, then `3ε_n`)
   - `ε_n` ratio termination (γ ≈ 0.95)
   - max iterations
   - logs of σ, ε, ε_max and the number of matches per iteration

   Validate on the PA4 debug sets, including Demo-Slow (bad initial guess). Plot the convergence basin by perturbing `F_reg` with random rotations (10-90°).
3. PA5 sketch (1.5 h):
   - load the modes, verify that mode 0 equals the mesh
   - implement the mode-only LS step (`Q λ ≈ s − q_0`)
   - optionally implement the combined linearized step `[−sk(s) I −Q][α;ε;λ] ≈ q_0 − s`
4. Robotics and HW warm-up (1.5 h):
   - skim `medical_robots_part_1` (virtual fixtures as QP, mid-level loop, RCM)
   - write a 20-line `cvxpy`/`scipy` QP that moves a point toward a goal while staying outside an SDF sphere and within joint-velocity limits
   - read the Taylor & Stoianovici 2003 overview

**Synthetic data recipe (if no official data)**
1. Sample a random rigid `F_true`, marker geometries (6-8 points over about a 100 mm spread) and a tip offset (≈ 100-150 mm).
2. Generate K pivot poses by rotating about the dimple: random axes, ±30°.
3. Add N(0, 0.1-0.3 mm) noise.
4. Add a smooth warp, e.g., `q + 0.02·[x y, y z, z x]/100`, for the distortion.
5. For meshes, use an icosphere or `trimesh` primitives (only for generating data).

**Deliverable at the end of the 5 days:** a repo with `frames.py`, `registration.py`, `pivot.py`, `distortion.py`, `mesh.py` (closest point plus tree) and `icp.py`, each with pytest tests and a one-page "validation table". This mirrors what the PA reports require.

---

## 10. Sources

**Primary (course materials, Fall 2025; read from the GitHub mirror [S6] because the wiki host was blocked):**
- [S1] Course home (CIIS wiki): https://ciis.lcsr.jhu.edu/doku.php?id=courses:455-655:455-655 (content seen via search snippets: Fall 2026 info, Tu/Th 1:30-2:45, Hackerman B17)
- [S2] `2025_cis_i_organization.pdf` (Fall 2025 organization slides): rules, grading formula, teams, prerequisites, 2024 grade histogram. Listed on https://ciis.lcsr.jhu.edu/doku.php?id=courses:455-655:2025:fall-2025-schedule
- [S3] Fall 2025 handouts: `programmingassignments1and2.pdf`, `programmingassignments1and2_scenario.pdf`, PA3/PA4 handout, PA5 handout (body still dated "Fall 2023"), `PA5 notes` (2025), PA general-expectations sheet, PA report guide, `cispa*.zip`, `pa_1_student_data.zip`, `pa_2_student_data.zip`, `2025_pa345_student_data.zip`. All are linked from the Fall 2025 schedule page.
- [S4] Fall 2025 homeworks: `2025_homework_assignment_1.pdf` … `_3.pdf`, `homework_4_600-455-2025.pdf` (same schedule page).
- [S5] Lecture decks listed in §2 (same schedule page; media namespace `courses:455-655:lectures:`, e.g., https://ciis.lcsr.jhu.edu/lib/exe/fetch.php?media=courses%3A455-655%3Alectures%3A2021_cis_i_organization.pdf)
- [S6] GitHub mirror and notes used to read the above: https://github.com/mzlumi/cis-surface-registration (`course-archive/fall-2025/`, `docs/handout/`, `data/README.md`) and https://github.com/mzlumi/cis-surgical-navigation (PA1/PA2 notes). **These are a student's solution repositories. Read only the course-material copies, not the solution code, and get the materials from the wiki yourself.**

**Schedules (search snippets only):**
- [S7] Fall 2026 schedule: https://ciis.lcsr.jhu.edu/doku.php?id=courses:455-655:2026:fall-2026-schedule
- [S8] Fall 2024 schedule: https://ciis.lcsr.jhu.edu/doku.php?id=courses%3A455-655%3A2024%3Afall-2024-schedule
- [S9] Fall 2022 schedule: https://ciis.lcsr.jhu.edu/doku.php?id=courses%3A455-655%3A2022%3Afall-2022-schedule ; Fall 2021: https://ciis.lcsr.jhu.edu/doku.php?id=courses%3A455-655%3A2021%3Afall-2021-schedule
- [S10] Legacy pages: https://www.cs.jhu.edu/cista/455 , https://www.cs.jhu.edu/cista/455/455-schedule-2020.html , 2020 PA1/2 handout https://www.cs.jhu.edu/cista/455/Homework_2020/Programming%20Assignments%201%20and%202/ProgrammingAssignments1and2.pdf , PA5 2020 https://www.cs.jhu.edu/cista/455/Homework_2020/Programming%20Assignments%20345/Programming%205%20%20600-445-2020.pdf
- [S11] Course-number note: https://ciis.lcsr.jhu.edu/doku.php?id=courses

**Catalogue, related pages and reviews:**
- [S12] CIS minor (catalogue PDF; EN.601.455 4 credits): https://e-catalogue.jhu.edu/engineering/full-time-residential-programs/degree-programs/robotics-computational-sensing/computer-integrated-surgery-minor/computer-integrated-surgery-minor.pdf ; https://lcsr.jhu.edu/computer-integrated-surgery-minor/ ; catalogue index https://e-catalogue.jhu.edu/course-descriptions/computer_science_601/
- [S13] JHU CS Fall 2026 course list: https://www.cs.jhu.edu/fall26/
- [S14] CIS course sequence page: https://www.cs.jhu.edu/cista/ ; CIS II wiki: https://ciis.lcsr.jhu.edu/doku.php?id=courses:456
- [S15] CIS II showcase news: https://www.cs.jhu.edu/news/engineering-course-showcases-latest-health-care-research
- [S16] Student reviews (aggregator): https://jhu.semester.ly/c/EN.601.455 , https://jhu.semester.ly/c/EN.600.645 , https://jhu.semester.ly/c/EN.600.445
- [S17] Instructor: https://www.cs.jhu.edu/~rht/ ; https://www.cs.jhu.edu/faculty/russell-taylor/
- [S18] Template and library: https://github.com/benjamindkilleen/cispa ; https://github.com/jhu-cisst/cisst

**Open uncertainties**
- Exact Fall 2026 lecture dates and order, and the PA/HW due dates (the schedule page was not fetchable).
- Whether Fall 2026 changed the number of homeworks or PAs. In 2025 there were 4 HW and 5 PA (PA5 optional); 2021-22 had 6 HW.
- The 601.655 meeting time conflict (wiki 1:30-2:45 vs cs.jhu.edu/fall26 3:00-4:15).
- Whether reuse of pre-course self-written code is allowed in graded PAs (ask the TA).
