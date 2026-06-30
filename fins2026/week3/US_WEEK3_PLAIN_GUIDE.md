# Week 3 with U.S. Data — A Plain-Language Beginner Guide

No experience needed. This guide walks you through Week 3 using United States
data, one small step at a time. Every term is explained in normal words. If you
follow it top to bottom, you will end up with finished charts, tables, and a
clear story you can write about.

---

## 1. What you are actually trying to do

You are trying to **guess next month's number before it happens**, using only
the past.

The number you are guessing is the **U.S. unemployment rate** — the share of
people who want a job but don't have one, reported once a month (like "4.1%").

Here is the one twist that trips people up:

> You are **not** guessing the rate itself. You are guessing the
> **change** from one month to the next.

So instead of guessing "next month will be 4.1%", you guess "next month it will
go **up by 0.1** / **down by 0.2** / **stay flat**".

**Why guess the change instead of the level?** Because the level drifts slowly
and is easy to fake-predict (just say "same as last month" and you'll look
roughly right). The change is the honest, hard part — and it's where the real
skill shows up. You'll see this proven in Step 5 below.

---

## 2. The big idea behind the whole week

You will build a few different "guessing machines" (called **models**) and have
them **compete**. The rules of the competition are simple and fair:

1. Each model only gets to see the past — never the future.
2. Each month, it makes one guess for the next month.
3. We then check the guess against what really happened.
4. We add up who was closest, over many months.

This fair replay of history is called a **backtest**. Think of it as: "If I had
been using this method back then, how wrong would I have been?"

The models, from simplest to fanciest:

| Model | What it does, in plain words |
|-------|------------------------------|
| **Naive** | "Next month's change = this month's change." Just copy the last number forward. This is the lazy baseline everyone must beat. |
| **AR** | "Look at the last few months' changes and learn the pattern." (AR = it looks back at itself.) |
| **ARMA** | The AR idea, plus it also learns from its own **recent mistakes** and corrects for them. |
| **ARX** | The AR idea, plus it peeks at **outside clues** — here, last month's interest-rate move and a bond-market signal. |

The whole point of Week 3 is to see **whether the fancier machines actually beat
the lazy one** — and by how much. Sometimes they do. Sometimes they barely do.
Finding that out *is* the assignment.

---

## 3. Before you start: open a terminal

You need to type a few commands. A "terminal" is just a text window where you
type commands and press Enter.

- Open it at the project folder (the `fins-agent` folder). In PyCharm, use the
  **Terminal** tab at the bottom.
- Every command below uses the project's own Python so the right tools are
  loaded. You don't need to understand it — just copy the whole line.

**On a Mac** (your setup), commands start with `./.venv/bin/python`.
**On Windows**, swap that for `.\.venv\Scripts\python.exe` and use back-slashes
in the path.

> If a command errors with "no such file" or "module not found", you probably
> haven't run setup yet. Type `/onboard` to Claude, or ask for help — don't
> push past the error.

---

## 4. The steps, in order (this is the recipe)

Run these **one at a time**, top to bottom. Read what each one prints before
moving on. Each line is one command — copy the whole line.

```bash
./.venv/bin/python fins2026/week3/scripts/make_us_beginner_forecasting_series.py
./.venv/bin/python fins2026/week3/scripts/run_us_beginner_unit_root_check.py
./.venv/bin/python fins2026/week3/scripts/run_us_beginner_naive_forecast.py
./.venv/bin/python fins2026/week3/scripts/run_us_beginner_ar_forecast.py
./.venv/bin/python fins2026/week3/scripts/run_us_beginner_arma_forecast.py
./.venv/bin/python fins2026/week3/scripts/run_us_beginner_arx_forecast.py
./.venv/bin/python fins2026/week3/scripts/run_us_beginner_model_horse_race.py
./.venv/bin/python fins2026/week3/scripts/make_us_beginner_forecast_story_figures.py
```

Here is what each one does, in plain words:

### Step A — Get the data ready
`make_us_beginner_forecasting_series.py`

Cleans up the raw U.S. numbers and saves tidy files: the unemployment rate, a
small table of extra clues (interest rates, a bond signal), and a tiny practice
dataset used in the next step. **Nothing to interpret here** — it just prepares
the kitchen.

### Step B — Check that "guess the change" was the right call
`run_us_beginner_unit_root_check.py`

This runs a quick test that answers one question: *"Is the raw unemployment
**level** too drifty to forecast directly?"* The test confirms yes — which is
exactly why the whole week works with the **monthly change** instead. You're
just confirming the plan from Step 1 was sensible. (Don't worry about the test's
name or its math; read the printed conclusion sentence.)

### Step C — Build the lazy baseline
`run_us_beginner_naive_forecast.py`

Builds the "just copy last month" guesser and runs the fair backtest on it. This
gives you the **score to beat**. Everything later is measured against this.

