# Murray, Li, Sastry — *A Mathematical Introduction to Robotic Manipulation* (MLS, 1994) — Part 2
Source: 09eb9223-mls94-complete.pdf, assigned PDF pages 241–474 (book pp. 223–456 + index).
**Page offset: book page = PDF page − 18** (e.g. book p. 223 = PDF p. 241).

**What the assigned range actually is (from the TOC, PDF pp. 7–11):** PDF 241 starts mid-Chapter 5
(§3 Force-Closure). So the range is: Ch. 5 §3–9 (force-closure, grasp planning, grasp constraints, rolling
contact), Ch. 6 Hand Dynamics & Control (constrained Lagrange, hand dynamics, redundancy, tendons, hand
control), Ch. 7 Nonholonomic behavior (Lie brackets, Frobenius, Chow), Ch. 8 Nonholonomic motion planning,
Ch. 9 Future prospects, Appendix A (manifolds, Lie groups, geometry of SE(3)), Appendix B (Mathematica
screw package), Bibliography, Index.

**Ch. 4 (Robot Dynamics & Control: Lagrange, M/C/N, Lyapunov, PD, computed torque, workspace control)
is book pp. 155–210 = PDF 173–228, i.e. NOT in this range.** Because the brief asks for R7/R8 to be checked
against the book, I also read Ch. 4 in full and add a compact "cross-check" section on it (clearly marked)
plus the start of Ch. 5 (§1–2 contact models / grasp map, PDF 229–240) because force-closure is meaningless
without it. A Part-1 reader may duplicate these; this version is written to verify R7/R8 equations.

---

## Ch. 5 §1–2 (context, outside range) — Contact models & the grasp map (PDF 229–240, book 211–222) — Lessons: R5 (statics/wrenches), R2 (adjoint)
**Big idea:** A finger touching an object can only push in certain directions. Model each contact as a
*wrench basis* $B_{c_i}$ (which wrenches it can apply, in the contact frame, z = inward normal) plus a
*friction cone* $FC_{c_i}$ (which coefficients are allowed). Map every contact wrench to the object frame
with the transpose-adjoint and stack: that is the **grasp map** $G$. Everything about grasping is then
linear algebra/convexity on $G$ and $FC$.

**Key definitions / equations**
- Contact frame $C_i$ located by $g_{oc_i}=(p_{oc_i},R_{oc_i})$ relative to the object frame $O$ (object
  reference point = centre of mass). Object config $x_o=g_{po}$ w.r.t. palm $P$.
- Frictionless point contact (5.1): $F_{c_i}=(0,0,1,0,0,0)^T f_{c_i}$, $f_{c_i}\ge0$ (push, never pull).
- Coulomb friction (5.2): $|f^t|\le\mu f^n$; cone half-angle $\alpha=\tan^{-1}\mu$. Table 5.1: steel/steel 0.58,
  rubber 1–4, typical $\mu<1$ so cone < 45°.
- Point contact with friction (5.3): $B_{c_i}=[e_1\ e_2\ e_3]$ (6×3), $FC=\{\sqrt{f_1^2+f_2^2}\le\mu f_3,\ f_3\ge0\}$.
- Soft finger (5.4): adds torque about the normal, $B=[e_1\ e_2\ e_3\ e_6]$, extra $|f_4|\le\gamma f_3$
  (γ = torsional friction coeff).
- General (5.5): $F_{c_i}=B_{c_i}f_{c_i}$, $f_{c_i}\in FC_{c_i}$; $FC$ closed, non-empty interior, closed under
  positive combinations (a convex cone). Table 5.2 lists the three models.
- Contact map $G_i = \mathrm{Ad}^T_{g_{oc_i}^{-1}}B_{c_i}$ where
  $\mathrm{Ad}^T_{g_{oc}^{-1}}=\begin{bmatrix}R_{oc}&0\\ \hat p_{oc}R_{oc}&R_{oc}\end{bmatrix}$ (wrench transformation,
  MLS wrench order = (force; moment)).
- **Grasp map (5.6)–(5.7):** $G=[\mathrm{Ad}^T_{g_{oc_1}^{-1}}B_{c_1}\ \cdots\ \mathrm{Ad}^T_{g_{oc_k}^{-1}}B_{c_k}]\in\mathbb R^{p\times m}$,
  $F_o=Gf_c$, $f_c\in FC=FC_{c_1}\times\dots\times FC_{c_k}$, $m=\sum m_i$. Def 5.1: a grasp = $(G,FC)$.

**Worked examples worth redoing**
- Ex 5.1 (p. 220): frictionless contacts → columns $(n_{c_i};\ p_{c_i}\times n_{c_i})$ — the wrench of a pure
  force along a line (a zero-pitch screw). Best quick check of the $\mathrm{Ad}^T$ formula.
- Ex 5.2 (pp. 220–221): box with two soft fingers at $(0,\mp r,0)$, 6×8 $G$ written out. Redo it by hand.
- Ex 5.3 (p. 222): planar rectangle, SE(2) wrench transform $\begin{bmatrix}f_o\\\tau_o\end{bmatrix}=
  \begin{bmatrix}R&0\\ [-p_y\ p_x]R&1\end{bmatrix}\begin{bmatrix}f_c\\\tau_c\end{bmatrix}$; planar convention puts the
  normal on the contact **y**-axis.

**Pitfalls:** MLS wrenches are $(f;\tau)$ (force first) like twists $(v;\omega)$; the transform is
$\mathrm{Ad}^T_{g^{-1}}$, not $\mathrm{Ad}_g$. Contact z-axis = *inward* normal here, but in §6 (rolling) the
Gauss frame uses the *outward* normal (footnote p. 246).

## Ch. 5 §3 Force-Closure (PDF 241–247, book 223–229) — Lessons: R5 (statics), C8/ASBR background
**Big idea:** A grasp is force-closure if the fingers can cancel *any* external wrench without leaving the
friction cones. The algebraic key is **internal forces**: contact forces in the null space of $G$ that
squeeze without moving the object. If you can squeeze strictly inside the cones and $G$ is onto, you can
always add enough squeeze to push any solution back inside the cone.

**Key definitions & results**
- Def 5.2: force-closure ⇔ ∀$F_e$ ∃$f_c\in FC$ with $Gf_c=-F_e$. **Prop 5.1:** ⇔ $G(FC)=\mathbb R^p$.
- Def 5.3: internal force $f_N\in\mathcal N(G)\cap FC$; *strictly* internal if $f_N\in\operatorname{int}FC$.
  Planar Ex 5.3 has $f_N=(0,1,0,1)$ (fingers squeeze).
