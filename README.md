# Content Decline Prediction for Refresh Prioritization

A machine-learning workflow for prioritizing content pages that may need refresh review.

## What it does

This project compares a transparent rule-based baseline with a Logistic Regression ranking model.

The practical question is:

> Can a machine-learning ranking model identify content pages showing signals of decline more effectively than a simple refresh rule?

The workflow is designed as decision support. It ranks pages for human review rather than automatically deciding which pages should be refreshed.

## Data

The project uses 30,000 anonymized content records with search, engagement, content, and lifecycle features.

The model uses 34 features after excluding identifiers, target/trend fields, and recent/previous 30-day fields to reduce leakage risk.

No client names, private queries, or private URLs are included.

## Method

### Baseline

A page enters the baseline review queue when:

- `days_since_last_update >= 181`
- `impressions_90d >= 500`

The baseline score is `impressions_90d`.

### Machine-Learning Model

The model is Logistic Regression.

It ranks pages using predicted probability of decline.

The target is based on the observed `trend_direction` label:

`trend_direction == "down"`

The preprocessing pipeline includes numeric imputation/scaling and categorical imputation/one-hot encoding.

## Evaluation Results

On the held-out test split used for the model comparison:

| Metric | Baseline | Logistic Regression |
|---|---:|---:|
| Precision@50 | 0.52 | 0.64 |
| Precision@100 | 0.56 | 0.66 |

The model measured higher Precision@50 and Precision@100 than the baseline on this validation setup.

These are observed results on this dataset and test split. They should be treated as directional decision-support evidence, not as a guarantee of the same performance on future clients or datasets.

## Error Profile

The model produced:

- False positives: 1,603
- False negatives: 1,097

Predictions therefore still require human review before taking a refresh action.

## Architecture

```text
Anonymized content data
          |
          v
   Data preprocessing
          |
          v
 Feature selection + leakage checks
          |
          +-------------------+
          |                   |
          v                   v
 Transparent baseline    Logistic Regression
          |                   |
          +---------+---------+
                    |
                    v
          Ranked review queue
                    |
                    v
             Human review
                    |
                    v
          Refresh / no-refresh
