# 03 — Machine Learning: Deep Learning (JHU EN.601.482 / EN.601.682)

> Study guide for an incoming JHU **MSE in Robotics** student.
>
> **How this was researched:** `deep.cs.jhu.edu`, `www.cs.jhu.edu`, `arcade.cs.jhu.edu`, `courses.jhu.edu`, `semester.ly`, `coursicle.com` and `e-catalogue.jhu.edu` could **not** be fetched directly from the research sandbox. Facts from those sites come from **search-engine snippets** of those pages and are cited by URL.
> The strongest primary evidence is **a past student's public repo** (`github.com/mosquitozm100/JHU-EN.601.682-01-Deep-Learning`, graded "A"). It holds the actual **HW1–HW4 handouts (LaTeX/PDF), lab notebooks and a backprop lecture deck** from the Unberath-era course, and I read all of them. Other primary evidence is the 2018 public starter repo (`deep-learning-jhu/deep-learning-jhu-cs-482-682`) and 2023–2025 final-project repos.
> Anything I could not confirm from a primary source is marked **(unverified)**.

---

## TL;DR: read this first

| | |
|---|---|
| **Course** | **EN.601.482 (undergrad) / EN.601.682 (grad)**: *Machine Learning: Deep Learning*, **4 credits** (3 credits in Fall 2019), Dept. of Computer Science, Homewood. Credit for only one of 482/682. |
| **Creator / main instructor** | **Mathias Unberath** (ARCADE Lab; Joel Dean Excellence in Teaching Award, 2021). He taught it every year from 2019 to 2025. |
| **Other instructors** | **Fall 2025:** Unberath **+ Murat Kocaoglu**, per the ARCADE Lab page. **Spring 2025 (unverified) and Spring 2026:** **Eric Nalisnick**, per courses.jhu.edu and his CV. **Spring 2018:** Greg Hager & Robert DiPietro (older 600.482/682). **Fall 2026:** Unberath is listed, but the department page and the course search disagree on meeting times. |
| **Format (Unberath)** | MWF: two 75-min lectures plus a 50-min Friday slot used for recitations. Fall 2023/2025 met MW 4:30–5:45 and F 4:30–5:20. Piazza for materials, Gradescope for submissions, **Google Colab** notebooks. |
| **Language / framework** | **Python**. **NumPy** for from-scratch work (computation graphs, MLP forward/backward), then **PyTorch** for CNNs and everything after. Kaggle in-class competition in HW4. |
| **Grading (varies by year)** | **F2019:** HW 45 / **Midterm 20** / Project 35. **F2020:** HW 50 / Project 40 / remaining 10% unknown. **F2023:** **HW 50 / Midterm 25 / Project 25**. |
| **Homework** | About **6–7 HWs**: a mix of **written derivations (LaTeX)** and **programming (Colab)**. In F2020 that meant 7 HWs, 3 written and 4 programming. In F2023, HW1–6 were **front-loaded into the first ~2/3 of the term** so the project could take the rest. |
| **Exam** | In-class **midterm** in F2019 and F2023; the F2023 midterm was **late in the term** (Week 14, after Thanksgiving). |
| **Project** | Team project of **3–4 students** on a problem you choose: proposal, then report, then poster/demo or pitch. Reports are short (2–3 pages) with *Introduction / Methods / Results / Discussion* sections. **No late days on the project.** |
| **Textbook** | **None required.** The S2021 syllabus says the course "does not explicitly follow a specific textbook." Use Goodfellow (DLB), Prince (UDL) or d2l.ai as references. |
| **Difficulty** | Students call it "great course but be prepared to commit a lot of time," "the learning curve is enormous," and "a hard class, but one of the best classes I have taken at Hopkins." |

**Biggest implication for the sprint:** the early HWs test **hand derivation of gradients** (sigmoid/softmax/KL, matrix backprop `dL/dW = dL/dP · Xᵀ`, vanishing gradients). The middle HWs test **from-scratch NumPy MLPs that pass a gradient check** and a **PyTorch CNN with an ablation study**. If you can do those three cold before Project 1 of the semester, you will be ahead. Generative models and transformers come later in the term and in the project.

**Robotics link:** **Hands-on Robot Learning (EN.601.498/698, course 04 in this repo) lists 601.482/682 (or Intro ML) as a prerequisite.** It expects you to already train transformers and diffusion models. This course is the gateway to it.

---

## 1. Course identity (with sources)

| Field | Value | Source |
|---|---|---|
| Numbers | EN.601.482 (UG) / EN.601.682 (grad); formerly 600.482/682 (Spring 2018/2020 handouts still say "600.482/682") | HW PDFs in mosquitozm100 repo; catalog snippet "Students may receive credit for EN.601.482 or EN.601.682, but not both" |
| Title | Machine Learning: Deep Learning (handouts say "Deep Learning") | deep.cs.jhu.edu syllabi; CS course pages |
| Credits | 4 (F2020, S2021, F2023, S2026); F2019 was 3 credits | syllabus PDF titles: "Fall 2019 Syllabus (3 credits)", "Fall 2020 Syllabus (4 credits)" |
| Course site | `https://deep.cs.jhu.edu/deeplearning.html` (public syllabi, staff, project galleries, anonymous feedback); `https://www.arcade.cs.jhu.edu/teaching/machine-learning-deep-learning` (Fall 2025 page) | search snippets |
| Description | "Introduce the basic concepts of deep learning (DL)… brief introduction to the basic theoretical and methodological underpinnings of machine learning, commonly used architectures for DL, DL optimization methods, DL programming systems, and specialized applications to computer vision, speech understanding, and robotics." | CS course listing / ARCADE page snippets |
| Learning goals | Fundamental ML concepts → DL theory → contemporary architectures → **build and validate DL solutions**. Students also learn to assess a model's predictions for accuracy and **bias**, and responsible-AI themes run through the course (explainability, ethics, domain gaps). | F2020 ABET syllabus; JHU Hub 2021 "harness power of AI for good" article |
| Prereqs (F2020/S2021 syllabus) | (AS.110.201 **or** AS.110.212 **or** EN.553.291 *Linear Algebra*) **and** (EN.553.310/311 **or** EN.553.420 **or** EN.560.348 *Probability/Stats*) **and** (**EN.601.475 Machine Learning or equivalent**). **Calc III and numerical optimization recommended.** Recommended co-req: **EN.601.382 Deep Learning Lab**. | S2021 syllabus snippet |
| Prereqs (other listings) | F2023 CS listing mentions Python plus partial derivatives, matrix multiplication and probability. courses.jhu.edu: "Python, Machine Learning, Statistics, Calc III strongly recommended". **(Exact current wording unverified.)** | CS fall23 table snippet; courses.jhu.edu snippet |
| Size | Large. 2019: 16 final projects; 2021: **27 teams** (record), presented over Zoom. | JHU Hub 2019; CS news 2021 |

### 1.1 Instructor history (best reconstruction)

