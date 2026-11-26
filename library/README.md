# Library: notes from the student's own textbooks and slides

Every page of every uploaded file was read (2026-11-26). Each note file is chapter by chapter, with:
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

**Modern Robotics (2017 preprint), book pages:**
- p. 221: $x^2+y^2$ → $\sqrt{x^2+y^2}$.
- p. 234: null-space term sign → $-(I-GJ)M^{-1}\nabla h$.
- p. 235: $\dot\theta^{i+1}$ → $\theta^{i+1}$.
- p. 257: four-bar inequality reversed.
- p. 287: eigenvalues → eigenvectors.
- p. 302: equality → inequality constraint.
- Eq. 9.36: $c,g$ → $c_i,g_i$.
- Eq. 10.4 / §10.6.4: damping $u=F-B\dot q$.
- p. 390: Jacobian applied twice.
- p. 408: "negative definite" → Hurwitz.
- Eq. 11.18: $R^T(d)$ → $R^T(t)$.
- Ch. 11 summary: PD+gravity sign.
- B.11: $r_{21}-r_{12}$.
- B.13: $q_2+iq_3$.
- B.17/18: Cayley sign, use $R=(I+[r])(I-[r])^{-1}$.
- B.19/20: invalid as printed.
- C.5: $\phi_{i-1}$ → $\phi_i$.
- C.12: index → $\mathrm{Ad}_{M_1\cdots M_i}$.
- App. D QP: $x=Gb-(I-GA)Q^{-1}c$, $\lambda=-(Bb+BAQ^{-1}c)$.
- Ex. 4.1 table: $v_3$ sign.
- PDF p. 110: $T_{bc}$ wording.

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
