# 00 Foundations: Video Watch-List (F1–F10)

Every URL below was checked on **2026-11-26**. The legend at the end explains how each one was checked. The list is
built for a CS graduate who starts every topic from zero. Python and tooling are complete but can be watched fast.
Most of the time goes to the math. Tick the boxes as you go.

**Duration notation.** `1:45:36` or `35 min` is an exact runtime, taken from catalog data or published timestamps.
`~10 min` is an estimate (±2 min for short videos, ±5 min for lectures). Section totals count **Required videos only**.
They leave out self-paced docs and interactive sites, and each video is counted once (cross-listed items say "counted in Fx").

**Totals (Required).** About **78.1 h** at 1× and **52.1 h** at 1.5×. Watch the Python and tooling sections at 2×.

| Section | Required 1× | Required 1.5× |
|---|---|---|
| F1 Python, NumPy, toolchain | 21.0 h | 14.0 h |
| F2–F4 Linear algebra + 3D rotations | 20.1 h | 13.4 h |
| F5 Calculus & matrix calculus | 9.6 h | 6.4 h |
| F6 Optimization | 2.4 h | 1.6 h |
| F7 ODEs & matrix exponential | 2.4 h | 1.6 h |
| F8 Probability | 11.2 h | 7.5 h |
| F9 PyTorch & autograd | 4.3 h | 2.9 h |
| F10 C++/Eigen, ROS 2, MATLAB, VM | 7.1 h | 4.7 h |
| **Total** | **78.1 h** | **52.1 h** |

---

## F1: Python, NumPy, toolchain

Playlists and hubs:
- CS50P course (Harvard, David Malan, 2022 recordings): https://cs50.harvard.edu/python/ · per-week pages are https://cs50.harvard.edu/python/weeks/N/ · YouTube playlist: https://www.youtube.com/playlist?list=PLhQjrBD2T3817j24-GogXmWqO5Q5vYy0V · full 16-h single video: https://www.youtube.com/watch?v=nLRL_NcnK-4
- MIT Missing Semester 2020: https://missing.csail.mit.edu/2020/ · playlist: https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J
- Corey Schafer Matplotlib playlist: https://www.youtube.com/playlist?list=PL-osiE80TeTvipOqomVEeZ1HRrcEvtZB_

### Required
1. - [ ] CS50P Lecture 0: Functions, Variables — 1:45:36 — https://www.youtube.com/watch?v=JP7ITIXGpHk · page https://cs50.harvard.edu/python/weeks/0/
   Covers types (str/int/float), functions, arguments and return values, and scope basics.
2. - [ ] CS50P Lecture 2: Loops — 1:20:47 — https://www.youtube.com/watch?v=-7xg8pGcP6w · page https://cs50.harvard.edu/python/weeks/2/
   Covers loops, lists, dicts, and iteration.
3. - [ ] CS50P Lecture 3: Exceptions — 44:35 — https://www.youtube.com/watch?v=LW7g1169v7w · page https://cs50.harvard.edu/python/weeks/3/
4. - [ ] CS50P Lecture 4: Libraries — 1:17:30 — https://www.youtube.com/watch?v=MztLZWibctI · page https://cs50.harvard.edu/python/weeks/4/
   Covers modules, `import`, packages, pip, and `sys.argv`.
5. - [ ] CS50P Lecture 5: Unit Tests — 51:07 — https://www.youtube.com/watch?v=tIrcxwLqzjQ · page https://cs50.harvard.edu/python/weeks/5/
   Covers `assert` and pytest.
6. - [ ] CS50P Lecture 6: File I/O — 1:32:10 — https://www.youtube.com/watch?v=KD-Yoel6EVQ · page https://cs50.harvard.edu/python/weeks/6/
   Covers open/with, CSV, and binary files (PIL).
7. - [ ] CS50P Lecture 8: Object-Oriented Programming — 2:51:12 — https://www.youtube.com/watch?v=e4fwY9ZsxPw · page https://cs50.harvard.edu/python/weeks/8/
   Covers classes, `__init__`/`__str__`, properties, class methods, inheritance, and operator overloading.
8. - [ ] CS50P Lecture 9: Et Cetera — ~2:29 — https://www.youtube.com/watch?v=6pgodt1mezg · page https://cs50.harvard.edu/python/weeks/9/
   Covers sets, globals, type hints, docstrings, argparse, unpacking, `*args/**kwargs`, map/filter, comprehensions, and **generators/iterators**.
9. - [ ] Programming Terms: Closures (Corey Schafer) — ~13 min — https://www.youtube.com/watch?v=swU3c34d2NQ
   CS50P does not cover closures.
10. - [ ] Python Tutorial: Generators (Corey Schafer) — ~10 min — https://www.youtube.com/watch?v=bD05uGo_sVI
    Goes deeper on `yield` and lazy evaluation.
11. - [ ] Missing Semester L1: Course Overview + The Shell — ~48 min — https://www.youtube.com/watch?v=Z56Jmr9Z34Q · notes https://missing.csail.mit.edu/2020/course-shell/
12. - [ ] Missing Semester L2: Shell Tools and Scripting — ~49 min — https://www.youtube.com/watch?v=kgII-YWo3Zw · notes https://missing.csail.mit.edu/2020/shell-tools/
13. - [ ] Missing Semester L5: Command-line Environment — ~56 min — https://www.youtube.com/watch?v=e8BO_dYxk5c · notes https://missing.csail.mit.edu/2020/command-line/
    Covers job control, tmux, dotfiles, and SSH.
14. - [ ] Missing Semester L6: Version Control (Git) — ~1:24 — https://www.youtube.com/watch?v=2sjqTHE0zok · notes https://missing.csail.mit.edu/2020/version-control/
    Covers Git's data model, branching and merging, and remotes.
15. - [ ] Missing Semester L7: Debugging and Profiling — ~59 min — https://www.youtube.com/watch?v=l812pUnKxME · notes https://missing.csail.mit.edu/2020/debugging-profiling/
    Covers pdb, logging, cProfile, and line/memory profilers.
16. - [ ] Complete Python NumPy Tutorial (Keith Galli) — 58:40 — https://www.youtube.com/watch?v=GB9ByFAIAH4
    Covers arrays, dtypes, indexing/slicing, math/stats, reshaping, and copying arrays.
17. - [ ] Python NumPy For Your Grandma 5.2: View vs Copy — ~10 min — https://www.youtube.com/watch?v=ubntlkO8yro
    Explains views vs copies and when slicing and indexing alias memory.
18. - [ ] NumPy Array Broadcasting in Python Explained — 13 min — https://www.youtube.com/watch?v=oG1t3qlzq14
19. - [ ] Losing Your Loops: Fast Numerical Computing with NumPy (Jake VanderPlas, PyCon 2015) — 30:31 — https://www.youtube.com/watch?v=EEUXKG97YRw
    Covers vectorization, ufuncs, aggregations, broadcasting, and fancy indexing.
20. - [ ] Matplotlib Tutorial Part 1: Creating and Customizing Our First Plots (Corey Schafer) — 35:00 — https://www.youtube.com/watch?v=UO98lJQ3QGI
21. - [ ] Matplotlib Tutorial Part 10: Subplots (Corey Schafer) — 21:21 — https://www.youtube.com/watch?v=XFZRVnP-MTU

**F1 Required total: 1260 min ≈ 21.0 h (1×) / 14.0 h (1.5×).**