| Term | Instructor(s) | Evidence |
|---|---|---|
| Spring 2018 | Greg Hager & Robert DiPietro (600.482/682) | CS spring18 listing snippet; GitHub-Classroom + Travis-CI starter repo |
| Fall 2018 | (unverified) Unberath's first offering is said to be 2018. A 2018 project repo exists (`kaushiksrini/cs482-finalproject`). | CS news says "taught the class for four years" as of 2021 |
| Spring 2019, Fall 2019, Spring 2020, Fall 2020, Spring 2021 | **Mathias Unberath** (with PhD TAs, e.g. Jie Ying Wu and Cong Gao, who co-present lecture slides) | syllabi; lecture deck header "Mathias Unberath, Jie Ying Wu, Cong Gao" |
| Spring 2022, Spring 2023 | Coursicle lists "M. Unberath, S. Moreira, G. Hager" as recent professors. **Who taught which term is unverified.** | Coursicle snippet |
| Fall 2023 | Unberath. TAs: Samuel Lefcourt, Ping-Cheng Ku. In person, no lecture recordings. | F2023 syllabus snippet |
| Fall 2024 | Unberath. Project groups were mentored by lab members (e.g. "supervised by Xuzhe Zhi and Dr. Mathias Unberath"). | `acheng41/MLDLFA24-CXR` README |
| Spring 2025 | Eric Nalisnick **(unverified, one snippet)** | courses.jhu.edu snippet |
| Fall 2025 | **Unberath + Murat Kocaoglu**. TAs: Jan Emily Mangulabnan, Blanca Inigo. MW 4:30–5:45, F 4:30–5:20. A third section listed Kocaoglu with Unberath. | ARCADE Lab page snippet; courses.jhu.edu snippet |
| Spring 2026 | **Eric Nalisnick**, 2 sections, MW 1:30–2:45 + F 1:30–2:20 | courses.jhu.edu snippet; enalisnick.github.io CV ("Machine Learning: Deep Learning (Graduate), JHU, 2025–2026") |
| Fall 2026 | Unberath listed. Meeting times conflict between sources (TuTh 12–1:15 vs MW 4:30–5:45). **Check SIS.** | CS fall26 page vs courses.jhu.edu |

**What this means for you:** the course appears to run **both semesters**, with **Unberath in Fall** and **Nalisnick in Spring** (Spring is unverified for 2025). Nalisnick works on probabilistic ML and human-AI collaboration, so a Spring section may lean more **probabilistic** (Bayesian NNs, uncertainty, generative models). That is a guess and is **(unverified)**. Everything below on HW content describes the **Unberath** version. The core fundamentals (backprop, MLP, CNN, optimization, regularization, sequence models, generative models) are common to every DL course, so the prep is the same either way.

---

## 2. Lecture-by-lecture syllabus (reconstructed)

No complete current schedule was reachable. The outline below merges fragments of the **F2019, F2020, S2021 and F2023** syllabi with the lecture decks and HW due dates found in the student repo. Lecture numbers (L-x) come from the syllabi where a snippet showed them; the rest is **ordering inferred (unverified)**. The F2023 term had about 25 lectures over 14–15 weeks plus Friday recitations.