- **Prop 5.2 (p. 224):** force-closure ⇔ $G$ surjective **and** ∃ $f_N\in\mathcal N(G)\cap\operatorname{int}(FC)$.
  Proof idea: any $f'_c$ with $Gf'_c=F_o$; $(f'_c+\alpha f_N)/\alpha\to f_N\in\operatorname{int}FC$, so for big α
  $f'_c+\alpha f_N\in FC$ and still maps to $F_o$.
- Positive linear algebra (p. 225): positively dependent ($\sum\alpha_iv_i=0$, $\alpha_i>0$); positively
  spanning; convex hull $co(S)=\{\sum\alpha_iv_i:\sum\alpha_i=1,\alpha_i\ge0\}$; separating / supporting
  hyperplane $H_v(c)=\{x: v^T(x-c)=0\}$.
- **Prop 5.3 (p. 226)** (frictionless point contacts only) equivalent: (1) force-closure; (2) columns of $G$
  positively span $\mathbb R^p$; (3) $co\{G_i\}$ contains a neighbourhood of 0; (4) no $v\ne0$ with
  $v\cdot G_i\ge0\ \forall i$.
- Friction contacts in the plane (Ex 5.5): replace each contact by its two cone *edges* $G'_i$ with
  $FC'=\{f\ge0\}$ → same as two frictionless contacts, so Prop 5.3 applies. In 3D the circular cone must be
  approximated by a polyhedral cone (Fig 5.10) — inscribed approximation gives a sufficient (not necessary)
  test.

**Algorithm (condition 4 test, p. 226):** for every set of $p-1$ independent columns of $G$, take $v\perp$
them; if for some $v$ all remaining $v\cdot G_j$ have the same sign (all ≥0 or all ≤0) → *not* force-closure;
if every candidate $v$ yields mixed signs → force-closure.

**Worked examples:** Ex 5.4 (pp. 226–228) planar box with 4 frictionless contacts: enumerate $v_{ij}$ for
all 6 column pairs — redo it; then the degenerate case $a=b=0$ (all normals through the centre) can't resist
torque, $v=(0,0,1)$ kills every column.

**Pitfalls:** "Force-closure" is MLS's name for *wrench* closure (footnote). Positive-dependence needs
strictly positive coefficients.

## Ch. 5 §4 Grasp Planning (PDF 247–252, book 229–234)
**Big idea:** How many fingers do you need, and where should they go? Convexity theorems give counts;
for two frictional fingers in the plane, a purely geometric "antipodal" test gives the answer.
- Exceptional surface: $co(\Lambda(\Sigma))$ of all frictionless contact wrenches doesn't contain a nbhd of
  0 → can never be grasped frictionlessly (sphere, circle).
- **Thm 5.4 Carathéodory:** positive span of $\mathbb R^p$ needs $k\ge p+1$. **Thm 5.5 Steinitz:** at most $2p$.
  Frictionless: planar $4\le k\le6$, spatial $7\le k\le12$ (lower bound achievable in the plane, and for
  polyhedra / smooth objects in 3D).
- **Table 5.3 (p. 231)** minimum fingers: planar — FPC 4 (non-exc.), PCWF 3, SF 3; spatial — FPC 7 (12 upper),
  PCWF 4, SF 4. Lower bound for PCWF exhibited with a regular tetrahedron & $\mu<\tan19.5°$.
- **Thm 5.6 planar antipodal (p. 232):** two point contacts with friction are force-closure ⇔ the segment
  joining the contacts lies inside both friction cones. Linear tests A–D with the 2-D cross product
  $a\otimes b=\det[a\ b]$, $n^\perp$ s.t. $n\otimes n^\perp=+1$:
  $(n_1\mp\mu n_1^\perp)\otimes(p_2-p_1)\gtrless0$, $(n_2\mp\mu n_2^\perp)\otimes(p_1-p_2)\gtrless0$.
  Plot over edge-parameter pairs (Fig 5.13, equilateral triangle) and pick the maximal square =
  *independent contact regions*.
- **Thm 5.7 spatial:** two **soft-finger** contacts are force-closure ⇔ same line-inside-cones condition.

## Ch. 5 §5 Grasp Constraints (PDF 252–260, book 234–242) — Lessons: R5 (Jacobians, statics), R2 (adjoint)
**Big idea:** Add the finger kinematics. Each finger is a little open-chain robot; contact means the finger
tip and the object point must agree in exactly the directions the contact can push in. Velocity constraint
and force map are transposes of each other — the same duality as $J$/$J^T$ in Ch. 3.

**Key results**
- Constraint (5.9): $B_{c_i}^T V^b_{f_ic_i}=0$ (no relative motion in force-transmitting directions).
- Via adjoint identities (5.10)–(5.12) and $\mathrm{Ad}^{-1}_{g_{pc_i}}\mathrm{Ad}_{g_{pf_i}}\mathrm{Ad}_{g_{s_if_i}}=\mathrm{Ad}^{-1}_{g_{s_ic_i}}$:
  **(5.13)** $B_{c_i}^T\mathrm{Ad}^{-1}_{g_{s_ic_i}}J^s_{s_if_i}(\theta_{f_i})\dot\theta_{f_i}=G_i^TV^b_{po}$.
- Hand Jacobian $J_h=\mathrm{blockdiag}(B_{c_i}^T\mathrm{Ad}^{-1}_{g_{s_ic_i}}J^s_{s_if_i})\in\mathbb R^{m\times n}$.
- **Fundamental grasping constraint (5.15):** $J_h(\theta,x_o)\dot\theta=G^TV^b_{po}$.
- Forces: $F_o=Gf_c$, **$\tau=J_h^Tf_c$ (5.16)** (virtual work). Fig 5.15 diagram:
  $\dot\theta\xrightarrow{J_h}\dot x_c\xleftarrow{G^T}V_{po}^b$, $\tau\xleftarrow{J_h^T}f_c\xrightarrow{G}F_o$.
- Def 5.5 / **Prop 5.8:** manipulable ⇔ $\mathcal R(G^T)\subset\mathcal R(J_h)$. A force-closure grasp is
  manipulable ⇔ $J_h$ surjective (summary p. 257).
- **Table 5.4 (p. 239)**: force-closure $G(FC)=\mathbb R^p$; manipulable $\mathcal R(G^T)\subset\mathcal R(J_h)$;
  internal forces $\mathcal N(G)\cap\operatorname{int}FC$; internal motions $\dot\theta_N\in\mathcal N(J_h)$;
  structural forces $G^+F_I\in\mathcal N(J_h^T)$ (forces the joints can't feel; set by mechanism elasticity).
  Force-closure and manipulability are independent (Fig 5.16).

**Worked example:** §5.3 two SCARA fingers grasping a box (pp. 240–242): reuse Ex 5.2's $G$ and the SCARA
spatial Jacobian (Ex 3.8), compute $\mathrm{Ad}^{-1}_{g_{s_ic_i}}$ by inspection, get $J_h$; the grasp is
force-closure but **not manipulable** (rotation about object y gives $G^T e\notin\mathcal R(J_h)$), and has
internal motions (spin of the last joint about the contact). Great R2/R5 drill: adjoint + Jacobian + rank.

## Ch. 5 §6 Rolling Contact Kinematics (PDF 260–273, book 242–255) — brief (no RDKDC lesson)
**Big idea:** Real fingertips roll. Describe each surface with an orthogonal chart $c(u,v)$; the
*geometric parameters* (metric $M$, curvature $K$, torsion $T$) let you write ODEs for how contact points
migrate over both surfaces given the relative twist.
- First fundamental form $I_p=[c_u\ c_v]^T[c_u\ c_v]$ (5.17), metric $M_p=\mathrm{diag}(\|c_u\|,\|c_v\|)$ (5.18),
  Gauss map $N=c_u\times c_v/\|c_u\times c_v\|$ (5.19), second fundamental form (5.20), curvature tensor
  $K_p=M^{-T}II_pM^{-1}$ (5.21), normalized Gauss frame (5.22), torsion form $T_p$ (5.24).
- Lemma 5.10: body velocity of the contact frame $v=(M\dot\alpha;0)$, $\hat\omega$ built from $KM\dot\alpha$, $TM\dot\alpha$
  (5.25–5.26). Ex 5.6 sphere: $K=I/\rho$, $M=\mathrm{diag}(\rho,\rho\cos u)$, $T=[0,\ -\tan u/\rho]$.
- **Thm 5.11 (Montana's contact equations, 5.28):** $\dot\alpha_f=M_f^{-1}(K_f+\tilde K_o)^{-1}((-\omega_y,\omega_x)^T-\tilde K_o(v_x,v_y)^T)$,
  $\dot\alpha_o=M_o^{-1}R_\psi(K_f+\tilde K_o)^{-1}((-\omega_y,\omega_x)^T+K_f(v_x,v_y)^T)$,
  $\dot\psi=\omega_z+T_fM_f\dot\alpha_f+T_oM_o\dot\alpha_o$, $0=v_z$. Requires the relative curvature
  $K_f+\tilde K_o$ invertible (fails e.g. convex in equal-radius concave). Pure rolling ($v_x=v_y=\omega_z=0$):
  Cor 5.11.1 (5.38). Ex 5.7 sphere on plane (5.39) — this is the classic nonholonomic example reused in Ch. 8.
- Grasp with rolling (5.40)–(5.42): $J_h(\theta,x_o,\eta)\dot\theta=G^T(\eta)V^b_{po}$, $\dot\eta_i=A_i(\eta_i)V^b_{of_i}$.

**Exercises worth doing (pp. 259–263):** 1–3 (build $G$, test force-closure — best grasp-statics drill),
6 (prehensile ⇔ force-closure), 7 (prove antipodal thm), 11–12 (SCARA box grasp; differentiating the
holonomic constraint reproduces $J_h$ — links to R5 Jacobian derivation), 16–17 (rolling contact).

## Ch. 6 Hand Dynamics and Control (PDF 283–334, book 265–316) — Lessons: R7 (constrained Lagrange), R8 (control extension), A3 (nonholonomic background)
**Big idea:** Fingers + object are a set of robots tied together by *velocity* constraints. Instead of
hunting for minimal coordinates (often impossible), keep the redundant coordinates and add constraint
forces $A^T\lambda$ (Lagrange multipliers), or project the equations onto the allowed directions
(Lagrange–d'Alembert). The punchline for control: the constrained system again has the form
$\tilde M\ddot x+\tilde C\dot x+\tilde N=F$ with $\tilde M>0$ and $\dot{\tilde M}-2\tilde C$ skew, so **every Ch. 4
controller (PD, computed torque, their Lyapunov proofs) carries over verbatim** — then use the null space
of $G$ to choose squeezing forces.

### §1 Lagrange's equations with constraints (pp. 265–276)
**Key definitions**
- Holonomic constraint $h(q)=0$ (6.2), $\partial h/\partial q$ full row rank; constraint force $\Gamma=(\partial h/\partial q)^T\lambda$,
  does no work: $\Gamma\cdot\dot q=\lambda^T\frac{d}{dt}h=0$. (Scleronomic = time-invariant, rheonomic = time-varying.)
- **Pfaffian constraint** $A(q)\dot q=0$, $A\in\mathbb R^{k\times n}$ full row rank. For a hand $A=[J_h\ \ -G^T]$, $q=(\theta,x)$.
  *Integrable* if ∃$h$ with $A\dot q=0\Leftrightarrow\frac{\partial h}{\partial q}\dot q=0$ (same null space, not necessarily $A=\partial h/\partial q$);
  otherwise **nonholonomic**. Constraint forces $\Gamma=A^T\lambda$; d'Alembert's principle = constraints do no
  (virtual) work (OK for rolling; not for sliding with friction → add friction as external force).

**Key equations**
- (6.4) $\frac{d}{dt}\frac{\partial L}{\partial\dot q}-\frac{\partial L}{\partial q}+A^T\lambda-\Upsilon=0$.
- With $L=\tfrac12\dot q^TM\dot q-V$: **(6.5)** $M\ddot q+C\dot q+N+A^T\lambda=F$; differentiate $A\dot q=0$ →
  $A\ddot q+\dot A\dot q=0$ → **(6.6)** $\lambda=(AM^{-1}A^T)^{-1}\big(AM^{-1}(F-C\dot q-N)+\dot A\dot q\big)$.
- Virtual displacement $\delta q$: $A(q)\delta q=0$ (not the same as $\dot q$!). **Lagrange–d'Alembert (6.7)–(6.8):**
  $\big(\frac{d}{dt}\frac{\partial L}{\partial\dot q}-\frac{\partial L}{\partial q}-\Upsilon\big)\cdot\delta q=0$ for all $\delta q$ with $A\delta q=0$.
- Split $A=[A_1\ A_2]$, $A_2$ invertible → $\delta q_2=-A_2^{-1}A_1\delta q_1$ → **(6.9)**
  $(\tfrac{d}{dt}\tfrac{\partial L}{\partial\dot q_1}-\tfrac{\partial L}{\partial q_1}-\Upsilon_1)-A_1^TA_2^{-T}(\tfrac{d}{dt}\tfrac{\partial L}{\partial\dot q_2}-\tfrac{\partial L}{\partial q_2}-\Upsilon_2)=0$;
  then eliminate $\dot q_2,\ddot q_2$ with the constraint. Nonholonomic dynamics = 2nd-order ODE in reduced
  variables + 1st-order constraint ODEs.
- §1.4 (pp. 274–276): **never substitute a nonholonomic constraint into the Lagrangian** before applying
  Lagrange's equations — eq. (6.15) shows the spurious extra terms; they vanish iff the constraint is
  integrable (mixed partials commute).

**Worked examples**
- Ex 6.1 planar pendulum in Cartesian $(x,y)$ with $A=[x\ y]$ (pp. 270–271): gives rod tension as λ.
  **Book sign error (p. 271):** the example writes $\lambda=(AM^{-1}A^T)^{-1}(AM^{-1}(Q-C\dot q-N)-\dot A\dot q)$, i.e.
  with $-\dot A\dot q$, contradicting (6.6), and gets $\lambda=-\frac{m}{l^2}(gy+\dot x^2+\dot y^2)$. Using (6.6) correctly,
  $\dot A\dot q=\dot x^2+\dot y^2$ and $\lambda=\frac{m}{l^2}(-gy+\dot x^2+\dot y^2)$. Check at the bottom ($y=-l$) moving at
  speed $v$: centripetal balance needs $\lambda l=mg+mv^2/l$, which matches the corrected form, not the book's.
  So the rod tension is $\|(x,y)\lambda\|=\frac{m}{l}(\dot x^2+\dot y^2-gy)$ (= $mg+mv^2/l$ at the bottom), not the
  printed $\frac{mg}{l}y+\frac{m}{l}(\dot x^2+\dot y^2)$.
- Ex 6.2 rolling disk (pp. 272–274): $q=(x,y,\theta,\phi)$, constraints $\dot x=\rho\cos\theta\dot\phi$,
  $\dot y=\rho\sin\theta\dot\phi$ (6.10); result (6.14) $\mathrm{diag}(\mathcal I_1,\ \mathcal I_2+m\rho^2)(\ddot\theta,\ddot\phi)=(\tau_\theta,\tau_\phi)$.
  Typo: the printed "$\mathcal I_\infty$" in (6.12)/(6.14) is $\mathcal I_1$. Also note the text's labels are muddled: from
  the constraints and Fig 6.2, φ is the *rolling* angle and θ the heading; the text calls φ "orientation w.r.t.
  vertical" and τ_θ the "driving" torque — trust the equations, not the prose. Exercise 2 asks you to show
  substitution into $L$ gives the wrong answer — do it.

### §2 Robot hand dynamics (pp. 276–285)
- Fingers: $M_f\ddot\theta+C_f\dot\theta+N_f=\tau$ (6.16), block-diagonal stacking. Object: Newton–Euler in body
  frame at COM, $\begin{bmatrix}mI&0\\0&\mathcal I\end{bmatrix}\dot V^b+\begin{bmatrix}\omega^b\times mv^b\\ \omega^b\times\mathcal I\omega^b\end{bmatrix}=F^b$, with gravity (6.17).
  In local coords $M_o\ddot x+C_o\dot x+N_o=0$, $\dot M_o-2C_o$ skew (Ex 4).
- Assumptions: force-closure & manipulable, $J_h$ invertible, contact forces stay in $FC$.
- Lagrange–d'Alembert with $\delta\theta=J_h^{-1}G^T\delta x$ → (6.20) → **(6.21)–(6.22):**
  $\tilde M\ddot x+\tilde C\dot x+\tilde N=F$, $\tilde M=M_o+GJ_h^{-T}M_fJ_h^{-1}G^T$,
  $\tilde C=C_o+GJ_h^{-T}\big(C_fJ_h^{-1}G^T+M_f\frac{d}{dt}(J_h^{-1}G^T)\big)$, $\tilde N=N_o+GJ_h^{-T}N_f$, $F=GJ_h^{-T}\tau$.
- **Lemma 6.1:** $\tilde M$ symmetric p.d.; $\dot{\tilde M}-2\tilde C$ skew. (Proof: sum of skew pieces + a term that
  flips sign under transpose.) Can be rewritten on SE(3) with $V_o^b$ (6.23).
- Internal forces (§2.2): full equations (6.24) with $A=[-J_h\ \ G^T]$ show λ = contact forces; (6.25) gives λ;
  equivalently $\lambda=J_h^{-T}(\tau-M_f\ddot\theta-C_f\dot\theta-N_f)$ — dynamics can create internal forces;
  $\lambda=J_h^{-T}\tau$ only at rest.
- §2.3 other systems with the same structure $J(q)\dot\theta=G^T(q)\dot x$:
  *coordinated lifting* ($B_{c_i}=I$, $FC=\mathbb R^p$); **workspace dynamics (6.26)**: $G=I$,
  $\tilde M=M_o+J^{-T}M_fJ^{-1}$, $\tilde C=C_o+J^{-T}(C_fJ^{-1}+M_f\frac{d}{dt}J^{-1})$, $\tilde N=N_o+J^{-T}N_f$,
  $F=J^{-T}\tau$ — effective inertia blows up at singularities (parameterization artifact; joint-space
  dynamics never singular). *Hybrid position/force*: holonomic task constraint $h(\theta,x)=0$ →
  $J=\partial h/\partial\theta$, $G^T=-\partial h/\partial x$ (6.27)–(6.28); internal forces = torques with $GJ^{-T}\tau=0$ =
  forces against the surface (chalkboard example).

### §3 Redundant & nonmanipulable systems (pp. 285–293)
- Two redundancies: kinematic ($\mathcal N(J_h)$, internal motions) and actuator ($\mathcal N(G)$, internal forces).
- Redundant arm: rows of $K(\theta)$ span $\mathcal N(J)$, $v_N=K\dot\theta$, $\bar J=\begin{bmatrix}J\\K\end{bmatrix}$ invertible;
  dynamics in $(\ddot x,\dot v_N)$ with $\tilde M=\bar J^{-T}M\bar J^{-1}$. If $K$ rows orthonormal and ⟂ rows of $J$:
  $\bar J^{-1}=[J^+\ \ K^T]$, $J^+=J^T(JJ^T)^{-1}$ (right pseudo-inverse) — directly relevant to R6 null-space IK.
  $v_N$ is generally *not* the derivative of a coordinate (integrability: $\partial K_{ij}/\partial\theta_k=\partial K_{ik}/\partial\theta_j$).
- Ex 6.3 3-link planar arm, absolute link angles (pp. 287–289): $K=[0\ 0\ 1]$ is integrable ($h=\theta_3$);
  $K=[\sin(\theta_2-\theta_3),\ -\sin(\theta_1-\theta_3),\ \sin(\theta_1-\theta_2)]$ (∝ cross product of the two
  rows of $J$) is not. **Typo:** the third row of $\bar J$ printed on p. 289 has middle entry $\sin(\theta_3-\theta_2)$; it
  should be $\sin(\theta_3-\theta_1)=-\sin(\theta_1-\theta_3)$ to match $K$.
- Redundant grasp: $\bar J_h=\begin{bmatrix}J_h\\K_h\end{bmatrix}$, $\bar G^T=\mathrm{diag}(G^T,I)$ (6.30). Quasistatic forces
  (6.31) $\tau=[J_h^T\ K_h^T](f_c;f_N)$ only valid if no internal motion is excited.
- Nonmanipulable grasp: restrict object velocities to $W$, $\dot x=Hw$, (6.32)–(6.33) using $J_h^+=(J_h^TJ_h)^{-1}J_h^T$.
  Ex §3.3: SCARA box grasp made manipulable by removing rotation about the contact line (8 → 7 velocities).

### §4 Tendon actuation (pp. 293–300) — brief
- Extension functions $h_i(\theta)$ (linear for pulleys: $l_i\pm r_{ij}\theta_j$), **coupling matrix** $P=(\partial h/\partial\theta)^T$,
  $\dot e=P^T\dot\theta$, by energy $\tau=Pf$ (the tendon analogue of $\tau=J^Tf$ and of $G$).
- Elastic tendons: $f=K(e+h(0)-h(\theta))$; stiffness $S(\theta)=PK(h(\theta)-h(0))$; new coupling $Q=PK$ (Ex 6.5).
  (Typo p. 297: dynamics line is missing $\ddot\theta$ after $M(\theta)$.)
- Tensions must be positive → tendon force-closure ⇔ $P$ surjective and ∃ $f_N>0$ with $Pf_N=0$; Carathéodory/Steinitz
  give **N+1** and **2N** tendon designs. Force choice $f=P^+\tau+f_N$, $P^+=P^T(PP^T)^{-1}$.

### §5 Control of robot hands (pp. 300–311) — Lesson: R8
- Split into (1) track object trajectory, (2) regulate internal force. Because $\tilde M>0$, $\dot{\tilde M}-2\tilde C$ skew,
  any Ch. 4 controller works on the virtual input $F$. **Computed torque for constrained systems:**
  $F=M(q)(\ddot x_d-K_v\dot e-K_pe)+C\dot x+N$, $e=x-x_d$.
- Torque distribution **(6.36):** $\tau=J^TG^+F+J^Tf_N$, $G^+=G^T(GG^T)^{-1}$, $f_N\in\mathcal N(G)$ chosen to keep
  contact forces in $FC$ (critical for grasping; $f_N=0$ for coordinated lifting). Final law
  $\tau=J_h^TG^+F+J_h^Tf_{N,d}$ (ignoring dynamic constraint forces).
- Force feedback pitfall: pure proportional $f=\alpha(f-f_d)$ is an algebraic loop (ill-posed); use feedforward +
  integral $f=f_d+\alpha\int(f-f_d)dt$ (watch windup/noise).
- §5.2 hierarchical control: "robot" objects $(M,C,N)$ + I/O; **attach** (Lagrange–d'Alembert merge with
  $J=\partial h/\partial\theta$, $G=-\partial h/\partial x$; printed formula on p. 305 has $G$ where $G^T$ is meant) and **control**
  operations; biological analogy (spinal reflex ≈ PD at high rate, cerebellum ≈ computed torque); Ex 6.7
  two-finger planar hand: PD per finger → feedforward Cartesian → attach box → computed torque on box.
- Other typos noticed: (6.33) forcing written $G\bar J_h^{+T}\tau$, should be $\bar GJ_h^{+T}\tau$; summary item 2
  (p. 311) writes $\partial L/\partial\dot q_1$ inside the second bracket where $\partial L/\partial\dot q_2$ is meant.

**Exercises worth doing (pp. 314–315):** 2 (substitute-then-Lagrange gives wrong disk dynamics — conceptual
must), 4 (Newton–Euler has $M>0$, $\dot M-2C$ skew; with local coords $\bar M=J^TMJ$ — good R7 drill),
5 (four-bar via constraints), 7 (2-link arm against a wall: constraint, dynamics, internal forces, PD + force
control — best R8 hybrid control exercise).

## Ch. 7 Nonholonomic Behavior in Robotic Systems (PDF 335–372, book 317–354) — Lessons: A3 (C-space, car-like robots), F7 (flows/ODEs), R1/R2 (Lie bracket background)
**Big idea:** A velocity constraint $\omega_i(q)\dot q=0$ either secretly confines you to a surface
$h(q)=c$ (holonomic) or it doesn't (nonholonomic — a car can't slide sideways but can still parallel-park
anywhere). Flip the viewpoint from forbidden directions to *allowed* ones: $\dot q=\sum g_j(q)u_j$. Wiggling
back and forth along $g_1,g_2$ produces net motion along the **Lie bracket** $[g_1,g_2]$; if brackets fill all
of $T_q\mathbb R^n$, you can reach everything (Chow). Frobenius is the converse: closed under brackets ⇔
integrable ⇔ stuck on a leaf.

**Key definitions**
- Integrable / holonomic / nonholonomic / *completely* nonholonomic (reachable set unrestricted) (pp. 318–320).
  Single constraint is holonomic ⇔ ∃ integrating factor α with $\alpha\omega_j=\partial h/\partial q_j$ (7.4) ⇔ (7.5)
  $\partial(\alpha\omega_j)/\partial q_i=\partial(\alpha\omega_i)/\partial q_j$.
- Dual control system (7.6): $g_j$ basis of the right null space of the constraints, $\dot q=g_1u_1+\dots+g_mu_m$,
  $m=n-k$. **Prop 7.3:** such $g_j$ always exist (constructive proof via normal form); $\Delta=\Omega^\perp$.
- Vector field, Lie derivative $L_fV=\frac{\partial V}{\partial q}f$, flow $\phi^f_t$ with $\phi^f_t\circ\phi^f_s=\phi^f_{t+s}$ (p. 322–323).
- **Lie bracket** (p. 324): $[f,g](q)=\frac{\partial g}{\partial q}f-\frac{\partial f}{\partial q}g$. From the square flow
  $g_1,g_2,-g_1,-g_2$ for ε each (7.8): $q(4\epsilon)=q_0+\epsilon^2[g_1,g_2](q_0)+O(\epsilon^3)$ (Fig 7.1).
  Equivalent: $L_{[f,g]}\alpha=L_fL_g\alpha-L_gL_f\alpha$.
- Ex 7.1: linear fields $f=Aq$, $g=Bq$ → $[f,g]=(BA-AB)q$ (note the **sign**: minus the matrix commutator $[A,B]=AB-BA$).
- **Prop 7.1:** skew-symmetry; Jacobi $[f,[g,h]]+[h,[f,g]]+[g,[h,f]]=0$; chain rule
  $[\alpha f,\beta g]=\alpha\beta[f,g]+\alpha(L_f\beta)g-\beta(L_g\alpha)f$.
- Distribution $\Delta=\mathrm{span}\{g_i\}$ (regular = constant rank), involutive (closed under bracket), involutive
  closure $\bar\Delta$; Def 7.1 Lie algebra (bilinear, skew, Jacobi); $\bar\Delta=L(g_1,\dots,g_m)$ the
  controllability Lie algebra. One-forms (row vectors), exact forms $d\beta$, codistributions.

**Key results**
- **Thm 7.2 (Frobenius):** a regular distribution is integrable ⇔ involutive (integral manifolds = level sets of
  $n-k$ functions; foliation/leaves).
- **Prop 7.4:** Pfaffian constraints integrable ⇔ annihilating distribution involutive.
- Controllability (p. 329): controllable; small-time locally controllable via reachable sets $R^V(q_0,\le T)$.
- **Thm 7.5 (Chow):** drift-free $\dot q=\sum g_iu_i$ is locally controllable at $q$ if $\bar\Delta_q=T_q\mathbb R^n$
  (controllability rank condition). Proof via Props 7.6–7.7 (grow nested manifolds $N_k$; drift-free ⇒ can
  run any input backwards $u'(t)=-u(T/2-t)$). Non-constructive → Ch. 8.
- Filtration $\Delta_1=\Delta$, $\Delta_i=\Delta_{i-1}+[\Delta_1,\Delta_{i-1}]$; **degree of nonholonomy** κ (Def 7.2);
  **growth vector** $r_i=\mathrm{rank}\Delta_i$, relative growth $\sigma_i=r_i-r_{i-1}$ (Def 7.3).
- Philip Hall basis (§4.3): ordered basis of Lie products respecting skew-symmetry & Jacobi; nilpotent of order k
  = all products longer than k vanish. Ex 7.12 lists order-3 basis for 3 generators ($[g_1,[g_2,g_3]]$ omitted by Jacobi).

**Examples worth redoing (all used again in Ch. 8)**
| Example | $q$ | Growth vector | Notes |
|---|---|---|---|
| 7.2/7.7 hopping robot in flight (p. 333, 341) | $(\psi,l,\theta)$ | (2,3) | ang. momentum $I\dot\theta+m(l+d)^2(\dot\theta+\dot\psi)=0$ |
| 7.3/7.8 planar space robot (p. 334, 342) | $(\psi_1,\psi_2,\theta)$ | not regular ($g_3=0$ at $\psi_1=\psi_2$) but controllable | |
| 7.4/7.9 disk rolling on plane (p. 336, 342) | $(x,y,\theta,\phi)$ | (2,3,4) | |
| 7.5/7.10 **kinematic car** (7.14) (p. 337, 343) | $(x,y,\theta,\phi)$ | (2,3,4) | $\dot x=\cos\theta u_1,\ \dot y=\sin\theta u_1,\ \dot\theta=\frac1l\tan\phi\,u_1,\ \dot\phi=u_2$ |
| 7.6/7.11 sphere rolling on plane (7.19) (p. 338, 343) | $(\alpha_f,\alpha_o,\psi)\in\mathbb R^5$ | (2,3,5) | from Montana's equations |

Kinematic car brackets: $g_3=[g_1,g_2]=(0,0,-\frac{1}{l\cos^2\phi},0)$, $g_4=[g_1,g_3]=(-\frac{\sin\theta}{l\cos^2\phi},\frac{\cos\theta}{l\cos^2\phi},0,0)$ —
"wiggle" = rotate, "rotate-drive" = sideways slide: exactly the parallel-parking manoeuvre.

**Exercises worth doing (pp. 349–353):** 3 (bracket properties), 4 (time-reversal of drift-free systems),
6 (car with N trailers — classic A3 C-space example), 8–9, 14 (code a Philip Hall basis), 16 (systems with drift).

**Pitfalls / typos noticed**
- Lie bracket sign convention: MLS uses $[f,g]=\frac{\partial g}{\partial q}f-\frac{\partial f}{\partial q}g$ (some texts use the opposite).
  For linear fields this equals $(BA-AB)q$, *not* $(AB-BA)q$.
- Ex 7.7 (p. 341) drops an $m$: denominator should be $I+m(l+d)^2$ (as in Ex 7.2).
- Ex 7.3 (p. 335): $a_{13}=ml^2+mrl\cos\psi_1$, $a_{23}=ml^2+mrl\cos\psi_2$ — printed "$mr\cos\psi$" is missing an $l$
  (compare $a_{33}$'s $2mrl\cos\psi$ terms; dimensional check).
- Ex 7.10 (p. 343) says independence holds for $\phi\ne\pm\pi$; it should be $\phi\ne\pm\pi/2$ ($\tan\phi$, $1/\cos^2\phi$ blow up),
  as the next sentence itself says.
- Ex 7.4: the prose assigns $u_1=\dot\theta$ "rate of rolling", but in (7.13) $g_1=(\rho\cos\theta,\rho\sin\theta,0,1)$ means
  $u_1=\dot\phi$ (rolling) and $u_2=\dot\theta$ (turning). Trust the vector fields.

## Ch. 8 Nonholonomic Motion Planning (PDF 373–412, book 355–394) — Lessons: A3 (planning for car-like/nonholonomic robots), F6/F7 (calculus of variations, Hamiltonians)
**Big idea:** Chow says a path exists; Ch. 8 builds it. For "model" systems the optimal inputs are
**sinusoids at integrally related frequencies**: a sine/cosine pair traces a closed loop in the directly
controlled states, and the *area* of that loop is net motion along a bracket direction (rectification of
harmonics). Chained form generalises this; real systems (car, hopper, rolling finger) are coordinate-changed
or Taylor/Fourier-approximated into it. Holonomic planners (A3's PRM/RRT) produce paths that violate the
constraints unless combined with such local steering.

**Approaches surveyed (§1):** optimal control (bang-bang for minimum time with bounded inputs), piecewise
constant inputs (naive graph search grows as $m^d$; Lafferriere–Sussmann for nilpotent systems), canonical
paths (Li–Canny for rolling fingertip).

**§2 Model systems & sinusoids**
- **Brockett system (8.2):** $\dot q_1=u_1,\ \dot q_2=u_2,\ \dot q_3=q_1u_2-q_2u_1$; $[g_1,g_2]=(0,0,2)$, growth (2,3).
  Minimising $\int_0^1\|u\|^2$ with multiplier λ (8.3) → λ constant, $\dot u=\Lambda u$, $\Lambda=\begin{bmatrix}0&-\lambda\\\lambda&0\end{bmatrix}$ →
  $u(t)=e^{\Lambda t}u(0)$ (sinusoids). To go $(0,0,0)\to(0,0,a)$: $e^{\Lambda}=I$ ⇒ $\lambda=2n\pi$; $q_3(1)=-\|u(0)\|^2/\lambda$;
  cost $\|u(0)\|^2=-\lambda a$, min at $n=-1$: cost $2\pi a$, frequency $2\pi$ (or $2\pi/T$). (q₃ = signed area swept by $(q_1,q_2)$.)
- m-input generalisation (8.4)–(8.5) $\dot q=u$, $\dot Y=qu^T-uq^T$, $Y\in so(m)$; $u(t)=e^{\Lambda t}u(0)$, $e^{\Lambda t}\in SO(m)$;
  Brockett: frequencies $2\pi,2\cdot2\pi,\dots,(m/2)2\pi$. **Algorithm 1:** steer $q_i$ any way, then a closed sinusoidal
  loop to fix $q_{ij}$.
- Second order (8.6), **Algorithm 2:** steer $q_i$, then $q_{ij}$ with sinusoids, then a third pass with frequencies
  chosen so $\omega_i\pm\omega_j\ne0$ but $\omega_i+\omega_j+\omega_k$ has a zero component. For m=2:
  $u_1=a\sin2\pi t,\ u_2=b\cos4\pi t$ gives $\Delta q_{121}=-a^2b/(16\pi^2)$; swapped gives $\Delta q_{122}=a^2b/(32\pi^2)$.
  Max number of controllable $q_{ijk}$: $(m+1)m(m-1)/3$. Optimal inputs here are elliptic functions (Brockett–Dai).
- **Chained form (8.7)–(8.8):** $\dot q_1=u_1,\ \dot q_2=u_2,\ \dot q_{k}=q_{k-1}u_1$ ($k\ge3$). **Lemma 8.1:**
  $\mathrm{ad}^k_{g_1}g_2=(-1)^ke_{k+2}$. **Prop 8.2:** controllable. **Algorithm 3 / Prop 8.3:** steer $q_1,q_2$; then for each
  $q_{k+2}$ use $u_1=a\sin2\pi t,\ u_2=b\cos2\pi kt$: $q_{k+2}(1)-q_{k+2}(0)=\big(\frac{a}{4\pi}\big)^k\frac{b}{k!}$, earlier states return.
  (Dual of the Goursat normal form.)

**§3 General methods**
- Fourier techniques: Ex 8.1 hopping robot — change coordinates $\alpha=\theta+\frac{md^2}{md^2+I}\psi$ → near-chained
  $\dot\alpha=f(l)u_1$; with $u_1=a_1\sin2\pi t$, $u_2=a_2\cos2\pi t$: $\Delta\alpha=\tfrac12a_1\beta_1(a_2)$ (Fourier coeff.), solve numerically
  ("cartwheel" = 2π phase). Ex 8.2 car: $z=(x,\phi,\sin\theta,y)$, $v_1=\cos\theta u_1$, $v_2=u_2$; Fig 8.1 shows the A/B/C
  manoeuvre (drive, then a sine/cosine to fix θ, then $u_1=a_1\sin4\pi t,\ u_2=a_2\sin2\pi t$ to fix y) — Lissajous loops.
- Conversion to chained form (§3.2): sufficient conditions — $\Delta_0,\Delta_1,\Delta_2$ (spans of $g_2,\mathrm{ad}^i_{g_1}g_2$) constant
  rank & involutive, ∃$h_1$ with $dh_1\cdot\Delta_1=0$, $dh_1\cdot g_1=1$; then $z_1=h_1$, $z_n=h_2$, $z_{n-1}=L_{g_1}h_2$, …,
  $z_2=L^{n-2}_{g_1}h_2$. Ex 8.3 car: $h_1=x$, $h_2=y$, $z_3=\tan\theta$, $z_2=\frac1l\sec^3\theta\tan\phi$ (book prints $\sec^2$; see pitfalls).
- **Optimal steering (§3.3):** cost $\tfrac12\int\|u\|^2$, Hamiltonian $H=\tfrac12u^Tu+p^T\sum g_iu_i$ (8.11); stationarity
  → $u_i=-p^Tg_i$ (8.13), $H^*=-\tfrac12\sum(p^Tg_i)^2$ (8.14), Hamilton's equations (8.15). **Prop 8.4:** $\|u(t)\|$ constant;
  $\dot u=\Omega(q,p)u$, $\Omega_{ij}=p^T[g_i,g_j]$ skew (8.16) ⇒ $u(t)=U(t)u(0)$, $U\in SO(m)$. Ex 8.4 Engel's system
  (growth (2,3,4)): optimal inputs are elliptic functions (8.19). **Ritz approximation:** inputs as truncated Fourier
  series, minimise $\tfrac12\sum\|\alpha_i\|^2+\tfrac\gamma2\|q(1)-q_f\|^2$ (8.21) — a finite-dim optimisation (F6).
- **Piecewise constant inputs (§3.4, Lafferriere–Sussmann):** formal exponentials $e^{tg}$, Chen–Fliess series: every flow is
  $S_t=e^{h_sB_s}\cdots e^{h_1B_1}$ over a Philip Hall basis (8.22) — "bears more than a passing resemblance to the PoE
  formula" (R3 link); Chen–Fliess–Sussmann ODE $\dot h=Q(h)v$ (8.26) with fictitious inputs. Ex 8.5 (2 inputs,
  nilpotent deg 3, eq. 8.27), Ex 8.6 (5-state chain, $h\in\mathbb R^8$). **Thm 8.5 Campbell–Baker–Hausdorff:**
  $e^{g_1}e^{g_2}=e^{g_1+g_2+\frac12[g_1,g_2]+\frac1{12}([g_1,[g_1,g_2]]-[g_2,[g_1,g_2]])+\cdots}$ (8.29). The 4-segment
  $\sqrt\epsilon$ cycle (8.28) produces $h=(0,0,\epsilon,h_4(\epsilon))$. Non-nilpotent: only approximate.

**§4 Dynamic finger repositioning:** $\dot x=u_1$, $\dot\theta=J_h^+G^Tu_1+Ku_2$ (8.31) ($K$ spans $\mathcal N(J_h)$).
Spherical finger on plane (8.32) → near-two-chained form (8.33) steered numerically with sinusoids (Fig 8.3).
**Geometric phase:** rolling a sphere along a closed path on its surface changes the contact angle by
**Thm 8.6 (Gauss–Bonnet):** $\Delta\psi=-$Area(Ω) on the unit sphere, independent of radius ($\dot\psi=-\sin u_f\,\dot v_f$, then Green's
theorem). **Prop 8.7:** square-ish path ABCDA in the plane moves the contact by $l$ where $2x-2\tan^{-1}\frac{x}{\pi/2}=l$.

**Exercises worth doing (pp. 391–394):** 4 (polynomial eqs for multi-sinusoid steering), 6 ($\dot u=\Omega u$, Ω skew ⇒
$U\in SO(m)$ — ties to R1 orthogonality of $e^{\hat\omega t}$), 12/14 (symbolic integration of sinusoid-steered systems,
Jacobian of $q(1)$ w.r.t. Fourier coefficients — a shooting/Newton problem, F6).

**Pitfalls / typos:** Exercise 8–9 cite "Proposition 8.16" — it means Prop 8.4. Ex 8.3 (p. 371) rewrites the car as $\dot x=u_1,\ \dot y=\tan\theta\,u_1,\ \dot\theta=\frac1l\tan\phi\,u_1$, but rescaling (7.14) by $\tilde u_1=\cos\theta\,u_1$ actually gives $\dot\theta=\frac{\tan\phi}{l\cos\theta}\tilde u_1$; with that, $z_2=L_{g_1}\tan\theta=\frac1l\sec^3\theta\tan\phi$ (the printed $\sec^2$ and the printed $u_2$ formula inherit the dropped $\sec\theta$). Verify before reusing.

## Ch. 9 Future Prospects (PDF 413–420, book 395–402) — brief; Lessons: C8 (surgical robots, context only)
Essay-style 1994 outlook. Teleoperation precedes autonomy (reasons for removing the human: comms delay, slow
human response to numeric data); virtual reality ≈ telepresence (touch/force are the hard senses).
§1 hazardous environments: shuttle RMS (20 m, 6-DOF, teleoperated), planetary rovers (delay forces autonomy),
underwater ROVs (hydraulic), nuclear/toxic/mining, firefighting/construction/agriculture (orange picking, sheep
shearing with singularity-free wrist). **§2 medical (relevant to CIS I C8):** minimally invasive surgery limited
by manipulator technology — a laparoscopic needle driver has only **4 DOF** (slide, twist, pivot ×2; fulcrum at the
incision, sutures must radiate from the port), loss of tactile feedback, tremor; proposes millimetre-scale
cable/hydraulic multifingered hands, teleoperated with force-feedback gloves. §3 microrobotics: MEMS/micromachining,
electrostatic beats electromagnetic at small scale, Pister/Fearing air-bearing platform chip, micro-hinge "origami"
self-assembly.

## Appendix A Lie Groups and Robot Kinematics (PDF 421–452, book 403–434) — Lessons: R1, R2, R5 (manipulability), F3, F7
**Big idea:** Everything in Ch. 2 (rotations, twists, adjoints, wrenches) is a special case of Lie-group
machinery: SE(3) is a matrix Lie group, se(3) its Lie algebra (twists), exp maps twists to motions, Ad is
differentiated conjugation, wrenches live in the *dual* se(3)*. The payoff is a set of warnings: there is **no
frame-invariant inner product on twists**, so "orthogonal twists", $\sigma_{min}(J)$ and condition number are
frame-dependent, while $\det J$ and twist–wrench pairing are invariant.

**§1 Manifolds (pp. 403–408):** charts/atlas, smooth maps, diffeomorphism; tangent vectors as derivations,
tangent map $F_{*p}$ (chain rule $T_pH=T_{G(p)}F\circ T_pG$); cotangent space, differential $df$; vector fields, flows,
Lie derivative; Lie bracket $[X,Y]f=X(Yf)-Y(Xf)$, in coordinates $[X,Y]_j=\sum_i(X_i\partial_iY_j-Y_i\partial_iX_j)$ (same as
Ch. 7); push-forward/pull-back; **Prop A.1** $F_*[X,Y]=[F_*X,F_*Y]$; Lie algebra axioms; one-forms and their pull-back.

**§2 Lie groups (pp. 408–416)**
- Lie group = group + smooth manifold with smooth product/inverse; left/right translations $L_g,R_g$ (commute).
  Examples A.1–A.4: $(\mathbb R^n,+)$, GL(n,ℝ), SO(n) (dim $n(n-1)/2$), SE(3) (dim 6, 4×4 homogeneous).
- Lie algebra $\mathfrak g=T_eG$ ≅ left-invariant vector fields $X_\xi(g)=T_eL_g\xi$, bracket (A.1).
  Ex A.6 gl(n): $[A,B]=AB-BA$. **Ex A.7 so(3):** $[\hat\omega_1,\hat\omega_2]=\hat\omega_1\hat\omega_2-\hat\omega_2\hat\omega_1=(\omega_1\times\omega_2)^\wedge$ — (ℝ³,×) ≅ so(3).
  **Ex A.8 se(3):** $[\hat\xi_1,\hat\xi_2]=\begin{bmatrix}(\omega_1\times\omega_2)^\wedge&\omega_1\times v_2-\omega_2\times v_1\\0&0\end{bmatrix}$, $\hat\xi\mapsto\xi=(v,\omega)$.
- Exponential map: $\exp\xi=\phi_\xi(1)$ (integral curve of the left-invariant field through e); one-parameter
  subgroups $\exp(\xi s)$; local diffeo near 0; surjective if G compact. Ex A.10 $\exp A=\sum A^n/n!$.
  **Ex A.11 Rodrigues (A.3):** $e^{\hat\omega}=I+\frac{\hat\omega}{\|\omega\|}\sin\|\omega\|+\frac{\hat\omega^2}{\|\omega\|^2}(1-\cos\|\omega\|)$ (angle = $\|\omega\|$, ω not unit).
  **Ex A.12 SE(3):** $\exp\hat\xi=\begin{bmatrix}e^{\hat\omega}&Av\\0&1\end{bmatrix}$, $A=I+\frac{\hat\omega}{\|\omega\|^2}(1-\cos\|\omega\|)+\frac{\hat\omega^2}{\|\omega\|^3}(\|\omega\|-\sin\|\omega\|)$; $\omega=0$: $[I\ v;0\ 1]$.
  (Equivalent to Ch. 2's $(I-e^{\hat\omega\theta})(\omega\times v)+\omega\omega^Tv\theta$ for unit ω.)
- Log: Ex A.13 GL series ($\|A-I\|<1$); **Ex A.14 SO(3):** $\mathrm{tr}R=1+2\cos\theta$, $\hat\omega=\frac{1}{2\sin\theta}(R-R^T)$ ($R\ne I$; multi-valued;
  printed "ω̂ ∈ se(3)" should read so(3)). **Ex A.15 SE(3):** $\log g=\begin{bmatrix}\hat\omega&A^{-1}p\\0&0\end{bmatrix}$,
  $A^{-1}=I-\tfrac12\hat\omega+\frac{2\sin\|\omega\|-\|\omega\|(1+\cos\|\omega\|)}{2\|\omega\|^2\sin\|\omega\|}\hat\omega^2$ (≡ the common form
  $I-\frac12\hat\omega+\frac1{\theta^2}(1-\frac{\theta\sin\theta}{2(1-\cos\theta)})\hat\omega^2$; I checked the identity).
- **Canonical coordinates (§2.4):** 1st kind $g=\exp(\sum X_i\sigma_i)$ (exponential coordinates); 2nd kind
  $g=\exp(X_1\theta_1)\cdots\exp(X_n\theta_n)$ — **the PoE formula and Euler angles are canonical coordinates of the
  second kind** (nice R3/R1 framing).
- Group actions (§2.5): left action axioms; orbits; conjugation $I_g(h)=ghg^{-1}$; **adjoint action**
  $\mathrm{Ad}_g\xi=T_eI_g\,\xi=g\xi g^{-1}$ for matrix groups; coadjoint $\mathrm{Ad}^*_{g^{-1}}$ on $\mathfrak g^*$; lifted action on $TM$.

**§3 Geometry of SE(3) (pp. 416–434)**
- Points (append 1) vs vectors (append 0) = action vs lifted action. Twists = se(3); twist of a screw through q along ω
  with pitch h: $\hat\xi=\begin{bmatrix}\hat\omega&-q\times\omega+h\omega\\0&0\end{bmatrix}$; exp surjective on SE(3) = **Chasles' theorem**; not injective
  (rotation θ about ω ≡ 2π−θ about −ω).
- **Body velocity = left translation to e:** $\hat V^b=g^{-1}\dot g$; **spatial velocity = right translation:** $\hat V^s=\dot gg^{-1}$;
  $\hat V^s=\mathrm{Ad}_g\hat V^b$; $v_a=\dot g q_b=(\dot gg^{-1})q_a$ (A.6). Wrenches = covectors in se(3)*, $F=(f,\tau)$; work
  $=V^s_{ab}\cdot F_a=V^b_{ab}\cdot F_b$ (same frame!).
- Transformation laws (p. 421): $V^s_{ac}=\mathrm{Ad}_{g_{ab}}V^s_{bc}$ (change of spatial frame); changing body frame leaves $V^s$
  unchanged; $V^b_{ac}=\mathrm{Ad}_{g_{bc}^{-1}}V^b_{ab}$ (fixed $g_{bc}$); moving frames: $V^s_{ac}=V^s_{ab}+\mathrm{Ad}_{g_{ab}}V^s_{bc}$;
  $\mathrm{Ad}_g=\begin{bmatrix}R&\hat pR\\0&R\end{bmatrix}$; wrenches $F_b=\mathrm{Ad}^T_{g_{ab}}F_a$ (book writes $\mathrm{Ad}^T_{g^{-1}_{ab}}$-style coadjoint; pairing is preserved:
  $F'\cdot V'=F\cdot V$).
- **Metrics (§3.2):** the ℝ⁶ dot product of twists is not Ad-invariant (explicit $\mathrm{Ad}_g^T\mathrm{Ad}_g\ne I$ when $p\ne0$).
  Lemma A.2: a symmetric matrix commuting with all of SO(n) is αI ⇒ **Prop A.3** unique invariant metric on ℝⁿ up to
  length scale. **Prop A.5:** bi-invariant quadratic forms on SE(3) are exactly $\Psi_e=\begin{bmatrix}0&\beta I\\\beta I&\gamma I\end{bmatrix}$ (order $(v,\omega)$).
  Ex A.21 **hyperbolic metric** (β=1, γ=0): $\langle\xi_1,\xi_2\rangle_H=v_1^T\omega_2+v_2^T\omega_1$ = the reciprocal product of Ch. 2;
  eigenvalues ±1 (indefinite). Ex A.22 **Killing form** (β=0, γ=−4): $-4\omega_1^T\omega_2$ (degenerate; $\mathrm{tr}(\mathrm{ad}\,\mathrm{ad})$).
  **Cor A.5.1: no bi-invariant (positive-definite) metric on SE(3).** Prop A.6: left-invariant metrics ↔ choice of
  frame origin + length scale.
- Ex A.23: pitch $h_\xi=-2\langle\xi,\xi\rangle_H/\langle\xi,\xi\rangle_K=\omega^Tv/\|\omega\|^2$ (frame-invariant). The printed magnitude
  $M_\xi=-\tfrac14\langle\xi,\xi\rangle_K=\|\omega\|^2$ is the *square* of Ch. 2's magnitude $\|\omega\|$ — use $M_\xi=\sqrt{-\tfrac14\langle\xi,\xi\rangle_K}$.
- **Ex A.24 hybrid force control:** constraint $f_i(g)=0$ → $\langle L_g^*df_i,\hat\xi\rangle=0$; constraint forces are covectors in se(3)*,
  free motions are twists annihilated by them. *Treating constraint directions as twists orthogonal (ℝ⁶ dot) to free
  motions is a frame-dependent mistake* (classic critique of naive hybrid control). Prop A.7 (Loncaric): a constraint is
  left/right invariant ⇔ $f^{-1}(0)$ is a subgroup.
- **Ex A.26 manipulability (R5!):** with two tool frames $J_1=\mathrm{Ad}_hJ_2$, so $\sigma_{min}$ and condition number change
  with tool-frame placement — design by maximising $\sigma_{min}$ is ill-posed. **Ex A.27:** $\det J_1=\det\mathrm{Ad}_h\det J_2=\det J_2$
  ($\det\mathrm{Ad}_h=1$) — the determinant (Yoshikawa-type volume) measure is frame-invariant.
- §3.3 volume forms: **Thm A.8** every left-invariant volume form on SE(3) is bi-invariant ($\det\mathrm{Ad}_h=1$);
  $\Omega_{SO(3)}(\hat\omega_1,\hat\omega_2,\hat\omega_3)=-\tfrac14\langle\hat\omega_1,[\hat\omega_2,\hat\omega_3]\rangle_K=\omega_1\cdot(\omega_2\times\omega_3)$.
  The book states vol SO(3) = $8\pi^3$ rad³; integrating this measure in exponential coordinates gives
  $\int_0^\pi4\pi\theta^2\cdot\frac{2(1-\cos\theta)}{\theta^2}d\theta=8\pi^2$, so **$8\pi^2$ appears correct** (the same factor appears in the bound
  $V_M\le\frac43\pi L_M^3\cdot8\pi^3$ of Ex A.28). Flag, verify if used.
- Ex A.28 / **Thm A.9 (Paden–Sastry):** a 6R arm of given length has maximal work volume ⇔ it is an *elbow manipulator*
  (spherical shoulder + elbow + spherical wrist, Fig A.2) or its inverse, with the elbow midway between shoulder and
  wrist — a nice justification of the PUMA/UR-style "spherical wrist" architecture (R4 context).

**Pitfalls:** MLS twist order is $(v,\omega)$ (p. 411); p. 417 writes $\xi=(\omega,v)$ — a typo, do not adopt it. Wrench order
$(f,\tau)$. Lie bracket of matrices $[A,B]=AB-BA$ vs vector-field bracket of linear fields $(BA-AB)q$ (Ch. 7) — sign flip.

## Appendix B Mathematica "Screws" package (PDF 453–458, book 435–440) — Lessons: R1–R5 (tool), F10
`Screws.m` / `RobotLinks.m` (Murray & Sur, Caltech; originally anonymous ftp — long dead; modern ports exist in
MATLAB/Python). SO(3): `AxisToSkew`, `SkewToAxis`, `SkewExp[S,θ]`, `RotationAxis`. SE(3): `RPToHomogeneous`,
`RigidPosition/Orientation`, `RigidAdjoint`, `TwistExp`, `RigidTwist` (log), `ScrewToTwist[h,q,w]`, `TwistAxis`,
`TwistPitch`, `TwistMagnitude`, `TwistToHomogeneous`/`HomogeneousToTwist`, `Point/VectorToHomogeneous`.
Robot: `RevoluteTwist[q,w]`, `PrismaticTwist[q,w]`, `ForwardKinematics[{ξ1,θ1},…,gst0]`, `SpatialJacobian[…]`.
SCARA example (p. 438–439): ξ1–ξ3 revolute about z through (0,0,0),(0,l1,0),(0,l1+l2,0), ξ4 prismatic along z,
$g_{st}(0)=(I,(0,l_1+l_2,0))$. **This is exactly the function list RDKDC students re-implement in MATLAB**
(skew, expm of twist, adjoint, PoE FK, spatial/body Jacobian) — use it as a checklist for R1–R5 code.

**Bibliography & Index (PDF 459–474):** 123 references (Ball's screw theory [6], Brockett PoE [12], Khatib operational
space [50], Nguyen force-closure [82], Montana contact [72], Paden–Sastry [85,86], etc.). Index confirms Ch. 4 locations:
Christoffel symbols p. 170, Coriolis matrix p. 171/176, Lyapunov pp. 178–189, computed torque pp. 190–192, PD pp. 193–195,
workspace control p. 195–198.

