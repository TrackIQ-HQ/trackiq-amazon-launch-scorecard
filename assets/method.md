# Method

## The target curve

A launch does not grow linearly and should not be graded as if it did. Weight
the week-12 target onto an S-curve:

```
share[w] = w^1.8 / 12^1.8                    # cumulative share of the target
target[w] = week12_target x (share[w] - share[w-1])   # that week's number
```

The exponent 1.8 gives a slow start, a steep middle and a flattening end —
roughly the shape of a launch that works. It is a **default, not a law.** If the
client has their own curve, use theirs and say so.

State the curve on the report. A grade against a hidden benchmark is not a
grade.

## The weekly grade

```
ratio = actual[w] / target[w]
```

| Grade | Test |
|---|---|
| **Go** | `ratio >= 0.9` and no red flag below |
| **Hold** | `0.7 <= ratio < 0.9`, or on target with one red flag |
| **Fix** | `ratio < 0.7`, or two or more red flags |

Red flags, any week:

- out of stock, or under 14 days of cover
- conversion rate down for two consecutive weeks
- sessions flat or falling while ad spend rises
- TACoS above the glide path for two consecutive weeks
- rating below 4.0 once there are 15 or more reviews

**Week 1 is never graded.** It is partial and the ad account is still learning.

## The TACoS glide path

A launch buys its first sales. The question is whether it stops having to.

```
tacos[w] = total_ad_spend[w] / total_revenue[w]
```

Total revenue, not attributed. Using ACOS hides the entire point — a launch can
hold a beautiful ACOS while producing no organic sales at all.

Default glide path, linear from week 2 to week 12:

```
path[w] = start - (start - target) x (w - 2) / 10
          start  = 60%      # week 2 — buying the launch
          target = 30%      # week 12 — break-even-ish, client-specific
```

Both ends are defaults. The week-12 figure should really be the client's
break-even TACoS, which is roughly gross margin minus the return they want.
Ask; if they do not know, use 30% and label it.

**Above the path for two consecutive weeks is a red flag.** One week is noise.

Where the launch shares campaigns with the rest of the catalogue, ASIN-level ad
spend cannot be isolated (see `assets/pulls.md`). Grade TACoS at campaign level
and label the row so nobody quotes it as the ASIN's figure.

## Sessions and conversion, read together

```
sessions[w]      growth week on week
cvr[w] = orders[w] / sessions[w]
```

- **Sessions up, conversion flat** — the launch is working. Keep spending.
- **Sessions up, conversion falling** — buying worse traffic. Fix targeting
  before adding budget.
- **Sessions flat, conversion up** — the page improved but nothing is finding
  it. A visibility problem, not a listing one.
- **Both falling** — stop and diagnose before spending another dollar.

This two-by-two is the most useful thing on the scorecard. Put it in words on
the page, not just as two lines on a chart.

## Review accumulation

```
reviews_per_100_units = (reviews_now - reviews_prev) / units_this_week x 100
```

Roughly 1 to 2 reviews per 100 units is normal without a review programme. Well
under that on a product selling steadily is worth flagging — usually it means
no follow-up is running.

**This requires a previous run.** There is no review history in either tool.
First run: report the count, say velocity starts next week, and save the count
into the output for next time.

## Rank

Where rank is sampled (Oxylabs — see `assets/pulls.md`), track position on the
agreed keyword set week by week and report movement, not absolute position. A
launch going from page 6 to page 2 is working even though page 2 is not where it
needs to end up.

Where rank is **not** sampled, leave the row off entirely. A blank row reads as
"checked and nothing found".

## The verdict and the lever

One verdict for the launch as a whole, taken from the most recent graded week,
and **one lever**.

| Verdict | The lever names |
|---|---|
| **Go** | what to increase, and by how much |
| **Hold** | what to watch next week, and the number that would change the verdict |
| **Fix** | the single action, and the metric it should move |

Not a list. A launch team that gets six recommendations does none of them.

## What this skill does not do

- **No rank from the MCP.** `get_keyword_rank` has null ranks and no history.
- **No review history.** Velocity needs a prior run of this skill.
- **No forecast past week 12.** After twelve weeks it is a product, not a
  launch, and `trackiq-sales-movers` is the right tool.
- **No ASIN-level ad spend** where the launch shares campaigns.