### Step D — The AR model (the heart of the week)
`run_us_beginner_ar_forecast.py`

Builds the model that learns from the last few months. It quietly tries a few
"how far back should I look?" settings, picks the best one automatically, and
then races Naive vs AR in the backtest. **This is the main event** — check
whether AR beats Naive here.

### Step E — Add memory of past mistakes
`run_us_beginner_arma_forecast.py`

Same as AR, but the model also learns from its own recent errors and nudges
itself. See if that nudge helps or not.

### Step F — Add outside clues
`run_us_beginner_arx_forecast.py`

Now the model is allowed to look at two extra hints from the wider economy:
- **last month's change in the Fed's interest rate** (are rates rising/falling?),
- **a bond-market signal** (the "yield spread", a number markets watch as a
  recession warning).

Question to ask: do these outside clues actually improve the guess, or is the
unemployment series basically talking to itself?

### Step G — The final scoreboard
`run_us_beginner_model_horse_race.py`

Lines all the models up and ranks them from best to worst on the same fair test.
**This is the table you'll quote in your write-up.** (How to read it is in the
next section.)

### Step H — Make the nice charts
`make_us_beginner_forecast_story_figures.py`

Turns all of the above into clean, presentation-ready charts that tell the story
visually. These go straight into your report or slides.

---

## 5. How to read the scoreboard (the only "scoring" you need)

The horse-race table gives each model a few scores. Here's all you need to know:

- **MAE** and **RMSE** — "how far off, on average." **Lower is better.** RMSE
  punishes big misses more than MAE does. Just compare the numbers; whoever is
  lowest, wins.
- **MASE** — the same idea but as a ratio against the lazy Naive model:
  - **Below 1.0** = beat the lazy baseline. 
  - **Exactly 1.0** = tied with it.
  - **Above 1.0** = worse than just copying last month. 
- **out-of-sample R-squared vs naive** — "how much better than lazy, as a
  percentage." `0.26` means about **26% better** than the Naive baseline. `0`
  means no better. A negative number means worse.

**So your whole judgement comes down to two questions:**
1. Which model has the lowest RMSE?
2. Did it actually beat Naive (MASE under 1.0, R-squared above 0)?

> Reality check: don't be surprised if the gap is small, or if the simplest
> model nearly ties the fancy ones. For a calm series like unemployment, "the
> simple model is hard to beat" is a **legitimate and interesting finding** —
> write that up honestly rather than pretending the fancy model won big.

---

## 6. Where your results land

After you run everything, look in these folders:

- **Charts (PNG images):**
  `fins2026/week3/results/figures/us_beginner_forecasting/`
  and the polished story versions in
  `fins2026/week3/results/figures/us_beginner_forecast_story/`
- **Tables (CSV files you can open in Excel):**
  `fins2026/week3/results/tables/us_beginner_forecasting/`
- **Cleaned data:**
  `fins2026/week3/results/data/us_beginner_forecasting/`

Open the charts to *see* the story; open the horse-race table to *quote* the
numbers.

---

## 7. What to write in your report

A simple, honest structure that follows what you did:

1. **What I tried to predict** — the monthly change in U.S. unemployment, and
   why the change rather than the level (Step B confirmed it).
2. **The baseline** — the lazy "copy last month" model and its score.
3. **The models I tested** — AR, ARMA, ARX, in one sentence each.
4. **The result** — the scoreboard. State which won, by how much, and whether
   it truly beat the baseline. Use real numbers ("RMSE 0.26 vs 0.30, about 14%
   better"), not vague words like "much better".
5. **One honest limitation** — e.g. no model could predict a sudden shock like
   2020; the outside clues helped only a little; etc.
6. **One takeaway sentence** — the single thing a reader should remember.

> Tip: when you write, lead with the result, use plain words, and back every
> claim with a number from your own tables. Ask Claude `/write-section` or
> `/proofread` to help polish — but the findings must be your own.

---

## 8. If something goes wrong

- **A command fails:** read the last few lines of red text. Most often it's a
  missing setup step — run `/onboard` or ask for help. Don't skip ahead.
- **A later script complains about missing files:** you probably ran the steps
  out of order. Start again from Step A and go top to bottom.
- **Numbers look weird or empty:** re-run Step A to rebuild the data, then
  re-run from there.
- **Stuck on what a result means:** open the matching chart in
  `results/figures/...` — the picture usually makes it obvious.

---

## 9. The one-paragraph summary

You take U.S. unemployment, turn it into month-to-month changes, and hold a fair
contest between a lazy "copy last month" guesser and a few smarter ones that
learn from the past and from outside clues. You replay history honestly, score
everyone on how close they got, and report who won and by how much. The skill
being graded isn't building the fanciest machine — it's running the contest
fairly and reading the scoreboard honestly.
