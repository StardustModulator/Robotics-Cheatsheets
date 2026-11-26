# RDKDC final project: `RDKDCFinalProject.pdf` (11 pages)
Source: `External_Resources/RDKDCFinalProject.pdf`. All 11 pages were read: text from the extraction, and figures and equations checked visually.

## What this file actually is
**This is not the course handout.** It is a **student team's final-project report**: "Robot Devices, Kinematics, Dynamic and Control (EN.530.646) — Final Project", by Kinjal Shah, Divya Ramesh, Wei-Lun Huang and Ge Sun.

**Date and setting:**
- The report gives no date and no instructor name.
- The PDF was compiled on **2020-09-27** with pdfTeX 1.40.19.
- The demo used a **real UR5 in the lab** (group photo, p. 10) plus **ROS 1 RViz** with `base_link`/`tool0` frames, the `ur5.get_current_joints`/`ur5.home` interface, and Keating's 2014 *UR5 Inverse Kinematics*.
- So the project was most likely done in **Fall 2019**, before COVID. This is **unverified**: the PDF could have been rebuilt later for a portfolio.

**Links:**
- This is the same project as README source [31] (kinjmshah.github.io/portfolio/rdkdc).
- Videos: main task https://youtu.be/9ZHz0VcOGyU; extra credit https://youtu.be/aduOeCUExFI.

**Academic integrity:** this is a past team's solution write-up. Under the student's rules (`PROGRESS.md`: no prior-year solutions; write all your own code), use it only to learn **what the task demanded and which pitfalls exist**, never as a template. The sections below describe the task and the design questions and flag the report's errors. They do not give an implementation recipe.

---

## 1. Task requirements that the report reveals ("as per the project instructions")
**Main task: "Place and Mark with Intention".** The report's video link is labelled "Pick and Place with Intention", which looks like a leftover from the older pick-and-place handout.

1. **Teach** poses by moving the robot by hand (UR interface or pendant), read the joints with `ur5.get_current_joints`, and turn them into poses with your own `ur5FwdKin`.
2. **Show "intention":** before moving, the robot visibly signals which way it is about to go.
3. **Motion profile**, run with **each of three controllers**:
   1. from the start, go to a point *above* mark 1;
   2. move down to mark 1;
   3. move back up;
   4. move above mark 2;
   5. move down to mark 2;
   6. move back up;
   7. go to `ur5.home`.
   - The team chose "above" = 5 cm.
4. **Three controllers** (each must reach the targets on the real UR5):
   - (a) **IK-based:** use the provided analytic IK (Keating, Paden–Kahan SP1–3; 8 solutions) and choose one solution;
   - (b) **resolved-rate (RR)** with the body Jacobian;
   - (c) **gradient / Jacobian-transpose (JT)**.
