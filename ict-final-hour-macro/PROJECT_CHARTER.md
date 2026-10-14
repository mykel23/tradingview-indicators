# ICT Final Hour Macro / SMT Indicator — Project Charter

## Purpose

Build a TradingView Pine Script indicator for the MNQ/MES 1-minute chart that
identifies ICT's Final Hour Market-on-Close macro setup: a liquidity raid on
engineered buy-side or sell-side stops inside the Final Hour Dealing Range
(3:00–3:50pm NY), confirmed by SMT divergence against a correlated instrument
(NQ↔ES), during the 3:50–4:00pm macro window.

## Guiding principles for this build

- **One capability per iteration.** Each iteration ships a small, independently
  testable piece of the indicator — not partial versions of many features at once.
- **Verify before advancing.** An iteration isn't "done" until it's been tested
  in TradingView (ideally via Replay on 08/03/2026) and confirmed working.
- **No scope creep mid-iteration.** New terminology or feature ideas that come up
  get parked for a later iteration rather than folded into the current one.
- **Code isn't touched until the iteration is explicitly greenlit.**

## Definition of done (applies to every iteration)

- Compiles with no errors in the Pine Editor
- Renders on the main candlestick pane (not a separate pane)
- Verified visually against Replay on 08/03/2026
- No regression to previously completed iterations

---

## Iteration backlog

### Iteration 1 — Final Hour Dealing Range (in progress)
**Goal:** Auto-plot the 3:00–3:50pm dealing range high/low as a box on the price
chart, correctly overlaid on the candles.

**Scope:**
- Track session high/low from 3:00–3:50pm NY time
- Draw as a box + high/low lines directly on the candlestick pane
- Lines stop extending at a configurable cutoff (default 4:25pm) instead of
  running to the edge of the chart forever
- Confirm the overlay/pane issue from the last test is resolved

**Explicitly out of scope for this iteration:** fib/STDV levels, equal
highs/lows, SMT, raids, targets, signals — all later iterations.

**Acceptance criteria:** On a replay of 08/03/2026, a clean box appears on the
candlestick pane matching the actual 3:00–3:50pm high/low, and it renders in
the same pane as price (no separate panel).

---

### Iteration 2 — Fib / STDV Levels on the Range
**Goal:** Plot the fib retracement levels (0, .125, .25, .375, .5 C.E., .625,
.75, .875, 1) and the STDV target-cap extensions (-0.5 / 1.5) on top of the
Iteration 1 range.

**Depends on:** Iteration 1 confirmed working.

**Acceptance criteria:** Levels match your fib settings screenshot, C.E. (0.5)
is visually distinct (red), and the STDV cap lines are clearly marked as the
target ceiling/floor.

---

### Iteration 3 — Buyside / Sellside Liquidity (Equal Highs / Equal Lows)
**Goal:** Auto-detect and plot equal highs (buyside liquidity) and equal lows
(sellside liquidity) within the range + macro window, using the tick
tolerances (true equal / equal / relative equal) and the sequencing validity
rule (2nd point higher for lows, lower for highs).

**Depends on:** Iteration 1 confirmed working. (Doesn't strictly need
Iteration 2, but easier to test with the range visible.)

**Acceptance criteria:** On replay, the equal-highs/lows clusters plotted
match what you'd manually mark on the same chart, with correct quality
labeling (True Equal / Equal / Relative Equal).

---

### Iteration 4 — SMT Divergence (NQ ↔ ES)
**Goal:** Auto-detect and plot SMT divergence between the primary symbol and
its correlated instrument, based on the Final Hour Dealing Range High/Low
sweep, matched to the exact same 1-minute timestamp.

**Depends on:** Iteration 1 confirmed working.

**Acceptance criteria:** On a day/time where one instrument swept its range
boundary and the other didn't, the "SMT with ES" (or NQ) line and label
appear correctly on the primary chart.

**Deferred to a later iteration:** SMT comparison at the equal-highs/lows
level specifically (only range-high/low SMT in this iteration).

---

### Iteration 5 — Liquidity Raid / Sweep Detection
**Goal:** Detect and flag the actual raid/sweep event: wick pierces a level
(range boundary or equal-highs/lows cluster), body stays on the safe side,
only valid during the macro window and only if the range hasn't seen a real
close-based breakout.

**Depends on:** Iterations 1 and 3 confirmed working.

**Acceptance criteria:** Raid markers appear only on valid sweep candles per
your wick-grading rules, and never fire outside the macro window or after a
real breakout.

---

### Iteration 6 — Targets (T1 / T2 + STDV Cap)
**Goal:** Plot T1 (opposite range boundary, or the "low hanging fruit" wick
level when one sits close by) and, if feasible, T2 (next MOC swing liquidity),
capped by the STDV extension.

**Depends on:** Iterations 1, 2, and 5 confirmed working.

**Acceptance criteria:** T1 plots correctly per the rules; T2 either plots
reliably or is explicitly flagged as unreliable/deferred rather than guessed.

---

### Iteration 7 — Buy/Sell Signals + Criteria Table
**Goal:** Add actual buy/sell signal markers and the top-right on-chart table
showing which criteria have been met.

**Depends on:** All prior iterations confirmed working — this is the
integration layer on top of everything else.

**Acceptance criteria:** Signals only fire when the full criteria set is met;
the table accurately reflects live status of each criterion.

---

### Iteration 8 — New Terminology Layer (Deferred)
**Goal:** Incorporate fair value gaps, premium wick / premium sensitivity, PD
arrays, octant/quadrant, and any other concepts you want added once you've
explained them.

**Depends on:** Iteration 7 confirmed working, and your explanations of the
terms.

---

## Status tracker

| # | Iteration | Status |
|---|-----------|--------|
| 1 | Final Hour Dealing Range | In progress — pane/overlay issue being resolved |
| 2 | Fib / STDV Levels | Not started |
| 3 | Buyside / Sellside Liquidity | Not started |
| 4 | SMT Divergence | Not started |
| 5 | Liquidity Raid / Sweep Detection | Not started |
| 6 | Targets (T1/T2) | Not started |
| 7 | Buy/Sell Signals + Table | Not started |
| 8 | New Terminology Layer | Not started (deferred) |
