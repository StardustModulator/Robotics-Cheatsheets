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

