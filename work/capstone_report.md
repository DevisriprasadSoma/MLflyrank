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
