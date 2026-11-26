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

## Open questions (student to verify; tick when resolved)
- [ ] Exact Computer Vision course number on the student's schedule (research says EN.601.661; alternatives EN.520.665 Machine Perception, EN.520.681 Perception for Embodied AI).
- [ ] Older ME manuals advise **against taking RDKDC (530.646) and ASBR (601.663) in the same semester**: check the 2026–27 Robotics MSE manual / advisor.
- [ ] Open https://cirl.lcsr.jhu.edu/sensorbasedrobotics/ and https://www.cs.jhu.edu/~sleonard/ in a browser to find the missing ASBR weekly decks (session sandbox could not reach JHU hosts).
- [ ] Get CIS I data/handouts from the official CIIS wiki (ciis.lcsr.jhu.edu); session sandbox couldn't reach it.
- [ ] DL: confirm current lecture list, HW5+ content, midterm format (Canvas/Piazza once enrolled).
- [x] Repo visibility → student is to set it to **private** in GitHub Settings (Claude cannot change repo settings).

## Log

### 2026-11-26 — Day 0 (planning)
- Profile update: CS engineering graduate + Associate SWE; start-from-zero is for completeness, not because of zero knowledge. Phase 0 rebalanced: faster programming, deeper math.
- Created `knowledge-base` branch, README entry point, Phase 0 foundations plan (`courses/00-foundations/`).
- Research complete for all six courses → `courses/0X-*/README.md` (sources cited; uncertain facts marked "(unverified)").
- Wrote `STUDY_PLAN_30_DAYS.md` (Days 1–30, two-track days) and `CROSS_COURSE_MAP.md` (12 shared engines, convention traps, GenAI policies).
- Decision: curated MIT 18.06/18.02 path instead of full lecture series (see foundations file).
- **Next: Day 1 (Nov 27): F1, Mac setup + Python end-to-end + Git + start 3B1B linear algebra.**
