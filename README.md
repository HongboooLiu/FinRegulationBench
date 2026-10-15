# Financial Regulation Violation Benchmark

Five tasks evaluate accounting-rule violation detection using synthetic financial statements and real-world financial disclosures. Each dataset includes financial inputs, rule content, and gold labels.

## Datasets

| Task | Samples | Rule coverage | Input | Target | Format |
|---|---:|---|---|---|---|
| Synthetic Simple | 8,801 | 50 rules; 1 per sample | One statement + specified rule | Violation / no violation | JSONL |
| Synthetic Medium | 4,909 | 49 rules; 8, 13, or 15 candidates per sample | One statement + category-specific rules | One violated rule / no violation | JSONL |
| Synthetic Hard | 4,277 | 49 candidates; 41 rules occur in positive labels | Four statements + candidate rules | Violated rules and affected rows | JSONL |
| Real World Simple | 1,197 | 98 rules; 1 per sample | Financial statements and notes + specified rule | Violation / no violation | JSON array |
| Real World Hard | 888 | 98 candidates per sample | Financial statements and notes + candidate rules | All violated rules / no violation | JSONL |

Real World Simple contains 1,197 sample–rule pairs derived from the same 888 source samples as Real World Hard. The two tasks overlap.

## Fields and format

| Task | Input field | Gold label field |
|---|---|---|
| Synthetic Simple | `prompt` | `gold_label` |
| Synthetic Medium | `input` | `gold_rule_id` |
| Synthetic Hard | `input.statements`, `input.candidate_rules` | `label.violated_rule_ids`; details in `label.statements` |
| Real World Simple | `input.financial_context`, `input.candidate_rules` | `output` |
| Real World Hard | `input.financial_context`, `input.candidate_rules` | `output.violated_rule_ids` |

Every record has a `sample_id`. JSONL stores one record per line; JSON stores an array. Financial inputs and rule content are included. Keep labels and provenance out of model prompts.

Binary labels are `VIOLATION` / `NO_VIOLATION`. Synthetic Medium uses a rule ID or `NO_VIOLATION`. Real World Hard uses a list of rule IDs or `["NON_VIOLATION"]`, exclusively.

## Distributions

| Task | Violation | No violation |
|---|---:|---:|
| Synthetic Simple | 4,353 | 4,448 |
| Synthetic Medium | 4,140 | 769 |
| Synthetic Hard | 4,277 | 0 |
| Real World Simple | 619 | 578 |
| Real World Hard | 459 | 429 |

Synthetic Medium contains 1,410 Balance Sheet, 1,372 Cash Flow, 1,301 Income Statement, and 826 Equity samples. Its 49 candidate rules comprise 15 BS, 13 CF, 13 SI, and 8 SE rules. Synthetic Hard uses the same candidate counts; its observed positive labels cover 15 BS, 12 CF, 7 SI, and 7 SE rules.

Synthetic Simple covers 15 BS, 12 CF, 13 SI, 7 SE, 2 cross-statement, and 1 error-correction rule.

| Violated rules per sample | Synthetic Hard | Real World Hard |
|---|---:|---:|
| 0 | 0 | 429 |
| 1 | 1,180 | 331 |
| 2 | 1,102 | 105 |
| 3 | 1,032 | 17 |
| 4 | 963 | 3 |
| 5 | 0 | 3 |

## Benchmark results

All scores are percentages.

### Synthetic Simple Task Performance

Model: **DeepSeek-V4-Flash**. The dataset contains 8,801 samples, of which 8,613 produced valid predictions and 7,380 were correct. There were 188 parsing or execution failures. Classification metrics below use valid predictions.

| Metric | Score (%) |
|---|---:|
| Accuracy | 85.68 |
| Precision — Violation | 86.13 |
| Recall — Violation | 84.39 |
| F1 — Violation | 85.25 |
| Precision — No Violation | 85.27 |
| Recall — No Violation | 86.92 |
| F1 — No Violation | 86.09 |
| Macro F1 | 85.67 |
| Balanced Accuracy | 85.66 |
| Full-set accuracy (failures counted as incorrect) | 83.85 |

These results reuse historical predictions for the retained 8,801-sample cohort after filtering.

### Synthetic Medium Task Performance

Overall accuracy: **74.81% (DeepSeek)** and **78.59% (ChatGPT)**.

