# Progress Log

> Append a dated entry after every study session. Newest at the top.
> Future sessions: read the latest entry to know exactly where we are.

## Student profile (stable facts)
- Incoming **MSE Robotics, JHU**. Courses: RDKDC, Computer Vision for Robotics, ML: Deep Learning,
  Hands-on Robot Learning, Algorithms for Sensor-Based Robotics, CIS I.
- **Background:** B.Tech/B.E. in **Computer Science Engineering**; worked as an **Associate Software Engineer**.
  Knows a lot already, but **deliberately starts every topic from zero** to leave **no holes in foundations**.
  - Treat as: "cover everything from first principles, nothing skipped, but move fast where CS/SWE
    background makes it quick (programming, git, tooling); spend the saved time on math rigor."
  - Self-rated not yet comfortable with: Python scientific stack (NumPy, PyTorch), C++, ROS, linear algebra
    (SVD, eigenvalues, matrix exponentials), probability (Bayes' rule, Gaussians).
  - Had browsed a good part of Algorithms for Sensor-Based Robotics ~1 week before the sprint
    (basic concepts understood; not completed).
- **Time:** 16–17 hours/day for 30 days (≈ 480 h total).
- **Machine:** MacBook Pro 16" M1 Max, 64 GB RAM (PyTorch MPS ok; MuJoCo native; ROS 2 via Docker/RoboStack).
- **Learning style:** application-first; build to get intuition, then theory; never stall.
- **Research interests:** deferred until the six courses are done.

## Standing rules
- **Student writes all implementation code.** Claude explains, designs with, debugs with and reviews the student's code,
  but does not write the solutions. Reason: CIS I (2025 handouts) forbids AI-generated code in the PAs and forbids
  prior-year solutions; the same discipline also builds real skill for every other course.
- Never use public solution repos for JHU assignments. Get course materials from official sites only.

## Log

### 2026-11-26 — Day 0 (planning)
- Profile update: CS engineering graduate + Associate SWE; start-from-zero is for completeness, not because of zero knowledge. Phase 0 rebalanced: faster programming, deeper math.
- Created `knowledge-base` branch, README entry point, Phase 0 foundations plan (`courses/00-foundations/`).
- Six research agents gathering full syllabi for each course → `courses/0X-*/README.md`.
- Next: synthesize `STUDY_PLAN_30_DAYS.md` and `CROSS_COURSE_MAP.md` from the research; then start Day 1 (Mac setup + Python).
