# Robotics Cheatsheets — Knowledge Base (branch: `knowledge-base`)

> **For any future Claude session:** read this file first, then `PROGRESS.md`, then
> `STUDY_PLAN_30_DAYS.md`. Together they restore the full context of this project.

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

| # | Course | Folder | Main texts / sources |
|---|--------|--------|----------------------|
| 0 | Foundations: Python, NumPy, linear algebra, calculus, probability, PyTorch (from zero) | `courses/00-foundations/` | Phase 0, Days 1–5 |
| 1 | Robot Devices, Kinematics, Dynamics & Control (RDKDC, EN.530.646) | `courses/01-rdkdc/` | Murray-Li-Sastry *A Mathematical Introduction to Robotic Manipulation*; Lynch & Park *Modern Robotics*; separate DH-parameter material |
| 2 | Computer Vision for Robotics | `courses/02-computer-vision/` | Geometric vision (multi-view geometry) |
| 3 | Machine Learning: Deep Learning (EN.601.482/682) | `courses/03-ml-deep-learning/` | Hands-on, implement models from scratch; rigorous |
| 4 | Hands-on Robot Learning | `courses/04-hands-on-robot-learning/` | Full course is public online |
| 5 | Algorithms for Sensor-Based Robotics (EN.601.463/663) | `courses/05-algorithms-sensor-based-robotics/` | Lecture material on the CIRL/CIIS site; student already covered a good part |
| 6 | Computer Integrated Surgery I (EN.601.455/655) | `courses/06-cis1/` | Full coursework and PAs public on the CIIS wiki (Russell Taylor) |

Each course folder has a `README.md` with: course identity, reconstructed syllabus,
textbook chapter map, assignments, a concept and equation checklist, reviews and tips,
free resources, prerequisite gaps, an application-first sprint plan and sources.

## Repo map

```
README.md                  ← you are here (entry point / context restore)
STUDY_PLAN_30_DAYS.md      ← master day-by-day plan across all 6 courses
PROGRESS.md                ← running log: what's done, what's next, open questions
CROSS_COURSE_MAP.md        ← how concepts overlap between courses (learn once, use 3×)
courses/0X-*/README.md     ← per-course knowledge base
```

## How to resume in a new session

1. `git checkout knowledge-base && git pull`
2. Read `README.md` → `PROGRESS.md` (latest entry) → today's block in `STUDY_PLAN_30_DAYS.md`.
3. Open the relevant `courses/0X-*/README.md` for details.
4. After each study session, append to `PROGRESS.md` and commit.
