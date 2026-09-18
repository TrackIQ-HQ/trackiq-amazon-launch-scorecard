---
name: trackiq-amazon-launch-scorecard
description: Grades a newly launched Amazon ASIN week by week through its first twelve weeks — units and revenue against a target curve, session growth, conversion rate, review accumulation, organic rank on its target keywords, and TACoS against a declining break-even glide path — ending in a go, hold or fix verdict with one lever. Use when the user asks about a product launch, new ASIN performance, launch tracking, is the launch working, launch scorecard, week one to twelve, or how is the new product doing.
---

# Launch Scorecard

A launch either builds organic demand or it buys sales forever. **Which one is
happening, by week twelve, while there is still time to act.**

Twelve weeks graded against a target curve, each week a go, hold or fix.

Output is a branded HTML scorecard.

## Requires

- The TrackIQ MCP, for `list_marketplaces` and `get_product_performance`.
- **The launch date** and **the target** — units or revenue by week twelve. Ask
  for both. Neither is in any tool, and without a target there is no scorecard,
  only a chart.
- **The Oxylabs scraper** for organic rank, if rank is wanted. The MCP's
  `get_keyword_rank` returns the tracked keyword roster with **null ranks**; see
  non-negotiable 3.
- **A previous run's file**, for review pace. See non-negotiable 4.
- **Without the MCP:** works from weekly Business Report exports for the ASIN.

## First run

Fill in a copy of `assets/account.example.md` saved as account.md beside the
skill. Every TrackIQ skill reads the same file, so an account already set up
for another TrackIQ report needs nothing added here.

If the runtime has no filesystem, print the same block and ask the user to
paste it into their project instructions once.

## Read first

- `assets/pulls.md` — the calls, one per week, and the three things that are not
  measurable from the MCP
- `assets/method.md` — the target curve, the glide path, and the weekly grade
- `assets/checks.md` — what to verify before anything is sent

Copy `assets/report-template.html` and replace every `{{TOKEN}}`.

## Non-negotiables

1. **One call per week.** `get_product_performance` silently ignores
   `granularity="daily"` — there is no series to slice. Twelve weeks is twelve
   calls over twelve explicit date ranges. Do not fake a weekly curve by
   dividing a twelve-week aggregate.
2. **Weeks run Sunday to Saturday** and are numbered from the launch date. A
   partial first week is labelled partial and excluded from the trend.
3. **Rank is not available from the MCP.** `get_keyword_rank` returns the
   tracked keyword roster with `organic_rank` and `sponsored_rank` **null on
   every row**, and a single `tracked_date` regardless of the range asked for.
   Sample rank with the Oxylabs `search_keyword` tool, or leave the rank row out
   and say why. Never present the roster as ranks.
4. **Review pace needs a prior run.** `reviews_count` is a point-in-time number
   with no history anywhere in either tool. Week-on-week review velocity is
   computable only by comparing against a file this skill wrote before. First
   run: report the count, state that velocity begins next week.
5. **The target comes from the client.** A launch graded against a curve you
   invented is a chart with opinions. If they have no target, say the scorecard
   is descriptive and grade nothing.
6. **TACoS, not ACOS.** A launch is judged on ad spend against *total* revenue,
   because the point is organic lift. ACOS hides exactly the thing being
   measured.
7. **The glide path declines.** Break-even TACoS at week 2 is not break-even at
   week 12. State the path used.
8. **One lever per week, not a list.** The verdict is go, hold or fix, and fix
   names a single action.
9. **Twelve weeks is the scope.** After that it is not a launch, it is a
   product, and `trackiq-sales-movers` is the right skill.
10. **Never print `account_id`.**

## What it pairs with

`trackiq-category-priority-keywords` supplies the terms a launch should be
ranking for — grade against those, not against whatever it happens to rank on.
`trackiq-restock-priority` matters more here than anywhere: a launch that runs
out of stock in week five does not recover, and the scorecard should say so
before it happens.

## Delivery

The output is produced in the chat first. Delivery is the last step and the
method comes from the Delivery block in account.md — never ask per run.

| Method | What to do | Needs |
|---|---|---|
| `in-chat` | Return the report. The default, and the fallback for every other method. | nothing |
| `file` | Write it beside the skill, dated. | a filesystem |
| `slack` | Post the headline findings as text, then upload the file. | a connected Slack tool |
| `n8n` | POST it to the configured webhook. | network access |
| `email` | Hand it to the connected mail tool. | a connected mail tool |

Confirm before the first outward send of a session, fall back to in-chat
loudly when a method is unavailable, and never substitute a different
outward channel.

## Version

`trackiq-amazon-launch-scorecard` v1.0.0 (2026-09-18).

If the user asks whether this skill is current, fetch
`https://trackiq.com/skills/registry.json`, compare the `version` field for
`trackiq-amazon-launch-scorecard`, and if it is newer, give them the download link and
the one-line changelog. Do not fetch at any other time.