### Optional
- [ ] CS50P Lecture 1: Conditionals — 55:59 — https://www.youtube.com/watch?v=_b6NgY_pMdw · https://cs50.harvard.edu/python/weeks/1/
- [ ] CS50P Lecture 7: Regular Expressions — 2:05:03 — https://www.youtube.com/watch?v=hy3sd9MOAcc · https://cs50.harvard.edu/python/weeks/7/
- [ ] Python Tutorial: Decorators (Corey Schafer) — ~30 min — https://www.youtube.com/watch?v=FsAPt_9Bf3U
- [ ] Python Tutorial: Iterators and Iterables (Corey Schafer) — ~13 min — https://www.youtube.com/watch?v=jTYiNjvnHZY
- [ ] Python OOP Tutorial 5: Special (Magic/Dunder) Methods (Corey Schafer) — ~13 min — https://www.youtube.com/watch?v=3ohzBxoFHAY
- [ ] Unit Testing Your Code with the unittest Module (Corey Schafer) — ~39 min — https://www.youtube.com/watch?v=6tNS--WetLI
- [ ] Pytest Tutorial: How to Test Python Code (freeCodeCamp) — ~1:40 — https://www.youtube.com/watch?v=cHYq1MRoyI0
- [ ] Python NumPy Tutorial for Beginners (freeCodeCamp re-upload of Keith Galli) — 58 min — https://www.youtube.com/watch?v=QUT1VHiLmmI
- [ ] Matplotlib Tutorial Part 7: Scatter Plots — 21:23 — https://www.youtube.com/watch?v=zZZ_RCwp49g
- [ ] Matplotlib Tutorial Part 9: Plotting Live Data in Real-Time — 20:33 — https://www.youtube.com/watch?v=Ercd-Ip5PfQ
  Useful for live sensor or robot-state plots.
- [ ] Git Tutorial for Beginners: Command-Line Fundamentals (Corey Schafer) — ~30 min — https://www.youtube.com/watch?v=HVsySz-h9r4
- [ ] Missing Semester **2026** edition (newer re-recording: shell, command-line environment, version control, debugging/profiling, packaging, code quality, agentic coding) — 2026 playlist https://www.youtube.com/playlist?list=PLyzOVJj3bHQunmnnTXrNbZnBaCA-ieK4L · e.g. https://missing.csail.mit.edu/2026/course-shell · https://missing.csail.mit.edu/2026/version-control/

---

## F2–F4: Linear algebra + 3D rotations / homogeneous transforms

Playlists and hubs:
- 3Blue1Brown *Essence of Linear Algebra*: https://www.3blue1brown.com/topics/linear-algebra · playlist https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab
- MIT 18.06SC (Strang) lecture-video gallery: https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/resources/lecture-videos/ · YouTube playlist https://www.youtube.com/playlist?list=PL221E2BBF13BECF6C (watch only the lectures listed below; the playlist also contains recitations)
- Steve Brunton SVD series: https://www.youtube.com/playlist?list=PLMrJAkhIeNNSVjnsviglFoY2nXildDCcv (about 7 h in total; watch only the items listed below)
- Modern Robotics (Lynch & Park), Chapter 3 video page: https://modernrobotics.northwestern.edu/chapters/chapter3 (whole chapter-3 playlist is 40:35) · full playlist https://www.youtube.com/playlist?list=PLggLP4f-rq02vX0OQQ5vrCxbJrzamYDfx

### Required: 3Blue1Brown Essence of Linear Algebra (all 16 chapters; watch first)
1. - [ ] Ch1 Vectors, what even are they? — 10 min — https://www.3blue1brown.com/lessons/vectors · https://www.youtube.com/watch?v=fNk_zzaMoSs
2. - [ ] Ch2 Linear combinations, span, and basis vectors — ~10 min — https://www.3blue1brown.com/lessons/span · https://www.youtube.com/watch?v=k7RM-ot2NWY
3. - [ ] Ch3 Linear transformations and matrices — ~11 min — https://www.3blue1brown.com/lessons/linear-transformations · https://www.youtube.com/watch?v=kYB8IZa5AuE
4. - [ ] Ch4 Matrix multiplication as composition — ~10 min — https://www.3blue1brown.com/lessons/matrix-multiplication · https://www.youtube.com/watch?v=XkY2DOUCWMU
5. - [ ] Ch5 Three-dimensional linear transformations — ~5 min — https://www.3blue1brown.com/lessons/3d-transformations · https://www.youtube.com/watch?v=rHLEWRxRGiM
6. - [ ] Ch6 The determinant — ~10 min — https://www.3blue1brown.com/lessons/determinant · https://www.youtube.com/watch?v=Ip3X9LOh2dk
7. - [ ] Ch7 Inverse matrices, column space, and null space — ~12 min — https://www.3blue1brown.com/lessons/inverse-matrices · https://www.youtube.com/watch?v=uQhTuRlWMxw
8. - [ ] Ch8 Nonsquare matrices as transformations between dimensions — ~4 min — https://www.3blue1brown.com/lessons/nonsquare-matrices · https://www.youtube.com/watch?v=v8VSDg_WQlA
9. - [ ] Ch9 Dot products and duality — 14 min — https://www.3blue1brown.com/lessons/dot-products · https://www.youtube.com/watch?v=LyGKycYT2v0
10. - [ ] Ch10 Cross products — ~9 min — https://www.3blue1brown.com/lessons/cross-products · https://www.youtube.com/watch?v=eu6i7WJeinw
11. - [ ] Ch11 Cross products in the light of linear transformations — ~13 min — https://www.3blue1brown.com/lessons/cross-products-extended · https://www.youtube.com/watch?v=BaM7OCEm3G0
12. - [ ] Ch12 Cramer's rule, explained geometrically — ~12 min — https://www.3blue1brown.com/lessons/cramers-rule · https://www.youtube.com/watch?v=jBsC34PxzoM
13. - [ ] Ch13 Change of basis — ~13 min — https://www.3blue1brown.com/lessons/change-of-basis · https://www.youtube.com/watch?v=P2LTAUO1TdA
14. - [ ] Ch14 Eigenvectors and eigenvalues — 17 min — https://www.3blue1brown.com/lessons/eigenvalues · https://www.youtube.com/watch?v=PFDu9oVAE-g
15. - [ ] Ch15 A quick trick for computing eigenvalues — ~13 min — https://www.3blue1brown.com/lessons/quick-eigen · https://www.youtube.com/watch?v=e50Bj7jn9IQ
16. - [ ] Ch16 Abstract vector spaces — ~17 min — https://www.3blue1brown.com/lessons/abstract-vector-spaces · https://www.youtube.com/watch?v=TgKwz5Ikpc8

### Required: MIT 18.06SC, Gilbert Strang (only these 20 lectures; OCW session page + YouTube)
17. - [ ] L1 The Geometry of Linear Equations — 39 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/the-geometry-of-linear-equations · https://www.youtube.com/watch?v=J7DzL2_Na80
18. - [ ] L2 Elimination with Matrices — ~48 min — https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/elimination-with-matrices/ · https://www.youtube.com/watch?v=QVKj3LADCnA
19. - [ ] L3 Multiplication and Inverse Matrices — ~47 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/multiplication-and-inverse-matrices · https://www.youtube.com/watch?v=FX4C-JpTFgY
20. - [ ] L5 Transposes, Permutations, Spaces Rⁿ — ~48 min — https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/transposes-permutations-vector-spaces/ · https://www.youtube.com/watch?v=JibVXBElKL0
21. - [ ] L6 Column Space and Nullspace — ~46 min — https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/column-space-and-nullspace/ · https://www.youtube.com/watch?v=8o5Cmfpeo6g
22. - [ ] L7 Solving Ax = 0: Pivot Variables, Special Solutions — ~43 min — https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/solving-ax-0-pivot-variables-special-solutions/ · https://www.youtube.com/watch?v=VqP2tREMvt0
23. - [ ] L8 Solving Ax = b: Row Reduced Form R — ~47 min — https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/solving-ax-b-row-reduced-form-r/ · https://www.youtube.com/watch?v=9Q1q7s1jTzU
24. - [ ] L9 Independence, Basis, and Dimension — ~50 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/independence-basis-and-dimension · https://www.youtube.com/watch?v=yjBerM5jWsc
25. - [ ] L10 The Four Fundamental Subspaces — ~49 min — https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/the-four-fundamental-subspaces/ · https://www.youtube.com/watch?v=nHlE7EgJFds
26. - [ ] L14 Orthogonal Vectors and Subspaces — ~50 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/orthogonal-vectors-and-subspaces · https://www.youtube.com/watch?v=YzZUIYRCE38
27. - [ ] L15 Projections onto Subspaces — 48:51 — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/projections-onto-subspaces · https://www.youtube.com/watch?v=Y_Ac6KiQ1t0
28. - [ ] L16 Projection Matrices and Least Squares — ~48 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/projection-matrices-and-least-squares · https://www.youtube.com/watch?v=osh80YCg_GM
29. - [ ] L17 Orthogonal Matrices and Gram–Schmidt — ~49 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/orthogonal-matrices-and-gram-schmidt · https://www.youtube.com/watch?v=0MtwqhIwdrI
30. - [ ] L18 Properties of Determinants — ~49 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/properties-of-determinants · https://www.youtube.com/watch?v=srxexLishgY
31. - [ ] L21 Eigenvalues and Eigenvectors — ~51 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/eigenvalues-and-eigenvectors · https://www.youtube.com/watch?v=cdZnhQjJu4I
32. - [ ] L22 Diagonalization and Powers of A — ~52 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/diagonalization-and-powers-of-a · https://www.youtube.com/watch?v=13r9QY6cmjc
33. - [ ] L23 Differential Equations and exp(At) — 51 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/differential-equations-and-exp-at · https://www.youtube.com/watch?v=IZqwi0wJovM
    This lecture is also required for F7 and is counted here.
