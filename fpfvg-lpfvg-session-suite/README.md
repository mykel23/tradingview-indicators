# FPFVG / LPFVG Session Suite

A TradingView overlay (Pine Script v5) for futures such as MNQ. In each of 13 New York-time sessions it marks up to five fair value gaps across the hour: the **first presented fair value gap (FPFVG)** at the top of the hour, the gaps that form in the **:15, :30 and :45** quarters, and the **last presented fair value gap (LPFVG)** at the end of the hour. It uses true 1-minute data whatever timeframe the chart is on.

A fair value gap is a three-candle pattern where the first and third candles' wicks don't overlap, leaving a price gap. Each one is drawn as a box with a dashed midline and a label, and extended until that session's end time.

![FPFVG / LPFVG Session Suite on a 1-minute MNQ chart](screenshot.png)
*MNQ, 1-minute chart: 6pm, 7pm and Tokyo session gaps, with their midlines and labels.*

## Sessions and slots

There are 13 sessions (New York time): **Midnight, 1am, 2am, 3am, 6am, 7am, 8am, 9am, 10am, 6pm, 7pm, Tokyo (8pm), 10pm.**

Each session has five slots, each with its own on/off toggle:

| Slot | Window | Rule |
|---|---|---|
| **:00** | :00–:15 | The first FVG found wins |
| **:15** | :15–:25 | The first FVG, replaced if a larger gap forms later in the window |
| **:30** | :30–:40 | Same override rule |
| **:45** | :45–:50 | Same override rule, plus a **:54 exception**: a larger gap at :54 replaces it |
| **Last (LPFVG)** | :54–:59 | The latest FVG in the window wins |

Windows are anchored to the displacement (middle) candle, and gaps of 5 ticks or less are ignored as noise.

## How long boxes extend

Each slot has an **Extension** dropdown: **Default**, 1h–23h, or 1–7 days. Default keeps that session's built-in rule:

| Session | Default extension |
|---|---|
| Midnight | 24 hours |
| 1am, 2am, 3am, 7am, 8am, 9am | Until 10:00am (9am :30 extends 24 hours) |
| 6am, 10am | Until 12:00pm (10am :00 extends 24 hours) |
| 6pm | Until 4:00am |
| 7pm | Until 11:00pm |
| Tokyo | :00, :30 and Last until 6:00am; :15 and :45 until midnight |
| 10pm | Until 6:00am |

Boxes grow 10 bars ahead at a time until they reach their end, then freeze. Colors are gold by slot type: pale for :00 and Last, gold for :30, and darker gold for :15 and :45.

## How it detects gaps on any timeframe

- **Charts above 1 minute** (5m, 15m…): it pulls real 1-minute bars with `request.security_lower_tf`. When TradingView can't supply true 1-minute history and silently hands back the chart's own bar instead, a reliability check spots it and skips that bar, so a 5-minute candle is never mistaken for 1-minute data.
- **1-minute charts:** it uses the chart's bars directly.
- **Charts below 1 minute** (30s, 15s…): it groups the bars into real 1-minute candles first.
- An adjacency check makes sure the three candles are consecutive minutes.

## Install

1. In TradingView, open the **Pine Editor** at the bottom of a chart.
2. Paste in [`fpfvg_lpfvg_session_suite.pine`](fpfvg_lpfvg_session_suite.pine) and click **Add to chart**.
3. Turn sessions and slots on or off in the indicator's settings.

## How it was built

The commit history shows every step: 20 versions.

1. **v01: a big first draft.** It tried to build all 29 trackers at once, and it was hard to debug.
2. **v02–v06: start over in small, testable steps.** Confirm the data and New York time, detect FVGs, then get one session (7am) fully right: per-day reset, correct bearish math, extension and the override window.
3. **v07–v13: scale up in batches.** First 11 simple sessions, then LPFVGs, the :15/:30/:45 override quarters, the :54 exception, and cleanup.
4. **v14–v18: accuracy.** True 1-minute detection on any timeframe, a reliability filter for missing data, sub-minute charts, displacement-candle anchoring, and minimum gap sizes.
5. **v19–v20: flexibility.** Five slots for every session and per-slot extension choices.

**About the history:** some intermediate files were overwritten during development. Versions v04, v07 and v14–v19 were **reconstructed** from notes and neighboring versions, and their commit messages say so. v01 is the earliest copy that survived. v02, v03, v05, v06, v08–v13 and the final v20 are the original files.

## Known limitations

- **Pine Script limits:** the script can draw up to 200 boxes and 200 labels. With all 65 slots on, older days drop off the chart sooner, so turn off slots you don't use.
- **History depth:** how far back true 1-minute data is available depends on your TradingView plan. Bars without reliable 1-minute data are skipped rather than guessed.
- **Session times are fixed** to New York time.
- **Labels can overlap** when two slots in the same session find gaps at nearly the same price (for example, 6pm :45 and the 6pm LPFVG).

## Credits

The trading concepts (fair value gaps, first and last presented FVGs, session timing) come from ICT's public teaching. The code is original, built with Claude from my own descriptions and chart testing.

*For educational purposes only. Not financial advice.*
