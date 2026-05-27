# VIPI v0.1.3 — Errata 001

**Event date:** 2026-05-27
**Affected sub-indices:** vipi, vipi_open, vipi_closed
**Severity:** Publication continuity — single-day halt
**Status:** Documented; remediation underway

## What happened

The daily fix for 2026-05-27 was not published. All three sub-indices entered halt state with `halt_reason: all_constituents_zero_observations` and `value: null`. The prior fix (2026-05-26) published normally; the next fix is expected to publish normally.

## Root-cause hypothesis (under investigation)

The Methodology Observation Confirmation (MOC) window for the daily fix runs 15:55–16:05 UTC, served by a 5-minute cron. Each constituent requires a minimum observation floor of 2 observations within the window to be eligible (K_OBSERVATION_FLOOR = 2). On 2026-05-27, the price-aggregator cron appears to have missed one tick inside the MOC window. With only one in-window observation per mapping, every constituent fell below the K=2 floor and was dropped. With zero eligible constituents per sub-index, all three sub-indices halted per methodology rule.

The underlying pipeline did not fail — observations continued landing across all polled providers throughout the day. The failure is specific to the daily-fix window.

## Why this happened

The methodology's K=2 observation floor combined with a 10-minute MOC window served by a 5-minute cron leaves zero tolerance for any tick miss. A single missed tick is sufficient to halt the fix. This is a methodology fragility, not solely an operational defect.

## Remediation

1. **Methodology amendment v0.1.4 (forthcoming):** Revise MOC window tolerance to survive a single missed tick without halting. Options under evaluation include reducing K_OBSERVATION_FLOOR, widening the MOC window, or adding a last-good-observation fallback rule with an explicit quality flag. The amendment will be a forward-effective methodology revision, not a retroactive change.
2. **VIPI publication monitor (forthcoming):** Independent worker checking daily-fix publication state shortly after MOC window close, with email alarm on halt events. The 2026-05-27 halt was discovered by manual inspection; the methodology requires a structural alarm for this failure mode.
3. **Cron tick heartbeat (forthcoming):** Per-tick monitoring with alarm on missed ticks within the MOC window, allowing intervention before window closure.

## What this does not affect

- **Historical record.** All prior daily fixes remain intact and reproducible. No data revision required.
- **Methodology version stamp.** This halt is documented under the existing v0.1.3 methodology. The v0.1.4 amendment will reference this errata in its changelog.
- **Pipeline data.** Polled providers continued to land observations throughout 2026-05-27. Basket constituent prices for that date remain queryable for reproducibility purposes outside the daily-fix window.

## Errata commitment

The Volt methodology errata protocol applies to this event. This is the first published errata document for v0.1.3.

— Published 2026-05-27
