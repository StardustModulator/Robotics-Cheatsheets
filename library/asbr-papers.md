# ASBR assigned papers: Park–Martin 1994, Davison et al. 2007 (MonoSLAM), Lozano-Pérez 1983, Kavraki et al. 1996 (PRM)

Source: the student's ASBR folder `downloads/Algorithms_For_Sensor_Based_Robotics/`: `park1994.pdf` (5 pp.), `davison2007.pdf` (16 pp.), `spatial-planning.pdf` (13 pp.), `prmbasic_01.pdf` (15 pp., image-only scan). Every page of all four was read (2026-11-26). Text-layer extractions were checked against page renders wherever symbols were garbled. `prmbasic_01.pdf` was read only from renders.

**Page convention:** "p. 4 (J720)" means PDF page 4 = journal page 720. The offsets are:

| File | Paper | PDF → journal |
|---|---|---|
| `park1994.pdf` | Park & Martin, "Robot Sensor Calibration: Solving AX = XB on the Euclidean Group", *IEEE T-RA* 10(5):717–721, Oct. 1994 | J = PDF + 716 |
| `davison2007.pdf` | Davison, Reid, Molton, Stasse, "MonoSLAM: Real-Time Single Camera SLAM", *IEEE PAMI* 29(6):1052–1067, June 2007 | J = PDF + 1051 |
| `spatial-planning.pdf` | Lozano-Pérez, "Spatial Planning: A Configuration Space Approach", *IEEE Trans. Computers* C-32(2):108–120, Feb. 1983 | J = PDF + 107 |
| `prmbasic_01.pdf` | **Kavraki, Švestka, Latombe, Overmars, "Probabilistic Roadmaps for Path Planning in High-Dimensional Configuration Spaces", *IEEE T-RA* 12(4):566–580, Aug. 1996** (the journal paper itself, not slides) | J = PDF + 565 |

PDF p. 1 of `spatial-planning.pdf` opens with the bios of Lin and Menon, the authors of the preceding article. Lozano-Pérez's paper starts mid-page.

**Lesson codes used here:**
- A1 kinematics / spatial Jacobian
- A2 hand-eye
- A3 C-space + potentials
- A4 graph planning
- A5 sampling planners
- A6 KF/EKF/EKF-SLAM
- A7 Bayes/PF/MCL
- A8 tools + exam prep

`asbr-slides-part1/2.md` use the older numbering (A4 = sampling, A5 = KF, A6 = Bayes), so translate when cross-reading.

---

## 1. Park & Martin 1994: AX = XB on SE(3) (5 pp.): Lesson A2

**Big idea.** Take the matrix log of the rotation parts. This turns the rotation equation $\Theta_A\Theta_X=\Theta_X\Theta_B$ into the **linear** vector equation $\alpha=\Theta_X\beta$ between rotation vectors (p. 2, J718). That gives:
- an exact closed form from two motions;
- a closed-form least-squares rotation from $k$ noisy motions, using the orthogonal Procrustes / Nádas result;
- then an ordinary linear least-squares solve for the translation.

Rotation and translation are solved **sequentially**, so the rotation estimate does not depend on length units (p. 4, J720).

**Problem statement (p. 1, J717).**
- $A$ is the wrist frame's motion relative to itself after a robot move.
- $B$ is the sensor (camera) frame's motion relative to itself after the same move.
- $X$ is the sensor frame relative to the wrist, and it satisfies $AX=XB$.
- One pair $(A,B)$ leaves a one-parameter family of solutions (first shown by Shiu and Ahmad), so at least two pairs are needed.
- With noise, the goal is to minimise $\sum_i d(A_iX,\,XB_i)$.

### Definitions, lemmas, theorems
- **SO(3) exponential (Lemma 1, p. 2, J718).** $\exp[\omega]=I+\frac{\sin\|\omega\|}{\|\omega\|}[\omega]+\frac{1-\cos\|\omega\|}{\|\omega\|^2}[\omega]^2$. Proof: by Cayley–Hamilton, $[\omega]^3=-\|\omega\|^2[\omega]$. Picture SO(3) as the solid ball of radius $\pi$ with antipodal boundary points identified.
- **SO(3) log (Lemma 2, p. 2, J718).** If $\mathrm{Tr}\,\Theta\ne-1$: $\log\Theta=\frac{\phi}{2\sin\phi}(\Theta-\Theta^T)$, where $1+2\cos\phi=\mathrm{Tr}\,\Theta$ and $|\phi|<\pi$. At $\mathrm{Tr}=-1$ ($\phi=\pi$) the log is two-valued: $\pm\pi[\hat\omega]$, with $\hat\omega$ the unit eigenvector for eigenvalue 1.
- **SE(3) exp/log (Lemmas 3–4, p. 2, J718).**
  - Exp: $\exp\begin{bmatrix}[\omega]&v\\0&0\end{bmatrix}=\begin{bmatrix}e^{[\omega]}&Av\\0&1\end{bmatrix}$, with $A=I+\frac{1-\cos\|\omega\|}{\|\omega\|^2}[\omega]+\frac{\|\omega\|-\sin\|\omega\|}{\|\omega\|^3}[\omega]^2$.
  - Log uses $A^{-1}=I-\tfrac12[\omega]+\frac{2\sin\|\omega\|-\|\omega\|(1+\cos\|\omega\|)}{2\|\omega\|^2\sin\|\omega\|}[\omega]^2$.
  - The SE(3) part is not needed for the solver; only the SO(3) log is used.
- **Block split (eqs. 2–3, p. 2, J718).** $\Theta_A\Theta_X=\Theta_X\Theta_B$ and $\Theta_Ab_X+b_A=\Theta_Xb_B+b_X$.
- **Lemma 5 (existence, p. 2, J718).** A rotation solution exists **iff** $\|\log A\|=\|\log B\|$. In words, the robot and the sensor must see the same rotation angle. Proof: $A=XBX^T$ gives $[\alpha]=X[\beta]X^T$, which gives $\alpha=X\beta$.
- **Theorem 1 (p. 2, J718).** If $X_p$ is one solution, then $X=X_pe^{[\beta]t}=e^{[\alpha]t}X_p$ is also a solution for every $t$. These are **all** the solutions (the proof finishes on p. 3, J719).
- **Remark 1 (p. 3, J719).** A particular solution is the rotation about $\beta\times\alpha$ by the angle from $\beta$ to $\alpha$. Remark 2 handles $\mathrm{Tr}=-1$.
- **Theorem 2 (uniqueness, p. 3, J719).** Assume $\mathrm{Tr}A_i,\mathrm{Tr}B_i\ne-1$ and $\|\log A_i\|=\|\log B_i\|$. Then the solution of $A_iX=XB_i$, $i=1,2$, is unique **iff** $\log A_1\times\log A_2\ne0$ and $\log B_1\times\log B_2\ne0$, i.e. **the two rotation axes are not parallel**.
  - The exact solution is eq. (4): $\boxed{\Theta_X=\mathcal A\mathcal B^{-1}}$, with $\mathcal A=[\alpha_1\;\alpha_2\;\alpha_1\times\alpha_2]$ and $\mathcal B=[\beta_1\;\beta_2\;\beta_1\times\beta_2]$.
- **Translation uniqueness (§III-B, p. 3, J719).** Eq. (5) is $(\Theta_A-I)b_X=\Theta_Xb_B-b_A$. Stacking two motions gives eq. (6):
  $\begin{bmatrix}\Theta_{A_1}-I\\\Theta_{A_2}-I\end{bmatrix}b_X=\begin{bmatrix}\Theta_Xb_{B_1}-b_{A_1}\\\Theta_Xb_{B_2}-b_{A_2}\end{bmatrix}$.
  - The row space of $\Theta_{A_i}-I$ is the plane perpendicular to axis $i$, so one motion gives rank 2.
  - Two non-parallel axes give rank 3, so $b_X$ is unique.
