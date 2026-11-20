# Summary Report — Mall Shopper Segmentation

## Business problem and data
A large Indian mall chain wants to understand visitor spending behaviour so it can redesign store layouts, plan promotions and personalise loyalty rewards. We used the Kaggle *Mall Customer Segmentation* dataset: 200 customers with Gender, Age, Annual Income (k$) and Spending Score (1–100). The data is clean (no missing values, 56% female / 44% male), so the work focused on algorithm tuning and business interpretation.

## Features: 2D vs 5D
Experiment A used **Annual Income + Spending Score**; Experiment B added **Age and encoded Gender** (four features, called "5D" in the brief). All features were standardised because K-Means, Ward and DBSCAN are distance-based. The 2D set gave far better-separated K-Means clusters (Silhouette **0.555** vs **0.314** for the multi-feature set), because Age and Gender form a continuum rather than separated groups. Income and Spending Score were therefore used for the final model, with Age and Gender used for profiling.

## Algorithm performance
| Algorithm | Clusters | Silhouette | Davies-Bouldin | Calinski-Harabasz | Noise |
|---|---|---|---|---|---|
| K-Means (k=5) | 5 | 0.555 | 0.572 | 248.6 | 0% |
| Agglomerative (Ward, 5) | 5 | 0.554 | 0.578 | 244.4 | 0% |
| DBSCAN (eps 0.4, min_samples 10) | 4 | 0.597 | 0.473 | 263.6 | 25.5% |

DBSCAN has the best raw scores, but they are computed after discarding a quarter of the customers — including almost all Budget Shoppers — so it is not a like-for-like comparison. **K-Means is recommended**: it is perfectly stable across seeds (0.5547 ± 0.0000), covers every customer, agrees with Ward on 98% of customers, and supports `predict()` for new shoppers. This matched business intuition: the five groups were already visible in the raw Income-vs-Spending scatter plot.

## Shopper segments
- **Big Spenders (19.5%)** – age ~33, income ~86k$, score ~82; the premium-brand shopper.
- **Careful Spenders (17.5%)** – age ~41, income ~88k$, score only ~17; wealthy but under-engaged.
- **Young Aspirers (11%)** – age ~25, income ~26k$, score ~79; trend-driven, spend beyond their income.
- **Mainstream Shoppers (40.5%)** – age ~43, income ~55k$, score ~50; the largest, volume-driving group.
- **Budget Shoppers (11.5%)** – age ~45, income ~26k$, score ~21; value and deal seekers.

## Next steps
Collect richer behavioural data (purchase categories, visit frequency from footfall sensors, loyalty-app activity) so that features such as age and gender become informative; test semi-supervised methods once some customers have confirmed labels; and expose `classify_shopper()` as a real-time persona-tagging API in the mall app to trigger personalised offers.