34. - [ ] L25 Symmetric Matrices and Positive Definiteness — ~44 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/positive-definite-matrices-and-applications/symmetric-matrices-and-positive-definiteness · https://www.youtube.com/watch?v=UCc9q_cAhho
35. - [ ] L27 Positive Definite Matrices and Minima — ~44 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/positive-definite-matrices-and-applications/positive-definite-matrices-and-minima · https://www.youtube.com/watch?v=vF7eyJ2g3kU
    Covers the Hessian test, which links to F5 and F6.
36. - [ ] L29 Singular Value Decomposition — ~41 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/positive-definite-matrices-and-applications/singular-value-decomposition · https://www.youtube.com/watch?v=TX_vooSnhm8

### Required: Steve Brunton SVD (core subset)
37. - [ ] SVD: Overview — 7 min — https://www.youtube.com/watch?v=gXbThCXjZFM
38. - [ ] SVD: Mathematical Overview — 13 min — https://www.youtube.com/watch?v=nbBvuuNVfco
39. - [ ] SVD: Matrix Approximation — ~10 min — https://www.youtube.com/watch?v=xy3QyyhiuY4
    Covers Eckart–Young and low-rank truncation.
40. - [ ] SVD: Linear Systems of Equations — ~11 min — https://www.youtube.com/watch?v=PjeOmOz9jSY
    Covers the pseudo-inverse and under/over-determined systems.
41. - [ ] Least Squares Regression and the SVD — 6 min — https://www.youtube.com/watch?v=02QCtHM1qb4
42. - [ ] Principal Component Analysis (PCA) — 14 min — https://www.youtube.com/watch?v=fkf4IBRSeEc

### Required: 3D rotations and homogeneous transforms (Modern Robotics ch. 3, Kevin Lynch)
43. - [ ] Ch 3: Introduction to Rigid-Body Motions — ~4 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/introduction-to-rigid-body-motions/ · https://www.youtube.com/watch?v=29LhXWjn7Pc
44. - [ ] 3.2.1 Rotation Matrices (Part 1 of 2) — ~3 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-2-1-rotation-matrices-part-1-of-2 · https://www.youtube.com/watch?v=OZucG1DY_sY
    Covers SO(3) and the properties of rotation matrices.
45. - [ ] 3.2.1 Rotation Matrices (Part 2 of 2) — ~4 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-2-1-rotation-matrices-part-2-of-2/ · https://www.youtube.com/watch?v=6KIPusOv5fA
    Covers the three uses of R: orientation, change of frame, and rotating a vector or frame.
46. - [ ] 3.2.2 Angular Velocities — ~4 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-2-2-angular-velocities · https://www.youtube.com/watch?v=zJJldJYMxVU
47. - [ ] 3.3.1 Homogeneous Transformation Matrices — ~5 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-3-1-homogeneous-transformation-matrices/ · https://www.youtube.com/watch?v=vlb3P7arbkU
    Covers SE(3), 4×4 transforms, and composition.

**F2–F4 Required total: 1206 min ≈ 20.1 h (1×) / 13.4 h (1.5×).** That is 3.0 h of 3B1B, 15.75 h of Strang, 1.0 h of Brunton, and 0.3 h of Modern Robotics.

### Optional
- [ ] SVD: Image Compression [Python] (Brunton) — ~9 min — https://www.youtube.com/watch?v=H7qMMudo3e8
- [ ] 18.06SC L4 Factorization into A = LU — ~48 min — https://www.youtube.com/watch?v=MsIvs_6vC38
- [ ] 18.06SC L33 Left and Right Inverses; Pseudoinverse — ~42 min — https://www.youtube.com/watch?v=Go2aLo7ZOlU
- [ ] 18.06SC L19 Determinant Formulas and Cofactors / L20 Cramer's Rule, Inverse Matrix, and Volume — ~50 min each — https://www.youtube.com/watch?v=23LLB9mNJvc · https://www.youtube.com/watch?v=QNpj-gOXW9M
- [ ] 3Blue1Brown: Quaternions and 3D rotation, explained interactively — ~6 min — https://www.3blue1brown.com/lessons/quaternions-and-3d-rotation · https://www.youtube.com/watch?v=zjMuIxRvygQ
- [ ] 3Blue1Brown: Visualizing quaternions (4D numbers) with stereographic projection — ~31 min — https://www.3blue1brown.com/lessons/quaternions · https://www.youtube.com/watch?v=d4EgbgTm0Bg
- [ ] MR 3.3.2 Twists (Part 1 & 2) — ~4 min each — https://www.youtube.com/watch?v=mvGZtO_ruj0 · https://www.youtube.com/watch?v=VTv0qmLNvjg
- [ ] MR 3.4 Wrenches — ~4 min — https://www.youtube.com/watch?v=0wsYPJPGtKE

---

## F5: Calculus & matrix calculus

Playlists and hubs:
- 3Blue1Brown *Essence of Calculus*: https://3blue1brown.com/topics/calculus · playlist https://www.youtube.com/playlist?list=PLZHQObOWTQDMsr9K-rj53DwVRMYO3t5Yr
- Khan Academy Multivariable Calculus (Grant Sanderson): https://www.khanacademy.org/math/multivariable-calculus · YouTube playlist https://www.youtube.com/playlist?list=PLSQl0a2vh4HC5feHa6Rc5c0wbRTx56nF7
- MIT 18.02 Multivariable Calculus, Fall 2007 (Denis Auroux; the same lectures that 18.02SC is cut from): https://ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-videos/ · playlist https://www.youtube.com/playlist?list=PL4C4C8A7D06566F38
- MIT 18.S096 Matrix Calculus for ML and Beyond (IAP 2023, Edelman & Johnson): https://ocw.mit.edu/courses/18-s096-matrix-calculus-for-machine-learning-and-beyond-january-iap-2023 · playlist https://www.youtube.com/playlist?list=PLUl4u3cNGP62EaLLH92E_VCN4izBKK6OE

