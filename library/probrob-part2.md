# Probabilistic Robotics (Thrun, Burgard, Fox; EARLY DRAFT 1999–2000), Part 2: localization, mapping, SLAM, MDP/POMDP
Source: acb9a9bc-ProbabilisticRobotics.pdf. Assigned range: PDF pages 246–492 (book pp. 234–480). **Page offset: book page = PDF page − 12.**

> **Read this first: the edition and the chapter map.** This is the 1999–2000 *early draft* ("EARLY DRAFT, NOT FOR DISTRIBUTION"), not the 2005 MIT Press book. The chapter numbers and contents differ from the published edition:
>
> | Draft ch. | Title | Book pp. | PDF pp. | Published 2005 equivalent |
> |---|---|---|---|---|
> | 7 | Mobile Robot Localization (Markov, EKF, ML data association, MHT) | 157–186 | 169–198 | ch. 7 |
> | 8 | Grid and Monte Carlo Localization (grid, MCL, random-particle/augmented MCL, mixture MCL, dynamic envs) | 187–219 | 199–231 | ch. 8 (no **KLD sampling** in the draft) |
> | 9 | Occupancy Grid Mapping | 221–243 | 233–255 | ch. 9 |
> | 10 | SLAM: EKF SLAM | 245–266 | 257–278 | ch. 10 |
> | 11 | The Extended Information Form algorithm (= **GraphSLAM** in 2005) | 267–302 | 279–314 | ch. 11 |
> | 12 | Sparse Extended Information Filter (SEIF) | 303–351 | 315–363 | ch. 12 |
> | 13 | Mapping with Unknown Data Association (EM mapping) | 353–391 | 365–403 | dropped from 2005 edition |
> | 14 | Fast Incremental Mapping Algorithms (scan matching, cycle closing, 3D) | 393–419 | 405–431 | partly 2005 ch. 13 context |
> | 15 | Markov Decision Processes (value iteration) | 421–436 | 433–448 | ch. 14 |
> | 16 | POMDPs (finite POMDPs, MC-POMDP, augmented MDPs) | 437–476 | 449–488 | ch. 15–16 |
> | — | References | 477–480 | 489–492 | — |
>
> **The draft has no FastSLAM chapter and no KLD sampling.** The localization chapters (7–8) lie *before* my assigned range (PDF 169–231). Because the ASBR A5/A6 check depends on them, I read them in full too (plus the likelihood-field model, §6.4, PDF 151–155) and cover them in depth below. Several algorithm tables in this draft contain **sign/index typos** (listed in each section and in the corrections list). Use the corrected forms given here, which match the 2005 edition.

---

## Ch 7 Mobile Robot Localization (PDF pp. 169–198, book pp. 157–186), Lessons: A5, A6, F8
*(Outside the assigned range, read for the A5/A6 check.)*

**Big idea:** Localization means estimating a robot's pose relative to a known map. Think of it as finding the coordinate transform between the map frame and the robot frame. One scan rarely pins the pose down (corridors look alike), so the robot must integrate evidence over time with a Bayes filter. "Markov localization" is just the Bayes filter with a map argument. The EKF version tracks one Gaussian: very efficient, but only for *position tracking* with known or easily matched landmarks.

**Key definitions (taxonomy, §7.2, pp. 158–162):**
- *Position tracking* (initial pose known, error stays local, so a unimodal belief is fine) < *global localization* (initial pose unknown) < *kidnapped robot* (the robot is teleported while it believes it knows where it is; this tests recovery from failure).
- Static vs dynamic environments; passive vs active localization; single vs multi-robot.

**Key results & equations:**
- **Table 7.1 Markov localization** (p. 163). For all $x_t$: $\overline{bel}(x_t)=\int p(x_t\mid u_t,x_{t-1},m)\,bel(x_{t-1})\,dx_{t-1}$, then $bel(x_t)=\eta\,p(z_t\mid x_t,m)\,\overline{bel}(x_t)$.
- Initial belief (Eqs 7.1–7.3): use a point mass or a narrow Gaussian $\mathcal N(x_0;\bar x_0,\Sigma)$ for tracking, and a uniform $1/|X|$ for global localization.
- **Velocity motion model** (Eq 7.4 / 5.13), with $u_t=(v_t,\omega_t)^T$:
  $$\begin{pmatrix}x'\\y'\\\theta'\end{pmatrix}=\begin{pmatrix}x\\y\\\theta\end{pmatrix}+\begin{pmatrix}-\frac{v}{\omega}\sin\theta+\frac{v}{\omega}\sin(\theta+\omega\Delta t)\\ \frac{v}{\omega}\cos\theta-\frac{v}{\omega}\cos(\theta+\omega\Delta t)\\ \omega\Delta t+\gamma\Delta t\end{pmatrix}$$
  ($\gamma$ is an extra final-rotation noise term.)
- **Range–bearing–signature landmark model** (Eqs 7.9–7.12): $z^i_t=(r,\phi,s)^T=h(x_t,j,m)+\mathcal N(0,Q_t)$ with $h=\big(\sqrt{(m_{j,x}-x)^2+(m_{j,y}-y)^2},\ \operatorname{atan2}(m_{j,y}-y,m_{j,x}-x)-\theta,\ m_{j,s}\big)^T$.
- **Multi-feature independence** (Eq 7.16): $p(z_t\mid x_t,m)=\prod_i p(z^i_t\mid x_t,m)$. This independence is what lets the algorithm handle the features one at a time.
- **ML data association** (Eqs 7.17–7.29): $\hat c^i_t=\arg\max_c \int p(z^i_t\mid c,x_t,m)\,\overline{bel}(x_t)\,dx_t$. Under linearization the integral is $\mathcal N\big(h(\bar\mu_t,c,m),\ H\bar\Sigma H^T+Q_t\big)$ (Eq 7.27), so you pick the landmark with the **smallest Mahalanobis distance** $(z-\hat z^k)^T\Psi_k^{-1}(z-\hat z^k)$, with $\Psi_k=H^k\bar\Sigma_t H^{kT}+Q_t$ (the innovation covariance). *(Eq 7.29 in the draft says "argmax" of the quadratic; it should be **argmin**, as in Table 7.3 line 14.)*
- **MHT** (Eqs 7.30–7.33): the belief is a Gaussian mixture $\frac{1}{\sum\psi}\sum_l\psi_{t,l}\mathcal N(\mu_{t,l},\Sigma_{t,l})$, with one track per data-association history. Each new branch gets weight $\psi_{t,m}=\psi_{t,l}\,p(z_t\mid c_{1:t-1,l},c_{t,m},\dots)$. Tracks whose relative weight falls below $\psi_{min}$ are pruned, so there are at most $\psi_{min}^{-1}$ tracks.

