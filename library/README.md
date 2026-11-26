# Library: notes from the student's own textbooks and slides

Every page of every uploaded file was read (2026-11-26). A second pass the same day added the student's local folders (`~/Downloads/Algorithms_For_Sensor_Based_Robotics/`, `~/Downloads/RobotDevicesKinematicsDynamicsAndControl/`): LaValle, four ASBR papers, past-student RDKDC notes, a past team's RDKDC project report, and an MR 2019-edition concordance. Each note file is chapter by chapter, with:
- the big idea of each chapter;
- definitions and equations with page numbers;
- algorithms, worked examples and exercises worth doing;
- pitfalls;
- corrections and a best-reference map by lesson code.

## Index

| File | Source | PDF pages | Page offset in the student's PDF | Main courses |
|---|---|---|---|---|
| `asbr-slides-part1.md` | ASBR decks: lecture01 (intro), lecture02 (kinematics), lecture03 (hand-eye), week03 (C-space), week05 + week06 (sampling planners) | all | — | ASBR |
| `asbr-slides-part2.md` | ASBR decks: week08 (KF/EKF/SLAM), week09 (Bayes/PF), review; CIRL course page; **student's handwritten notes audit** | all | — | ASBR |
| `mls-part1.md` | Murray–Li–Sastry ch. 1–4, ch. 5 §1–2 | 1–240 | PDF = book + 18 | RDKDC, ASBR |
| `mls-part2.md` | MLS ch. 5–9, App. A–B (+ supplement on ch. 4) | 241–474 | PDF = book + 18 | RDKDC |
| `mr-part1.md` | Modern Robotics ch. 1–5 | 1–219 | PDF = book + 18 | RDKDC, ASBR |
| `mr-part2.md` | MR ch. 5–11.2 (IK, closed chains, dynamics, trajectories, planning) | 216–430 | PDF = book + 18 | RDKDC, ASBR |
| `mr-part3.md` | MR ch. 11–13, App. A–D (control, grasping, mobile robots, quaternions, DH, optimization) | 431–642 | PDF = book + 18 | RDKDC, CIS I |
| `choset-part1.md` | Choset ch. 1–6 (bug, C-space, potentials, roadmaps, cells) | 1–205 | drifts +19 → +13 | ASBR |
| `choset-part2.md` | Choset ch. 6–11 (sampling planners, KF, Bayesian methods, dynamics) | 206–410 | drifts +16 → +14 | ASBR |
| `choset-part3.md` | Choset ch. 11–12, App. A–J (+ deep dive on ch. 8–9) | 411–616 | PDF = book + 14 | ASBR |
| `probrob-part1.md` | Thrun *Probabilistic Robotics* **early draft** ch. 1–9 | 1–245 | PDF = book + 12 | ASBR |
| `probrob-part2.md` | Thrun draft ch. 9–16 (+ deep dive on ch. 7–8 localization) | 246–492 | PDF = book + 12 | ASBR |
| `lavalle-part1.md` | LaValle *Planning Algorithms* ch. 1–7 (discrete planning, C-space, sampling, combinatorial, extensions) | 1–192 | **2-up**: PDF = ⌊(book + 17)/2⌋ | ASBR |
| `lavalle-part2.md` | LaValle ch. 8–15 (feedback plans, decision theory, information spaces, sensing uncertainty, differential models, kinodynamic planning, system theory) | 193–470 | 2-up, same rule | ASBR, Robot Learning, RDKDC |
| `asbr-papers.md` | Park & Martin 1994 (AX=XB); Davison MonoSLAM 2007; Lozano-Pérez 1983 *Spatial Planning*; Kavraki et al. 1996 PRM (`prmbasic_01.pdf`, image scan) | all | — | ASBR |
| `mr-2019-edition.md` | Student's local MR = **Dec 2019** preprint: chapter concordance with the 2017 notes, §8.7 rewrite, every MR erratum re-checked | — | PDF = book + 20 | RDKDC, ASBR |
| `rdkdc-handwritten-notes.md` | `robot-devices.pdf`: a past student's handwritten notes of Cowan's **Fall 2017** RDKDC lectures (17 lectures, up to Lyapunov) | 1–105 | — | RDKDC |
| `rdkdc-final-project.md` | `RDKDCFinalProject.pdf`: a past team's final-project **report** (~2019–20), not the handout | 1–11 | — | RDKDC |

**Choset chapter starts (PDF pages):**
- Ch. 1 → 20 · Ch. 2 → 35 · Ch. 3 → 57 · Ch. 4 → 94 · Ch. 5 → 124 · Ch. 6 → 177
- Ch. 7 → 213 · Ch. 8 → 284 · Ch. 9 → 316 · Ch. 10 → 363 · Ch. 11 → 387 · Ch. 12 → 415
- App. E → 503 · App. F → 513 · App. H → 535

