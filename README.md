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

All scores are percentages. Blank cells indicate results not reported or not clearly attributable to a model in the slides. Values follow the Baseline Comparison slides.

### Synthetic Simple Task Performance

| Metric | DeepSeek V4 Flash | ChatGPT 5.5 | Qwen 8B |
|---|---:|---:|---:|
| Accuracy | 82.72 | 85.68 | 62.35 |
| Precision — Violation | 81.36 | 85.70 | 63.44 |
| Recall — Violation | 86.33 | 85.66 | 61.24 |
| F1 — Violation | 84.27 | 85.67 | 62.33 |
| Precision — No Violation | 85.27 | 87.33 | 67.33 |
| Recall — No Violation | 86.92 | 88.32 | 65.19 |
| F1 — No Violation | 86.09 | 87.19 | 65.35 |
| Macro F1 | 85.67 | 86.52 |63.94  |
| Balanced Accuracy | 85.66 | 87.07 | 63.66 |

### Synthetic Medium Task Performance

| Metric | DeepSeek V4 Flash | ChatGPT 5.5 | Qwen 8B |
|---|---:|---:|---:|
| Accuracy | 74.42 | 78.59 | 62.32 |
| Macro Precision | 75.31 | 77.15 | 63.15 |
| Macro Recall | 74.19 | 76.60 | 65.63 |
| Macro F1 | 73.36 | 75.28 | 61.44 |
| Weighted F1 | 75.35 | 78.51 | 65.44 |

### Synthetic Hard Task Performance

| Metric | DeepSeek V4 Flash | ChatGPT 5.5 | Qwen 8B |
|---|---:|---:|---:|
| Exact Match Ratio | 0.78 | 0.97 | 0.31 |
| All gold rules included in predictions (extra predictions allowed) | 67.07 |  |  |
| At least one gold rule matched | 94.84 | 95.25 | 84.71 |
| Micro Precision | 35.91 | 38.52 | 28.32 |
| Micro Recall | 81.77 | 83.65 | 68.42 |
| Micro F1 | 50.33 | 52.75 | 40.12 |
| Macro Precision | 48.68 | 51.53 | 39.95 |
| Macro Recall | 67.28 | 70.96 | 57.79 |
| Macro F1 | 52.71 | 56.90 | 41.33 |
| Weighted F1 | 63.11 | 68.42 | 49.87 |

### Real World Simple Task Performance

| Metric | DeepSeek V4 Flash | ChatGPT 5.5 | Qwen 8B |
|---|---:|---:|---:|
| Accuracy | 67.39 | 65.32 | 47.51 |
| Precision — Violation | 75.42 | 73.35 | 52.19 |
| Recall — Violation | 61.38 | 59.71 | 45.21 |
| F1 — Violation | 64.57 | 65.28 | 50.17 |
| Precision — No Violation | 61.58 | 58.72 | 53.13 |
| Recall — No Violation | 89.15 | 88.32 | 59.31 |
| F1 — No Violation | 72.84 | 71.89 | 54.81 |

### Real World Hard Task Performance

| Metric | DeepSeek V4 Flash | ChatGPT 5.5 | Qwen 8B |
|---|---:|---:|---:|
| Accuracy (Exact Match) | 1.88 | 2.13 | 0.32 |
| Micro Precision | 3.35 | 3.66 | 2.11 |
| Micro Recall | 21.45 | 22.81 | 17.73 |
| Micro F1 | 5.79 | 6.31 | 3.22 |
| Macro Precision | 4.98 | 5.52 | 3.79 |
| Macro Recall | 9.16 | 10.10 | 5.32 |
| Macro F1 | 4.99 | 5.49 | 2.64 |
| Weighted F1 | 13.71 | 15.46 | 10.37 |

Real World Simple results refer to the historical benchmark cohort of 1,554 sample–rule pairs; the current released dataset contains 1,197 pairs.

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
