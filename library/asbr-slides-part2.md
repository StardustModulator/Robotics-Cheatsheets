# JHU ASBR (EN.601.463/663) course material — Part 2: KF/EKF, Bayesian filtering/PF, review deck, course page, student's handwritten notes
Source: uploads `c7d456ff-week08.pdf` (49 pp), `fd570829-week09.pdf` (55 pp), `2e63680d-review.pdf` (24 pp), `e53b0db3-Algorithms_for_Sensor-Based_Robotics___CIRL.pdf` (6 pp), `6a4988ed-algorithm.pdf` (29 pp, handwritten).
All pages were read visually (slide images). Slide footers read "600.436/600.636, G.D. Hager / S. Leonard" (old course numbers of the same course); many sensor/Bayes slides are credited "16-735, Howie Choset with slides from G.D. Hager and Z. Dodds".

---

## Week 8 deck: "Kalman Filters for Mapping and Localization" (PDF pp. 1–49) — Lessons: A5 (main), F8, F5, A6 (motivation)
Source: c7d456ff-week08.pdf, PDF pp. 1–49 (one slide per page)

**Big idea:** A robot never knows its state exactly: joint encoders on an industrial arm are very accurate, but a wheeled robot integrates velocity and drifts (slip, wheel diameter, alignment). Sensors are the link to the world, and every localization / mapping / SLAM method is "updating a state and its uncertainty". If the models are linear and the noise Gaussian, the belief stays Gaussian forever ("we stay in the Gaussian world"), so the whole belief is a mean and a covariance and the optimal recursive update is the Kalman filter. If the models are nonlinear, linearize them around the current estimate (EKF). Putting the landmarks into the state turns EKF localization into EKF-SLAM.

### Slide-by-slide map
| PDF p. | Content |
|---|---|
| 1 | Title |
| 2 | Robot arms: very accurate (encoders 0.04 deg, ISO 9283 accuracy/repeatability, ABB IRB position-accuracy table ~0.3–0.75 mm avg; FARO arm as metrology instrument) |
| 3 | Mobile robots: error sources = wheel slip, wheel diameter, wheel alignment, steering alignment, integration of velocity; figure: ideal vs real trajectory, position drift + orientation drift |
| 4–5 | Sensor zoo: laser, IR, sonar, Kinect, light-field camera; gyro, GPS, F/T, IMU (gyro+accelerometer), compass. "Sensors, sensors, sensors! and tracking what is sensed: world models" |
| 6–8 | IR sensors: (1) intensity-based "object sensing", (2) angle-based (triangulation) diffuse distance sensing; calibration surfaces: response depends on ambient light (dark/fluorescent/incandescent) and material (black fabric vs white plastic) |
| 9–11 | Sonar: chirp at 0, ~75 µs blanking (reverberation), listen until ~0.5 s; effects (a) good range, (b–c) poor lateral resolution (closest object in cone answers), (d) specular reflections make walls disappear, (e) open corners weak, (f) closed corners measure to the corner (multiple reflections → "sonar ray tracing"); spatial response lobe ~15 deg; accumulated sonar map |
| 12 | Simulated sonar cones (RViz-style) |
| 13–15 | Laser ranging (SICK, Google car, Velodyne 64 lasers), structured light (Kinect dot pattern), depth from focus, depth from stereo |
| 16 | **The Problem**: Mapping (assumes perfect position), Localization (assumes perfect map), SLAM ("How can you localize without a map? map without localization?"). "All localization, mapping or SLAM methods are based on updating a state: what makes a state? how certain is it?" |
| 17 | Representations for Bayesian robot localization: Kalman filters (late 80s: Gaussians, approx. linear models, position tracking), discrete approaches ('95 topological/POMDP, '96 grid-based metric: global localization, recovery), particle filters ('99: sample-based, global localization, recovery), multi-hypothesis ('00: multiple KFs, global localization, recovery) |
| 18 | Gaussian pdf, univariate and multivariate (formulas below) |
| 19 | Properties: linear map of a Gaussian is Gaussian; sum of independent Gaussians is Gaussian. "We stay in the Gaussian world as long as we start with Gaussians and perform only linear transformations." Same for multivariate |
| 20 | Dynamical systems: state $x_t$, input $u_t$, measurement $z_t$; process model (state transition), observation model; must be observable (graphical model; note slide typo: third input labelled $u_{t+1}$ twice) |
| 21 | KF definition: $x\in\mathbb R^n$, $z\in\mathbb R^m$, discrete time, recursive, linear: $x_t = A x_{t-1}$, $z_t = H x_t$ |
| 22 | Prior estimate $\hat x'_t$ (predict with process model); posterior $\hat x_t$ (correct with measurement); $\hat x_t = \hat x'_t + K_t(z_t - H\hat x'_t)$; $K_t$ = "blending factor" weighting the innovation |
| 23 | Error and covariance definitions; optimal gain; limiting cases (fill-in-the-blank) |
| 24 | KF recipe (predict/correct loop diagram) |
| 25 | 1-D corridor-with-doors illustration of predict (bel widens, shifts) → correct ($P(o\mid s)$ sharp) → predict |
| 26 | Fully observable (KF, unimodal) vs partially observable (doors: multimodal belief) — motivates particle filters / multi-hypothesis |
| 27 | KF limitations: linear process, linear observation, white Gaussian noise. Nonlinear $x_{t+1}=f(x_t,u_t,w_t)$, $z_t=h(x_t,v_t)$ → "Linearize it!" |
| 28 | KF vs EKF picture: tangent line of $f$ at the operating point |
| 29 | Jacobians $A, W, H, V$ |
| 30, 38 | EKF for range–bearing localization (unicycle + range/bearing to known landmarks) |
| 31 | Linearized process model, $A(s_{t-1})$ |
| 32 | Linearized observation model, $H_i(s_t)$ |
| 33 | KF recipe vs EKF recipe side-by-side |
| 34–35 | Artificial landmarks (acoustic beacons, ship-hull positioning, iRobot ceiling beacons), natural visual landmarks (SIFT, SURF, corners; Se et al. IJRR stereo-SIFT map with covariance ellipses) |
| 36 | **Data association**: the $i$-th landmark's observation must be in the $i$-th row of $z_t$ |
| 37 | From localization to mapping: two choices of state (landmarks relative to robot vs robot + landmarks in start frame) |
| 39 | KF and SLAM: state = robot + landmarks |
| 40 | EKF-SLAM prior state (augmented to $2N+3$) |
| 41 | EKF-SLAM prior covariance (block Jacobian) |
| 42 | EKF-SLAM Kalman gain, $H_i$ with landmark columns |
| 43 | EKF-SLAM measurement & data association |
| 44 | EKF-SLAM posterior update; full covariance matrix structure (robot block, robot–landmark cross-covariances, landmark–landmark) |
| 45 | MonoSLAM (Davison, Imperial College): single camera gives no range; data association by tracking/matching patches |
| 46 | Submaps: state grows with landmarks; only few visible; submaps keep size manageable (Victoria Park dataset) |
| 47 | Navigation: RMS Titanic (Leonard & Eustice): EKF-based, 866 images, 3494 camera constraints, 3.1 km path |
| 48 | Search of Flight 370: INS = "a glorified IMU with Kalman filter" |
| 49 | Summary: system modelling, KF as estimation from a system model, linearization, localization & mapping in EKF, extensions for landmark matching and not-well-constrained systems |

**Key definitions (pp. 18–23):**
- Univariate: $p(x)=\frac{1}{\sqrt{2\pi}\sigma}e^{-\frac12\frac{(x-\mu)^2}{\sigma^2}}$; multivariate: $p(\mathbf x)=\frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}}e^{-\frac12(\mathbf x-\boldsymbol\mu)^T\Sigma^{-1}(\mathbf x-\boldsymbol\mu)}$.
- $X\sim N(\mu,\sigma^2),\ Y=aX+b \Rightarrow Y\sim N(a\mu+b, a^2\sigma^2)$; $X_1+X_2\sim N(\mu_1+\mu_2,\sigma_1^2+\sigma_2^2)$ (independence implied, not stated on slide).
- Prior error $e'_t = x_t-\hat x'_t$, prior covariance $\Sigma'_t = E[e'_t e'^T_t]$; posterior error $e_t=x_t-\hat x_t$, posterior covariance $\Sigma_t=E[e_te_t^T]$.
- Notation: **prime = prior/predicted** ($\hat x'_t$, $\Sigma'_t$), **no prime = posterior**. $Q$ = process noise covariance, $R$ = measurement noise covariance. (Thrun uses $\bar\mu_t,\bar\Sigma_t$ and $R_t$ = process, $Q_t$ = measurement: the opposite letters. Welch & Bishop use $\hat x^-_k$, $P^-_k$ with $Q$ process, $R$ measurement — the slides follow Welch & Bishop letters.)