**Algorithms: Table 7.2 EKF localization, known correspondences (p. 169), shown CORRECTED.** The draft's $G_t$ and $H$ have sign errors, which are fixed here to match the 2005 edition and the derivative of Eq 7.4.
```
EKF_localization_known_correspondences(μ_{t-1}, Σ_{t-1}, u_t=(v,ω), z_t, c_t, m):
  θ = μ_{t-1,θ}
  μ̄ = μ_{t-1} + [ -v/ω sinθ + v/ω sin(θ+ωΔt) ;  v/ω cosθ - v/ω cos(θ+ωΔt) ;  ωΔt ]
  G = [ 1 0  -v/ω cosθ + v/ω cos(θ+ωΔt) ;
        0 1  -v/ω sinθ + v/ω sin(θ+ωΔt) ;
        0 0   1 ]                                   # draft prints the 3rd column with the opposite sign
  Σ̄ = G Σ_{t-1} Gᵀ + R_t                            # R_t = MOTION noise (Thrun convention)
  Q = diag(σ_r², σ_φ², σ_s²)                         # draft writes σ_r, σ_φ, σ_s (variances intended)
  for each observed feature z^i = (r, φ, s):
     j = c^i ;  δ = (m_jx - μ̄_x, m_jy - μ̄_y) ;  q = δᵀδ
     ẑ = ( √q , atan2(δ_y, δ_x) - μ̄_θ , m_js )
     H = (1/q) [ -√q δ_x   -√q δ_y    0 ;
                  δ_y       -δ_x     -q ;
                  0          0        0 ]           # draft: (1/q)[√qδx, −√qδy, 0; δy, δx, −1; 0 0 0] (wrong)
     K = Σ̄ Hᵀ (H Σ̄ Hᵀ + Q)⁻¹
     # 2005 edition: update sequentially here:  μ̄ ← μ̄ + K(z^i − ẑ) (wrap angle!);  Σ̄ ← (I − K H) Σ̄
  # draft instead sums: μ = μ̄ + Σ_i K^i (z^i − ẑ^i);  Σ = (I − Σ_i K^i H^i) Σ̄   (approximate; see pitfalls)
  return μ, Σ
```
The draft's own Eq 7.15 gives row 1 of H as $((m_x-\bar\mu_x)/\sqrt q,\ (y-\bar\mu_y)/\sqrt q,\ 0)$, which is also sign-wrong. The correct derivatives are $\partial r/\partial x=-\delta_x/\sqrt q$, $\partial r/\partial y=-\delta_y/\sqrt q$, $\partial\phi/\partial x=\delta_y/q$, $\partial\phi/\partial y=-\delta_x/q$, $\partial\phi/\partial\theta=-1$. The signature row is all zeros, so with known correspondence the signature carries no information (p. 170).

