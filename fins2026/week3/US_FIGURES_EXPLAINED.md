# Week 3 (U.S. Data) — Every Figure Explained in Plain Words

You've run all the code. This file walks through each chart you made, in the
order the story unfolds. No jargon. For each one: what you're looking at, and
what it's telling you.

Your figures live in two folders:

- **Teaching figures** (one chart per step):
  `results/figures/us_beginner_forecasting/`
- **Story figures** (polished, report-ready):
  `results/figures/us_beginner_forecast_story/`

Quick reminder of the cast of characters (the models):

- **Naive** = the lazy baseline: "next month's change = this month's change."
- **AR(3)** = looks back at the last 3 months to spot a pattern.
- **ARMA(2,1)** = the AR idea, plus it also corrects for its own recent mistakes.
- **ARX(3)** = the AR idea, plus outside clues (interest rates + a bond signal).

---

## PART 1 — Setting the scene

### `..._story_level_change.png` — Level vs. change
*(story folder)*

**Two stacked charts, 1948 to today.**
- **Top ("Unemployment level"):** the actual unemployment rate over ~75 years.
  It wanders in big slow waves and has a giant single spike in 2020 (COVID).
  The slow wandering is the point: the level "remembers" where it was, so it's
  too drifty to predict head-on.
- **Bottom ("Change in unemployment"):** the month-to-month *change*. It mostly
  hugs zero with small wiggles — except one enormous spike in 2020. **This
  bottom line is the thing you're actually forecasting.** The shaded band on the
  right is the "exam period" (the recent months used to score the models).

**Takeaway:** the level is too slippery to predict directly, so the whole week
forecasts the change instead. This chart justifies that choice visually.

### `..._unit_root_simulations.png` — Why "change," proven with fake data
*(teaching folder)*

**Three made-up example series stacked vertically:**
- **Top ("Random walk"):** wanders off and never comes back to a home base —
  like the unemployment *level*. Hard to forecast.
- **Middle ("mean reversion"):** bounces around a fixed center and keeps
  returning to it — like the *change*. Much friendlier to forecast.
- **Bottom ("short memory"):** similar, returns to center quickly.

**Takeaway:** this is a "here's the difference" demo. It shows what a
"won't-settle-down" series looks like vs. a "keeps-coming-home" series, which is
exactly why you forecast the well-behaved change, not the drifty level.

---

## PART 2 — Picking the model settings

### `..._ar_lag_selection.png` — How far back should AR look?
*(teaching folder)*

**Four blue bars: looking back 1, 3, 6, or 12 months.** The bars hang *down*
from zero; a score called BIC rewards a good trade-off between fit and
simplicity. **The lowest (most-negative) bar wins.**

- Looking back **1 month** is clearly the weakest (shortest bar).
- **3 and 6 months** are tied for best (longest bars).
- The model picks **3 months → AR(3)** (it prefers the simpler of the two ties).

**Takeaway:** the data says "one month of memory isn't enough; about three is the
sweet spot."

### `..._arma_order_selection.png` — Which ARMA recipe?
*(teaching folder)*

**Four blue bars for four recipe combinations** — written as `(2,1)` etc.,
meaning "how much past + how much mistake-correction." Same rule: lowest bar wins.

- **(2,1)** is clearly the best (longest bar).
- So the chosen model is **ARMA(2,1)**.

**Takeaway:** adding a dash of "learn from my recent error" (the `1`) on top of
two months of memory gives the best score.

---

## PART 3 — Watching each model guess (the backtests)

These replay history month by month: each line is what a model *would have
predicted*. Closer to the black "Actual" line = better.

### `..._story_backtest.png` — The headline comparison
*(story folder)*

**One chart, 2020 onward, three lines:** black **Actual**, grey **Naive**, red
**ARMA(2,1)** (the winner).

- In **2020**, COVID sends the actual change rocketing to **+10**. Both models
  miss it (nobody could predict COVID), but notice **Naive overshoots wildly**
  with a huge late spike, while **ARMA stays calmer**.
- After 2021 everything settles near zero and the lines mostly overlap — calm
  times are easy for everyone.

**Takeaway:** the models earn their keep around the shock. The winner doesn't
*predict* COVID; it just panics less than the lazy baseline.

### `..._ar_backtest.png` and `..._arma_backtest.png` — Per-model close-ups
*(teaching folder)*

Same idea as above, but each focuses on one model family against Naive, in two
panels (level on top, change on bottom). Use these if you want a clean one-model
picture for a slide. The story they tell is identical: tight tracking in calm
periods, the real differences show up at the 2020 spike.

### `..._arx_backtest.png` — Do outside clues help?
*(teaching folder)*

**Two panels with several near-identical lines:** plain AR(3), AR plus interest
rates, and AR plus interest rates *and* the bond signal.

- The lines sit **almost on top of each other**. Adding the outside clues barely
  changes the forecast.

**Takeaway:** for U.S. unemployment, the series mostly "talks to itself." The
extra economic signals don't add much — an honest and useful finding to report.

### `..._story_absolute_errors.png` — How wrong, month by month
*(story folder)*

**One chart of "miss size" over time** (0 = perfect; higher = worse), grey Naive
vs. red ARMA. **Lower line = better.**

- A big shared hump in **2020** (everyone struggles with COVID), but red (ARMA)
  peaks **lower** than grey (Naive) — it misses the shock by less.
- After 2021 both lines drop near zero and stay tiny.

**Takeaway:** the winner's advantage comes almost entirely from making a
*smaller* mistake during the 2020 shock, not from being better in calm months.

---

## PART 4 — The verdict

### `..._story_scorecard.png` — The final scoreboard
*(story folder)*

**Four mini bar charts, one per score.** Each ranks all four models.

- **RMSE (lower better):** ARMA(2,1) shortest → best. Then AR(3), then ARX(3),
  with **Naive worst**.
- **MAE (lower better):** same order, ARMA best.
- **MASE (lower better):** same story — the learned models beat lazy.
- **OOS R² vs naive (higher better):** ARMA highest (biggest improvement over
  lazy), AR(3) close behind, ARX(3) only a little above zero.

**Takeaway — the whole week in one picture:**
1. **ARMA(2,1) wins.** The learned models beat the lazy baseline.
2. **ARX is the weakest of the smart models** — the outside clues actually hurt
   a touch here, confirming the ARX backtest.
3. The gains are real but modest, and they come from handling 2020 better.

### `..._story_latest_forecast.png` — What it predicts next
*(story folder)*

**Top:** a zoom-in on the recent unemployment level with a dot marking where the
**winner** thinks next month lands. **Bottom:** the four models' guesses for next
month's change, side by side ("model spread").

- All four predict a **small change near zero** — they broadly agree right now.
- Naive's bar is the largest (it just echoes last month); the learned models sit
  closer to zero.

**Takeaway:** in today's calm conditions the models nearly agree, so the
next-month call is "roughly flat." The disagreement only matters when things get
turbulent.

---

## The 30-second summary

You forecast the **monthly change** in U.S. unemployment (not the level, because
the level won't settle down). You tuned an AR model to look back **3 months** and
an ARMA model to recipe **(2,1)**, then raced everything fairly from 2020 on.
**ARMA(2,1) won, the learned models beat the lazy baseline, and the outside
economic clues barely helped.** Almost all of the winner's edge comes from
panicking less during the 2020 shock — in calm months, even the lazy model is
hard to beat.