### Required: 3Blue1Brown Essence of Calculus (all 12 chapters)
1. - [ ] Ch1 The Essence of Calculus — 17 min — https://www.3blue1brown.com/lessons/essence-of-calculus · https://www.youtube.com/watch?v=WUvTyaaNkzM
2. - [ ] Ch2 The paradox of the derivative — 17 min — https://www.3blue1brown.com/lessons/derivatives · https://www.youtube.com/watch?v=9vKqVkMQHKk
3. - [ ] Ch3 Derivative formulas through geometry (power rule, trig) — ~19 min — https://www.3blue1brown.com/lessons/derivatives-power-rule · https://www.youtube.com/watch?v=S0_qX4VJhMQ
4. - [ ] Ch4 Visualizing the chain rule and product rule — 16 min — https://www.3blue1brown.com/lessons/chain-rule-and-product-rule · https://www.youtube.com/watch?v=YG15m2VwSjA
5. - [ ] Ch5 What's so special about Euler's number e? — 14 min — https://www.3blue1brown.com/lessons/eulers-number · https://www.youtube.com/watch?v=m2MIpDrF7Es
6. - [ ] Ch6 Implicit differentiation — 16 min — https://www.3blue1brown.com/lessons/implicit-differentiation · https://www.youtube.com/watch?v=qb40J4N1fa4
7. - [ ] Ch7 Limits, L'Hôpital's rule, and epsilon-delta definitions — ~18 min — https://www.3blue1brown.com/lessons/limits · https://www.youtube.com/watch?v=kfF40MiS7zA
8. - [ ] Ch8 Integration and the fundamental theorem of calculus — 21 min — https://www.3blue1brown.com/lessons/integration · https://www.youtube.com/watch?v=rfG8ce4nNh0
9. - [ ] Ch9 What does area have to do with slope? — ~13 min — https://www.3blue1brown.com/lessons/area-and-slope · https://www.youtube.com/watch?v=FnJqaIESC2s
10. - [ ] Ch10 Higher order derivatives — ~6 min — https://www.3blue1brown.com/lessons/higher-order-derivatives · https://www.youtube.com/watch?v=BLkz5LGWihw
11. - [ ] Ch11 Taylor series — 22 min — https://www.3blue1brown.com/lessons/taylor-series · https://www.youtube.com/watch?v=3d6DsjIBzJ4
12. - [ ] Ch12 The other way to visualize derivatives — ~14 min — https://www.3blue1brown.com/lessons/derivatives-and-transforms · https://www.youtube.com/watch?v=CfW845LNObM

### Required: Khan Academy multivariable (Grant Sanderson); videos from the official Khan playlist
13. - [ ] Partial derivatives, introduction — 11 min — https://www.youtube.com/watch?v=AXqhWeUEtQU
14. - [ ] Gradient — ~6 min — https://www.youtube.com/watch?v=tIpKfDc295M
15. - [ ] Directional derivative — 7 min — https://www.youtube.com/watch?v=N_ZRcLheNv0
16. - [ ] Why the gradient is the direction of steepest ascent — 11 min — https://www.youtube.com/watch?v=TEB2z7ZlRAw
17. - [ ] Gradient and contour maps — 6 min — https://www.youtube.com/watch?v=ZTbTYEMvo10
18. - [ ] Multivariable chain rule — 10 min — https://www.youtube.com/watch?v=NO3AqAaAE6o
19. - [ ] Vector form of the multivariable chain rule — ~6 min — https://www.youtube.com/watch?v=qZlBjnC3iro
20. - [ ] Local linearity for a multivariable function — ~5.5 min — https://www.youtube.com/watch?v=Vnga_psnCAo
21. - [ ] The Jacobian matrix — 6 min — https://www.youtube.com/watch?v=bohL918kXQk
22. - [ ] Computing a Jacobian matrix — ~4 min — https://www.youtube.com/watch?v=CGbBbH1e7Yw
23. - [ ] The Hessian matrix — 6 min — https://www.youtube.com/watch?v=LbBcuZukCAw
    The Khan Lagrange-multiplier videos are listed in F6.

### Required: MIT 18.S096 Matrix Calculus (core parts)
24. - [ ] Lecture 1 Part 1: Introduction and Motivation — 58 min — https://ocw.mit.edu/courses/18-s096-matrix-calculus-for-machine-learning-and-beyond-january-iap-2023/resources/ocw_18s096_lecture01_part1_2023jan18_mp4 · https://www.youtube.com/watch?v=0YqjeqLhDDE
25. - [ ] Lecture 1 Part 2: Derivatives as Linear Operators — 48 min — https://www.youtube.com/watch?v=5DUQ3-Y_gX4
26. - [ ] Lecture 2 Part 1: Derivatives in Higher Dimensions: Jacobians and Matrix Functions — 1:14 — https://ocw.mit.edu/courses/18-s096-matrix-calculus-for-machine-learning-and-beyond-january-iap-2023/resources/ocw_18s096_lecture02-part1_2023jan20_mp4 · https://www.youtube.com/watch?v=v8EIBpBEC6A
27. - [ ] Lecture 3 Part 1: Kronecker Products and Jacobians — 53 min — https://www.youtube.com/watch?v=BKo57m-uU1k
28. - [ ] Lecture 5 Part 1: Derivative of Matrix Determinant and Inverse — 28 min — https://www.youtube.com/watch?v=66Hc7vnDQ8o
29. - [ ] Lecture 7 Part 2: Second Derivatives, Bilinear Forms, and Hessian Matrices — 46 min — https://www.youtube.com/watch?v=PkpRIStmfrE

**F5 Required total: 578 min ≈ 9.6 h (1×) / 6.4 h (1.5×).**

### Optional: MIT 18.02 (Auroux) lectures, OCW per-lecture pages (the "rigor track"; each is about 50 min)
- [ ] L1 Dot Product — ~50 min — https://www.ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-1-dot-product · https://www.youtube.com/watch?v=PxCxlsl_YwY
- [ ] L2 Determinants; Cross Product — ~50 min — https://ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-2-determinants/ · https://www.youtube.com/watch?v=9FLItlbBUPY
- [ ] L3 Matrices; Inverse Matrices — ~50 min — https://www.ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-3-matrices · https://www.youtube.com/watch?v=bHdzkFrgRcA
- [ ] L8 Level Curves; Partial Derivatives; Tangent Plane Approximation — ~50 min — https://www.ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-8-partial-derivatives · https://www.youtube.com/watch?v=dK3NEf13nPc
- [ ] L9 Max-Min Problems; Least Squares — ~50 min — https://ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-9-max-min-and-least-squares/ · https://www.youtube.com/watch?v=UYe98CcxPbs
- [ ] L10 Second Derivative Test; Boundaries and Infinity — ~50 min — https://www.ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-10-second-derivative-test/ · https://www.youtube.com/watch?v=3_goGnJm5sA
- [ ] L11 Differentials; Chain Rule — ~50 min — https://www.ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-11-chain-rule · https://www.youtube.com/watch?v=7eZVshlT33Q
- [ ] L12 Gradient; Directional Derivative; Tangent Plane — 50 min — https://ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-12-gradient/ · https://www.youtube.com/watch?v=2XraaWefBd8
- [ ] L13 Lagrange Multipliers — ~50 min — https://www.ocw.mit.edu/courses/18-02-multivariable-calculus-fall-2007/resources/lecture-13-lagrange-multipliers · https://www.youtube.com/watch?v=15HVevXRsBA
  (The 18.02 total is about 7.5 h. Watch L9, L10 and L13 if you only take three.)

### Optional: other
- [ ] Khan: Second partial derivative test — ~? — https://www.youtube.com/watch?v=m1FhUjMMv30 · Saddle points https://www.youtube.com/watch?v=8aAU4r_pUUU
- [ ] Khan: The Jacobian Determinant — ~4–9 min — https://www.youtube.com/watch?v=p46QWyHQE6M
- [ ] 18.S096 Lecture 2 Part 2: Vectorization of Matrix Functions — https://ocw.mit.edu/courses/18-s096-matrix-calculus-for-machine-learning-and-beyond-january-iap-2023/resources/ocw_18s096_lecture02-part2_2023jan20_mp4 · https://www.youtube.com/watch?v=DH118YqIgow
- [ ] 18.S096 Lecture 3 Part 2: Finite-Difference Approximations — 51 min — https://www.youtube.com/watch?v=gtG62xaelPE
- [ ] 18.S096 Lecture 4 Part 1: Gradients and Inner Products in Other Vector Spaces — https://www.youtube.com/watch?v=qWYtrT4TeIs
- [ ] 18.S096 Lecture 5 Part 2: Forward Automatic Differentiation via Dual Numbers — 36 min — https://www.youtube.com/watch?v=5F6roh4pmJU
- [ ] 18.S096 Lecture 5 Part 3 / 8 Part 2: Differentiation on Computational Graphs / Automatic Differentiation on Computational Graphs (65 min) — https://www.youtube.com/watch?v=r9_5dxtDTOk · https://www.youtube.com/watch?v=43kl6wi1H6k
  This is a good bridge to F9 backprop.