5. **Extra credit** (this team's choice): write "RDKDC" on a sheet of paper placed at **any orientation**.
6. **Report contents implied:** algorithm per controller, RViz evidence, live-demo videos, a **workload-distribution** section, and an appendix (singularities).

**Not in this file** (it is a report, not the handout). For these, see the Cowan 2017 handout in README source [14b], or the current handout once enrolled:
- required function names and signatures;
- grading breakdown;
- safety rules;
- due dates;
- team size;
- submission format.

## 2. Frames
- The report notes that Keating's IK (and the robot's own TF) uses **`base_link` → `tool0`**, "rather than the S and T frames we leveraged in **Homework 6**". So HW6 defined course S/T frames for the UR5 (this matches the README's HW6 = UR5 FK/Jacobian).
- The team **re-derived the PoE directly in `base_link`→`tool0`** instead of building constant `g_S0` and `g_6T` bridges to Keating's DH frames 0 and 6. Either route works if every function uses the same pair of frames.
- **Data as printed (p. 3):**
  - $\omega_1=(0,0,1)$; $\omega_{2,3,4}=(0,1,0)$; $\omega_5=(0,0,-1)$; $\omega_6=(0,1,0)$;
  - $q_1=0$, $q_2=(0,0,L_0)$, $q_3=(L_1,0,L_0)$, $q_4=(L_1+L_2,0,L_0)$, $q_5=(L_1+L_2,L_3,0)$, $q_6=(L_1+L_2,0,L_0-L_4)$;
  - $g_{bt}(0)=(R_x(\pi/2)R_y(-\pi),\ (L_1+L_2,\ L_3+L_5,\ L_0-L_4))$.
  - The $L_i$ are never given numerically, so these values could not be checked against the UR5 table. **Do not reuse them.** Derive your own and test against TF.

## 3. Controller designs in the report (summary only)
**IK selection:** from the 8 IK solutions, discard any that are:
- near-singular (manipulability $|\mu|\le10^{-4}$);
- "below the table" (heuristic: $\theta_2\notin(-\pi,0)$);
- colliding (heuristic: tool height $\le0$).

Then pick the solution with the smallest $\|q_i-q_{start}\|$.

**RR loop:**
- error twist from $e^{\hat\xi}=g_d^{-1}g_{st}$;
- update $q_{t+1}=q_t-K\,T_{step}\,J_b^{-1}\xi_t$;
- abort and return $-1$ if $|\mu|<10^{-3}$;
- stop when $\|v\|<0.0035$ m and $\|\omega\|<\pi/24$ rad (7.5°);
- report the final position error in cm.
- This is the same law as Lab 3 and as the 2017 lecture notes (`rdkdc-handwritten-notes.md` L10).

**JT loop:**
- same structure, with $J_b^T$ instead of $J_b^{-1}$;
- added an adaptive step: if the position error stalls, they rescale $T_{step}$ by 10.

**Reported results:**
- IK is fastest and exact;
- RR is slower near the goal;
- JT is the slowest.
- All three are shown in RViz (Figs. 3–5) and on hardware.

**Extra credit:**
- teach 27 stroke points and the 4 paper corners once;
- re-teach 2 corners after moving the paper;
- re-map the points by a planar rotation and translation;
- execute with the IK controller using "above-before" and "above-after" point lists.

**Singularities listed:** $\theta_3\in\{0,\pi\}$, $\theta_5\in\{0,\pi\}$, and "$\theta_2,\theta_3,\theta_4\in\{0,\pi\}$ simultaneously".

**Practical lessons worth keeping (not code):**
- **`ur5.home` (arm straight up) is singular,** so plan a non-singular intermediate pose before running RR or JT.
- **Choose the IK branch** by joint-space distance plus singularity and table checks.
- **Communication pauses** between MATLAB and the UR5 limit speed.
- A **pen offset change** between teaching and running shifts the drawing (Fig. 6). Measure the tool offset ($g_{6T}$ or tool0→pen tip) once and keep it fixed.

## 4. Errors in the report (do not copy)
1. **PoE product in the wrong order (p. 3).** The report writes $g_{bt}=e^{\hat\xi_6\theta_6}e^{\hat\xi_5\theta_5}\cdots e^{\hat\xi_1\theta_1}g_{bt}(0)$. With twists taken at the home configuration it must be $e^{\hat\xi_1\theta_1}\cdots e^{\hat\xi_6\theta_6}g_{bt}(0)$ (MLS (3.3)). The reversed order is only valid with *moved* axes; see notes p. 58.
2. **Intention-pose rules (p. 2) are contradictory.** Rules 2/3 and 4/5 each repeat the same condition ("greater than"), but one of each pair must be "less than".
3. **Paper angle uses $\tan^{-1}(\Delta p_x/\Delta p_y)$ (pp. 8–9).** This loses the quadrant; use `atan2`.
   - The back-rotation in step (m) has the wrong sign on one term: the second row should be $-x\sin\theta+y\cos\theta$, the inverse of step (g).
   - Step (n) adds $p_{iy}$ to itself; it should add $p^{TL}_{c,y}$.
   - The course's own tool, composing SE(3) frames for the paper, avoids these hand-written trig errors.
4. **Unit slip (p. 7):** "translational error ≈ 0.0035 mm" for a 0.0035 **m** tolerance.
5. **The third "singularity" (p. 10) is not the UR5 shoulder singularity.**
   - $\theta_2,\theta_3,\theta_4\in\{0,\pi\}$ together is already covered by $\theta_3\in\{0,\pi\}$.
   - The real shoulder singularity is the wrist centre lying on the joint-1 axis (README §6).
6. **Inconsistent manipulability thresholds:** $10^{-4}$ for IK filtering vs $10^{-3}$ for the loops. The written check "$|\mu>0.0001|$" has the absolute-value bars misplaced.
7. **Collision test "$p_i>0$"** compares a vector with a scalar. Presumably the $z$ component was meant.

## 5. Comparison with `courses/01-rdkdc/README.md` §5.3 (mismatches and missing items)
| README says | This report shows | Action |
|---|---|---|
| 2017–2021 = pick-and-place a **cube with a gripper**; ~2022–2025 = **place-and-draw** with a pen | A **marker "place-and-mark"** task already existed by 2019/2020 (compiled Sept 2020); the video label "Pick and Place with Intention" suggests a transitional handout | Re-date: the pen/marker variant existed by ≤2020 |
| "Writing RDKDC" and tic-tac-toe extras are listed under ~2022–2025 [16][31] | Source [31] (kinjmshah) **is this project**: "write RDKDC at any orientation" as **extra credit**, ≤2020 | Fix the year attribution of [31] |
| Students define constant `g_S0` (Keating frame 0 → S) and `g_6T` (6 → T) | This team skipped DH frames and wrote the PoE in `base_link`→`tool0`; HW6 used separate course S/T frames | Add: "any consistent frame pair works; match TF `base_link`/`tool0` when you check in RViz" |
| — | **"Intention"** requirement: signal the motion direction before moving | Add to the task list |
| — | **Approach/retreat via an "above mark" pose** for every mark | Add |
| — | **`ur5.home` is singular**, so a custom non-singular start pose is needed before RR/JT | Add to pitfalls |
| Lab 3 RR thresholds "start around 5 cm and 15°" | Final project used **0.35 cm and 7.5°** | Note: tighten for the project |
| Teach by jogging; choose IK by manipulability | Confirmed. IK branch also filtered by table/collision heuristics and minimum joint motion | Add the selection criteria (as criteria, not code) |
| Deliverables: repo with `ur5_project.m` (mode select), PDF report, 2–4 min video | Report with algorithms, RViz figures, workload section, two YouTube videos. Repo and main-script name are not visible | Consistent; repo/script still unverified |
| Minimum task ≈ B/B+, extra needed for A | The report labels "Extra Credit Task" | Consistent (grading not in this file) |
| Shoulder singularity = wrist centre on the joint-1 axis | The report's list misses it (see §4.5) | Keep the README version |

**Still unknown after reading this file:**
- the official handout text for the student's term;
- grading percentages;
- safety rules (e-stop, speed limits, TA supervision);
- function signatures;
- due dates.

Get these from the Fall 2026 or Spring 2027 handout (Kim).
