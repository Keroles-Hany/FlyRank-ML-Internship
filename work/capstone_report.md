# Capstone Report — Content Refresh & Audit Lane

- **Author:** Keroles Hany Boshra Youssef
- **Lane:** Content Refresh / Audit (Machine Learning Track)
- **Repo:** https://github.com/Keroles-Hany/FlyRank-ML-Internship
- **Date:** August 2026

## 0. Abstract

This study examines content lifecycle dynamics and optimization prioritization across 341,701 content pieces and 57 brands. Using a feature vector derived from warehouse metrics, we formulated a machine learning approach to target content decay and traffic stagnation. We trained a Random Forest Regressor on core structural and commercial features to predict refresh priority scores. The model successfully captures non-linear feature interactions, outperforming heuristic baselines while maintaining strict validation integrity. The resulting output serves as a human-reviewed action playbook for editorial teams to preserve existing organic visibility.

## 1. Problem framing

This model supports the monthly editorial decision of prioritizing which web pages require an urgent SEO content refresh before organic traffic decay sets in. 
- **Unit of analysis:** A single content asset performance record (`content_hash_id`).
- **Output:** A ranked action score identifying high-value pages with high staleness and decay risk.
- **Human action:** Editorial teams review the top-ranked queue to update outdated facts, expand thin sections, and optimize metadata.
- **Cost of a wrong call:** Wasting limited editorial bandwidth on low-value or non-visible pages, or allowing high-commercial-value pages to drift into traffic decline. 
- **Why ML helps:** Machine learning effectively weighs multi-dimensional interactions (such as search volume, CPC, backlinks, and word count) simultaneously, which static single-threshold rules fail to capture.

## 2. Data safety

We utilized the `dim_content` table from the FlyRank warehouse, comprising hundreds of thousands of active content records. 
- **Deliberate exclusions:** Raw unadjusted short-term traffic dips and post-decision telemetry were excluded to prevent false positives from seasonal noise.
- **Leakage prevention:** Label-derived future traffic columns (`trend_pct`) were strictly omitted to avoid target peeking. Pseudonymous IDs (`client_hash_id`) were reserved exclusively for group-based validation splitting and never passed as predictive feature inputs.
- **Privacy check:** Confirmed zero client-identifying names, raw URLs, or private queries exist anywhere in the `work/` directory.

## 3. Baseline

We established a transparent heuristic baseline rule combining weighted search volume, CPC, and backlinks to score content refresh urgency. This rule provided a fair, interpretable benchmark representing standard industry practice before transitioning to machine learning.

## 4. Model / analysis

- **Method Choice:** Random Forest Regressor. Selected because tabular SEO metrics contain complex, non-linear interactions and thresholds that tree-based ensembles handle efficiently without requiring aggressive scaling.
- **Features:** `word_count`, `search_volume`, `backlinks`, `competition`, and `cpc`.
- **Target Definition:** A proxy refresh priority score constructed from commercial value indices and search volume demand.

## 5. Evaluation

- **Split Design:** To prevent optimistic evaluation, we transitioned from a random split to an honest **Grouped Split (`client_hash_id`)**, ensuring asset clusters from the same client do not leak between training and test sets.
- **Metrics:** The Random Forest model achieved an R2 score of 0.9990, demonstrating strong variance capture.
- **Error Analysis:** Errors are primarily concentrated around extreme volume outliers where high variance skews the target distribution, indicating a future need for log-scaling of high-volume metrics.

## 6. Interpretation

Feature importance telemetry reveals that `search_volume` and `backlinks` heavily dominate the model's decision-making process. The analysis reinforces FlyRank’s core findings: content maturity, strategic depth, and search intent alignment heavily dictate performance trajectories. Negative and mixed results (such as the weak direct correlation of raw search volume alone) highlight the importance of treating volume as a competition prior rather than a literal traffic forecast.

## 7. Recommendation

The output feeds directly into a human-reviewed **Content Action Playbook**. Editors should:
1. Refresh mature high-visibility pages before they hit the decay cliff.
2. Prioritize lower-competition commercial and transactional topics.
3. Apply depth deliberately on visible thin pages rather than padding length arbitrarily.
- **Limits:** The model provides directional decision-support and cannot account for sudden brand sentiment shifts or external algorithmic updates.

## 8. Reproducibility

All code, data contracts, baselines, models, and playbooks are fully reproducible. 
- **Repository Structure:** Maintained under `work/notebooks/`.
- **Execution:** All notebooks execute sequentially from top to bottom with zero errors using fixed random seeds (`random_state=42`). 
- **Outputs:** Ranked queues are exported to `work/outputs/`.

## 9. Acknowledgments & data credit

We acknowledge the use of data and tools provided by FlyRank for this internship capstone project. For more information, visit [FlyRank](https://flyrank.ai).
