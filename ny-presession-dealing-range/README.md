# NY Pre-Session Dealing Range

A TradingView overlay (Pine Script v6) for futures such as MNQ. It records the high, low and midpoint (C.E.) of the **7:00–9:00am New York session**, projects standard-deviation extension levels above and below that range, and draws octant (eighths) levels inside it. It also plots the 9:30am and midnight open prices.

Lines grow forward 10 bars at a time until they reach their end time, and they stay on the chart so past days can be reviewed.

## What it draws

| Element | Details |
|---|---|
| Dealing range | High, Low and C.E. (midpoint) of the 7–9am NY session, starting from whichever extreme formed first |
| STDV levels | .25, .5, 1, 1.5, 2, 2.5, 3, 4 and 4.5 times the range width, above the high and below the low |
| Octant levels | Six unlabeled lines at .125, .25, .375, .625, .75 and .875 of the range (the .5 line is the C.E.) |
| Open prices | 9:30am and midnight NY opens, labeled with the weekday and date |

## Settings

**NY Pre-Session Dealing Range**
- **Show Dealing Range:** toggles the High, Low and C.E. lines
- **Range Line Extension:** hours after the 9am session end (default 7, ending at 4:00pm NY; range 1–24). Also sets where STDV lines end.
- **High / Low / C.E. Color:** defaults `#3658b7`, `#3658b7`, `#b22833`
- **Line Width:** 1–4, applies to every line the script draws

**STDV Levels**
- **Show STDV Levels:** master toggle
- Individual toggles for each of the nine levels, drawn on both sides of the range
- **STDV Color:** default red

**Octant Levels**
- **Show Octant Levels:** toggles the six inner lines
- **Octant Extension Cutoff:** NY hour on a 24-hour clock (default 16 = 4:00pm)
- **Octant Color:** default blue

**Reference Open Prices**
- **Show 9:30 Open Price / Show Midnight Open Price:** separate toggles
- **Open Price Line Extension:** hours (default 72 = three days; range 1–168)
- **9:30 Open Color / Midnight Open Color:** default teal

## Install

1. In TradingView, open the **Pine Editor** at the bottom of a chart.
2. Paste in [`ny_presession_dealing_range.pine`](ny_presession_dealing_range.pine) and click **Add to chart**.
3. Use a 1-minute to 15-minute chart for best results.

## How it evolved

Each version is a separate commit, so the history shows how it was built:

| Version | Change |
|---|---|
| v1 | Dealing range, STDV grid, octants and open lines |
| v2 | Fixed a runtime error (RE10045) when the extension loops ran on empty arrays |
| v3 | Range and STDV lines end at 4:00pm NY instead of running 24 hours |
| v4 | Octants made unlabeled; dropped the 4/8 line since it duplicates the C.E. |
| v5 | Octants extend to 4pm and turn blue |
| v6 | Custom colors for High, Low and C.E. |
| v7 | Lines start where the first range extreme formed, not at 9:00am |
| v8 | Open lines extend three days, with weekday and date in the label |

## Known limitations

- **Tested on 1m and 15m charts only.** Session detection uses the chart's own bars, so 1-hour and higher timeframes are unverified.
- **v7 and v8 haven't been visually confirmed** on a live chart yet.
- **The "(this week)" label suffix is static** and doesn't change as lines age.
- **The octant cutoff must be later than 9am** and is whole hours only; an earlier hour would produce a negative length.
- **The range extension counts from 9:00am,** not from where the lines start. This is intentional so the 4pm end stays fixed.
- **Old drawings aren't cleaned up.** That's by design for reviewing past days, but TradingView keeps at most 500 lines and 500 labels. At about 29 lines per day, the oldest days start dropping after roughly 17 trading days.

## Planned

- 9:39am sweep and 9:30-open reclaim logic
- "Possible Reversal" marking snapped to the octant grid
- 10am 15-minute C.E. closure check, with a "TP 1" label
- 7am and 8am first-presented and 8am last-presented fair value gap overlays

## Credits

The trading concepts (dealing ranges, standard-deviation projections, fair value gaps) come from ICT's public teaching. The code is original, written with Claude from my own descriptions and chart screenshots. It follows the line and label conventions of my Final Hour Dealing Range indicator.

*For educational purposes only. Not financial advice.*