**Thrun draft chapter starts (PDF pages):**
- Ch. 2 → 21 · Ch. 3 → 45 · Ch. 4 → 79 · Ch. 5 → 103 · Ch. 6 → 133 (likelihood field §6.4: 151–155)
- Ch. 7 → 169 · Ch. 8 → 199 · Ch. 9 → 233 · Ch. 10 → 257

## Notation across sources

| Thing | ASBR slides | Our lessons | MLS | MR | Choset | Thrun (draft) |
|---|---|---|---|---|---|---|
| Twist order | $[v;\omega]$ | MLS order; RDKDC lab handout uses $[\omega;v]$ | $(v,\omega)$ | $(\omega,v)$ | — | — |
| Transform | ${}^AE_B$ | $g$, $F=[R,p]$ (CIS) | $g_{ab}$ | $T_{ab}$ | — | — |
| KF process / measurement noise | $Q$ / $R$ | $Q$ / $R$ | — | — | $V$ / $W$ | $R_t$ / $Q_t$ |
| Kalman gain | $K$ | $K$ | — | — | **$R$** | $K_t$ |
| DH | — | standard (Spong), UR5 handout | standard, §3.2.3 | **modified (Craig)**, App. C | — | — |
| Quaternion storage | — | — | — | scalar first | — | — |
| Control error | — | $e=\theta-\theta_d$ | $e=\theta-\theta_d$ (summary p. 207 flips) | $\theta_e=\theta_d-\theta$ | — | — |

ROS and SciPy store quaternions scalar last. Choset's roll-pitch-yaw names differ from ROS.

## Errata found in the student's copies (do not copy these into code)

**Modern Robotics (2017 preprint), book pages.** The student's local copy is the **Dec 2019 preprint** (`~/Downloads/RobotDevicesKinematicsDynamicsAndControl/MR-largefont-v2.pdf`); each item is re-checked there in `mr-2019-edition.md`. Items marked *(fixed in 2019)* are correct in that copy; the rest are still wrong in 2019.
- p. 221: $x^2+y^2$ → $\sqrt{x^2+y^2}$.
- p. 234: null-space term sign → $-(I-GJ)M^{-1}\nabla h$.
- p. 235: $\dot\theta^{i+1}$ → $\theta^{i+1}$.
- p. 257: four-bar inequality reversed.
- p. 287: eigenvalues → eigenvectors. *(fixed in 2019)*
- p. 302: equality → inequality constraint. *(fixed in 2019)*
- Eq. 9.36: $c,g$ → $c_i,g_i$.
- Eq. 10.4 / §10.6.4: damping $u=F-B\dot q$. *(fixed in 2019)*
- p. 390: Jacobian applied twice.
- p. 408: "negative definite" → Hurwitz.
- Eq. 11.18: $R^T(d)$ → $R^T(t)$. *(fixed in 2019)*
- Ch. 11 summary: PD+gravity sign.
- B.11: $r_{21}-r_{12}$.
- B.13: $q_2+iq_3$.
- B.17/18: Cayley sign, use $R=(I+[r])(I-[r])^{-1}$.
- B.19/20: invalid as printed.
- C.5: $\phi_{i-1}$ → $\phi_i$. *(fixed in 2019)*
- C.12: index → $\mathrm{Ad}_{M_1\cdots M_i}$. *(fixed in 2019)*
- App. D QP: $x=Gb-(I-GA)Q^{-1}c$, $\lambda=-(Bb+BAQ^{-1}c)$.
- New in the 2019 check: App. D p. 599 least-squares expansion missing parentheses; p. 435 "Proposition 8.1.2" → Proposition 8.1; the "(B.18)" reference before B.21 and $T_{i,i-1}$ in App. C are still wrong.
- Ex. 4.1 table: $v_3$ sign. *(fixed in 2019)*
- PDF p. 110: $T_{bc}$ wording. *(fixed in 2019)*

**Murray–Li–Sastry:**
- Eq. 3.41: $g_{st}^{-1}(0)$.
- Eq. 3.45: indices.
- Eq. 4.25: missing $g$.
- p. 175: $N$ missing $g$.
- p. 195: augmented-PD result is local, not global.
- p. 198: workspace CT needs $\tilde C,\tilde N$.
- p. 207 summary: feedback signs assume $e=\theta_d-\theta$.
- p. 417: twist order.
- p. 422: wrench transform $F_b=\mathrm{Ad}^T_{g_{ab}}F_a$.
- Ex. 6.1: sign.
- ZYX singular at $\pm\pi/2$.

