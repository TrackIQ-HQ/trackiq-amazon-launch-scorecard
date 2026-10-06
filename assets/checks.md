# Before you send it

## 1. The weekly data is real

- **Twelve separate calls were made**, one per week, over explicit date ranges.
- No week's figure was derived by dividing a multi-week aggregate. Spot-check:
  are any two weeks identical to the cent? That is the signature of a divided
  aggregate.
- Weeks run Sunday to Saturday and are numbered from the launch date.
- Week 1 is labelled partial and is **not graded**.
- SKUs were summed to the ASIN. A launch that gained a second SKU mid-window has
  both counted.

## 2. The target

- The week-12 target is stated, with where it came from.
- The curve is stated — the exponent, or the client's own shape.
- If no target was supplied, **nothing on the page is graded** and the report
  says it is descriptive.

## 3. TACoS

- Computed against **total** revenue, not attributed sales.
- The glide path is drawn on the page with both endpoints stated.
- Where ad spend could not be isolated to the ASIN, the row says so and is
  labelled campaign-level.
- No apportioned account spend is presented as the launch's spend.

## 4. Rank

- If rank is shown, it came from **Oxylabs sampling**, not from
  `get_keyword_rank` — that tool returns null ranks and a single tracked date.
- If rank is not being sampled, **the row is absent**, not blank.
- The keyword set is listed, so the client knows what was measured.

## 5. Reviews

- On a first run, the report says velocity begins next week and does not show a
  zero.
- On a later run, the previous figures used are stated with their date.
- The current counts are **saved into the output** for the next run.
- No trend is drawn from the eight inline review bodies.

## 6. Stock

- Cover was checked and computed **per ASIN**, never per SKU.
- A stock-out or sub-14-day cover appears at the **top** of the scorecard, not
  in a footnote. It invalidates the grade and the report should say that.

## 7. The verdict

- One verdict, from the most recent graded week.
- **One lever.** Not a list.
- A "fix" names a single action and the metric it should move.
- Red flags that triggered the grade are visible on the page beside it.

## 8. Render check

```js
({ overflows: document.documentElement.scrollWidth > document.documentElement.clientWidth,
   weeks: document.querySelectorAll('table tbody tr').length,
   logos: [...document.images].map(i => i.naturalWidth > 0),
   grades: [...document.querySelectorAll('.state')].map(e => e.textContent.trim()),
   tokens: (document.body.innerHTML.match(/\{\{[A-Z0-9_]+\}\}/g) || []).length })
```

`overflows` false, `logos` all true, `tokens` zero, and `grades` one shorter
than `weeks` — week 1 is not graded.

## 9. Ship

Save as `<client>-launch-scorecard-<ASIN>-week-<NN>.html`. Named by the week,
because the whole point is the sequence.

Keep every week's file. The scorecard's value compounds — week 9 read alone says
little, and week 9 beside weeks 1 to 8 is the whole story.
