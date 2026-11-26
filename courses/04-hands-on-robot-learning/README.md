# Hands-on Robot Learning (JHU EN.601.498 / EN.601.698)

> Self-study cheatsheet for an incoming JHU MSE Robotics student.
> Main sources: the course's public website source repo
> (`github.com/hands-on-robot-learning/hands-on-robot-learning.github.io`, last commit 2026-11-05),
> which holds the syllabus PDF, the lecture slide PDFs and the assignment handouts. I read all of them.
> Anything I could not confirm from a primary source is marked **(unverified)**.

---

## TL;DR: read this first

| | |
|---|---|
| **Course** | **EN.601.498 (undergrad) / EN.601.698 (grad)**: *Hands-on Robot Learning*, 3 credits, Dept. of Computer Science |
| **Instructor** | **Krishna Murthy Jatavallabhula**, Assistant Professor of CS (https://krrish94.github.io/) |
| **First offering** | Fall 2026: Thursdays 3:00–5:30 PM, Ames 234. One long lecture per week. |
| **Website** | **https://hands-on-robot-learning.github.io/** (Astro/Starlight site; source at https://github.com/hands-on-robot-learning/hands-on-robot-learning.github.io) |
| **What it is** | A **full-stack, real-hardware imitation-learning course**. Teams of 3 build an **SO-101** arm and teleoperate it with a leader–follower pair. They record LeRobot-format datasets, train **ACT / Diffusion Policy**, deploy them, and evaluate with statistical tests (STEP). Then they build a **multi-task / VLA / agentic** system for a demo in front of a faculty panel. |
| **What it is NOT** | **It is not a reinforcement-learning course.** The slides contain no MDP-solving, Bellman equations, PPO, SAC or Q-learning. RL shows up only at the edges: RECAP / advantage-conditioned VLA post-training (π0.6*), DSRL (latent-noise RL steering of diffusion policies), and reward/value models in the world-models lecture. **Sim-to-real and domain randomization are not lecture topics.** Robustness is handled with real-world data variation and image augmentation. |
| **Exams** | None. Grade = A0 5% + A1 10% + A2 30% + A3 35% + participation/individual 20%. |
| **Prereqs** | **Required:** Intro to ML (EN.601.475/675) **or** Deep Learning (EN.601.482/682). You must already be able to train and debug **transformers and diffusion models**. **Preferred:** Intro to Robotics (EN.601.463, EN.601.495, EN.530.420), *required for undergrads*. |

**Biggest implication for self-study:** spend the time on **PyTorch transformer and diffusion fluency, LeRobot, and the kinematics and control basics of a 5-DOF arm**. Don't spend it on PPO/SAC. RL fundamentals are still in this sheet (§4.6) because the user asked for them and other JHU robotics courses use them, but they are **optional** for this course.

---

## 1. Course identity

| Field | Value | Source |
|---|---|---|
| Number | EN.601.498 / EN.601.698 (syllabus header "EN.601.[4\|6]98" on slides) | site `index.mdx`, syllabus PDF, slide title pages |
| Title | Hands-on Robot Learning | same |
| Credits | 3 | syllabus PDF p.1 |
| Semester | Fall 2026 (Sep 3 – Dec 10, 2026) | syllabus + site schedule |
| Meeting | Thursdays 3:00–5:30 PM, Ames 234 | site |
| Instructor | Krishna Murthy Jatavallabhula, kmj@jhu.edu, office Hackerman 226E; OH Thu 12–1 PM (email ahead) | syllabus, site |
| Course assistants | Xinyi Yin (Wed 2–3 PM, Shriver 001), Yinzhe (Steven) Zhou (Fri 9:30–10:30 AM, Wyman Park 265W), Aleks Santari (Tue 6–7 PM, Malone 122) | site |
| Platforms | Canvas (readings, submissions, grades); public website for slides, handouts and schedule | syllabus |
| GitHub org | https://github.com/hands-on-robot-learning has **one** public repo (the website). **No public starter-code repo.** | GitHub org page |
| Next offering | Unknown. Kits "will be reused in future offerings of this class" (syllabus), so a repeat is likely, but no term is announced **(unverified)**. | syllabus p.7 |

### Instructor background
- Assistant Professor of CS at JHU, affiliated with the Data Science and AI Institute. Research: full-stack robotic systems at the perception–action interface, structured world models, and sensorimotor learning. PhD Université de Montréal; postdoc at MIT CSAIL. Some older JHU pages also list him as a research scientist at Meta AI before joining (search-snippet summary; JHU pages were not directly fetchable from this session).
  - https://www.cs.jhu.edu/faculty/krishna-murthy-jatavallabhula
  - https://engineering.jhu.edu/faculty/krishna-murthy-jatavallabhula/
- Well-known prior work (from memory, **unverified** here): ConceptFusion, gradSLAM, ∇Sim. These reflect a 3D perception / world-model bent, which explains the world-models lecture.

### Official description (verbatim, abridged)
> "This course provides a hands-on introduction to the design and implementation of modern learning-based robotic systems. Students will work in small teams to assemble and operate an SO-101 arm, collect robot data, and develop the software needed to train and deploy learned policies."
>
> "…we will take a 'full-stack' perspective of robot learning… task design and selection, curating and processing data, training neural network models on the processed data, deploying these trained models, and evaluating how the trained approaches fare."
>
> "**This is not an introductory class.** We expect students to already have requisite knowledge in deep learning (training and debugging transformer models, diffusion models) or be able to pick them up independently. We will do an extremely short recap of the essentials (one 2.5-hour lecture)."
>
> "Roughly half of your time in this course will be spent outside the classroom, building, collecting data on, deploying policies on, and debugging, the SO-101 robot arm… Brace for hardware and/or software failures happening frequently."

### Course goals (verbatim list)
1. Describe the modern landscape of data-driven robot learning and common system designs.
2. Assemble, calibrate, and safely operate a robotic manipulator built from readily available electromechanical components.
3. Implement, train, and evaluate modern imitation learning approaches for simple manipulation tasks.
4. Deploy a vision-language-action (VLA) model on a physical robot and characterize its success and failure modes.
5. Compare tradeoffs between policy architectures, low-level design choices, and data collection methods.
6. Define evaluation metrics and failure taxonomies, and conduct reproducible robot experiments.

### Key policies worth knowing
- **AI tools: fully allowed** (coding assistants, agents, anything), but you must disclose what you used and own every line. Robot damage caused by AI-generated code that violates joint limits is on you.
- **Compute: not provided.** Assignments are "designed to be completed with modest GPU access". Use Colab, Kaggle, HF or your own GPUs.
- **Scientific integrity:** evaluation videos must be unedited and continuous. A *"20% success rate, honestly measured and carefully analyzed"* beats an unreproducible 90%. Staff **expect 30–65% success rates** on most tasks and get suspicious of near-perfect numbers.
- **Individual accountability:** staff quiz **every** team member on the **whole** system: data, architecture, training, inference, control, failures. *"I only worked on the cameras" is not acceptable.*

---

## 2. Lecture-by-lecture schedule (Fall 2026, as actually run)

The site's "updated schedule" differs from the syllabus PDF's tentative calendar. The actual schedule is below. Slide PDFs are at `https://hands-on-robot-learning.github.io/slides/<file>.pdf`.

| # | Date | Module / Title | Topics (from site and slides) | Slides | Key readings (linked from site or cited on slides) |
|---|---|---|---|---|---|
| L1 | Sep 3 | **M1 · Introduction to modern robot learning** | The data-driven robotics lifecycle: task/embodiment spec → collect/curate data → train/adapt policy → deploy → evaluate → diagnose and iterate. State of the art. | none posted | — |
| L2 | Sep 10 | **Essentials (whirlwind recap)** | Robot design and payload/torque computations; smart servos (FeeTech STS3215, inner PID loop, H-bridge); SO-101 anatomy; motor datasheets; joint vs Cartesian space; FK/IK (analytic and numerical); Jacobians and singularities (SO-101 Jacobian is **6×5, rank-deficient**: a 5-DOF arm can't reach arbitrary 6D poses); PID tuning (overshoot, settling, steady-state error); impedance/stiffness intuition; ML recap (attention, transformer blocks, ViT patch embeddings). | [lecture-02](https://hands-on-robot-learning.github.io/slides/lecture-02-essentials-whirlwind-recap.pdf) | [STS3215 datasheet](https://core-electronics.com.au/attachments/uploads/sts3215-smart-servo-datasheet-translated.pdf); [SO-101 servo testing](https://robonine.com/testing-of-feetech-sts3215-servomotor-backlash-repeatability-and-torque/); [H-bridge](https://www.circuitbread.com/ee-faq/how-does-an-h-bridge-work); [Spong, *Robot Modeling and Control*](https://arl.human.cornell.edu/879Readings/Spong_RobotModeling.pdf); [MIT 6.S191](https://introtodeeplearning.com/); site applets: [IK](https://hands-on-robot-learning.github.io/applets/inverse-kinematics.html), [DoF](https://hands-on-robot-learning.github.io/applets/degrees-of-freedom.html) |
| L3 | Sep 17 | **M2 · Data for robot learning: teleop and other sources** | What a demo should contain; **commanded vs realized poses** (train on *commanded* leader targets; many public datasets don't say which they log); kinesthetic teaching; **leader–follower teleop** (SO-101 leader/follower, 12-bit encoders, calibration); GELLO and gravity compensation; task-space and VR teleop; wearables and **UMI**; dexterous/whole-body retargeting (DexUMI); browsing a LeRobot-format dataset; open problems in scalable data collection. | [lecture-03](https://hands-on-robot-learning.github.io/slides/lecture-03-data-for-robot-learning.pdf) | [GELLO](https://wuphilipp.github.io/gello_site/); [NVIDIA SO-101 calibration guide](https://docs.nvidia.com/learning/physical-ai/sim-to-real-so-101/latest/07-calibrating-so101.html); [UMI](https://real.stanford.edu/umi/); [DROID](https://droid-dataset.github.io/); [DexUMI](https://proceedings.mlr.press/v305/xu25b.html) |
| L4 | Sep 24 | **M3 · Imitation learning and action chunking** | States vs observations, policies π(aₜ\|oₜ), MDP definition (POMDPs explicitly *not* covered); **BC as supervised learning** (MSE / likelihood); **covariate shift and compounding error O(εT²)**; recovery data (NVIDIA driving side-cameras), **DART** noise injection, **DAgger**; 4 evaluation strategies (val loss → replay → sim → real); **causal confusion**; **action chunking** and observation/prediction/execution horizons; **ACT** (CVAE, transformer decoder with one query per future step, L1 loss, z=0 at inference, temporal ensembling); practical gotchas; controller gains matter; *why* chunking works (Simchowitz et al.; Zhang et al.; Lazzati et al.). | [lecture-04](https://hands-on-robot-learning.github.io/slides/lecture-04-imitation-learning-action-chunking.pdf) | Ross et al. [DAgger](https://arxiv.org/abs/1011.0686); Laskey et al. [DART](https://arxiv.org/abs/1703.09327); de Haan et al. [Causal Confusion](https://arxiv.org/abs/1905.11979); Bojarski et al. [End-to-end driving](https://arxiv.org/abs/1604.07316); Zhao et al. [ACT/ALOHA](https://arxiv.org/abs/2304.13705); Simchowitz et al., *Pitfalls of IL when actions are continuous* (COLT 2025); Zhang et al., *Action chunking and exploratory data collection…* (ICLR 2026); Bronars et al., *Tune to Learn: controller gains* (RSS 2026) |
| — | Oct 1 | **SO-101 assembly and calibration** (in-class) | Arm assembly, motor ID setup, calibration, camera checks, safety. **A0.** Bring a laptop with LeRobot installed (1 per team). | — | LeRobot SO-101 docs (§7) |
| L6 | Oct 8 | **Generative policies** | Multimodality (MSE averages modes, so the robot drives *through* the obstacle); desiderata: plausibility, multimodality, conditioning, configurability; action-generation families (deterministic, mixture/latent, energy-based, diffusion/flow); **DDPM** forward/reverse, ε-prediction loss, sinusoidal timestep embedding, DDPM vs DDIM; **flow matching**; **Diffusion Policy** (vision encoder + denoising action head over action chunks); preprocessing (drop zero-action frames, pad episode ends, normalize the gripper carefully); **LeRobot SO-101 DP defaults**; DP3, EquiDiff, factorized DP; **CUPID** data curation; Ambient diffusion; steering via noise (**golden tickets**), **DSRL**, cost **guidance**, **policy composition**; **inference stack**: async execution, **real-time chunking (RTC)**, smooth-as-butter. | [lecture-06](https://hands-on-robot-learning.github.io/slides/lecture-06-generative-policies.pdf) + 14 [interactive demos](https://hands-on-robot-learning.github.io/demos/week06/) | [DDPM](https://hojonathanho.github.io/diffusion/); [Diffusion Policy](https://diffusion-policy.cs.columbia.edu/); [3D Diffusion Policy](https://3d-diffusion-policy.github.io/); [EquiDiff](https://equidiff.github.io/); [CUPID](https://cupid-curation.github.io/); [DSRL](https://diffusion-steering.github.io/); [Golden Ticket](https://lottery-tickets.rai-inst.com/); [Real-Time Chunking](https://www.pi.website/research/real_time_chunking); [Smooth-As-Butter](https://alexander-soare.github.io/robotics/2025/08/05/smooth-as-butter-robot-policies.html) |
| L7a | Oct 15 | **M4 · Multitask policy learning and VLAs** | Generalization and multitask; task-conditioned DP and **Large Behavior Models** (TRI); language-conditioned policies (Lynch & Sermanet, BC-Z), early VLAs (Gato, **RT-1**, SayCan, PaLM-E, **RT-2**, RT-X/Open-X, RT-H, RT-Trajectory, RT-Sketch, RT-Affordance); **action tokenization**: uniform binning → **FAST** (DCT + BPE) → **VQ-BeT** (residual VQ) → **OAT** (ordered tokens, FSQ, nested dropout); continuous action representations (absolute/delta, EEF/joint, relative-to-first vs previous); **Octo**, **π0** (PaliGemma ~3B + ~300M flow-matching action expert), GEN-0; **GR00T N1** (web, sim, real and human data; latent actions; DexMimicGen; DreamGen); catastrophic forgetting and **knowledge insulation**; **π0.5**. | [lecture-07a](https://hands-on-robot-learning.github.io/slides/lecture-07a-multitask-policy-learning-and-vlas.pdf) | [LBM](https://toyotaresearchinstitute.github.io/lbm1/); [RT-2](https://robotics-transformer2.github.io/); [FAST](https://www.pi.website/research/fast); [VQ-BeT](https://sjlee.cc/vq-bet/); [OAT](https://ordered-action-tokenization.github.io/); [Octo](https://octo-models.github.io/); [π0](https://www.pi.website/blog/pi0); GR00T [N1](https://research.nvidia.com/labs/lpr/publication/gr00tn1_2025/), [N1.5](https://research.nvidia.com/labs/gear/gr00t-n15/); [Knowledge insulation](https://www.pi.website/research/knowledge_insulation); [π0.5](https://www.pi.website/blog/pi05) |
| — | Oct 22 | Fall break | | | |
| L7b | Oct 29 | **VLA models, continued** | Retaining pretrained knowledge; KI and π0.5; GR00T N1.5, **FLARE** (implicit world modeling), learning from human video; **DreamGen** synthetic trajectories; **VLA post-training with π0.6\* and RECAP** (value function → advantage → advantage-conditioned policy, i.e. offline-RL-flavoured); memory and partial observability (MEM); **π0.7** and subgoal conditioning; few-shot and in-context adaptation (**ICRT**, behavior prompting, Generalist, Skild); Helix 2.5. | [lecture-07b](https://hands-on-robot-learning.github.io/slides/lecture-07b-multitask-policy-learning-and-vlas-continued.pdf) | Papers cited on slides: Driess et al. (KI, NeurIPS 2025); Black et al. π0.5 (CoRL 2025); Amin et al. π0.6\* (RSS 2026); Ai et al. π0.7 (2026); Zheng et al. FLARE (CoRL 2025); Jang et al. DreamGen (CoRL 2025); Fu et al. ICRT (ICRA 2025); Torne et al. MEM (2026) |
| L8 | Nov 5 | **Agentic methods in robot learning** + **A1 demos** | **Code as Policies** and **CaP-X** (benchmarking coding agents for manipulation); LLMs/VLMs as direct action predictors; progress monitoring and recovery (Spotter); in-context learning; skill discovery (ASPIRE); policy self-improvement (ENPIRE); graph-as-policy harnesses (GaP); sim environments from video (Real2Gym, Video2World, SimEX); hardware interfaces and safety. | [lecture-08](https://hands-on-robot-learning.github.io/slides/lecture-08-agentic-methods-in-robot-learning.pdf) | Liang et al. [Code as Policies](https://arxiv.org/abs/2209.07753); Fu et al. CaP-X (ICML 2026); others are 2026 preprints cited on slides |
| — | Nov 12 | **Hands-on policy learning with LeRobot** (staff tutorial) | Dataset prep, training, checkpointing, deployment, debugging, closed-loop eval. A2 prep and classroom data collection. | none | LeRobot docs |
| — | Nov 19 | **A2 demos** (block stacking) | | | |
| — | Nov 26 | Fall recess | | | |
| L? | Dec 3 | **World models and policy learning** | Learned dynamics / world models; action-centric vs goal-centric; planning, prediction, action extraction; reward and value models; evaluation. (The syllabus originally planned "Evaluating generalist robot models" here.) | not yet posted (as of 2026-11-05 commit) | — |
| — | Dec 10 | **A3 final demos** (faculty panel) | Multi-task demos and short presentations | | |

**Lecture numbering note:** slides are numbered 02, 03, 04, 06, 07a, 07b, 08. Lecture 05 ("policy inference stack" in the syllabus) appears to have been **merged into Lecture 06** (its inference-stack section). That is my inference from the slide content.

---

## 3. Assignments, platforms, code

### Hardware and software stack
- **Robot:** **SO-101** (open-source, low-cost 5-DOF arm + gripper; HF/TheRobotStudio design). Teams get a kit with a **leader arm** (teleop) and a **follower arm**, plus a **wrist RGB camera**. Servos: **FeeTech STS3215** smart servos, 12-bit magnetic encoders, position control only (no current/torque control, so no true gravity compensation on the leader).
- **Software:** **Hugging Face LeRobot**, with datasets in **LeRobotDataset v3** format (strongly recommended for every submission). Policies recommended: **ACT** first, then Diffusion Policy. A3 allows VLAs (π0/π0.5/SmolVLA/GR00T via LeRobot), world models, or coding agents.
- **Control:** 30 Hz. ACT setting used in class: prediction horizon **Hₚ = 30** (1 s), execution horizon **Hₑ = 10** (suggested; you pick). LeRobot DP default for SO-101 (L6 slide): **U-Net + DDPM, 100k steps, batch size 8, chunk 16 (~0.53 s), execute 8**. Action space is absolute joint positions; gripper normalized to [0, 100].
  - **Version caveat:** on current LeRobot `main` (v0.7.1), `DiffusionConfig` defaults to `horizon=64, n_action_steps=32` and `ACTConfig` defaults to `chunk_size=100, n_action_steps=100, use_vae=True`. The 16/8 numbers on the slide must come from an older release or the course's own config. **Always set these explicitly and write them down**, which A2 requires anyway.
- **Evaluation statistics:** **STEP** (Sequential Testing for Efficient Policy comparison) from TRI, https://github.com/TRI-ML/sequentialized_barnard_tests, with Beta-posterior violin plots, 90% Wilson CIs, and compact-letter-display labels.
- **Simulation:** not a core platform. Lecture 6 uses browser demos. L8 covers sim environments for training and eval. No MuJoCo/Isaac/PyBullet/robosuite assignment.
- **Compute:** your own (Colab/Kaggle/HF). A1 must run **CPU-only** on a laptop.

### A0 · Build and calibrate (5%)
Assemble the SO-101, configure motor IDs, calibrate, and document a working setup. Done in class Oct 1; due ~Oct 9–12.

### A1 · Modular sense–plan–act pick-and-place (10%), demo Nov 5 (moved from Oct 29)
- Task: pick a **red cube** and place it in a **blue tray** with the **follower arm only**.
- **No learned policy for action generation.** You need explicit *sense* (RGB only: color blobs, ArUco/AprilTag allowed, learning allowed *only* in perception), *plan*, and *act* modules. **No depth** of any kind (no depth camera, stereo or learned depth).
- Wrist camera recommended. An external RGB camera is allowed, but the system must still work if staff cover it.
- **CPU only**, no GPU and no remote inference.
- Must print or show "cube picked" and "task complete" states.
- Deliverables: 1-page report, ≥10 test videos including ≥2 failures, a results table, and a contributions + AI-use statement.
- Awards: "least strings attached" and "best-performing".
- What you'll actually use: camera intrinsics/extrinsics, pixel→table-plane back-projection with a known table height, IK for a 5-DOF arm, joint-space interpolation, gripper open/close.

### A2 · Single-task imitation learning (30%), TA session Nov 12, demo Nov 19
- **Task 1 (take-home):** the same cube→tray task via **behavior cloning**. You must **compare ≥2 policies or configs** under one experimental question: ACT vs DP, chunk size, execution horizon, or amount of data.
  - ≥25 matched rollouts per variant, with the same initial configurations for both and every attempt logged.
  - Partial-credit rubric (reach / grasp / transport / place, 0.25 each) is reported separately from binary success.
  - **STEP analysis:** `MirroredStepTest(inference_mode="ranking", alternative=Hypothesis.P0LessThanP1, alpha=alpha_pair, n_max=…)`, with α_pair = 0.10 / (k(k−1)/2). Fix `n_max` *before* looking at results and don't extend it after an inconclusive result.
  - Report: success counts with 90% Wilson CI, median/IQR completion time, failure locations, and a **Beta(s+1, n−s+1) posterior violin plot** with CLD letters.
- **Task 2 (in-class):** **block stacking**, ≥3-block freestanding tower. You train at home and demo in the classroom, which tests **distribution shift** (lighting, background, camera pose). You may build fixtures; fewer changes earn bonus awards. Encouraged: varied lighting/backgrounds and image augmentation.
- No pretrained multi-task models allowed: the policy must be **single-task, trained from scratch** on your demos.
- Submit: demos, checkpoints, configs, rollout logs and videos, analysis code, and 1-page write-ups.

### A3 · Multi-task robot manipulation "solution" (35%), final demo Dec 10
- Build a **unified system** that does multiple tasks. It doesn't have to be one model. Eligible approaches: task-conditioned policies (LBM-style), **fine-tuned or steered VLAs**, **world models / world-action models**, **coding agents as policies**, agentic harnesses.
- **Not eligible:** a switch statement over single-task checkpoints. Staff quiz you on what is *learned vs adapted vs shared* across tasks.
- You must define: task definitions and success criteria, the **task-specification interface** (language? goal image?), training/fine-tuning data, how you handle distribution shift, the compute used, and contributions.
- Pre-register an evaluation protocol (LBM-style: subtask predicates, recoveries, time-to-completion).
- **Faculty panel** of WSE robotics faculty judges versatility, originality, performance, system design and generalization. Hardware mods (e.g., a fin-ray gripper) need staff approval.

### Code repos
- **No public course starter code.** The only repo is the website.
- What you'll actually use:
  - LeRobot: https://github.com/huggingface/lerobot (v0.7.1 at the time of writing; CLI `lerobot-find-port`, `lerobot-setup-motors`, `lerobot-calibrate`, `lerobot-teleoperate`, `lerobot-record`, `lerobot-train`, `lerobot-eval`, `lerobot-rollout`; note that `lerobot-rollout` does not exist in ≤ v0.5.x, where you use `lerobot-record` with a policy instead)
  - STEP: https://github.com/TRI-ML/sequentialized_barnard_tests
  - TRI tutorial on statistical policy eval: https://medium.com/toyotaresearch/statistical-thinking-for-robot-policy-evaluation-from-rigorous-a-b-testing-to-effective-0ae886fbd68d

---

## 4. Core concept checklist with key equations

Marked **[C]** = covered in course slides, **[B]** = background you're expected to have, **[O]** = optional / not covered by this course (RL fundamentals).

### 4.1 Robotics essentials [C, L2]
- [ ] FK: $x = f(q)$. Jacobian: $J(q) = \partial f/\partial q$, with $\dot x = J(q)\dot q$. For SO-101, $J \in \mathbb{R}^{6\times5}$, so the arm can't realize arbitrary 6D twists.
- [ ] Numerical IK (damped least squares): $\Delta q = J^\top (J J^\top + \lambda^2 I)^{-1} (x^\* - f(q))$. Singularities show up as rank drops of $J$.
- [ ] PID: $u(t) = K_p e + K_i \int e\,dt + K_d \dot e$. Tune for overshoot, settling time and steady-state error. The servo runs its own inner PID loop.
- [ ] Static torque / payload: $\tau = m g \ell \cos\theta$, so longer lever arms mean lower payload at stall.
- [ ] Joint vs end-effector control; absolute vs delta actions; why **compliant, over-damped controllers help BC** (Tune-to-Learn).

### 4.2 Imitation learning [C, L4]
- [ ] **MDP:** $(\mathcal S, \mathcal A, P(s_{t+1}\mid s_t,a_t), R, \rho_0, T)$. Markov property: $P(s_{t+1}\mid s_{0:t},a_{0:t}) = P(s_{t+1}\mid s_t,a_t)$. Observations $o_t$ are not states; real robots are POMDPs.
- [ ] **BC loss** (continuous): $\mathcal L(\theta) = \frac{1}{N}\sum_i \lVert \pi_\theta(o_i) - a_i \rVert_2^2$. Alternatively maximize likelihood: $\max_\theta \sum_i \log \pi_\theta(a_i\mid o_i)$.
- [ ] **Covariate shift / compounding error:** with per-step error ≤ ε under the expert distribution, worst-case cost is $\epsilon \sum_{t=0}^{T-1} t = \epsilon\,T(T-1)/2 = O(\epsilon T^2)$. DAgger brings this to $O(\epsilon T)$ (Ross & Bagnell; Ross et al. 2011).
- [ ] **DART:** inject noise into the *executed* action during demos and record the expert's *commanded* correction.
- [ ] **DAgger:** (1) train $\pi_1$ on $\mathcal D$; (2) roll out $\pi_i$ and have the expert label the visited states; (3) $\mathcal D \leftarrow \mathcal D \cup \mathcal D_i$; (4) retrain and repeat. Costs: the expert has to be in the loop and has to label unsafe states.
- [ ] **Causal confusion:** more input information can hurt (the brake-light example).
- [ ] **Commanded vs realized actions:** train on commanded leader targets.
- [ ] **Evaluation ladder:** val loss → replay episodes → sim → real robot.

### 4.3 Action chunking and ACT [C, L4]
- [ ] Chunk $A_t = [a_t, \dots, a_{t+H_p-1}] \in \mathbb R^{H_p\times d_a}$. Predict $H_p$ steps and execute $H_e \le H_p$ before re-querying.
- [ ] **ACT** = ResNet image tokens + proprioception (+ optional CVAE latent $z$) → transformer encoder → **decoder with $H_p$ learned queries** → whole chunk in parallel.
  - Loss: $\mathcal L = \lVert \hat A_t - A_t\rVert_1 + \beta\, D_{KL}\big(q_\phi(z\mid A_t, q_t)\,\Vert\,\mathcal N(0,I)\big)$, masking padded steps at episode end.
  - Inference: $z=0$, so the policy is deterministic. The instructor recommends **dropping the CVAE** for A2.
  - **Temporal ensembling:** query every step and average overlapping predictions for the current time with weights $w_i = \exp(-m\,i)$, where $i=0$ is the oldest prediction.
- [ ] Gotchas: strip leading zero-action frames, vary initial configurations, **normalize the gripper carefully**, replay a training episode through the inference controller, overfit a small clean subset first, and never move the camera mid-project.
- [ ] Why chunking works (open question): more stable closed-loop dynamics, less error propagation, non-Markovian expressivity.

### 4.4 Generative policies [C, L6]
- [ ] **Multimodality:** the L2 regression target is the conditional mean, which is wrong when there are two separated modes.
- [ ] **DDPM forward:** $x_k = \sqrt{\bar\alpha_k}\,x_0 + \sqrt{1-\bar\alpha_k}\,\epsilon$, with $\bar\alpha_k=\prod_{i\le k}\alpha_i$ and $\epsilon\sim\mathcal N(0,I)$.
- [ ] **Training:** $\mathcal L = \mathbb E_{x_0,k,\epsilon}\lVert \epsilon - \epsilon_\theta(x_k, k)\rVert^2$, with $k\sim U\{1..K\}$.
- [ ] **Reverse (DDPM):** $x_{k-1} = \frac{1}{\sqrt{\alpha_k}}\big(x_k - \frac{1-\alpha_k}{\sqrt{1-\bar\alpha_k}}\epsilon_\theta(x_k,k)\big) + \sigma_k z$. DDIM is deterministic and needs fewer steps.
- [ ] **One-step denoised estimate:** $\hat x_0 = (x_k - \sqrt{1-\bar\alpha_k}\,\epsilon_\theta)/\sqrt{\bar\alpha_k}$. Guidance uses it.
- [ ] **Diffusion Policy:** $A_t^k = \sqrt{\bar\alpha_k}A_t + \sqrt{1-\bar\alpha_k}\epsilon$ and $\mathcal L = \mathbb E\lVert \epsilon - \epsilon_\theta(A_t^k, O_t, k)\rVert^2$. The observation embedding conditions a 1D temporal U-Net via FiLM, or a transformer.
- [ ] **Flow matching** (π0 action expert; L6 demo 08): $x_\tau = \tau x_1 + (1-\tau)x_0$ with $x_0\sim\mathcal N(0,I)$ and $x_1$ = data. Loss: $\lVert v_\theta(x_\tau,\tau,o) - (x_1 - x_0)\rVert^2$. Sample by integrating $\dot x = v_\theta$ with ~10 Euler steps. Sign and time conventions differ between papers; π0 uses the reverse convention.
- [ ] **Sinusoidal timestep embedding:** $\text{emb}(k)_{2i}=\sin(k\cdot 10000^{-2i/d})$ and $\text{emb}(k)_{2i+1}=\cos(\cdot)$.
- [ ] **Guidance / steering:** $p^\*(a\mid o)\propto p_\theta(a\mid o)\,e^{-\lambda C(a,o)}$, so $\nabla_a\log p^\* = \nabla_a\log p_\theta - \lambda\nabla_a C$. In practice, $\epsilon \leftarrow \epsilon + \lambda_t\sqrt{1-\bar\alpha_t}\,\nabla C(\hat a_0)$.
- [ ] Policy as an API, $a = f_\theta(O, z)$: change θ = fine-tune; change z = steer (golden ticket, DSRL); combine several $f$ = composition.
- [ ] **Inference stack:** policy latency of O(10–100 ms) vs controller rates of 30–1000 Hz; naive async switching causes jerks; **RTC** inpaints the next chunk so it stays consistent with actions already committed. Smoothness and reactivity are different metrics.

### 4.5 Transformers, VLAs, tokenization [B + C, L2/L7]
- [ ] **Attention:** $\text{Attn}(Q,K,V) = \text{softmax}(QK^\top/\sqrt{d_k})V$. Know the pre-LN block (LN → MHA → residual → LN → MLP → residual), ViT patch embeddings, and causal masking for autoregressive decoding.
- [ ] **Uniform binning** (RT-1/RT-2/OpenVLA): clip each dim to $[l,u]$, then $\text{bin} = \lfloor (a-l)/(u-l)\cdot(B-1)\rceil$ with $B=256$, mapped onto reserved or rarely used LM vocabulary tokens. Cost is $d_a \times H$ tokens per chunk, and highly correlated tokens give poor learning signal at high control rates.
- [ ] **FAST:** normalize → per-dimension **DCT-II** over time → scale and round coefficients → flatten (low frequencies first) → **BPE**. This gives variable-length, compressed tokens, and invalid sequences have to be resampled.
- [ ] **VQ-BeT:** residual VQ-VAE over action chunks; the token is a code tuple $(k_1,k_2)$ and the latent is $e_1+e_2$, plus an offset head.
- [ ] **OAT:** learned queries cross-attend to the chunk, then FSQ quantization and nested (prefix) dropout give an *ordered* coarse-to-fine token prefix.
- [ ] **Continuous action experts:** Octo (diffusion head), **π0** (≈3B PaliGemma VLM + ≈300M flow-matching action expert, cross-embodiment by zero-padded action space), GR00T N1 (System 2 VLM + System 1 diffusion transformer). **Knowledge insulation:** stop gradients from the action expert into the VLM and co-train with FAST tokens and web data, which avoids catastrophic forgetting.
- [ ] **RECAP (π0.6\*)** is the RL-ish one: learn a value $V(o)$ from success and step-cost labels, set advantage $A = r + V(o') - V(o)$ (or an n-step version), binarize it into a conditioning token, and at test time condition on "high advantage". Related to AWR: $\max_\theta \mathbb E[\log\pi_\theta(a\mid o)\,e^{A/\beta}]$.

### 4.6 RL fundamentals [O: not in this course; useful elsewhere and for DSRL, RECAP and world models]
- [ ] Return $G_t=\sum_{k\ge0}\gamma^k r_{t+k}$. $V^\pi(s)=\mathbb E_\pi[G_t\mid s_t=s]$, $Q^\pi(s,a)=\mathbb E_\pi[G_t\mid s_t=s,a_t=a]$.
- [ ] **Bellman expectation:** $V^\pi(s)=\sum_a\pi(a\mid s)\sum_{s'}P(s'\mid s,a)[r+\gamma V^\pi(s')]$.
- [ ] **Bellman optimality:** $Q^\*(s,a)=\mathbb E_{s'}[r+\gamma\max_{a'}Q^\*(s',a')]$.
- [ ] **Value iteration:** $V_{k+1}(s)=\max_a\sum_{s'}P(s'\mid s,a)[r+\gamma V_k(s')]$. **Policy iteration:** evaluate, then improve greedily.
- [ ] **Q-learning:** $Q(s,a)\leftarrow Q(s,a)+\alpha[r+\gamma\max_{a'}Q(s',a')-Q(s,a)]$. **DQN** adds replay and a target net: $\mathcal L=(r+\gamma\max_{a'}Q_{\bar\theta}(s',a')-Q_\theta(s,a))^2$.
- [ ] **Policy gradient theorem:** $\nabla_\theta J=\mathbb E_{\pi_\theta}[\nabla_\theta\log\pi_\theta(a_t\mid s_t)\,Q^{\pi}(s_t,a_t)]$. **REINFORCE** uses $G_t$ in place of $Q$, minus a baseline $b(s_t)$.
- [ ] **Advantage** $A=Q-V$. TD error $\delta_t=r_t+\gamma V(s_{t+1})-V(s_t)$. **GAE:** $\hat A_t=\sum_{l\ge0}(\gamma\lambda)^l\delta_{t+l}$.
- [ ] **PPO-clip:** $L^{CLIP}=\mathbb E_t[\min(r_t(\theta)\hat A_t,\ \text{clip}(r_t(\theta),1-\epsilon,1+\epsilon)\hat A_t)]$ with $r_t=\pi_\theta(a_t\mid s_t)/\pi_{\theta_{old}}(a_t\mid s_t)$ and $\epsilon\approx0.2$, plus a value loss and an entropy bonus.
- [ ] **SAC:** $J(\pi)=\sum_t\mathbb E[r(s_t,a_t)+\alpha\mathcal H(\pi(\cdot\mid s_t))]$. Soft Q target: $y=r+\gamma(\min_{j}Q_{\bar\phi_j}(s',a')-\alpha\log\pi(a'\mid s'))$ with $a'\sim\pi(\cdot\mid s')$. Actor: $\min\ \mathbb E[\alpha\log\pi(a\mid s)-Q(s,a)]$ using the reparameterization trick.
- [ ] Model-based RL / world models (Dec 3 lecture): learn $\hat P(s'\mid s,a)$ or a latent dynamics model, then plan (MPC / CEM) or train the policy in imagination (Dreamer, TD-MPC). LeRobot ships a TD-MPC policy and HIL-SERL.
- [ ] Offline RL (IQL/CQL) and sim-to-real / domain randomization: **not covered in this course**. Get them from CS285 or CS224R if needed.

### 4.7 Evaluation statistics [C, A2]
- [ ] Wilson score interval for success rate $\hat p$ with $n$ trials and $z=1.645$ (90%): $\frac{\hat p+\frac{z^2}{2n}\pm z\sqrt{\frac{\hat p(1-\hat p)}{n}+\frac{z^2}{4n^2}}}{1+z^2/n}$.
- [ ] Posterior $\text{Beta}(s+1,n-s+1)$ under a uniform prior, used for the violin plots.
- [ ] Bonferroni-style split $\alpha_{pair}=\alpha/\binom{k}{2}$. Sequential tests (STEP) let you stop early without p-hacking, *provided* $n_{max}$ is fixed in advance.
- [ ] Compact letter display (CLD): policies sharing a letter are not statistically separated.

---

## 5. Must-read papers (prioritized for this course)

**Tier 1: read first (the A2 core)**
1. Zhao, Kumar, Levine, Finn. **ACT / ALOHA**: *Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware*, RSS 2023. https://arxiv.org/abs/2304.13705 · https://tonyzhaozh.github.io/aloha/
2. Chi et al. **Diffusion Policy**, RSS 2023. https://arxiv.org/abs/2303.04137 · https://diffusion-policy.cs.columbia.edu/
3. Ho, Jain, Abbeel. **DDPM**, NeurIPS 2020. https://arxiv.org/abs/2006.11239
4. Ross, Gordon, Bagnell. **DAgger**, AISTATS 2011. https://arxiv.org/abs/1011.0686
5. Barreiros et al. (TRI). **A Careful Examination of Large Behavior Models** (eval protocol §4.1 is used in A2/A3). https://arxiv.org/abs/2507.05331 · https://toyotaresearchinstitute.github.io/lbm1/

**Tier 2: VLAs (A3 core)**
6. Black et al. **π0**, RSS 2025. https://arxiv.org/abs/2410.24164 · https://www.pi.website/blog/pi0
7. Pertsch et al. **FAST** action tokenization, RSS 2025. https://arxiv.org/abs/2501.09747
8. Zitkovich/Brohan et al. **RT-2**, CoRL 2023. https://arxiv.org/abs/2307.15818
9. Ghosh et al. **Octo**, RSS 2024. https://arxiv.org/abs/2405.12213
10. Physical Intelligence. **π0.5**, CoRL 2025. https://arxiv.org/abs/2504.16054
11. Driess et al. **Knowledge Insulating VLAs**, NeurIPS 2025. https://arxiv.org/abs/2505.23705
12. Bjorck et al. **GR00T N1**. https://arxiv.org/abs/2503.14734
13. Shukor et al. **SmolVLA** (small VLA that runs on SO-100/101 via LeRobot; not on the slides but practical for A3). https://arxiv.org/abs/2506.01844
14. Kim et al. **OpenVLA** (not on the slides; the canonical open binned-token VLA). https://arxiv.org/abs/2406.09246

**Tier 3: data, inference, steering**
15. Chi et al. **UMI**, RSS 2024. https://arxiv.org/abs/2402.10329
16. Wu et al. **GELLO**. https://arxiv.org/abs/2309.13037
17. Black et al. **Real-Time Action Chunking**. https://www.pi.website/research/real_time_chunking (arXiv 2506.07339)
18. Laskey et al. **DART**, CoRL 2017. https://arxiv.org/abs/1703.09327
19. de Haan, Jayaraman, Levine. **Causal Confusion in IL**, NeurIPS 2019. https://arxiv.org/abs/1905.11979
20. Wagenmaker et al. **DSRL**, CoRL 2025. https://diffusion-steering.github.io/ (arXiv 2506.15799)
21. Agia et al. **CUPID**, CoRL 2025. https://cupid-curation.github.io/
22. Lee et al. **VQ-BeT**, ICML 2024. https://arxiv.org/abs/2403.03181
23. Ze et al. **3D Diffusion Policy**, RSS 2024. https://arxiv.org/abs/2403.03954
24. Brohan et al. **RT-1**, RSS 2023. https://arxiv.org/abs/2212.06817
25. Open X-Embodiment, ICRA 2024. https://arxiv.org/abs/2310.08864
26. Liang et al. **Code as Policies**, ICRA 2023. https://arxiv.org/abs/2209.07753
27. Lipman et al. **Flow Matching for Generative Modeling** (background for π0). https://arxiv.org/abs/2210.02747
28. Simchowitz, Pfrommer, Jadbabaie. *The Pitfalls of Imitation Learning when Actions are Continuous*, COLT 2025 (link not verified).

**Optional RL (not on the syllabus):** PPO https://arxiv.org/abs/1707.06347 · SAC https://arxiv.org/abs/1801.01290 · GAE https://arxiv.org/abs/1506.02438 · DQN https://arxiv.org/abs/1312.5602

---

## 6. Difficulty, reviews, tips

- **No student reviews exist yet.** Fall 2026 is the first offering. Searches turned up no Reddit or evaluation data, only aggregator posts announcing the course and its slides (digg.com links in Sources, not directly readable from this session).
- **Difficulty (my assessment from syllabus and handouts):** high workload, mainly from hardware and evaluation rather than math.
  - About half the time is out-of-class robot work. The syllabus warns of frequent hardware and software failures "inevitably before/during deadlines/demos".
  - Evaluation is heavy: ≥25 matched rollouts × 2 policies with full logs and unedited videos for A2 Task 1 alone.
  - Assigned teams of 3 share one kit. Grades depend on *explaining every low-level decision*, not just on success rate.
  - The math load is moderate (BC, diffusion, attention). The expected DL fluency is high: you must debug transformer and diffusion training on your own.
- **Tips taken directly from the slides and handouts:**
  1. Use **ACT without the CVAE** for your first policy. Overfit a small clean subset first.
  2. **Remove leading zero-action frames**, or the policy "doesn't want to move".
  3. **Gripper normalization** is often what separates "doesn't work" from "works quite well".
  4. Use **commanded** (leader) joint targets as actions and document action representation, normalization, control frequency, Hₚ and Hₑ.
  5. **Replay a recorded training episode** through your inference controller before training anything.
  6. Don't move the camera or robot between data collection and evaluation. Keep lighting and background consistent, or deliberately vary them and augment if you need robustness (A2 Task 2).
  7. Recalibrate after reinstalling any motor. Never stall the servos: STS3215s overheat and burn out. Clamp the base to the table.
  8. Val loss is a poor checkpoint selector. Use closed-loop rollouts and log smoothness, retries and failure types.
  9. Fill out the **teaming questionnaire by the end of Week 2** (the syllabus explains how teams are formed: breadth across electronics, mechanics, learning and systems, plus compute access).
  10. Budget your own GPU (Colab or Kaggle). ACT trains in hours on one consumer GPU. VLA fine-tuning (SmolVLA ~450M) is feasible on a single GPU. π0-class models need much more memory (see LeRobot's `hardware_guide`).

---

## 7. Best free resources

| Resource | Why | URL |
|---|---|---|
| **LeRobot docs** (SO-101 build, `il_robots`, ACT, Diffusion, SmolVLA, π0/π0.5, dataset v3, cheat-sheet) | *The* toolchain of this course | https://huggingface.co/docs/lerobot · https://huggingface.co/docs/lerobot/so101 · https://huggingface.co/docs/lerobot/lerobot-dataset-v3 · repo https://github.com/huggingface/lerobot (`docs/source/cheat-sheet.mdx`, `examples/tutorial/{act,diffusion,smolvla,pi0,rl}`) |
| **SO-101 hardware** | BOM and assembly | https://github.com/TheRobotStudio/SO-ARM100 |
| **Course week-6 interactive demos** | Best intuition builder for diffusion, flow and RTC | https://hands-on-robot-learning.github.io/demos/week06/ |
| **Diffusion Policy code + Colab notebooks** (state and vision PushT) | Minimal from-scratch DP | https://github.com/real-stanford/diffusion_policy |
| **ACT original code** | Reference ACT implementation | https://github.com/tonyzhaozh/act |
| **Stanford CS224R** (Deep RL, Finn): has strong IL / offline-RL / robot-learning lectures | IL + RL theory | https://cs224r.stanford.edu/ |
| **Berkeley CS285** (Deep RL, Levine): lectures 2 (IL), 5–9 (PG, AC, Q), 11–12 (model-based), 15–16 (offline) | The most thorough RL course | https://rail.eecs.berkeley.edu/deeprlcourse/ |
| **OpenAI Spinning Up** | Clean PG/PPO/SAC derivations + code | https://spinningup.openai.com/ |
| **Sutton & Barto, RL: An Introduction (2nd ed.)**, free PDF | Ch. 3–6 (MDP, DP, MC, TD), Ch. 13 (PG) | http://incompleteideas.net/book/the-book-2nd.html |
| **Lilian Weng: "What are Diffusion Models?"** | Best written diffusion derivation | https://lilianweng.github.io/posts/2021-07-11-diffusion-models/ |
| **MIT 6.S191** (linked from L2) | DL refresher | https://introtodeeplearning.com/ |
| **Spong, Hutchinson, Vidyasagar** (linked from L2) | FK/IK/Jacobians/PID | https://arl.human.cornell.edu/879Readings/Spong_RobotModeling.pdf |
| **Russ Tedrake, *Robotic Manipulation*** (MIT 6.4210) | Free text; has IL / diffusion-policy chapters | https://manipulation.csail.mit.edu/ |
| **Karpathy, "Let's build GPT"** | Transformer-from-scratch fluency (a required prereq) | https://www.youtube.com/watch?v=kCc8FmEb1nY |
| **Stable-Baselines3 + Gymnasium + MuJoCo** | Fast RL baselines (optional for this course) | https://stable-baselines3.readthedocs.io/ · https://gymnasium.farama.org/ |
| **CleanRL** | Single-file PPO/SAC/DQN to read and reimplement | https://github.com/vwxyzjn/cleanrl |
| **TRI statistical-eval blog + STEP** | A2 statistics | links in §3 |

---

## 8. Gaps and prerequisites to fill (ranked for this course)

1. **PyTorch DL workflow, hands on (REQUIRED):** dataloaders, normalization stats, mixed precision, checkpointing, W&B or trackio logging, overfitting a batch. *Gap check:* can you train a small ViT or ResNet and a 1D-U-Net DDPM from scratch and debug a NaN?
2. **Transformers (REQUIRED):** attention, positional encodings, encoder–decoder with learned queries (DETR-style, which ACT copies).
3. **Diffusion and flow matching (REQUIRED):** ε-prediction, noise schedules, DDPM/DDIM sampling, conditioning (FiLM, cross-attention), classifier-free guidance.
4. **Kinematics and control (preferred, assumed):** rigid transforms SE(3), DH or product-of-exponentials FK, numerical IK, Jacobian, PID, joint vs EE control. (JHU MSE Robotics core courses cover this; RDKDC / EN.530.646 is the usual one.)
5. **Camera geometry (needed for A1):** pinhole model, intrinsics calibration (OpenCV checkerboard), extrinsics via ArUco/AprilTag (`cv2.aruco`, `solvePnP`), back-projecting a pixel onto a known plane, HSV color thresholding.
6. **Linux and hardware hygiene:** serial ports (`/dev/ttyACM*`), udev permissions, USB cameras (OpenCV indices), power supplies (SO-101 uses 7.4 V or 12 V supply variants **(unverified)**; check the kit docs).
7. **Experimental statistics:** binomial CIs, Bayesian Beta posterior, multiple comparisons, sequential testing.
8. **RL (optional for this course):** only enough to read RECAP, DSRL and world-model papers. Needed more for other JHU courses such as EN.530.696 *Learning-Based Control for Robotics* (https://airo.lcsr.jhu.edu/lbcr/) and EN.601.497/697 *Human-Centered Robotics* (https://alliance-ai.cs.jhu.edu/teaching/HCR), which per search snippets covers model-free/model-based RL, IL and diffusion policy (not directly verified).

---

## 9. Build-first project sequence (do in order; you write all code)

Assumptions: one CUDA GPU (Colab T4/L4 is enough for Projects 1–3), Python ≥ 3.12 (LeRobot 0.7.x requires it), `pip install 'lerobot[pusht,aloha]'` (extras names per LeRobot `installation.mdx`). **No robot needed.** CLI flags below are from LeRobot `main` (v0.7.1, Nov 2026); check `lerobot-train --help` if they've changed. Each day is ~6–8 focused hours. Days are ordered by course importance, with RL last and optional.

### Project 1: Datasets and behavior cloning from scratch (L3 + L4)
- [ ] Install LeRobot. Load `lerobot/pusht` with `LeRobotDataset`, print `dataset.meta.features`, plot one episode's `observation.state` and `action`, and run `lerobot-dataset-viz` on an SO-101 community dataset from the Hub. **Find out whether its actions are commanded or realized** (L3's PSA).
- [ ] **Write a 150-line BC trainer** (state-only PushT: MLP, MSE, min-max normalization). Roll it out in `gym_pusht` and record success and coverage. You'll see mode-averaging failures, which motivates DP.
- [ ] Add **action chunking** (predict 16, execute 8) to the same MLP and compare. Then try temporal ensembling.
- [ ] Toy DAgger on a 2D point-mass or PushT with a scripted expert: show the compounding error curve vs dataset-aggregation rounds.
- **Milestone:** a plot of success vs Hₑ ∈ {1, 4, 8, 16} for chunked BC.

### Project 2: Diffusion and flow matching from scratch, then Diffusion Policy (L6)
- [ ] Morning: open the course **week-6 demos** (all 14). Implement **DDPM on a 2D mixture-of-Gaussians** (~100 lines: MLP ε-predictor, sinusoidal embedding, linear β schedule, DDPM and DDIM samplers). Then **flow matching** on the same data with Euler sampling. Show that both capture two modes while MSE doesn't.
- [ ] Afternoon: run the real-stanford DP **state-based PushT Colab**, then train with LeRobot:
  `lerobot-train --dataset.repo_id=lerobot/pusht --policy.type=diffusion --env.type=pusht --output_dir=outputs/train/dp_pusht --policy.device=cuda --policy.push_to_hub=false`
  (`--env.type` enables periodic sim eval. Disable or shorten it if it's slow.)
- [ ] `lerobot-eval --policy.path=outputs/train/dp_pusht/checkpoints/last/pretrained_model --env.type=pusht --eval.n_episodes=50`. Compare against the pretrained `lerobot/diffusion_pusht` on the Hub.
- [ ] Implement cost **guidance** on your 2D toy (push samples away from a region) and a "golden ticket" noise search.
- **Milestone:** your DP PushT success/coverage number plus a GIF.

### Project 3: ACT and the real-robot workflow, dry-run (L4 + A2)
- [ ] Read the ACT paper and code. Train **ACT** in LeRobot on `lerobot/aloha_sim_insertion_human` or `aloha_sim_transfer_cube_human` with `--env.type=aloha` (or on PushT), once **with and without the CVAE** (`--policy.use_vae=false`, confirmed in `configuration_act.py`), with chunk sizes {10, 30, 50} (`--policy.chunk_size`, `--policy.n_action_steps`), and with `--policy.temporal_ensemble_coeff=0.01` (which needs `n_action_steps=1`).
- [ ] Practice the **A2 statistics pipeline** on your sim rollouts: install STEP, run `MirroredStepTest` on two policies' 0/1 sequences (n_max=25, α=0.10), compute 90% Wilson CIs, and draw Beta-posterior violins with CLD labels.
- [ ] Read the LeRobot SO-101 docs end to end and write out the exact sequence you'll run on hardware: `lerobot-find-port` → `lerobot-setup-motors` → `lerobot-calibrate` → `lerobot-teleoperate` → `lerobot-record` → `lerobot-train --policy.type=act` → `lerobot-rollout`.
- **Milestone:** a one-page "A2-style" report on sim: question, matched evaluation, STEP decision, violin plot.

### Project 4: A1 perception and kinematics, plus VLA tokenization (L2 + L7)
- [ ] A1 prep without hardware: write an SO-101 FK from the URDF in the SO-ARM100 repo (URDF location there is **unverified**), or use `ikpy` or `placo`, plus a damped-least-squares IK. Check the 6×5 Jacobian rank. Do HSV red/blue blob detection, ArUco pose via `solvePnP`, and pixel→table-plane back-projection on photos from your phone.
- [ ] Implement **FAST** tokenization on PushT/ALOHA action chunks: `scipy.fft.dct` (type II, norm='ortho') per dimension, scale and round, then a BPE tokenizer (HF `tokenizers`). Compare token count against naive 256-bin tokenization and measure reconstruction error. (The official FAST tokenizer is on the Hub as `physical-intelligence/fast`.)
- [ ] Skim π0, π0.5 and knowledge insulation. Write a 1-page "VLA anatomy" note: VLM backbone, action expert, flow matching, tokens, and cross-embodiment padding.
- **Milestone:** working IK + blob-to-3D pipeline on static images, and a FAST compression-ratio table.

### Project 5: VLA fine-tune (A3 preview), then optional RL crash course
- [ ] Fine-tune **SmolVLA** (`--policy.path=lerobot/smolvla_base`, 450M params; you need `--rename_map` to map your camera names to `camera1/2/3`, per `smolvla.mdx`) on a small SO-100/101 community dataset or LIBERO subset, or run inference only if compute is tight. Note GPU memory and throughput, then try language-conditioning on two tasks. (LeRobot docs: `smolvla.mdx`, `pi0.mdx`, `pi05.mdx`, `hardware_guide.mdx`.)
- [ ] **RL, optional for this course:** (a) tabular **Q-learning** on `FrozenLake-v1` / `CliffWalking-v0` in ~40 lines; also value iteration and policy iteration on the known model. (b) **PPO with SB3** on `Pendulum-v1` and `HalfCheetah-v5` (MuJoCo), then read CleanRL's `ppo_continuous_action.py` and reimplement GAE and the clip loss yourself. (c) Optionally SB3 **SAC** on HalfCheetah and compare sample efficiency. (d) Read the RECAP and DSRL slides again with this background.
- **Milestone:** SmolVLA running inference on a Hub dataset; PPO curve on Pendulum; Q-table GIF.

### If you can get an SO-101 kit early (high value)
Building and teleoperating one before the semester is the best possible prep, since half the course is hardware. Kit prices and vendors vary **(unverified)**; see https://github.com/TheRobotStudio/SO-ARM100. Do A0 → A1 → A2 Task 1 at home.

---

## 10. Sources

**Primary (read in full from the course repo, commit `f8ff875`, 2026-11-05):**
- Course website: https://hands-on-robot-learning.github.io/
- Website source repo: https://github.com/hands-on-robot-learning/hands-on-robot-learning.github.io (`src/content/docs/index.mdx`, `assignments/assignment-{1,2,3}.md`, `public/documents/hands-on-robot-learning-fall-2026-syllabus-and-policies.pdf`, `public/slides/*.pdf`, `public/demos/week06/`)
- GitHub org (one repo): https://github.com/hands-on-robot-learning
- Syllabus PDF: https://hands-on-robot-learning.github.io/documents/hands-on-robot-learning-fall-2026-syllabus-and-policies.pdf
- Assignments: https://hands-on-robot-learning.github.io/assignments/assignment-1/ · /assignment-2/ · /assignment-3/
- Slides: lecture-02, 03, 04, 06, 07a, 07b, 08 at https://hands-on-robot-learning.github.io/slides/

**Instructor and JHU pages (via search results; not directly fetchable from this session):**
- https://www.cs.jhu.edu/faculty/krishna-murthy-jatavallabhula
- https://engineering.jhu.edu/faculty/krishna-murthy-jatavallabhula/
- https://krrish94.github.io/
- JHU CS Fall 2026 course list (search snippet confirms the instructor and "groups of ~3 build an SO-101 from a provided kit"): https://www.cs.jhu.edu/fall26/
- Third-party syllabus repost (mislabeled as U. Michigan): https://www.studocu.com/en-us/document/university-of-michigan/introduction-to-robotics-and-mechatronics/en601498-en601698-robot-learning-fall-2026-syllabus-policies/172948913
- Aggregator announcements (unreadable from this session): https://digg.com/ai/peb4c6yn · https://digg.com/ai/eqx28tdb
- Related JHU courses: https://airo.lcsr.jhu.edu/lbcr/ · https://alliance-ai.cs.jhu.edu/teaching/HCR

**Tools:**
- LeRobot (v0.7.1, cloned 2026-11-24; `docs/source/cheat-sheet.mdx`, `AGENT_GUIDE.md`, `pyproject.toml`): https://github.com/huggingface/lerobot · https://huggingface.co/docs/lerobot
- STEP: https://github.com/TRI-ML/sequentialized_barnard_tests
- TRI eval blog: https://medium.com/toyotaresearch/statistical-thinking-for-robot-policy-evaluation-from-rigorous-a-b-testing-to-effective-0ae886fbd68d

**Note on paper links:** arXiv was unreachable from this research session, so I wrote the arXiv IDs above from memory and could not check them, apart from the LBM link (2507.05331), which the A2 handout itself cites. Project-page links are copied from the course site.
