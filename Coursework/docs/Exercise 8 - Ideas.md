# SEMTM0051 — Exercise 8: Investigation Ideas and Recommendation

**Coursework:** *Underfitting, overfitting and held-out data*  
**Purpose of this document:** Help choose and plan an original, manageable investigation. **No experiments have been performed and no assessed answers have been written.**  
**Basis:** The supplied `coursework.pdf`, particularly Exercises 1–8 and the marking guidance (pages 3–9). The designs and rankings below are **my proposals**, not instructions or guaranteed marks from the lecturer.

## 1. What Exercise 8 is actually asking for

Choose **one specific question** about **underfitting, overfitting, or held-out data**. Change `fit.py` or `make_data.py` **as needed**, carry out a controlled experiment, and explain:

1. Your **research question** (one sentence at the top).
2. **What you changed and why**.
3. **How you designed the experiment** and what stayed constant.
4. **What the results show**, supported by clear figures.
5. **What you conclude**, including limitations or a negative result.

**Assessment constraints from the brief:**

- **20 marks** for Exercise 8; the question, experiment design, conclusion, and originality carry approximately equal weight.
- At most **two pages of written discussion plus figures**, with **no more than three report pages total** allocated to Exercise 8 (the full report is capped at nine pages).
- Aim for a simple experiment that runs in **a few minutes on a laptop**, not an expensive parameter sweep.
- Preserve the **test-set rule from Exercise 5**: never use the test set to choose models; evaluate the **final chosen model only once** on the untouched test set.
- After Exercise 1, train using **training data only**. Any deliberately corrupted or modified labels must belong to the training set; do not leak validation/test labels into training.
- Report the actual findings, even if the hypothesis proves false. A clear negative result is acceptable.
- The separate code submission should reproduce the submitted figures. Do not assume the methods listed here can be inserted unchanged into `fit.py` without inspecting that file first.

**Key warning:** The brief explicitly says that repeating Exercises 2–4 with a single number changed will earn few marks. Give your investigation an independently interesting **why/how** question.

## 2. Shortlist: which ideas fit this assignment best?

| Rank | Proposed investigation | Originality potential* | Effort* | Main connection | Overall assessment |
|---|---|---|---|---|---|
| **1** | **Incorrect labels: squared loss vs Huber loss** | High | Low–medium | Loss functions; overfitting; held-out performance | **Best balance of relevance, originality and manageable code** |
| **2** | **Where training points are located: centre-heavy vs spread-out data** | High | Medium | Generalisation; coverage; distribution shift | Particularly interesting if you like data/design questions |
| **3** | **Do early stopping and weight decay complement each other?** | Medium–high | Low–medium | Exercises 3 and 4; regularisation | Easiest technical extension, but frame the question carefully |
| **4** | **Do three unusually bad labels matter more than many small errors?** | Medium–high | Low–medium | Noise type; robustness; overfitting | Good alternative focused on data quality, without changing loss |
| **5** | **Can a small validation set consistently choose model capacity?** | Medium | Medium | Exercises 2 and 5; model selection | Conceptually strong, but closer to a suggested direction in the brief |

*These are qualitative estimates, not marks or verified runtime measurements.*

---

## 3. Best choice — Incorrect training labels and robust loss

### Proposed research question (one sentence)

> **When a small fraction of training labels contain large errors, does Huber loss reduce overfitting to those errors compared with squared loss, while maintaining performance on clean data?**

### Why this is a strong investigation

The coursework trains with **mean squared error (MSE)**, which heavily penalises large residuals. An occasional accidentally incorrect label might therefore attract disproportionate attention during training. **Huber loss** is a standard extension to the loss functions studied in the lectures: it behaves approximately like squared loss for small residuals and like absolute loss for sufficiently large residuals. The precise formula and its implementation are **additional techniques proposed for Exercise 8**, not requirements stated in the coursework.

The interesting question is **not** whether one loss is mathematically smaller than the other. It is whether the way the network is trained changes its **predictions on untouched validation observations**.

### Simple experiment: a 2 × 2 comparison

Keep network size, optimiser, starting seed, epochs, and all other training settings fixed. Change only:

- **Data quality:** original training labels vs a copy with a few intentionally incorrect labels.
- **Training objective:** MSE vs Huber loss.

This gives **four controlled training runs**:

| Run | Training labels | Optimisation loss |
|---|---|---|
| A | Original | MSE |
| B | Original | Huber |
| C | Three deliberately corrupted labels | MSE |
| D | **Exactly the same** three corrupted labels | Huber |