- **Rejected metric (eqs. 7–8, p. 3, J719).** $d^2(A,B)=\|\log\Theta_A^T\Theta_B\|^2+\|b_A-b_B\|^2$ is a left-invariant geodesic metric, but minimising it is a hard nonlinear problem. The paper deliberately uses a simpler linear criterion instead.
- **Least squares (§IV, p. 4, J720).**
  - Nádas: $\min_{\Theta,b}\sum\|\Theta x_i+b-y_i\|^2$ is solved by $\Theta=(M^TM)^{-1/2}M^T$, $b=\bar y-\Theta\bar x$, with $M=\sum x_iy_i^T$ and the symmetric positive-definite square root.
  - Applied with $x_i=\beta_i$, $y_i=\alpha_i$ and no translation: $\eta_1=\sum\|\Theta_X\beta_i-\alpha_i\|^2$ is minimised by
  $$\boxed{\Theta_X=(M^TM)^{-1/2}M^T,\qquad M=\sum_i\beta_i\alpha_i^T}$$
  - Then $\eta_2=\sum\|(\Theta_{A_i}-I)b_X-\Theta_Xb_{B_i}+b_{A_i}\|^2$ is minimised by $b_X=(C^TC)^{-1}C^Td$, with
  $C=\begin{bmatrix}I-\Theta_{A_1}\\\vdots\\I-\Theta_{A_k}\end{bmatrix}$ and $d=\begin{bmatrix}b_{A_1}-\Theta_Xb_{B_1}\\\vdots\\b_{A_k}-\Theta_Xb_{B_k}\end{bmatrix}$.
  - Matrix square root (p. 4, J720): $M^TM=Q\Lambda Q^{-1}$ gives $(M^TM)^{-1/2}=Q\,\mathrm{diag}(\lambda_i^{-1/2})Q^{-1}$. This assumes distinct eigenvalues.
  - **"A minimum of three $(A_i,B_i)$ pairs are needed in order for $M$ to be nonsingular (with only two data pairs the formula for the exact solution can be used)"** (p. 4, J720).

### Algorithm (as in the paper)
```
input: k pairs (A_i, B_i) of relative wrist and sensor motions
for each i: alpha_i = vee(log Theta_A_i); beta_i = vee(log Theta_B_i)   # Lemma 2
            (sanity check: |alpha_i| ≈ |beta_i|, Lemma 5; skip angle ≈ 0 or ≈ π)
if k == 2:  Theta_X = [a1 a2 a1×a2] [b1 b2 b1×b2]^{-1}                # eq. 4, Thm 2
else:       M = Σ beta_i alpha_i^T                                    # needs rank 3
            Q Λ Q^{-1} = eig(M^T M);  Theta_X = Q Λ^{-1/2} Q^{-1} M^T
C = stack(I - Theta_A_i);  d = stack(b_A_i - Theta_X b_B_i)
b_X = (C^T C)^{-1} C^T d                                              # use lstsq/QR in code
return X = [Theta_X b_X; 0 1]
```

### Proved vs only claimed
- **Proved:**
  - existence (Lemma 5);
  - the full one-parameter solution family (Thm 1);
  - two-motion uniqueness (Thm 2);
  - translation uniqueness (rank argument);
  - the exp/log formulas.
- **Cited without proof:** the Nádas least-squares formula (IBM report RC6945).
- **Only claimed or shown by simulation:**
  - the error decreases with $k$;
  - sequential rotation-then-translation is "simple and computationally efficient".
  - The authors admit it is not the geometrically invariant optimum of eqs. (7)–(8) (Conclusions, p. 5, J721).

### Experiments and numbers worth remembering (pp. 4–5, J720–721)
- **Exact example (from Shiu–Ahmad).**
  - $A_1$ is a rotation of 3 rad about $z$: $\alpha_1=(0,0,3)$.
  - $A_2$ is a rotation of 1.5 rad about $y$: $\alpha_2=(0,1.5,0)$.
  - $\beta_1=(0,0.596,2.9402)$ and $\beta_2=(0,1.4701,-0.298)$.
  - The answer is $X$ with $\Theta_X=\mathrm{Rot}_x(0.2\text{ rad})$ (entries 0.980067, ±0.198669) and $b_X=(10,50,100)$, eq. (9).
  - **Re-verified numerically** after fixing one printed typo (see Errors).
- **Noisy simulation.**
  - True $X_s$ is eq. (9). Translations of $A_i$ have magnitude up to $400\sqrt3$. The $\alpha_i$ are uniform in the radius-$\pi$ ball.
  - Small noise: uniform noise $\epsilon\in[-\pi/100,\pi/100]$ on each component of $\alpha_i,\beta_i$, and $\xi\in[-5,5]$ on each translation component.
  - Large noise: $\epsilon\in[-\pi/20,\pi/20]$ and $\xi\in[-10,10]$.
  - Error is averaged over 10 runs of 100 measurements.
  - Fig. 1 (small noise): error ≈ 8 at $k=2$, falls fast until $k\approx10$, ≈ 1 by $k=20$.
  - Fig. 2 (large noise): ≈ 31 at small $k$, falls to ≈ 2–3 by $k=10$.
  - One run with $k=100$: $b_X=(11.07,\,49.52,\,100.26)$, rotation entries within ~0.002 of truth.

### Differences from the slides and README (checked)
- **Formula check.** README §5.2 (line 208) and lecture03 pp. 13–15 write $M=\sum\beta_i\alpha_i^T$, $R_X=(M^TM)^{-1/2}M^T$, $C=[I-R_{A_i}]$, $d=[t_{A_i}-R_Xt_{B_i}]$. **These match the paper exactly** (p. 4, J720). The slides' eigen-decomposition step is the paper's text verbatim.
- **Mismatch: number of motions.**
  - The README attaches "Needs ≥2 motions with non-parallel rotation axes" to the **least-squares** formula.
  - Per the paper, 2 non-parallel axes are enough for **uniqueness**, but the $(M^TM)^{-1/2}$ formula needs $M$ of rank 3: **≥3 motions whose rotation axes are not all coplanar**.
  - With 2 motions, $M$ has rank 2. This was checked numerically on the paper's example: eigenvalues of $M^TM$ are $(0,\,5.16,\,81.0)$. So use eq. (4) for 2 motions.
  - `asbr-slides-part1.md` says the LS "fails if $M$ is rank-deficient (all rotation axes parallel)". All-parallel is only the worst case; coplanar axes or $k=2$ also fail.
- **Frames.**
  - The paper's $A$ and $B$ are the wrist and sensor motions "relative to itself", with $X$ = sensor in wrist.
  - The slides build them from absolute poses: $A=E_1^{-1}E_2$, $B=S_1S_2^{-1}$ (sensor pose of the target). These are consistent.
  - The paper never says how to form $A,B$ from absolute poses; that is the slides' contribution.
- **Not in the paper:**
  - the det = −1 / reflection fix (Nádas solves over O(3));
  - the SVD form $R_X=V\,\mathrm{diag}(1,1,\det VU^T)U^T$ from $M^T=U\Sigma V^T$;
  - any weighting.
  - The objective $\sum\|\Theta_X\beta_i-\alpha_i\|^2$ implicitly weights each motion by its rotation angle $\|\alpha_i\|$. Large rotations dominate, but the log is ill-conditioned near $\pi$.

### Exercises worth doing (build items; write all code yourself)
1. SO(3) `log`/`exp` with the $\theta\to0$ series and the $\theta=\pi$ branch. Test round trips on 10⁴ random rotations.
2. Reproduce the exact example: compute $\alpha_i,\beta_i$, eq. (4), eq. (6), and get $b_X=(10,50,100)$. Use the corrected $B_2$.
3. Verify Theorem 1 numerically: for one pair, $X_pe^{[\beta]t}$ solves $AX=XB$ for every $t$.
4. Reproduce Figs. 1–2 with the paper's noise model. Plot error vs $k$ for the LS rotation, the SVD+det version, and (optional) a final nonlinear refinement on eq. (8).
5. Degeneracy study:
   - parallel axes (Thm 2 fails);
   - coplanar axes with $k\ge3$ (the LS formula fails, SVD still works);
   - $\theta\to\pi$;
   - pairs whose $|\|\alpha\|-\|\beta\||$ is large (use Lemma 5 as an outlier test).
6. Anchored pairs ($A_i=E_1^{-1}E_{i+1}$) vs consecutive pairs: compare error. Anchored pairs share pose-1 noise.

### Pitfalls
- $\|\alpha\|=\theta$ is the rotation angle, not a unit axis. $M$ uses the full $\theta\hat\omega$ vectors.
- Pairs with $\theta\approx0$ carry no rotation information, and their $\alpha$ is noise-dominated. Pairs with $\theta\approx\pi$ have a sign-ambiguous log.
- Translation is solved *after* rotation, so rotation error leaks into $b_X$ (the error is about $\|b_B\|$ times the rotation error). Keep $\|b_{B_i}\|$ moderate.
- $(C^TC)^{-1}C^Td$ is fine on paper; use QR or `lstsq` in code.

### Errors found (verified)
1. **$B_2$, entry (1,3), p. 4 (J720): printed 0.997612, should be 0.977612.**
   - With the printed value $\Theta_{B_2}$ is not orthogonal (row 1 and column 3 both have norm 1.0196), and $\log$ gives $\beta_2=(0,1.4851,-0.298)$, not the printed $(0,1.4701,-0.298)$.
   - With 0.977612, $X^{-1}A_2X$ reproduces $B_2$ to 6 digits, the printed $\beta_2$ is recovered, and eqs. (4) and (6) return $b_X=(10.00,50.00,100.01)$.
