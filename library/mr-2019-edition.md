# Modern Robotics: using the notes with the 2019 "updated first edition" copy

**The two editions.**
- **Notes:** `mr-part1.md`, `mr-part2.md` and `mr-part3.md` were written from the **May 2017 preprint** (`5a2ab312-MR.pdf`, 642 PDF pages). In that file, PDF page = book page + 18.
- **Student's copy:** `~/Downloads/RobotDevicesKinematicsDynamicsAndControl/MR-largefont-v2.pdf`. It is the "preprint version of the updated first edition", dated December 30, 2019, and has 644 PDF pages.
  - **Offset:** PDF page = book page + 20 on every numbered page, from Ch. 1 (book 1 = PDF 21) to the last index page (book 624 = PDF 644).
  - **Printed copies:** The title page says that printed copies marked "3rd printing 2019" or later contain the same corrections.

**How this was checked.**
- Text was extracted from every page of the 2019 file. Each erratum spot was also rendered and read visually.
- The Appendix B, Appendix D and §6.3 formulas, and the new Example 8.5, were also checked numerically.
- The 2017 PDF is not on this machine. All "2017" page numbers below are the ones the notes cite.
- The authors' errata pages were used as a cross-check:
  - [original first edition](https://hades.mech.northwestern.edu/index.php/Modern_Robotics_Errata,_First_Edition_Version_1)
  - [updated first edition](http://hades.mech.northwestern.edu/index.php/Modern_Robotics_Errata)

## 1. Chapter and appendix concordance

| Part | 2017 book p. (notes) | 2017 PDF (notes) | **2019 book p.** | **2019 PDF** | Book-page shift |
|---|---|---|---|---|---|
| Front matter: Contents, Forewords, Preface | i–xvi | 3–18 | i–xviii | 3–20 | Preface +2 pp. ("Notes on the updated first edition" + thanks) |
| Ch 1 Preview | 1 | 19 | 1 | 21 | 0 |
| Ch 2 Configuration Space | 11 | 29 | 11 | 31 | 0 (to end of §2.7) |
| Ch 3 Rigid-Body Motions | 59 | 77 | **57** | 77 | −2 |
| Ch 4 Forward Kinematics | 137 | 155 | 135 | 155 | −2 |
| Ch 5 Velocity Kinematics and Statics | 171 | 189 | 169 | 189 | −2 |
| Ch 6 Inverse Kinematics | 219 | 237 | 217 | 237 | −2 |
| Ch 7 Kinematics of Closed Chains | 245 | 263 | 243 | 263 | −2 |
| Ch 8 Dynamics of Open Chains | 271 | 289 | 269 | 289 | −2 through §8.6, then +2 from §8.8 |
| Ch 9 Trajectory Generation | 325 | 343 | 327 | 347 | +2 |
| Ch 10 Motion Planning | 353 | 371 | 355 | 375 | +2 |
| Ch 11 Robot Control | 403 | 421 | 405 | 425 | +2 |
| Ch 12 Grasping and Manipulation | 461 | 479 | 463 | 483 | +2 |
| Ch 13 Wheeled Mobile Robots | 513 | 531 | 515 | 535 | +2 |
| App A Summary of Useful Formulas | 565 | 583 | 567 | 587 | +2 |
| App B Other Representations of Rotations | 575 | 593 | 577 | 597 | +2 |
| App C Denavit–Hartenberg Parameters | 585 | 603 | 587 | 607 | +2 |
| App D Optimization and Lagrange Multipliers | 597 | 615 | 599 | 619 | +2 |
| Bibliography | 599 | 617 | 601 | 621 | +2 |
| Index | 617 | 635 | 617 | 637 | 0 (both end at book 624) |

**The book page numbers did change; this is not only a front-matter offset.** Three content changes account for it, and they cancel out, so both editions still end at book p. 624:

1. **Ch 2 exercises (−2 pp.).**
   - The chapter body is identical: Table 2.1 is on p. 17, Ex 2.3 on p. 18, the rolling-coin argument on p. 32, and §2.8 Exercises starts on p. 38 in both copies. The last exercise is 2.32 in both.
   - The 2019 exercises end on p. 56, whereas the notes put the end of Ch 2 at p. 58. Without the 2017 file, I could not tell where inside the exercises the two pages went.
2. **§8.7 Constrained Dynamics (+4 pp.).** It grew from pp. 301–303 (2017) to **pp. 299–304 (2019, PDF 319–324)**. See §2 below.
3. **Bibliography (−2 pp.).** It is two pages shorter.

The extra two preface pages explain why the 2019 file has 644 PDF pages instead of 642.

Spot checks behind the table:

| Anchor | Notes (2017 book / PDF) | 2019 book / PDF |
|---|---|---|
| Fig 3.14 text ($T_{bc}$) | 92 / 110 | 90 / 110 |
| Eq (3.73) | 99 / 117 | 97 / 117 |
| Ex 4.1 table | 144 / 162 | 142 / 162 |
| §6.3 null-space formula | 234 / 252 | 232 / 252 |
| Eq (7.22) | 257 / 275 | 255 / 275 |
| Eq (8.26) | 287 / 305 | 285 / 305 |
| Eq (8.57) | 295 / 313 | 293 / 313 |
| (8.94) "inequality" | 302 / 320 | 300 / 320 |
| Ch 8 exercises | 321–324 | 323–326 / 343–346 |
| §11.2.2 "negative definite" | 408 / 426 | 410 / 430 |
| Eq (11.18) | 420 / 438 | 422 / 442 |
| Ch 11 Summary | 449–451 / 467–469 | 451–453 / 471–473 |
| Eq (12.14) | 480 / 498 | 482 / 502 |
| Eq (B.11) | 581 / 599 | 583 / 603 |
| Eq (C.5) | 590 / 608 | 592 / 612 |
| Eq (C.12) | 594 / 612 | 596 / 616 |
| App D QP | 598 / 616 | 600 / 620 |

## 2. Section-level differences

**Numbering is unchanged.** Every section and subsection number the notes cite exists in the 2019 table of contents with the same title. This includes §5.1.6, §8.2.3, §8.9.2, §9.4.3–9.4.4, §10.6.1–10.6.5, §11.4.3, §11.8–11.9, B.1–B.4 and C.1–C.6. Nothing was added, removed or moved at section level.

**One section was rewritten: §8.7 Constrained Dynamics** (2019 pp. 299–304, PDF 319–324).
- **New equation (8.95):** $\dot A\dot\theta+A\ddot\theta=0$.
- **Multiplier formula (8.98):** it is now $\lambda=(AM^{-1}A^T)^{-1}\big(AM^{-1}(\tau-h)+\dot A\dot\theta\big)$.
  - The notes' 2017 form uses $-A\ddot\theta$ in place of $\dot A\dot\theta$.
  - The two forms are equivalent by (8.95), but only the 2019 form can be used for forward dynamics.
- **New subsections:** constrained forward dynamics, task-space constraint forces ($J^T\mathcal F_{tip}=A^T\lambda$, $\lambda=(J^{-T}A^T)^\dagger\mathcal F_d$, linked to §11.6), and constrained inverse dynamics.
- **New Example 8.5 (Figs 8.7–8.8):** a 2R arm whose tip moves in a frictionless channel.
  - I checked its numbers: $\lambda=0.289\tau_1-1.155\tau_2-0.167$, $\ddot\theta=(0.5\tau_1+0.289,\,-1.155)$, $P=\begin{bmatrix}1&0\\0.25&0\end{bmatrix}$. All are correct.
- **Projections:** $P$ is now (8.99)–(8.100). The new (8.102) adds the identity $P_{\ddot\theta}=M^{-1}PM=P^T$.

**Knock-on renumbering in Ch 8.**
- **Equations from about (8.95) on are +1** relative to the notes:

  | Item | Notes (2017) | 2019 |
  |---|---|---|
  | $\lambda$ formula | (8.97) | (8.98) |
  | $P$ | (8.98)–(8.99) | (8.99)–(8.100) |
  | $P_{\ddot\theta}$ | (8.100)–(8.101) | (8.101)–(8.102) |
  | DC-motor voltage equation | (8.102) | (8.103) |
  | RNEA with rotors | (8.103)–(8.111) | (8.104)–(8.112) |

- **Figures after Fig 8.6 are +2.** The notes' Fig 8.13 (friction models) is 2019 Fig 8.15. The motor operating-region figure (2017 Fig 8.10) is 2019 Fig 8.12.
- Exercise numbers are unchanged; Ex 8.15 (UR5 falling under gravity) is still 8.15.

**Small changes found in or next to the errata spots:**
- **Eq (11.18):** the top block of $X_e$ is now the vector $\omega_e$ with $[\omega_e]=\log(R^T(t)R_d(t))$. In 2017 it was an so(3) matrix.
- **§10.6.4:** both damping laws now read $-B\dot q$.

## 3. Errata re-check (README list, plus the extra items in the `mr-part*.md` files)

Verdict counts:

| Verdict | README items | Extra items from the `mr-part*.md` files |
|---|---|---|
| **FIXED IN 2019** | 8 | 0 |
| **STILL WRONG** | 13 | 2 |
| **CHANGED** | 0 | 0 |
| **COULD NOT LOCATE** | 0 | 0 |

None of the earlier "errata" turned out to be correct as printed. For the §10.6.3 item (p. 390), the authors' own updated-edition errata page now lists the same correction.

| # | Item (2017 cite) | 2019 book / PDF | Exact 2019 text | Verdict |
|---|---|---|---|---|
| 1 | p. 221: 2R reach test uses $x^2+y^2$ | 219 / 239 | "If $x^2+y^2$ lies outside the range $[L_1-L_2, L_1+L_2]$ then no solution exists." | **STILL WRONG.** It should be $\sqrt{x^2+y^2}$. |
| 2 | p. 234: null-space sign | 232 / 252 | $\dot\theta=G\mathcal V_d+(I-GJ)M^{-1}\nabla h$, $\lambda=B\mathcal V_d+BJM^{-1}\nabla h$ (cost $\tfrac12\dot\theta^TM\dot\theta+\nabla h^T\dot\theta$, FONC $J^T\lambda=M\dot\theta+\nabla h$) | **STILL WRONG.** A KKT solve gives $-(I-GJ)M^{-1}\nabla h$. The $\lambda$ formula is right (checked numerically). |
| 3 | p. 235: Summary iteration | 233 / 253 | "$\dot\theta^{i+1}=\theta^i+J^\dagger(\theta^i)\mathcal V$" | **STILL WRONG.** It should be $\theta^{i+1}$. |
| 4 | p. 257: four-bar existence inequality | 255 / 275 | "a solution will fail to exist if $\gamma^2\le\alpha^2+\beta^2$" | **STILL WRONG.** Eq (7.22) needs $\lvert\gamma\rvert\le\sqrt{\alpha^2+\beta^2}$, so there is no solution when $\gamma^2>\alpha^2+\beta^2$. |
| 5 | p. 287: "eigenvalues" for the columns of $R_{bc}$ | 285 / 305 | "the columns of $R_{bc}$ correspond to the eigenvectors of $\mathcal I_b$" | **FIXED IN 2019** |
| 6 | p. 302: writing robot "equality" | 300 / 320 | "(For the writing robot, there is also an inequality constraint: the robot can only apply pushing forces …)" | **FIXED IN 2019** |
| 7 | Eq (9.36): $c,g$ instead of $c_i,g_i$ | 340 / 360 | $L_i=\dfrac{\tau_i^{\min}-c(s)\dot s^2-g(s)}{m_i(s)}$ etc. (all four bounds) | **STILL WRONG.** It should use $c_i(s)$ and $g_i(s)$. 2019 also adds that the $m_i=0$ case is discussed in §9.4.4. |
| 8 | Eq (10.4) and §10.6.4: damping sign | 389 / 409; 393–394 / 413–414 | "$u=F(q)-B\dot q$. (10.4)"; "$u=F(q)-B\dot q$ (control law (10.4))"; "$u=F_{proj}(q)-B\dot q$" | **FIXED IN 2019** |
| 9 | p. 390: Jacobian applied twice (§10.6.3) | 393 / 413 | $F'_{ij}=-\dfrac{\partial\mathcal P'_{ij}}{\partial q}=\dfrac{k}{\lVert f_i-c_j\rVert^4}\Big(\dfrac{\partial f_i}{\partial q}\Big)^T(f_i-c_j)\in\mathbb R^3$, then $F_{ij}=J_i^TF'_{ij}$ | **STILL WRONG.** Use $F'_{ij}=k(f_i-c_j)/\lVert f_i-c_j\rVert^4$, the derivative with respect to $f_i$. The authors' updated-edition errata list this too. |
| 10 | p. 408: "negative definite" for Hurwitz | 410 / 430 | "converges to $x=0$ if the matrix $A$ is negative definite, i.e., all eigenvalues of $A$ … have negative real components" | **STILL WRONG.** The word should be "Hurwitz"; the "i.e." clause is the correct definition. |
| 11 | Eq (11.18): $R^T(d)$ | 422 / 442 | $[\omega_e(t)]=\log(R^T(t)R_d(t))$ | **FIXED IN 2019.** $X_e$ now also contains the vector $\omega_e$. Only a closing period is missing. |
| 12 | Ch 11 Summary: PD + gravity sign | 452 / 472 | "$\tau=K_p\theta_e+K_d\dot\theta+\tilde g(\theta)$" | **STILL WRONG.** It should be $-K_d\dot\theta$ (equivalently $+K_d\dot\theta_e$), as in Eq (11.39) on p. 434. |
| 13 | (B.11): third entry | 583 / 603 | "$r_{21}-212$" | **STILL WRONG.** It should be $r_{21}-r_{12}$. |
| 14 | (B.13): entry of $Q$ | 584 / 604 | $Q=\begin{bmatrix}q_0+iq_1&q_2+ip_3\\-q_2+iq_3&q_0-iq_1\end{bmatrix}$ | **STILL WRONG.** It should be $q_2+iq_3$. With the typo, $R(n)\ne R_qR_p$ (checked numerically). |
| 15 | (B.17)–(B.18): Cayley sign | 584 / 604 | $R=(I-[r])(I+[r])^{-1}$, $[r]=(I-R)(I+R)^{-1}$ | **STILL WRONG.** With $r=\hat\omega\tan\frac\theta2$ (B.16), these give $R^T$ and $-[r]$, contradicting (B.21)/(B.22), which are correct. Use $R=(I+[r])(I-[r])^{-1}$ and $[r]=(R-I)(R+I)^{-1}$. |
| 16 | (B.19)–(B.20): case tr $R=-1$ | 585 / 605 | $R=-(I-[r])(I+[r])^{-1}$, $[r]=(I+R)(I-R)^{-1}$ | **STILL WRONG.** They are invalid: $I-R$ is singular for every rotation, and the first has det −1. |
| 17 | (C.5): $\phi_{i-1}$ | 592 / 612 | $\mathrm{Rot}(\hat z,\phi_i)$ uses $\cos\phi_i,\sin\phi_i$ | **FIXED IN 2019** |
| 18 | (C.12): index | 596 / 616 | $[\mathcal S_i]=(M_1\cdots M_i)[\mathcal A_i](M_1\cdots M_i)^{-1}$ | **FIXED IN 2019** |
| 19 | App D QP solution | 600 / 620 | $x=Gb+(I-GA)Q^{-1}c$, $\lambda=Bb+BAQ^{-1}c$; "$Q\in\mathbb R^n$ is symmetric positive-definite" | **STILL WRONG.** It should be $x=Gb-(I-GA)Q^{-1}c$, $\lambda=-(Bb+BAQ^{-1}c)$, and $Q\in\mathbb R^{n\times n}$ (checked numerically against a direct KKT solve). |
| 20 | Ex 4.1 table: $v_3$ sign | 142 / 162 | Table row 3: $\omega_3=(1,0,0)$, $v_3=(0,-L_2,0)$ | **FIXED IN 2019** |
| 21 | PDF 110: $T_{bc}$ wording | 90 / 110 | "$T_{bc}=(R_{bc},p_{bc})$ represents {c} relative to {b}" | **FIXED IN 2019** |
| x1 | Text before (B.21) cites (B.18) (from `mr-part3.md`) | 585 / 605 | "Furthermore, Equation (B.18) can be explicitly computed as $R=\dots$ (B.21)" | **STILL WRONG.** (B.21) gives $R$, so the reference should be (B.17). |
| x2 | App C writes "$T_{i,i-1}$" where $T_{i-1,i}$ is meant (from `mr-part3.md`) | 587 / 607; 593 / 613 | "where $T_{i,i-1}\in SE(3)$ denotes the relative displacement between link frames {i−1} and {i}"; "A useful way to visualize $T_{i,i-1}$ …" | **STILL WRONG** (notation only). |

### New issues noticed in the 2019 text near these spots (each verified)

- **App D, p. 599 (PDF 619):** "$\tfrac12\lVert Ax-b\rVert^2=\dots=\tfrac12x^TA^TAx-2b^TAx+b^Tb$". The parentheses are missing. The expansion should be $\tfrac12x^TA^TAx-b^TAx+\tfrac12b^Tb$. Eq (D.1) on p. 600 is consistent with the correct form.
- **App D, p. 600 (PDF 620):**
  - "$x^TQx>0$ for all $x\in\mathbb R^n$" should say for all $x\neq0$.
  - "mininum" is a spelling slip.
- **§11.4.2, p. 435 (PDF 455):** after Eq (11.42), the text cites skew-symmetry of $\dot M-2C$ as "Proposition 8.1.2". It is Proposition 8.1 (p. 277, PDF 297).
- **Not near these spots:** the authors' updated-edition errata page lists more 2019 corrections, which I did not re-derive here:
  - Eqs (11.52)–(11.53): $K_{fp}\to K_{fp}+I$.
  - The sentence after (11.16) about the gains.
  - Ex 3.20 figure axes.
  - Eq (13.37): $\mathcal V\to\mathcal V_e$.

  The original-edition errata page also lists 2017 items the notes never flagged, for example Eq (5.7), Eq (8.14) and Ex 12.4. These were not re-checked here.

## 4. How to use the existing MR notes with the 2019 copy

- **Equation, example, exercise and section numbers** match the 2019 copy everywhere except Ch 8 after §8.6 (equations +1, figures +2). Prefer these numbers to page numbers.
- **Converting a PDF page number from the notes:**

  | Where | 2019 PDF |
  |---|---|
  | Ch 1–2 (notes PDF 19–55) | **add 2** |
  | Ch 2 exercises | 2019 PDF 58–76; look up by exercise number |
  | **Ch 3 through §8.6** (notes PDF 77–~319) | **same number**; no change |
  | §8.7 | rewritten; read 2019 PDF 319–324 |
  | **§8.8 through App D** (notes PDF 321–616) | **add 4** |
  | Bibliography | add 4 |
  | Index | add 2 |

- **Converting a book page number from the notes:**

  | Where | 2019 book page |
  | --- | --- |
  | Ch 1–2 | same |
  | Ch 3–§8.6 | **subtract 2** |
  | §8.8–App D | **add 2** |
  | Index | same |

  Then 2019 PDF = 2019 book + 20.
- **Errata:** treat everything marked STILL WRONG in §3 as still applying to the 2019 copy. The README's MR errata list is correct for the 2019 copy except for the eight items marked FIXED IN 2019.
