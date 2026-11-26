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