2. **Error metric, p. 5 (J721):** $\eta=\sum_{i=1}^{k}\|\hat X-\hat X_s\|^2$ has no $i$ in the summand, and $X_s$ (the true value) should not wear a hat. Read it as $\|\hat X-X_s\|^2$ averaged over runs.
3. **Nádas statement, p. 4 (J720):**
   - With a translation $b$ in the model, the optimal $\Theta$ must use the **centred** cross-covariance $\sum(x_i-\bar x)(y_i-\bar y)^T$.
   - As printed (uncentred $M$) it is right only when no translation is fitted.
   - That is the case in the hand-eye rotation step, so the hand-eye result is unaffected. Do not reuse the printed formula for point registration (use Arun/Horn).

### Gaps this fills in the knowledge base
- Lemma 5: equal rotation angles as an **existence/consistency test** and an outlier filter.
- Theorem 1's explicit one-parameter family, which explains *why* one motion is not enough.
- The ≥3-motion / rank-3 requirement for the LS formula, with eq. (4) as the 2-motion fallback.
- The paper's numeric test case, usable as a unit test for Assignment 2.
- The rejected geodesic metric (eq. 8) as the "proper" nonlinear refinement objective.

---

## 2. Davison, Reid, Molton, Stasse 2007: MonoSLAM (16 pp.): Lesson A6

**Big idea.** Full-covariance EKF-SLAM, exactly the week08 recipe, runs at 30 Hz with **one hand-held camera** and no odometry. Four ingredients make it work:
1. a **constant-velocity, constant-angular-velocity** motion model, which replaces the control input;
2. **active search**: predict each feature's image position and search only inside its innovation ellipse;
3. a sparse map of about 100 high-quality landmarks;
4. a **top-down** feature initialization that represents unknown depth as a 1-D particle set along a ray (pp. 1–2, J1052–1053).

**Problem statement.** Recover the 3-D trajectory of a freely moving monocular camera in an unknown scene, in real time and drift-free, with a persistent map (pp. 1–2). The paper explicitly prioritises localization; the map exists to serve localization (p. 1).

### State and covariance (§3.1, p. 4, J1055)
- Full state: $\hat{\mathbf x}=(\hat{\mathbf x}_v;\hat{\mathbf y}_1;\hat{\mathbf y}_2;\dots)$ with dense $P$ in blocks $P_{xx},P_{xy_i},P_{y_iy_j}$ (eq. 1).
- **Camera, 13-D (eq. 2):** $\mathbf x_v=(\mathbf r^W;\ \mathbf q^{WR};\ \mathbf v^W;\ \boldsymbol\omega^R)$:
  - position in world $W$ (3);
  - orientation quaternion (4);
  - linear velocity in $W$ (3);
  - angular velocity in the camera ("robot") frame $R$ (3).
- **Features:** $\mathbf y_i$ is a 3-D point in $W$.
  - While being initialized, a feature is a 6-D ray $\mathbf y_{pi}=(\mathbf r_i^W;\hat{\mathbf h}_i^W)$ plus a separate depth particle set (§3.6, p. 7, J1058).
  - The 2007 paper does **not** use inverse depth. It cites Solà et al. (Gaussians uniform in inverse depth) and **Montiel et al. [47] (inverse-depth parametrisation)** as later improvements (p. 8, J1059).
- Cost: map size $O(N^2)$ and update $O(N^2)$. That bounds the map to "around 100" features at 30 Hz (p. 4, J1055).

### Motion model (§3.4, p. 6, J1057)
- Each step, unknown zero-mean Gaussian accelerations give a velocity impulse, eq. (3): $\mathbf n=(\mathbf V^W;\boldsymbol\Omega^R)=(\mathbf a^W\Delta t;\ \boldsymbol\alpha^R\Delta t)$.
- $P_n$ is diagonal (uncorrelated components).
- State update, eq. (4):
$$\mathbf f_v=\begin{pmatrix}\mathbf r^W+(\mathbf v^W+\mathbf V^W)\Delta t\\ \mathbf q^{WR}\times\mathbf q\big((\boldsymbol\omega^R+\boldsymbol\Omega^R)\Delta t\big)\\ \mathbf v^W+\mathbf V^W\\ \boldsymbol\omega^R+\boldsymbol\Omega^R\end{pmatrix}$$
  Here $\mathbf q(\boldsymbol\theta)$ is the quaternion of rotation vector $\boldsymbol\theta$.
- Process noise, eq. (5): $Q_v=\frac{\partial\mathbf f_v}{\partial\mathbf n}P_n\frac{\partial\mathbf f_v}{\partial\mathbf n}^T$.
- **The paper does not give the Jacobians:** "complicated but a tractable matter of differentiation; we do not present the results here" (p. 6). Derived below; check them by finite differences.
- Tuning: small $P_n$ means smooth motion but fails on sudden moves; large $P_n$ copes with jerks but needs many measurements per frame.
- AR values: $\sigma_a=10\ \mathrm{m\,s^{-2}}$ (≈1 g) and $\sigma_\alpha=6\ \mathrm{rad\,s^{-2}}$ (p. 11, J1062).

**Jacobians (derived here, not in the paper).** Quaternions are scalar first. $\mathbf q_\omega=\mathbf q(\boldsymbol\omega\Delta t)$, and $[\mathbf p]_R$ / $[\mathbf q]_L$ are the right/left quaternion-product matrices ($\mathbf q\otimes\mathbf p=[\mathbf q]_L\mathbf p=[\mathbf p]_R\mathbf q$).
$$F_v=\frac{\partial\mathbf f_v}{\partial\mathbf x_v}=\begin{bmatrix}I_3&0&I_3\Delta t&0\\0&[\mathbf q_\omega]_R&0&[\mathbf q]_L\,\frac{\partial\mathbf q(\boldsymbol\omega\Delta t)}{\partial\boldsymbol\omega}\\0&0&I_3&0\\0&0&0&I_3\end{bmatrix},\qquad
\frac{\partial\mathbf f_v}{\partial\mathbf n}=\begin{bmatrix}I_3\Delta t&0\\0&[\mathbf q]_L\frac{\partial\mathbf q}{\partial\boldsymbol\omega}\\I_3&0\\0&I_3\end{bmatrix}$$
- Here $\frac{\partial\mathbf q(\boldsymbol\theta)}{\partial\boldsymbol\omega}=\Delta t\frac{\partial\mathbf q}{\partial\boldsymbol\theta}$, with $\mathbf q(\boldsymbol\theta)=(\cos\frac{|\boldsymbol\theta|}{2},\ \sin\frac{|\boldsymbol\theta|}{2}\frac{\boldsymbol\theta}{|\boldsymbol\theta|})$. Use the small-angle limit $\partial\mathbf q/\partial\boldsymbol\theta\to[0;\tfrac12I_3]$.
- $P_n=\mathrm{diag}(\sigma_a^2\Delta t^2I_3,\ \sigma_\alpha^2\Delta t^2I_3)$.
- Features are static, so the full $F=\mathrm{blkdiag}(F_v,I_{3N})$. Prediction touches only $P_{xx}\leftarrow F_vP_{xx}F_v^T+Q_v$ and $P_{xy_i}\leftarrow F_vP_{xy_i}$, which is $O(N)$.