- [ ] 18.S096 Lecture 6 Part 1: Adjoint Differentiation of ODE Solutions — 58 min — https://www.youtube.com/watch?v=cvBHoCAUkD4
- [ ] 3Blue1Brown: Divergence and curl — ~16 min — https://www.3blue1brown.com/lessons/divergence-and-curl · https://www.youtube.com/watch?v=rB83DpBJQsE

---

## F6: Optimization

### Required
1. - [ ] Gradient descent, how neural networks learn (3Blue1Brown, DL ch. 2) — 21 min — https://www.3blue1brown.com/lessons/gradient-descent · https://www.youtube.com/watch?v=IHZwWFHWa-w
2. - [ ] Visually Explained: Newton's Method in Optimization — 11 min — https://www.youtube.com/watch?v=W7S94pq5Xuo
   Covers the quadratic model, the Newton step, and its failure modes.
3. - [ ] Least Squares: 5 Minutes with Cyrill (Stachniss) — ~5 min — https://www.youtube.com/watch?v=87S82fh4rI4
4. - [ ] Least Squares: An Informal Introduction (Cyrill Stachniss) — ~1 h — https://www.youtube.com/watch?v=r2cyMQ5NB1o
   Covers nonlinear least squares, linearization, normal equations, and the **Gauss–Newton** iteration, in a SLAM and robotics context.
5. - [ ] Harvard AM205 video 1.8: Nonlinear Least Squares (Chris Rycroft) — 27 min — https://www.youtube.com/watch?v=4qFlaqCsnQA
   Covers **Gauss–Newton and Levenberg–Marquardt** for curve fitting. The ID comes from a single source; see "Unverified" below.
6. - [ ] Lagrange multipliers, using tangency to solve constrained optimization (Khan) — 9 min — https://www.youtube.com/watch?v=yuqB-d5MjZA · article https://www.khanacademy.org/math/multivariable-calculus/applications-of-multivariable-derivatives/constrained-optimization/a/lagrange-multipliers-examples
7. - [ ] Meaning of Lagrange multiplier (Khan) — 10 min — https://www.youtube.com/watch?v=m-G3K2GPmEQ

**F6 Required total: 143 min ≈ 2.4 h (1×) / 1.6 h (1.5×).** Strang L27 (positive-definite minima) in F4 and 18.02 L13 in F5 support this section.

### Optional
- [ ] Constrained optimization introduction (Khan) — https://www.youtube.com/watch?v=vwUV2IDLP8Q · Lagrange multiplier example, part 1 — https://www.youtube.com/watch?v=BSKtQcLQLWU
- [ ] SLAM Course 14: Least Squares (Stachniss, 2013/14) — ~1 h — https://www.youtube.com/watch?v=yVd-QDy0K6k
- [ ] ICP & Point Cloud Registration Part 3: Non-linear Least Squares (Stachniss, 2021) — https://www.youtube.com/watch?v=CJE59i8oxIE
- [ ] ICP Part 1: Known Data Association & SVD (Stachniss) — https://www.youtube.com/watch?v=dhzLQfDBx2Q
  This is Arun's method, which you can practise in F4 build 3.
- [ ] Gradient Descent, Step-by-Step (StatQuest) — 24 min — https://www.youtube.com/watch?v=sDv4f4s2SB8
- [ ] Strang 18.065 L22: Gradient Descent: Downhill to a Minimum — 53 min — https://www.youtube.com/watch?v=AeRwohPuUHQ
- [ ] 18.S096 Lecture 4 Part 2: Nonlinear Root Finding, Optimization, and Adjoint Gradient Methods — 44 min — https://www.youtube.com/watch?v=lBfqvBJaFmc
- [ ] Modern Robotics 6.2: Numerical Inverse Kinematics (Part 1 & 2): Newton–Raphson for IK — https://www.youtube.com/watch?v=VhUA0jf7tI8 · https://www.youtube.com/watch?v=24cXvgQl-nk

---

## F7: ODEs & matrix exponential

Hub: 3Blue1Brown Differential Equations topic page https://www.3blue1brown.com/topics/differential-equations

### Required
1. - [ ] Differential equations, studying the unsolvable (3Blue1Brown DE ch. 1) — ~27 min — https://www.3blue1brown.com/lessons/differential-equations · https://www.youtube.com/watch?v=p_di4Zn4wz4
   Covers what an ODE is, the pendulum, phase space, and a first numerical (Euler-style) simulation.
2. - [ ] e^(iπ) in 3.14 minutes, using dynamics (3Blue1Brown) — ~4 min — https://www.3blue1brown.com/lessons/eulers-formula-dynamically · https://www.youtube.com/watch?v=v0YEaeIClKY
3. - [ ] How (and why) to raise e to the power of a matrix (3Blue1Brown) — 27 min — https://www.3blue1brown.com/lessons/matrix-exponents · https://www.youtube.com/watch?v=O85OWBJ2ayo
4. - [ ] Strang 18.06SC L23 Differential Equations and exp(At) — 51 min — https://www.ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/least-squares-determinants-and-eigenvalues/differential-equations-and-exp-at · https://www.youtube.com/watch?v=IZqwi0wJovM
   Counted in F2–F4.
5. - [ ] Numerical Simulation of ODEs: Integrating ODEs (Steve Brunton) — ~24 min — https://www.youtube.com/watch?v=QBeNXHrAYns
   Covers vector fields, trajectories, and the forward-Euler derivation.
6. - [ ] Deriving Forward Euler and Backward/Implicit Euler Integration Schemes (Brunton) — ~23 min — https://www.youtube.com/watch?v=MstPeOTCVzQ
7. - [ ] Runge–Kutta Integrator Overview: All Purpose Numerical Integration of ODEs (Brunton) — 30 min — https://www.youtube.com/watch?v=HOWJp8NV5xU
   Covers RK2 and **RK4**.
8. - [ ] MR 3.2.3 Exponential Coordinates of Rotation (Part 1 of 2) — ~4 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-2-3-exponential-coordinates-of-rotation-part-1-of-2/ · https://www.youtube.com/watch?v=v_KBHaG0mas
9. - [ ] MR 3.2.3 Exponential Coordinates of Rotation (Part 2 of 2) — ~4 min — https://modernrobotics.northwestern.edu/nu-gm-book-resource/3-2-3-exponential-coordinates-of-rotation-part-2-of-2 · https://www.youtube.com/watch?v=WHn9xJl43nY
   Covers the matrix exp/log on so(3) and the Rodrigues formula.

**F7 Required total (excluding L23, counted in F4): 143 min ≈ 2.4 h (1×) / 1.6 h (1.5×).**

### Optional
- [ ] MR 3.3.3 Exponential Coordinates of Rigid-Body Motion — ~4 min — https://modernrobotics.northwestern.edu/?p=44 · https://www.youtube.com/watch?v=1jYMvm1U2D0
- [ ] Stability of Forward Euler and Backward Euler Integration Schemes (Brunton) — 33 min — https://www.youtube.com/watch?v=RdXCGnTT6UY
- [ ] But what is a partial differential equation? (3B1B) — ~18 min — https://www.3blue1brown.com/lessons/pdes · https://www.youtube.com/watch?v=ly4S0oi3Yz8
- [ ] Solving the heat equation (3B1B) — ~14 min — https://www.3blue1brown.com/lessons/heat-equation · https://www.youtube.com/watch?v=ToIXSwZ1pJU
- [ ] But what is a Fourier series? (3B1B) — ~25 min — https://www.3blue1brown.com/lessons/fourier-series · https://www.youtube.com/watch?v=r6sGWTCMz2k
- [ ] But what is a Laplace Transform? (3B1B, 2025) — https://www.3blue1brown.com/lessons/laplace-transform · https://www.youtube.com/watch?v=j0wJBEZdwLs