**Choset:**
- Alg. 4 (p. 85): descent sign $q-\alpha\nabla U$.
- p. 102: $d^*_{goal}$ squared, $\zeta_j$.
- p. 56: chart range $(-1,1)$.
- p. 283: innovation $(y-\hat y)$.
- p. 291: atan2 order.
- Eq. 8.34: $G$ → $H$.
- Eq. 8.48: $S^{-1}$.
- Eq. 9.18: pose added twice.
- Eq. 10.3: gravity sign.
- p. 353: PD test only for 2×2.
- p. 361: $R^2$.
- Thm. 7.4.2 proof: $1.5/\beta$.
- p. 214: random axis + angle is not uniform.
- p. 451: $L/\tan\gamma$.
- p. 493: $\operatorname{tr}\notin\{3,-1\}$.
- p. 551: zero covariance does not imply independence.
- App. J: stability needs care with repeated imaginary-axis eigenvalues.

**Thrun early draft:**
- Tables 7.2/7.3 (EKF localization) and the SLAM tables have wrong signs in $G_t$ and $H_t$:
  - $Q_t$ uses $\sigma$ instead of $\sigma^2$;
  - gains are summed instead of applied as sequential updates;
  - Eq. 7.29 says argmax where it should say argmin.
- Table 3.5: $+H\bar\mu$.
- Eq. 3.63: $\bar\Sigma_t$.
- Eq. 4.21: initial log odds.
- Table 5.4: sampler variance.
- Table 8.1: missing factor.
- Table 8.3: reset $w_{avg}$.
- Table 8.4: $z_{short}p_{short}$.
- Table 9.2: $|r-z^k|<\alpha/2$.
- Ch. 10: $F_x$ block sizes.
- Ch. 11–12: information-vector signs.
- Ch. 15: missing $\gamma$.
- Ch. 16: MC-POMDP accumulator.

**ASBR slides:**
- lecture02 p. 19: ${}^At_C$.
- week03 p. 4: 2R FK.
- week06 p. 42: toward $q_{rand}$.
- week06 p. 49: missing $n$.
- week06 p. 54: PRS = PRM.
- week08 p. 24: $(I-KH)$.
- week08 p. 33: $WQW^T$.
- review p. 9: atan2.
- review p. 17: missing $P(x_k)$.

**LaValle *Planning Algorithms* (2006), Ch. 1–7** (details in `lavalle-part1.md`):
- Eq. 4.38 (p. 159): the convolution as written computes $O\oplus A$; it needs $g(\tau-x)$.
- Eqs. 4.70–4.73 (p. 175): the three-link polynomials are wrong (they give reach 4 at $\theta=0$).
- Fig. 4.25 caption: the point is at $f_1=3$, not 0.
- p. 153: the list of failure cases for matrix → quaternion conversion is wrong.
- p. 199: $n=4$ → $n=3$.
- Fig. 5.25 BUILD_ROADMAP (p. 238): wrong loop index (infinite loop; connects the next sample).
- Eq. 6.7 conchoid: $x^2-y^2$ → $x^2+y^2$.
- Example 6.3: $z>1$ → $z\ge1$. Example 6.5: $\ge$ → $\le$.
- §7.4.1 (p. 342): Jacobian rank should be $n-m$.
- DH text (pp. 105, 107): $a_i$ → $a_{i-1}$; a prismatic joint varies $d_i$.

**LaValle, Ch. 8–15** (about 40 items in `lavalle-part2.md`; main ones):
- Eq. 11.68 should use $\Sigma^{-1}$; eqs. 11.79/11.83: the second $H$ needs a transpose.
- Eq. 10.104: $Q^*$ → $Q_\pi$. Eq. 8.58: the $\alpha$ term.
- Eq. 13.198: sign error in Hamilton's equation. Eqs. 13.103/13.105: quaternion kinematics are missing the ½ factor and the scalar-part terms. Example 13.13 (two-link arm) is largely wrong.
- Eq. 14.45: the gravity term should not carry $\dot s$. Eq. 15.125: CBHD sign. Eq. 15.161: chained-form sinusoids.
- Exercise 11.5: matrices are row-stochastic, against the stated convention. D\* step 6 (Fig. 12.18) probably tests the wrong state (unconfirmed).

**Papers** (details in `asbr-papers.md`):
- Park & Martin 1994 p. 720: $B_2(1,3)$ printed 0.997612, should be 0.977612; the η metric on p. 721 is mis-typed.
- MonoSLAM 2007: eq. 3 $\alpha^W$ → $\alpha^R$; text $q^{RW}$ vs eq. 2 $q^{WR}$; z sign flip in the ground-truth table (p. 1064).
- Lozano-Pérez 1983 App. I: `angle[0]` should be `angle[size]−2π`, and the loop ends early and drops vertices.
- Kavraki et al. 1996: text says C3 failed where the Fig. 3 table shows C2; "III-B-1" → "III-A-1".