**Suggested setup (adjust only if the provided scripts require it):**

1. Start with the coursework's **fixed split**: 30 training points, 30 validation points and 60 untouched test points.
2. Make a **copy of training labels**. With a fixed random seed, select three of the 30 training indices and add a substantial preset offset to those labels (for example, `+6`, `−6`, `+6`). Record the chosen indices and offsets so the experiment is reproducible. Keep the original labels for the clean condition.
3. Choose one moderate fixed network size, such as `H = 10`, and keep it identical across runs; use Adam and a common random seed and learning rate. Starting with the baseline **20,000 epochs** is consistent with the supplied setup, but simplify if runtime is too high.
4. Add a **training-loss option** to `fit.py`, such as MSE versus PyTorch's Huber loss (`torch.nn.functional.huber_loss`). Do not change the data split or inadvertently train on validation observations.
5. Evaluate **all four models using the same metrics on the same untouched validation set**: validation **MSE** as the principal measure, and optionally validation **MAE** (mean absolute error) to clarify whether a few large mistakes dominate.
6. Make the model decision **using validation only**. After the experiment and decision are final, evaluate **the selected final model once** on the untouched test set, without changing your choice afterwards.

### What to hold constant

- The same `xtr`, `xva` and `xte` and the same *uncorrupted* `yva`, `yte`.
- The same 3 corrupted training observations and error magnitudes for runs C and D.
- The same architecture, random initialisation, optimiser configuration and epoch budget.
- The same evaluation metrics, regardless of which loss a model optimised.

### Suggested evidence for the report

**Figure 1 — What the models learned:** Plot the original training observations, highlight the three altered-label locations (with their corrupted labels), and overlay fitted curves for the two models trained on corrupted data (MSE vs Huber). This directly shows whether either network bends toward the wrong labels.

**Figure 2 — Generalisation comparison:** A compact bar plot of **validation MSE** for runs A–D, optionally with validation MAE as a second panel or a small table. Use identical measurement units for all four models.

**Short explanation:** State whether the training objective changed the amount of apparent overfitting, whether the clean-data case behaves differently, and why the validation data are more informative than training loss alone.

### What would count as an informative result?

- **Huber works better on corrupted training data:** explain how limiting the influence of large residuals changes what the network learns.
- **Little difference:** discuss whether the network capacity, number/magnitude of corrupted labels, or optimisation conditions made the methods behave similarly.
- **Huber performs worse:** discuss the trade-off between robustness and fitting genuine structure; do not tune the experiment repeatedly until a preferred result appears.

**Do not compare raw training Huber loss numerically against raw training MSE.** They are different objectives and the values are not directly comparable. Use shared validation metrics.

### Why this is more original than repeating a listed suggestion

The brief mentions **changing the noise distribution** as a starting point. This proposal goes further by testing a concrete, realistic failure mode—**a few incorrect labels in the training set**—and isolating the interaction between **data quality and the loss function** using a clean/corrupted control. The originality is in the **specific question and controlled comparison**, not in inventing an entirely new algorithm.

**Main risk:** This extends `fit.py` to support a different objective; the exact edit cannot be prescribed until you inspect the supplied implementation. If that feels too complex, consider Idea 3 or Idea 4.

---

## 4. Other worthwhile investigations

### Idea 2 — Does *where* training data are collected matter as much as *how much* data we collect?

**Question:** With the same number of training points, does a network generalise differently when its training inputs are concentrated near the centre of the input range instead of covering the whole range?

**Design:** Keep the training sample size fixed (for example, 30). Generate two training-only input samples: one distributed over all of `[-2, 2]`, the other concentrated near `0`. Generate labels from the same underlying function and noise mechanism. Keep architecture, training duration, validation data and random seed controlled. Use the **same untouched validation set** for comparing the resulting curves and their prediction errors across different `x` regions. Reserve the test set for the final selected model.

**Useful figure:** The two input distributions and the two fitted curves on the same horizontal axis; optionally show validation error for centre versus edge regions.

**Why interesting:** Tests whether sparse coverage makes a network unreliable in specific areas even when overall loss looks reasonable. This is more focused than simply testing points outside `[-2, 2]`.

**Difficulty/risk:** Requires careful changes to the data generator and a fair common validation set. Avoid inadvertently changing noise level or training-set size between conditions.

### Idea 3 — Does weight decay reduce the need for early stopping?

**Question:** Does adding weight decay delay the point at which validation loss starts to rise, and does using both techniques improve validation performance more than either alone?