---

## F8: Probability

Hubs:
- Harvard Stat 110 (Joe Blitzstein) playlist: https://www.youtube.com/playlist?list=PL2SOU6wwxB0uwwH80KTQ6ht66KWxbzTIo
- Seeing Theory (interactive; self-paced, about 1 h, not in the video total): https://seeing-theory.brown.edu/

### Required
1. - [ ] Stat 110 L1: Probability and Counting — ~47 min — https://www.youtube.com/watch?v=KbB0FjPg0mw
2. - [ ] Stat 110 L2: Story Proofs, Axioms of Probability — ~50 min — https://www.youtube.com/watch?v=FJd_1H3rZGg
3. - [ ] Stat 110 L4: Conditional Probability — ~46 min — https://www.youtube.com/watch?v=P7NE4WF8j-Q
   Covers independence, conditional probability, and **Bayes' rule**.
4. - [ ] Stat 110 L5: Conditioning Continued, Law of Total Probability — ~49 min — https://www.youtube.com/watch?v=JzDvVgNDxo8
5. - [ ] Stat 110 L8: Random Variables and Their Distributions — ~48 min — https://www.youtube.com/watch?v=k2BB0p8byGA
   Covers PMF/CDF, Bernoulli, and Binomial.
6. - [ ] Stat 110 L9: Expectation, Indicator Random Variables, Linearity — 50 min — https://www.youtube.com/watch?v=LX2q356N2rU
7. - [ ] Stat 110 L12: Discrete vs. Continuous, the Uniform — ~49 min — https://www.youtube.com/watch?v=Tci---bVs60
   Covers PDFs, the uniform distribution, and variance.
8. - [ ] Stat 110 L13: Normal Distribution — ~46 min — https://www.youtube.com/watch?v=72QjzHnYvL0
9. - [ ] Stat 110 L14: Location, Scale, and LOTUS — ~48 min — https://www.youtube.com/watch?v=9vp1Ll2NpRw
   Covers variance and the standardization of the Normal.
10. - [ ] Stat 110 L19: Joint, Conditional, and Marginal Distributions — 50 min — https://www.youtube.com/watch?v=J70dP_AECzQ
11. - [ ] Stat 110 L21: Covariance and Correlation — 49 min — https://www.youtube.com/watch?v=IujCYxtpszU
12. - [ ] Stat 110 L29: Law of Large Numbers and Central Limit Theorem — 50 min — https://www.youtube.com/watch?v=OprNqnHsVIA
13. - [ ] Stat 110 L30: Chi-Square, Student-t, **Multivariate Normal** — 47 min — https://www.youtube.com/watch?v=MF-XSJOsGqw
    Watch the second half for the MVN.
14. - [ ] Bayes theorem, the geometry of changing beliefs (3Blue1Brown) — 15 min — https://www.3blue1brown.com/lessons/bayes-theorem · https://www.youtube.com/watch?v=HZGCoVF3YvM
15. - [ ] The medical test paradox, and redesigning Bayes' rule (3Blue1Brown) — 21 min — https://www.3blue1brown.com/lessons/better-bayes · https://www.youtube.com/watch?v=lG4VkPoG3ko
16. - [ ] Maximum Likelihood, clearly explained!!! (StatQuest) — 6 min — https://www.youtube.com/watch?v=XepXtl9YKwc
    Stat 110 does not cover MLE; it belongs to Stat 111.

**F8 Required total: 671 min ≈ 11.2 h (1×) / 7.5 h (1.5×).** Seeing Theory adds about 1 h of interactive work.

### Optional
- [ ] Stat 110 L3: Birthday Problem, Properties of Probability — https://www.youtube.com/watch?v=LZ5Wergp_PA
- [ ] Stat 110 L6: Monty Hall, Simpson's Paradox — 49 min — https://www.youtube.com/watch?v=fDcjhAKuhqQ
- [ ] Stat 110 L7: Gambler's Ruin and Random Variables — https://www.youtube.com/watch?v=PNrqCdslGi4
- [ ] Stat 110 L10: Expectation Continued — https://www.youtube.com/watch?v=P1fSFvhPf7Q
- [ ] Stat 110 L11: The Poisson Distribution — 43 min — https://www.youtube.com/watch?v=TD1N4hxqMzY
- [ ] Stat 110 L17: Moment Generating Functions — https://www.youtube.com/watch?v=N8O6zd6vTZ8
- [ ] Stat 110 L22: Transformations and Convolutions — 48 min — https://www.youtube.com/watch?v=yXwPUAIvFyg
- [ ] Stat 110 L26: Conditional Expectation Continued — https://www.youtube.com/watch?v=PgawcWisb0I
- [ ] Stat 110 L28: Inequalities (Markov, Chebyshev, Jensen) — 47 min — https://www.youtube.com/watch?v=UtXK_EQ3Pow
- [ ] But what is the Central Limit Theorem? (3B1B) — 31 min — https://www.3blue1brown.com/lessons/clt · https://www.youtube.com/watch?v=zeJD6dqJ5lo
- [ ] A pretty reason why Gaussian + Gaussian = Gaussian (3B1B) — 13 min — https://www.3blue1brown.com/lessons/gaussian-convolution · https://www.youtube.com/watch?v=d_qvLDhkg00
- [ ] Why π is in the normal distribution (3B1B) — ~24 min — https://www.3blue1brown.com/lessons/gaussian-integral · https://www.youtube.com/watch?v=cy8r7WSuT1I
- [ ] The quick proof of Bayes' theorem (3B1B) — ~3 min — https://www.3blue1brown.com/lessons/bayes-theorem-quick · https://www.youtube.com/watch?v=U_85TaXbeIo
- [ ] **Kalman filter intuition:** Visually Explained: Kalman Filters — 11 min — https://www.youtube.com/watch?v=IFeCIbljreY
- [ ] SLAM Course 03: Bayes Filter / 04: Extended Kalman Filter (Stachniss) — https://www.youtube.com/watch?v=5Pu558YtjYM · https://www.youtube.com/watch?v=DE6Jn2cB4J4

---

## F9: PyTorch & autograd

Hubs:
- Official "Introduction to PyTorch – YouTube Series" (Brad Heintz), tutorial index: https://docs.pytorch.org/tutorials/beginner/introyt/introyt_index.html · playlist https://www.youtube.com/playlist?list=PL_lsbAsL_o2CTlGHgMxNrKhzP97BaG9ZN
- Official **Learn the Basics** tutorial (text + code; self-paced about 1.5 h, not in the video total): https://docs.pytorch.org/tutorials/beginner/basics/intro.html

### Required
1. - [ ] Introduction to PyTorch (YouTube Series 1/7) — ~12 min — https://www.youtube.com/watch?v=IC0_FRiX-sw
2. - [ ] Introduction to PyTorch Tensors (2/7) — ~28 min — https://www.youtube.com/watch?v=r7QDUPb2dCM
   Covers views vs copies, broadcasting, and devices.
3. - [ ] The Fundamentals of Autograd (3/7) — ~24 min — https://www.youtube.com/watch?v=M0fX15_-xrY
4. - [ ] Building Models with PyTorch (4/7) — ~21 min — https://www.youtube.com/watch?v=OSqIP-mOWOI
5. - [ ] Training with PyTorch (6/7) — ~30 min — https://www.youtube.com/watch?v=jF43_wj_DCQ
6. - [ ] The spelled-out intro to neural networks and backpropagation: building micrograd (Karpathy) — 2:25 — https://www.youtube.com/watch?v=VMj-3S1tku0