**Key results & equations:**
- Optimal gain (p. 23): "The gain $K$ that minimizes the posterior covariance": $K_t=\Sigma'_tH^T(H\Sigma'_tH^T+R)^{-1}$ (minimizes trace of $\Sigma_t$).
- Limiting cases (p. 23, left blank on the slide — classic exam item): if $R\to0$, $K_t\to H^{-1}$ (when $H$ invertible) and $\hat x_t = H^{-1}z_t$ (trust the sensor completely); if $\Sigma'_t\to0$, $K_t=0$ and $\hat x_t=\hat x'_t$ (trust the prediction, ignore the measurement).
- Linearization (p. 27), Welch–Bishop form: $x_{t+1}\approx\tilde x_{t+1}+A(x_t-\hat x_t)+Ww_t$, $\tilde x_{t+1}=f(\hat x_t,u_t,0)$; $z_t\approx\tilde z_t+H(x_t-\tilde x_t)+Vv_t$, $\tilde z_t=h(\tilde x_t,0)$.
- Jacobians (p. 29): $A=\partial f/\partial x$, $W=\partial f/\partial w$, $H=\partial h/\partial x$, $V=\partial h/\partial v$, all evaluated at the estimate with zero noise.

**Algorithms (exactly as slides present them):**

KF recipe (p. 24, repeated p. 33):
1. Start with an initial guess $\hat x_0,\Sigma_0$.
2. Compute prior (prediction / time update): $\hat x'_t=A\hat x_{t-1}+Bu_{t-1}$; $\Sigma'_t=A\Sigma_{t-1}A^T+Q$.
3. Compute posterior (correction / measurement update): $K_t=\Sigma'_tH^T(H\Sigma'_tH^T+R)^{-1}$; $\hat x_t=\hat x'_t+K_t(z_t-H\hat x'_t)$; $\Sigma_t=(I-K_tH)\Sigma'_t$ (p. 24 writes "$(1-K_tH)$"; p. 33 correctly writes $I$).
4. REPEAT.

EKF recipe (p. 33):
- Given $\hat x_0,\Sigma_0$.
- Prediction: $\hat x'_t=f(\hat x_{t-1},u_{t-1},0)$; $\Sigma'_t=A_t\Sigma_{t-1}A_t^T+W_tQ_tW_t^T$ (slide image has a garbled "$W_tM_tW_t^T$"; p. 41 confirms $W_tQW_t^T$).
- Correction: $K_t=\Sigma'_tH_t^T(H_t\Sigma'_tH_t^T+V_tRV_t^T)^{-1}$; $\hat x_t=\hat x'_t+K_t(z_t-h(\hat x'_t,0))$; $\Sigma_t=(I-K_tH_t)\Sigma'_t$.

