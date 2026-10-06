# The pull sequence

## 0. Account and inputs

`list_marketplaces` first. Never print `account_id`.

| Input | Required | Notes |
|---|---|---|
| ASIN | yes | the launch |
| Launch date | yes | first day the listing was buyable |
| Week-12 target | yes | units or revenue. No target, no grade. |
| Break-even TACoS | no | default 30% at week 12; see method |
| Target keywords | no | from `trackiq-category-priority-keywords` |
| Previous run's file | no | needed for review velocity |

## 1. Twelve calls, one per week

```
for w in 1..12:
    get_product_performance(account_id, start_date=<week w Sunday>,
                            end_date=<week w Saturday>,
                            group_by='product', limit=200)
```

**There is no daily series.** `granularity="daily"` is accepted and silently
ignored by this tool — no error, no `date` field, rows come back aggregated. So
a weekly curve is genuinely twelve calls over twelve explicit ranges.

Do **not** pull twelve weeks in one call and divide by twelve. That produces a
flat line, which is the one shape a launch never has, and it will be believed.

Filter each response to the launch ASIN. Sum across its SKUs — a launch often
acquires a second SKU (a multipack, an FBM shadow) within the window.

Fields per week: `revenue`, `units`, `sessions`, `orders`, `conversion_rate`.
Derive price as `revenue / units`; there is no price field.

Week 1 is usually partial. Label it partial, show it, and exclude it from any
trend or projection.

## 2. Advertising, per week

```
for w in 1..12:
    get_account_overview(account_id, start_date=<week w>, end_date=<week w>)
```

Gives `total_spend` and `total_revenue` for the account — **not for the ASIN**.

For ASIN-level ad spend, the closest available path is `get_product_ads`
filtered to the ASIN, or campaign totals where the launch has its own campaign.
Where the launch shares campaigns with the rest of the catalogue, **ASIN-level
ad spend cannot be isolated**. Say so and grade TACoS at campaign level,
labelled as such. Do not apportion account spend by revenue share and present it
as the launch's spend.

## 3. Rank — not from the MCP

`get_keyword_rank` looks like the right tool and is not:

- `organic_rank` and `sponsored_rank` were **null on 100 of 100 rows** on the
  account this was built against
- every row carried the same `tracked_date` whatever date range was requested —
  it is a roster, not a series
- rows are per SKU, so `_FBM` shadows duplicate every keyword and ASIN pair

It tells you **which keywords are tracked**, which is genuinely useful as the
target list. It does not tell you where anything ranks.

For rank, sample with Oxylabs:

```
search_keyword(query=<term>, ...)      # 1 credit per page
```

and find the ASIN's position. Costs a credit per keyword per run, so agree the
keyword set first — five to ten terms is a scorecard, fifty is a bill.

If rank is not being sampled, **leave the rank row off the scorecard** and say
why. A blank row invites the reader to assume it was checked.

## 4. Reviews — needs a prior run

Oxylabs `get_product` returns `reviews_count`, `rating` and
`_oxylabs_bonus.rating_stars_distribution`. All three are reliable.

**None of them has history.** There is no review-count time series in either
tool. Weekly review velocity is only computable against a number this skill
recorded before.

- **First run:** report the count and the distribution. State that velocity
  starts next week.
- **Later runs:** read the previous run's saved figures and compute the
  difference.

Ask the user for the previous scorecard file if one exists. Save the current
counts into the output so the next run has them.

`get_reviews` returns only about **eight inline reviews** — the dedicated review
source is tier-gated. Eight is enough for a flavour of early sentiment and
nowhere near enough to call a quality trend.

## 5. New-to-brand, if the client has AMC

```
get_amc_ntb_purchases(account_id, start_date, end_date)
```

NTB share is the cleanest signal that a launch is recruiting rather than
cannibalising. Only available on AMC-enabled accounts — check before promising
it, and leave the row out rather than showing zeros if it is absent.

## 6. Stock — check it every week

```
get_inventory_snapshot(account_id, limit=100, offset=…)
```

A launch that goes out of stock in week five does not recover: rank decays, the
ad spend that built it is wasted, and the twelve-week grade becomes meaningless.
Compute cover per ASIN, never per SKU (see `trackiq-restock-priority`), and put
a stock-out warning at the top of the scorecard rather than in a footnote.