**F9 Required total: 260 min ≈ 4.3 h (1×) / 2.9 h (1.5×).** Learn the Basics adds about 1.5 h.

### Optional
- [ ] PyTorch TensorBoard Support (5/7) — ~7 min — https://www.youtube.com/watch?v=6CEld3hZgqc
- [ ] Model Understanding with Captum (7/7) — ~11 min — https://www.youtube.com/watch?v=Am2EF9CLu-g
- [ ] What is backpropagation really doing? / Backpropagation calculus (3B1B DL ch. 3–4) — 13 min + 10 min — https://www.youtube.com/watch?v=Ilg3gGewQ5U · https://www.youtube.com/watch?v=tIeHLnjs5U8

---

## F10: C++/Eigen, ROS 2, MATLAB, Mac VM

Hubs and docs (self-paced, not in the video total):
- The Cherno C++ playlist: https://www.youtube.com/playlist?list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb
- Eigen Getting Started: https://eigen.tuxfamily.org/dox/GettingStarted.html
- Eigen Space transformations tutorial (rotations, quaternions, Isometry3d): https://eigen.tuxfamily.org/dox/group__TutorialGeometry.html
- Eigen Geometry module reference: https://eigen.tuxfamily.org/dox/group__Geometry__Module.html
- ROS 2 **Jazzy** official tutorials: https://docs.ros.org/en/jazzy/Tutorials.html · Client libraries: https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries.html
- MoveIt 2 docs: https://moveit.picknik.ai/main/ · Getting Started (Jazzy on Ubuntu 24.04 recommended): https://moveit.picknik.ai/main/doc/tutorials/getting_started/getting_started.html
- MATLAB Onramp (free, about 2 h, self-paced): https://matlabacademy.mathworks.com/details/matlab-onramp/gettingstarted
- UTM (Mac VM): https://mac.getutm.app/ · UTM's Ubuntu guide: https://docs.getutm.app/guides/ubuntu/

### Required: C++ (The Cherno, essential episodes)
1. - [ ] How C++ Works — ~20 min — https://www.youtube.com/watch?v=SfGuIVzE_Os
2. - [ ] How the C++ Compiler Works — 20:21 — https://www.youtube.com/watch?v=3tIqpEmWMLI
3. - [ ] How the C++ Linker Works — 17:55 — https://www.youtube.com/watch?v=H4s55GgAg0I
4. - [ ] C++ Header Files — 9:50 — https://www.youtube.com/watch?v=9RJTQmK0YPI
5. - [ ] POINTERS in C++ — 8:20 — https://www.youtube.com/watch?v=DTxHyVn0ODg
6. - [ ] REFERENCES in C++ — 16:59 — https://www.youtube.com/watch?v=IzoFn3dfsPA
7. - [ ] CLASSES in C++ — 9:11 — https://www.youtube.com/watch?v=2BP8NhxjrO0
8. - [ ] Constructors in C++ — 7:45 — https://www.youtube.com/watch?v=FXhALMsHwEY
9. - [ ] Virtual Functions in C++ — 8:00 — https://www.youtube.com/watch?v=oIV2KchSyGQ
10. - [ ] CONST in C++ — 14:07 — https://www.youtube.com/watch?v=4fJBrditnJU
11. - [ ] Object Lifetime in C++ (Stack/Scope Lifetimes) — 6:08 — https://www.youtube.com/watch?v=iNuTwvD6ciI
12. - [ ] SMART POINTERS in C++ (unique_ptr, shared_ptr, weak_ptr) — 9:12 — https://www.youtube.com/watch?v=UOB7-B2MfwA
13. - [ ] Copying and Copy Constructors in C++ — 11:37 — https://www.youtube.com/watch?v=BvR1Pgzzr38
14. - [ ] Dynamic Arrays in C++ (std::vector) — 16:04 — https://www.youtube.com/watch?v=PocJ5jXv8No
15. - [ ] Stack vs Heap Memory in C++ — 21:50 — https://www.youtube.com/watch?v=wJ1L2nSIV1s
16. - [ ] Templates in C++ — 16:54 — https://www.youtube.com/watch?v=I-hZkUa9mIs
    Needed to read Eigen's templated types.

### Required: build, ROS 2, VM
17. - [ ] CMake Tutorial for Absolute Beginners: From GCC to CMake including Make and Ninja — ~20–30 min — https://www.youtube.com/watch?v=NGPo7mz1oa4
18. - [ ] ROS2 Tutorial: ROS2 Humble 2H50 [Crash Course] (Robotics Back-End) — 2:50 — https://www.youtube.com/watch?v=Gg25GfA456o
    Covers nodes, topics, services, params, launch, and colcon. The concepts carry over to Jazzy; follow the official Jazzy tutorials above for exact commands.
19. - [ ] Install Ubuntu 24.04 on Apple Silicon Macs Using UTM w/ Hardware Acceleration — ~15 min — https://www.youtube.com/watch?v=JrNS3brSnmA
    Ubuntu 24.04 is the target OS for ROS 2 Jazzy.

**F10 Required total: 424 min ≈ 7.1 h (1×) / 4.7 h (1.5×).** The docs above (Eigen, ROS 2 Jazzy, MoveIt 2, MATLAB Onramp) are extra hands-on time.

### Optional
- [ ] Classes vs Structs / Arrays / Operators and Operator Overloading (Cherno) — 8:42 / 8:49 / 7:54 — https://www.youtube.com/watch?v=fLgTtaqqJp0 · https://www.youtube.com/watch?v=ENDaJi08jCU · https://www.youtube.com/watch?v=mS9755gF66w
- [ ] Lambdas in C++ (Cherno) — 31:44 — https://www.youtube.com/watch?v=mWgmBBz0y8c
- [ ] lvalues and rvalues in C++ / Move Semantics in C++ (Cherno) — 35:49 / 37:15 — https://www.youtube.com/watch?v=fbYknr-HPYE · https://www.youtube.com/watch?v=ehMg6zvXuMY
- [ ] Using Libraries in C++ (Static Linking) (Cherno) — 22:03 — https://www.youtube.com/watch?v=or1dAmUO8k0
- [ ] ITERATORS in C++ (Cherno) — 18:42 — https://www.youtube.com/watch?v=SgcHcbQ0RCQ
- [ ] Robotics Back-End "ROS2 Tutorials – ROS2 Humble For Beginners" (per-topic videos) — playlist https://www.youtube.com/playlist?list=PLLSegLrePWgJudpPUof4-nVFHGkB62Izy
- [ ] Articulated Robotics "Getting Ready to Build Robots with ROS" — playlist https://www.youtube.com/playlist?list=PLunhqkrRNRhYYCaSTVP-qJnyUPkTxJnBt (first video https://www.youtube.com/watch?v=2lIV3dRvHmQ)
- [ ] Articulated Robotics "Building a mobile robot" (URDF, Gazebo, ros2_control) — playlist https://www.youtube.com/playlist?list=PLunhqkrRNRhYAffV8JDiFOatQXuU-NnxT

---

## Coverage checklist (every required topic → video)

