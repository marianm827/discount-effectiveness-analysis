# Grocery Discount & Coupon Effectiveness Analysis

Do grocery discounts and coupons actually lift sales? This project uses Python to analyze retail transaction data and answer that question, while checking the data for problems that could make the results misleading.

**Status:** In progress (started October 2026)

## Data

[Dunnhumby "The Complete Journey"](https://www.dunnhumby.com/source-files/) retail dataset: about 2.6M line items across 2,500 households, including transactions, product details, and coupon and campaign records.

## Key Findings

- **A 15-fold jump in weekly sales was a data artifact, not real growth.** Comparing sales against the number of active households showed the ramp-up came from households entering the dataset over time. Trend analysis was restricted to stable weeks.
- **Deeper discounts went with higher unit sales.** Within the same product, weeks discounted 20+ points deeper than the product's norm sold about 54% more units in Grocery (10,776 products) and about 62% more in Seafood (123 products). The pattern held across different minimum-history cutoffs.
- **Coupon redeemers differ from non-redeemers before any campaign.** Among 1,584 campaign-targeted households, redeemers spent about 76% more per week beforehand. The gap in post-campaign spending change was 6 to 14 percentage points, and it held in timing-matched subsets.

## Limitations

- Coupon redeemers choose to redeem, so the coupon comparison reflects **self-selection**. The results show association, not proof that coupons caused the extra spending.
- Discount results compare each product to its own typical pricing, but other factors such as promotions, displays, or seasonality are not controlled for.

## Methods

- Cleaned and joined the transaction, product, and campaign tables
- Logged every row exclusion in a reproducible cleaning log (`cleaning_log.md`)
- Compared sales against active-household counts to detect the data artifact
- Measured discount depth relative to each product's own norm
- Compared redeemers and non-redeemers before and after campaigns

## Files

| File | Description |
| --- | --- |
| `discount_analysis.ipynb` | Main analysis notebook |
| `cleaning_log.md` | Record of every data cleaning and exclusion decision |
| `sales_by_department.png` | Sales by department chart |

## Tools

Python, pandas, NumPy, matplotlib, Git