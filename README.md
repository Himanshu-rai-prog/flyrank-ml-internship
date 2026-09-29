
# Content Decline Prediction for Refresh Prioritization

A machine-learning workflow for prioritizing content pages that may need refresh review.

## What it does

This project compares a transparent rule-based baseline with a Logistic Regression ranking model.

The practical question is:

> Can a machine-learning ranking model identify content pages showing signals of decline more effectively than a simple refresh rule?

The workflow is designed as decision support. It ranks pages for human review rather than automatically deciding which pages should be refreshed.

## Data

The project uses 30,000 anonymized content records with search, engagement, content, and lifecycle features.

The model uses 34 features after excluding identifiers, the target/trend fields, and recent/previous 30-day fields to reduce leakage risk.

No client names, private queries, or private URLs are included.

## Method

### Baseline

A page enters the baseline review queue when:

- `days_since_last_update >= 181`
- `impressions_90d >= 500`

The baseline score is `impressions_90d`.

### Machine-learning model

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

This means predictions still require human review before taking a refresh action.

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
````

## Usage / Reproducibility

Clone the repository:

```bash
git clone https://github.com/Himanshu-rai-prog/flyrank-ml-internship.git
cd flyrank-ml-internship
```

The main research workflow is documented in:

```text
work/notebooks/capstone.ipynb
```

Related validation work is in:

```text
work/notebooks/w06_validation_audit.ipynb
```

The baseline workflow is in:

```text
work/notebooks/w04_baseline_score.ipynb
```

## Limitations

1. The dataset is anonymized and may not represent every future client or content environment.
2. Model performance depends on the available features and the validation design.
3. False positives and false negatives remain, so predictions should not be treated as automatic decisions.
4. A stale page is not necessarily a page that needs refreshing; some pages may be intentionally evergreen.
5. The measured associations do not establish that a particular feature causes content decline.
6. Validation results are directional and should be re-evaluated on new data before operational use.

## AI Transparency

AI assistance was used to help structure documentation, explain methodology, draft presentation language, and review the project narrative. The dataset, analysis workflow, model results, and validation outputs were checked against the project work before being presented.

## Project Paper

The deployed research paper is available here:

[https://himanshu-rai-prog.github.io/flyrank-ml-internship/](https://himanshu-rai-prog.github.io/flyrank-ml-internship/)

## Data Credit

Built on the FlyRank ML Internship dataset.

[https://flyrank.ai](https://flyrank.ai)

