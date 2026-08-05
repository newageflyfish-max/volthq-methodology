# VIPI v0.1.6 — Errata 001

**Event date:** 2026-06-24
**Affected sub-indices:** vipi, vipi_open (vipi_closed unaffected)
**Severity:** Published composition-artifact values — reclassified to single-day halt
**Status:** Corrected under §12.1; methodology rule added in v0.1.6

## What happened

The daily fix for 2026-06-24 published eight VIPI and VIPI-Open values (`halted = false`, methodology `vipi-0.1.5`) that were composition artifacts rather than price observations. The values published were:

| Sub-index | Value kind | Published value (prior) |
|---|---|---|
| vipi | blended | 46.19201466199683 |
| vipi | input | 40.38357508952707 |
| vipi | output | 57.86636154911405 |
| vipi | best_blended | 46.51813153042409 |
| vipi_open | blended | 39.69217664350616 |
| vipi_open | input | 34.14672120874229 |
| vipi_open | output | 51.42928612577805 |
| vipi_open | best_blended | 38.63184079601989 |

The adjacent closes on 2026-06-23 and 2026-06-25 are identical to each other — VIPI blended 100.61440480082791, VIPI-Open blended 100.57205371459115 — so the 2026-06-24 values register as an isolated ~54% single-day drop and recovery with no corresponding price movement. `vipi_closed` published normally on 2026-06-24 (all four value kinds at or ≈ 100.00, constituent count 6): its constituents retained coverage.

## Detection

The condition was identified on 2026-06-29 during administrator review, five trading days after the event. No automated alert fired at publication time. This five-day detection latency is disclosed here; the monitoring gap is addressed in the "tape-health" note below. The mis-stated values remained on the published tape from 2026-06-24 until reclassified on 2026-07-22 — a 28-day exposure window disclosed on the face of this errata.

## Root cause

The Together provider feed produced zero clean MOC-window observations on 2026-06-24 (15:55–16:05 UTC). Two constituents — `vcmi:llama-3.1-405b` and `vcmi:llama-3.1-70b` (the nvidia/nemotron mapping) — had zero clean observations from **all** of their included mappings that day; six further surviving constituents lost Together as a medium-confidence fallback mapping.

vipi-cron's default arithmetic excluded the null-close constituents from the numerator while retaining the 1/N constituent weights and carrying the prior 19-constituent divisor forward. The result was **17 contributing constituents summed against a 19-constituent divisor** — a composition change registering as a price move.

The day-one transient zero-coverage case was unspecified in the v0.1.5 methodology. §7.2's escalation procedure covers losses persisting for ≥ 5 consecutive trading days, and §9.3's fourth emergency-removal condition requires a structural classification of the loss; neither addressed the first day of a transient provider outage. In the absence of a specified rule, the default numerator/divisor arithmetic produced a published value.

## Considered and rejected: re-anchor-on-survivors correction

A correction that re-anchors the divisor on the 17 surviving constituents at the 2026-06-23 close was computed and **rejected** by the Administrator. It yields VIPI blended **77.29619426028977** — a residual −23% step relative to the 2026-06-23 close.

That residual step is itself a pure mapping-composition artifact, not price movement:

- All DeepInfra survivor prices were **bit-identical** from 2026-06-23 to 2026-06-24.
- The re-anchored `best_blended` corrected values equal the 2026-06-23 closes **exactly**, which proves no price movement occurred on 2026-06-24.

Publishing the re-anchored series would therefore let a composition change register as price movement — the precise outcome §8.1's divisor mechanism exists to prevent. There is no defensible reconstructed value to publish for 2026-06-24.

## Resolution

1. **Methodology rule (v0.1.6, effective 2026-07-22):** §7.2 adds a day-one publication-halt rule for zero coverage. If one or more constituents of a sub-index record zero clean MOC-window observations across all included mappings on a trading day, that sub-index is halted for the day (no value computed or published), independently of and below the §7.4 impairment thresholds, with the five-day escalation clock running concurrently from day one.
2. **Retroactive reclassification (§12.1.2):** 2026-06-24 is reclassified as a publication halt for vipi and vipi_open under the new rule, retroactively, with the retroactivity explicitly disclosed. The eight daily rows are set to halted (`value` NULL, `divisor` NULL, `constituent_count` 0, `halted` true, `halt_reason` `provider_outage_zero_coverage`, methodology `vipi-0.1.6`).
3. **Prior values preserved:** the eight prior published values are retained in `vipi_corrections` under `reason_class = calculation_error`, so the mis-stated series remains fully auditable.

This reclassification is distinct from the 2026-06-05 event (§12.1.1), where the published values were the correct output of the then-specified calculation and a retroactive halt would have departed from the published rules. Here the values were mis-stated composition artifacts that were never a valid index observation, and the correction replaces them with the halt that the now-published v0.1.6 rule specifies.

## Monitoring — tape-health note

A constituent-coverage alert — the v0.1.4 monitoring control that fires when any VIPI constituent records zero clean MOC-window observations on a trading day — exists in code at `packages/workers/tape-health`, committed 2026-06-10. Whether that control was deployed and delivered a notification on 2026-06-24 is under investigation at the time of this errata. No determination is stated here beyond that the condition was not acted upon until the 2026-06-29 administrator review.

## No backfill

No values are reconstructed for 2026-06-24. Consistent with the no-backfill principle, the day is disclosed as uncomputable-as-specified: the surviving-constituent series is a composition artifact, and no defensible price-only value exists. Coverage recovered on 2026-06-25 and published values self-restored from that date.

## Errata commitment

The Volt methodology errata protocol applies to this event. This is the first published errata document for v0.1.6.

— Published 2026-07-22