**F1**
- Python types → CS50P L0
- Functions → CS50P L0 (plus L9 for `*args/**kwargs` and lambdas via map/filter)
- Closures → Corey Schafer *Closures*
- Classes → CS50P L8 (optional: Corey *Dunder methods*)
- Generators → CS50P L9 and Corey *Generators*
- Exceptions → CS50P L3
- Modules → CS50P L4
- Unit tests → CS50P L5 (optional: Corey *unittest*, freeCodeCamp *pytest*)
- File I/O → CS50P L6
- NumPy arrays → Keith Galli *Complete NumPy*
- Views vs copies → *NumPy For Your Grandma 5.2* (plus Galli's copy section and PyTorch *Tensors* in F9)
- Broadcasting → *NumPy Array Broadcasting Explained* and VanderPlas
- Vectorization → VanderPlas *Losing Your Loops*
- Matplotlib → Corey Schafer Parts 1 and 10
- Shell → Missing Semester L1, L2, L5
- Git → Missing Semester L6
- Debugging/profiling (toolchain) → Missing Semester L7

**F2–F4**
- Vectors, span, linear maps, matrix multiplication, determinant, inverse, column/null space, dot/cross, change of basis, eigen, abstract spaces → 3B1B EoLA ch1–16
- Geometry of Ax=b → 18.06 L1
- Elimination → L2
- Multiplication & inverse → L3
- Transposes, permutations, spaces → L5
- Column space & nullspace → L6
- Ax=0 → L7
- Ax=b → L8
- Independence, basis, dimension → L9
- Four subspaces → L10
- Orthogonality → L14
- Projections → L15
- Least squares → L16 (plus Brunton *LS regression & SVD*)
- Orthogonal matrices & Gram–Schmidt → L17
- Determinants → L18
- Eigen → L21
- Diagonalization → L22
- exp(At) → L23
- Symmetric & positive definite → L25
- Positive definite & minima → L27
- SVD → L29 and the Brunton SVD set (pseudo-inverse, low-rank, PCA)
- 3D rotations → MR 3.2.1 (two parts), 3B1B ch5, optional quaternions
- Homogeneous transforms → MR 3.3.1 (SE(3))

**F5**
- Single-variable calculus (derivative, chain/product rule, e, implicit differentiation, limits, integrals, FTC, Taylor) → 3B1B EoC ch1–12
- Partial derivatives → Khan *Partial derivatives, introduction* (optional 18.02 L8)
- Gradient → Khan *Gradient*, *steepest ascent*, *contour maps* (optional 18.02 L12)
- Directional derivative → Khan *Directional derivative* (optional 18.02 L12)
- Multivariable chain rule → Khan *Multivariable chain rule* and *vector form* (optional 18.02 L11)
- Jacobian → Khan *Local linearity*, *The Jacobian matrix*, *Computing a Jacobian* (plus 18.S096 L2P1 and L3P1)
- Hessian / second derivative test → Khan *Hessian*, 18.S096 L7P2, Strang L27 (optional 18.02 L10)
- Lagrange multipliers → Khan videos in F6 (optional 18.02 L13)
- 18.02 dot product, determinants & cross product, matrices, max-min & least squares → optional 18.02 L1, L2, L3, L9 (also covered by 3B1B EoLA ch6/9/10 and Strang L16)
- Matrix calculus → 18.S096 L1P1, L1P2, L2P1, L3P1, L5P1, L7P2

**F6**
- Gradient descent → 3B1B DL ch. 2
- Newton's method → *Visually Explained: Newton's Method*
- Gauss–Newton → Stachniss *Least Squares: An Informal Introduction* (and AM205 1.8)
- Levenberg–Marquardt → AM205 1.8 *Nonlinear least squares*
- Lagrange multipliers / constrained optimization → Khan *Lagrange multipliers* and *Meaning of Lagrange multiplier* (plus the Khan article)

**F7**
- ODE basics and phase space → 3B1B DE ch. 1
- Matrix exponential → 3B1B *e to the power of a matrix* and Strang L23
- Euler → Brunton *Numerical Simulation of ODEs* and *Deriving Forward/Backward Euler*
- RK4 → Brunton *Runge–Kutta Integrator Overview*
- Matrix exponential of rotations (MR 3.2.3) → MR 3.2.3 Parts 1 and 2

**F8**
- Probability basics → Stat 110 L1, L2
- Conditional probability & Bayes → L4, L5, 3B1B Bayes (two videos)
- Random variables → L8
- Expectation → L9
- Variance → L12, L14 (LOTUS)
- Continuous RVs → L12
- Normal → L13
- Joint/conditional distributions → L19
- Covariance & correlation → L21
- LLN & CLT → L29 (optional 3B1B CLT)
- Multivariate normal → L30
- MLE → StatQuest *Maximum Likelihood*
- Interactive intuition → Seeing Theory
- Kalman (optional) → *Visually Explained: Kalman Filters*

**F9**
- PyTorch official intro series → YouTube Series 1–4 and 6
- Learn the Basics → docs.pytorch.org tutorial
- Autograd/backprop from scratch → Karpathy micrograd (plus Series 3/7 *Autograd*)

**F10**
- C++ → Cherno essentials (16 episodes)
- CMake → *CMake Tutorial for Absolute Beginners*
- Eigen → Getting Started, Space transformations tutorial, Geometry module docs
- ROS 2 → Jazzy official tutorials and the Robotics Back-End crash course (optional: Articulated Robotics)
- MoveIt 2 → moveit.picknik.ai Getting Started
- MATLAB → MATLAB Onramp
- Mac VM → UTM site, UTM Ubuntu guide, Ubuntu 24.04-in-UTM video

**No coverage gaps.** One thin spot: Levenberg–Marquardt rests on a single required video (AM205 1.8), whose ID is single-source. Back it up with the written LM sections in the F6 notes or the textbook.

---

## How URLs were verified

YouTube, ocw.mit.edu, cs50.harvard.edu and missing.csail.mit.edu could **not be fetched directly** from this environment because their hosts are blocked by the sandbox network policy. Verification therefore used the following:
1. **WebSearch results showing the exact URL.** This covers all 18.06SC session pages, the 18.02 (2007) lecture pages, the 18.S096 course and resource pages, the Modern Robotics ch. 3 pages, the CS50P week and notes pages, the Missing Semester 2020/2026 index, PyTorch tutorial pages, ROS 2 Jazzy tutorials, MoveIt 2 pages, Seeing Theory, the Khan Lagrange article, the 3Blue1Brown topic and lesson pages, and the Ubuntu-in-UTM video.
2. **The official sites' own source repositories on GitHub.** The Missing Semester repo gives the lecture → YouTube ID mapping and the playlists. The 3Blue1Brown website repo gives the lesson slug → video ID mapping for every 3B1B link. The pytorch/tutorials repo gives the intro-series video IDs and playlist.
3. **YouTube-API playlist dumps on GitHub plus independent references.** These give the per-lecture YouTube IDs for 18.06SC, 18.02, Stat 110, Khan multivariable, Modern Robotics, 18.S096 and Stachniss. Every other YouTube ID was found with the matching title in at least two independent GitHub repos or on a web-search result page. Doc URLs (Eigen, UTM, MATLAB Onramp) each have 70 to 2,500 GitHub references.

Exact durations come from third-party catalog data: the chapter timestamps of the 16-h CS50P full-course video, GitHub-hosted channel listings for Cherno, Matplotlib and Keith Galli, and curriculum repos that record runtimes for Stat 110, 3B1B, Khan and 18.S096. A few come from search snippets, such as Strang L15 at 48:51 and 18.S096 L2P1 at 1:14. A leading `~` marks an estimate.

## Unverified / caveats

- **Harvard AM205 video 1.8 "Nonlinear least squares"** (https://www.youtube.com/watch?v=4qFlaqCsnQA). A search aggregator confirms the title and its 27-min length, but the video ID appears in only one GitHub source. If the link fails, search YouTube for "AM205 video 1.8 nonlinear least squares Rycroft".
- **The 3Blue1Brown Differential Equations YouTube playlist ID was not confirmed**, so it is omitted. Use the topic page https://www.3blue1brown.com/topics/differential-equations, which is verified.
- **Khan Academy unit-level URLs** (for example …/multivariable-calculus/multivariable-derivatives) were not confirmed, so the list uses the verified course root, the verified Lagrange article, and the official Khan YouTube videos instead.
- **Durations marked `~` are estimates.** That includes the Missing Semester lectures, most 18.06 lectures (about 40–52 min each), the CMake video, the UTM video and the PyTorch series. They can move section totals by about ±5%.
