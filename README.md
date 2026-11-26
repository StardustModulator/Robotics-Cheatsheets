# Robotics Cheatsheets — Knowledge Base (branch: `knowledge-base`)

> **For any future Claude session:** read this file first, then `PROGRESS.md`, then
> `STUDY_PLAN.md`. Together they restore the full context of this project.

## Who / what / why

- **Student:** incoming **MSE in Robotics at Johns Hopkins University (LCSR)**.
- **Goal:** self-study all six first-semester courses in **30 days** (sprint started
  2026-11-26), so that during the semester the student has time for research and networking.
- **Learning style (important):** **application-first.** Build/code something for each
  concept, then fill in theory. Do not stall on pure theory. Keep moving forward.
- **Repo rule:** this branch (`knowledge-base`) holds only relevant, high-value
  information about the six courses: syllabi, concept checklists, equations, resources,
  plans and progress. Code projects come later (separate folders/branches).

## The six courses

| # | Course (confirmed by research) | Instructor (latest found) | Folder |
|---|---|---|---|
| 0 | Foundations: Python, NumPy, linear algebra, calculus, probability, PyTorch, C++/ROS 2 | — | `courses/00-foundations/` |
| 1 | **EN.530.646** Robot Devices, Kinematics, Dynamics & Control (RDKDC). Core. MLS + Modern Robotics; DH via Keating UR5 IK handout; MATLAB + ROS 2 Jazzy labs on UR5 | Jin Seob Kim (course built by Noah Cowan) | `courses/01-rdkdc/` |
| 2 | **EN.601.661** Computer Vision (no JHU course is titled exactly "CV for Robotics"; **verify on your schedule**). Szeliski 2e; H&Z reference | Kapil Katyal & Greg Hager | `courses/02-computer-vision/` |
| 3 | **EN.601.682** Machine Learning: Deep Learning. NumPy-from-scratch HWs then PyTorch; Kaggle CNN; final project | Mathias Unberath (Fall) | `courses/03-ml-deep-learning/` |
| 4 | **EN.601.698** Hands-on Robot Learning (first offered Fall 2026). Imitation learning + VLAs on real **SO-101** arms with **LeRobot**; DL is a prerequisite | Krishna Murthy Jatavallabhula | `courses/04-hands-on-robot-learning/` |
| 5 | **EN.601.663** Algorithms for Sensor-Based Robotics. Core. 5 C++/ROS 2 assignments; site: cirl.lcsr.jhu.edu/sensorbasedrobotics | Simon Leonard | `courses/05-algorithms-sensor-based-robotics/` |
| 6 | **EN.601.655** Computer Integrated Surgery I. PA1–PA5 on CIIS wiki; take-home HWs instead of exams | Russell Taylor | `courses/06-cis1/` |

Each course folder has a `README.md` with: course identity, reconstructed syllabus,
textbook chapter map, assignments, a concept and equation checklist, reviews and tips,
free resources, prerequisite gaps, an application-first sprint plan and sources.

## Repo map

```
README.md                  ← you are here (entry point / context restore)
STUDY_PLAN.md              ← master plan: subjects, what to learn, how, where (no day-schedule)
PROGRESS.md                ← running log: what's done, what's next, open questions
CROSS_COURSE_MAP.md        ← how concepts overlap between courses (learn once, use 3×)
courses/0X-*/README.md     ← per-course knowledge base
```

## How to resume in a new session

1. `git checkout knowledge-base && git pull`
2. Read `README.md` → `PROGRESS.md` (latest entry) → the current subject in `STUDY_PLAN.md`.
3. Open the relevant `courses/0X-*/README.md` for details.
4. After each study session, append to `PROGRESS.md` and commit.
