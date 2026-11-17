## Cleaning Log

### 1. Excluded KIOSK-GAS department
- Rows removed: 22,059 transaction rows (~0.8% of rows)
- Sales removed: $544,222 (~6.8% of total sales)
- Reason: Gas is not a grocery product and doesn't respond to
  promotions the way groceries do. Including it would blur the
  discount and promotion results.
- Note: It's only 16 products, but it's a large share of sales because
  each gas purchase is a big dollar amount. That's why it was excluded
  despite the small row count.

### 2. Excluded MISC SALES TRAN department
- Rows removed: 6,050 transaction rows
- Content: all rows have COMMODITY_DESC = "COUPON/MISC ITEMS"
- Sales removed: $119,960 (~1.5% of total sales)
- Reason: These are coupon and miscellaneous bookkeeping entries, not
  products customers chose to buy, so they don't belong in a product
  or discount analysis.
- Caveat: Coupons are part of this project. They'll be analyzed
  separately using coupon_redempt and the COUPON_DISC columns.

### Summary after steps 1-2
- Rows before: 2,595,732 | Rows after: 2,567,623
- Total removed: 28,109 rows (~1.1% of the data)
- Merge check: transaction and merged row counts matched (no duplicates)

### 3. Kept: zero-sales rows with quantity > 0 (fully discounted items)
- Rows: 4,444 (~0.17% of 2,567,623 rows)
- Pattern: QUANTITY ≥ 1 but SALES_VALUE = 0. About [84]% have a
  negative RETAIL_DISC equal to the item's price, meaning the store
  discounted the item 100% (e.g., a free item in a promotion).
- Decision: Kept. These are real purchase events and 100% discounts,
  which are directly relevant to the question of whether discounts
  generate sales.
- Caveat: SALES_VALUE = 0 breaks calculations that divide by the
  amount paid. Pre-discount price will be reconstructed as
  (SALES_VALUE - RETAIL_DISC - COUPON_MATCH_DISC) / QUANTITY instead.
- Open item: The remaining [16]% have RETAIL_DISC = 0. Investigating
  [what they are, based on department and coupon checks]. Decision: [pending].

  ### 4. Excluded COUP/STR & MFG department
- Rows removed: 817 (~$1,028 in sales)
- Content: all rows have COMMODITY_DESC = "COUPONS/STORE & MFG"
- Reason: Coupon bookkeeping entries, not products customers bought.
  Same reasoning as MISC SALES TRAN. Coupon information is kept in
  the discount columns and in coupon_redempt.
- Caveat: Interpretation inferred from the data, not confirmed in
  the dataset documentation.

  ### 5. Dropped: unexplained zero-price items (no discount, no coupon)
- Rows removed: 251 (~0.01% of rows)
- Pattern: QUANTITY ≥ 1 and SALES_VALUE = 0, but RETAIL_DISC,
  COUPON_DISC and COUPON_MATCH_DISC are all 0.
- Reason: Unlike the fully discounted items (kept), nothing in the
  data explains why these were free. They can't be treated as
  discount events and their price can't be reconstructed.
- Caveat: Could be giveaways or recording errors; the cause is
  unknown. Share is negligible, so impact on results should be minimal.

  ### 6. Dropped: zero-quantity rows
- Rows removed: 14,406 (~0.56% of rows)
- Pattern: QUANTITY = 0. Nearly all also have SALES_VALUE = 0; a small
  number have tiny amounts (mostly floating-point noise or cents).
  One produce row had $5.82.
- Reason: Nothing was sold, so these rows carry no information about
  purchases or discounts.
- Note: The fully discounted items (quantity ≥ 1, SALES_VALUE = 0)
  were kept, since quantity was used as the filter.

  SUMMARY : ROWS WENT  FROM 2,595,732 to 2,552,149. 1.7% of data removed

  ### 7. Kept: high-quantity rows (QUANTITY > 50)
- Rows: 16
- Pattern: Bulk purchases of candy, corn, drink mixes and soup.
  Unit prices range from $0.10 to about $2.00, which is plausible
  for each product type.
- Decision: keep since there is no weirdness
- Reason: No sign of data errors, some low unit price can be due to promotions, which are relevant
- Caveat: The two lowest unit prices ($0.10) could be deep
  promotions or pricing errors. They look plausible, so were kept.

  ### 8. Blank DEPARTMENT values
- Result: 0 rows with a blank DEPARTMENT remain in the cleaned data.
- Explanation: The 15 products with blank departments only appeared
  in rows removed under step 6 (zero quantity).
- No further action needed.

### Summary
- Rows before cleaning: 2,595,732
- Rows after cleaning: 2,552,149
- Total removed: 43,583 (~1.7%)
- Kept on purpose: fully discounted items (QUANTITY ≥ 1, SALES_VALUE = 0)
  and 16 high-quantity rows

  ### 9. [Kept / Excluded]: MISC. TRANS. department
- Rows: 2,292 (~$10.7K, ~0.14% of sales)
- Pattern: All rows carry the placeholder description "NO COMMODITY
  DESCRIPTION", but they cover 467 different products (strawberries,
  beer, soft drinks, etc.) with typical grocery prices (median $1.57)
  and some retail discounts. A few high-value rows exist, but rows
  over $100 also appear in other departments.
- Decision: Kept. It's a small share of sales, and the rows look like
  real purchases.
- Reason: The rows look like real purchases (typical prices, retail
  discounts), so they're relevant to the discount analysis.
- Caveat: Because the category is missing, these rows shouldn't be used
  for product or category breakdowns. High-value rows are outliers, so
  use medians instead of means for price or basket-value summaries.