### Measurement model (§3.5, pp. 6–7, J1057–1058)
- Point in camera frame, eq. (6): $\mathbf h^R_L=R^{RW}(\mathbf y^W_i-\mathbf r^W)$, with $R^{RW}=R(\mathbf q^{WR})^T$.
- Pinhole, eq. (7): $u=u_0-fk_u\,h_x/h_z$ and $v=v_0-fk_v\,h_y/h_z$. The **minus signs** come from the paper's camera-frame axes. Copy them only together with that frame.
- Radial distortion, eqs. (8–10), chosen because it is approximately invertible: $u_d-u_0=\dfrac{u-u_0}{\sqrt{1+2K_1r^2}}$, $v_d-v_0=\dfrac{v-v_0}{\sqrt{1+2K_1r^2}}$, $r=\sqrt{(u-u_0)^2+(v-v_0)^2}$.
- Calibration: $fk_u=fk_v=195$ px, $(u_0,v_0)=(162,125)$, $K_1=6\times10^{-6}$, at 320×240 with a FOV of nearly 100° (p. 7). A wide FOV was chosen deliberately: it trades pixel resolution for better-conditioned geometry (ref. [6]).
- **Innovation covariance, eq. (11):** $S_i=\frac{\partial\mathbf u_{di}}{\partial\mathbf x_v}P_{xx}\frac{\partial\mathbf u_{di}}{\partial\mathbf x_v}^T+\frac{\partial\mathbf u_{di}}{\partial\mathbf x_v}P_{xy_i}\frac{\partial\mathbf u_{di}}{\partial\mathbf y_i}^T+\frac{\partial\mathbf u_{di}}{\partial\mathbf y_i}P_{y_ix}\frac{\partial\mathbf u_{di}}{\partial\mathbf x_v}^T+\frac{\partial\mathbf u_{di}}{\partial\mathbf y_i}P_{y_iy_i}\frac{\partial\mathbf u_{di}}{\partial\mathbf y_i}^T+R$. That is $H_iPH_i^T+R$ written blockwise, with $R$ diagonal and set by image resolution.
- **Jacobian structure (derived).**
  - $\partial\mathbf h^R/\partial\mathbf y_i=R^{RW}$ and $\partial\mathbf h^R/\partial\mathbf r=-R^{RW}$.
  - $\partial\mathbf h^R/\partial\mathbf q$ is $3\times4$.
  - $\partial\mathbf u/\partial\mathbf h^R=\begin{bmatrix}-fk_u/h_z&0&fk_uh_x/h_z^2\\0&-fk_v/h_z&fk_vh_y/h_z^2\end{bmatrix}$.
  - $\partial\mathbf u_d/\partial\mathbf u=sI-2K_1(1+2K_1r^2)^{-3/2}\,\mathbf d\mathbf d^T$, with $\mathbf d=\mathbf u-\mathbf u_0$ and $s=(1+2K_1r^2)^{-1/2}$.
  - $H_i$ is zero in the $\mathbf v,\boldsymbol\omega$ columns and in other features' columns.

### Active search (§3.5, p. 7, J1058)
- **Gating:** a $3\sigma$ ellipse from $S_i$ defines the only image region searched.
- Matching: normalized cross-correlation of the warped 11×11 template inside that ellipse (§3.2, p. 5).
- $S_i$ also measures information. When many features are visible, measure the **10–12 with the largest $S_i$**, which squashes the largest uncertainty axis.
- Search regions are typically 15–20 px across (§3.7, p. 9).
- Active search is "not sensible if the camera were to become lost": there is no relocalization (p. 7).

### Feature initialization (§3.6, pp. 7–8, J1058–1059)
1. After a feature is detected (Shi–Tomasi), add a semi-infinite ray $(\mathbf r_i,\hat{\mathbf h}_i)$ to the EKF state.
2. Put **100 equally weighted depth particles uniformly on 0.5–5.0 m** (Fig. 5).
3. On each new frame, project every hypothesis $\mathbf y_{\lambda i}=\mathbf r_i+\lambda\hat{\mathbf h}_i$ into an ellipse and run NCC in all of them (with a look-up table so overlaps are not recomputed). Reweight the particles by Bayes' rule using each ellipse's 2-D Gaussian likelihood, and prune the weakest.
4. When **$\sigma_{depth}/\text{depth}<0.3$**, convert to a 3-D Gaussian point. This typically takes 2–4 frames.
5. Approximation: while partially initialized, measurements inform only the depth, not the ray parameters. The method works poorly for motion along the optic axis (little parallax).

### System start-up and gauge (§3.3, pp. 5–6)
- A **known target** (typically 4 corners of a black rectangle) enters the map with **zero uncertainty**. It fixes the world frame and the **scale**.
- Monocular scale is otherwise unobservable, and without features the filter could not run from frame 1 to frame 2.
- The initial camera uncertainty is "a few degrees and centimetres".

### Map management (§3.7, p. 9, J1060)
- Keep about **12** reliable features visible. Add features only when fewer are visible.
- New features are found by Shi–Tomasi in a randomly placed **80×60** box that avoids existing features and is not about to leave the view.
- Visibility is predicted from the image bounds and from not having translated too far from the initialization viewpoint.
- **Delete** a feature if more than **50 %** of its predicted-visible match attempts fail. This removes T-junctions, moving objects, specular highlights and often-occluded points.

### Feature orientation (§3.8, pp. 9–10)
- Each patch is a local plane. The template is warped by the homography $H=CR[\mathbf n^T\mathbf x_pI-\mathbf t\mathbf n^T]C^{-1}$ (eq. 12).
- Normals live in a separate 2-parameter EKF per feature, outside the main state. The warp is refined by inverse-compositional alignment.
- The main system works without this (normals are assumed parallel to the viewing ray at initialization).

### Algorithm (one frame)
```
predict:  x_v ← f_v(x_v, 0);  P_xx ← F_v P_xx F_vᵀ + Q_v;  P_xy ← F_v P_xy
for each mapped feature i predicted visible:
    u_di ← h_i(x̂);  H_i ← [∂u/∂r, ∂u/∂q, 0, 0, …, ∂u/∂y_i, …];  S_i ← H_i P H_iᵀ + R
select ≤ 10–12 features with largest S_i
for each selected: NCC search in 3σ ellipse of S_i (warped template)
    if match: EKF update with z_i (sequential or stacked); else record failure
    (optional, HRP-2: gyro z = ω^R, H = [0 0 0 I], update before vision, σ = 0.01 rad/s)
partially-initialized features: search their particle ellipses, reweight depth particles,
    convert to 3-D point when σ_d/d < 0.3
map management: delete if >50% failures; add a new ray if < ~12 visible
```

### Mapping to the ASBR week08 EKF-SLAM deck (wk8 pp. 27–44)
| week08 symbol / step | MonoSLAM element |
|---|---|
| state $s_t=[x,y,\theta,l_1..l_N]$, $2N+3$ (p. 40) | $[\mathbf r,\mathbf q,\mathbf v,\boldsymbol\omega,\mathbf y_1..\mathbf y_N]$, $13+3N$ (eqs. 1–2) |
| $f(s_{t-1},u_{t-1})$, unicycle driven by **known** $u$ (p. 30) | constant-velocity $\mathbf f_v$ with **no** control input. Velocities are states, accelerations are noise (eq. 4) |
| $A$ = blkdiag($A_{robot}$, $I$) "static landmarks" (p. 41) | $F$ = blkdiag($F_v$, $I_{3N}$) |
| $W Q W^T$ (p. 33/41) | $Q_v=\frac{\partial f_v}{\partial n}P_n\frac{\partial f_v}{\partial n}^T$ (eq. 5): $W=\partial f_v/\partial n$, $Q=P_n$ |
| range–bearing $h_i$ (p. 30) | pinhole + radial distortion $\mathbf u_{di}$ (eqs. 6–10) |
| sparse $H_i$: robot columns + landmark-$i$ columns, landmark columns = −robot $x,y$ columns (p. 42) | same pattern: nonzero in $\mathbf r,\mathbf q$ and $\mathbf y_i$ columns; $\partial\mathbf h^R/\partial\mathbf y_i=-\partial\mathbf h^R/\partial\mathbf r$ |
| $S=H\Sigma'H^T+VRV^T$ inside $K$ (p. 33) | $S_i$ of eq. (11), also used for **gating** and **feature selection** |
| data association by row ordering (pp. 36, 43) | association by **appearance** (NCC on a warped patch) restricted to the $3\sigma$ gate |
| landmarks given or initialized by inverse measurement (not on slides) | **partial initialization**: ray in state + 1-D depth particle filter → Gaussian point |
| unobservability without fixed landmarks (review p. 10) | known target with zero covariance fixes the gauge (frame) and the monocular **scale** |
| p. 45 "MonoSLAM: no range; data association by patch matching" | all of the above |
| p. 46 submaps | paper lists submapping as future work for large scale (p. 14) |

### Proved vs only claimed
- No theorems. It is a systems paper.
- **Claimed:**
  - "first successful application of the SLAM methodology … to the pure vision domain of a single uncontrolled camera" (p. 1);
  - drift-free operation and routine loop closure (p. 12);
  - "little doubt that the system would run for much longer" (p. 11);
  - FastSLAM-type methods might not give equally good repeatable localization ("remains to be proven", p. 4).

### Experiments and numbers worth remembering
- Real time at **30 Hz** on a 1.6 GHz Pentium M, **19 ms** per frame (p. 14, J1065):
  - 2 ms image loading;
  - 3 ms correlation;
  - **5 ms Kalman update**;
  - 4 ms feature init;
  - 5 ms rendering.
- **AR demo** (§4, pp. 10–12): room-scale, several minutes (thousands of frames). The user clicks 3 features to define a plane for virtual furniture. Tracking survives with 2–3 features visible.
- **HRP-2 humanoid** (§5, pp. 12–13):
  - wide-angle camera (~90° FOV);
  - chest gyro at 200 Hz, sampled at 30 Hz with σ = 0.01 rad/s, used as an "internal measurement" of $\boldsymbol\omega^R$;
  - walks a circle of radius 0.75 m in about 30 s; the loop is closed at the end with a visible drift correction (Fig. 10).