**Design:** Fix `H = 20`, the training data, initial seed and optimiser. Compare a small set such as `λ = 0` and `λ = 0.01`. For each run, record training and validation loss across epochs. Identify the epoch with the best validation loss, and compare the **best validation epoch** and the **gap between final-epoch and best-epoch validation loss**. Treat these as a *relationship between two regularisation techniques*, not just another capacity/weight-decay sweep.

**Useful figure:** Validation-loss-versus-epoch curves for both decay settings; a small table with best epoch and final/best validation losses.

**Why interesting:** Exercises 3 and 4 study the techniques separately. This examines whether they complement or substitute for one another.

**Difficulty/risk:** Easy to implement with existing functionality, but the idea must go beyond repeating the original questions. Avoid overclaiming synergy based on one noisy run.

### Idea 4 — A few large errors versus many small errors

**Question:** If two training datasets have similar overall noise magnitude, is the network more likely to overfit when errors are concentrated in a few observations rather than spread across many?

**Design:** Make two **training-only** label-noise patterns while keeping the network and evaluation data fixed: (i) small perturbations on many labels and (ii) large perturbations on a few labels. Decide in advance how to make the total perturbation energy comparable (for example, similar `Σ noise_i²`). Fit the same MSE-based network to both and compare fitted shape and shared validation MSE. Evaluate the selected final model only once on test data.

**Useful figure:** Two fitted curves with the altered labels highlighted, plus a validation-MSE comparison.

**Why interesting:** Isolates **noise concentration** rather than merely increasing the noise level. It also tests a reason why squared-error training may produce unexpected behaviour.

**Difficulty/risk:** You must control the *total* noise scale; otherwise the experiment confounds the number of mistakes with overall noise severity.

### Idea 5 — How stable is the choice of `H` when validation data are scarce?

**Question:** Does a larger validation sample make the chosen number of hidden units more stable across random splits?

**Design:** Reserve the original test observations before any experimentation. Repeatedly create **training/validation partitions only from non-test data**, with clearly stated sizes, and compare the `H` selected using validation loss. Report the distribution of selected `H` values across a small, fixed number of random seeds. Choose a final procedure without inspecting test results; only then train/select the final model and evaluate once on the original test set.

**Useful figure:** Dot plot of selected `H` by split or a small frequency chart; optionally variation in validation loss.

**Why interesting:** Directly tests the uncertainty in model selection that Exercise 2(d) raises.

**Difficulty/risk:** Repeated training can exceed the brief's runtime guidance. It is also very close to the lecturer's suggested *split itself* investigation, so the question needs a distinctive hypothesis or metric.

---

## 5. How I would choose

**Choose Idea 1 if:** You want the strongest combination of a concrete real-world problem, connection to earlier loss-function lectures, an interpretable experiment, and a reasonable amount of coding. **This is my recommendation.**

**Choose Idea 3 if:** You want the easiest route through the existing code with minimal modifications. Prioritise a precise interaction question and avoid presenting it as another version of Exercises 3 and 4.

**Choose Idea 2 if:** You want a more distinctive investigation and are comfortable editing how inputs are generated. It has good potential for visual explanation, but requires more care to avoid changing multiple factors at once.

**Choose Idea 4 if:** You like Idea 1's practical focus but would rather keep MSE as the training loss and study the **structure of the noise** instead.

**Choose Idea 5 if:** You are most interested in validation reliability, have enough computing time, and can formulate a clear question that goes beyond the examples supplied in the brief.

## 6. Pre-implementation checklist

Before writing code, make sure you can answer **yes** to the following:

- [ ] My question is stated in **one sentence** and concerns underfitting, overfitting or held-out data.
- [ ] I change **one main factor** or use a clearly controlled small-factor design.
- [ ] I know which conditions must remain fixed, including the random seed when appropriate.
- [ ] The test set stays untouched until the **single final evaluation**.
- [ ] I will use **comparable validation metrics**, not assume different training losses can be directly compared.
- [ ] I can communicate the result in **one or two figures**, a small table if needed, and short explanations.
- [ ] The code can reproduce my figures, and the experiment fits into **a few minutes**.
- [ ] I will report the actual result even if it contradicts my hypothesis.

### Final suggested investigation title

**Robustness to Incorrect Labels: Can Huber Loss Reduce Overfitting Compared with Mean Squared Error?**

**Next step (not yet implemented):** Inspect `fit.py` and `make_data.py`, then write a minimal experiment plan with fixed inputs, seeds, loss definitions, plotting and a strict train/validation/test boundary. Do not change the implementation until the experiment question has been finalised.
