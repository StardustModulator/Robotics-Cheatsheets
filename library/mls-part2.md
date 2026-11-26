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