- **Ground truth** (§6.1, p. 13): a plumb-line over a 1.00 × 0.50 m rectangle (1 cm truth precision). Errors of a few cm, jitter 1–2 cm; persistent biases shrink by about 1 cm per loop.

### Exercises worth doing (build items)
1. Implement $\mathbf f_v$ and both Jacobians above. Check against central finite differences (watch the quaternion normalisation).
2. Implement $\mathbf u_d(\mathbf x_v,\mathbf y)$ with the paper's camera numbers and its full Jacobian. Finite-difference check.
3. A 2-D or 3-D simulated MonoSLAM: a known-ID point landmark cloud, a camera on a smooth trajectory, a 4-landmark zero-covariance "target", $3\sigma$ gating, and top-$k$ selection by $\det S_i$ or $\mathrm{tr}\,S_i$. Plot NEES.
4. The depth particle initializer (100 particles on 0.5–5 m; collapse at 0.3). Compare sideways vs forward motion.
5. Extension: inverse-depth features (Montiel et al.) and compare convergence; add a gyro update.
6. Show scale drift by removing the zero-covariance target.

### Pitfalls
- Normalise $\mathbf q$ after every prediction and update. The EKF update does not preserve $\|\mathbf q\|=1$ (not discussed in the paper).
- $\boldsymbol\omega$ is in the camera frame and $\mathbf v$ in the world frame. Mixing frames breaks $F_v$.
- Mind the sign convention in eq. (7), and do not undistort the image: the paper predicts *distorted* coordinates and matches on raw images.
- Monocular depth is not Gaussian at first sight. Inserting a single 3-D Gaussian at an arbitrary depth gives a bad linearisation, which is why the ray + particles step exists.
- Active search assumes the camera is not lost. There is no relocalization.

### Errors found (verified)
1. **Ground-truth table, p. 13 (J1064):** true $z=-0.62$ m at all four corners, but every estimated $z$ is printed **positive** (0.64, 0.63, 0.66, 0.64). The sign is dropped (or the frames differ); the magnitudes agree. The text also says "0.93 mean x-value" for the entry printed $-0.93$.
2. **Eq. (3), p. 6 (J1057):** the angular acceleration is introduced as $\boldsymbol\alpha^W$ but used as $\boldsymbol\alpha^R$ in $\mathbf n$. It must be $R$, to match $\boldsymbol\omega^R$.
3. **p. 4 (J1055):** the text says "orientation quaternion $\mathbf q^{RW}$" while eq. (2) has $\mathbf q^{WR}$. Use $\mathbf q^{WR}$, consistent with eq. (4) and $R^{RW}$ in eq. (6).

### Gaps this fills in the knowledge base
- The **constant-velocity process model** for platforms without odometry ("no $u$"), with its Jacobians and the $P_n$ tuning trade-off.
- A 3-D camera measurement model in EKF form.
- **Gating** with $S_i$, and choosing measurements by $S_i$. This is the missing practical half of week08 p. 36 data association.
- **Landmark initialization**, i.e. state augmentation when depth is unknown. week08 skips it.
- Gauge and scale fixing by a known target.
- Map management (add/delete rules).
- The $O(N^2)$ cost → about 100 features. That is the concrete number behind week08 p. 46 "submaps".

---

## 3. Lozano-Pérez 1983: Spatial Planning, a C-space approach (13 pp.): Lessons A3, A4

**Big idea.** Shrink the moving object $A$ to a **point** in its configuration space, and grow each obstacle $B$ into the set of configurations where $A$ would hit it: the **C-space obstacle** $CO_A(B)$. Two problems then become point problems:
- placing a point outside all $CO_A(B_j)$ and inside $CI_A(R)$ (**Findspace**);
- finding a curve of such points (**Findpath**).

For a translating convex polygon, $CO_A(B)$ is the Minkowski sum $B\oplus(-A)$ and takes $O(n+m)$ time to build. Rotation adds a curved dimension, handled approximately by **slices** (pp. 1–3, J108–110).

### Definitions
- **Findspace / Findpath (p. 2, J109).** $R$ contains $k_B$ fixed (possibly intersecting) objects $B_j$.
  - Findspace: find a position of $A$ inside $R$ with $A\cap B_j=\emptyset$ for all $j$ (a "safe position").
  - Findpath: find a path from $s$ to $g$ through safe positions inside $R$.
  - $R,B_j$ are convex polyhedra; $A$ is a union of convex polyhedra.
- **Configuration (p. 2, J109).** A set of independent parameters fixing every point of $A$, measured relative to an initial configuration $(A)_0$, in which the **reference vertex** $rv_A$ is at the origin.
  - A $k$-D polyhedron needs $d=k+\binom k2$ parameters: polygon → $(x,y,\theta)$, $d=3$; polyhedron → $d=6$.
  - With orientation fixed, $Cspace_A$ is just $(x,y)$.
- **C-space obstacle (p. 3, J110).** $CO_A(B)=\{x\in Cspace_A\mid(A)_x\cap B\ne\emptyset\}$. Moving $rv_A$ to a different vertex translates $CO_A(B)$.
- **C-space interior (p. 3, J110).** $CI_A(B)=\{x\mid(A)_x\subseteq B\}$, so $CI_A(B)=\overline{CO_A(\overline B)}$ (complement of the C-obstacle of the complement). Findspace and Findpath then mean staying outside all $CO_A(B_j)$ and inside $CI_A(R)$.
- **Set operations (p. 4, J111).** $A\oplus B=\{a+b\}$, $A\ominus B=\{a-b\}=A\oplus(\ominus B)$, $\ominus A=\{-a\}$. Note that $A\oplus A\ne\{2a\}$ and $A\ominus A\ne\{0\}$.

### Theorems
- **Thm 1 (p. 4, J111).** For fixed orientation, $CO_A^{xy}(B)=B\ominus(A)_0=B\oplus(-A_0)$. Proof: $(A)_c=c\oplus(A)_0$; a common point $b=c+a'$ gives $c=b-a'$. It extends to $xyz$.
- If $A,B$ are convex, then $A\oplus B$ is convex. If both are convex polygons, $CO$ is a convex polygon.
- **Lemma 1 (p. 4).** The support set of $A\oplus B$ in direction $u$ is the sum of the support sets: $\pi(A\oplus B,u)\cap(A\oplus B)=(\pi(A,u)\cap A)\oplus(\pi(B,u)\cap B)$.
- **Lemma 2 (p. 4).**
  - segment ⊕ point = translated segment;
  - parallel, same-direction segments add end to end.
- **Thm 2 (p. 4, J111).** For a convex $n$-gon $A$ and $m$-gon $B$, $CO_A^{xy}(B)$ takes **$O(n+m)$** time to compute.
  - As the normal $u$ sweeps counter-clockwise, each boundary element is one of: a new vertex (vertex + vertex), a displaced edge (edge + vertex), or a merged edge (edge + parallel edge).
  - Store both polygons with edges sorted by angle and merge the two lists in linear time.
  - For $B\ominus(A)_0$, first replace each $a_i$ by $-a_i$ (Fig. 7 sorts by **edge-normal angle**, p. 5, J112).
- **Non-convex (p. 5, J112).** Split boundaries into arcs with interior angles $<\pi$ and merge arc pairs. The result may self-intersect, at worst **$O(n\times m)$**.
- **Thm 3 (p. 5, J112).** For unions, $CO_A(B)=\bigcup_i\bigcup_j CO_{A_i}(B_j)$ over convex pieces.
- **Rotation, $CO_A^{xy\theta}(B)$ (§III, pp. 5–6, J112–113).**
  - For fixed $\theta$, the edges are $e^a_i=b_j\oplus s(a_i(\theta),a_{i+1}(\theta))$ (rotating, eq. 2a) or $e^b_j=a_i(\theta)\oplus s(b_j,b_{j+1})$ (only displaced, eq. 2b).
  - The vertex–edge pairing changes at the critical angles $\theta^*_{ij}$ where edge $i$ of $A$ becomes parallel to edge $j$ of $B$. There are $O(n\times m)$ of them.
  - Between critical angles, $e^b$ faces lie on $\langle u_j,x\rangle=\|a_i\|\cos(\theta+\phi_i-\eta_j)+\langle b_j,u_j\rangle$ (eq. 3).
  - $e^a$ faces are **curved** surfaces in $(x,y,\theta)$ (eq. 4).
  - So the full obstacle is **not polyhedral** and cannot feed the visibility graph directly.
