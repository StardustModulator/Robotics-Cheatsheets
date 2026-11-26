# Path to 100% (mirror of Notion page "7 · Path to 100%")

Notion: https://app.notion.com/p/424fe2d3cc0281f88829fddc38786e18

The lessons reach ~80%. The rest normally happens only inside each course: real robots, exact lab tools, assignment-sized builds, team projects, exams. Close each gap with a substitute now. "100%" = every graded skill and tool already practised; only official graded submissions and in-person demos remain for the semester.

**Levers:**
1. Simulate the hardware.
2. Buy an SO-101 kit.
3. Shadow assignments (self-designed, same tools and deliverables; never past students' code).
4. Mock exams (≥ 85%, Claude writes and grades them).
5. Course-format deliverables in LaTeX.
6. Get the real course materials from your Mac; meet classmates early.

| Subject | Now | Gap closers | Done when |
|---|---|---|---|
| RDKDC | 85% | Ubuntu 24.04 VM + ROS 2 Jazzy + `rdkdc_jazzy` sim (URSim optional); MATLAB + ROS/Robotics toolboxes; Lab 1–3 functions rewritten in MATLAB with course names; full place-and-draw project in sim (3 controllers) with report; 2 mock exams | straight pen line in sim with all 3 controllers; ≥ 85% on both mocks |
| CIS I | 85% | Official PA1–PA5 + HW1–HW4 from CIIS wiki; full pipeline on official data with debug comparison, tests, LaTeX reports; HWs by hand; ask TA before reusing pre-course code | matches all debug outputs; explain every HW aloud |
| ASBR | 85% | Leonard's full slide folder (incl. week 5); shadow assignments: UR5e resolved-rate (Gazebo, C++/ROS 2), hand-eye (MATLAB, sim), MoveIt 2 EST plugin, EKF GPS+IMU on sim Jackal/Husky, likelihood-field MCL vs AMCL; mock midterm + final | all 5 run in sim; pass both mocks |
| Deep Learning | 80% | CS231n A1–A3; Karpathy series through nanoGPT; HW1–3-style derivations in LaTeX; CIFAR-100 Kaggle-style run + 2-page ablation; solo project (proposal → report → poster), robotics topic | gradient checks pass; project report + poster done |
| Computer Vision | 80% | CMU 16-385 / CS231A problem sets; detector + U-Net + SuperPoint/LightGlue; VO or object-pose project evaluated vs ground truth; mock exam | project evaluated; mock passed |
| Robot Learning | 60% | SO-101 leader/follower kit (or LeRobot sim + MuJoCo SO-100/101 model); course slides + demos; A0 build/calibrate; A1 RGB sense-plan-act; A2 ~50 demos → ACT + Diffusion Policy, STEP over 25+ matched rollouts; A3 SmolVLA fine-tune; rehearse with 2 future classmates | learned policy + VLA running on a real arm with an honest statistical comparison |

**Time:** these extend the lesson builds. Net extra is roughly 80–120 h, mostly Robot Learning hardware, the DL project and the ASBR shadow assignments. Do the cheap, high-value items first: simulators, MATLAB, mock exams. Hardware and project work goes in any time between the sprint and the first day of classes.
