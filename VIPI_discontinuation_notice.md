# VIPI — Notice of Discontinuation

**Effective:** August 5, 2026
**Applies to:** VIPI, VIPI-Open, VIPI-Closed
**Final published fix:** 2026-08-05
**Methodology in force at discontinuation:** v0.1.6 (effective 2026-07-22)
**License:** CC-BY-4.0

---

## Notice

The Administrator has discontinued publication of the Volt Inference Price Index (VIPI) and its sub-indices, VIPI-Open and VIPI-Closed, effective August 5, 2026. No further daily fixes will be computed or published. The final published fix is dated 2026-08-05.

This is a permanent cessation of publication, not a suspension or a halt under Section 7.4. Halts, as defined in the Methodology, are single-day non-publications within an ongoing series; this notice ends the series.

## Status of the published record

The historical series remains published and unchanged. Specifically:

- **The full daily series** from the base date of May 18, 2026 through the final fix of 2026-08-05 remains available at the published feed endpoints, including all halted days.
- **All audit records** remain available per date, including per-constituent mapping and observation detail.
- **The correction record** remains complete. Both correction events — the 2026-06-05 constituent-dropout correction (Section 12.1.1) and the 2026-06-24 zero-coverage reclassification (Section 12.1.2) — remain published with prior values preserved, reasoning disclosed, and detection latency stated.
- **The Methodology**, in all versions from v0.1.3 through v0.1.6, together with the Chain of Custody, Curation Policy, Pre-Registration, Reproducibility Appendix, and errata documents, remains public in the methodology mirror under CC-BY-4.0.

No values are withdrawn, revised, or removed as a consequence of discontinuation. Nothing in the historical record is reconstructed, backfilled, or altered. The no-backfill principle that governed the series in operation governs it in discontinuation.

## Reason for discontinuation

The Administrator is a single individual operating the index without external funding. Continued publication requires ongoing operational attention that the Administrator is no longer able to commit at the standard the Methodology requires. Rather than allow the series to degrade — the failure mode in which values continue to publish without adequate supervision, and errors persist undetected — publication is ceased deliberately and on the record.

The 2026-06-24 event is instructive on this point and is disclosed accordingly: a zero-coverage condition produced mis-stated values that remained published for 28 days before correction. A benchmark whose administrator cannot guarantee timely detection should not continue to publish. That judgment is the proximate reason for this notice.

## Scope of the index at discontinuation

At the final fix, the index comprised a 19-constituent basket priced across a panel of 8 inference providers through 73 active provider mappings, with three published sub-indices and four value kinds each. The base date was May 18, 2026, 16:00 UTC, base value 100.00. All inputs were observed posted prices; no transacted-price data was used at any point, and the Methodology never claimed otherwise.

## Known limitations, stated for the record

Any future user of this dataset should understand the following, which the Administrator regards as material:

1. **Posted prices, not transactions.** VIPI measured administered list prices published by providers. It did not measure prices actually paid. Posted per-token prices proved to be substantially static — non-Akash observations were bit-identical day-over-day in the overwhelming majority of cases — and a series with this property is not suitable as a settlement reference for financial instruments. This limitation is structural, not a defect of implementation.

2. **Provider-outage sensitivity.** The index was sensitive to single-provider observability gaps, as the 2026-06-24 event demonstrates. The v0.1.6 halt rule addresses this prospectively but the series contains one such event, disclosed.

3. **Short history.** The series covers approximately eleven weeks. It is too short to support seasonality, volatility, or trend claims of any strength.

4. **Single-administrator governance.** Every governance function — methodology, computation, correction, and disclosure — was performed by one individual. There was no independent oversight committee, no external audit, and no segregation of duties, notwithstanding the IOSCO-aligned design.

## Citation and reuse

The dataset and Methodology remain available for citation and reuse under CC-BY-4.0. Cite the version in force at the date of the data used. Users should cite VIPI as a discontinued series and reference this notice.

The Administrator makes no representation that the series will be resumed, and undertakes no obligation to maintain, update, or support it. Should publication ever resume, resumption would be disclosed as a new period with the publication gap stated on its face; the historical series would not be retroactively extended across the gap.

---

*Volt HQ — Administrator, Volt Inference Price Index*
*Published 2026-08-05*