- **Thm 4 (p. 6, J113, proved).** $\mathrm{conv}(A\oplus B)=\mathrm{conv}(A)\oplus\mathrm{conv}(B)=\mathrm{conv}(\mathrm{vert}A\oplus\mathrm{vert}B)$.
  - Corollary: for convex polyhedra, $CO^{xyz}_A(B)=\mathrm{conv}(\mathrm{vert}(B)\ominus\mathrm{vert}((A)_0))$.
  - **Thm 5 (p. 7, J114):** $O(n^2\log n)$ via a hull of the $O(n^2)$ pairwise differences.
  - It also gives conservative hulls for non-convex shapes.
- **3-D with rotation (§V, p. 7, J114).** There are three face types, eqs. (5a–c): $A$-face + $B$-vertex, $A$-vertex + $B$-face, and $A$-edge ⊕ $B$-edge parallelograms. Each lies on $\langle N,x\rangle=\langle N,a_i(\theta)+b_j\rangle$ (eq. 6) between discontinuities. The full object is a curved 6-D set.
- **Slice projection (§VI, pp. 7–8, J114–115).** Split the $\theta$ range into $k$ intervals. Each "θ-slice" projected onto $(x,y)$ is the C-obstacle of the **area swept by $A$** over that range, approximated by a union of polygons (Fig. 10).
  - **Thm 6** (proved in App. II, pp. 11–12, J118–119): slice projection = C-obstacle of the swept volume, *if* the swept volume is "displaceable" (eq. 7).
  - Properties: a solution found in a slice is a true solution, but **not all solutions are found** (conservative, incomplete). Rotations happen only in quantized steps.
  - Counterexample: carrying a sofa round a tight hallway corner, which needs continuous rotation while translating (p. 8, J115).
- **Manipulators (§VII, pp. 8–9, J115–116).** Model arms as linked polyhedra. Joint parameters are the natural C-space, but rotary joints break the $xyz$ algorithm.
  - For Cartesian robots, sweep the wrist rotations into a polyhedron and use $CO^{xyz}$.
  - Sweeping joints $j..n$ together (a "displaceable" swept volume) is the condition behind Thm 6.

### Algorithms
**SET-SUM, App. I (pp. 10–11, J117–118; Thm 2).** Polygons are stored counter-clockwise: vertex `vert[i]` starts edge $i$, and `angle[i]` is edge $i$'s outward-normal angle in $[0,2\pi)$, increasing. The corrected version:
```
# input: a, b convex, CCW, normal angles increasing; output c = a ⊕ b
ea ← index with a.angle[ea-1] < b.angle[1] < a.angle[ea]   (treat a.angle[0] = a.angle[size] − 2π)
c ← [a.vert[ea] + b.vert[1]]
loop until every edge of a and b has been consumed (b.angle[size+1] = b.angle[1] + 2π):
    ang ← offset + a.angle[ea]
    if ang ≤ b.angle[eb]: advance ea (on wrap: ea ← 1, offset ← 2π)
    if ang ≥ b.angle[eb]: advance eb                      # both on parallel edges
    append a.vert[ea] + b.vert[eb]
# for CO_A(B): call with a = −(A)_0 (negate vertices, keep CCW order), b = B
```
**Findpath by visibility graph (p. 3, J110).**
- Fixed orientation, 2-D, Euclidean metric: shortest safe paths are polygonal lines through vertices of the $CO^{xy}(B_j)$.
- Build the **Vgraph**: nodes are all CO vertices plus $s$ and $g$; edges join every pair that "see" each other (the segment crosses no obstacle interior).
- The shortest Vgraph path is the shortest safe path.
- Footnote 4: rectilinear-metric optimality is different (ref. [19]).
- In 3-D the Vgraph is **neither optimal nor complete**: a path may need to run along CO edges whose vertices lie outside $CI(R)$ (p. 7, J114).
- The paper gives **no complexity** for Vgraph construction. The README's $O(n^3)$ naive / $O(n^2\log n)$ sweep figures come from Choset and de Berg, not from here.

**Complexity summary:**

| Case | Cost |
|---|---|
| convex polygons, fixed θ | $O(n+m)$ |
| non-convex polygons | $O(nm)$ |
| convex polyhedra, fixed orientation | $O(n^2\log n)$ |
| critical orientations for rotating polygons | $O(nm)$ |

### Proved vs only claimed
- **Proved:** Thms 1, 4, 6, and the Thm 2 construction. Thm 3 is immediate.
- **Cited or asserted:**
  - Vgraph optimality in 2-D (ref. [24], Lozano-Pérez & Wesley 1979);
  - 3-D Vgraph non-optimality (ref. [24]);
  - the swept-volume approximations needed by slices ("simple approximations are not difficult", footnote 6, an area "where better algorithms are required").

### Exercises worth doing (build items)
1. SET-SUM, done carefully: implement the corrected version above. Test against brute force (convex hull of all $b_j-a_i$) on 10³ random pairs. Then implement the printed listing literally and watch it fail (see Errors).
2. Show the reference-point effect: move $rv_A$ and check that $CO$ translates by the same vector.
3. $CI_A(R)$ for a convex room $R$: intersect the inward-shifted half-planes. Then do Findspace for a rectangle among 5 convex obstacles.
4. 2-D Findpath: $CO$ polygons → Vgraph (an $O(n^3)$ visibility test is fine) → Dijkstra/A*. Show that the path touches obstacles, then inflate by a margin.
5. Rotation by slices: for a rectangle $A$, compute $CO^{xy}$ at θ samples and the swept-area slice C-obstacles for $k=4,8,16$. Find a sofa-in-corner instance that slices cannot solve.
6. Convex polyhedra: $CO^{xyz}=\mathrm{conv}\{b_j-a_i\}$ with a 3-D hull. Time it against $n$.

### Pitfalls
- Use $-A$, not $A$: $CO=B\oplus(-A_0)$. Forgetting to reflect the robot gives the wrong shape for asymmetric robots.
- The paper's "$\ominus$" means $\{a-b\}$ (Minkowski sum with the negation; Choset App. F uses the same convention). Many image-processing texts use $\ominus$ for **erosion**, a different operation.
- Merge by **edge-normal angle**, consistently counter-clockwise, and handle parallel edges (both indices advance).
- Slices are conservative. A "no path" result from a slice planner proves nothing.
- Shortest Vgraph paths graze obstacles, so add clearance in practice (the paper flags model and motion inaccuracy, p. 3).

### Errors found (verified)
1. **App. I, polygon record (p. 10, J117):** "angle[0] = 2π − angle[size]" should be **angle[size] − 2π**.
   - The first `WHILE` loop needs `a.angle[0] < b.angle[1]` when the straddling edge is edge 1.
   - With the printed value, a faithful transcription ran off the end of the array in 545 of 2000 random convex-polygon pairs.
2. **App. I, main loop (pp. 10–11, J117–118):** `WHILE (eb <= b.size)` stops as soon as $b$'s edges are used up.
   - It skips every edge of $a$ whose normal angle lies after $b$'s last edge (between `b.angle[size]` and `b.angle[1]+2π`), so those vertices are **missing**.
   - Even with fix 1, the literal listing missed vertices in 837 of 2000 random pairs.
   - Continuing the loop until $a$ has also wrapped (sentinel `b.angle[size+1] = b.angle[1] + 2π`) matched brute force in 2000 of 2000.
   - Theorem 2 itself is correct; only the listing is wrong.
3. Text-layer artefact, not a paper error: the extraction shows `vc:-1`; the page reads `vc := 1`.

### Gaps this fills in the knowledge base
- The formal **Findspace / Findpath** statement and $CI_A(R)$ (staying *inside* a container). These are absent from README §5.3 and the week03 notes.
- **Reference-vertex dependence** of C-obstacles.
- The non-convex cost $O(nm)$ and the union rule (Thm 3).
- Polyhedra: $O(n^2\log n)$ via a hull of vertex differences (Thm 4/5).
- Rotation: $O(nm)$ critical angles, curved faces, and why exact $(x,y,\theta)$ obstacles don't feed a Vgraph.
- **Slice projection = C-obstacle of the swept area** (Thm 6), with its conservativeness. This is the precise version of the "stack of slices" in Choset App. F.4 and MR Fig. 10.6.
- Vgraph caveats: grazing paths, and non-optimal, incomplete behaviour in 3-D.
- The week03 slides credit C-space to "Lozano-Pérez 1979" (the CACM paper with Wesley). **This 1983 paper is the one that defines $CO_A(B)$ and proves the Minkowski characterisation.**

---

## 4. Kavraki, Švestka, Latombe, Overmars 1996: PRM (15 pp.): Lesson A5

`prmbasic_01.pdf` is the **original journal paper** (IEEE T-RA 12(4):566–580), scanned without a text layer. All 15 pages are legible. It is not lecture slides.

**Big idea.** Spend a **learning phase** building a graph (the probabilistic roadmap) in two steps:
1. **construction**: random free configurations joined by a fast, deterministic **local planner**;
2. **expansion**: extra nodes in "difficult" regions, picked by a heuristic.