EKF range–bearing localization (pp. 30–32, 38):
- State $s_t=[x_t\ y_t\ \theta_t]^T$, input $u_t=[v_t\ \omega_t]^T$.
- Process (Euler-integrated unicycle): $f=\begin{bmatrix}x_{t-1}+\Delta t\,v_{t-1}\cos\theta_{t-1}\\ y_{t-1}+\Delta t\,v_{t-1}\sin\theta_{t-1}\\ \theta_{t-1}+\Delta t\,\omega_{t-1}\end{bmatrix}+\begin{bmatrix}w_x\\w_y\\w_\theta\end{bmatrix}$ (additive noise → $W=I$).
- $A(s_{t-1})=\begin{bmatrix}1&0&-\Delta t\,v_{t-1}\sin\theta_{t-1}\\0&1&\Delta t\,v_{t-1}\cos\theta_{t-1}\\0&0&1\end{bmatrix}$.
- Known landmarks $l_i=[x_{l_i}\ y_{l_i}]^T$; $h_i(s_t,v_t)=\begin{bmatrix}\sqrt{(x_t-x_{l_i})^2+(y_t-y_{l_i})^2}\\ \operatorname{atan2}(y_{l_i}-y_t,\ x_{l_i}-x_t)-\theta_t\end{bmatrix}+\begin{bmatrix}v_r\\v_b\end{bmatrix}$; $z_t$ stacks $h_1..h_N$ ($2N$ rows).
- With $q=(x_t-x_{l_i})^2+(y_t-y_{l_i})^2$: $H_i=\begin{bmatrix}\frac{x_t-x_{l_i}}{\sqrt q}&\frac{y_t-y_{l_i}}{\sqrt q}&0\\ \frac{y_{l_i}-y_t}{q}&\frac{x_t-x_{l_i}}{q}&-1\end{bmatrix}$ (verified by hand: correct).

EKF-SLAM (pp. 39–44):
1. State $s_t=[x_t\ y_t\ \theta_t\ l_1^T\cdots l_N^T]^T$ ($2N+3$).
2. Prior state: robot part propagated by the unicycle; landmarks copied ($l'_{i,t}=l_{i,t-1}$). Written as $s'_t = s_{t-1} + F^T[\Delta t v\cos\theta,\ \Delta t v\sin\theta,\ \Delta t\omega]^T$ with $F^T=[I_3;0]$ ("landmarks don't depend on external input").
3. Prior covariance: $\Sigma'_t=A_t\Sigma_{t-1}A_t^T+W_tQW_t^T$ with $A=\begin{bmatrix}A_{robot,3\times3}&0\\0&I_{2N}\end{bmatrix}$ ("static landmarks"; "motion of the robot does not affect the coordinates of the landmarks").
4. Gain: $K_t=\Sigma'_tH_t^T(H_t\Sigma'_tH_t^T+V_tRV_t^T)^{-1}$. $h_i$ depends only on 5 variables $x_t,y_t,\theta_t,x_{l_i},y_{l_i}$; compute each $H_i$ (2×(2N+3), zero except robot columns and landmark-$i$ columns) and stack. Landmark columns are the negatives of the robot x/y columns: range: $\frac{x_{l_i}-x}{\rho_i},\frac{y_{l_i}-y}{\rho_i}$; bearing: $\frac{-(y_{l_i}-y)}{\rho_i^2},\frac{x_{l_i}-x}{\rho_i^2}$. "Need to be in the correct columns of H."
5. Measurement $z_{i,t}=[r_{i,t}\ \phi_{i,t}]^T$; data association: each landmark observation must appear in the correct rows of $z_t$.
6. Posterior: $\hat s_t=\hat s'_t+K_t(z_t-h(\hat s'_t,0))$, $\Sigma_t=(I-K_tH_t)\Sigma'_t$; full dense covariance with robot–landmark and landmark–landmark correlations (p. 44).

