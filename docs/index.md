# Refresh / Content Opportunity Scoring

**Author:** G P Revanth Raj

**Lane:** Refresh / Content Opportunity Scoring

**Repository:** https://github.com/ray007herowars-cmyk/Flyrank-ML

## Abstract

This project investigates how observed search and engagement signals can support prioritization of content items for editorial review. The analysis uses March 2026 content-performance data and evaluates future impression decline using a June 2026 outcome window. A transparent baseline ranking was compared with Logistic Regression and Random Forest models using Precision@K. The baseline achieved the strongest P@20 result in the evaluated test set, while the more complex ML models did not improve ranking precision. The resulting ranked list is intended as decision support for editors when prioritizing content for review.

## Introduction / Problem statement

The goal is to support an editor deciding which content items should be reviewed first.

The unit of analysis is one content item for one client. The output is a ranked priority score together with a reason code and recommended action.

A high-priority item can be reviewed by an editor for a possible refresh, investigation, or decision to leave the content unchanged.

A false positive can waste editorial time, while a false negative can cause a potentially useful review opportunity to be missed.

Data helps because several search and engagement signals can interact when prioritizing content items.

## Data

The analysis uses the FlyRank internship content-performance dataset.

March 2026 data was aggregated to the client-content level to create the feature frame.

The final features used were:

- `march_impressions`
- `march_clicks`
- `avg_search_position`
- `march_sessions`
- `march_engaged_sessions`

Pseudonymous client and content identifiers were used only for grouping and joining and were not used as model features.

Label-derived fields such as `trend_direction` and `trend_pct` were excluded.

Future outcome fields were excluded from the model feature matrix.

The final feature frame contained 331,437 rows before matching with the future outcome data.

## Methodology

A transparent baseline ranking was created first using search volume, search position, and CTR signals.

Two ML approaches were then evaluated:

- Logistic Regression
- Random Forest

The models used the same feature set and the same grouped train/test split.

The split contained 151,492 training rows and 25,232 test rows, with grouping by client.

The future outcome was defined operationally as:

`future_decline = June impressions at least 20% lower than March impressions, among pages with March impressions > 0.`

The target is treated as an operational proxy rather than proof that a page required a refresh.

## Results

Precision@K was used to compare the ranking methods.

| Method | P@10 | P@20 | P@50 | P@100 |
|---|---:|---:|---:|---:|
| Baseline | 1.00 | 1.00 | 0.94 | 0.95 |
| Logistic Regression | 0.80 | 0.85 | 0.88 | 0.90 |
| Random Forest | 0.90 | 0.80 | 0.82 | 0.82 |

The test-set base rate was 83.85%.

The baseline achieved the highest P@20 at 1.0000 and was therefore selected over the two ML models.

The Random Forest feature-importance analysis showed that `avg_search_position` (0.531589) and `march_impressions` (0.337476) were the strongest features, followed by `march_sessions` (0.061709), `march_clicks` (0.059219), and `march_engaged_sessions` (0.010007).

These feature-importance results are directional and do not establish causality.

## Limitations & honest framing

This analysis uses a limited time period rather than a long-term evaluation.

Search and analytics signal availability varies across clients, which can affect the usefulness of engagement-related features.

The future outcome is an operational proxy based on the observed change in impressions between March and June.

A future decline does not prove that a page needed a refresh, and the analysis does not establish that refreshing a page would cause performance improvement.

The test-set base rate was 83.85%, so Precision@K should be interpreted alongside the base rate.

The baseline also outperformed both ML models at P@20, so the results do not support replacing the transparent baseline with a more complex model.

The recommendations are decision-support only and should be reviewed by a human editor.

## Ranked recommendations

The selected baseline produces a ranked list of content items for editorial review.

The highest-ranked items receive the `SEARCH_OPPORTUNITY` reason code and `REVIEW` action.

The top-ranked recommendations are intended to help an editor prioritize which content items to inspect first.

The ranking does not determine that a refresh is required.

## Reproducibility

The analysis uses a fixed random seed of 42.

The March feature frame and future outcome frame are aggregated at the client-content level and joined using pseudonymous identifiers.

The model feature list is:

`march_impressions`, `march_clicks`, `avg_search_position`, `march_sessions`, `march_engaged_sessions`.

Future outcome columns were excluded from the feature matrix, and the leakage check passed.

The analysis notebook and generated artifacts are committed to the repository under `work/`.

## Acknowledgments & data credit

Built on the FlyRank ML Internship dataset.

[FlyRank](https://flyrank.ai)