After that, each **query** only connects start and goal to the graph and searches it, which takes a fraction of a second. The method targets many-dof holonomic robots and accepts weaker guarantees than exact methods (pp. 1–3, J566–568).

### Definitions (§III, p. 4, J569)
- Roadmap $R=(N,E)$, undirected. Nodes are free configurations. An edge $(a,b)$ means the local planner connects $a$ and $b$.
- **Local paths are not stored.** They are recomputed at query time, which is possible because the planner is deterministic.
- $\mathcal C_f$ is the free C-space.
- $\Delta:\mathcal C_f\times\mathcal C_f\to\{0,1\}$ says whether the local planner succeeds.
- $D:\mathcal C\times\mathcal C\to\mathbb R^+\cup\{0\}$ is the distance function (a symmetric, non-degenerate pseudo-metric).

### Learning phase, construction step (p. 4, J569)
```
(1) N ← ∅ ; (2) E ← ∅
(3) loop
(4)   c ← a randomly chosen free configuration          # uniform per-dof sampling, collision-checked
(5)   N_c ← candidate neighbours of c chosen from N
(6)   N ← N ∪ {c}
(7)   for all n ∈ N_c in order of increasing D(c,n) do
(8)     if ¬same_connected_component(c,n) ∧ Δ(c,n) then
(9)        E ← E ∪ {(c,n)}
(10)       update R's connected components            # union-find; no graph search needed
```
- **Neighbours (p. 5, J570):** $N_c=\{\tilde c\in N\mid D(c,\tilde c)\le\texttt{maxdist}\}$, capped at **maxneighbors** (≈30). This makes each iteration's cost independent of $|N|$, so local-planner calls grow linearly in the graph size.
- **Skipping same-component neighbours** means the roadmap is a **forest**, with no cycles. This saves time but can give long query paths, which smoothing fixes (p. 4).
- **General local planner (p. 5, J570).**
  - Use a straight line in C-space with a joint-limit check.
  - Discretize it so that no point of the robot moves more than `eps` between consecutive configurations.
  - Test each configuration with the robot **"grown" by eps**. If all are free, the *continuous* segment is free.
  - Requirements: deterministic and very fast. A powerful but slow planner gives a smaller roadmap but costs more at query time.
- **Distance (eq. 1, p. 5, J570):** $D(c,n)=\max_{x\in\text{robot}}\|x(n)-x(c)\|$, a cheap proxy for the swept region. It should reflect the chance that the local planner *fails*.

### Learning phase, expansion step (p. 6, J571)
- The goal is to improve connectivity in narrow regions where the construction step left several components.
- Each node gets a weight $w(c)$, and nodes are sampled with $\Pr(c)=w(c)$. Possible weights:
  - inverse of the number of nodes nearby;
  - inverse of the distance $d_c$ to the nearest other component;
  - **the one used:** failure ratio $r_f(c)=\frac{f(c)}{n(c)+1}$, where $n(c)$ = local-planner attempts at $c$ and $f(c)$ = failures (each failure counts for **both** endpoints), then $w(c)=r_f(c)/\sum_{a\in N}r_f(a)$.
- **Expand** $c$ with a **random-bounce walk**:
  - pick a random C-space direction, move until collision, and repeat for $T_{RB\_expand}$;
  - add the final node $n$ and the edge $(c,n)$, **storing that path explicitly** (it is non-deterministic);
  - then try to connect $n$ to the *other* components as in the construction step.
- The expansion step never creates new components.
- Weights are computed once, at the start of expansion.
- Afterwards, discard components smaller than `mincomponent` percent of $|N|$.
- **Budget:** $T_C=2T_L/3$ and $T_E=T_L/3$ (a 2:1 ratio) works across scenes.

### Query phase (pp. 6–7, J571–572)
1. Connect $s$ and then $g$ to the roadmap. Try nodes in increasing $D$ (ignoring nodes beyond maxdist) with the local planner until one succeeds.
2. If that fails, run up to $N_{RB\_query}$ random-bounce walks of duration $T_{RB\_query}$, trying to connect each walk's endpoint (which is **not** added to $R$).
3. With several components, try to connect $s$ and $g$ to the **same** component, closest first. Report failure if impossible.
4. Search $R$ (breadth-first is enough).
5. Concatenate: $P_s$, recomputed local paths (stored paths for random-bounce edges), and $P_g$ reversed.
6. Optionally smooth (random shortcutting via the local planner, or corner cutting).
- No re-checking of recomputed local paths is needed if the planner aborts on the first collision.
- Frequent query failures mean $T_L$ is too small. Learning can resume on the existing roadmap (the phases can interleave).

### Customization for planar articulated robots (§IV, pp. 7–8, J572–573)
- **Robot.** $q$ revolute joints with limits $[low_i,up_i]$. A free base adds $(x,y)$ and $\theta\in[0,2\pi]$. Self-collision of non-adjacent links is forbidden.
- **Specific local planner.**
  - Odd joints $J_{2i-1}$ move in straight workspace lines.
  - Even joints follow by 2-link inverse kinematics.
  - If $q$ is even, the last joint rotates at constant rate.
  - Abort on collision, joint-limit violation or IK failure.
  - If it ends near $b$ at $b'$, try a straight C-space line $b'\to b$.
  - It sweeps less area than the C-space straight line, so it succeeds more often, but it is slower (it needs IK).
- **Distance:** $D(a,b)=\sum_{i=1}^{q+1}\|J_i(a)-J_i(b)\|^2$, using workspace joint positions.
- **Collision checking:** each link is its own 3-dof robot with a precomputed 3-D C-space **bitmap** (stacked 2-D slices built via Lozano-Pérez [37] or FFT [24]). A 128³ bitmap takes a few seconds; a check is then $O(1)$. One bitmap serves all links if long links are split into short ones. This is impractical in 3-D (it would need 6-D bitmaps).

### Parameters (§V, p. 9, J574)
| Parameter | Meaning | Value |
|---|---|---|
| $T_C$, $T_E$ | construction / expansion time | $T_E=T_C/2$ |
| `maxdist` | max $D$ for a connection attempt | 0.4 (customized), 0.5 (general); workspace scaled to the unit square |
| `eps` | path discretization | 0.01 |
| `maxneighbors` | local-planner calls per node | 30 |
| $T_{RB\_expand}$ | random-bounce duration, expansion | 0.01 s |
| $T_{RB\_query}$, $N_{RB\_query}$ | random-bounce duration and count, query | 0.05 s, 45 |
| `mincomponent` | small-component threshold | "typically 0.01%" (p. 6) |

### Experiments and numbers worth remembering
- **Setup.** DEC Alpha (126.0 SPECfp92) for the customized version, written in C.
  - Test sets are 8 hand-picked hard configurations $C_1..C_8$.
  - 30 independent roadmaps per row; keep only the largest component; a connection must succeed within 2.5 s.
- **Scene 1: fixed-base 7-revolute robot with narrow gates** (Fig. 2).
  - With expansion (Fig. 3, p. 10, J575): $T_L=20.1$ s, 1062 nodes, success 36.7–100 %. $T_L=60.2$ s, 3284 nodes, ~1.63 M collision checks, **100 % for all 8**. $T_L=80.4$ s, 4272 nodes, 100 %.
  - Without expansion (Fig. 4): at 20 s some configurations are at 10–13 %; at 60 s still 90 % for $C_2$, $C_4$, $C_7$.
  - Conclusion: spend part of $T_L$ on expansion.
- **Single roadmaps** (Fig. 5): connecting a test configuration usually takes tens to hundreds of collision checks, occasionally thousands. Learning needs 2–3 orders of magnitude more checks than queries.
- **RPP comparison.** RPP needs "a few tens of minutes" per query in scene 1, so PRM is faster even counting learning there. In easier scenes RPP is as fast or faster (p. 11, J576).
- **Scene 2: free-base 5-joint robot, 7 dof** (Figs. 6–8): with expansion, 100 % at $T_L=70.1$ s; without expansion, 100 % only at 80.2 s.
- **General implementation** (§VI, pp. 12–13, J577–578): SGI Indigo², straight-line planner, eq. (1) distance, PLAGEO collision checking.
  - 4-dof scene: 93.3 % at $T_L=5$ s, 100 % at 7.5 s.
  - 5-dof scene: 87 % at 5 s, 100 % at 10 s.
  - The 7-dof scene of Fig. 2 needs about **25 min** for 100 % (Fig. 12). Customization matters.

