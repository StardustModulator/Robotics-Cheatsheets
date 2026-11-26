# Murray, Li & Sastry — *A Mathematical Introduction to Robotic Manipulation* (1994) — Part 1 (Ch. 1–4, start of Ch. 5)
Source: `09eb9223-mls94-complete.pdf` (474 PDF pages, free electronic edition from Caltech), PDF pages 1–240 (front matter + book pp. 1–222).

**Page offset (verified at every chapter opener): PDF page = book page + 18.** (Front matter i–xix = PDF 1–18.)

## Chapter → page map (whole book)
| Ch. | Title | Book pp. | PDF pp. | Lessons |
|---|---|---|---|---|
| — | Contents / Preface / Acknowledgements | vii–xix | 7–18 | — |
| 1 | Introduction (history, hands, outline) | 1–18 | 19–36 | context only |
| 2 | Rigid Body Motion | 19–80 | 37–98 | F3, F7, R1, R2, A1, C1 |
| 2.1 | Rigid body transformations | 20–22 | 38–40 | F3, R1 |
| 2.2 | Rotational motion in R³ (SO(3), exp coords, Rodrigues, Euler angles, quaternions) | 22–34 | 40–52 | F3, F7, R1 |
| 2.3 | Rigid motion in R³ (homogeneous coords, twists, screws, Chasles) | 34–51 | 52–69 | R2 |
| 2.4 | Velocity of a rigid body (spatial/body velocity, adjoint) | 51–61 | 69–79 | R2, R5, A1 |
| 2.5 | Wrenches and reciprocal screws | 61–70 | 79–88 | R5 (statics) |
| 2.6–2.8 | Summary / Bibliography / Exercises | 70–80 | 88–98 | R1, R2 |
| 3 | Manipulator Kinematics | 81–154 | 99–172 | R3–R6, A1 |
| 3.2 | Forward kinematics, PoE, workspace | 83–97 | 101–115 | R3, R4 |
| 3.3 | Inverse kinematics, Paden–Kahan subproblems | 97–115 | 115–133 | R6 |
| 3.4 | Manipulator Jacobian, statics, singularities, manipulability | 115–129 | 133–147 | R5 |
| 3.5 | Redundant & parallel manipulators (four-bar, Stewart) | 129–143 | 147–161 | R5, R6 |
| 3.6–3.8 | Summary / Bibliography / Exercises | 143–154 | 161–172 | R3–R6 |
| 4 | Robot Dynamics and Control | 155–210 | 173–228 | R7, R8 |
| 4.2 | Lagrange's equations, inertia, 2-link example, Newton–Euler | 156–168 | 174–186 | R7 |
| 4.3 | Dynamics of open chains (M, C, N; PoE form) | 168–179 | 186–197 | R7 |
| 4.4 | Lyapunov stability theory | 179–189 | 197–207 | R8 |
| 4.5 | Position control & trajectory tracking (computed torque, PD, workspace) | 189–200 | 207–218 | R8 |
| 4.6 | Control of constrained manipulators | 200–206 | 218–224 | R8 |
| 4.7–4.9 | Summary / Bibliography / Exercises | 206–210 | 224–228 | R7, R8 |
| 5 | Multifingered Hand Kinematics (grasp statics, force closure, rolling) | 211–264 | 229–282 | (C8/H-ish; mostly beyond the six courses) |
| 6 | Hand Dynamics and Control | 265–316 | 283–334 | — |
| 7 | Nonholonomic Behavior in Robotic Systems | 317–354 | 335–372 | (A3 background) |
| 8 | Nonholonomic Motion Planning | 355–394 | 373–412 | (A3/A4 background) |
| 9 | Future Prospects | 395–402 | 413–420 | — |
| A | Lie Groups and Robot Kinematics | 403–434 | 421–452 | R1, R2 (advanced) |
| B | Mathematica package for screw calculus | 435–440 | 453–458 | (tools) |
| — | Bibliography / Index | 441–456 | 459–474 | — |

**This file covers:** PDF 1–240 = front matter, Ch. 1, Ch. 2, Ch. 3, Ch. 4 in full, and Ch. 5 §1–§2.2 (to book p. 222).

---

## Front matter & Chapter 1 — Introduction (PDF pp. 1–36, book pp. i–18) — Lessons: context
**Big idea:** MLS is deliberately "more abstract" than Craig/Spong: everything is built on the exponential map, twists and wrenches, so kinematics (PoE), Jacobians and dynamics all fall out of one geometric picture. The preface says a senior course covers Ch. 1–4 in 12 weeks — exactly the RDKDC scope. Prerequisites are linear algebra and some signals & systems; "the student who can master the concepts in Chapter 2 should have no difficulty with the remainder of the book."

**Content (brief):**
- §1 Brief history: teleoperators at Argonne/Oak Ridge → Devol's 1954 "programmed articulated transfer device" → Unimation (first robot at GM 1961) → Stanford arm, WAVE language (1973), Cincinnati Milacron T³ (1974), PUMA (1978), SCARA (1979), CMU direct-drive arm (Asada 1981); 1980s research on recursive Newton–Euler/Lagrangian computation, computed torque, hybrid/compliant control, flexible joints.
- §2 Multifingered hands: limits of grippers (lack of dexterity, few grasps, big arm motions for small end-effector motions, no fine force control); prosthetic history (Berlichingen 1509; Childress' four classes: cosmetic, passive, body-powered, externally powered); research hands — Salisbury (Stanford/JPL, 3 fingers × 3 DOF, cable driven), Utah/MIT (4 × 4 DOF, pneumatic tendons), NYU planar hand, Berkeley Styx.
- §3 Outline: Ch. 2 rigid motion via exponential map/screws; Ch. 3 PoE kinematics (Brockett [12]) instead of Denavit–Hartenberg, Paden–Kahan subproblems, Jacobian columns = twist axes; Ch. 4 Lagrangian dynamics + Lyapunov + computed torque and PD control; Ch. 5–6 grasping and hand dynamics; Ch. 7–8 nonholonomic systems.

**Conventions / pitfalls:** The book avoids numerical exercises on purpose — you are expected to make up numbers from a real robot (for RDKDC: the UR5).

