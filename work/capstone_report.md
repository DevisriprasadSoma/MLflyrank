## 0. Abstract

This study evaluates whether a simple Logistic Regression model can provide useful directional signal for prioritizing content pages that may be at risk of declining compared with a transparent rule-based baseline. Using the public-safe FlyRank ML Internship dataset, the analysis uses 19 observed content, traffic, engagement, freshness, and search-performance features to classify pages labeled as declining. On the stratified 80/20 development holdout, Logistic Regression achieved a ROC-AUC of 0.6777 and Average Precision of 0.6945, compared with 0.5787 and 0.5699 for the rule-based baseline. Under client-grouped validation, performance decreased to 0.5950 ROC-AUC and 0.5939 Average Precision, showing that the measured result is sensitive to validation design. The resulting ranked scores are intended as directional decision-support for prioritizing human review of pages for possible refresh or improvement, not as automatic content decisions or predictions of Google's ranking algorithm.

## 1. Problem Framing

### Decision supported

The analysis supports a content-team decision: **which pages should be reviewed first for possible refresh or improvement?**

The goal is to prioritize limited human review effort using a measurable ranking signal rather than manually reviewing every page with the same priority.

### Unit of analysis

The unit of analysis is an individual content page represented by one row in the FlyRank ML Internship dataset.

### Model output

The model produces a decline-risk score for each page. Pages can then be ranked from higher to lower priority for human review.

### Human action

A content team can use the ranked list to inspect higher-priority pages for factors such as:

- Content freshness
- Search position
- User engagement
- Content depth
- Overall content quality and search intent

The final decision remains with a human reviewer.

### Cost of a wrong call

A false positive may cause a team to spend review time on a page that does not require a refresh. A false negative may cause a potentially declining page to receive less attention than it should.

Because these errors have different costs, the model is used as a prioritization signal rather than an automatic content decision.

### Why machine learning helps

A rule-based approach can provide a transparent starting point, but a machine-learning model can combine multiple observed content, traffic, engagement, freshness, and search-performance signals into a single ranking score.

This analysis therefore compares Logistic Regression with a rule-based baseline to determine whether the model provides additional directional ranking signal for prioritizing human review.

## 2. Data Safety

### Data used

The analysis uses the public-safe FlyRank ML Internship starter dataset containing **30,000 rows and 44 columns**.

The model uses 19 observed features covering:

- Traffic and visibility
- User engagement
- Content freshness
- Content length
- Search performance
- AI traffic and scroll activity

### Target definition

The target is derived from the provided `trend_direction` field. Pages where `trend_direction = down` are assigned to the decline class.

The target-defining fields are not used as model inputs.

### Leakage controls

The following fields were excluded from the model features because they are directly related to the outcome definition:

- `trend_direction`
- `trend_pct`
- `target`

A feature-level leakage audit confirmed that these fields were not included among the 19 model features.

The leakage audit reduces the risk of directly exposing the target to the model, but it does not prove that every possible source of bias or leakage is absent.

### Client identifiers

The `client_id` field is used only for the client-grouped validation design. It is not used as a model feature.

Client-grouped validation ensures that clients do not overlap between the training and test sets, providing a stricter check of generalization to unseen clients.

### Public-safe handling

No client names, private search queries, credentials, or other client-identifying details are included in the analysis or report.

The findings are presented as measured and directional evidence from the internship dataset and are not used to make claims about Google's ranking algorithm.

## 3. Baseline

A transparent rule-based baseline was used as the reference method for evaluating whether Logistic Regression added useful ranking signal.

The baseline combines four signals:

1. **Visibility score** — percentile rank of `log1p(impressions_90d)`.
2. **Freshness risk score** — percentile rank of `days_since_last_update`.
3. **Search-position opportunity** — based on normalized `avg_position`, weighted by visibility.
4. **Content-depth gap** — based on the relative position of `word_count`, weighted by visibility.

The final baseline score is calculated as:

- 40% visibility
- 30% freshness
- 25% search-position opportunity
- 5% content-depth gap

The resulting score is clipped to the range 0 to 1 and used to rank pages for review.

### Baseline comparison

The baseline and Logistic Regression model were evaluated on the same stratified 80/20 development test set using the same metrics.

| Method | ROC-AUC | Average Precision |
|---|---:|---:|
| Rule-based baseline | 0.5787 | 0.5699 |
| Logistic Regression | 0.6777 | 0.6945 |

The Logistic Regression model improved over the baseline by **+0.0990 ROC-AUC** and **+0.1246 Average Precision** on this development holdout.

This comparison indicates measured additional ranking signal from the model on the development dataset, while not establishing production or future performance.

## 4. Model / Analysis

### Model

The analysis uses **Logistic Regression** as the main classification model because it provides a simple and interpretable approach for combining multiple observed signals into a decline-risk score.

Numeric features were standardized before fitting the Logistic Regression model.

### Input features

The model uses 19 features:

- `impressions_90d`
- `clicks_90d`
- `pageviews_90d`
- `sessions_90d`
- `users_90d`
- `engaged_sessions_90d`
- `ai_sessions_90d`
- `scroll_events_90d`
- `days_with_impressions`
- `days_with_sessions`
- `content_age_days`
- `days_since_last_update`
- `word_count`
- `char_count`
- `ctr`
- `avg_position`
- `engagement_rate`
- `scroll_rate`
- `ai_traffic_pct`

These features represent observed content, traffic, engagement, freshness, and search-performance signals.

### Target

The binary target represents whether a page is classified as declining.

Pages where:

```text
trend_direction = down

## 5. Evaluation

### Evaluation metrics

The analysis uses **ROC-AUC** and **Average Precision** because the objective is to rank pages for review rather than make an automatic production decision.

On the stratified 80/20 development holdout, Logistic Regression achieved:

- ROC-AUC: **0.6777**
- Average Precision: **0.6945**

The decline-class base rate in the dataset is approximately **54.21%**.

### Model vs baseline

The Logistic Regression model was compared with the rule-based baseline on the same test set.

| Method | ROC-AUC | Average Precision |
|---|---:|---:|
| Rule-based baseline | 0.5787 | 0.5699 |
| Logistic Regression | 0.6777 | 0.6945 |

The model therefore measured an improvement of:

- **+0.0990 ROC-AUC**
- **+0.1246 Average Precision**

### Client-grouped validation

A stricter validation design was used to test generalization to unseen clients.

| Validation design | ROC-AUC | Average Precision |
|---|---:|---:|
| Stratified holdout | 0.6777 | 0.6945 |
| Client-grouped split | 0.5950 | 0.5939 |

Performance decreased by **0.0826 ROC-AUC** and **0.1007 Average Precision** under client-grouped validation.

This indicates that the measured performance is sensitive to validation design and that patterns specific to individual clients may contribute to the stronger result observed on the stratified holdout.

### Error analysis

On the stratified test set, the model produced:

- **1,226 false positives**
- **935 false negatives**

False positives represent pages prioritized as declining that were not in the decline class, while false negatives represent declining pages that were not prioritized by the model.

The error analysis reinforces that the model should be used to prioritize human review rather than to make automatic content decisions.

### Interpretation of evaluation

The results provide measured evidence that Logistic Regression adds ranking signal over the rule-based baseline on the development holdout.

However, the lower client-grouped performance shows that this result should be treated as **directional decision-support**, not as guaranteed future performance or production-level accuracy.