**Worked examples worth redoing:**
- p. 25 corridor: draw the 1-D belief through predict (mean shifts by $u$, variance grows by $Q$) and correct (variance shrinks). Do it numerically with scalars: $\sigma'^2=\sigma^2+q$, $k=\sigma'^2/(\sigma'^2+r)$, $\sigma_{post}^2=(1-k)\sigma'^2$.
- pp. 31–32: derive $A$ and $H_i$ by hand (both are the exam-level Jacobian derivations).
- pp. 40–42: write the full $A$ (block-diag) and the sparse $H_i$ for 2 landmarks ($7\times7$ state).

**Exam / assignment hints:**
- p. 23 blanks ("If $R\to0$ … $\hat x_t=$ ?", "If $\Sigma'_t\to0$ … $\hat x_t=$ ?") — expect this exact question.
- p. 36 + 43: data association ordering bug is the classic implementation error in the EKF assignment.
- p. 37: "two choices" for the SLAM state and their pros/cons (robot-relative landmarks: no history, no fixed reference; robot+landmarks in start frame: landmarks fixed, own pose degrades).
- p. 26: why a KF cannot do global localization with identical doors (unimodal) → particle filter.
- p. 17: which representation gives global localization / recovery (discrete, PF, multi-hypothesis; not a single KF).

**Must be able to derive by hand:** Gaussian linear-transformation rule; the KF predict equations from $x_t=Ax_{t-1}+Bu+w$; gain limits; unicycle Jacobian $A$; range–bearing $H_i$ (robot and landmark columns); EKF-SLAM $A$ block structure and $H$ sparsity.

**Conventions / pitfalls (and slide errata):**
- p. 20 graphical model labels the third input $u_{t+1}$ (should be $u_{t+2}$).
- p. 24 writes $\Sigma_t=(1-K_tH)\Sigma'_t$ — must be identity $I$.
- p. 29 typo "$\partial f_1(,x_tu_t,0)$"; Jacobian index ranges are inconsistent (M vs N for rows/columns of $V$ and $H$). $A$ is $n\times n$, $W$ is $n\times\dim w$, $H$ is $m\times n$, $V$ is $m\times\dim v$.
- p. 30 indexes $f(s_t,\dots)$ but the right-hand side uses $t-1$; read as $s'_t=f(s_{t-1},u_{t-1},w_{t-1})$.
- p. 33 EKF prediction covariance term garbled ("$W_tM_tW_t^T$") — it is $W_tQ_tW_t^T$.
- p. 39 says "then we can write a linear observation system" — the range–bearing model is still nonlinear; the point is that the same EKF machinery applies to the augmented state.
- Bearing innovation must be wrapped to $(-\pi,\pi]$ (not on slides; standard pitfall).
- Thrun's EKF localization book version uses a velocity motion model with arcs ($\frac{v}{\omega}$ terms); the slides use the simpler Euler model — use the slide model for ASBR.

---

## Week 9 deck: "Bayesian Methods" (PDF pp. 1–55) — Lessons: A6 (main), A5, F8, A3 (brushfire reuse)
Source: fd570829-week09.pdf, PDF pp. 1–55 (one slide per page; pp. 9–14 and 30–33 are build-up animations of the same slide)

**Big idea:** The Kalman filter is one special case of a general recursion, the Bayes filter: keep a full probability distribution over states, blur it with the motion model (prediction = total probability / convolution), then multiply by the likelihood of the measurement and renormalize (correction = Bayes rule). When the belief is not Gaussian (identical doors, symmetric rooms, global localization, kidnapping), we must represent arbitrary distributions: either with a grid (piecewise constant / histogram filter) or with weighted random samples (particle filter, Monte Carlo localization). The sensor model $P(z\mid x,m)$ is where most of the engineering goes: the beam model and the likelihood-field model for range finders, and image-patch likelihoods for ceiling-camera localization. "We're not interested in finding the most likely state. *We want the whole distribution*." (pp. 9–14)

### Slide-by-slide map
| PDF p. | Content |
|---|---|
| 1 | Title "Bayesian Methods" |
| 2 | Recall robot localization: given $z_{1:t}$, known controls $u_{0:t}$, known model $P(x_{t+1}\mid x_t,u_t)$ with initial $P(x_1\mid u_0)$, known map $P(z_t\mid x_t)$; compute $P(x_t\mid z_{1:t-1},u_{0:t-1})$. "This is just a probabilistic representation of what you've already learned!" |
| 3 | Simple example: 1-D corridor with 3 doors, colour-coded multimodal belief through sense–move–sense–move |
| 4 | "Why not just use a Kalman filter?": laser scans in a symmetric office map; grid belief starts spread over many places and converges (global localization, multimodal) |
| 5 | Bayes filter problem statement + graphical model ($u_{k-1}\to x_k$, $x_k\to z_k$) |
| 6 | Bayesian filtering derivation: Bayes theorem, conditional independence, labelled "observation / state prediction / recursive instance" |
| 7–8 | Observation model $P(z_k\mid x_k)$: same $z_k$, different $x_k$ — "this is more likely than that, but how do we measure?"; compute distance of end-point of each beam to nearest obstacle |
| 9–14 | 1-D wall example built point by point: for fixed $z_k$, slide the robot along X and plot $P(z_k\mid x_k)$; final curve "doesn't have to be Gaussian" |
| 15–16 | Beam model: per beam, distance $d_i$ between measured and expected obstacle; "other things must be considered" |
| 17 | Range-finder beam model: $p_{hit}$, $p_{short}$, $p_{max}$, $p_{rand}$ (formulas below) |
| 18 | Plots of the 4 components (Probabilistic Robotics fig. 6.3) |
| 19 | Mixture $P(z_k\mid x_k,m)=[w_{hit},w_{short},w_{max},w_{rand}]\cdot[p_{hit},p_{short},p_{max},p_{rand}]^T$, weights sum to 1 |
| 20 | Constants $\eta_{hit}$, $\eta_{short}$ |
| 21 | Likelihood field model: $P_{hit}=N(0,\sigma_{hit})$ evaluated at $d$ = distance from beam endpoint to nearest obstacle |
| 22 | Algorithm likelihood_field_range_finder_model (Thrun Table 6.3) |
| 23 | Precompute nearest-obstacle distance: distance transform / **brushfire algorithm** (grid of wavefront numbers) |
| 24 | Bayes filtering formula with check mark on $P(z_k\mid x_k)$ (done) and arrow on $P(x_k\mid u_{k-1},x_{k-1})$ (next) |
| 25 | Predict motion (prior distribution): $P(x_{k+1})=\int P(x_{k+1}\mid x_k,u_k)P(x_k)\,dx_k$ |
| 26–27 | System model for discrete states $x\in\{A,\dots,Z\}$: every previous state can flow into $x_{t+1}=A$ (and $=B$) |
| 28–33 | **Worked discrete prediction** with 4 states and a transition table (below) |
| 34, 37 | Bayes filter recap as a trellis: $P(x_{k-1})$ → apply command $u_{k-1}$ → $P(x_k\mid x_{k-1},u_{k-1})$ → obtain $z_k$ and apply $P(z_k\mid x_k)$ |
| 35 | Recap of each term: measurement likelihood, prior (recursive), state transition over *all* $x_{k-1}$ |
| 36 | **Discrete Bayes filter algorithm** |
| 38 | Kalman vs Bayes (Isard 1998): deterministic drift, stochastic diffusion, reactive effect of measurement — unimodal vs multimodal |
| 39 | Piecewise-constant representation: observation update = pointwise multiplication; dynamic update = discrete convolution ("blurring") by the motion noise |
| 40 | 3-D grid $Bel(x_t=\langle x,y,\theta\rangle)$ for a mobile robot |
| 41 | Corridor: continuous belief vs histogram (grid) filter, starting from uniform |
| 42 | **Particle filter** motivation: $P(z\mid x,m)$ and $P(x_{k+1}\mid x_k,u_k)$ "never directly computable", must propagate an entire distribution; samples; non-Gaussian, nonlinear; aka Monte Carlo filter, survival of the fittest, Condensation, bootstrap filter (Rubin 88, Gordon 93, Kitagawa 96; Isard & Blake 96/98; Kanazawa 95) |
| 43 | Particles/samples: draw N samples, weight each by probability, $\sum w_i=1$ (Isard 1998 figure) |
| 44 | Corridor: grid belief vs particle belief (sample density + weights) |
| 45 | **Particle filter algorithm** |
| 46 | **Odometry motion model** sampler (Thrun Table 5.6 + Fig. 5.7) |
| 47 | Motion model samples: banana-shaped clouds growing along an L-shaped 10 m path |
| 48 | **Resampling**: roulette wheel (binary search, $\log n$) vs stochastic universal / systematic resampling (linear time, easy, low variance) |
| 49 | Sample-based localization with sonar (UW rse-lab): particles uniformly over the whole map |
| 50 | Video: Minerva tour of the Smithsonian NMAH (Burgard) |
| 51 | Using ceiling maps for localization (Dellaert et al. 99) |
| 52 | Vision-based localization: $P(z\mid x)$ from comparing the camera's ceiling pixel brightness with the ceiling mosaic |
| 53–55 | Likelihood maps $P(z\mid x)$ for a measurement "under a light" (very peaked, few places), "next to a light", "elsewhere" (uninformative: most of the map equally likely) |

**Key definitions / notation:**
- Time index $k$ (also $t$ on p. 2 and system-model slides). Controls indexed so that **$u_{k-1}$ moves $x_{k-1}\to x_k$**; measurements $z_{1:k}$, controls $u_{0:k-1}$.
- Posterior (belief) $P(x_k\mid u_{0:k-1},z_{1:k})$; $\eta_k$ = normalizer $=1/P(z_k\mid u_{0:k-1},z_{1:k-1})$.
- Map $m$ appears in sensor models $P(z_k\mid x_k,m)$; $z_k^*$ = true (ray-cast) range, $z_{max}$ = max range.
- Transition matrix $P_{ij}=P(j\mid i)$ = probability of moving from $i$ to $j$; **each row sums to 1** (p. 28).

**Key results & equations:**
- Bayes filter (pp. 6, 24, 35):
$$P(x_k\mid u_{0:k-1},z_{1:k})=\eta_k\,P(z_k\mid x_k)\int_{x_{k-1}}P(x_k\mid u_{k-1},x_{k-1})\,P(x_{k-1}\mid u_{0:k-2},z_{1:k-1})\,dx_{k-1}$$
Derivation (p. 6): Bayes rule on $z_k$ conditioned on everything else; Markov/conditional independence: $P(z_k\mid x_k,u_{0:k-1},z_{1:k-1})=P(z_k\mid x_k)$; total probability over $x_{k-1}$ and Markov: $P(x_k\mid x_{k-1},u_{0:k-1},z_{1:k-1})=P(x_k\mid x_{k-1},u_{k-1})$; and $x_{k-1}$ does not depend on future $u_{k-1}$. Labels: observation × state prediction × recursive instance.
- Beam model (p. 17): $p_{hit}=\eta_{hit}N(z_k;z_k^*,\sigma_{hit}^2)$ on $[0,z_{max}]$; $p_{short}=\eta_{short}\lambda e^{-\lambda z_k}$ on $[0,z_k^*]$; $p_{max}=I(z_k=z_{max})$; $p_{rand}=1/z_{max}$ on $[0,z_{max}]$. Mixture with $w_{hit}+w_{short}+w_{max}+w_{rand}=1$ (p. 19).
- Constants (p. 20): $\eta_{hit}=\left(\int_0^{z_{max}}N(z_k^*,\sigma^2)\,dz_k\right)^{-1}$; $\eta_{short}=\frac{1}{1-e^{-\lambda_{short}z_k^*}}$ (derive: integral of $\lambda e^{-\lambda z}$ from 0 to $z^*$ is $1-e^{-\lambda z^*}$).
- Likelihood field (p. 21): $P_{hit}(z_k\mid x_k,m)=N(d;0,\sigma_{hit})$ with $d$ = distance from the beam's endpoint to the nearest obstacle. Smooth in pose, no ray casting, ignores occlusion.
- Prediction (p. 25): $P(x_{k+1})=\int P(x_{k+1}\mid x_k,u_k)P(x_k)\,dx_k$; discrete: $P(x_{k+1}=j)=\sum_{i}P(x_{k+1}=j\mid x_k=i,u_k)P(x_k=i)$, i.e. new row-vector $=$ old row-vector $\times$ transition matrix.

**Worked example (pp. 28–33) — redo by hand, likely exam format:**
Prior $P(x_k)=[0,\ 0.5,\ 0,\ 0.5]$ over $\{1,2,3,4\}$. Transition table (rows $x_k$, columns $x_{k+1}$): row1 $[0.25,0.5,0.25,0]$, row2 $[0,0.25,0.5,0.25]$, row3 $[0,0,0.25,0.75]$, row4 $[0,0,0,1]$.
Result (p. 33): $P(x_{k+1})=[0,\ 0.125,\ 0.25,\ 0.625]$ (check: $0.5\cdot0.25=0.125$; $0.5\cdot0.5=0.25$; $0.5\cdot0.25+0.5\cdot1=0.625$; sums to 1 — verified). The natural follow-up exam step: multiply by a given $P(z\mid x)$ vector and normalize.

**Algorithms (exactly as the slides present them):**

Discrete_Bayes_filter($u_{0:k-1}$, $y_{1:k}$, $P(x_0)$) (p. 36):
```
1  P(x) = P(x0)                       (if you don't know: uniform distribution)
2  for i = 1:k
3    for all states x in X
4      P'(x) = sum_{x' in X} P(x | u, x') P(x')   # prediction given prior dist. and command
5    end for
6    eta = 0                                      # normalization constant
7    for all states x in X
8      P(x) = P(z | x) P'(x)                      # update using measurement
9      eta = eta + P(x)
10   end for
11   for all states x in X
12     P(x) = P(x) / eta                          # normalize to 1
13   end for
14 end for
```
(Signature names measurements $y_{1:k}$ but body uses $z$; $u$ means $u_{i-1}$, $z$ means $z_i$.)

likelihood_field_range_finder_model($z_t$, $x_t$, $m$) (p. 22; Thrun Table 6.3):
```
q = 1
for all beams k:
  if z_t^k != z_max:
    x_z = x + x_{k,sens} cos(th) - y_{k,sens} sin(th) + z_t^k cos(th + th_{k,sens})
    y_z = y + y_{k,sens} cos(th) + x_{k,sens} sin(th) + z_t^k sin(th + th_{k,sens})
    dist = min over occupied (x',y') of sqrt((x_z-x')^2 + (y_z-y')^2)
    q = q * ( z_hit * prob(dist, sigma_hit) + z_random / z_max )
return q
```
(Max-range readings are skipped; `dist` lookup is precomputed by a distance transform / brushfire (p. 23) — the same brushfire used for potential-field planning in A3.)

Particle_filter($u_{0:k-1}$, $y_{1:k}$, $P(x_0)$, set of N samples $\mathcal M=\{x_j,w_j\}$) (p. 45):
```
1  for i = 1:k
2    for j = 1:N
3      compute a new state x by sampling according to P(x | u_{i-1}, x_j)
4      x_j = x
5    end
6    eta = 0
7    for j = 1:N
8      w_j = P(z_i | x_j)
9      eta += w_j
10   end
11   for j = 1:N
12     w_j = w_j / eta
13   end
14   resample(M) according to weights w_j
15 end
```
Note: line 8 *overwrites* the weight (no multiplication by the old weight) — correct only because the set is resampled every step (bootstrap / SIR filter with proposal = motion model). Initialization (sampling N particles from $P(x_0)$, uniform for global localization, p. 49) is implicit.

sample_motion_model_odometry($u_t$, $x_{t-1}$) (p. 46; Thrun Table 5.6), with odometry $u_t=(\bar x_{t-1},\bar x_t)$:
```
d_rot1  = atan2(y'_bar - y_bar, x'_bar - x_bar) - th_bar
d_trans = sqrt((x_bar - x'_bar)^2 + (y_bar - y'_bar)^2)
d_rot2  = th'_bar - th_bar - d_rot1
dh_rot1  = d_rot1  - sample(a1 d_rot1^2 + a2 d_trans^2)
dh_trans = d_trans - sample(a3 d_trans^2 + a4 d_rot1^2 + a4 d_rot2^2)
dh_rot2  = d_rot2  - sample(a1 d_rot2^2 + a2 d_trans^2)
x' = x + dh_trans cos(th + dh_rot1)
y' = y + dh_trans sin(th + dh_rot1)
th' = th + dh_rot1 + dh_rot2
return (x', y', th')
```
(rotate–translate–rotate decomposition; $\alpha_{1..4}$ are robot-specific noise parameters; `sample(b^2)` draws zero-mean noise with variance $b^2$.)

Resampling (p. 48): roulette wheel = N independent draws, each a binary search in the cumulative weights, $O(N\log N)$; **systematic / stochastic universal sampling** = one random offset $r\in[0,1/N)$ then N equally spaced pointers $r+(j-1)/N$, $O(N)$, lower variance (Thrun's low_variance_sampler).

Piecewise constant (grid/histogram) filter (pp. 39–41): observation = pointwise multiply; motion = discrete convolution of the grid with the motion-noise kernel; 3-D grid over $(x,y,\theta)$.

**Exam / assignment hints:**
- pp. 28–33 discrete prediction table is the template for a hand-computed Bayes filter question (predict with the transition matrix, then correct with $P(z\mid x)$ and normalize). Remember the "each row sums to 1" property and which index is "from".
- pp. 17–20: know the 4 beam-model components, what physical effect each models (noise / unexpected obstacle / max-range failure / random), and the normalizers.
- p. 21 vs p. 17: beam model (ray casting to expected range $z^*$) vs likelihood field (endpoint distance to nearest obstacle).
- p. 45: PF loop structure (sample motion → weight by likelihood → normalize → resample). p. 48: why low-variance resampling.
- pp. 53–55: interpret a likelihood map (informative vs uninformative measurement).
- The week-9 slides do not cover kidnapped robot / random particle injection / KLD sampling / particle deprivation explicitly — those come from the Thrun et al. 2000 "Robust MCL" reading (W10–12) and should be studied there.

**Must be able to derive by hand:** the Bayes filter recursion from Bayes rule + Markov assumptions (p. 6); the discrete prediction sum; $\eta_{short}$; the posterior for a small discrete example; why KF is a special case (Gaussian prior × linear-Gaussian transition = Gaussian; Gaussian likelihood × Gaussian prior = Gaussian).

**Conventions / pitfalls (and slide errata):**
- p. 2 says "compute $P(x_t\mid z_{1:t-1},u_{0:t-1})$ — most likely state x at time t" — that is the *predicted* belief (no $z_t$) and it is a distribution, not "most likely state"; p. 5 gives the correct target $P(x_k\mid z_{1:k},u_{0:k-1})$.
- p. 2 also calls $P(z_t\mid x_t)$ the "known map … most likely sensor reading given state" — it is the measurement likelihood (sensor model), which *uses* the map.
- p. 25 text: "given the command $u_k$ and all the previous states $x_k$" — means marginalizing over all possible previous states.
- p. 17 writes $N(z_k^*,\sigma_{hit})$ while p. 20 writes $N(z_k^*,\sigma^2)$: the second argument is the variance $\sigma_{hit}^2$.
- p. 45 PF and p. 36 discrete filter sign their measurements $y_{1:k}$ but use $z$ in the body.
- PF weights must be normalized before resampling; for numerical safety multiply many beam likelihoods in log-space.
- Using the motion model as proposal makes the weight = likelihood only; a very peaked $P(z\mid x)$ (e.g. "under a light", p. 53) causes particle deprivation — motivation for mixture proposals in Thrun et al. 2000.

---

## Review deck (untitled, 24 pp.; second-half / final-exam review) — Lessons: F8, A5, A6
Source: 2e63680d-review.pdf, PDF pp. 1–24 (one slide per page)

**Big idea:** This is the instructor's own condensed list of what matters from weeks 8–12: Gaussians and covariance, KF, EKF, EKF-SLAM, Bayes filter, sensor models (beam mixture and likelihood field), discrete Bayes filter, and the particle filter. Read it as the **exam syllabus for the estimation half**. Every slide is a re-use of a week-8/9 slide, with a few additions (marked NEW below) that signal emphasis.

### Slide-by-slide map
| PDF p. | Content |
|---|---|
| 1 | Gaussian pdf, univariate and multivariate (= wk8 p. 18) |
| 2 | Properties of Gaussians (= wk8 p. 19) |
| 3 | **NEW: "Covariance, covariance, covariance"** — "Know what's a covariance matrix: what it does, what it means, why it matters, how you can compute one, how you can visualize them." Sample mean $\bar x=\frac1N\sum x_i$, $e_i=x_i-\bar x$, $\Sigma=E[e_ie_i^T]$; 2-D ellipse and 3-D ellipsoid pictures |
| 4 | KF definition **NEW: with noise**: $x_t=Ax_{t-1}+Bu_{t-1}+w_{t-1}$ (state transition, control input (optional), Gaussian white noise); $z_t=Hx_t+v_t$ |
| 5 | KF recipe (= wk8 p. 24) **NEW caption: "Minimizes the sum of the expected errors: $\sum e_i^2$"** (i.e. minimum mean-square error / trace of $\Sigma_t$) |
| 6 | 1-D corridor KF walk-through (= wk8 p. 25) |
| 7 | KF limitations (= wk8 p. 27 without the linearization lines) |
| 8 | KF vs EKF recipes side by side (clean version: EKF prediction $\Sigma'_t=A_t\Sigma_{t-1}A_t^T+W_tQW_t^T$ — confirms the wk8 p. 33 garble) |
| 9 | EKF range–bearing localization (bearing now written $\tan^{-1}\frac{y_t-y_{l_i}}{x_t-x_{l_i}}-\theta_t$; see pitfalls) |
| 10 | KF and SLAM + full covariance picture; **NEW: "note that if we don't have some fixed landmarks, our system is unobservable (we can't fully determine all unknown quantities)"** |
| 11 | EKF-SLAM prior state estimation (= wk8 p. 40) |
| 12 | EKF-SLAM prior covariance update (= wk8 p. 41) |
| 13 | Bayes filter statement (dynamic model written $P(x_k\mid x_{k-1})$, control omitted) |
| 14 | Bayesian localization derivation (= wk9 p. 6; with $dx_{k-1}$) |
| 15 | Bayes filter recap trellis (= wk9 p. 34) |
| 16 | Predict motion (= wk9 p. 25) |
| 17 | System model 4-state example (= wk9 p. 28) — but the "Compute" line drops the $P(x_k)$ factor (see pitfalls) |
| 18 | Observation model 1-D "doesn't have to be Gaussian" (= wk9 p. 14) |
| 19 | Beam-model mixture (= wk9 p. 19) with **NEW: a plain Gaussian sensor model crossed out** ("not just a Gaussian": use the 4-component mixture) |
| 20 | Likelihood field: $P_{hit}(z_k\mid x_k,m)=\varepsilon_{\sigma_{hit}^2}(d^2)$ (Thrun notation: zero-mean Gaussian of the endpoint distance), with Thrun Fig. 6.8 (map, likelihood field, and the resulting $p_{hit}$ along one beam with obstacles $o_1,o_2,o_3$ — multimodal, unlike the beam model's single Gaussian at $z^*$) |
| 21 | Discrete Bayes filter algorithm (= wk9 p. 36) |
| 22 | Particle filter motivation (= wk9 p. 42, shortened) |
| 23 | Particle filter corridor illustration (Thrun Fig. 4.3 style) |
| 24 | Particle filter algorithm (= wk9 p. 45) |

**Key results & equations (new or re-emphasised):**
- Sample covariance (p. 3): $\bar x=\frac1N\sum_i x_i$, $\Sigma=\frac1N\sum_i (x_i-\bar x)(x_i-\bar x)^T$ (use $\frac1{N-1}$ for the unbiased estimate). Visualize: eigen-decomposition $\Sigma=V\Lambda V^T$; the $k$-sigma ellipse has semi-axes $k\sqrt{\lambda_i}$ along eigenvectors $v_i$ (ties to F4).
- KF optimality (p. 5): $K_t$ minimizes $E[\|x_t-\hat x_t\|^2]=\operatorname{tr}\Sigma_t$.
- Observability (p. 10): with no fixed/known landmarks (or an anchored initial pose), the global frame is unobservable (gauge freedom) — SLAM is only defined relative to the start pose.

**Algorithms:** identical to the week 8 (KF, EKF, EKF-SLAM) and week 9 (discrete Bayes filter, particle filter) versions recorded above.

**Exam / assignment hints (strong):**
- p. 3 is an explicit study list for covariance: define, compute from samples, interpret (correlations/off-diagonals), propagate ($A\Sigma A^T$), visualize (ellipse from eigenvectors/eigenvalues).
- Expect: run one KF step by hand in 1-D or 2-D; write $A$, $H$ for the range–bearing EKF; explain the SLAM covariance block structure and why off-diagonal robot–landmark terms matter; explain unobservability without fixed landmarks; derive the Bayes filter; do the 4-state discrete prediction (+ correction); list beam-model components; contrast beam vs likelihood field; write the PF loop.
- The review deck contains **no planning content** (C-space, potential fields, PRM/RRT) and no hand–eye/rigid-motion content; those first-half topics were presumably covered by a separate midterm review (not in these uploads).

**Conventions / pitfalls (slide errata):**
- p. 3 sums $i=0..N$ with a $\frac1N$ prefactor (N+1 terms): index should run $1..N$.
- p. 5 and p. 6 again write $(1-K_tH)$; it is $(I-K_tH)$.
- p. 9 writes the bearing as $\tan^{-1}\!\big(\frac{y_t-y_{l_i}}{x_t-x_{l_i}}\big)-\theta_t$: same ratio as the correct expression but a single-argument arctan loses the quadrant (off by $\pi$ when the landmark is behind). Always use $\operatorname{atan2}(y_{l_i}-y_t,\ x_{l_i}-x_t)-\theta_t$ (wk8 p. 30) and wrap to $(-\pi,\pi]$.
- p. 13 omits $u_{k-1}$ in the dynamic model; p. 17 writes "Compute $P(x_{k+1})=\int P(x_{k+1}\mid x_k,u_k)\,dx_k$" — the factor $P(x_k)$ is missing (wk9 p. 28 has it).
- p. 20's $\varepsilon_{\sigma^2}(d^2)$ is Thrun's notation; it means $\frac{1}{\sqrt{2\pi\sigma^2}}e^{-d^2/(2\sigma^2)}$.

---

## Official course page: "Algorithms for Sensor-Based Robotics" (CIRL website, 6 pp.) — Lessons: all A1–A7
Source: e53b0db3-Algorithms_for_Sensor-Based_Robotics___CIRL.pdf, PDF pp. 1–6 (web-page printout, CIRL = Computational Interaction and Robotics Laboratory)

**Big idea:** The course is a survey of navigation from an algorithmic perspective: basic kinematics, C-space, motion planning, localization and mapping, taught in the context of ROS, with examples from mobile platforms, manipulation, robotic surgery, and human–machine systems.

**Logistics (p. 1–2):** 3 credits; instructor Simon Leonard; lectures Tue/Thu 12:00–1:15 PM (Hodson 210); office hours Thu 2–4 PM (Zoom link in the syllabus); CAs' Zoom office hours in syllabus. Lab: Wyman 170, 12 desktops with Ubuntu 24.04 and ROS 2 Humble. (Note: ROS 2 Humble officially targets Ubuntu 22.04, and Jazzy targets 24.04; the page's pairing is unusual — check the lab machines before you set up a local copy.)
**Books:** primary *Principles of Robot Motion* (Choset et al., MIT Press 2005); other: *Probabilistic Robotics* (Thrun, Burgard, Fox 2006), *Planning Algorithms* (LaValle 2006), ROS documentation.
**Preparation:** advanced undergrads / first-year grads; rudimentary linear algebra and calculus; **must program in C++ and Matlab**.
**Grading (p. 2):** five assignments (47%), two exams (25% each), demonstrations (3%). "All students will need to use real robots by the end of the term to earn their final mark."
**Dates (this offering, Fall 2026):** A1 Sep 24 · A2 Oct 8 · A3 Nov 5 · A4 Nov 19 · A5 Dec 10 · Midterm Oct 20 · Final Dec 22 (2026). Due 23:59 EST; −10% per day late; submit on Canvas only (no email); Canvas for questions.

**Schedule and readings (pp. 3–4):**
| Week | Topic | Readings | Lesson |
|---|---|---|---|
| 1 | Introduction | — | — |
| 2 | Rigid body motion and velocity (linear algebra for relative 3-D motion and velocity); kinematics: forward/inverse kinematics of chains and mobile robots, manipulator Jacobian | Choset ch. 3; Murray–Li–Sastry ch. 2; Springer Handbook ch. 1 | A1 (F3, R1–R2, R5) |
| 3 | Hand-eye calibration | Park & Martin, "Robot sensor calibration: solving AX=XB on the Euclidean group", IEEE T-RA 10(5):717–721, 1994 | A2 |
| 4 | Potential fields: attractive and repulsive potentials, sensor constraints as potentials | Choset ch. 4; Mezouar & Chaumette, "Path planning for robust image-based control", IEEE T-RO 2002 | A3 |
| 5 | Graph-based path planning: visibility maps, generalized Voronoi diagram, cell decompositions, Minkowski sum | Choset ch. 5, 6; de Berg et al. *Computational Geometry* ch. 6, 7, 13, 15 | A3 |
| 6 | Sample-based path planning: PRM, RRT | Choset ch. 7; LaValle ch. 5 | A4 |
| 7 | Review and midterm | — | — |
| 8, 9 | Kalman filter (dynamic linear systems), Extended Kalman filter (nonlinear) | Choset ch. 8; Davison et al. "MonoSLAM", PAMI 2007; Harris, "Geometry from visual motion", Active Vision 1993 | A5 |
| 10, 11, 12 | Bayesian filtering and multiple hypotheses; particle filter | Choset ch. 9; Thrun, Fox, Burgard, Dellaert "Robust Monte Carlo localization for mobile robots", AI 2000; Doucet, de Freitas, Murphy, Russell "Rao-Blackwellised particle filtering for dynamic Bayesian networks", UAI 2000 | A6 |
| 13 | Review | — | — |

**Policies (pp. 5–6), important for how we help the student:**
- No teamwork or collaboration on assignments; no public or private sharing of code, assignments, exams.
- Cannot use any material from students in previous semesters; cannot share material in future semesters.
- **"You cannot use any online tools (AI-based or otherwise) such as ChatGPT or Copilot."** Any misconduct → at least an F course grade, retroactive.
- Implication for the knowledge base: our lessons must teach concepts and use our own practice problems; they must not produce solutions to the actual graded assignments.

**Exam / assignment hints:** two exams split the course at week 7: midterm = rigid motion, hand–eye, potential fields, graph planning, sampling planners; final (Dec 22) = KF/EKF/SLAM + Bayes/PF (matches the review deck above). Five assignments map plausibly to: A1 rigid motion/kinematics, A2 hand–eye, A3 planning (potential fields/graph), A4 sampling planners, A5 estimation (KF/EKF/PF) — due dates fit this ordering, but the page does not state assignment contents.