| Table type | # of samples | Correct samples | Accuracy | Macro Precision | Macro Recall | Macro F1 | Weighted F1 |
|---|---:|---:|---:|---:|---:|---:|---:|
| Balance Sheet | 1,410 | 1,192 | 84.56 | 86.49 | 83.99 | 83.95 | 85.08 |
| Cash Flow Statement | 1,372 | 1,102 | 80.30 | 69.70 | 68.91 | 67.92 | 79.45 |
| Income Statement | 1,301 | 924 | 70.99 | 67.31 | 64.99 | 64.35 | 70.66 |
| Statement of Equity | 826 | 640 | 77.44 | 86.24 | 87.90 | 83.95 | 77.17 |
| Overall | 4,909 | 3,858 | 78.59 | 77.15 | 76.60 | 75.28 | 78.51 |

### Synthetic Hard Task Performance

| Metric | Score |
|---|---:|
| Exact Match Ratio | 0.97 |
| All gold rules included in predictions (extra predictions allowed) | 67.07 |
| At least one gold rule matched | 94.84 |
| Micro Precision | 38.52 |
| Micro Recall | 83.65 |
| Micro F1 | 52.75 |
| Macro Precision | 51.53 |
| Macro Recall | 70.96 |
| Macro F1 | 56.90 |
| Weighted F1 | 68.42 |

### Real World Simple Task Performance

**Accuracy: 67.39%.** The benchmark configuration contains 1,554 sample–rule pairs: 805 violation and 749 non-violation pairs.

| Class | Precision | Recall | F1 |
|---|---:|---:|---:|
| Violation | 81.65 | 46.45 | 59.22 |
| No Violation | 61.58 | 89.15 | 72.84 |

### Real World Hard Task Performance

| Metric | Score |
|---|---:|
| Accuracy (Exact Match) | 2.13 |
| Micro Precision | 3.66 |
| Micro Recall | 22.81 |
| Micro F1 | 6.31 |
| Macro Precision | 5.52 |
| Macro Recall | 10.10 |
| Macro F1 | 5.49 |
| Weighted F1 | 15.46 |

### Metric definitions

For each class or rule $c$, let $TP_c$ be correct positive predictions, $FP_c$ incorrect positive predictions, and $FN_c$ missed positives. In single-label classification, each sample has one gold class. In multi-label detection, each candidate rule is treated as a separate binary decision, so a sample can contribute to several rules.

$$
P_c = \frac{TP_c}{TP_c + FP_c}, \qquad
R_c = \frac{TP_c}{TP_c + FN_c}, \qquad
F1_c = \frac{2P_cR_c}{P_c + R_c}.
$$

**Micro averaging** pools counts across classes before computing the scores. Frequent classes contribute more because they have more instances.

$$
P_{\mathrm{micro}} = \frac{\sum_c TP_c}{\sum_c TP_c + \sum_c FP_c}, \qquad
R_{\mathrm{micro}} = \frac{\sum_c TP_c}{\sum_c TP_c + \sum_c FN_c}.
$$

$$
F1_{\mathrm{micro}} = \frac{2P_{\mathrm{micro}}R_{\mathrm{micro}}}{P_{\mathrm{micro}} + R_{\mathrm{micro}}}.
$$

**Macro averaging** computes each class's score first, then averages equally over the $C$ evaluated classes. Rare and frequent classes receive the same weight.

$$
P_{\mathrm{macro}} = \frac{1}{C}\sum_c P_c, \qquad
R_{\mathrm{macro}} = \frac{1}{C}\sum_c R_c, \qquad
F1_{\mathrm{macro}} = \frac{1}{C}\sum_c F1_c.
$$

Macro F1 is the average of class-level F1 scores, rather than the harmonic mean of Macro Precision and Macro Recall. For a per-table macro score, average over the evaluated classes associated with that table.

**Weighted F1** weights each class-level F1 by its gold support $n_c = TP_c + FN_c$:

$$
F1_{\mathrm{weighted}} = \frac{\sum_c n_c F1_c}{\sum_c n_c}.
$$

In multi-label tasks, supports count sample–rule labels and may sum to more than the number of samples. Undefined class-level precision, recall, or F1 is assigned zero in the verified Real World Hard evaluation. That evaluation averages rule metrics over the 98 ASC rules, excluding `NON_VIOLATION`.

**Accuracy / exact match** counts the fraction of samples whose predicted label is correct. For multi-label tasks, the entire predicted rule set must equal the gold set; extra or missing rules make the sample incorrect. A no-violation sample is correct only when the model predicts no violation.