### Proved vs only claimed
- **No completeness theorem in this paper.** It says the roadmap "is never guaranteed to fully represent free C-space connectivity, though if we let our techniques run long enough it eventually will (but we don't know how long is enough)" (p. 3, J568). Theory is deferred to refs. [4], [26], [30].
- The $\lceil2L/\rho\rceil e^{-\sigma\rho^dn}$ bound on the week05/06 slides comes from that later analysis (Kavraki, Kolountzakis, Latombe, ref. [26]; Choset ch. 7), **not** from this paper.
- Everything else (the 2:1 ratio, parameter robustness, query speed) is empirical.

### Differences from the slides and README
- **Construction.**
  - week05 p. 25 "The Complete Algorithm": adds $q_{init},q_{goal}$ to $V$ first (single-query style) and connects every neighbouring pair, so cycles are allowed.
  - The paper: adds $s,g$ only at query time, skips same-component pairs (a **forest**), and uses a `maxdist` ball capped at 30 nodes sorted by $D$.
  - README §5.4: "kNN or r-ball", all pairs, plus "accessibility/departability". It has no expansion step, no random-bounce query fallback and no small-component pruning.
- **Expansion weights.**
  - Paper: failure ratio $r_f$.
  - Choset's connection sampling (choset-part2): $1/(\deg+1)$.
  - EST in Assignment 3: $1/(1+\deg)$.
  - Do not mix these up.
- **Edge check.**
  - Slides: midpoint subdivision on points only, which can miss thin obstacles.
  - Paper: `eps` discretization **with the robot grown by eps**, which makes the edge check conservative.

### Exercises worth doing (build items)
1. Paper-faithful PRM for a planar $q$-link arm (start with $q=4$ in a unit-square workspace): forest construction with union-find, `maxdist`, `maxneighbors`=30, distance eq. (1), and an eps-grown edge check.
2. Add the expansion step (failure ratio, random-bounce walk with a stored path). Reproduce the Fig. 3 vs Fig. 4 comparison: success rate vs $T_L$ on 8 hand-picked narrow-gate configurations, 30 seeds each.
3. Query phase with the random-bounce fallback and shortcut smoothing. Measure collision checks for learning vs queries (expect a 100–1000× ratio).
4. Swap $D$: eq. (1) vs $\sum\|J_i(a)-J_i(b)\|^2$ vs plain joint-space Euclidean. Compare success at a fixed $T_L$.
5. Optional: the specific odd/even-joint local planner with 2-link IK.

### Pitfalls
- A forest is fine for connectivity but gives long paths, so smooth them.
- Random-bounce edges must store their paths. Deterministic edges must not, or memory explodes.
- Normalise the workspace (the paper scales it to the unit square) so that `maxdist` and `eps` mean something.
- Revolute joints with free rotation ($\theta\in[0,2\pi]$) need wrap-aware interpolation and distance.
- Hand-picked test configurations matter. Uniformly random queries are easy and overstate performance (p. 9).

### Errors found (verified)
1. **p. 10 (J575):** the text says the only failures at $T_L=70.3$ s were "$C_3$, $C_4$ and $C_7$". The Fig. 3 row shows **$C_2$, $C_4$, $C_7$** at 96.7 %, with $C_3$ at 100 %.
2. **pp. 7, 9 (J572, J574):** "Subsection III-B-1" is cited for local-path discretization and random-configuration generation. Both are in **III-A-1** (the construction step); III-B (query phase) has no subsections.
3. **Suspicious, unconfirmed:** "mincomponent percent (typically 0.01%)" (p. 6, J571). With about 4000 nodes that is under one node, so nothing would ever be discarded. A fraction of 0.01 (1 %) was probably meant.

### Gaps this fills in the knowledge base
- The **expansion step**: failure-ratio weights and random-bounce walks.
- The $T_C:T_E=2:1$ budget.
- The query-phase random-bounce fallback and multi-component handling.
- The forest / union-find construction and the `maxneighbors` cap (which makes cost linear).
- The **eps-grown conservative edge check**.
- A swept-distance metric, eq. (1).
- Concrete parameter values and the expansion vs no-expansion evidence.
- Attribution: the slides' completeness bound is from the follow-up analysis, not this paper.

---

## Cross-paper summary for the README (paste-ready)
- **[A2]** Park–Martin LS $R_X=(M^TM)^{-1/2}M^T$ with $M=\sum\beta_i\alpha_i^T$ matches the paper (p. 4, J720). It needs **≥3 motions whose rotation axes are not coplanar**, because $M$ must have rank 3. For exactly 2 motions with non-parallel axes, use $R_X=[\alpha_1\,\alpha_2\,\alpha_1{\times}\alpha_2][\beta_1\,\beta_2\,\beta_1{\times}\beta_2]^{-1}$ (eq. 4).
- **[A2]** Reject pairs whose robot and sensor rotation angles differ ($\|\alpha_i\|\ne\|\beta_i\|$, Lemma 5), and pairs with $\theta\approx0$ or $\theta\approx\pi$.
- **[A6]** MonoSLAM state:
  - 13-D camera $[\mathbf r^W,\mathbf q^{WR},\mathbf v^W,\boldsymbol\omega^R]$ plus 3-D points;
  - constant-velocity model with acceleration noise (σ ≈ 10 m/s², 6 rad/s²);
  - pinhole + $1/\sqrt{1+2K_1r^2}$ distortion;
  - $3\sigma$ gating on $S_i$, measuring the 10–12 largest-$S_i$ features;
  - depth by 100 particles on 0.5–5 m (inverse depth is the later Montiel et al. method);
  - known target fixes scale;
  - ~100 features at 30 Hz.
- **[A3/A4]** C-obstacle $=B\oplus(-A_0)$ depends on the reference point. Containers use $CI_A(R)=\overline{CO_A(\overline R)}$. Costs: non-convex $O(nm)$; polyhedra $O(n^2\log n)$; rotating polygons $O(nm)$ critical angles. θ-slices are C-obstacles of swept areas, which is conservative and incomplete.
- **[A5]** Kavraki 1996 PRM:
  - forest construction (skip same-component neighbours), `maxdist` + `maxneighbors`≈30;
  - expansion with failure ratio $f/(n+1)$ and random-bounce walks;
  - $T_C:T_E=2:1$;
  - query by nearest-first connection with a random-bounce fallback;
  - eps-grown edge checks;
  - no completeness proof in this paper.

## Best-reference map
- **[A1]** Park–Martin Lemmas 1–4 (p. 2, J718): closed-form SO(3)/SE(3) exp and log, with the $A$ and $A^{-1}$ matrices.
- **[A2]** Park–Martin:
  - pp. 2–3 (J718–719): Lemma 5, Thms 1–2, eqs. (2)–(6);
  - p. 4 (J720): least squares and translation;
  - pp. 4–5 (J720–721): numeric example (fix $B_2$) and noise study;
  - the slides (lecture03 pp. 6–15) for the absolute-pose → relative-motion step.
- **[A3]**
  - Lozano-Pérez pp. 2–5 (J109–112): definitions, Thms 1–3, Minkowski merge with Figs. 4–7.
  - App. I (pp. 10–11, J117–118): listing, with the two fixes.
  - §III (pp. 5–6): rotation.
  - §VI + App. II (pp. 7–8, 11–12): slices.
  - Kavraki §IV-3 (p. 8, J573): C-space bitmaps built from Lozano-Pérez obstacles.
- **[A4]**
  - Lozano-Pérez p. 3 (J110): visibility-graph Findpath and its caveats.
  - p. 7 (J114): 3-D Vgraph not complete or optimal.
  - Complexity of Vgraph construction: Choset ch. 5, not this paper.
- **[A5]** Kavraki:
  - pp. 4–5 (J569–570): construction, local planner, neighbours, distance;
  - p. 6 (J571): expansion and 2:1 budget;
  - pp. 6–7 (J571–572): query;
  - pp. 9–13 (J574–578): parameters and results;
  - the completeness bound: week05/06 slides + Choset ch. 7.
- **[A6]** MonoSLAM:
  - p. 4 (J1055): state;
  - p. 6 (J1057): motion model;
  - pp. 6–7 (J1057–1058): measurement, $S_i$, active search;
  - pp. 7–8 (J1058–1059): initialization;
  - p. 9 (J1060): map management;
  - pp. 12–14: HRP-2, ground truth, timing;
  - pair with week08 pp. 37–46 using the mapping table above.
- **[A7]** MonoSLAM §3.6 (pp. 7–8): a small particle filter (Bayes reweighting of depth hypotheses) inside an EKF. A good bridge example between A6 and A7.
- **[A8]** Exam-style items:
  - derive $\alpha=\Theta_X\beta$ and Thm 2's rank argument;
  - hand-compute $B\oplus(-A)$ for a triangle and a square by merging normals;
  - write the MonoSLAM $F_v$ block structure;
  - write PRM construction/expansion/query pseudocode with the forest rule.