**Table 7.3 EKF localization with unknown correspondences (p. 175):** the same prediction step. Then for every map landmark $k$, compute $\hat z^k$, $H^k$ and $\Psi_k=H^k\bar\Sigma H^{kT}+Q$. For each observed feature, set $j(i)=\arg\min_k (z^i-\hat z^k)^T\Psi_k^{-1}(z^i-\hat z^k)$ and $K^i=\bar\Sigma H^{j(i)T}\Psi_{j(i)}^{-1}$, then update. Practical additions (pp. 176, 181–183): gate outliers with a Mahalanobis threshold (a $\chi^2$ test), or add an outlier class $c=N+1$ with prior $\pi_{N+1}$; enforce **mutual exclusion** (two features in one scan can't be the same landmark); prune candidate landmarks cheaply.

**Worked examples worth redoing:** Fig 7.4 (1D hallway with 3 doors, Markov localization step by step); Fig 7.5 (the same hallway with a Gaussian and labeled doors: note the posterior variance is smaller than both the prior and the measurement variance); Fig 7.6 (a single landmark shrinks the error ellipse).

**Exercises worth doing:** The draft's only exercise (§8.7, p. 219) asks you to derive the additive multi-feature update in Table 7.2 lines 14–15. It is worth doing *because the answer shows the additive form is only approximate*: it is exact in information form, $\Omega=\bar\Omega+\sum_i H^{iT}Q^{-1}H^i$, but not as a sum of Kalman gains each computed from the same $\bar\Sigma$. Also: derive $G_t$ and $H$ yourself and check them against finite differences.

**Conventions / pitfalls:**
- **Noise naming:** Thrun uses $R_t$ = motion/process noise and $Q_t$ = measurement noise. Many lecture slides (and our Notion A5) use the opposite ($Q$ process, $R$ measurement).
- Wrap bearing innovations $\phi-\hat\phi$ to $(-\pi,\pi]$.
- When $\omega\to0$ the $v/\omega$ terms blow up, so switch to the straight-line limit.
- $R_t$ should scale with $|v|,|\omega|$ (p. 172).
- Rule of thumb (p. 183): EKF/MHT linearization fails once the heading standard deviation exceeds about ±20°. So Gaussian localizers cannot do global localization or kidnapping, even with a huge initial covariance.
- An EKF cannot use **negative information** (not seeing an expected landmark).

---

## Ch 8 Grid and Monte Carlo Localization (PDF pp. 199–231, book pp. 187–219), Lessons: A6 (core), A5
*(Outside the assigned range, read for the A5/A6 check. This is the chapter for ASBR Assignment 5.)*

**Big idea:** To localize globally and recover from failure, drop the Gaussian. A grid (histogram) or a particle set can carry many hypotheses at once, can use raw scans without feature extraction, and can use negative information. MCL, the particle-filter version, adapts its cost to the available compute through the particle count M. Its standard failure mode is losing every particle near the true pose, which the book fixes by injecting random particles (augmented MCL) or by sampling some particles from the sensor model (mixture MCL).

**Algorithms:**

*Table 8.1 Grid localization (p. 188)*, a discrete Bayes filter over cells $x_k$:
```
for all k:  p̄_k = Σ_i p_{i,t-1} · motion_model(mean(x_k), u_t, mean(x_i))
            p_k  = η · measurement_model(z_t, mean(x_k), m) · p̄_k        # draft omits the "· p̄_k" factor (typo)
```
Typical resolution is 15 cm × 15 cm × 5°. With coarse grids you must *inflate* the sensor and motion noise; otherwise a robot moving 10 cm/s with 1 m cells never changes cell (pp. 191–192, Figs 8.3–8.4). Speed-ups (pp. 193–194): pre-cache ray-cast ranges per cell; subsample beams (8 of 360 worked); delay motion updates; update cells selectively.

*Table 8.2 MCL (p. 200):*
```
MCL(X_{t-1}, u_t, z_t, m):
  X̄ = X = ∅
  for m = 1..M:
     x^[m] = sample_motion_model(u_t, x_{t-1}^[m])
     w^[m] = measurement_model(z_t, x^[m], m)       # beam model or likelihood field
     X̄ += <x^[m], w^[m]>
  for m = 1..M:
     draw i with probability ∝ w^[i];  add x^[i] to X
  return X
```
Initialization: draw M particles from $p(x_0)$, which is uniform over free space for global localization, each with weight $1/M$. One option is to keep sampling until the next $(u,z)$ arrives, which makes M adapt to the CPU (p. 204).

*Table 8.3 Augmented MCL (p. 206), the "random particle MCL" that recovers from kidnapping:*
```
Augmented_MCL(X_{t-1}, u_t, z_t, m):
  static w_slow, w_fast
  X̄ = X = ∅ ;  w_avg = 0                           # draft forgets to reset w_avg
  for m = 1..M:
     x^[m] = sample_motion_model(u_t, x_{t-1}^[m]);  w^[m] = measurement_model(z_t, x^[m], m)
     X̄ += <x^[m], w^[m]>;  w_avg += w^[m] / M
  w_slow += α_slow (w_avg − w_slow)
  w_fast += α_fast (w_avg − w_fast)                  # require 0 ≤ α_slow ≪ α_fast
  for m = 1..M:
     with probability max(0, 1 − w_fast/w_slow): add a random pose to X
     else: draw i ∝ w^[i]; add x^[i] to X
  return X
```
Rationale (Eqs 8.3–8.4): the mean weight $\frac1M\sum_m w^{[m]}$ is a stochastic estimate of $p(z_t\mid z_{1:t-1},u_{1:t},m)$. A short-term average that drops below the long-term average signals that the robot is lost. Smoothing keeps a single noisy scan from triggering injection. Random poses can be drawn uniformly, or, for landmark sensors, directly from the measurement likelihood (Table 6.5). Example: RoboCup legged-robot relocalization (Fig 8.15).

*Mixture MCL (§8.3.4, pp. 209–211, Eqs 8.5–8.6):* Paradox: with a *perfect* sensor, plain MCL fails, because particles proposed by the motion model almost never land on the measurement manifold. Fixes: (a) inflate the sensor noise; (b) generate a fraction (about 5%) of particles from $x\sim p(z_t\mid x)$, weighted by $\int p(x\mid u_t,x_{t-1})\,bel(x_{t-1})\,dx_{t-1}$. Fig 8.16: mixture MCL < MCL+random < plain MCL in error rate. Mixture MCL is also a principled answer to kidnapping, but it is hard to implement because it needs an invertible sensor model, and the book used density trees.

*Table 8.4 Range-measurement rejection in dynamic environments (§8.4, pp. 211–216, Eqs 8.7–8.10):* Introduce a cause variable $\bar c\in\{hit,short,max,rand\}$. Compute $p(\bar c=short\mid z^k)=\frac{\int z_{short}p_{short}\,bel}{\sum_c\int z_cp_c\,bel}$ by averaging over particles, and reject the beam if it exceeds $\chi$.
```
p = q = 0
for each particle x^[m]:
   p += z_short · p_short(z^k | x^[m], m)          # draft line 4 says z_hit·p_hit: a typo
   q += z_hit p_hit + z_short p_short + z_max p_max + z_rand p_rand
accept if p/q ≤ χ else reject
```
This filters out *surprisingly short* readings (people) but keeps surprisingly long ones, so recovery stays possible. Fig 8.20: final error 19 cm with standard MCL vs 1 cm with rejection (museum robot Rhino). The book notes this is useful even in static maps with clutter, because the beam model is discontinuous in pose. *The likelihood-field model is smooth in pose, so it does not need this trick* (p. 216).

**Key results (Table 8.5, p. 217), comparison:**

| | EKF | MHT | Coarse (topological) grid | Fine (metric) grid | MCL |
|---|---|---|---|---|---|
| Measurements | landmarks | landmarks | landmarks | raw | raw |
| Noise | Gaussian | Gaussian | any | any | any |
| Posterior | Gaussian | Gaussian mixture | histogram | histogram | particles |
| Efficiency (memory / time) | ++ / ++ | ++ / + | + / + | − / − | + / + |
| Ease of implementation | + | − | + | − | ++ |
| Resolution | ++ | ++ | − | + | + |
| Robustness | − | + | + | ++ | ++ |
| Global localization | no | no | yes | yes | yes |

**Likelihood-field measurement model (draft §6.4, Table 6.3, PDF 151–155; the model used in ASBR A5):**
```
likelihood_field_range_finder_model(z_t, x_t=(x,y,θ), m):
  q = 1
  for all beams k:
     if z^k ≠ z_max:                                   # max-range readings are DISCARDED
        x_z = x + x_sens cosθ − y_sens sinθ + z^k cos(θ + θ_sens)
        y_z = y + y_sens cosθ + x_sens sinθ + z^k sin(θ + θ_sens)
        dist = min over occupied (x',y') of ‖(x_z,y_z) − (x',y')‖     # precompute as a distance transform
        q *= z_hit · prob(dist, σ_hit) + z_rand / z_max
  return q
```
Full mixture (Eq 6.35): $z_{hit}p_{hit}+z_{rand}p_{rand}+z_{max}p_{max}$, with $p_{hit}=\mathcal N(\text{dist};0,\sigma_{hit}^2)$. The model is "ad hoc", meaning it is not a physical generative model. But it is **smooth in pose**, needs no ray casting, and ignores occlusion (it "sees through walls").

**Worked examples worth redoing:** Fig 8.11 (1D hallway MCL, showing the weights before and after resampling); Fig 8.12 (sonar MCL; ambiguity persists for 55 m); Figs 8.6–8.9 (grid global localization with laser and sonar, AAAI-94 arena); Fig 8.15 (kidnapping recovery).

**Conventions / pitfalls:**
- Resampling with too few particles over a large volume (e.g., M = 50 in global localization) causes **particle deprivation** (p. 205).
- A sensor that is too accurate starves MCL. Inflate $\sigma_{hit}$, or raise the product of beam likelihoods to a power less than 1, to account for correlated beams (p. 218).
- Multiply many beam likelihoods in log space.
- Resample only when needed; the low-variance sampler is in ch 4.
- The draft has *no KLD sampling* (adaptive M from the KL bound). For that, use the 2005 edition §8.3.7 / Fox 2003.

---

## Ch 9 Occupancy Grid Mapping (PDF pp. 233–255, book pp. 221–243; assigned range starts at PDF 246 = §9.3.3), Lessons: A6 (maps for MCL), A3 (grid C-space), F8
**Big idea:** With *known poses*, mapping becomes estimation. Represent the map as a grid of binary random variables, assume the cells are independent ($p(m\mid z,x)=\prod_i p(m_i\mid z,x)$, Eq 9.4), and run a static binary Bayes filter in log-odds per cell. This is how the maps used for MCL and for planning are usually built, after some SLAM method has fixed the poses.

**Key results & equations:**
- Log-odds $l_{t,i}=\log\frac{p(m_i\mid z_{1:t},x_{1:t})}{1-p(\cdot)}$ (Eq 9.5); $p=1-\frac{1}{1+e^{l}}$ (Eq 9.6); prior $l_0=\log\frac{p(m_i)}{1-p(m_i)}$ (Eq 9.7).
- **Table 9.1:** for cells in the perceptual field of $z_t$, $l_{t,i}=l_{t-1,i}+\text{inverse\_sensor\_model}(m_i,x_t,z_t)-l_0$; other cells are unchanged. (Eq 9.8 should say the inverse model returns the *log-odds* of $p(m_i\mid z_t,x_t)$.)
- **Table 9.2 inverse range sensor model:** For a cell centre, compute $r$ and $\phi=\operatorname{atan2}(\cdot)-\theta$, and pick the nearest beam $k$. If $r>\min(z_{max},z^k+\alpha/2)$ or $|\phi-\theta_k|>\beta/2$, return $l_0$. If $z^k<z_{max}$ and $|r-z^k|<\alpha/2$, return $l_{occ}$ (the draft misprints $|r-z_{max}|$). If $r\le z^k$, return $l_{free}$. Here $\alpha$ is obstacle thickness and $\beta$ is beam width. Also mark the robot's own footprint as free (p. 230).
- **Multi-sensor fusion (Eq 9.9):** build one map per sensor type and combine them pessimistically with $m_i=\max_k m^k_i$, rather than Bayes-fusing sensors that see different obstacle types.
- **Learning inverse models (§9.3, Eqs 9.10–9.20):** $p(m_i\mid x,z)=\eta\int_{m:m(i)=m_i}p(z\mid x,m)p(m)\,dm$ is intractable. Instead, simulate triples (map from a prior, pose, measurement from the forward model) and fit a function approximator such as a neural network. Exploit invariances: use *relative* polar coordinates of the cell (Eq 9.14 rotates the cell into the robot frame), use only nearby beams, and share one model across identical sensors. Train with the **cross-entropy** loss $J(W)=-\sum_i\big[m_i\log f+(1-m_i)\log(1-f)\big]$ (Eq 9.20). The draft's text says "(9.20)" where it means (9.17). Fig 9.6 shows learned sonar interpretations.
- **MAP occupancy mapping (§9.4, Table 9.3, Eqs 9.21–9.24):** Independent cells cause conflicts: two sonar cones disagreeing at a wall (Fig 9.7), and doorways that never get mapped (Fig 9.8). Instead, maximize $\sum_t\log p(z_t\mid x_t,m)+\sum_i l_0\,m_i$ by **hill climbing over cell flips**, using the *forward* model. Start from the all-free map, or warm-start from the standard map. This needs all the data (batch), gives no uncertainty beyond a local sensitivity analysis, and needs $p(m_i)$ not near 1.

**Worked examples:** Fig 9.4 (people filtered out of the map); Fig 9.8 (the open door is recovered only by the MAP map).
**Exercises worth doing:** Implement Tables 9.1 and 9.2 on simulated scans with known poses, then use the result as the map for your MCL (A6). Design the data structure the text leaves as an exercise: which measurements are affected when a cell flips (p. 242).
**Pitfalls:** Subtract $l_0$ in the update. Clamp the log-odds so the map can still change (dynamic objects). Independent cells overcount correlated sonar evidence.

---

## Ch 10 Simultaneous Localization and Mapping: EKF SLAM (PDF pp. 257–278, book pp. 245–266), Lessons: A5 (EKF in a big state), A6 (context), F8
**Big idea:** In SLAM you know neither the map nor the poses. *Online SLAM* estimates $p(x_t,m\mid z_{1:t},u_{1:t})$ (Eq 10.1). *Full SLAM* estimates $p(x_{1:t},m\mid z_{1:t},u_{1:t})$ (Eq 10.2). Online SLAM is full SLAM with past poses integrated out (Eq 10.3), plus summing over correspondences when they are unknown (Eqs 10.4–10.6). EKF SLAM puts robot pose and landmarks into one Gaussian state vector. The punchline: the **cross-covariances** carry the history. Re-observing an old, well-known landmark shrinks the robot's uncertainty *and* that of every correlated landmark (loop closure, Fig 10.1d). In the limit all landmark estimates become fully correlated (Fig 10.2): the map becomes known exactly in *relative* terms, while its absolute placement stays as uncertain as the initial pose.

**Key definitions:** Combined state $y_t=(x,y,\theta,m_{1,x},m_{1,y},s_1,\dots,m_{N,x},m_{N,y},s_N)^T$, dimension $3N+3$ (Eq 10.7). Prior (Eqs 10.9–10.10): $\mu_0=0$; $\Sigma_0$ has a $3\times3$ zero block for the pose (which defines the origin) and $\infty$ everywhere else. Assumptions (§10.2.1): point-landmark maps (usually fewer than 1000 landmarks), Gaussian noise with small uncertainty, and positive sightings only (no negative information).

**Key results & equations:**
- Motion acts only on the pose (Eqs 10.11–10.14): $y_t=y_{t-1}+F_x^T\,g_{pose}(u_t,\cdot)+\mathcal N(0,F_x^TR_tF_x)$, where $F_x=[I_3\ \ 0_{3\times3N}]$ (Eq 10.13; Table 10.1 misprints the zero block as $2N$ wide).
- $G_t=I+F_x^T g_tF_x$ (Eq 10.16). The $3\times3$ $g_t$ has nonzero entries only in column 3: $(-\tfrac v\omega\cos\theta+\tfrac v\omega\cos(\theta+\omega\Delta t),\ -\tfrac v\omega\sin\theta+\tfrac v\omega\sin(\theta+\omega\Delta t),\ 0)$. *The draft's Eq 10.17 and Table 10.1 line 4 have the opposite sign; see Ch 7.*
- Measurement Jacobian $H^i_t=h^i_tF_{x,j}$ (Eq 10.20). $F_{x,j}$ is $6\times(3N+3)$ and selects the pose and landmark $j$ (the draft says "(3N+3)×5", and its zero blocks should be $3j-3$ and $3N-3j$ wide, not $2j-2$ and $2N-2j$). The correct low-dimensional Jacobian, over $(x,y,\theta,m_x,m_y,m_s)$, is
  $$h^i_t=\frac1q\begin{pmatrix}-\sqrt q\,\delta_x&-\sqrt q\,\delta_y&0&\sqrt q\,\delta_x&\sqrt q\,\delta_y&0\\ \delta_y&-\delta_x&-q&-\delta_y&\delta_x&0\\0&0&0&0&0&q\end{pmatrix}$$
  where $\delta=\bar\mu_{j}-\bar\mu_{t,xy}$ and $q=\delta^T\delta$. Draft row 2 shows $-1$ and row 3 shows $1$ under a $1/q$ prefactor; both must be scaled by $q$.
- **Landmark initialization** (Eq 10.23 / line 10): the first time landmark $j$ is seen, set $\bar\mu_j=\bar\mu_{t,xy}+r\,(\cos(\phi+\bar\mu_\theta),\sin(\phi+\bar\mu_\theta))$ and $\bar\mu_{j,s}=s$. Otherwise you linearize around (0,0). This is only possible because $h$ is invertible (range+bearing). **Bearing-only (camera) SLAM needs several sightings and triangulation** (p. 256).
- Cost: memory and time are $O(N^2)$ per update (p. 256). The Kalman gain is dense, $(3N+3)\times3$ (the text calls it "3 by 3N+3"), so every observation updates every correlated landmark.

**Algorithms:**
- *Table 10.1 EKF_SLAM_known_correspondences (p. 249).* Prediction: lines 2–5 (pose block only, $\bar\Sigma=G\Sigma G^T+F_x^TR_tF_x$). For each feature: initialize it if new (line 10), compute $\delta,q,\hat z,F_{x,j},H$ and $K=\bar\Sigma H^T(H\bar\Sigma H^T+Q)^{-1}$. Then, as in the localization table, $\mu=\bar\mu+\sum_iK^i(z^i-\hat z^i)$ and $\Sigma=(I-\sum_iK^iH^i)\bar\Sigma$. *Implement the update sequentially, feature by feature (2005 form).*
- *Table 10.2 EKF_SLAM with ML correspondences and outlier rejection (p. 257).* For each feature, hypothesize a new landmark $N_t+1$ initialized from the measurement. For all $k\le N_t+1$, compute $\Psi_k=H^k\bar\Sigma H^{kT}+Q$ and $\pi_k=(z-\hat z^k)^T\Psi_k^{-1}(z-\hat z^k)$. Set $\pi_{N_t+1}=\alpha$, a **new-landmark threshold** on the Mahalanobis distance. Take $j(i)=\arg\min_k\pi_k$, set $N_t=\max(N_t,j(i))$ (growing the state when needed), and $K^i=\bar\Sigma H^{j(i)T}\Psi_{j(i)}^{-1}$.
- *Map management (§10.3.3):* keep a **provisional landmark list** (not used for pose correction until confirmed), and a per-landmark existence **log-odds** $o_j$ (increment when seen, decrement when it should have been seen; delete below a threshold). Choose landmarks that are far apart and have distinctive signatures.

**Worked examples:** Fig 10.1 (loop closure shrinks all ellipses); Fig 10.2 (the correlation matrix becomes a checkerboard); Figs 10.3–10.4 (Oberon underwater vehicle with sonar, 14 landmarks).
**Exercises / projects worth doing (§10.6):** P1 (replace the EKF by particle filters: this *is* FastSLAM, which the draft has no chapter on); P2 (mixture-of-Gaussians data association). Also implement Table 10.2 in Python on a simulated landmark world and watch the correlation matrix. This is the natural follow-on to the A5 EKF.
**Pitfalls:** the $\omega\to0$ singularity; angle wrapping; quadratic cost; ML data association can't be undone, so a single bad association can be catastrophic; dense correlation means you must not "zero out" small cross-covariances naively (that makes the filter overconfident). The text calls EKF SLAM "mostly historical" (p. 264).

---

## Ch 11 The Extended Information Form (EIF) algorithm, i.e. GraphSLAM (PDF pp. 279–314, book pp. 267–302), Lessons: F4, F6 (least squares / Gauss–Newton), A5
**Big idea:** Solve **full SLAM** in batch. Write the log-posterior as a sum of quadratic "springs": one for the prior, one per control linking $x_{t-1}$ to $x_t$, and one per measurement linking $x_t$ to $m_j$. Linearizing turns it into a sparse information matrix $\Omega$ and vector $\xi$. Then (1) marginalize out the landmarks with a Schur complement, which leaves pose-to-pose links; (2) solve for the path; (3) recover each landmark given the path; (4) relinearize and repeat. This is Gauss–Newton nonlinear least squares in disguise, and it is the ancestor of modern pose-graph SLAM (g2o, GTSAM). In the 2005 edition this chapter is renamed **GraphSLAM**.

**Key results & equations:**
- Full posterior (Eqs 11.5–11.6): $p(y_{0:t}\mid\cdot)=\eta\,p(x_0)\prod_t\big[p(x_t\mid x_{t-1},u_t)\prod_ip(z^i_t\mid y_t,c^i_t)\big]$. Its negative log is a sum of Mahalanobis terms (Eq 11.11), with anchor $\Omega_0=\infty I$ on $x_0$ (Eqs 11.9–11.10).
- After linearization, $\log p=\text{const}-\tfrac12y^T\Omega y+y^T\xi$ (Eq 11.15) with additive updates (Eqs 11.16–11.20). Corrected form, ordering $(x_{t-1},x_t)$:
  - control: $\Omega\mathrel{+}=\begin{pmatrix}-G_t^T\\I\end{pmatrix}R_t^{-1}\begin{pmatrix}-G_t&I\end{pmatrix}$, $\ \xi\mathrel{+}=\begin{pmatrix}-G_t^T\\I\end{pmatrix}R_t^{-1}\big[g(u_t,\mu_{t-1})-G_t\mu_{t-1}\big]$
  - measurement: $\Omega\mathrel{+}=H^{iT}Q^{-1}H^i$, $\ \xi\mathrel{+}=H^{iT}Q^{-1}\big[z^i-h(\mu_t)+H^i\mu_t\big]$
  - The draft's Eqs 11.14/11.18 and Table 11.2 line 8 write $+G_t\mu_{t-1}$, and Eq 11.20 writes $-H\mu$. Both signs are wrong: expand $\|x_t-g-G(x_{t-1}-\mu)\|^2$ yourself to check.
- **Information-form lemmas** (Tables 11.6–11.7, worth memorizing):
  - Marginal: $\bar\Omega_{xx}=\Omega_{xx}-\Omega_{xy}\Omega_{yy}^{-1}\Omega_{yx}$ and $\bar\xi_x=\xi_x-\Omega_{xy}\Omega_{yy}^{-1}\xi_y$ (Eq 11.22, a Schur complement).
  - Conditional: $p(x\mid y)$ has information matrix $\Omega_{xx}$ and information vector $\xi_x-\Omega_{xy}y$. *The draft prints $+$ (Eq 11.24); the correct sign is minus.*
- Reduction (Eqs 11.30–11.35): $\Omega_{mm}$ is **block-diagonal** because measurements never link two landmarks directly. So eliminating each landmark is local: it only touches the poses $\tau(j)$ that observed it, and it creates pose–pose links among them.
- Recovery (Eqs 11.36–11.43): $\tilde\Sigma=\tilde\Omega^{-1}$, $\tilde\mu=\tilde\Sigma\tilde\xi$, and $\mu_j=\Omega_{jj}^{-1}\big(\xi_j-\Omega_{j,\tau(j)}\tilde\mu_{\tau(j)}\big)$ (the draft shows $+$). Only the *path* covariance is returned; the map is conditioned on the path.
- Without cycles $\Omega$ can be ordered into a band and solved in $O(t)$. Loops create long-range links, and that is where the cost lies (pp. 269–270).

**Algorithms:**
- Table 11.1 `EIF_initialize`: dead-reckon the means from the controls.
- Table 11.2 `EIF_construct`: build Ω and ξ in O(t). Each measurement adds a 5×5 block between $x_t$ and $m_j$.
- Table 11.3 `EIF_reduce`: for each $j$, subtract $\Omega_{\tau j}\Omega_{jj}^{-1}\xi_j$ from ξ at $x_{\tau(j)}$ and $\Omega_{\tau j}\Omega_{jj}^{-1}\Omega_{j\tau}$ from Ω, then delete $m_j$.
- Table 11.4 `EIF_solve`.
- Table 11.5: repeat construct, reduce, solve until convergence. Two or three iterations suffice; iteration matters mostly when the initial heading error exceeds about 20°.
- **Unknown correspondence:**
  - Table 11.8 `EIF_correspondence_test(j,k)`: take the marginal over $(m_j,m_k)$, $\Omega_{[jk]}=\Omega_{jk,jk}-\Omega_{jk,\tau}\Sigma_{\tau\tau}\Omega_{\tau,jk}$ and $\xi_{[jk]}=\Omega_{[jk]}\mu_{jk}$. Form $\Delta=m_j-m_k$ with $\Omega_\Delta=(I,-I)\Omega_{[jk]}(I,-I)^T$ and $\xi_\Delta$ likewise. Return $p(\Delta=0)=|2\pi\Omega_\Delta^{-1}|^{-1/2}\exp(-\tfrac12\mu_\Delta^T\Omega_\Delta\mu_\Delta)$ (Eq 11.54). The draft writes $\Omega_\Delta^{-1}$ inside the exponent; it should be $\Omega_\Delta$, since the covariance is $\Omega_\Delta^{-1}$.
  - Table 11.9 `EIF_SLAM`: greedy merging of feature pairs whose probability exceeds χ, rebuilding after each merge. Line 17's "π < χ" should be "> χ".
- Practical notes (§11.6): pre-merge obvious matches into local maps (local occupancy grids, one node per 5 m of travel); enforce mutual exclusion by matching whole scans; undoing associations is possible but there is no test for when to do it; negative information is ignored.
- Example: the Groundhog mine-mapping robot (Figs 11.1–11.5). Pairwise scan matching gives duplicated corridors; EIF data-association search fixes them.

**Exercises worth doing:** Implement 1D/2D pose-graph least squares with Ω, ξ assembly. Verify that the Schur-complement reduction gives the same path as solving the full system. This directly reinforces F4/F6 (normal equations, Gauss–Newton) and CIS C2/C6 (least-squares registration).
**Pitfalls:** You must anchor $x_0$, or Ω is singular (gauge freedom). Heading linearization errors are what force relinearization. Ω is sparse but Σ is dense, so never invert the full Ω.

---

## Ch 12 The Sparse Extended Information Filter, SEIF (PDF pp. 315–363, book pp. 303–351), Lessons: A5 (information filter), F4
**Big idea:** This is online SLAM in information form. The *normalized* SLAM information matrix is nearly sparse: strong links exist only between nearby landmarks. SEIF forces that matrix to be exactly sparse by bounding the number of *active* landmarks, those linked to the robot pose. That makes every update constant time. It sits between EKF SLAM (proactive, $O(N^2)$) and EIF/GraphSLAM (lazy, batch). The cost is two approximations: sparsification, which makes it **overconfident** ("inconsistent", p. 349), and only approximate recovery of the mean.

**Key mechanics (§12.2):**
- *Measurement update* adds a link between the robot and the observed landmark. It is local and exact: $\Omega\mathrel{+}=\sum_iH^{iT}Q^{-1}H^i$ and $\xi\mathrel{+}=\sum_iH^{iT}Q^{-1}[z^i-\hat z^i+H^i\mu]$ (Eqs 12.18–12.19; the draft prints $-H\mu$). Note that summing over features is **exact in information form**, unlike summing Kalman gains.
- *Motion update* weakens robot–landmark links and creates landmark–landmark links between **active** landmarks. Integrating out the old pose fills in the Schur complement.
- *Sparsification* cuts robot–landmark links to deactivate landmarks.
- *State recovery*: amortized coordinate descent on $\mu=\Omega^{-1}\xi$.

**Key results & equations:**
- Table 12.1, the outer loop: motion update, update state estimate, measurement update, sparsification.
- **Table 12.2 motion update in O(1)** (Eqs 12.8–12.15):
  - $\Psi=F_x^T[(I+\Delta)^{-1}-I]F_x$
  - $\lambda=\Psi^T\Omega+\Omega\Psi+\Psi^T\Omega\Psi$
  - $\Phi=\Omega+\lambda$
  - $\kappa=\Phi F_x^T(R^{-1}+F_x\Phi F_x^T)^{-1}F_x\Phi$
  - $\bar\Omega=\Phi-\kappa$
  - $\bar\xi=\xi+(\lambda-\kappa)\mu+\bar\Omega F_x^T\delta$
  - $\bar\mu=\mu+F_x^T\delta$
  - This uses the matrix inversion lemma, and $G^{-1}=I+\Psi$ because $G=I+F_x^T\Delta F_x$ only touches the pose block. (The landmark state here is 2D, so the state has $2N+3$ entries.)
- **Sparsification** (§12.5): approximate $p(a,b,c)$ by $\tilde p=p(a,c)p(b,c)/p(c)$, which removes the direct a–b link. This is the KL-closest such distribution, and it can only *increase* variances (Eqs 12.20–12.21). In SEIF, $a=x_t$, $b=m^0$ (landmarks being deactivated) and $c=m^+$ (landmarks staying active), and the passive set $m^-$ is conditioned to 0 (Eqs 12.22–12.30). Table 12.4: $\tilde\Omega=\Omega-\Omega^0F_{m0}(F_{m0}^T\Omega^0F_{m0})^{-1}F_{m0}^T\Omega^0+\Omega^0F_{x,m0}(\cdot)^{-1}F_{x,m0}^T\Omega^0-\Omega F_x(F_x^T\Omega F_x)^{-1}F_x^T\Omega$, and $\tilde\xi=\xi+(\tilde\Omega-\Omega)\mu$.
- **Amortized mean recovery** (§12.6, Eqs 12.31–12.39): $\mu$ is the mode of $\exp(-\tfrac12\mu^T\Omega\mu+\xi^T\mu)$. Coordinate descent, $\mu_i\leftarrow\Omega_{ii}^{-1}\big(\xi_i-\sum_{j\ne i}\Omega_{ij}\mu_j\big)$ (Gauss–Seidel), runs a fixed K updates per step. The text suggests conjugate gradient as a better choice.
- How sparse (§12.7, Figs 12.5–12.10): with 50 landmarks, about **6 active features** match EKF accuracy at much lower cost. EKF time and memory grow quadratically; SEIF time is constant and its memory linear.
- **Data association:**
  - Incremental ML (§12.8): approximate $p(x_t,y_c)$ using the combined **Markov blanket**, conditioning the rest on their means (Eq 12.43). Keep a candidate list for new features. Constant time breaks when closing a large loop.
  - **Tree-based (lazy) data association** (§12.9): keep the log-likelihood of the whole frontier of the data-association tree, and expand any frontier node that beats the current leaf (an A*-like argument). Soft equality constraints $\Omega\mathrel{+}=F_{i-j}^TCF_{i-j}$ (Eq 12.48) can be **removed later** (Eq 12.49) to undo an association. The correspondence test is Table 12.6. Example: a mine map (Figs 12.18–12.21; Fig 12.21 even compares against FastSLAM, which this draft never covers).
- **Multi-robot** (§12.10): transform a map in information form with $\Omega^{k\leftarrow j}=R^T\Omega^jR$ and $\xi^{k\leftarrow j}=(\xi^j+\Omega^jD^T)R^T$ (Eqs 12.50–12.53). Fuse by **adding** Ω, ξ and collapsing matched landmarks by adding rows and columns (Eqs 12.54–12.55). Correspondence comes from landmark-triplet signatures in an SR-tree. Example: Victoria Park (3.5 km, under 0.5 m error, about 2× faster and less than ¼ the memory of the EKF, Figs 12.12–12.15).

**Pitfalls:** SEIF linearizes once (unlike EIF). Removing a soft link that was never added breaks positive semidefiniteness. Constant time is optimistic once map recovery has to propagate around large loops.

---

## Ch 13 Mapping with Unknown Data Association: EM (PDF pp. 365–403, book pp. 353–391), Lessons: F8 (EM, forward–backward), brief
**Big idea:** Find the **most likely map** (not a posterior) by EM. The **E-step** is localization in the current map with *forward–backward* smoothing: $Bel(s_t)\propto\alpha_t\beta_t$, where $\alpha$ is forward localization from $s_0=(0,0,0)$ and $\beta$ is "inverse localization" from a uniform final pose (Eqs 13.45–13.71). The **M-step** is mapping from the expected poses, treating cells independently as a weighted frequency, "expected # times l observed at ⟨x,y⟩ / expected # times anything observed" (Eq 13.75). It is batch and has no uncertainty output, but it handles ambiguous data association by revising past poses (Figs 13.10–13.12: path A-B-C-D-C-B with indistinguishable places).
- §13.1 is an author's scratch "latest derivation" with indicator variables, marked "reader, don't read this". The draft chapter has placeholders ("Make Figure", "[[[Insert picture]]]").
- **Table 13.1 EM_mapping:** α forward pass, β backward pass, Bel = αβ, then per-cell counts. 3–5 iterations usually suffice. The first E-step ignores observations, so poses come from odometry only.
- Grid speed-ups (§13.5): subsample the data, update selectively, cache the motion model, and exploit symmetry with relative pose coordinates (Eqs 13.85–13.89). Together these gave about a 10^8 memory reduction.
- **Layered EM** (§13.6, Table 13.2): local occupancy maps over about 5–10 m segments, each with a pose belief. The perceptual model is a product over local maps (Eq 13.108). The M-step uses **deterministic annealing**, $Bel\propto(\alpha\beta)^{1/\sigma}$, with $\sigma\leftarrow0.9\sigma$ (Eq 13.116). The draft calls $\sigma$ a temperature in (0,1], yet with exponent $1/\sigma$, σ→0 gives the hard maximum.
- Taxonomy (§13.8): robot-centric vs world-centric maps; topological vs metric maps.
- Draft inconsistency: Table 13.1 line 14 uses the forward model $P(o\mid s,m=l)$, while Eq 13.76 uses the inverse model $P(m=l\mid o,s)$.

---

## Ch 14 Fast Incremental Mapping (PDF pp. 405–432, book pp. 393–420), Lessons: A6 (scan matching with the likelihood field), C6 (ICP analogue), F6
**Big idea:** This is real-time, non-probabilistic incremental ML mapping (scan matching). At each step, pick the pose that maximizes $\log p(o_t\mid s,m_{t-1})+\log p(s\mid a_{t-1},s_{t-1})$ (Eq 14.13) by **gradient ascent** from the odometry prediction (Eqs 14.16–14.17, Table 14.1), then paste the scan into the map. It works for hallways. It fails to close loops (Fig 14.2) because it cannot fix past poses. Fix: run an MCL posterior alongside, and when the posterior mode and the ML pose disagree at loop closure, spread the error linearly back around the loop and refine (Table 14.5, Fig 14.3). This is the ancestor of modern scan-matching front ends with pose-graph back ends.
- **Table 14.2: gradient of the log likelihood-field model with respect to pose** (very useful for A6/A5 extensions). For each non-max beam: compute the endpoint and its θ-derivative, find the nearest occupied point, and set $dist^2$ with $\partial dist^2/\partial(x,y,\theta)$. With $a=z_{hit}/\sqrt{2\pi\sigma^2}$, $b=-dist^2/(2\sigma^2)$ and $c=(1-z_{hit})/o_{max}$, the gradient is $\partial\log q/\partial\cdot=\frac{ae^b}{c+ae^b}\,\partial b/\partial\cdot$. Precompute the nearest neighbour on a 10–20 cm grid (a distance transform) for a ~100× speed-up (§14.3.3).
- Table 14.3: gradient of the log odometry motion model (incomplete in the draft: "???" entries). Table 14.4: derivative of the zero-mean normal density.
- Scan alignment converges from a 10 cm + 30° error in about 100 iterations (Fig 14.1). Make the model *symmetric*, penalizing map-occupied cells that fall in the scan's free space too.
- §14.4: the posterior's motion model must model the *residual error of the ML estimator*. Its covariance can come from the Hessian of the log-likelihood. Measurements should only be used for loop-closure conflicts, so that no data is double-counted.
- Mapping without odometry works (Fig 14.4).
- Multi-robot (§14.5): global localization (MCL) of robot 2 in the leader's map; merge once the **entropy** falls below a threshold.
- 3D mapping (§14.6): an upward-pointing laser plus a panoramic camera gives textured VRML models.
- Projects (§14.9): 3D pose estimation, multi-robot mapping without a common start, semantic components.

---

## Ch 15 Markov Decision Processes (PDF pp. 433–448, book pp. 421–436), Lessons: A3 (navigation functions / value-function planners), H-track (RL background)
**Big idea:** Planning must account for uncertainty. A *classical* plan is a fixed action sequence (no uncertainty). An *MDP* has stochastic actions but a fully observable state, so you need a **policy** over all states (a "universal plan" or navigation function). A *POMDP* adds partial observability, so you plan in **belief space**, which yields information-gathering behaviour (Fig 15.1: go look at the landmark before heading for goal or pit). The chapter text is "Markov Devision Processes" in the draft.

**Key results & equations:**
- Payoff $c(s)$, e.g. +100 at the goal and −1 per step (Eq 15.1). Expected cumulative discounted payoff $C^T=E[\sum_{\tau=1}^T\gamma^\tau c_{t+\tau}]$ (Eq 15.3).
- Horizons: greedy (T=1), finite (usually γ=1), infinite (needs γ<1, since $C^\infty\le c_{max}/(1-\gamma)$, Eqs 15.4–15.5).
- Finite-horizon recursion (Eqs 15.9–15.14) and the Bellman equation (Eq 15.15), $C(s)=\max_a\int[c(s')+\gamma C(s')]p(s'\mid a,s)\,ds'$. **The draft drops γ from Eqs 15.11–15.20 even though Eq 15.10 includes it, so put it back.** The greedy policy with respect to the converged value function is optimal (Eq 15.17).
- **Table 15.1 MDP value iteration:** set $\hat C=0$, then repeat $\hat C(s)\leftarrow\max_a\sum_{s'}[c(s')+\gamma\hat C(s')]p(s'\mid a,s)$. The update order does not matter as long as every state is updated infinitely often.
- Fig 15.2: value iteration over the C-space of a circular robot (obstacles grown by the robot radius). It gives a navigation function, i.e. a stochastic generalization of Dijkstra/wavefront planning, paired in practice with a reactive collision avoider.

**Exercises worth doing:** Grid-world value iteration with an 80/10/10 slip model. Compare the result with an A* path, and watch the policy prefer wide corridors once motion noise is added (the narrow vs wide corridor discussion, p. 425).

---

## Ch 16 Partially Observable MDPs (PDF pp. 449–488, book pp. 437–476), Lessons: A6 (belief = particle filter), H-track, brief
**Big idea:** Do value iteration over **beliefs**. Rather than integrating over the next belief, integrate over the next **observation**. Each $(b,a,o')$ gives a unique next belief $B(b,a,o')$ from the Bayes filter (Eqs 16.30–16.34). So $C^T(b)=\max_a\int\big[c(B(b,a,o'))+C^{T-1}(B(b,a,o'))\big]p(o'\mid a,b)\,do'$. For finite worlds the value function is **piecewise linear and convex** (PWLC). Convexity means knowing is never worse than not knowing (Eq 16.28).

**Key results & equations:**
- **Worked example (Fig 16.1, pp. 439–447):** states s1–s4, actions a1 (switch between s1 and s2, which also senses) and a2 (commit; s3 pays +100, s4 pays −100), observations o1/o2, γ=0.9.
  - $C^1(b)=\max\{0;\ 72p_1-72p_2\}$ (Eq 16.11).
  - $C^2(b)=\max\{0;\ -33.048p_1+7.776p_2;\ 72p_1-72p_2\}$ (Eq 16.27).
  - Observation probabilities are the Bayes normalizers ($\eta_i^{-1}=p(o_i\mid a,b)$, Eq 16.20).
  - *Caution:* Eq 16.15 swaps $p'_1$ and $p'_2$, and Eq 16.19 is mislabeled $o_1$. Redo the arithmetic yourself; it is a good Bayes-filter drill.
- **Finite POMDP backup (Eqs 16.42–16.57):** $C^T(b,a)=\gamma\sum_{o'}\max_k\sum_i(c_i+C^{T-1}_{k,i})p(o'\mid s'_i)\sum_jp(s'_i\mid a,s_j)p_j$. The $p(o'\mid a,b)$ factor cancels. In the **Table 16.1 linear-programming form**, the constraint count grows as $|\Phi^T|=|A|\,|O|^{|\Phi^{T-1}|}$ (Eq 16.68): doubly exponential, so pruning dominated vectors is essential.
- **Table 16.2 general POMDP** is in-principle only, since the value function over infinite-dimensional beliefs has no representation.
- **MC-POMDP (§16.4):** particle-filter beliefs (Table 16.3 `particle_filter_2(b,a,o')`), Monte Carlo backups, Q over beliefs stored by **k-nearest-neighbour with KL divergence** between kernel-smoothed particle sets. Results: the heaven/hell "priest" task (about 25-step lookahead, solved after 17 episodes) and a simulated find-and-grasp task (>80% success after about 4000 iterations). *The draft's inner-loop line 10 accumulates into $C^T(b)$ where it should accumulate into $C^T(b,a)$.*
- **Augmented MDP / coastal navigation (§16.5):** compress the belief to $\bar b=\langle\arg\max_s b(s),\ H[b]\rangle$ (Eqs 16.80–16.81), learn $p(\bar b'\mid a,\bar b)$ by stochastic simulation, and run MDP value iteration on that (Table 16.4). The planner hugs walls to stay localized (Fig 16.5), with lower goal entropy especially for short-range sensors (Fig 16.6). This is the concrete link between *localization quality* and *planning*.
- **Exploration** has no chapter of its own in this draft. It is discussed only as information gathering (Ch 15 intro, POMDP), entropy thresholds (§14.5), and coastal navigation.

**Exercises worth doing:** Re-derive Eqs 16.12–16.27 by hand. Implement the 2-state POMDP with α-vector pruning and plot $C^T(p_1)$ for T = 1…5.

---

## Corrections & upgrades for the knowledge base

**Edition and chapter-number facts (affect "Learn from" pointers):**
- [A5, A6, ASBR README] **This upload is the early draft, not the 2005 book.** Despite the "1999–2000" copyright line, its references go up to 2003 (FastSLAM 2.0). The localization and SLAM chapter numbers match 2005 (7 localization, 8 grid/MCL, 9 occupancy, 10 EKF SLAM, 11 GraphSLAM-as-"EIF", 12 SEIF). But **sections differ**: EKF localization is **§7.5** here (2005: §7.4). The README line "PR Ch. 3.3, 7.4" is right for 2005 but off by one section for this PDF. Suggest citing as "PR §7.4 (2005) / §7.5 (draft PDF p. 178)".
- [A6] This draft contains **no KLD sampling** and **no FastSLAM chapter**; it mentions FastSLAM only in Fig 12.21 and Project 10.1. The ASBR README/plan items "KLD-sampling adapts M" and "FastSLAM: Rao-Blackwellized PF" must point to the 2005 edition (§8.3.7 and ch. 13), or to Stachniss's lectures, not to this PDF. The draft's augmented MCL is called "Random Particle MCL" (§8.3.3, Table 8.3).
- [A6] Notion A6 "Learn from: *Probabilistic Robotics* ch. 4 & 6–8" is correct for this draft: likelihood field §6.4/Table 6.3 (PDF 151–155), MCL Table 8.2 (PDF 212), augmented MCL Table 8.3 (PDF 218). Suggest making it more precise: "§6.4 (likelihood field), §8.3 (MCL, Tables 8.2–8.3), §4.2 (particle filter/resampling)".

**Notion A5 (Bayes / KF / EKF):**
- [A5] **Noise-symbol clash.** Notion uses $Q$ for process noise and $R$ for measurement noise ($\bar P=APA^T+Q$, $K=\bar PH^T(H\bar PH^T+R)^{-1}$). Thrun uses the **opposite**: $R_t$ is motion noise and $Q_t$ is measurement noise (PR Tables 3.1, 7.2: $\bar\Sigma=G\Sigma G^T+R_t$, $K=\bar\Sigma H^T(H\bar\Sigma H^T+Q_t)^{-1}$). The Notion equations are internally correct, but since the lesson says "Learn from PR ch. 2–3", **add a one-line warning**: "PR swaps Q and R: R = process, Q = measurement." Also state the KF control model as Thrun does, $x_t=A_tx_{t-1}+B_tu_t+\varepsilon_t$.
- [A5] The self-test "Kalman gain near $H^{-1}$" only makes sense when $H$ is square and invertible. Reword: "K→0 trusts the prediction; as R→0 (H invertible), $K\to H^{-1}$, i.e. trust the measurement."
- [A5] Upgrade: add **EKF landmark localization (PR Table 7.2)** as the reference example, using the **correct Jacobians**. The draft PDF has sign errors that a student copying it would propagate:
  - $G_t[0,2]=-\frac v\omega\cos\theta+\frac v\omega\cos(\theta+\omega\Delta t)$ and $G_t[1,2]=-\frac v\omega\sin\theta+\frac v\omega\sin(\theta+\omega\Delta t)$ (draft, PDF 181: opposite sign).
  - $H=\big[-\delta_x/\sqrt q,\ -\delta_y/\sqrt q,\ 0;\ \ \delta_y/q,\ -\delta_x/q,\ -1\big]$ (draft PDF 181/185: $+\delta_x/\sqrt q$ and $+\delta_x/q$ are wrong).
  - $Q=\mathrm{diag}(\sigma_r^2,\sigma_\phi^2)$, not $\sigma$.
  - Update features **sequentially**, not by summing gains computed from the same $\bar\Sigma$ (draft lines 14–15).
- [A5] Upgrade: add **ML data association with Mahalanobis gating**: $j=\arg\min_k(z-\hat z^k)^T\Psi_k^{-1}(z-\hat z^k)$ with $\Psi_k=H^k\bar\Sigma H^{kT}+Q$ (PR Table 7.3; the draft's Eq 7.29 wrongly says argmax). Add the ±20° heading-σ rule of thumb for when EKF linearization breaks (p. 183).

**Notion A6 (PF / MCL):**
- [A6] Notion says the likelihood field "scores a scan by the distance from each beam endpoint to the nearest obstacle". Correct (Table 6.3). Add the three details the assignment needs:
  1. **max-range readings are skipped**;
  2. the per-beam score is the mixture $z_{hit}\,\mathcal N(d;0,\sigma_{hit}^2)+z_{rand}/z_{max}$, multiplied over beams (work in logs);
  3. the endpoint includes the **sensor mounting offset**, $x+x_s\cos\theta-y_s\sin\theta+z\cos(\theta+\theta_s)$.
  Also note that it is smooth in pose, which is why it beats the beam model for MCL (p. 216).
- [A6] Add **augmented MCL** for kidnapping, not just "recovery from a global start": track $w_{avg}=\frac1M\sum w$ with $w_{slow},w_{fast}$ ($0\le\alpha_{slow}\ll\alpha_{fast}$) and inject random particles with probability $\max(0,1-w_{fast}/w_{slow})$ (Table 8.3). The draft forgets to reset $w_{avg}=0$ each call; mention it.
- [A6] Add the failure modes from §8.3.3–8.3.4: particle deprivation with small M, and "a too-accurate sensor breaks MCL". The fixes are to inflate $\sigma_{hit}$ or use mixture MCL with about 5% of particles drawn from $p(z\mid x)$.
- [A6] Optional upgrade: the measurement-rejection filter for people/dynamic obstacles (Table 8.4; the draft's line 4 should accumulate $z_{short}p_{short}$, not $z_{hit}p_{hit}$), and the gradient of the likelihood field with respect to pose (Table 14.2) for scan-matching refinement.

**Other errata in this draft worth knowing (for anyone using this PDF):**
- Table 8.1 line 4 is missing the factor $\bar p_{k,t}$.
- Table 9.2 line 8 should read $|r-z^k_t|<\alpha/2$.
- EKF SLAM Table 10.1 and Eqs 10.13/10.22: $F_x$ needs $3N$ zero columns, and $F_{x,j}$ needs zero blocks of $3j-3$ and $3N-3j$. The $H$ row-2/row-3 scaling also needs fixing.
- EIF/GraphSLAM: the information-vector updates need $-G\mu$ and $+H\mu$ (Eqs 11.18, 11.20, and SEIF 12.19). The conditional-Gaussian lemma needs $\xi_x-\Omega_{xy}y$ (Eq 11.24), so $\mu_j=\Omega_{jj}^{-1}(\xi_j-\Omega_{j\tau}\mu_\tau)$ (Eq 11.43). Eq 11.54 should use $\Omega_\Delta$ (not its inverse) in the exponent. Table 11.9 line 17 should use ">".
- Ch 15 drops γ from the value-iteration equations.
- Ch 13 contains unfinished author notes.

## Best-reference map
- [A5] EKF localization with landmarks: draft §7.5, Table 7.2 (PDF 181; use the corrected Jacobians above), derivation PDF 182–186. ML data association: Table 7.3 (PDF 187), derivation PDF 188–191. MHT: §7.7 (PDF 191–193). When Gaussians fail: §7.8 (PDF 193–196).
- [A5] Information filter in practice (good for understanding $\Omega=\Sigma^{-1}$, Schur complements, additivity): marginal/conditional lemmas, Tables 11.6–11.7 (PDF 294–295); SEIF motion update with the inversion lemma, Eqs 12.8–12.15 (PDF 325–328).
- [A6] **Assignment-5 core:** likelihood-field model, draft §6.4, Table 6.3 (PDF 151–155); MCL Table 8.2 and properties (PDF 212–216); augmented/random-particle MCL Table 8.3 (PDF 216–221); mixture MCL §8.3.4 (PDF 221–223); grid localization Table 8.1 and resolution effects (PDF 200–211); comparison Table 8.5 (PDF 229).
- [A6] Building the map MCL runs on: occupancy grid mapping, Tables 9.1–9.2 (PDF 237–242).
- [A6, C6] Scan matching / pose refinement with the likelihood field: §14.3, Table 14.2 (PDF 410–416); loop closure with an MCL posterior, Table 14.5 (PDF 419–424).
- [A5/A6 extension, F4, F6] SLAM: EKF SLAM Tables 10.1–10.2 (PDF 261, 269); GraphSLAM-style least squares, Tables 11.1–11.5 (PDF 283–288); SEIF, Tables 12.1–12.6 (PDF 321–350).
- [F8] Forward–backward smoothing and EM: §13.4.4, Eqs 13.45–13.71 (PDF 383–388).
- [A3] Value-iteration navigation functions: §15.3, Table 15.1, Fig 15.2 (PDF 443–448). Coastal navigation (planning under localization uncertainty): §16.5 (PDF 480–486).
