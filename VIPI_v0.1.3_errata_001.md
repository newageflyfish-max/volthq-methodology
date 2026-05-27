# VIPI v0.1.3 — Errata 001

**Event date:** 2026-05-27
**Affected sub-indices:** vipi, vipi_open, vipi_closed
**Severity:** Publication continuity — single-day halt
**Status:** Documented; remediation underway

> **REVISED 2026-05-27 22:02 UTC.** This document was first published earlier on 2026-05-27 with a root-cause attribution naming a missed cron tick as the proximate cause. Subsequent inspection of D1 snapshot records found that every `*/5` cron fired inside the MOC window on 2026-05-27; no tick was missed. The actual proximate cause is documented in the "Root cause" section below. The directional finding — that K = 2 combined with a 10-minute MOC window served by a 5-minute cron has zero tolerance for any single excluded in-window snapshot — is unchanged and remains the load-bearing methodology fragility this errata documents.

## What happened

The daily fix for 2026-05-27 was not published. All three sub-indices entered halt state with `halt_reason: all_constituents_zero_observations` and `value: null`. The prior fix (2026-05-26) published normally; the next fix is expected to publish normally.

## Root cause

The MOC window for the daily fix runs 15:55–16:05 UTC, served by a 5-minute cron — exactly two ticks per window. Each constituent requires a minimum observation floor of K = 2 in-window observations to be eligible (K_OBSERVATION_FLOOR = 2).

On 2026-05-27, both in-window ticks fired on schedule. The first tick (snapshot at 2026-05-27T15:55:46Z) was stamped `quality_flag = 'degraded'` by the price-aggregator's graceful-degradation logic after one polled provider's served-model catalog contracted sharply during that snapshot. The vipi-cron compute step filters MOC-window snapshots by `quality_flag = 'clean'`, which excluded the degraded snapshot's rows entirely. The second tick was `clean` and contributed its rows normally.

The result was exactly one in-window observation per mapping — strictly below K = 2. Every constituent was dropped. Every sub-index halted per methodology rule. The underlying pipeline did not fail: graceful-degradation operated as designed, and the cron fired on schedule.

## Why this happened

The methodology's K = 2 observation floor combined with a 10-minute MOC window served by a 5-minute cron means the eligibility computation has zero tolerance for any single excluded in-window snapshot — whether that snapshot is missed, stamped `degraded`, or excluded by any other quality filter. A single excluded in-window tick is sufficient to halt all three sub-indices. This is a methodology fragility, not solely an operational defect. Methodology §7.2 explicitly identifies this combination as the source of the fragility that fired on 2026-05-27.

## Remediation

1. **Methodology amendment v0.1.4 (forthcoming):** Revise MOC-window tolerance to survive a single excluded in-window snapshot without halting. Options under evaluation include reducing K_OBSERVATION_FLOOR, widening the MOC window, or admitting `degraded` rows with explicit downweighting and annotation in the audit record. The amendment will be a forward-effective methodology revision, not a retroactive change.
2. **VIPI publication monitor (forthcoming):** Independent worker checking daily-fix publication state shortly after MOC-window close, with email alarm on halt events. The 2026-05-27 halt was discovered by manual inspection; the methodology requires a structural alarm for this failure mode.
3. **In-window snapshot quality monitoring (forthcoming):** Per-tick monitoring with alarm on missed or degraded snapshots inside the MOC window, allowing intervention before window closure.

## What this does not affect

- **Historical record.** All prior daily fixes remain intact and reproducible. No data revision required.
- **Methodology version stamp.** This halt is documented under the existing v0.1.3 methodology. The v0.1.4 amendment will reference this errata in its changelog.
- **Pipeline data.** Polled providers continued to land observations throughout 2026-05-27. Basket constituent prices for that date remain queryable for reproducibility purposes outside the daily-fix window.

## Errata commitment

The Volt methodology errata protocol applies to this event. This is the first published errata document for v0.1.3.

— Published 2026-05-27
— Revised 2026-05-27 22:02 UTC
