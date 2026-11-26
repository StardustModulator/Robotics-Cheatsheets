# Probabilistic Robotics (Thrun, Burgard, Fox — early draft, ©1999–2000) — Part 1 (Ch. 1–9.3.2)
Source: acb9a9bc-ProbabilisticRobotics.pdf, PDF pages 1–245 (book pages i–xiii, 1–233)

> **Edition warning.** This is the *early draft* ("EARLY DRAFT—NOT FOR DISTRIBUTION"), not the 2005 MIT Press book.
> Chapter numbers, section numbers, table numbers and equation numbers differ from the published edition
> (e.g. here motion is Ch 5 and measurement is Ch 6; in the 2005 book they are Ch 5 and Ch 6 too, but localization
> is split into Ch 7 "Markov & Gaussian" + Ch 8 "Grid & MC", and there is **no UKF anywhere in this draft** — the
> UKF (§3.4 of the 2005 book) was added later; the draft's §3.4 is the *Information Filter*).
> When citing for the student, prefer section *names*; table/equation numbers below are the draft's.

**Page offset:** PDF page = book page + 12 (front matter = PDF 1–12; book p.1 = PDF 13; checked at book p.480 = PDF 492).

## Chapter → PDF page map (whole book, 492 PDF pages)
| Ch | Title (draft) | Book pp. | PDF pp. |
|---|---|---|---|
| — | Title, Contents | i–xiii | 1–12 |
| 1 | Introduction | 1–8 | 13–20 |
| 2 | Recursive State Estimation (Bayes filter) | 9–32 | 21–44 |
| 3 | Gaussian Filters (KF, EKF, Information filter, EIF) | 33–66 | 45–78 |
| 4 | Nonparametric Filters (histogram, binary Bayes/log-odds, particle filter) | 67–90 | 79–102 |
| 5 | Robot Motion (velocity & odometry models) | 91–120 | 103–132 |
| 6 | Measurements (beam, likelihood field, correlation, feature models) | 121–156 | 133–168 |
| 7 | Mobile Robot Localization (Markov, EKF localization, correspondences, MHT) | 157–186 | 169–198 |
| 8 | Grid and Monte Carlo Localization | 187–220 | 199–232 |
| 9 | Occupancy Grid Mapping | 221–244 | 233–256 |
| 10 | SLAM (EKF SLAM) | 245–266 | 257–278 |
| 11 | The Extended Information Form Algorithm (EIF SLAM / GraphSLAM precursor) | 267–302 | 279–314 |
| 12 | The Sparse Extended Information Filter (SEIF) | 303–352 | 315–364 |
| 13 | Mapping with Unknown Data Association (EM) | 353–392 | 365–404 |
| 14 | Fast Incremental Mapping Algorithms | 393–420 | 405–432 |
| 15 | Markov Decision Processes | 421–436 | 433–448 |
| 16 | POMDPs | 437–476 | 449–488 |
| — | References | 477– | 489–492 |

This file covers PDF 1–245 = Ch 1–8 fully and Ch 9 through §9.3.2 (book p. 233).

---

## Ch 1 Introduction (PDF pp. 13–20, book pp. 1–8) — Lessons: A5, A6 (motivation)
**Big idea:** Robots live in unstructured worlds, so uncertainty is the central problem. The probabilistic
answer is to carry a whole *distribution* over hypotheses (a belief) instead of one best guess, and to act
on that belief. The thesis: "a robot that carries a notion of its own uncertainty and acts accordingly is
superior to one that does not" (p. 5).

**Key definitions:**
- Five sources of uncertainty (p. 2): environment, sensors (range/resolution limits + noise), robots (actuation noise, wear), models (abstractions are inaccurate), computation (real-time ⇒ approximate algorithms).
- *Global localization*: start with no idea of the pose (uniform prior), localize from scratch. *Kidnapped robot*: recover from a localization failure (p. 5).
- Fig 1.1 (PDF 16): the 1-D "corridor with three doors" Markov localization cartoon — uniform prior → sense door → 3 peaks → move (shift + blur) → sense door → one dominant peak. **This is the single best mental picture for A5/A6**: measurement update = multiply by $p(z|x)$ and renormalize; motion update = shift and convolve (blur).

**Key results:** price of probabilistic methods = computational cost + the need to approximate (continuous state → infinite-dimensional distributions).

**Conventions / pitfalls:** The draft's road map (p. 7) has unresolved "??" references; ignore them. Bibliographic remarks (p. 7–8) are only notes.

## Ch 2 Recursive State Estimation (PDF pp. 21–44, book pp. 9–32) — Lessons: F8, A5
**Big idea:** Everything in the book is a special case of one recursion, the Bayes filter. You keep a belief
$bel(x_t)$ = posterior over state given *all* data; each step you (1) push it through the motion model
(prediction — integrate over where you came from) and (2) multiply by the measurement likelihood and
renormalize (correction). It is exact only under the Markov (complete-state) assumption; every practical
filter (KF, EKF, histogram, PF) is a way to *represent* and *approximate* this recursion.

**Key definitions (§2.2, pp. 10–15):**
- Discrete: $\sum_x p(x)=1$ (2.2). 1-D Gaussian (2.3): $p(x)=(2\pi\sigma^2)^{-1/2}\exp\{-\tfrac12 (x-\mu)^2/\sigma^2\}$, written $\mathcal N(x;\mu,\sigma^2)$.
- Multivariate Gaussian (2.4): $p(x)=\det(2\pi\Sigma)^{-1/2}\exp\{-\tfrac12(x-\mu)^T\Sigma^{-1}(x-\mu)\}$. Note the compact $\det(2\pi\Sigma)^{-1/2}$ form ≡ $(2\pi)^{-n/2}|\Sigma|^{-1/2}$.
- A PDF integrates to 1 but its *value* may exceed 1 (p. 11).
- Joint (2.6); independence $p(x,y)=p(x)p(y)$ (2.7); conditional $p(x|y)=p(x,y)/p(y)$ (2.9).
- Total probability (2.11)/(2.12): $p(x)=\int p(x|y)p(y)\,dy$ — this *is* the prediction step.
- Bayes rule (2.13)/(2.14): $p(x|y)=\frac{p(y|x)p(x)}{\int p(y|x')p(x')dx'}$; prior $p(x)$, data $y$, posterior $p(x|y)$; $p(y|x)$ is the **generative model** (how state causes measurements).
- Normalizer (2.15): $p(x|y)=\eta\,p(y|x)p(x)$; discrete recipe (2.16)–(2.18): compute $aux_{x|y}=p(y|x)p(x)$ for all $x$, sum, divide. The same symbol η is reused for *different* constants.
- Expectation (2.19), linearity (2.20) $E[aX+b]=aE[X]+b$, covariance (2.21) $E[X^2]-E[X]^2$.
- Entropy (2.22): $H(P)=-\sum_x p(x)\log_2 p(x)$ (used later for exploration).
- Conditioned Bayes (2.23): $p(x|y,z)=p(y|x,z)p(x|z)/p(y|z)$ — the form used in the Bayes-filter derivation.
- Conditional independence (2.24)/(2.25): $p(x,y|z)=p(x|z)p(y|z)$ ⇔ $p(x|z)=p(x|z,y)$. (2.26)/(2.27): conditional independence and absolute independence do **not** imply each other.

**Robot–environment model (§2.3, pp. 16–22):**
- State $x_t$: pose (6 DOF in 3D, $(x,y,\theta)$ in the plane = "kinematic state"), joint configuration, velocities ("dynamic state"), landmark/object locations, people, sensor-broken flags, battery…
- **Complete state** (p. 17): best predictor of the future; past states/measurements/controls add nothing. Processes with complete state are Markov chains. Real systems use *incomplete* state.
- Discrete time $t=0,1,2,\dots$ (2.28). Measurement $z_t$ (2.29), $z_{t_1:t_2}$ (2.30). Control $u_t$ is the change over $(t-1,t]$ (2.31). **Odometry is treated as control data**, not measurement (p. 20). Convention: control $u_t$ first, then measurement $z_t$ (p. 20).
- Measurements tend to *increase* knowledge, motion tends to *lose* it (p. 20).
- Generative laws: (2.32)→(2.33) **state transition probability** $p(x_t|x_{0:t-1},z_{1:t-1},u_{1:t})=p(x_t|x_{t-1},u_t)$; (2.34) **measurement probability** $p(z_t|x_{0:t},z_{1:t-1},u_{1:t})=p(z_t|x_t)$. Together with $p(x_0)$ these define an HMM / dynamic Bayes network (p. 22).
- **Belief** (2.35): $bel(x_t)=p(x_t|z_{1:t},u_{1:t})$. **Prediction** (2.36): $\overline{bel}(x_t)=p(x_t|z_{1:t-1},u_{1:t})$. Going $\overline{bel}\to bel$ = correction / measurement update.

**Algorithms — Table 2.1 Bayes filter (PDF 36, p. 24):**
```
Algorithm Bayes_filter(bel(x_{t-1}), u_t, z_t):
  for all x_t do
     belbar(x_t) = ∫ p(x_t | u_t, x_{t-1}) bel(x_{t-1}) dx_{t-1}     # control update / prediction
     bel(x_t)    = η p(z_t | x_t) belbar(x_t)                        # measurement update
  endfor
  return bel(x_t)
```
Initialization: point mass if $x_0$ known; uniform if ignorant (p. 23–24). Implementable as-is only for finite state or closed-form integrals.

**Derivation (§2.4.3, PDF 40–41, pp. 28–29) — know this cold for A5:**
1. Bayes rule (2.23) on $z_t$: $p(x_t|z_{1:t},u_{1:t})=\eta\,p(z_t|x_t,z_{1:t-1},u_{1:t})\,p(x_t|z_{1:t-1},u_{1:t})$ (2.55).
2. Markov/complete state: $p(z_t|x_t,z_{1:t-1},u_{1:t})=p(z_t|x_t)$ (2.56) ⇒ $bel(x_t)=\eta\,p(z_t|x_t)\,\overline{bel}(x_t)$ (2.57)/(2.58).
3. Total probability over $x_{t-1}$ (2.59): $\overline{bel}(x_t)=\int p(x_t|x_{t-1},z_{1:t-1},u_{1:t})\,p(x_{t-1}|z_{1:t-1},u_{1:t})\,dx_{t-1}$.
4. Markov: $p(x_t|x_{t-1},z_{1:t-1},u_{1:t})=p(x_t|x_{t-1},u_t)$ (2.60).
5. **Drop $u_t$ from $p(x_{t-1}|z_{1:t-1},u_{1:t})$** because controls are "chosen at random" (a future control carries no info about the past state) ⇒ (2.61) $\overline{bel}(x_t)=\int p(x_t|x_{t-1},u_t)\,bel(x_{t-1})\,dx_{t-1}$. Proof is by induction from a correct $bel(x_0)$.

**Worked example worth redoing — door (§2.4.2, PDF 36–40, pp. 24–28):** prior 0.5/0.5; sensor $p(\text{sense open}|\text{open})=0.6$, $p(\text{sense open}|\text{closed})=0.2$ (2.39)/(2.40); push: closed→open w.p. 0.8 (2.41)/(2.42); do-nothing = identity (2.43)/(2.44).
- $u_1$=nothing, $z_1$=sense open: $\overline{bel}=(0.5,0.5)$; $bel\propto(0.3,0.1)$, η=2.5 ⇒ (0.75, 0.25) (2.49)–(2.52).
- $u_2$=push, $z_2$=sense open: $\overline{bel}=(1\cdot0.75+0.8\cdot0.25,\;0.2\cdot0.25)=(0.95,0.05)$ (2.53); $bel\propto(0.57,0.01)$ ⇒ (0.983, 0.017) (2.54).
- Lesson: even 0.983 is not "certain" — decision making should keep both hypotheses when errors are costly (autopilot remark, p. 28). Excellent by-hand drill for F8/A5.

**Markov assumption violations (§2.4.4, p. 30):** unmodeled dynamics (people), inaccurate $p(z|x)$/$p(x'|u,x)$, approximation error of the belief representation (grids, Gaussians), software state such as a "target location" that couples controls over time. Rule of thumb: define $x_t$ so that the unmodeled effects are close to random. Bayes filters are surprisingly robust to violations.

**Representation trade-offs (§2.5, p. 30–31):** computational efficiency (Gaussian = polynomial in dim; particles = any-time), approximation accuracy (Gaussian unimodal; histograms multimodal but coarse; particles flexible but may need many), ease of implementation (particles are easy for nonlinear systems).

**Exercises:** none in this chapter of the draft. Self-exercise: redo the door example with $z_2$ = sense closed; implement Table 2.1 for a 1-D cyclic corridor (Fig 1.1).

**Conventions / pitfalls:**
- In the pdf text layer the overbar of $\overline{bel}$ is lost — in the printed Table 2.1 Line 3 computes $\overline{bel}$. Don't confuse the two.
- Line 3 in the draft table is typeset with "$dx$"; it means $dx_{t-1}$.
- Order: control first then measurement in each step ($u_t$ then $z_t$).
- The derivation needs *two* assumptions: complete state (Markov) **and** controls chosen independently of the past ("at random").

## Ch 3 Gaussian Filters (PDF pp. 45–78, book pp. 33–66) — Lessons: A5 (core), F8, F4/F5 (matrix algebra)
**Big idea:** If the belief starts Gaussian and both models are *linear with additive Gaussian noise*, the
Bayes filter stays Gaussian forever, so you only track mean and covariance: that is the Kalman filter, and
it is exact. Nonlinear models break this; the EKF *pretends* they are linear by a first-order Taylor
expansion at the current best guess, giving an approximate Gaussian belief. The same Gaussian can be stored in
"moments" form $(\mu,\Sigma)$ or "canonical/information" form $(\xi,\Omega)$; prediction is cheap in moments
form, measurement update is cheap in information form — the two filters are duals.

> **No UKF in this draft.** p. 54 only *mentions* the unscented KF ("probes the function at selected points")
> and moment matching as superior linearizations. For UKF algorithm tables use the 2005 book §3.4 (Table 3.4)
> or Julier & Uhlmann; the ASBR lecture slides are the reference for A5.

**Key definitions (§3.1–3.2.1, pp. 33–36):**
- Moments representation: $\mu$ (n-vector), $\Sigma$ (n×n, symmetric PSD) ⇒ storage quadratic in n. Gaussians are **unimodal** — fine for tracking, bad for global localization (p. 33).
- Linear Gaussian system (the 3 KF assumptions, p. 34–36):
  1. (3.2) $x_t=A_t x_{t-1}+B_t u_t+\varepsilon_t$, $\varepsilon_t\sim\mathcal N(0,R_t)$; $A_t$ n×n, $B_t$ n×m. ⇒ (3.4) $p(x_t|u_t,x_{t-1})=\mathcal N(x_t;A_tx_{t-1}+B_tu_t,R_t)$.
  2. (3.5) $z_t=C_t x_t+\delta_t$, $\delta_t\sim\mathcal N(0,Q_t)$; $C_t$ k×n ⇒ (3.6) $p(z_t|x_t)=\mathcal N(z_t;C_tx_t,Q_t)$.
  3. $bel(x_0)=\mathcal N(\mu_0,\Sigma_0)$.
- **Notation (Thrun): $R_t$ = process/motion noise covariance, $Q_t$ = measurement noise covariance.** This is the *opposite* of much control/EE literature and of many course slides (often $Q$=process, $R$=measurement). Always check which is which.

**Algorithm — Table 3.1 Kalman filter (PDF 48, p. 36):**
```
Algorithm Kalman_filter(μ_{t-1}, Σ_{t-1}, u_t, z_t):
 2: μ̄_t = A_t μ_{t-1} + B_t u_t
 3: Σ̄_t = A_t Σ_{t-1} A_tᵀ + R_t
 4: K_t = Σ̄_t C_tᵀ (C_t Σ̄_t C_tᵀ + Q_t)⁻¹
 5: μ_t = μ̄_t + K_t (z_t − C_t μ̄_t)
 6: Σ_t = (I − K_t C_t) Σ̄_t
 7: return μ_t, Σ_t
```
- Complexity (p. 37): Line 4 inversion ~$O(k^{2.8})$ (k = measurement dim), Line 6 at least $O(n^2)$; for mapping (k ≪ n) the $O(n^2)$ dominates. EKF same: $O(k^{2.8}+n^2)$ (p. 53).
- Illustration Fig 3.2 (PDF 50, p. 38): 1-D localization with GPS-like measurement: fusing prior and measurement yields a mean *between* the two and a variance *smaller than both* (information integration); motion shifts and widens. Measurement decreases, prediction increases uncertainty. (Text on p. 39 says "Lines 5-7" for the measurement update — a draft typo; it is Lines 4–6.)

**KF derivation (§3.2.4, PDF 51–60, pp. 39–48) — the structure to remember:**
- *Prediction:* (3.7)–(3.10) write $\overline{bel}(x_t)=\eta\int\exp\{-L_t\}dx_{t-1}$ with $L_t$ quadratic in $x_{t-1}$ and $x_t$. Split (3.11) $L_t=L_t(x_{t-1},x_t)+L_t(x_t)$ so that the $x_{t-1}$ part is a complete Gaussian in $x_{t-1}$ whose integral (3.20)–(3.21) $=\det(2\pi\Psi_t)^{1/2}$ does not depend on $x_t$. Curvature (3.15): $\Psi_t^{-1}=A_t^TR_t^{-1}A_t+\Sigma_{t-1}^{-1}$; mean (3.17). Remaining $L_t(x_t)$ (3.25) is quadratic in $x_t$; setting its gradient to zero with the inversion lemma (3.27) $R^{-1}-R^{-1}A(A^TR^{-1}A+\Sigma^{-1})^{-1}A^TR^{-1}=(R+A\Sigma A^T)^{-1}$ gives (3.30) $\bar\mu_t=A_t\mu_{t-1}+B_tu_t$, and the Hessian (3.31) $(R_t+A_t\Sigma_{t-1}A_t^T)^{-1}$ = $\bar\Sigma_t^{-1}$.
- *Correction:* (3.33)–(3.34) $J_t=\tfrac12(z_t-C_tx_t)^TQ_t^{-1}(z_t-C_tx_t)+\tfrac12(x_t-\bar\mu_t)^T\bar\Sigma_t^{-1}(x_t-\bar\mu_t)$. Hessian (3.36)/(3.37): $\Sigma_t=(C_t^TQ_t^{-1}C_t+\bar\Sigma_t^{-1})^{-1}$ (information form!). Zero gradient (3.38)–(3.41) ⇒ define (3.42) $K_t=\Sigma_tC_t^TQ_t^{-1}$, giving (3.43) $\mu_t=\bar\mu_t+K_t(z_t-C_t\bar\mu_t)$. Algebra (3.44) rewrites $K_t=\bar\Sigma_tC_t^T(C_t\bar\Sigma_tC_t^T+Q_t)^{-1}$ (no $\Sigma_t$ needed). Inversion lemma again (3.45)–(3.46) ⇒ $\Sigma_t=(I-K_tC_t)\bar\Sigma_t$ — avoids inverting the n×n state covariance.
- **Inversion lemma, Table 3.2 (PDF 56, p. 44):** $(R+PQP^T)^{-1}=R^{-1}-R^{-1}P(Q^{-1}+P^TR^{-1}P)^{-1}P^TR^{-1}$ (Sherman–Morrison–Woodbury), with a proof by multiplying out. Lesson F4/F5 should include it.
- Key insight for students: **KF = minimize a sum of two Mahalanobis quadratics** (prior + measurement); mean = argmin, covariance = inverse Hessian. This is exactly a Gauss–Newton/weighted least-squares step (links F4, F6).

**EKF (§3.3, PDF 60–66, pp. 48–54):**
- Model (3.47)/(3.48): $x_t=g(u_t,x_{t-1})+\varepsilon_t$, $z_t=h(x_t)+\delta_t$. Belief is then *not* Gaussian; EKF approximates it by one.
- Linearize (3.49)–(3.50): $g(u_t,x_{t-1})\approx g(u_t,\mu_{t-1})+G_t(x_{t-1}-\mu_{t-1})$, $G_t=\partial g/\partial x_{t-1}$ at $(u_t,\mu_{t-1})$ (n×n). (3.52): $h(x_t)\approx h(\bar\mu_t)+H_t(x_t-\bar\mu_t)$, $H_t=\partial h/\partial x_t$ at $\bar\mu_t$ (k×n). Note: **$G_t$ linearizes at the previous posterior mean, $H_t$ at the predicted mean.**
- **Table 3.3 EKF (PDF 63, p. 51):**
```
Algorithm Extended_Kalman_filter(μ_{t-1}, Σ_{t-1}, u_t, z_t):
 2: μ̄_t = g(u_t, μ_{t-1})
 3: Σ̄_t = G_t Σ_{t-1} G_tᵀ + R_t
 4: K_t = Σ̄_t H_tᵀ (H_t Σ̄_t H_tᵀ + Q_t)⁻¹
 5: μ_t = μ̄_t + K_t (z_t − h(μ̄_t))
 6: Σ_t = (I − K_t H_t) Σ̄t
 7: return μ_t, Σ_t
```
  KF→EKF swap table (p. 51): $A\mu+Bu\to g(u,\mu)$, $C\bar\mu\to h(\bar\mu)$, $A,B\to G$, $C\to H$.
- Derivation sketch (3.54)–(3.63) parallels KF with $\Phi_t=(G_t^TR_t^{-1}G_t+\Sigma_{t-1}^{-1})^{-1}$ (3.57). **Typo in draft (3.63):** gain printed as $\bar\Sigma_tH_t^T(H_t\bar\Sigma_{t-1}H_t^T+Q_t)^{-1}$ — the $\bar\Sigma_{t-1}$ should be $\bar\Sigma_t$ (Table 3.3 Line 4 is correct).
- Practical (§3.3.4, pp. 53–54): EKF = "single guess + uncertainty ellipse"; most popular estimator; robust in practice even when assumptions are violated. Multi-modal extension: mixture of Gaussians (3.64) $bel(x)=\sum_j a_j\mathcal N(x;\mu_{j,t},\Sigma_{j,t})$, $a_j\ge0,\sum a_j=1$ → **multi-hypothesis EKF (MHEKF)** (relevant to the ASBR "multiple hypotheses" week). Linearization quality depends on (i) degree of nonlinearity and (ii) **width of the belief** — keep uncertainty small. EKF does *not* preserve true mean/covariance; UKF and moment matching do better (p. 54).

**Information filter (§3.4, PDF 67–75, pp. 55–63):**
- Canonical form (3.65)/(3.66): $\Omega=\Sigma^{-1}$ (information/precision matrix), $\xi=\Sigma^{-1}\mu$; back: $\Sigma=\Omega^{-1}$, $\mu=\Omega^{-1}\xi$ (3.67)/(3.68). Gaussian (3.72): $p(x)=\eta\exp\{-\tfrac12x^T\Omega x+x^T\xi\}$; (3.73) $-\log p(x)=\text{const}+\tfrac12x^T\Omega x-x^T\xi$ — quadratic, minimum at $\Omega^{-1}\xi$ (3.74). Ω-weighted quadratic distance = **Mahalanobis distance** (p. 57).
- **Table 3.4 Information filter (PDF 69, p. 57):**
```
Algorithm Information_filter(ξ_{t-1}, Ω_{t-1}, u_t, z_t):
 2: Ω̄_t = (A_t Ω_{t-1}⁻¹ A_tᵀ + R_t)⁻¹
 3: ξ̄_t = Ω̄_t (A_t Ω_{t-1}⁻¹ ξ_{t-1} + B_t u_t)
 4: Ω_t = C_tᵀ Q_t⁻¹ C_t + Ω̄_t
 5: ξ_t = C_tᵀ Q_t⁻¹ z_t + ξ̄_t
 6: return ξ_t, Ω_t
```
- Duality (p. 58): IF prediction needs two n×n inversions ($O(n^{2.8})$); IF measurement update is *additive* ($O(n^2)$, cheaper if the measurement touches few variables). KF: the reverse. Derivation (3.77)–(3.85): substitute (3.79) into KF prediction; measurement update by multiplying out the exponent and reading off quadratic/linear terms.
- **Table 3.5 Extended information filter (PDF 72, p. 60):**
```
 2: μ_{t-1} = Ω_{t-1}⁻¹ ξ_{t-1}
 3: Ω̄_t = (G_t Ω_{t-1}⁻¹ G_tᵀ + R_t)⁻¹
 4: ξ̄_t = Ω̄_t g(u_t, μ_{t-1})
 5: μ̄_t = g(u_t, μ_{t-1})
 6: Ω_t = Ω̄_t + H_tᵀ Q_t⁻¹ H_t
 7: ξ_t = ξ̄_t + H_tᵀ Q_t⁻¹ [z_t − h(μ̄_t) − H_t μ̄_t]     ← SIGN ERROR in draft
```
  **Draft error (Table 3.5 Line 7 and Eqs. 3.97–3.100):** the linearized measurement is $h(\bar\mu)+H(x-\bar\mu)=Hx+(h(\bar\mu)-H\bar\mu)$, so the linear term is $H^TQ^{-1}[z_t-h(\bar\mu_t)+H_t\bar\mu_t]$. The 2005 book (Table 3.6) has the correct **$+H_t\bar\mu_t$**. Sanity check: if $h$ is linear, $h(\bar\mu)=H\bar\mu$ and the bracket must reduce to $z_t$ (as in Table 3.4) — only "+" does that.
- Practical (§3.4.6, pp. 62–63): global uncertainty is trivial ($\Omega=0$); numerically more stable; natural for **decentralized multi-robot fusion** (information adds, order-independent). Downsides: nonlinear EIF must recover $\mu=\Omega^{-1}\xi$ each step (inversion); prediction needs inversions. But **Ω is often sparse when Σ is dense** → Gaussian Markov random fields; sets up SEIF/GraphSLAM (Ch 11–12).

**Summary (§3.5, pp. 64–65):** three assumptions (Gaussian prior, linear-Gaussian motion, linear-Gaussian measurement) ⇒ exact; "extended" = Taylor/Jacobian linearization; accuracy depends on nonlinearity × posterior width; polynomial time; unimodal limitation.

**Worked examples worth redoing:** Fig 3.2 1-D KF (choose numbers: prior N(10,4), z=14 with var 4 ⇒ N(12,2); move +10 with var 3 ⇒ N(22,5) …). Derive Lines 4–6 yourself from $J_t$ (3.34) — this *is* the A5 derivation exam question.

**Exercises:** draft has none for Ch 3. Suggested (for Assignment 4, GPS+IMU EKF): write $g$ for an IMU-driven planar/3D kinematic model, $h$ for GPS position ($H=[I\;0]$, linear), derive $G_t$; implement Table 3.3; compare with Table 3.4/3.5 to see duality.

**Conventions / pitfalls:**
- $R$ = motion noise, $Q$ = measurement noise (Thrun). Check every slide/assignment for the swap.
- $\Sigma_t=(I-KH)\bar\Sigma$ can lose symmetry/PSD numerically; Joseph form $(I-KH)\bar\Sigma(I-KH)^T+KQK^T$ is the safe variant (not in the book — add to A5).
- EKF does not propagate the control noise separately here: $R_t$ is in *state* space. In Ch 7 (EKF localization) control-space noise $M_t$ is mapped by $V_tM_tV_t^T$.
- Angle states: innovation $z-h(\bar\mu)$ must have bearings wrapped to $(-\pi,\pi]$ (not stated in Ch 3; see Ch 7).

## Ch 4 Nonparametric Filters (PDF pp. 79–102, book pp. 67–90) — Lessons: A6 (core), A5 (histogram), F8
**Big idea:** Drop the Gaussian assumption; represent the belief with a *finite, adjustable* number of numbers.
Either chop the state space into cells (histogram / grid filter: piecewise-constant density) or keep random
samples (particle filter: the density is where the particles are). Both handle multimodal beliefs and global
uncertainty; both converge to the true posterior as resolution/sample count → ∞; both cost more than a
Gaussian. Adaptive / resource-adaptive variants change the number of parameters online (p. 68).

### 4.1 Histogram filter (pp. 68–77)
- **Table 4.1 Discrete Bayes filter (PDF 80):** for each cell k: predicted mass $\bar p_{k,t}=\sum_i p(X_t=x_k|u_t,X_{t-1}=x_i)\,p_{i,t-1}$; corrected $p_{k,t}=\eta\,p(z_t|X_t=x_k)\,\bar p_{k,t}$. Same as Table 2.1 with the integral → sum. In HMM language this is the *forward pass* (p. 69).
- Continuous state: partition (4.1) into convex disjoint regions; piecewise-uniform density (4.2) $p(x_t)=p_{k,t}/|x_{k,t}|$.
- Probe each cell at its centroid (4.3) $\hat x_{k,t}$; approximations (4.4) $p(z_t|x_{k,t})\approx p(z_t|\hat x_{k,t})$ and (4.5)/(4.10) $p(x_{k,t}|u_t,x_{i,t-1})\approx\eta\,|x_{k,t}|\,p(\hat x_{k,t}|u_t,\hat x_{i,t-1})$; derivations (4.6)–(4.10) show these are exact under the piecewise-uniform model + "density constant over the cell". With equal-size cells the volume factor drops into η. Note $\bar p_k$ in Line 3 is not normalized; Line 4 normalizes.
- Decomposition (§4.1.3, pp. 73–74): **static** (fixed grid) vs **dynamic** (density trees, Fig 4.1 — resolution follows probability mass). **Selective updating**: update only cells above a probability threshold — makes 3-D+ grids feasible. Topological (graph of significant places) vs metric (regular grid, e.g. 10 cm cells).
- **Binary Bayes filter with static state, log odds (§4.1.4, pp. 74–77)** — the basis of occupancy grids (Ch 9):
  - Odds (4.12) $p(x)/(1-p(x))$; log odds (4.13) $l(x)=\log\frac{p(x)}{1-p(x)}\in(-\infty,\infty)$; avoids truncation near 0/1.
  - **Table 4.2**: $l_t=l_{t-1}+\log\frac{p(x|z_t)}{1-p(x|z_t)}-\log\frac{p(x)}{1-p(x)}$. Uses the **inverse measurement model** $p(x|z_t)$ (easier when z is complex, e.g. camera image → "door closed?").
  - Recovery (4.14): $bel_t(x)=1-\frac{1}{1+\exp\{l_t\}}$.
  - Derivation (4.15)–(4.20): Bayes on $p(z_t|x)$ inside the filter, write the same for ¬x, divide — the hard terms $p(z_t)$, $p(z_t|z_{1:t-1})$ cancel.
  - **Draft error (4.21):** initial log odds printed as $l_0=\log\frac{1-p(x)}{p(x)}$; it should be $l_0=\log\frac{p(x)}{1-p(x)}$ (prior log odds; 2005 book has it right).

### 4.2 Particle filter (pp. 77–89)
- Particles (4.22) $\mathcal X_t=\{x_t^{[1]},\dots,x_t^{[M]}\}$, typically M≈1000; ideally (4.23) $x_t^{[m]}\sim p(x_t|z_{1:t},u_{1:t})$ — holds only as M→∞ (fine for M ≥ 100, p. 78).
- **Table 4.3 Particle filter (PDF 90, p. 78), in brief:** for m=1..M: sample $x_t^{[m]}\sim p(x_t|u_t,x_{t-1}^{[m]})$ (motion model, *sampling* version); weight $w_t^{[m]}=p(z_t|x_t^{[m]})$ (measurement model, *density* version); add to temporary set $\bar{\mathcal X}_t$. Then resample M times with replacement, $\Pr(i)\propto w_t^{[i]}$, into $\mathcal X_t$.
  - The temporary weighted set represents $\overline{bel}$ (unweighted) / $bel$ (weighted); after resampling the unweighted set ≈ $bel$. Duplicates are normal (p. 79).
  - No-resampling alternative (4.24) $w_t=p(z_t|x_t)w_{t-1}$ — works but wastes particles in low-probability regions. Resampling = "survival of the fittest" (p. 80).
- **Importance sampling (§4.2.2, pp. 80–82, Fig 4.2):** target f, proposal g with $f>0\Rightarrow g>0$; weights (4.26) $w=f/g$; weighted empirical measure converges (4.27) to $\int_A f$ (convergence of the CDF, not the density); rate typically $O(1/\sqrt M)$. In the PF: target = $bel(x_t)$, proposal (4.28) = $p(x_t|u_t,x_{t-1})\,bel(x_{t-1})$ = $\overline{bel}$.
- **Derivation (§4.2.3, pp. 82–84):** treat particles as whole trajectories $x_{0:t}$ (4.29)/(4.30). (4.31) $p(x_{0:t}|z_{1:t},u_{1:t})=\eta\,p(z_t|x_t)\,p(x_t|x_{t-1},u_t)\,p(x_{0:t-1}|z_{1:t-1},u_{1:t-1})$ — no integral because nothing is marginalized. Proposal (4.32) = motion × previous path posterior, so weight (4.33) = target/proposal = $\eta\,p(z_t|x_t)$; resampling ∝ w yields (4.34) samples of $bel(x_0:t)$; marginal on $x_t$ is trivial. (Text says "Table 4.2" — means Table 4.3; and (4.32) has $z_{0:t-1}$, $u_{0:t-1}$ typos for $1{:}t-1$.)
- **Four error sources (§4.2.4, pp. 84–89) — exam-grade list:**
  1. *Finite M bias:* with M=1 resampling always keeps the one particle ⇒ samples $p(x_t|u_{1:t})$ (4.35), measurements ignored. Cause: normalization removes one degree of freedom (4.36). Fades with large M.
  2. *Resampling variance / loss of diversity:* static state + no sensors ⇒ eventually M copies of one state. Remedies: (a) **resample less often** — never when the state is static (robot stopped: also pause measurement integration); keep weights multiplicatively between resamplings (4.37); decide by **variance of the weights** (all equal ⇒ don't resample). (b) **Low-variance sampling** (Table 4.4).
  3. *Proposal–target mismatch:* proposal ignores $z_t$. Accurate sensor + sloppy motion ⇒ few particles land in the narrow likelihood; deterministic sensors ⇒ all weights 0. Fixes: **inflate the measurement noise** deliberately (works in practice), or use measurement-aware proposals (Ch 8 §8.3.4).
  4. *Particle deprivation:* unlucky resampling wipes out particles near the truth (probability > 0 each step, so eventually it happens). Fix of last resort: **inject a few random particles** after each resampling (biased posterior). Number of particles grows with dimension and with uncertainty (uniform ⇒ many).
- **Table 4.4 Low-variance (systematic) sampler (PDF 98, p. 86), paraphrased:** draw one $r\sim U[0,1/M)$; set $c=w^{[1]}$, $i=1$; for m=1..M: $U=r+(m-1)/M$; while $U>c$: $i\leftarrow i+1$, $c\leftarrow c+w^{[i]}$; add $x^{[i]}$. Selected index per (4.38) is the first i whose cumulative weight ≥ U (Fig 4.3). **Assumes normalized weights** (sum 1) — normalize first or scale the comb by the total weight. Benefits: systematic coverage; equal weights ⇒ set unchanged (no diversity lost if resampling without new info); **O(M)** vs O(M log M) for independent sampling via binary search.
- Stratified sampling mentioned as an alternative (good for multiple hypotheses in one PF, p. 88).

**Worked examples worth redoing:** Fig 4.2 (importance weights f/g); M=1 thought experiment; static no-sensor diversity-collapse thought experiment — simulate both in NumPy (F1/A6).

**Exercises:** none printed in draft Ch 4. Suggested: implement Table 4.3 + Table 4.4 for 1-D corridor; compare multinomial vs low-variance resampling variance over 1000 runs; implement log-odds update for a single cell.

**Conventions / pitfalls:**
- Resampling needs weights normalized; keep log-weights for numerical stability with many beams (product of 180 likelihoods underflows).
- The sampling motion model is used in the PF (Line 4), the *density* measurement model in Line 5 — Ch 5/6 give both forms.
- Effective sample size $N_{\text{eff}}=1/\sum w^2$ is the standard concrete "variance of weights" test (not named in the draft; add to A6).

## Ch 5 Robot Motion (PDF pp. 103–132, book pp. 91–120) — Lessons: A6 (MCL prediction), A5 (EKF g and G), F3 (planar poses)
**Big idea:** The motion model $p(x_t|u_t,x_{t-1})$ is deterministic planar kinematics "probabilified" by
adding noise to the *control parameters* (not directly to x, y, θ). Each model comes in two flavours:
a **density** version (give it $x_{t-1},u_t,x_t$, it inverts the kinematics to find which noisy control would
explain the move and returns that control's probability) and a **sampling** version (forward-simulate with
noisy controls — what particle filters need, and much easier). Practical lesson: models that *over*estimate
noise are more robust to Markov violations (p. 92).

**Key definitions (§5.2, pp. 92–94):**
- Planar pose (5.1) $(x,y,\theta)^T$; θ=0 points along +x, θ=π/2 along +y (Fig 5.1). "Location" = (x,y) without heading (5.2).
- Motion model (5.3) $p(x_t|u_t,x_{t-1})$ with $x$'s full poses (Fig 5.2: banana-shaped clouds).
- Odometry models are usually more accurate (wheel encoders vs. imperfectly tracked velocity commands) but only available **after** motion ⇒ odometry for **estimation**, velocity for **planning/prediction** (p. 95).

### 5.3 Velocity motion model (pp. 95–107)
- Control (5.4) $u_t=(v_t,\omega_t)^T$, ω>0 = counter-clockwise (left turn). Covers diff-drive, Ackermann, synchro-drive; not Mecanum/legged.
- **Exact motion (5.5)–(5.9):** constant $(v,\omega)$ for Δt ⇒ circular arc of radius $r=|v/\omega|$ ($v=\omega r$, 5.6); center (5.7)/(5.8) $x_c=x-\frac v\omega\sin\theta$, $y_c=y+\frac v\omega\cos\theta$;
  $$x'=x-\tfrac v\omega\sin\theta+\tfrac v\omega\sin(\theta+\omega\Delta t),\quad y'=y+\tfrac v\omega\cos\theta-\tfrac v\omega\cos(\theta+\omega\Delta t),\quad \theta'=\theta+\omega\Delta t\;(5.9)$$
  (ω=0 ⇒ straight line, handle separately: $x'=x+v\Delta t\cos\theta$, $y'=y+v\Delta t\sin\theta$ — not written in the book but needed in code).
- **Noise (5.10):** $\hat v=v+\varepsilon_{\alpha_1|v|+\alpha_2|\omega|}$, $\hat\omega=\omega+\varepsilon_{\alpha_3|v|+\alpha_4|\omega|}$, where $\varepsilon_b$ = zero-mean noise **with variance b**. Error distributions: normal (5.11) or triangular (5.12) with support $|a|\le\sqrt{6b}$, peak $1/\sqrt{6b}$ (Fig 5.6, Table 5.2).
- **Degeneracy & final rotation (pp. 103):** with only 2 noise variables the posterior lives on a 2-D manifold in 3-D pose space (every sample lies exactly on some circular arc) ⇒ density is zero almost everywhere. Fix: extra final rotation (5.14)/(5.15) $\theta'=\theta+\hat\omega\Delta t+\hat\gamma\Delta t$, $\hat\gamma=\varepsilon_{\alpha_5|v|+\alpha_6|\omega|}$ ⇒ full model (5.16).
- **Density version (Table 5.1, PDF 109; derivation (5.17)–(5.32), pp. 103–106):** find the circle through $(x,y)$ tangent to the initial heading and through $(x',y')$: center on the perpendicular to the heading through $(x,y)$ *and* on the perpendicular bisector of the chord ⇒ (5.18) $\mu=\tfrac12\frac{(x-x')\cos\theta+(y-y')\sin\theta}{(y-y')\cos\theta-(x-x')\sin\theta}$, $x^*=\frac{x+x'}2+\mu(y-y')$, $y^*=\frac{y+y'}2+\mu(x'-x)$ (5.19); radius $r^*$ (5.20); swept angle $\Delta\theta=\text{atan2}(y'-y^*,x'-x^*)-\text{atan2}(y-y^*,x-x^*)$ (5.21); $\hat v=r^*\Delta\theta/\Delta t$, $\hat\omega=\Delta\theta/\Delta t$ (5.23)/(5.24); $\hat\gamma=(\theta'-\theta)/\Delta t-\hat\omega$ (5.25). Return the product (5.32) of the three error densities evaluated at $v-\hat v$, $\omega-\hat\omega$, $\hat\gamma$ with the variances above (independence assumed). atan2 definition (5.22).
- **Sampling version (Table 5.3, PDF 110):** perturb $v,\omega$ with samples of the given variances, draw $\hat\gamma$, then apply (5.16). Fig 5.4: 500 samples ≈ Fig 5.3 densities. Sampling only needs the *forward* model — the reason PFs are easy (p. 99).
- **Noise samplers (Table 5.4):** normal by summing 12 uniforms (central-limit trick); triangular from the product of two uniforms. **Draft errors:** as printed, $\frac b6\sum_{i=1}^{12}\text{rand}(-1,1)$ has variance $b^2/9$ and $b\cdot\text{rand}\cdot\text{rand}$ is not triangular with variance b. The 2005 book fixes this by treating b as a *standard deviation*: $\tfrac12\sum_{i=1}^{12}\text{rand}(-b,b)$ (var $b^2$) and $\tfrac{\sqrt6}2[\text{rand}(-b,b)+\text{rand}(-b,b)]$ (triangular, var $b^2$). In NumPy just use `rng.normal(0, sqrt(var))`.
- **Variance parameterization differs by edition:** draft uses variances linear in $|v|,|\omega|$ ($\alpha_1|v|+\alpha_2|\omega|$); the 2005 book uses $\alpha_1v^2+\alpha_2\omega^2$ (i.e. std ∝ speed). Check which one the ASBR assignment template uses.

### 5.4 Odometry motion model (pp. 107–114)
- Odometry is a *measurement*, but treated as control to avoid putting velocities in the state (p. 108). $u_t=(\bar x_{t-1},\bar x_t)$ (5.33): two consecutive odometry poses in the robot's drifting internal frame — only their **relative** motion is used.
- **Decomposition (Fig 5.7, (5.34)–(5.36)):** $\delta_{rot1}=\text{atan2}(\bar y'-\bar y,\bar x'-\bar x)-\bar\theta$; $\delta_{trans}=\sqrt{(\bar x'-\bar x)^2+(\bar y'-\bar y)^2}$; $\delta_{rot2}=\bar\theta'-\bar\theta-\delta_{rot1}$. Three parameters ⇒ no degeneracy (no extra "final rotation" needed).
- **Noise (5.37)–(5.39):** true = measured − noise, with variances $\alpha_1|\delta_{rot1}|+\alpha_2\delta_{trans}$ (rot1), $\alpha_3\delta_{trans}+\alpha_4|\delta_{rot1}+\delta_{rot2}|$ (trans), $\alpha_1|\delta_{rot2}|+\alpha_2\delta_{trans}$ (rot2). Pose update (5.40): $x'=x+\hat\delta_{trans}\cos(\theta+\hat\delta_{rot1})$, $y'=y+\hat\delta_{trans}\sin(\theta+\hat\delta_{rot1})$, $\theta'=\theta+\hat\delta_{rot1}+\hat\delta_{rot2}$. (The printed third row "θ + δ̂rot1 + δ̂rot2" in the *increment* vector double-counts θ — a typo.)
- **Density version (Table 5.5, PDF 120):** compute the δ's from odometry and the $\hat\delta$'s from the hypothesized $x_{t-1}\to x_t$; return the product of the three error densities (5.44)–(5.46). **Wrap angular differences to [−π, π]** — the book calls missing this "a common error" that causes occasional divergence (p. 110).
- **Sampling version (Table 5.6, PDF 122):** compute δ's, subtract sampled noise, apply (5.40). This is exactly the **MCL prediction step for ASBR A6 / Assignment 5**.
- Draft typos/quirks: $\delta_{trans}$ printed with "…)s" instead of squared; in Table 5.5 the variance arguments lack absolute values (use |·|, variances must be ≥0); the 2005 book uses $\alpha_1\delta_{rot1}^2+\alpha_2\delta_{trans}^2$, $\alpha_3\delta_{trans}^2+\alpha_4(\delta_{rot1}^2+\delta_{rot2}^2)$ — the draft's $|\delta_{rot1}+\delta_{rot2}|$ lets opposite turns cancel, which the later edition avoids. Note also Table 5.5 compares $\delta$ (odometry) with $\hat\delta$ (hypothesis) while (5.41)–(5.46) write $\bar\delta$ — same thing, inconsistent notation.
- Fig 5.8/5.9 densities vs samples; with frequent updates (~10 Hz) velocity and odometry models are nearly identical (p. 111). **Fig 5.10:** dead-reckoning sample clouds grow without bound — motion alone only loses information.

### 5.5 Motion and maps (pp. 114–117)
- Map-aware model (5.47) $p(x_t|u_t,x_{t-1},m)\ne p(x_t|u_t,x_{t-1})$. Approximation (5.48): $p(x_t|u_t,x_{t-1},m)=\eta\,p(x_t|u_t,x_{t-1})\,p(x_t|m)$, with $p(x_t|m)=0$ if the pose collides with an occupied cell (obstacle grown by robot radius = C-space obstacle, ties to **A3**) and constant otherwise.
- Table 5.7: density = product; sampling = rejection-sample until $p(x_t|m)>0$ and return the weighted sample $\langle x_t,\pi\rangle$ (guard the loop's termination).
- Derivation (5.49)–(5.51) needs the crude assumption $p(m|x_t,u_t,x_{t-1})=p(m|x_t)$: only the *final* pose is checked, so a robot can "tunnel through walls" (region (*) in Fig 5.11). Harmless when updates are frequent and noise is bounded ⇒ **update frequency matters** (p. 117).

**Summary (§5.6):** two models × two implementations (closed-form = inverse model; sampling = forward model) + map extension. Not covered: holonomic drives, dynamics (would add velocity to the state).

**Worked examples worth redoing:** reproduce Figs 5.4/5.9/5.10 in NumPy (500 samples per step); verify Table 5.1 by sampling 10⁶ poses with Table 5.3 and histogramming against the density.

**Exercises:** none printed. For A6: implement `sample_motion_model_odometry` vectorized over particles; for A5: derive the EKF Jacobian $G_t$ of (5.9) (done in Ch 7 eq. 7.8).

**Conventions / pitfalls:** $\varepsilon_b$ is parameterized by **variance** b (draft). Wrap every angle difference. Handle ω≈0 in the velocity model. Odometry frame ≠ world frame — only use differences.

## Ch 6 Measurements (PDF pp. 133–168, book pp. 121–156) — Lessons: A6 (likelihood field = Assignment 5), A5 (landmark h and H), F8 (mixtures, MLE, EM)
**Big idea:** $p(z_t|x_t,m)$ is a *generative* story of how a scan arises from a pose and a map, with explicit
terms for every way a sensor goes wrong. Physical realism is not the only goal: a model must also be
**smooth in the pose** and **cheap**, or the filter that uses it suffers. Because real beams are correlated
and the model ignores that, every practical model deliberately *under-trusts* its sensor.

**Setup (§6.1–6.2, pp. 121–124):**
- Sonar pathologies (Fig 6.1): specular reflection ⇒ too-long/max readings; cross-talk and unmodeled people ⇒ short readings.
- Scan (6.1) $z_t=\{z_t^1,\dots,z_t^K\}$; **beam independence** (6.2) $p(z_t|x_t,m)=\prod_k p(z_t^k|x_t,m)$ — an approximation (people, map errors, approximations correlate beams).
- Map (6.3) $m=\{m_1,\dots,m_N\}$: **feature-based** (index = feature, stores its location; easy to adjust, popular in SLAM) vs **location-based / volumetric** ($m_{x,y}$; also encodes free space; occupancy grid = canonical example, good for path planning).

### 6.3 Beam model (pp. 124–139)
- Mixture of four components (Fig 6.2, 6.3), with $z^{k*}_t$ = expected range by **ray casting** in the map:
  1. $p_{hit}$ (6.4)–(6.6): Gaussian $\mathcal N(z;z^{k*},\sigma_{hit}^2)$ truncated to $[0,z_{max}]$ and renormalized.
  2. $p_{short}$ (6.7)–(6.10): exponential $\lambda_{short}e^{-\lambda_{short}z}$ on $[0,z^{k*}]$, normalizer $1/(1-e^{-\lambda_{short}z^{k*}})$. Why exponential: the nearest of several independent unexpected objects is what gets seen, so short ranges are more likely (p. 126).
  3. $p_{max}$ (6.11): point mass $I(z=z_{max})$ for failures (black/absorbing surfaces, sunlight, specular sonar).
  4. $p_{rand}$ (6.12): uniform $1/z_{max}$ on $[0,z_{max})$.
  - (6.13): $p(z^k|x,m)=z_{hit}p_{hit}+z_{short}p_{short}+z_{max}p_{max}+z_{rand}p_{rand}$, weights sum to 1.
- **Table 6.1 (PDF 141), in words:** for each beam, ray-cast $z^{k*}$, evaluate the 4-term mixture, multiply into q; return q.
- **Learning the intrinsic parameters Θ = {z_hit, z_short, z_max, z_rand, σ_hit, λ_short} (§6.3.2, pp. 129–134; Table 6.2 PDF 143):** maximize (6.14) $p(Z|X,m,\Theta)$ over a logged dataset with known poses. Fig 6.4: 10,000 sonar vs laser readings at true range 3 m (sonar: many max-range readings). Algorithm = **EM**: E-step gives responsibilities $e_{i,c}$ = normalized component likelihoods; M-step: mixture weights = mean responsibilities (6.30); $\sigma_{hit}=\sqrt{\sum e_{i,hit}(z_i-z_i^*)^2/\sum e_{i,hit}}$ (6.31); $\lambda_{short}=\sum e_{i,short}/\sum e_{i,short}z_i$ (6.32). A dozen iterations typically suffice.
  - Derivation (§6.3.3, pp. 134–138): with known correspondence labels $c_i$: weights = class fractions (6.15); Gaussian MLE (6.16)–(6.20) $\sigma_{hit}^2=\frac1{|Z_{hit}|}\sum(z_i-z_i^*)^2$ (log-likelihood → derivative → zero); exponential MLE (6.21)–(6.24) $\lambda=|Z_{short}|/\sum z_i$. Unknown $c_i$ ⇒ maximize expected log-likelihood (6.25)–(6.29) ⇒ EM. **Great F8 MLE worked example.**
  - **Pitfall in draft Table 6.2 / (6.28)–(6.29):** responsibilities are computed without the mixture weights; a textbook EM E-step uses $e_{i,c}\propto z_c\,p_c(z_i|x_i,m)$. Use the weighted version.
- Fig 6.5: learned models — narrower Gaussians at short range; laser ≫ sonar. Large short/rand weights reduce information per beam but buy **robustness** to people and other unmodeled effects. Fig 6.6: one 180° scan gives a likelihood spread along a symmetric corridor (two bands = two headings) — one scan is not enough to localize.
- **Practical (§6.3.4, pp. 138–139):** use a **subset of beams** (e.g. 8 of 360) — faster *and* less correlated; temper the likelihood $p(z^k|x,m)^\alpha$, α<1 (6.3.4, recommended again in §6.7 over feature reduction); tune Θ for multi-step localization performance (gradient descent) rather than single-step ML. Precompute ray casts on a 3-D pose grid (15 cm × 2° works indoors) ⇒ ~10× speed-up, big memory.

### 6.4 Likelihood field ("end-point") model (pp. 139–145) — **the Assignment 5 model**
- Motivation: the beam model is **not smooth** in pose (clutter like chair legs; especially in θ) ⇒ PF/hill-climbing trouble; ray casting is **expensive** and its tables are 3-D.
- The model is admittedly ad hoc ("no plausible physical explanation") but smooth and fast (p. 140).
- **Endpoint projection (6.33):** sensor mounted at $(x_{k,sens},y_{k,sens})$ with beam angle $\theta_{k,sens}$ in the robot frame:
  $$\begin{pmatrix}x_{z^k}\\y_{z^k}\end{pmatrix}=\begin{pmatrix}x\\y\end{pmatrix}+R(\theta)\begin{pmatrix}x_{k,sens}\\y_{k,sens}\end{pmatrix}+z^k\begin{pmatrix}\cos(\theta+\theta_{k,sens})\\\sin(\theta+\theta_{k,sens})\end{pmatrix}$$
  (a planar rigid transform — F3/C1). **Max-range readings are discarded.**
- Noise: $p_{hit}$ (6.34) = zero-mean Gaussian in **dist**, the Euclidean distance from the endpoint to the nearest occupied map cell (Fig 6.7, 6.8); plus $p_{max}$ and $p_{rand}$. Mixture (6.35) $z_{hit}p_{hit}+z_{rand}p_{rand}+z_{max}p_{max}$. No $p_{short}$.
- **Table 6.3 (PDF 155), in words:** for each non-max beam, project the endpoint with (6.33), look up the squared distance to the nearest obstacle, multiply q by $z_{hit}\cdot\mathcal N(\text{dist};0,\sigma_{hit}^2)+z_{rand}/z_{max}$.
  - **Notation pitfall:** the draft writes prob(dist², σ²_hit); evaluate the Gaussian as $\exp(-\text{dist}^2/2\sigma^2)/\sqrt{2\pi\sigma^2}$ — i.e. a Gaussian *in dist* — not a Gaussian of the argument dist² (which would give $\exp(-\text{dist}^4/\dots)$). The 2005 book writes prob(dist, σ_hit).
- **Precompute the likelihood field** (distance transform of the occupancy grid, Fig 6.9 Tech Museum) ⇒ evaluation = transform + table lookup; only 2-D storage.
- Downsides (p. 144): no model of short readings (people); **"sees through walls"** (nearest neighbour ignores occlusion); no map uncertainty. Extension: cells *unknown* ⇒ constant $1/z_{max}$ likelihood. Symmetric scan↔map matching (Fig 6.10) is what the best implementations use.
- Practical note for A6: work in **log-likelihood**, sum over beams, and subtract the max before exponentiating weights; temper (α<1) or subsample beams.

### 6.5 Correlation-based (map matching) (pp. 145–147)
- Build a local map from a few scans (Fig 6.11), transform into global frame at pose $x_t$, correlation (6.36) with mean (6.37); $p(m_{local}|x_t,m)=\max(\rho,0)$ (6.38). Uses free space (unlike likelihood field) but not smooth unless the map is Gaussian-blurred; may "see through walls" via 0.5-filled regions; no physical noise model.

### 6.6 Feature-based models (pp. 147–153)
- Feature extractor $f(z_t)$; features (6.39) = range, bearing, signature triplets $(r_t^i,\phi_t^i,s_t^i)$; conditional independence across features (6.40).
- **Range–bearing landmark model (6.41)** for feature i ↔ landmark j, pose $(x,y,\theta)$:
  $r=\sqrt{(m_{j,x}-x)^2+(m_{j,y}-y)^2}+\varepsilon_{\sigma_r^2}$, $\phi=\text{atan2}(m_{j,y}-y,\,m_{j,x}-x)-\theta+\varepsilon_{\sigma_\phi^2}$, $s=s_j+\varepsilon_{\sigma_s^2}$. This is the EKF $h$ of Ch 7.
- **Correspondence variable** $c_t^i\in\{1,\dots,N+1\}$; $N+1$ = "not in map" (spurious features / new landmarks in SLAM).
- **Table 6.4 (PDF 162):** product of three Gaussian densities on range error, bearing error, signature error. (Bug in draft: line 4 omits the "−θ" from $\hat\phi$; (6.41) has it. Also wrap the bearing error.)
- **Sampling poses from a landmark observation (§6.6.4, Table 6.5, PDF 163):** with a uniform prior (6.42) the pose posterior ∝ the likelihood; a range+bearing fixes 2 of 3 DOF, so sample the free angle $\hat\gamma\sim U[0,2\pi)$, perturb range and bearing, set $x=m_{j,x}+\hat r\cos\hat\gamma$, $y=m_{j,y}+\hat r\sin\hat\gamma$, $\theta=\hat\gamma-\pi-\hat\phi$. Result: a ring in x-y, a spiral in (x,y,θ) (Fig 6.12, 5 m / 30°). Used for sensor-based particle injection in MCL (Ch 8).
- §6.6.5: features are *not* a sufficient statistic (6.43) — information is lost (data association, global localization get harder); dense scan methods dominate with fast computers.

**Practical (§6.7, pp. 153–154):** choose models for what the *algorithm* needs (smoothness), not just realism; **inflate uncertainty** because ignored dependencies make the filter overconfident; exponent α is the better way to reduce information than projecting to features.

**Worked examples worth redoing:** Fig 6.4–6.5 EM fit on synthetic data (generate from known Θ, recover it); Fig 6.7–6.8 likelihood along one beam; Table 6.5 pose sampling.

**Exercises:** none printed. For A6: implement distance-transform likelihood field (scipy.ndimage.distance_transform_edt) + Table 6.3 vectorized over particles × beams.

**Conventions / pitfalls:** $\varepsilon_{\sigma^2}$ is variance-parameterized; Thrun's measurement noise matrix later is $Q_t=\text{diag}(\sigma_r^2,\sigma_\phi^2,\sigma_s^2)$. Bearing is relative to robot heading (subtract θ) and must be wrapped. Skip max-range beams in the likelihood field.