| Week | Lecture(s) | Topics | Evidence / notes |
|---|---|---|---|
| 1 | L-1 Intro; L-2 *Features & Loss* | What DL is; ML basics: features, linear classifiers `f = Wx + b`, **SVM hinge loss, softmax cross-entropy**, KL divergence | F2019 file `Lecture_2_Features_Loss.pdf`; HW1 content. S2021 recitation in week 1–2: "Q/A Week 1 and intro to Python". |
| 2 | L-3 *Regularization & Optimization* ("Basics Part II"); L-4 *Computational Graphs & Backprop Part I* | L1/L2 weight decay, GD/SGD, learning rate; computation graphs, chain rule, forward/backward pass | F2019 syllabus; `Lecture_3_Regularization_Optimization.pdf` |
| 2–3 | *Backprop Part II* (in S2020 posted as "NOT LECTURED" self-study) | Matrix calculus: scalar → vector → matrix derivatives; **backprop through `P = WX`** | `Lecture_4_Backprop_part2_NOT_LECTURED.pdf` (deck read) |
| 3 | *Neural networks / MLPs*; *Convolutional Neural Networks* | Activations (sigmoid saturation, ReLU), XOR and hidden units, universal approximation; convolution, pooling, receptive field | F2019 & F2020: "CNNs in Week 3"; HW2 XOR question |
| 4 | *Training NNs I/II* | Initialization, vanishing/exploding gradients, **batch norm**, **dropout**, **momentum / Adam**, LR schedules, **data augmentation, transfer learning** | snippet: "Training updates w/ momentum, augmentation, transfer learning"; HW3 vanishing grads; HW4 tricks list |
| 5 | *CNN architectures* | AlexNet → VGG → **ResNet** (skip connections) → DenseNet; reading group on AlexNet/VGG/ResNet | repo "Reading Group" folder (AlexNet, VGG, ResNet PDFs); HW4 cites DenseNet |
| 6 | *CV applications* | Segmentation (U-Net / FCN), detection; medical imaging examples (Unberath's area) | **(unverified)**: inferred from instructor research and project topics |
| 6–7 | *Word embeddings* | word2vec CBOW / skip-gram; "character and word embeddings" | F2019 "Current topics" snippet; Lab 2 notebook = word2vec CBOW from scratch |
| 7 | L-12 *RNNs and LSTM* | BPTT, vanishing gradients in time, LSTM/GRU gates, seq2seq | F2020 syllabus snippet "L-12) RNNs and LSTM" |
| 8 | L-13 *Unsupervised & Self-supervised Learning*; L-14 *Autoencoders & VAEs* | AE, denoising AE, **VAE + ELBO**; pretext tasks, **contrastive** learning | F2020 "L-13) Unsupervised and Self-supervised Learning"; S2021 week 8 "autoencoders and VAEs" |
| 9 | L-15 *GANs* | Minimax game, DCGAN, mode collapse, WGAN, conditional GANs | S2021 "L-15 GANs" |
| 9–10 | *Diffusion / modern generative models* | DDPM forward/reverse process, noise-prediction loss | **(unverified for this course)**: very likely in 2023+ offerings; F2023 has a week titled "Unsupervised Learning and Generative Models" |
| 10–12 | *Current topics* | **Generalization, domain gap / domain adaptation, explainable AI**, uncertainty, **human-centered AI & ethics**, bias | F2019 Week 10 "Current Topics" snippet |
| 13 | L-21 *NLP and Transformers*; L-22 *Deep Reinforcement Learning* | Attention, self-attention, transformer block, BERT/GPT; DQN / policy gradient overview | S2021 Week 13: "L-21 Natural Language Processing and Transformers, L-22 Deep Reinforcement Learning" |
| 14 | L-23 **Midterm Exam** + *Healthcare Applications* | Closed-format in-class exam (F2023) | F2023: "Week 14 (Nov 27): Midterm and Healthcare Applications"; "Recitation: Midterm Q/A" in the week of Oct 23 |
| 15 / finals | Project pitch / poster & demo | F2019 "Final Projects: Demo and Digital Poster Session"; F2020/S2021 "Synchronous project pitch" with breakout rooms | syllabi |

**Recitations / labs** (Friday slot) seen in the student repo:
- **Lab 1 — tools:** Python, NumPy, Matplotlib, Jupyter (taken from the LxMLS 2018 guide); install Anaconda + PyTorch.
- **"Python and NumPy" notebooks:** (a) Python refresher, (b) NumPy basics, (c) **logistic regression in NumPy**.
- **Lab 2 — word2vec:** train CBOW (embedding size 50, window 5), cosine-similarity nearest words, analogies `v_x ≈ v_w1 − v_w2 + v_w3`, TensorBoard projector, **CBOW from scratch**.
- **Lab 4 part 1 — logistic regression & MLP in PyTorch:** 4-vs-9 MNIST, `torch.from_numpy`, `requires_grad`, batching, MLP with 50 hidden units.
- **Lab 4 part 2 — convolutions & CNNs:** SciPy 2-D convolution with a 15×15 averaging filter, `%timeit` CPU vs **GPU on Colab**, then CNNs.

---

## 3. Textbook / resource chapter mapping per topic

No textbook is required. The mapping below lets you read *one* good treatment per topic.
**DLB** = Goodfellow, Bengio & Courville, *Deep Learning* (2016, free HTML).
**UDL** = Prince, *Understanding Deep Learning* (2023, free PDF + notebooks).
**D2L** = Zhang et al., *Dive into Deep Learning* (free, PyTorch code).
**Bishop** = Bishop & Bishop, *Deep Learning: Foundations and Concepts* (2024, free online).
Chapter numbers are from memory of the current editions. **Spot-check them against the TOCs.**

| Topic | UDL (best first read) | DLB | D2L | Bishop |
|---|---|---|---|---|
| Linear algebra / probability refresh | App. A–C | Ch 2, 3 | Ch 2 (Preliminaries) | Ch 2–3 |
| ML basics, MLE, bias–variance, generalization | Ch 2, 5, 8 | Ch 5 | Ch 3 | Ch 4 |
| Linear/logistic/softmax regression | Ch 2, 5 | Ch 5.7, 6.2 | Ch 3, 4 | Ch 4, 5 |
| MLPs, activations, universal approximation | Ch 3, 4 | Ch 6 | Ch 5 | Ch 6 |
| Loss functions as likelihoods | **Ch 5** | Ch 6.2 | Ch 4 | Ch 5 |
| Gradient descent, SGD, momentum, Adam | Ch 6 | Ch 8 | Ch 12 | Ch 7 |
| Backprop, init, vanishing/exploding | **Ch 7** | Ch 6.5, 8.4 | Ch 5.3–5.4 | Ch 8 |
| Regularization: weight decay, dropout, augmentation, early stopping | Ch 9 | Ch 7 | Ch 3.7, 5.6 | Ch 9 |
| BatchNorm / LayerNorm | Ch 11.4 | Ch 8.7.1 | Ch 8.5 | Ch 7.4 |
| CNNs | Ch 10 | Ch 9 | Ch 7 | Ch 10 |
| ResNets, modern CNNs | **Ch 11** | — | Ch 8 | Ch 9.5, 10 |
| RNN / LSTM / GRU | — (UDL has little RNN coverage) | **Ch 10** | Ch 9, 10 | Ch 12.2 (brief) |
| Attention & Transformers | **Ch 12** | — | Ch 11 | **Ch 12** |
| Autoencoders / VAEs | Ch 17 | Ch 14, 20.10.3 | — | Ch 16, 19 |
| GANs | Ch 15 | Ch 20.10.4 | Ch 20 | Ch 17 |
| Normalizing flows | Ch 16 | — | — | Ch 18 |
| Diffusion models | **Ch 18** | — | — | **Ch 20** |
| Self-supervised / contrastive | Ch 14 (unsupervised), 9.3 | Ch 15 | Ch 15 (NLP pretraining) | Ch 6.3.4 (brief) |
| Deep RL | Ch 19 | — | Ch 17 | — |
| Why DL works / ethics | Ch 20, 21 | — | — | — |
| Practical methodology / debugging | Ch 8 | **Ch 11** | Ch 6 (Builders' Guide) | — |

**Reading strategy:** UDL for intuition and clean figures, D2L for runnable PyTorch, DLB Ch 6–10 for the math the written HWs and the midterm reward, and Bishop Ch 12/20 for transformers and diffusion.

---

## 4. Assignments, projects, exams in detail

### 4.1 Homework structure and logistics (Unberath version)

- **Count:** F2020 had **7 HWs (3 written + 4 programming)**. F2023 had **HW1–6**, with "the bulk of the homework workload condensed into the first [~two-thirds] of the term". Fall 2024 due dates (from a Course Hero listing, so **unverified in detail**): HW1 Sep 4, HW3 Sep 25, HW6 Nov 6. That is roughly one HW per week through early November.
- **Written HWs:** typeset answers **inline in the provided LaTeX file**, compile to PDF and submit on **Gradescope**.
- **Programming HWs:** complete a provided **Google Colab** notebook, then submit `.py` (File → Download .py) plus a LaTeX report PDF. Notebooks include **loss and gradient-check assertions** that you must pass.
- **Late days:** F2019/F2020 gave **3 late days**, whole days only, requested in advance. F2023 gave **5 late days** with no request needed, and **zero points** after they run out. **No late days on the final project.**
- **Compute:** Colab (free GPU) was the expected platform. HW4 notebooks call `google.colab` and CUDA. A student comment on the course site complains that "it was hard to find time to rerun networks with many parameter settings": **start training HWs early**. On your M1 Max, PyTorch's `mps` backend is fine for MNIST/CIFAR-scale work.

### 4.2 Actual homework content (Spring 2020 handouts, read in full)

These are the real handouts from the student repo, headed "600.482/682 Deep Learning, Spring 2020", entry code redacted. Later years renumber and extend them, but the skills tested are the same.

**HW1 — written: probabilistic view of classification and losses**
1. **Sigmoid from Bernoulli odds:** odds `o = p/(1−p)`, `logit(p) = log o`, derive `logit⁻¹(x) = 1/(1+e^{−x})`. Compute `S(±100), S(±10), S(0)` and `S'(x)` at those points to show **saturation**.
2. **Softmax loss for a multinomial linear classifier** `f = Wx + b`, `W ∈ ℝ^{K×D}`: write `L(x,W,b,y)` and derive `∂L/∂w_k`.
3. **KL divergence asymmetry:** normalize two count vectors `P=[1,6,12,5,2,8,12,4]`, `Q=[1,3,6,8,15,10,5,2]`, compute KL(p‖q) and KL(q‖p). Write both directions as objectives in `q_W`, say which direction is easier (the entropy term of `p` is constant), and derive the gradients w.r.t. `q_W(d)`.
4. **Hand calculation** of **SVM hinge loss** and **softmax cross-entropy** for a given 3×4 `W`, `b` and `x` (natural log).

**HW2 — written + NumPy programming: computation graphs → MLP on MNIST**
1. `f(x₁,x₂,w₁,w₂) = σ(w₁x₁ + w₂x₂) + 0.5(w₁² + w₂²)`: derive all four partials, **draw the computation graph with intermediate forward/backward values** at `w=(0.3,−0.5), x=(0.2,0.4)`, implement it in NumPy, and run **30 GD steps with lr 0.01**, plotting `w₁, w₂`.
2. **XOR** in TensorFlow Playground: can a linear classifier solve it? How many ReLU hidden units are needed (compare 2/3/4) and why?
3. **MLP from scratch in NumPy on MNIST** (must pass the notebook's loss and gradient checks):
   - `linear_classifier_forward_and_backward` (softmax regression)
   - `mlp_single_hidden_forward_and_backward` (1 hidden layer, ReLU, hidden size N/2)
   - `mlp_two_hidden_forward_and_backward` (**2 hidden layers + skip connection**, ReLU)
   - Plot dev accuracy per epoch for all three (batch 50, lr 0.005, 20 epochs). Then **tune hyperparameters on dev, report test accuracy once**.

**HW3 — written: matrix backprop and vanishing/exploding gradients**
1. For `P = WX`, `L = f(P)`: **prove `∂L/∂W = (∂L/∂P) Xᵀ`** step by step. Then do one GD step (lr 0.1) of L2 loss with given 2×2 `W`, `X`, `Y`.
2. 1-D, 3-layer sigmoid network `ŷ = σ(w₃σ(w₂σ(w₁x)))` with BCE: derive σ' and its maximum (**0.25 at x=0**), and compute the gradients at `w=(0.25,−0.11,0.78), x=0.63, y=1`. They are **tiny (vanishing)**. Then switch to regression (no final sigmoid, L2 loss) with `y=128`. Now the gradients are **huge (exploding)**.

**HW4 — PyTorch programming + Kaggle: CNNs with tricks and ablations**
- Classify a **modified CIFAR-100 subset** (Kaggle in-class competition). Do **not** train on the original CIFAR-100, because the held-out test samples come from it.
- Build **3 model variants**. Each must use **≥2**, and all three together **≥5**, of: **dropout, batchnorm, skip connections, transfer learning, data augmentation, regularization, batch size / LR / LR schedulers / optimizers**. Use a fixed random seed.
- Accuracy is **not** graded ("anything above 50% is very good"; 10% initially is normal).
- **Deliverables:** (1) a **≤2-page informal report** with no introduction: highlights and intuition per model, dev accuracy/loss plot for the best model, and **ablation-study tables**, graded on depth of analysis; (2) code (Colab `.ipynb` or a Python project + `requirements.txt`), graded on correctness, consistency with the report and good practice; (3) a Kaggle submission (participation grade; **top 30% get extra credit**).
- The student repo's solution: simple CNN ablations → ResNet-18 + augmentation → ResNet-18 + augmentation + transfer learning, with DenseNet as inspiration.

**Later HWs (HW5–HW7): not found publicly (unverified).** Given the lecture order and the F2020 split (4 programming HWs), expect roughly: **RNN/LSTM or word-embedding programming**, a **generative-model HW (VAE/GAN, likely diffusion in 2023+)**, and possibly a **transformer/self-supervised HW**. The F2024 HW6 (due Nov 6) required "a Jupyter notebook and a written report" (Course Hero snippet). Prepare for all of these in §9.

### 4.3 The 2018 (Hager/DiPietro) variant, for reference

The public starter repo `deep-learning-jhu/deep-learning-jhu-cs-482-682` (fork: `ahundt/...`) used **GitHub Classroom + Travis-CI autograding + pep8 checks**:
- `p02_fashion_mnist`: a training-loop experiment script and 15 written questions.
- `p03_layers`: **re-implement PyTorch primitives yourself**: `P3SGD` (SGD with momentum), `P3Dropout`, `P3Dropout2d`, `P3LinearFunction` (**custom `torch.autograd.Function` with a manual backward**), `P3ReLU`, `P3ELUFunction` (manual backward), and `P3BCELoss`.

This is excellent practice, because it forces you to know exactly what `nn.Linear`, dropout scaling and momentum do.

### 4.4 Midterm

- **F2019:** 20% midterm. **F2023:** 25% midterm, **in class, Week 14 (L-23)**, with a "Midterm Q/A" recitation in late October.
- Format and allowed materials were not found **(unverified)**. Judging from the HWs, expect **hand derivations**: softmax/CE gradients, backprop through a small graph with numbers, matrix-calculus identities, conv output shapes and parameter counts, why BN/dropout/residuals help, LSTM gating, attention computation, the VAE ELBO, the GAN objective, and short conceptual questions (bias-variance, overfitting, domain gap, explainability).

### 4.5 Final project

| Item | Detail | Source |
|---|---|---|
| Teams | Groups of **4** (F2019) / **3** (F2020, S2021); formed early in the term | syllabus snippets |
| Topic | Teams "implement a deep learning model on a problem of their choosing"; design → implement → **validate** | CS news 2020 |
| Milestones | **Proposal** (S2021: due Apr 9), **preliminary report** (2019/2020 repo has a "Preliminary Report" doc), **final report** (S2021: due May 7), **poster/demo** (F2019: "Demo and Digital Poster Session") or **synchronous pitch** (F2020/S2021) | syllabus snippets; student repo |
| Report format | LaTeX, about **2–3 pages**, titled "CS482/682 Final Project Report Group N". Sections: **Introduction (Background, Related Work) → Methods (Dataset; Setup, Training & Evaluation) → Results → Discussion**, plus references. (F2024 reports: 2–3 pages.) | `acheng41/MLDLFA24-CXR`, `RongkunZ/JHU-DL-Final` reports |
| Mentoring | Teams can be supervised by Unberath-lab PhD students | F2024 README |
| Weight | 35% (F19) → 40% (F20) → 25% (F23) | syllabi |

**Real past projects** (to calibrate scope):
- Domain generalization for chest X-ray with style-randomization modules and episodic training on an AlexNet backbone (F2024).
- Audio-MNIST → MNIST image VAE (cross-modal generation, F2024).
- **Robot self-modeling with MLP + implicit neural representations (SIREN/SDF) for 3D robot reconstruction** (F2023, a *robotics* project).
- Seamless texture GAN with FID evaluation (S2023).
- WaveNet audio upsampling (S2021).
- DANN domain adaptation + VGG16 ensembles (S2022).
- Optical-flow + kinematics surgical gesture encoders (`jabarragann/cs682-DeepLearning-Project`, S2022, a *surgical-robotics* project).
- Pokémon VQA (2019).
- Multi-class classification with cross-validation (S2020).

**Tip:** pick a robotics/perception project you can reuse. For example: a behavior-cloning policy from images, a depth or segmentation network for a manipulation scene, or surgical tool or gesture recognition (JIGSAWS). Unberath's lab (ARCADE) works on medical imaging, surgical data science and human-centered AI, so projects in that area tend to get good mentorship.

---

## 5. Core concept checklist with key equations

Tick each box when you can **derive it on paper and implement it in under 30 lines**.

### 5.1 Linear models, losses, MLE
- [ ] **Linear regression:** `ŷ = wᵀx + b`. MSE `L = (1/N)Σ(y_i − ŷ_i)²`. Closed form `w* = (XᵀX)⁻¹Xᵀy`. Gradient `∇_w L = (2/N)Xᵀ(Xw − y)`.
- [ ] **MLE view:** a Gaussian noise model `y ~ N(wᵀx, σ²)` gives `−log p(y|x) = (y − wᵀx)²/(2σ²) + const`, so **MSE = Gaussian NLL**. A Bernoulli model gives BCE, and a categorical model gives softmax CE. (UDL Ch 5 recipe: pick a distribution, have the network predict its parameters, minimize the NLL.)
- [ ] **Logistic regression:** `p = σ(z) = 1/(1+e^{−z})`. `σ'(z) = σ(z)(1−σ(z)) ≤ 0.25`. BCE `L = −[y log p + (1−y) log(1−p)]`. **`∂L/∂z = p − y`.**
- [ ] **Softmax:** `p_k = e^{z_k}/Σ_j e^{z_j}`. CE `L = −Σ_k y_k log p_k = −z_y + log Σ_j e^{z_j}`. **`∂L/∂z = p − y`**, so `∂L/∂W = (p − y)xᵀ`. **Numerical stability:** subtract `max(z)` before exponentiating, i.e. use log-sum-exp.
- [ ] **Multiclass SVM (hinge):** `L_i = Σ_{j≠y_i} max(0, s_j − s_{y_i} + Δ)`, with `Δ = 1`.
- [ ] **KL:** `KL(p‖q) = Σ p log(p/q) ≥ 0`, and it is asymmetric. `CE(p,q) = H(p) + KL(p‖q)`, so minimizing CE over q is the same as minimizing **forward KL** (mean-covering). Reverse KL `KL(q‖p)` is mode-seeking.
- [ ] **L2 regularization:** `L + (λ/2)‖w‖²` is MAP with a Gaussian prior. L1 is MAP with a Laplace prior and gives sparsity.

### 5.2 Optimization
- [ ] **GD / SGD:** `θ ← θ − η ∇_θ L_B(θ)` over mini-batch `B`. Gradient noise variance is ∝ 1/|B|.
- [ ] **Momentum:** `v ← μv − η∇L; θ ← θ + v` (μ ≈ 0.9). **Nesterov:** evaluate the gradient at `θ + μv`.
- [ ] **AdaGrad / RMSProp:** `s ← ρs + (1−ρ)g²; θ ← θ − η g/(√s + ε)`.
- [ ] **Adam:** `m ← β₁m + (1−β₁)g`, `v ← β₂v + (1−β₂)g²`, bias-correct `m̂ = m/(1−β₁ᵗ)`, `v̂ = v/(1−β₂ᵗ)`, update `θ ← θ − η m̂/(√v̂ + ε)` (β₁=0.9, β₂=0.999, ε=1e-8). **AdamW:** decoupled weight decay `θ ← θ − ηλθ`.
- [ ] **LR schedules:** step decay, cosine `η_t = η_min + ½(η_max − η_min)(1 + cos(πt/T))`, linear warmup (essential for transformers).
- [ ] **Gradient clipping:** `g ← g · min(1, c/‖g‖)` (needed for RNNs).

### 5.3 Backprop and computational graphs
- [ ] **Chain rule on a DAG:** `∂L/∂x = Σ_{children c} (∂L/∂c)(∂c/∂x)`. Each node implements `forward` and `backward(upstream_grad)` and gradients **accumulate (+=)** at fan-out.
- [ ] **Local gradients:** add distributes the gradient; mul swaps the inputs; max routes the gradient to the argmax; ReLU masks it with `1[x>0]`.
- [ ] **Matrix forms** (HW3): `P = WX` gives `∂L/∂W = (∂L/∂P)Xᵀ` and `∂L/∂X = Wᵀ(∂L/∂P)`. For row-major batches `H = XW + b`: `dW = Xᵀ dH`, `db = Σ_rows dH`, `dX = dH Wᵀ`. **Shape check:** a gradient always has the shape of its variable.
- [ ] **Gradient check:** centered difference `(f(θ+h) − f(θ−h))/2h`, `h ≈ 1e-5`, relative error `|a−b|/max(|a|,|b|) < 1e-6` (float64).
- [ ] **Reverse vs forward mode:** reverse mode costs about one forward pass per *scalar output*, which is why it is used for losses.

### 5.4 Initialization and normalization
- [ ] **Vanishing/exploding:** the gradient is a product of Jacobians `Π W_lᵀ diag(σ'(z_l))`. Sigmoid caps each factor at 0.25 (HW3).
- [ ] **Xavier/Glorot:** `Var(W) = 2/(n_in + n_out)` (tanh). **He/Kaiming:** `Var(W) = 2/n_in` (ReLU). Both keep activation variance constant across layers.
- [ ] **BatchNorm** (per feature over the batch): `x̂ = (x − μ_B)/√(σ²_B + ε)`, `y = γx̂ + β`. Running statistics are used at eval time (`model.eval()`!). It smooths the loss landscape and allows higher learning rates.
- [ ] **LayerNorm** (per sample over features) is batch-size independent and is the default in transformers (Pre-LN: `x + Attn(LN(x))`). Also know GroupNorm/InstanceNorm.

### 5.5 Regularization and generalization
- [ ] **Dropout (inverted):** at train time `h ← h ⊙ m / (1−p)` with `m ~ Bernoulli(1−p)`; at test time it is the identity. It approximates an ensemble.
- [ ] Data augmentation (flips, crops, color jitter, Mixup/CutMix), early stopping, weight decay, label smoothing `y' = (1−ε)y + ε/K`.
- [ ] **Bias–variance:** `E[(y − f̂)²] = Bias² + Var + σ²`. Also know **double descent** (UDL Ch 8).
- [ ] **Transfer learning:** freeze the backbone and train a head (linear probe), or fine-tune everything with a lower LR.

### 5.6 CNNs
- [ ] **Output size:** `O = ⌊(W − K + 2P)/S⌋ + 1`. "Same" padding for odd K at S=1 is `P = (K−1)/2`.
- [ ] **Params per conv layer:** `(K·K·C_in + 1)·C_out`. **FLOPs (MACs):** `K²·C_in·C_out·H_out·W_out`.
- [ ] **Receptive field:** `r_l = r_{l−1} + (K_l − 1)·Π_{i<l} S_i`.
- [ ] Pooling (max/avg, global average pooling), 1×1 conv (channel mixing), depthwise-separable conv, transposed conv (output `(W−1)S − 2P + K`), dilated conv.
- [ ] **Equivariance** to translation (conv) vs **invariance** (pooling).
- [ ] **ResNet:** `y = F(x) + x`, so `∂y/∂x = I + ∂F/∂x` and gradients flow through the identity. Bottleneck block 1×1 → 3×3 → 1×1. Know the history: LeNet → AlexNet → VGG (stacked 3×3) → Inception → ResNet → DenseNet (concatenate) → EfficientNet/ConvNeXt.
- [ ] **Segmentation / detection basics:** FCN, **U-Net** (encoder–decoder + skip connections), IoU, Dice loss `1 − 2|A∩B|/(|A|+|B|)`. Anchors/NMS (Faster R-CNN, YOLO) at a conceptual level.

### 5.7 Sequence models
- [ ] **Vanilla RNN:** `h_t = tanh(W_hh h_{t−1} + W_xh x_t + b)`, `y_t = W_hy h_t`. **BPTT:** `∂h_T/∂h_k = Π_{t=k+1}^T diag(1 − h_t²) W_hh`, so it vanishes or explodes with the spectral radius of `W_hh`.
- [ ] **LSTM:** `f,i,o = σ(W[h_{t−1}, x_t] + b)`, `g = tanh(·)`; `c_t = f ⊙ c_{t−1} + i ⊙ g`; `h_t = o ⊙ tanh(c_t)`. The additive cell path ("constant error carousel") is what lets gradients survive. **GRU:** update/reset gates.
- [ ] **word2vec:** skip-gram / CBOW, negative-sampling loss `−log σ(u_oᵀv_c) − Σ_k log σ(−u_kᵀv_c)`.
- [ ] Seq2seq with teacher forcing; beam search; perplexity `exp(mean NLL)`.

### 5.8 Attention and Transformers
- [ ] **Scaled dot-product attention:** `Attn(Q,K,V) = softmax(QKᵀ/√d_k + M) V`, with `Q = XW_Q`, `K = XW_K`, `V = XW_V`. The mask `M` is −∞ above the diagonal for causal models. Dividing by `√d_k` keeps the logit variance at about 1.
- [ ] **Multi-head:** `MHA(X) = Concat(head_1..head_h) W_O`, `d_k = d_model/h`.
- [ ] **Block (Pre-LN):** `X ← X + MHA(LN(X))`; `X ← X + FFN(LN(X))` with `FFN = W₂ GELU(W₁x)` (4× expansion).
- [ ] **Positional encoding:** sinusoidal `PE(pos,2i) = sin(pos/10000^{2i/d})`, learned, or RoPE. Self-attention cost is **O(n²d)**.
- [ ] Encoder (BERT, masked LM), decoder (GPT, next-token CE), encoder–decoder (cross-attention). **ViT:** split the image into 16×16 patches, add a linear embedding, a [CLS] token and position embeddings.
- [ ] Parameter count per block ≈ `12·d_model²`.

### 5.9 Generative models
- [ ] **Autoencoder:** `min ‖x − g(f(x))‖²`.
- [ ] **VAE ELBO:** `log p(x) ≥ E_{q_φ(z|x)}[log p_θ(x|z)] − KL(q_φ(z|x) ‖ p(z))`. **Reparameterization:** `z = μ + σ ⊙ ε`, `ε ~ N(0, I)`. For a Gaussian `q` and `p(z) = N(0,I)`: `KL = ½ Σ_j (μ_j² + σ_j² − log σ_j² − 1)`. β-VAE weights the KL term by β.
- [ ] **GAN:** `min_G max_D E_x[log D(x)] + E_z[log(1 − D(G(z)))]`. The optimal `D* = p_data/(p_data + p_g)` gives `2·JSD − log 4`. Use the **non-saturating** generator loss `−log D(G(z))`. Failure modes: mode collapse, instability. **WGAN:** Earth-Mover distance with a Lipschitz critic (gradient penalty). Metrics: FID, IS.
- [ ] **Diffusion (DDPM):** forward process `q(x_t|x_{t−1}) = N(√(1−β_t) x_{t−1}, β_t I)`. Closed form `x_t = √ᾱ_t x_0 + √(1−ᾱ_t) ε`, with `ᾱ_t = Π(1−β_s)`. **Loss:** `E_{t,x₀,ε} ‖ε − ε_θ(x_t, t)‖²`. Sampling: `x_{t−1} = (1/√α_t)(x_t − (β_t/√(1−ᾱ_t)) ε_θ) + σ_t z`. Also know DDIM (deterministic, fewer steps) and **classifier-free guidance** `ε̃ = (1+w)ε_θ(x_t,c) − w ε_θ(x_t,∅)`. **Diffusion Policy** in robotics is exactly this, conditioned on observations and producing action sequences.
- [ ] **Normalizing flows:** `log p(x) = log p(z) + log|det ∂z/∂x|` (concept only).

### 5.10 Self-supervised / contrastive learning
- [ ] Pretext tasks: rotation prediction, jigsaw, inpainting, masked autoencoding (**MAE**: mask 75% of patches and reconstruct pixels).
- [ ] **InfoNCE / NT-Xent (SimCLR):** `ℓ_i = −log [exp(sim(z_i,z_j)/τ) / Σ_{k≠i} exp(sim(z_i,z_k)/τ)]`, with cosine similarity, two augmented views as the positive pair, and the other batch items as negatives. **MoCo:** momentum encoder + queue. **BYOL/DINO:** no negatives (EMA teacher, stop-gradient). **CLIP:** image–text contrastive learning.
- [ ] Evaluation: **linear probe** and k-NN accuracy on frozen features.

### 5.11 Evaluation and practice
- [ ] Train/dev/test splits (HW2: tune on dev, report test **once**), k-fold CV, data leakage.
- [ ] Classification: accuracy, precision/recall/F1, ROC-AUC, confusion matrix, **calibration (ECE)**. Segmentation: IoU/Dice. Generative: FID, NLL/bits-per-dim.
- [ ] **Ablation studies** (HW4: one change at a time, fixed seed, report a table).
- [ ] Debugging recipe: **overfit one batch first**, check initial loss ≈ `log K` for K classes, plot LR vs loss, look at the data.
- [ ] Explainability: saliency/gradients, Grad-CAM. Domain shift, distribution shift, uncertainty (MC dropout, ensembles), fairness/bias. These are the instructor's "current topics."

### 5.12 Deep RL overview (one lecture)
- [ ] MDP `(S, A, P, R, γ)`. Q-learning target `r + γ max_a' Q(s',a')`. DQN = replay buffer + target network. Policy gradient `∇J = E[∇ log π_θ(a|s) Â]`. Concept level only.

---

## 6. Difficulty, student reviews, tips

**What students say** (anonymous feedback quoted on deep.cs.jhu.edu, via search snippets):
- "great course but be prepared to commit a lot of time to the course"
- "the learning curve is enormous!"
- "a hard class, but also one of the best classes I have taken at Hopkins!"
- "very challenging but also very helpful in learning the materials"
- It was hard to find time to rerun networks with many parameter settings.

**Other signals:**
- Unberath won the **Professor Joel Dean Excellence in Teaching Award** (2021), and the course is described as "popular", with a record 27 project teams in 2021.
- No Reddit threads specific to this course were found. Coursicle and semester.ly review pages exist but could not be read.

**Tips derived from the actual HWs:**
1. **Matrix calculus fluency is the #1 differentiator.** HW1–3 are pure derivations: practice `∂(softmax CE)/∂W`, `∂L/∂W = (∂L/∂P)Xᵀ`, and sigmoid saturation numbers by hand. Typeset in LaTeX quickly (learn Overleaf now).
2. **Gradient checks pass or you get nothing.** Write the gradient-check utility first (see §9 Project 1).
3. **HW4-style ablations:** keep a fixed seed, change one thing at a time, log every run (W&B or a CSV), and **write the report with tables**. Grading is on analysis depth, not accuracy.
4. **Start GPU HWs early.** Colab sessions time out, and Kaggle does not accept late submissions.
5. **Front-loaded workload:** HWs pile up in the first ~10 weeks (F2023), so the project and the late midterm (F2023) overlap at the end. Form your project team in week 1–2.
6. Use **LaTeX + Gradescope page matching**, and keep the provided function signatures exactly (autograder/assertions).
7. Graduate (682) students may have extra questions or expectations **(unverified)**. Typical for JHU 4xx/6xx pairs.
8. Your **MSE Robotics** background helps you pick a strong project (perception, policy learning, surgical robotics), and ARCADE/LCSR labs can mentor it.

---

## 7. Best free resources (with URLs)

| Resource | Why | URL |
|---|---|---|
| **Karpathy — Neural Networks: Zero to Hero** | micrograd (autograd from scratch) → makemore (bigram, MLP, **BatchNorm & activation stats**, **"becoming a backprop ninja"** = manual backprop through a whole net) → **GPT from scratch** → tokenizer → GPT-2 repro. The single best match for HW2/HW3 skills. | https://karpathy.ai/zero-to-hero.html · https://github.com/karpathy/micrograd · https://github.com/karpathy/nanoGPT · https://github.com/karpathy/build-nanogpt |
| **Stanford CS231n** | Notes on backprop, optimization and CNNs; **assignments** = NumPy softmax/SVM/2-layer net, FC nets + BN + dropout + CNN layers with gradient checks, then RNN/Transformer captioning, GANs/self-supervised (recent years add diffusion, **unverified**). The closest public analog to this course's HWs. | https://cs231n.github.io/ · https://cs231n.stanford.edu/ |
| **Prince — Understanding Deep Learning (UDL)** | Clearest modern textbook; free PDF + ~70 Python notebooks; strong on loss-as-likelihood, init, residuals, transformers, VAEs, diffusion. | https://udlbook.github.io/udlbook/ |
| **Dive into Deep Learning (d2l.ai)** | Runnable PyTorch for every architecture (LeNet→ResNet, RNN/LSTM, attention/transformers, optimizers). | https://d2l.ai |
| **Goodfellow et al. — Deep Learning** | Rigorous math for MLPs, regularization, optimization, CNNs, **RNNs (Ch 10)**, practical methodology (Ch 11). | https://www.deeplearningbook.org/ |
| **Bishop & Bishop — Deep Learning: Foundations and Concepts** | Modern, Bishop-quality treatment incl. transformers (Ch 12) and diffusion (Ch 20). | https://www.bishopbook.com/ |
| **fast.ai — Practical Deep Learning for Coders** | Top-down, application-first; transfer learning and training tricks fast. | https://course.fast.ai/ |
| **3Blue1Brown — Neural networks series** | Visual intuition for GD, backprop, attention/transformers. | https://www.3blue1brown.com/topics/neural-networks |
| **The Matrix Calculus You Need for Deep Learning** (Parr & Howard) | Exactly the math of HW3. | https://explained.ai/matrix-calculus/ |
| **CS231n backprop notes / "Derivatives, backprop, vectorization"** | Short, practical matrix backprop. | https://cs231n.github.io/optimization-2/ · http://cs231n.stanford.edu/handouts/derivatives.pdf |
| **The Annotated Transformer** | Line-by-line PyTorch transformer. | https://nlp.seas.harvard.edu/annotated-transformer/ |
| **Lilian Weng — diffusion / contrastive posts** | Best concise derivations of DDPM and contrastive losses. | https://lilianweng.github.io/posts/2021-07-11-diffusion-models/ · https://lilianweng.github.io/posts/2021-05-31-contrastive/ |
| **Mathematics for Machine Learning (Deisenroth et al.)** | Linear algebra, vector calculus, probability refresher. | https://mml-book.github.io/ |
| **CMU 11-785 Intro to DL** | Very hands-on alternative with "MyTorch" from-scratch homework if you want more practice. | https://deeplearning.cs.cmu.edu/ |
| **Past JHU student repos** (read only; do not copy for graded work) | Real HW1–HW4 handouts, labs, project report | https://github.com/mosquitozm100/JHU-EN.601.682-01-Deep-Learning · https://github.com/deep-learning-jhu/deep-learning-jhu-cs-482-682 |
| **Robotics bridges** | Diffusion Policy; LeRobot (ACT, Diffusion Policy code); the course-04 sheet in this repo | https://diffusion-policy.cs.columbia.edu/ · https://github.com/huggingface/lerobot |

---

## 8. Gaps / prerequisites to fill and math refreshers

Ranked by how much each one costs you in this course:

1. **Multivariable / matrix calculus** (the biggest risk). You need Jacobians, the chain rule on vectors, and `∂(aᵀx)/∂x = a`, `∂(xᵀAx)/∂x = (A+Aᵀ)x`, `∂ tr(AB)/∂A = Bᵀ`, plus the row- vs column-layout conventions. → Parr & Howard; MML Ch 5; CS231n derivatives handout. **Drill:** derive softmax-CE gradients and 2-layer-MLP gradients on paper and verify them numerically.
2. **Probability:** Bernoulli/categorical/Gaussian; expectation and variance; Bayes; **MLE and MAP**; KL and entropy; the reparameterization trick. → MML Ch 6; UDL Ch 5 and App. C.
3. **Linear algebra:** matrix multiply as a linear map, shapes and broadcasting, eigenvalues and spectral radius (for exploding RNNs), SVD (PCA ↔ linear autoencoder), norms. → MML Ch 2–4; 3Blue1Brown *Essence of Linear Algebra*.
4. **Classical ML (the formal prerequisite is EN.601.475 Machine Learning):** train/val/test, overfitting, bias–variance, regularization, logistic regression, k-NN, SVM idea, cross-validation, PCA. → UDL Ch 2, 8; CS231n A1; Andrew Ng's CS229 notes if time permits.
5. **Optimization basics** ("numerical optimization recommended"): convexity, GD convergence and the role of the learning rate, saddle points, conditioning, momentum intuition.
6. **NumPy vectorization & broadcasting:** no Python loops over samples. Know `einsum`, `np.add.at`, and stable softmax.
7. **PyTorch fluency:** `Dataset/DataLoader`, `nn.Module`, `train()`/`eval()`, `torch.no_grad()`, optimizers/schedulers, `.to(device)` (MPS on your Mac), seeding, checkpointing, custom `autograd.Function`.
8. **Tooling:** LaTeX/Overleaf (every HW report), Colab + Kaggle CLI, Weights & Biases or TensorBoard, git.

**Quick self-test, before Project 1:** can you, without notes, (a) write stable softmax-CE and its gradient in NumPy, (b) state He init, (c) compute a conv output shape, and (d) write the ELBO? If any is shaky, schedule the matching drill from §9.

---

## 9. Build-first project sequence (do in order; you write all code)

Each project is a long focused block. The pattern is **build first (morning) → read the theory for what you just built (afternoon) → hand-derive and write it up in LaTeX like a HW (evening)**. All code goes in `~/dl-sprint/` with one folder per day. Use the `mps` device on the M1 Max and Colab only if something is slow.

### Project 1: Autograd from scratch + the HW1/HW3 math
- **Build:** a micrograd-style scalar autograd `Value` class (`+ * ** exp log tanh relu`, topological-sort backward), then a tiny MLP on the two-moons dataset (follow Karpathy lecture 1).
- **Extend:** a **tensor version** in NumPy with `Linear`, `ReLU`, `Sigmoid`, `SoftmaxCE` modules, each with `forward/backward`, plus a **generic gradient checker** (centered differences, relative error).
- **Reproduce HW2-Q1:** `f = σ(w₁x₁+w₂x₂) + 0.5‖w‖²`. Draw the graph with intermediate values, run 30 GD steps at lr 0.01, and plot `w₁, w₂`.
- **Derive on paper (LaTeX):** sigmoid from log-odds; softmax-CE gradient `p − y`; `∂L/∂W = (∂L/∂P)Xᵀ`; KL asymmetry with HW1's two vectors; hinge vs CE on a toy example; the HW3 vanishing/exploding-gradient numbers.
- **Read:** UDL Ch 5 & 7; CS231n optimization-2 notes; Karpathy "backprop ninja" (lecture 5) for practice.
- ✅ **Milestone:** all gradient checks < 1e-6, and a 1-page LaTeX "HW" with the derivations.

### Project 2: NumPy MLP on MNIST (= HW2-Q3) + optimization & regularization
- **Build:** load MNIST and normalize with train-set statistics only. Implement `linear_classifier`, a 1-hidden-layer MLP (hidden N/2, ReLU), and a **2-hidden-layer MLP with a skip connection**, all with hand-written backward passes that pass your grad check. Train with batch 50, lr 0.005, 20 epochs, and plot dev accuracy for all three.
- **Add (from scratch):** SGD+momentum, Adam, L2 weight decay, inverted dropout, He vs naive init. Compare activation histograms per layer.
- **Do it properly:** tune on dev, then report test accuracy once. Expect ~92% (linear) and ~98% (MLP).
- **Port to PyTorch** in 30 lines and confirm the same curves. Then write `P3Linear` as a custom `torch.autograd.Function` with a manual backward, and check it with `torch.autograd.gradcheck` (2018 HW style).
- **Read:** UDL Ch 6, 9; DLB Ch 7–8 skim; BatchNorm paper abstract and Karpathy lecture 4 (activations, gradients, BN).
- ✅ **Milestone:** a plot of three models' dev curves plus an optimizer/regularizer comparison table.

### Project 3: PyTorch CNNs on CIFAR (= HW4) + architectures + robotics perception
- **Build:** a CIFAR-10 pipeline (or a CIFAR-100 subset to mimic HW4) with augmentation (random crop + flip), a **simple CNN → +BN → +dropout → ResNet-18-style net (write the BasicBlock yourself)**, and **transfer learning** (torchvision ResNet-18 pretrained, linear probe vs fine-tune). Use AdamW or SGD+momentum with a cosine schedule.
- **Ablation table:** 5+ tricks, fixed seed, one change at a time, with a **≤2-page report in LaTeX** using HW4's exact deliverable format.
- **Conv arithmetic drills:** output sizes, parameter counts, MACs and receptive field for your network (compute them by hand, then verify with `torchinfo`).
- **Robotics tie-in (1–2 h):** fine-tune a small **U-Net** or `torchvision` segmentation model on a handful of tabletop/robot images, or train a **keypoint/pose regressor**. Perception networks like this are what a manipulation policy consumes.
- **Read:** UDL Ch 10–11; d2l Ch 7–8; ResNet paper (§3); Grad-CAM paper skim. Run Grad-CAM on your model.
- ✅ **Milestone:** ResNet-18 from scratch ≥ 90% on CIFAR-10 (≈ 30–60 min on MPS/Colab), plus an ablation report.

### Project 4: Sequences: RNN/LSTM → attention → mini-GPT
- **Morning:** a character-level **vanilla RNN in NumPy with BPTT** (min-char-rnn style) on a small text. Watch the gradient norms and add clipping. Then an `nn.LSTM` char-model in PyTorch, and write the **LSTM cell equations yourself** (a manual `LSTMCell`) and match the PyTorch outputs.
- **Afternoon:** a **nanoGPT-style transformer from scratch** (Karpathy lecture 7): token + position embeddings, **causal multi-head self-attention** (write the mask and `√d_k` scaling yourself), Pre-LN blocks, FFN, and a weight-tied head, trained on tiny-shakespeare. Report perplexity and samples.
- **Evening:** word2vec CBOW with negative sampling (the Lab 2 equivalent, 1 h). Then derive **attention shapes, the parameter count (~12d² per block) and the O(n²) cost** on paper. Compare a **ViT-tiny** on CIFAR-10 with your ResNet if time allows.
- **Read:** DLB Ch 10 (RNN/LSTM); UDL Ch 12; Annotated Transformer; Bishop Ch 12.
- ✅ **Milestone:** a mini-GPT that generates plausible text, and a manual LSTM cell that matches `nn.LSTM`.

### Project 5: Generative & self-supervised: VAE, GAN, DDPM, SimCLR
- **VAE on MNIST** (2 h): MLP or conv encoder → `(μ, log σ²)` → reparameterize → decoder. Loss = BCE recon + analytic KL. Visualize the 2-D latent space and interpolations. **Derive the ELBO and the Gaussian KL on paper.**
- **DCGAN on MNIST / Fashion-MNIST** (2 h): use the non-saturating loss and observe instability and mode collapse. Write out `D*` and the JSD result.
- **Tiny DDPM on MNIST** (4 h): a small U-Net with a time embedding, linear β schedule (T=1000), `ε`-prediction MSE, then ancestral sampling and **DDIM** sampling with 50 steps. Add **class-conditioning + classifier-free guidance**. This is the core of Diffusion Policy.
- **SimCLR-mini on CIFAR-10** (2 h): two augmentations → ResNet-18 encoder → projection head → NT-Xent (τ≈0.5). Evaluate with a **linear probe** against your supervised baseline.
- **Read:** UDL Ch 14, 15, 17, 18; Lilian Weng's diffusion & contrastive posts; Bishop Ch 20.
- ✅ **Milestone:** a grid of samples from VAE/GAN/DDPM plus a linear-probe accuracy number.

### Project 6: Robotics capstone (project rehearsal) + midterm drill
- **Mini project, in the course's report format** (Intro/Background/Related Work → Methods → Results → Discussion, 2–3 pages LaTeX):
  - **Option A — Behavior cloning from images:** collect or download demos (e.g. **PushT** or ALOHA-sim via **LeRobot**, or a gymnasium/MuJoCo task). Train a **CNN encoder + MLP policy (BC)** and then a **small Diffusion Policy** (1-D temporal U-Net or transformer over action chunks, conditioned on image features), and compare success rates. This bridges directly to course 04 (Hands-on Robot Learning).
  - **Option B — Perception for manipulation:** grasp-point or keypoint heatmap prediction, or segmentation of objects/tools (e.g. surgical tools for an ARCADE/LCSR-flavored project), with an ablation table and failure-case analysis (domain shift, calibration).
- **Midterm drill (3 h, closed book, timed):** (1) backprop through a 6-node graph with numbers; (2) softmax-CE gradient; (3) conv shapes/params for a 5-layer CNN; (4) why BN/residuals/LSTM gates help with gradients; (5) attention for a 3-token example by hand; (6) the ELBO and the reparameterization trick; (7) the GAN objective and `D*`; (8) the DDPM forward closed form; (9) bias–variance, overfitting diagnosis from learning curves; (10) domain shift and explainability short answers.
- **Wrap-up:** fill in every box of §5 and append to `PROGRESS.md`.

### How this connects to robotics
- **Perception:** CNNs, ViTs and U-Nets for detection, segmentation, depth, pose and keypoints. These feed grasp planning, visual servoing and SLAM front-ends (course 02 Computer Vision; course 06 CIS I for medical/surgical imaging).
- **Policy learning:** behavior cloning is supervised learning with an MSE/NLL loss over actions. **ACT** is a transformer + CVAE (§5.8 + §5.9). **Diffusion Policy** is DDPM over action chunks (§5.9). **VLAs** are transformers with VLM backbones. All of it is the course-04 toolkit, and course 04 lists 601.482/682 as a prerequisite.
- **Representation learning:** contrastive/self-supervised pretraining (R3M, MVP, DINO features) is used as frozen visual encoders for robot policies.
- **Sequence models:** transformers and LSTMs model trajectories, action chunks and time-series sensor data. Deep RL (one lecture here) leads into sim-to-real policy training.
- **Uncertainty & domain gap** (Unberath's "current topics") matter for safe deployment on robots and in surgery.

---

## 10. Sources

**Primary (read directly via git clone):**
- Past student repo with S2020 handouts HW1–HW4, labs and lecture deck: https://github.com/mosquitozm100/JHU-EN.601.682-01-Deep-Learning
  (files: `Deep Learning hw1/DeepLearning_Fall2019_hw1.tex` [titled Spring 2020], `Deep Learning hw2/DL_hw2.tex`, `Deep Learning hw3/DeepLearning_Fa19_hw3.tex`, `Deep Learning hw4/DeepLearning_sp20_hw4.pdf`, `Lecture_4_Backprop_part2_NOT_LECTURED.pdf`, `lab1/tool_tutorials.pdf`, `lab2/Deep_Learning_Lab_22.ipynb`, `lab4/*.ipynb`, `Python and NumpPy/*.ipynb`)
- 2018 public starter repo (Hager/DiPietro era): https://github.com/deep-learning-jhu/deep-learning-jhu-cs-482-682 · fork https://github.com/ahundt/deep-learning-jhu-cs-482-682
- Final-project repos/reports: https://github.com/acheng41/MLDLFA24-CXR (F2024) · https://github.com/RongkunZ/JHU-DL-Final (F2024) · https://github.com/feiyu12138/SelfModel (F2023, robotics) · https://github.com/jugpanther/Seamless-Texture-GAN-JHU-Deep-Learning-Project- (S2023) · https://github.com/OysterTree/601.682-Final (S2022) · https://github.com/jabarragann/cs682-DeepLearning-Project (S2022) · https://github.com/agking10/DL682 (S2021) · https://github.com/jonathanyxchen/Deep-Learning-Project (F2019) · https://github.com/tpsatish95/pokemon-vqa · https://github.com/kaushiksrini/cs482-finalproject (2018)

**Course pages and syllabi (via search-engine snippets; not directly fetchable from the sandbox):**
- Course home: https://deep.cs.jhu.edu/deeplearning.html · https://deep.cs.jhu.edu/
- Syllabi: https://deep.cs.jhu.edu/files/Syllabus_DL_Fall19_v1.pdf · https://deep.cs.jhu.edu/files/Syllabus_DL_Fall20_no_hyperlinks.pdf · https://deep.cs.jhu.edu/files/Syllabus_DL_Fall20_ABET_website.pdf · https://deep.cs.jhu.edu/files/Syllabus_DL_Spring_2021_ABET_homepage.pdf · https://deep.cs.jhu.edu/files/Syllabus_DL_Fall_2023_Homepage.pdf
- ARCADE Lab course page (Fall 2025): https://www.arcade.cs.jhu.edu/teaching/machine-learning-deep-learning · https://www.arcade.cs.jhu.edu/teaching
- JHU course search: https://courses.jhu.edu/?query=en.601.482
- CS dept course listings: https://www.cs.jhu.edu/fall23/ · https://www.cs.jhu.edu/fall23/table.html · https://www.cs.jhu.edu/spring26/ · https://www.cs.jhu.edu/fall26/ · https://www.cs.jhu.edu/spring18/table.html
- Catalog: https://e-catalogue.jhu.edu/course-descriptions/computer_science_601/ · https://e-catalogue.jhu.edu/search/?P=EN.601.482
- semester.ly / Coursicle: https://jhu.semester.ly/c/EN.601.482 · https://jhu.semester.ly/c/EN.601.682 · https://www.coursicle.com/jhu/courses/EN601/682/
- Course Hero listing (HW titles/dates F2019, F2024; F2023 syllabus copy): https://www.coursehero.com/sitemap/schools/1030-Johns-Hopkins-University/courses/10811204-EN605482/ · https://www.coursehero.com/file/228528104/MLDL-Syllabuspdf/
- Eric Nalisnick (Spring instructor): https://enalisnick.github.io/ · https://enalisnick.github.io/CV.pdf
- Mathias Unberath: https://engineering.jhu.edu/faculty/mathias-unberath/ · https://malonecenter.jhu.edu/people/mathias-unberath/

**News / reviews:**
- "Deep learning course prepares students for success in AI careers" (2020): https://www.cs.jhu.edu/news/deep-learning-course-prepares-students-for-success-in-ai-careers/
- "Popular Deep Learning course goes beyond the fundamentals" (2021): https://www.cs.jhu.edu/news/popular-deep-learning-course-goes-beyond-the-fundamentals/ · https://malonecenter.jhu.edu/popular-deep-learning-course-goes-beyond-the-fundamentals/
- JHU Hub (2019 projects): https://hub.jhu.edu/2019/06/11/ai-deep-learning · (2021 ethics/AI for good): https://hub.jhu.edu/2021/01/22/ethical-artificial-intelligence-deep-fakes/

**Learning resources:** see the URLs in §7.

**Not found / open questions (verify once enrolled via Piazza/Canvas):**
- The current (Fall 2026) full lecture list.
- HW5–HW7 content in recent years (likely RNN/transformer, generative/diffusion, self-supervised).
- Midterm format and allowed materials.
- What the 10% in F2020 grading covered.
- The exact current catalog prerequisite line.
- Whether the Spring (Nalisnick) offering uses different HWs.
- The Fall 2026 meeting time conflict.
