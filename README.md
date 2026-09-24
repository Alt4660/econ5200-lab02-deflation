# econ5200-lab02-deflation
# Index Integrity — Deflation, Substitution Bias & Goodhart

## Objective
Diagnose and repair a real-wage deflation pipeline, quantify upper-level substitution bias between CPI-U and the Chained CPI in the correct units, and show how a single target metric can diverge from the quality it is meant to represent.

## Methodology
- Diagnosed four defects in a nominal-to-real wage conversion: a single-month base instead of the base-year average, unaligned series that injected NaN rows, no missing-data handling, and a hard-coded scaling line that overwrote the correct result.
- Rebuilt the function to scale by the base-year average CPI, restrict to dates observed in both series, and drop missing values. Validated it with an arithmetic check that holds as FRED data are revised.
- Re-based the unadjusted CPI-U and the C-CPI-U to a common start month and computed each index's compound average annual inflation rate; the difference in rates is the substitution-bias estimate.
- Simulated a Goodhart scenario with DAU/MAU as the target metric and time per session as the counter-metric, then measured the correlation within the organic and gaming phases separately.
- Packaged the tested deflation function in `deflation_utils.py` and built an interactive monitor (start-date picker, seasonally adjusted vs. unadjusted toggle, rolling-correlation window, first-negative-window alert).

## Key Findings
- **Deflation:** The broken function returned real wages of 7.62 to 9.82, which are in 1982-84 dollars while labelled as 2020 dollars. The corrected series runs from $19.71 to $25.42 (2020 dollars), with January 1973 at $24.43.
- **Substitution bias:** Over Dec 1999 to Aug 2026, CPI-U rose 2.61% a year and C-CPI-U 2.35% a year, a gap of **0.27 percentage points per year**. Because CPI-U already uses geometric means within categories, this isolates the upper-level (between-category) component. It sits above Boskin's 1996 estimate of 0.15 pp; a plausible, untested explanation is the relative-price dispersion of the 2021-23 inflation surge.
- **Units matter:** The same gap expressed as 0.50 index points per year is not a rate, because it depends on the index level. Only the compounded percentage figure is the quantity a cost-of-living adjustment applies.
- **Goodhart's Law:** The correlation between DAU/MAU and time per session flipped from +0.93 (organic phase) to -0.96 (gaming phase), a diagnostic sign that the target metric decoupled from quality.
- **Monitoring:** With a 12-month rolling window, the monitor's alert first fired for Feb 2023 to Jan 2024 (r = -0.20), six months after gaming began, illustrating the speed-versus-noise tradeoff in window length.
