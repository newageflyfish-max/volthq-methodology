# Volt Inference Price Index (VIPI) — Methodology

**Version:** 0.1.3 (public draft)
**License:** CC-BY-4.0
**Administrator:** Volt HQ (volthq.dev)
**Status:** Public draft. Methodology open for external review and feedback. v1.0 targeted as canonical reference release.
**Base date:** May 18, 2026, 16:00:00 UTC
**Base value:** 100.00
**Dependency:** Volt Canonical Model Identifier (VCMI) v0.1.3 (or any later patch in the v0.1.x series)
**Last updated:** April 21, 2026
**IOSCO status:** Designed to adhere to IOSCO Principles for Financial Benchmarks (2013). Formal statement of adherence planned for v1.0.

---

## 0. Version History

| Version | Effective | Summary |
|---|---|---|
| 0.1   | April 2026 | Initial private draft. Three-index family (VIPI, VIPI-Open, VIPI-Closed), 3:1 input/output blended weighting, 16:00 UTC daily MOC, equal-weighted with monthly rebalancing, IOSCO-aligned governance roadmap. |
| 0.1.1 | April 20, 2026 | Adopted four of five round-1 blocking fixes: realigned 3:1 input/output framing with the underlying formula, introduced the divisor method to handle constituent additions via divisor adjustment (resolving the missing-chaining-rule problem), eliminated the backfilled-history look-ahead bias by removing the backfill period entirely, and moved the base date from March 23 to April 7, 2026 so the 14-day continuity criterion and 7-day trailing-median outlier rule are satisfied from day 1. |
| 0.1.2 | April 20, 2026 | Fixed emergency-removal weight handling: previously a mid-cycle removal left the slot empty with reduced N and unchanged 1/N weights (dampening the index); v0.1.2 re-weights remaining constituents to 1/(N−1) on the effective day with a divisor adjustment, returning to 1/N at the next scheduled rebalancing — aligned with S&P 500 Equal Weight delisting practice. Also pinned the VCMI cross-reference to v0.1.2 Section 10, corrected two internal section pointers, and removed the "corrected" qualifier from the §13 preamble. |
| 0.1.3 | April 21, 2026 | Refinements adopted from external review: Artificial Analysis attribution clarified for the 3:1 weighting; outlier-rule promotional-pricing caveat added; halt-trigger thresholds disclosed as initial values pending 90-day review; new-family ineligibility window disclosed; standard-tier non-cached non-batched pricing scope explicitly stated; sample-calculation rationale clarified (two-stage median preferred over pooled). VCMI dependency bumped to v0.1.3 (forward-compatible). No retroactive changes to constituent inclusion or calculation rules. |

Future versions and substantive methodology changes between versions are governed by Section 12.3.

---

## 1. Purpose and Scope

The Volt Inference Price Index (VIPI) is a daily price index for AI inference. It measures the per-million-token cost of running language-model inference across the commercially available market, as observed across a defined universe of inference providers. VIPI is published for the following purposes:

- **Reference rate.** A citable number suitable for inclusion in contracts, procurement benchmarks, research citations, and regulatory disclosure.
- **Market observation.** A time series from which market structure, pricing trends, provider behavior, and adoption dynamics can be measured.
- **Hedging and settlement anchor.** A foundation on which future inference-cost hedging instruments may eventually settle.

VIPI is NOT:

- A proxy for any individual user's inference bill (user costs depend on usage mix, caching, batching, negotiated discounts).
- A prediction of future prices.
- A recommendation to use any particular provider.
- An investment instrument.

VIPI v0.1 covers the centralized and decentralized inference-provider market as observed by Volt HQ's continuous pricing pipeline. Reasoning-model-specific indices and quality-adjusted indices are out of scope for v0.1 and addressed in Section 14.

---

## 2. Relationship to VCMI

VIPI is computed entirely over the VCMI v0.1.3 (or any later patch in the v0.1.x series) canonical identity layer. Without canonical identity, cross-provider price comparison is not possible; with it, VIPI inherits the rigor of VCMI's weight-identity rules, confidence levels, and pass-through resale handling. Adoption of VCMI v0.2 or later requires a VIPI methodology revision under Section 12.3 and is not automatic.

Specifically:

- Every VIPI constituent is a single VCMI entry with `status = active`.
- A constituent's price on a given day is derived from `provider_mappings` with `confidence = high` (see Section 7.2 for fallback rules).
- VCMI's Principle 6 (identity follows weights, not branding) ensures that provider brand suffixes cannot be gamed to create spurious constituents.
- VCMI's `gpu_type` and `serving_tier` fields on `provider_mappings` ensure that multi-price configurations are correctly differentiated.

Any change to VCMI that affects VIPI constituent identity is treated as a methodology event under Section 12 (Corrections and Halts) and disclosed in VIPI's changelog.

---

## 3. Design Principles

VIPI follows seven operating principles, in order of priority:

1. **Integrity over responsiveness.** Methodology choices that preserve the benchmark's long-run credibility take precedence over choices that make the benchmark feel fresh or reactive in the short term.

2. **Observed prices only.** VIPI uses prices that providers have published via their official APIs or pricing pages. No estimates, no submissions, no survey data, no projections. This is the single most important structural difference from LIBOR: inputs are observed commercial prices rather than subjective submissions. However, the v0.1 provider panel (typically 2–4 mappings per constituent) is small by mature benchmark standards. A single provider's price change can shift a constituent's median; a coordinated pair among three providers could dominate it. VIPI's structural resistance to manipulation strengthens as provider coverage grows. Users evaluating VIPI's suitability as a settlement reference should account for the current panel size.

3. **Rules over judgment.** VIPI's monthly rebalancing and daily calculation are governed by published rules that a reader with access to the raw feed can reproduce independently. Expert judgment is invoked only at explicit, documented decision points (methodology revisions, retractions, halts) and always with reasoning disclosed.

4. **Transparency over competitive advantage.** Every input, rule, and calculation step is publicly disclosed. The methodology is a public good; Volt's commercial advantage derives from data collection, historical depth, and curation quality — not from withheld methodology.

5. **Conservative change management.** Minor corrections are routine. Material methodology changes require documented rationale, consultation where practical, minimum notice periods, and continuity provisions. Historical VIPI values are never retroactively revised except to correct data errors.

6. **No conflicts of interest.** Volt HQ holds no equity, debt, token, or revenue share in any provider whose prices contribute to VIPI. Any prospective arrangement that would create such an interest must be disclosed and the provider potentially excluded before the arrangement is established. This is a permanent structural rule, not a policy subject to change.

7. **Hierarchy of evidentiary inputs is disclosed per-determination.** Every published VIPI value is accompanied by a daily audit record (§10.4) that classifies each contributing provider mapping by evidentiary sub-class (api_inline_price, api_catalog_gated, static_by_design, gpu_hour_derived) and reports the aggregate publication mix across the three IOSCO-aligned input tiers (transaction-based, spread-based, interpolated/extrapolated). This makes the input hierarchy a verifiable artifact of each publication, not a methodological assertion.

These principles are the operational translation of IOSCO Principle 1 (governance), Principle 7 (data sufficiency), Principle 8 (hierarchy of inputs), Principle 9 (transparency of methodology), and Principle 13 (transition).

---

## 4. The Index Family (v0.1)

VIPI v0.1 publishes three indices. Each shares the same methodology; only the constituent universe differs.

| Index | Short name | Constituent universe |
|---|---|---|
| Volt Inference Price Index | **VIPI** | All VCMIs meeting inclusion criteria (the headline). |
| Volt Inference Price Index — Open | **VIPI-Open** | VCMIs with open-weight licenses (Apache-2.0, MIT, Llama-Community, Qwen-License, Apache 2.0 derivatives). |
| Volt Inference Price Index — Closed | **VIPI-Closed** | VCMIs with proprietary licenses (Anthropic, OpenAI, Google proprietary, Zhipu proprietary, etc.). |

**Why three indices at launch.** The divergence between open-weight and closed-source pricing is a structurally important observation in the inference market. An index family that cannot surface that divergence fails to illuminate the market's most important dynamic. A single headline index would also be competitively weak: at least one existing service (inferencepriceindex.com) already publishes tiered inference indices sourced from aggregator data.

**VIPI-Reasoning** is planned for v0.2 (target: within 60 days of v0.1 public launch). Reasoning models are excluded from v0.1 because they consume substantially more tokens per task, which makes per-million-token comparisons misleading relative to non-reasoning models. Treating them as a separate index preserves per-token interpretability for v0.1.

**Licensing edge case.** A VCMI whose license does not classify cleanly as either "open-weight" or "proprietary" (for example, "Commercial Use Restricted" source-available licenses) may qualify for the headline VIPI but be excluded from both VIPI-Open and VIPI-Closed. In such cases, the headline VIPI includes the constituent and the sub-indices do not. The sub-indices are subsets of, but not necessarily an exhaustive partition of, the headline basket.

---

## 5. Inclusion Criteria

A VCMI is eligible for inclusion in a VIPI sub-index at a monthly rebalancing date if and only if all of the following are true at the rebalancing reference date (the last day of the prior calendar month, 16:00 UTC):

1. **Active status.** The VCMI has `status = active` in the VCMI registry.
2. **Identity confidence.** The VCMI's upstream source URL is verified (`high` confidence on upstream author).
3. **Provider coverage.** Criterion 3 establishes requirements to ensure that each recorded price is verifiably associated with the same underlying model it claims to price, with the specific verification mechanism depending on whether the model is served by multiple providers or solely by its issuer of record.

   **(a) For models served by multiple providers:** the VCMI must include at least two provider mappings with confidence = high or confidence = medium, with at least one mapping required to have confidence = high. This requirement implements cross-provider corroboration by relying on independent provider observations of the same underlying model, where agreement across providers provides evidence that the recorded price is not an artifact of any single provider's data or reporting process, but is consistently associated with the model across sources. Confidence levels (`high`, `medium`, `low`) are defined in VCMI v0.1.3 Section 6. In summary: `high` requires either identical upstream source URL confirmation, byte-level weight verification, direct provider confirmation, or trivial decomposition of the provider string into VCMI components with documented brand-suffix meaning.

   **(b) For sole-issuer proprietary models:** Some proprietary models are offered only by their issuer of record; for these models, the cross-provider corroboration requirement in clause (a) cannot be performed because no independent second provider exists against which to compare pricing. Instead, Criterion 3 is satisfied through a single-provider pathway requiring a verified-identity, high-confidence mapping to the issuer of record and the use of issuer-published pricing for the corresponding model. In this sole-issuer context, issuer-published pricing functions as the canonical reference price by construction, and the framework validates the consistency of published pricing tied to verified model identity, not whether that price is economically fair, efficient, or competitive. This pathway is contingent on identity continuity under Criterion 2, such that the mapped model must remain the same identifiable model over time, preventing silent substitution or rolling re-identification under a stable pricing label.

   This amendment resolves the sole-issuer portion of Open Question 4 by specifying that proprietary models available only from their issuer of record use issuer-published pricing under clause (b), while the multi-reseller case addressed in Open Question 4 remains deferred for v0.2.
4. **Continuity.** The VCMI has been continuously observed in the pricing feed for at least 14 consecutive days ending on the rebalancing reference date. "Continuously" means present in at least 95% of the daily 16:00 UTC close snapshots during the 14-day window.

   The 95% threshold is measured against the number of MOC closes for which the pricing feed produced at least one clean snapshot, rather than against the number of calendar days within the evaluation window. If no snapshot was produced on a given day, that day is excluded from both the numerator and denominator. This includes both days within the window that had not yet elapsed at the time eligibility was evaluated and Administrator-side infrastructure outages. Provider-side absences are treated differently: if the feed operated as expected but a provider failed to return the model, that day is counted as a missed observation in the numerator calculation.

   Any rebalancing in which Administrator-side gaps reduced the denominator must disclose this in the rebalancing announcement, including both the number of affected days and the specific dates involved. Mechanical safeguards preventing denominator erosion resulting from Administrator-side gaps are deferred until v0.1.4.
5. **Non-reasoning.** The VCMI is not a reasoning-specialized model (identified by `variant` containing `reasoning`, or by explicit categorization in the VCMI notes field). Reasoning models are routed to VIPI-Reasoning when that index launches.
6. **Non-opaque pricing.** The VCMI's pricing is expressed in per-input-token and per-output-token terms (not per-GPU-hour, not subscription, not freemium). This excludes `pricePerGpuHour`-only offerings and subscription tiers.
7. **Sub-index specific criteria:**
   - **VIPI:** no additional criteria beyond 1–6.
   - **VIPI-Open:** VCMI's `license` field is one of a published list of open-weight licenses (Apache-2.0, MIT, Llama-*-Community, Qwen-License, Gemma license, or other licenses explicitly classified as open-weight by the maintainer).
   - **VIPI-Closed:** VCMI's `license` field contains `Proprietary-*` or is otherwise classified as proprietary by the maintainer.

**Dual inclusion:** A VCMI that qualifies for VIPI-Open or VIPI-Closed is also in the headline VIPI. The sub-indices are subsets of the headline, not substitutes.

**Ineligible license classifications** (e.g. "Commercial Use Restricted" source-available licenses) are excluded from both VIPI-Open and VIPI-Closed at v0.1 and flagged for v0.2 discussion.

**New-family ineligibility window.** Models from families not yet in the VCMI controlled vocabulary are ineligible until a VCMI vocabulary update is published. The Administrator maintains automated monitoring for new model families at tracked providers; vocabulary extensions require Administrator review per VCMI Principle 6 and are processed as a priority matter.

**Retention bias (S&P 500-style rule):** A constituent that marginally violates one or more inclusion criteria at a rebalancing date is NOT automatically removed. The maintainer retains discretion to leave a constituent in place for one additional rebalancing cycle if the violation is transient (e.g. a provider temporarily delisting a model that remains available elsewhere). This discretion is exercised consistent with the published retention criteria in Section 9.2 and is always disclosed in the rebalancing announcement.

---

## 6. Constituent Cap and Weighting

Each sub-index is capped at **N = 20 constituents** at v0.1. Caps exist to prevent the index from being dominated by a single family (e.g. 30 variants of Llama 3.3 70B) and to keep the constituent list tractable for manual curation during the private phase.

**Cap application:** When more than 20 VCMIs are eligible, the maintainer selects constituents using the following tie-breaker cascade:
1. Higher total provider coverage (more `provider_mappings`).
2. Higher `confidence = high` mapping count.
3. More distinct upstream families represented (diversification: prefer one Llama + one Qwen over two Llamas).
4. Older first-seen date in the VCMI registry (established models preferred over newly added ones).
5. Alphabetical VCMI order as final tie-breaker.

Diversification tie-breaker (#3) is equivalent in spirit to BCOM's diversification rules, which cap single-commodity concentration to preserve benchmark representativeness.

**Weighting.** All constituents are **equally weighted** in each sub-index. At the monthly rebalancing, each constituent receives weight 1/N where N is the number of constituents included.

Rationale for equal weighting at launch:
- No defensible alternative weighting exists in this market. Usage data (analogous to liquidity in BCOM or market cap in S&P 500) is not publicly available per provider per model.
- Equal weighting is transparent, reproducible, and reduces the maintainer's discretion.
- Equal-weighted variants of major indices exist (S&P 500 Equal Weight) as precedent.
- A usage-weighted variant is flagged for v0.2 research once estimation methodology is defensible.

**Single-provider weight cap (forward-looking).** No single provider's mappings shall contribute more than 30% of the aggregate observation-count-weighted basket within any sub-index on a given day. This forward-looking commitment is set at 30% in line with the literature on benchmark concentration risk and will be implemented mechanically in a future minor revision (v0.1.4 or later) following a Session 5 dry-run boundary check against the §7.4 source-side halt trigger. Until mechanical implementation, the cap is disclosed as methodology intent and monitored manually by aggregating mapping-level observation counts in the daily audit record (§10.4).

---

## 7. Daily Price Determination

### 7.1. Assessment Window (Market-on-Close)

VIPI is calculated once daily using a **Market-on-Close (MOC) assessment window** ending at 16:00:00 UTC. The window runs from 15:55:00 UTC to 16:04:59 UTC — a 10-minute interval centered on the close.

Rationale for 16:00 UTC:
- After Asian market close (Tokyo, Shanghai, Singapore).
- During European end-of-day (London at 17:00, Frankfurt at 18:00).
- Before US market close (NYSE at 21:00 UTC during EST).
- Well-established convention for global reference rates serving multiple time zones (cf. WM/Reuters FX benchmark at 16:00 UTC London).

Rationale for 10-minute median window (vs single-snapshot close):
- The 10-minute window is sized to ensure multiple independent observations enter the median calculation under Volt's pricing-feed cadence, providing robustness against single-snapshot data-pipeline errors and ensuring no individual observation determines the close in isolation.
- Mitigates the LIBOR-era criticism that single-point benchmarks with narrow windows and subjective timing ("just prior to 11am") enable manipulation.
- Median (rather than mean) is robust to single-snapshot outliers caused by provider API glitches.

**Definition of trading day.** For VIPI, every calendar day is a trading day. Inference providers operate continuously (24/7/365); there are no market holidays, weekend closures, or exchange-observance adjustments. The MOC window is computed every calendar day at 16:00:00 UTC regardless of the day of the week.

### 7.2. Per-Constituent Price

For each constituent VCMI on each trading day:

**Step 1 — Collect all provider mappings.** Retrieve all `provider_mappings` with `confidence = high` for this VCMI. If fewer than two `high`-confidence mappings exist, fall back to including `confidence = medium` mappings for price determination (but note the degradation in the daily calculation audit trail).

**Sole-issuer constituents.** A constituent included under §5(3)(b) (sole-issuer proprietary models) will, by construction, have exactly one high-confidence provider mapping, reflecting the fact that no independent secondary provider exists for that model. In this case, Step 1 does not enter a degraded or fallback state; instead, the single available high-confidence mapping constitutes the complete input set for price determination.

The median operation applied to a one-element set returns that element directly, and this behavior is intended under §5(3)(b) rather than representing a reduction in data quality or provider coverage.

This sole-issuer handling is distinct from the fallback path described in Step 1 for cases with fewer than two high-confidence mappings, where medium-confidence mappings are introduced and a degradation is explicitly recorded in the audit trail. The sole-issuer case does not trigger degradation labeling, as it reflects structural uniqueness of the model's provider landscape rather than incomplete or missing provider coverage.

**Escalation for sustained fallback.** If a constituent operates on medium-confidence-only pricing (zero high-confidence mappings with observations in the MOC window) for more than 5 consecutive trading days, the Administrator evaluates whether the high-confidence mapping loss is transient (e.g., provider API outage) or structural (e.g., provider delisted the model). If structural, the constituent is flagged for potential emergency removal under Section 9.3. The evaluation is disclosed in the daily audit record on the day the escalation criterion is met and on each subsequent day the constituent remains in medium-only status.

**Step 2 — Collect prices in the MOC window.** For each included mapping, retrieve all `offering_prices` rows with `quality_flag = 'clean'` where `timestamp >= 15:55:00 UTC` and `timestamp < 16:05:00 UTC` on the trading day. Note: VIPI uses each provider's published standard-tier, non-cached, non-batched pricing. Effective prices paid by users may differ due to caching discounts, batch-API pricing, volume commitments, and negotiated enterprise rates. See Section 15 for the rationale for this choice.

**Step 3 — Compute per-mapping close price.** For each mapping, the close price is the median of its `priceInputPerMillion` (and separately, `priceOutputPerMillion`) values observed in the window. If only one observation is present, that value is used. If zero observations are present for a mapping in the window, that mapping is excluded from this day's calculation.

**Observation-count floor.** For each included mapping, the MOC window must contain at least K = 2 clean observations (after Step 2's `quality_flag = 'clean'` filter and Section 7.3's outlier exclusion). If fewer than 2 clean observations remain for a mapping, that mapping is excluded from this day's calculation and the exclusion is logged in the daily audit record alongside the outlier_exclusions field. The K = 2 floor is the maximum achievable under VIPI's current 5-minute snapshot cadence and 10-minute MOC window (15:55:00 UTC – 16:04:59 UTC), which admits at most two cron firings per window. The floor is set deliberately low to maximize basket retention at v0.1; a higher floor (K ≥ 3) requires either a wider MOC window or a tighter cron cadence and is scheduled for review at v0.2 after 30 days of live publication.

**Step 4 — Compute per-constituent close price.** The constituent's daily close price is the **median across all included provider mappings**, computed separately for input and output. Formally:

- `input_price_{c,d}` = median over mappings of `input_price_{mapping,d}`
- `output_price_{c,d}` = median over mappings of `output_price_{mapping,d}`
- `blended_price_{c,d}` = (3 × `input_price_{c,d}` + `output_price_{c,d}`) / 4

The 3:1 input-to-output weighting reflects the Artificial Analysis published convention, which assumes a 3:1 ratio of input to output tokens ("we calculate a blended price assuming a 3:1 ratio of input to output tokens", artificialanalysis.ai/methodology). This convention is also used by Epoch AI's LLM price trend analysis. Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references. The two-stage median (within-provider, then across-provider) is chosen over a pooled single-stage median to prevent providers with more frequent snapshot observations from having outsized influence on the constituent price.

### 7.3. Outlier Exclusion

Within Step 2's observation set, any individual snapshot price that exceeds 3× the 7-day trailing median for that mapping, or is below 1/3 of the 7-day trailing median, is flagged as an outlier and excluded from the median computation. The 3× and 1/3 thresholds are initial values chosen as conservatively permissive starting points. They will be reviewed against observed data at the first scheduled methodology review. A tighter threshold (e.g., 2× / 1/2) may be appropriate once sufficient price-change events are observed to calibrate empirically. This rule implements the IOSCO-endorsed practice of "outlier elimination based on time-series information" and protects against single-snapshot data-pipeline errors. Excluded outliers are logged in the daily audit record but do not affect published VIPI values.

The outlier rule is designed to filter transient data-pipeline errors, not sustained promotional pricing. If a provider offers sustained loss-leader pricing that is below commercial sustainability thresholds, the Administrator may classify this as an integrity concern under Section 9.3 and exclude the mapping. The Administrator monitors for price anomalies concentrated in the MOC window period. If a provider's pricing shows patterns consistent with window-dressing (prices that diverge from non-window observations), the Administrator may reclassify the provider's confidence level or exclude the mapping under Section 9.3.

### 7.4. Insufficient-Data Halt

If, on any trading day:

- More than 30% of a sub-index's constituents have zero valid observations in the MOC window, OR
- More than 3 constituents of any sub-index are in outlier-excluded state simultaneously,

then that sub-index is **not published** for that day. The 30% and 3-constituent thresholds are initial values; the Administrator will review them against observed halt-trigger frequency after 90 days of live publication. The missing day is later backfilled only if the underlying data issue is resolved and the audit trail supports a reconstructed calculation; otherwise the day remains a gap. Halts are disclosed immediately in the VIPI publication channel. This rule ensures VIPI is never published with known-degraded inputs.

*BMR correspondence: this section addresses the substance of BMR Article 27(2)(e) (procedures to be followed during periods of market stress or in circumstances where transaction data sources become insufficient or unrepresentative) for data-availability stress. See §15 for the formal-labelling deferral notice.*

---

## 8. Index Calculation

### 8.1. Calculation Formula

For each sub-index I and trading day d, the index value is:

```
VIPI_I(d) = (Σ_{c in constituents} w_c × blended_price_{c,d}) / D_I(d)
```

Where:
- `w_c` is the constituent weight (1/N at v0.1, where N is the number of constituents in sub-index I)
- `blended_price_{c,d}` is the day-d blended price for constituent c (Section 7.2 Step 4)
- `D_I(d)` is the divisor for sub-index I on day d
- The sum runs over all constituents of sub-index I

The divisor `D_I` is initialized at the base date such that `VIPI_I(base) = 100`. Between constituent changes, the divisor remains constant; the index moves only with prices. On every constituent change (scheduled rebalancing per Section 9.2 or emergency removal per Section 9.3), the divisor is adjusted to preserve index continuity across the change.

Specifically, let `VIPI_I(t)_old` be the value just prior to the change, computed with the prior basket and divisor `D_I_old`. The new divisor `D_I_new` is set such that `VIPI_I(t)_new = VIPI_I(t)_old` when evaluated with the new basket at the same prices:

```
D_I_new = (Σ_{c in new basket} w_c_new × blended_price_{c,t}) / VIPI_I(t)_old
```

This is the standard divisor-adjustment method used by the S&P 500, Dow Jones Industrial Average, S&P 500 Equal Weight, and most major equity benchmarks. It ensures continuity across composition changes without re-weighting existing constituents or altering historical values.

**Chaining rule for newly added constituents.** When a new constituent c' joins the basket at time t, its `blended_price_{c',t-1}` is not defined because c' was not in the basket on day t-1. The divisor adjustment above handles this automatically: the adjustment incorporates c' at its day-t price with weight `w_{c'}_new`, and the index is continuous from the prior basket to the new basket at identical value. No reference to a prior-day price for the new constituent is needed in the calculation.

### 8.2. Base Value

The twelve-series anchor structure establishes a common reference point across the full VIPI family, allowing direct comparison between blended, input-only, output-only, and best-available variants within and across the three sub-indices (VIPI, VIPI-Open, VIPI-Closed). Each series maintains an independent divisor under §8.1's mechanics while sharing the same base date and normalized starting value, ensuring that relative divergence between series reflects differences in observed pricing behavior rather than differences in initialization scale. This expansion supersedes the earlier three-anchor structure from commit c520214, which initialized only the headline blended series for each sub-index.

**Base date and time.** May 18, 2026, 16:00:00 UTC. All twelve published VIPI series share this single base date and time, enabling direct relative comparison across the entire family.

**Base values.** Each of the twelve published series (four value_kinds per §8.4, across the three sub-indices) is anchored to 100.00 at the base date:

- VIPI: 100.00
- VIPI-Input: 100.00
- VIPI-Output: 100.00
- VIPI-Best: 100.00
- VIPI-Open: 100.00
- VIPI-Open-Input: 100.00
- VIPI-Open-Output: 100.00
- VIPI-Open-Best: 100.00
- VIPI-Closed: 100.00
- VIPI-Closed-Input: 100.00
- VIPI-Closed-Output: 100.00
- VIPI-Closed-Best: 100.00

**Divisor initialization.** §8.1's formula extends naturally to each (sub_index, value_kind) pair. For each such pair, the divisor is initialized at the base date such that the series equals 100.00:

```
D_{I,k}(base) = (Σ_{c in constituents} w_c × p_{c,k,base}) / 100
```

Where:

- `I` ranges over the three sub-indices: `vipi`, `vipi_open`, `vipi_closed`
- `k` ranges over the four published value_kinds per §8.4: `blended`, `input`, `output`, `best`
- `w_c` is the constituent weight (1/N per §6, where N is the number of constituents in sub-index `I`)
- `p_{c,k,base}` is the per-constituent base-date price for value_kind `k`:
  - `blended`: blended_price per §7.2 Step 4 (3:1 input/output weighted)
  - `input`: input_price per §7.2 Step 3 (median across mappings, input-token prices)
  - `output`: output_price per §7.2 Step 3 (median across mappings, output-token prices)
  - `best`: cheapest-provider blended_price per the §8.4 VIPI-Best definition (clarified below)

Twelve divisors total. Each is persisted to the `vipi_daily.divisor` column on the base-date row for its (date, sub_index, value_kind) tuple.

**Application of §8.1's constancy and rebalancing-continuity rules.** The rules stated in §8.1 — that the divisor remains constant between constituent changes, and that the divisor adjusts on every constituent change to preserve continuity — apply independently to each of the twelve (sub_index, value_kind) divisors. A rebalancing or emergency removal triggers twelve simultaneous divisor adjustments, one per series, each computed against its own series' prior value per §8.1's continuity formula.

The base date is selected such that all inaugural constituents have at least 14 calendar days of continuous observation history (per Section 5, criterion 4) on the base date and the outlier-exclusion rule (Section 7.3) has at least 7 days of trailing-median data from day one of publication. Volt's continuous pricing pipeline began on March 23, 2026; the base date is set with sufficient margin beyond that to satisfy both lookback requirements with operational headroom. All three sub-indices share the same base date to enable direct relative comparison across the family.

The shared 100.00 anchor across all published series prioritizes interpretability of relative movement and spread relationships over time, particularly between median-based and best-available pricing views. Maintaining a uniform initialization framework across the family also ensures that subsequent divisor adjustments under §8.1 preserve continuity independently for each series without impairing cross-series readability.

### 8.3. Inception and Historical Record

VIPI v0.1 has no backfilled historical period. Publication begins on the base date (May 18, 2026) using the live constituent set selected per Section 5 inclusion criteria as evaluated at the base date.

The period from March 23, 2026 (first continuous pricing snapshot) to May 17, 2026 (day prior to base date) is termed the **bootstrap window**. Data collected during the bootstrap window is used exclusively for computing inclusion criteria (the 14-day continuity requirement in Section 5, criterion 4) and for initializing the 7-day trailing median required by the outlier-exclusion rule (Section 7.3). No VIPI values are published for the bootstrap window. This design eliminates look-ahead bias: VIPI's published series reflects only the constituent decisions that could have been made with information available at each date.

Published VIPI values are treated identically to live values for correction purposes (Section 12). Historical values are not recomputed under revised methodology versions except to correct errors in the input data.

### 8.4. Published Values

Each trading day, three values are published per sub-index:

- **Blended VIPI** (3:1 input-to-output weighted; the headline).
- **VIPI-Input** (input-token-only variant; equivalent calculation using input prices only).
- **VIPI-Output** (output-token-only variant; equivalent calculation using output prices only).

Plus a secondary metric per sub-index:

- **VIPI-Best** — computed identically to VIPI but using the **cheapest provider per constituent** rather than the median. Useful as a "best-available" benchmark analogous to Platts' "most competitive grade" concept. Published alongside but not considered the headline number.

**VIPI-Best provider selection (Day 75 clarification).** For each constituent on each observation day, the "cheapest provider" is determined by the lowest blended price for that constituent across all included provider mappings, where blended price is computed per §7.2 Step 4 (3:1 input-to-output weighted). The provider so identified contributes both its input price and its output price to the constituent's VIPI-Best calculation; provider selection is performed once per (constituent, day) tuple, not separately for input and output dimensions. This rule is defined at the constituent level to preserve methodological coherence with §8.4's "cheapest provider per constituent" formulation and to ensure that each constituent contribution corresponds to a real executable sourcing venue on the observation day. Selecting providers independently for input and output dimensions would instead construct a synthetic cross-provider composite that does not correspond to an actual sourcing path available on the observation day and is therefore not used in the VIPI-Best methodology.

---

## 9. Rebalancing

### 9.1. Schedule

VIPI rebalances **monthly**. The rebalancing reference date is the last calendar day of each month at 16:00 UTC. The rebalanced constituent set becomes effective on the **third trading day of the following month** at 16:00 UTC, creating a transparent two-trading-day implementation window during which the prior and new constituent sets are both published for comparison.

### 9.2. Rebalancing Procedure

At each rebalancing reference date:

1. **Eligibility sweep.** All active VCMIs are evaluated against Section 5 inclusion criteria as of the reference date.
2. **Cap application.** If more than 20 VCMIs qualify for a sub-index, the Section 6 tie-breaker cascade is applied to select the final 20.
3. **Comparison against prior constituents.** The new constituent set is compared against the prior set. Additions, removals, and continued inclusions are enumerated.
4. **Retention-bias review (S&P 500 principle).** Any prior constituent that marginally violates inclusion criteria is reviewed against retention criteria:
   - Is the violation transient (e.g. one-week API outage) or structural (e.g. model delisted across all providers for >30 days)?
   - Transient violations: constituent retained for one additional cycle with a note.
   - Structural violations: constituent removed.
5. **Announcement.** The new constituent set, the changes, and the retention-bias reasoning are published no later than the second trading day of the new month. No constituent changes are made outside the announced rebalancing, except for emergency removals under Section 12.
6. **Implementation.** On the third trading day at 16:00 UTC, the new constituent set becomes effective for VIPI calculation. The prior set is used for all days up to and including the second trading day.

### 9.3. Emergency Rebalancing

Between scheduled rebalancings, constituents may be removed — but not added — only under these conditions:

- The constituent's VCMI is retracted (Section 10 of VCMI v0.1.3 spec) due to discovering the underlying model does not exist or was a phantom listing.
- The constituent's VCMI is formally deprecated by the upstream author with no continuing provider coverage.
- An active integrity concern (e.g. evidence of pricing-page manipulation at a provider) renders the price data unreliable.

Emergency removals are disclosed with rationale and take effect at the next trading day's close. On removal, the remaining constituents are re-weighted equally to 1/(N−1) each (where N is the pre-removal constituent count), and the divisor is adjusted per Section 8.1 to preserve index continuity at the removal close. The vacated slot is filled at the next scheduled rebalancing per Section 9.2, at which point all constituents return to 1/N weights with a further divisor adjustment. This procedure follows standard index-maintenance practice for equal-weight benchmarks (e.g. S&P 500 Equal Weight delisting handling) and ensures the index continues to capture 100% of market movement between emergency events and scheduled rebalancings.

---

## 10. Publication

### 10.1. Publication Schedule

VIPI values for trading day d are published no later than 18:00:00 UTC on day d (two hours after the close). Each publication includes:

- Three indices × three values (blended/input/output) = nine core numbers
- Three VIPI-Best secondary numbers
- Daily audit record: constituents included, mappings used per constituent, MOC-window observation counts, outlier exclusions
- Data integrity flags and any halt notices

### 10.2. Publication Channels

Primary: `volthq.dev/vipi` (human-readable) and `volthq.dev/vipi/api/v1/daily.json` (machine-readable, JSON).

Secondary: daily disclosure in the Touch Grass Capital Substack publication. Commentary accompanying VIPI movements when material.

### 10.3. Citation Format

The recommended citation format is:

> Volt HQ (2026). *Volt Inference Price Index, day d.* Retrieved from volthq.dev/vipi.

And for academic use:

> Arnot, J. (2026). *The Volt Inference Price Index: a methodology for benchmark construction in the AI inference market.* Volt HQ. Retrieved [retrieval-date]. volthq.dev/vipi/methodology.

### 10.4. Audit Record Schema

Each published VIPI value is accompanied by a daily audit record persisted to the `vipi_audit_records` D1 table (one row per (date, sub_index) pair). The canonical schema for the `audit_json` JSON blob is specified in this section; implementation plan §3.6 references this section as the binding contract and documents the operational details required for `audit.ts` to emit conformant records. The schema is locked at v0.1.3 with the explicit additive-fields license: future minor revisions may add top-level keys without contract break.

**Locked v0.1.3 keys (4 required + 1 conditional):**

- `constituents` (array of VCMI identifiers): every constituent VCMI included in the day's calculation for the given sub_index.
- `mappings_used_per_constituent` (object: vcmi_id → array of provider_ids): for each constituent, the list of provider adapters whose mappings contributed observations.
- `moc_window_observation_counts` (object: vcmi_id → object: provider_id → integer): per-constituent, per-provider count of clean observations inside the 10-minute MOC window.
- `outlier_exclusions` (array of objects with fields `vcmi`, `provider_id`, `snapshot_id`, `price`, `reason`): observations excluded by §7.3 outlier rule.
- `confidence_fallback_constituents` (array of VCMI identifiers; conditional): present only when the §7.2 Step 1 medium-confidence fallback is triggered for any constituent on the given day.

**v0.1.3 additive keys (3 new, per the additive-fields license):**

- `evidentiary_sub_class_per_mapping` (object: mapping_id → enum): classifies each included mapping into one of `api_inline_price`, `api_catalog_gated`, `static_by_design`, or `gpu_hour_derived`. Disclosure-only; this classification does not enter §7-§8 computation.
- `evidentiary_sub_class_publication_mix` (object: `{transaction_based: number, spread_based: number, interpolated_extrapolated: number}`): per-day percentages summing to 100, mapped to the three IOSCO Principle 8 input tiers. For VIPI v0.1.3 the expected mix is `{0, 0, 100}` (all observations are posted-rate inputs); the structure is in place for future revisions that incorporate transaction or spread-based inputs.
- `silent_fallback_exclusions` (array of objects): mappings curator-omitted under the curation policy's silent-FALLBACK exclusion rule (forthcoming subsection in v0.1.3 curation policy amendment), documented for audit completeness.

The additive-fields license is explicit at implementation plan §3.6 (L197 and L216-L218) and migration `0006_create_vipi_audit_records.sql` header (L5-L6): "Minimum-viable shape per Decision 2C; v0.1.4 may add additive fields without contract break." The three additive keys above exercise this license; they are not a schema migration.

---

## 11. Governance

### 11.1. Administrator

Volt HQ is the VIPI Administrator. During the private phase (v0.1 through the first public release), the Administrator's role is held by Jack Arnot. The Administrator is responsible for:

- Daily calculation and publication
- Monthly rebalancing decisions
- Methodology changes
- Correction and halt determinations
- Coordination with VCMI maintainer (same party at v0.1; to be formally separated as VCMI adoption grows)

### 11.2. Conflicts of Interest

Volt HQ maintains a written conflicts-of-interest register, published on volthq.dev, disclosing:

- Any direct or indirect financial interest in a tracked provider (currently: none)
- Any commercial contract, grant, or licensing arrangement with a tracked provider (currently: none)
- Any advisory, investment, or employment relationship between Volt HQ personnel and a tracked provider (currently: none)

Changes to this register are disclosed within 10 business days of arising.

**Provider-side manipulation risk.** Volt HQ does not regulate provider pricing decisions and cannot prevent a provider from setting prices that influence VIPI. This risk is structurally analogous to LIBOR's submitter-panel self-interest, with three critical mitigations: (a) providers' published prices are actual commercial rates at which customers transact, not estimates — any manipulation would require a provider to offer suboptimal prices to all customers simultaneously, incurring direct commercial cost; (b) the median-across-providers computation (Section 7.2) limits any single provider's influence on a constituent's price; (c) as VIPI adoption grows and potential settlement applications emerge, provider-side manipulation risk will warrant dedicated monitoring. The Administrator commits to formal provider-manipulation analysis at v1.0 and inclusion of provider-side conflict monitoring in the Advisory Group's remit (Section 11.4).

### 11.3. Source Classification Policy

The Administrator publishes — and maintains in the methodology changelog — the classification logic that assigns every provider mapping an `evidentiary_tier` value (one of `live_api` or `pricing_page_snapshot`) and a per-mapping evidentiary sub-class (`api_inline_price`, `api_catalog_gated`, `static_by_design`, or `gpu_hour_derived`) exposed in each day's audit record at §10.4. Mappings omitted under the curation policy's silent-FALLBACK rule are disclosed in the daily audit record via the `silent_fallback_exclusions` key; Akash GPU-hour-derived offerings are excluded under the curation policy's eligibility rules. The classification policy is reviewed annually alongside the methodology. This commitment operationalizes IOSCO Principle 8 (Hierarchy of Inputs) and adopts the BMR Article 27 disclosure framework as a design reference, not as a regulatory claim of EU benchmark status.

#### 11.3.1. Independent dimensions of source classification

The `evidentiary_tier` and `evidentiary_sub_class` classifications operate as independent dimensions of source classification rather than as a single derived hierarchy. The `evidentiary_tier` classification identifies how the Administrator's pipeline obtains a price observation on a given day (`live_api` or `pricing_page_snapshot`); the `evidentiary_sub_class` classification identifies the price-publication mechanism at the provider for that observation (`api_inline_price`, `api_catalog_gated`, `static_by_design`, or `gpu_hour_derived`). Any pairing of values across the two classifications permitted by the underlying adapter mechanics is a legitimate published combination; the two values are reported jointly per mapping rather than collapsed into a single composite class.

#### 11.3.2. Inaugural-basket classification (73 mappings as of base date)

| Provider | Mappings | `evidentiary_sub_class` | Empirical basis |
|---|---|---|---|
| DeepInfra | 23 | `api_inline_price` | API returns `pricing.cents_per_input_token` and `pricing.cents_per_output_token` inline in the live model record |
| Together | 21 | `api_inline_price` | API returns `pricing.input` and `pricing.output` inline in the live model record (empirically confirmed via live API curl on May 15, 2026) |
| Cerebras | 2 | `api_catalog_gated` | Live API returns model identifiers only; per-token prices resolved against Administrator-maintained `PRICING_MAP` |
| Fireworks | 3 | `api_catalog_gated` | Same retrieval pattern as Cerebras |
| Groq | 6 | `api_catalog_gated` | Same retrieval pattern as Cerebras |
| Hyperbolic | 12 | `api_catalog_gated` | Same retrieval pattern as Cerebras |
| Anthropic | 5 | `static_by_design` | No HTTP retrieval during daily computation; prices stored as source-file literals |
| OpenAI | 1 | `static_by_design` | Same retrieval pattern as Anthropic |
| **Total** | **73** | — | — |

#### 11.3.3. Per-class rationale

The `api_inline_price` class covers mappings where the provider exposes token pricing directly inside the live model metadata returned by the serving API. For these mappings, the Administrator's adapter reads the per-token price field directly from the provider response at retrieval time. This class includes 44 mappings across the DeepInfra and Together providers.

The `api_catalog_gated` class covers mappings where the provider API exposes model identifiers through a live catalog endpoint but does not return token pricing in the response payload itself. For these mappings, the Administrator maintains a provider-specific `PRICING_MAP` populated from the provider's publicly published pricing page and joined against the live model catalog during retrieval. This class includes 23 mappings across Cerebras, Fireworks, Groq, and Hyperbolic.

The `static_by_design` class covers mappings where the Administrator does not perform a live HTTP pricing retrieval request against the provider during the daily computation process. Instead, the applicable token prices are stored as source-file literals and revised manually when the provider's published pricing schedule changes. This class includes 6 mappings across Anthropic and OpenAI.

The `gpu_hour_derived` class covers mappings where the provider publishes GPU-hour infrastructure pricing rather than direct token pricing. For these mappings, the Administrator's adapter derives a per-token estimate using a fixed throughput constant applied to the published GPU-hour rate. Per Curation Policy §5(6), all Akash mappings are excluded from the eligibility universe. The sub-class remains enumerated for disclosure completeness.

#### 11.3.4. Publication mix and IOSCO Principle 8 mapping

The aggregate publication mix across the 73 inaugural mappings, exposed per-day in the audit record via the `evidentiary_sub_class_publication_mix` key defined at §10.4, is:

- 44/73 = 60.3% `api_inline_price`
- 23/73 = 31.5% `api_catalog_gated`
- 6/73 = 8.2% `static_by_design`
- 0/73 = 0% `gpu_hour_derived`

Mapped to the IOSCO Principle 8 input tiers per the audit-record disclosure at §10.4, this publication mix resolves to `{transaction_based: 0, spread_based: 0, interpolated_extrapolated: 100}`. All Administrator-included observations are posted-rate inputs and therefore classify under the interpolated/extrapolated tier of IOSCO Principle 8's hierarchy of inputs.

#### 11.3.5. Empirical basis

The 73-mapping classification recorded above corresponds to the inaugural distribution of `vcmi_provider_mappings` records in the `volt-snapshots` database as of base date. The per-class assignment for each provider is grounded in the Administrator's adapter source code, with the price-retrieval mechanism for each included provider summarized in the §11.3.2 table's "Empirical basis" column.

### 11.4. Oversight (Public Phase)

For the public phase (v1.0 and later), the Administrator commits to establishing a **VIPI Advisory Group** of 3–5 external members drawn from:

- Academic economics (market microstructure, price-index methodology)
- Buyer-side representation (enterprise FinOps, procurement)
- Industry-adjacent (not provider-side to avoid conflicts)

The Advisory Group reviews material methodology changes, consults on inclusion-criteria revisions, and reviews the annual compliance statement. The Advisory Group is advisory; final methodology decisions remain with the Administrator.

### 11.5. Record Retention

All inputs, calculations, audit records, correction logs, and rebalancing announcements are retained for a minimum of **5 years** from the date of publication. This meets IOSCO Principle 17 (Audits) requirements. Each VIPI value is stamped with a `methodology_version` of the form `vipi-X.Y.Z` corresponding to the version under which it was computed; this is distinct from the `vcmi-X.Y.Z` namespace stamped on identity-layer records and from any other Volt HQ measurement-product version namespaces.

### 11.6. External Audit

At v1.0 public launch, Volt HQ commits to an annual external audit of VIPI calculation methodology and adherence to published procedures. Auditor selection and audit scope will be disclosed at that time.

---

## 12. Corrections and Halts

### 12.1. Corrections

A published VIPI value is subject to correction if, after publication:

- An error is discovered in the input data used (e.g. a corrupted snapshot, a misclassified VCMI)
- A constituent's VCMI metadata is retroactively corrected (e.g. license classification revised)
- A calculation error is discovered

The correction protocol:

1. Corrections are announced within 1 business day of error confirmation.
2. The prior published value and the corrected value are both disclosed, with the reason for the correction.
3. Corrections are published in the daily audit record and the VIPI changelog.
4. Downstream consumers (contracts, research citations) are directed to the corrected value as authoritative. Historical time series on volthq.dev reflect the corrected value; prior values are preserved in the changelog for audit.

*BMR correspondence: this section addresses the substance of BMR Article 27(2)(f) (procedures for re-determination of the Benchmark including, where appropriate, re-publication). See §15 for the formal-labelling deferral notice.*

### 12.2. Halts

A sub-index is halted (not published) for a trading day under the conditions in Section 7.4 (insufficient data). Additional halt triggers:

- An active integrity investigation concerning data inputs or provider pricing manipulation.
- A material methodology change in transition that prevents a clean calculation under either the prior or new methodology.
- A force majeure event affecting the Administrator's ability to publish (e.g. widespread infrastructure outage).

Halts are disclosed immediately in the publication channel. A halted day is not backfilled unless the underlying issue is resolved and a defensible reconstructed value can be computed under the audit trail.

*BMR correspondence: this section addresses the substance of BMR Article 27(2)(e) for non-data-stress halts (integrity, force majeure, methodology transition). See §15 for the formal-labelling deferral notice.*

### 12.3. Methodology Versioning

Every VIPI value is associated with the methodology version under which it was computed. When methodology changes take effect:

- The change is announced at least **30 calendar days** before it becomes effective.
- A consultation window of at least 14 calendar days is provided for stakeholder feedback.
- The change is disclosed in the methodology changelog with full rationale.
- Prior methodology versions remain accessible at permanent URLs.
- Historical VIPI values are NOT recomputed under new methodology versions except when the change is a bug-fix correction (Section 12.1).

---

## 13. Sample Calculation (Illustrative)

The following illustrates the VIPI calculation under the divisor method using the 3:1 input:output blended-price formula. Constituent VCMIs and prices in this section are illustrative and not necessarily real models or actual inaugural constituents. Exact values will depend on the live inaugural constituent set determined at the May 18, 2026 base date.

**Scenario.** Three-constituent illustrative basket (the actual v0.1 basket may include up to 20 constituents per sub-index).

**Base date (May 18, 2026, 16:00 UTC).** Prices observed in the MOC window (15:55:00–16:04:59 UTC):

| Constituent VCMI | Input (`$/M`) | Output (`$/M`) | Blended (3:1) |
|---|---|---|---|
| `vcmi:llama-3.3-70b/fp8` | $0.25 | $0.80 | $0.3875 |
| `vcmi:qwen-3-235b-a22b` | $0.40 | $1.50 | $0.6750 |
| `vcmi:gpt-5-mini-undisclosed` | $0.15 | $0.60 | $0.2625 |

Where blended = (3 × input + output) / 4.

**Weighted basket value at base.** With equal weights `w_c = 1/3`:

weighted_sum = (1/3)(0.3875 + 0.6750 + 0.2625) = 1.3250 / 3 = 0.441667

**Divisor initialization.** VIPI(base) is set to 100.00:

D(base) = weighted_sum / 100 = 0.441667 / 100 = 0.00441667

**Subsequent trading day (no constituent change).** Suppose on a later day, prices are:

| Constituent | Input | Output | Blended |
|---|---|---|---|
| Llama 3.3 70B FP8 | $0.25 | $0.80 | $0.3875 (unchanged) |
| Qwen3 235B A22B | $0.40 | $1.50 | $0.6750 (unchanged) |
| GPT-5 Mini | $0.15 | $0.50 | $0.2375 (output reduced) |

weighted_sum_new = (1/3)(0.3875 + 0.6750 + 0.2375) = 1.3000 / 3 = 0.433333

VIPI = weighted_sum_new / D(base) = 0.433333 / 0.00441667 = 98.11

**Interpretation.** GPT-5 Mini's output price fell ~17% ($0.60 → $0.50). With equal weighting and three constituents, the basket value dropped ~1.9%, and VIPI moved from 100.00 to 98.11.

**Rebalancing example.** Suppose at the next monthly rebalancing, GPT-5 Mini is replaced by a new constituent `vcmi:claude-haiku-5-undisclosed` (input $1.00, output $5.00, blended $2.00). At the effective day's MOC snapshot, with the other two constituents at their current prices (blended $0.3875 and $0.6750):

New basket weighted_sum = (1/3)(0.3875 + 0.6750 + 2.00) = 3.0625 / 3 = 1.020833

New divisor is set to preserve continuity: `D_new = new_weighted_sum / VIPI_old`, where `VIPI_old = 98.11` is the index value just prior to the change.

D_new = 1.020833 / 98.11 = 0.010405

The index at the moment of the change is unchanged at 98.11. Subsequent days use `D_new` for the index calculation until the next composition change.

---

## 14. Relationship to Existing Benchmarks and Reference Points

The following comparison is provided for reader orientation, not as a quality judgment on existing references.

VIPI is designed to be interoperable with and comparable to existing academic and industry price references where possible. Specifically:

- **3:1 input-to-output blended ratio** matches Artificial Analysis and Epoch AI conventions, enabling direct comparison of VIPI to their published price trends.
- **Median-across-providers** for per-constituent pricing matches Epoch AI's approach for models without first-party APIs.
- **VCMI identity layer** provides the canonical identity infrastructure that earlier analyses (including the NBER working paper on the emerging LLM market) have had to construct ad-hoc.

VIPI differs from existing references in:

- **Data depth.** VIPI uses direct 5-minute resolution pricing from each provider's own API or pricing page. Existing references often rely on aggregator pass-through pricing or periodic manual collection, which is lower-frequency and may lag actual provider pricing events.
- **Provider coverage.** VIPI covers 9 providers at v0.1 launch (including DePIN providers Akash and Hyperbolic), versus the centralized-only coverage typical of existing comparisons.
- **Methodology transparency.** Full methodology is published (this document). Existing indices have largely undocumented or lightly documented methodologies.
- **Governance.** IOSCO-principle-adherent governance structure planned from v1.0.

VIPI is **not** designed to replace:

- Hardware-level GPU price indices (e.g. Silicon Data's SDH100, SDB200RT) — those measure a different layer of the stack (rental hardware) and are complementary.
- Quality-adjusted price indices (e.g. a16z's LLMflation, BLS hedonic cloud computing PPI). VIPI v0.1 is unadjusted for model quality. A quality-adjusted variant is flagged for v0.2 research.
- Usage-weighted indices. VIPI is equal-weighted.

Researchers working on derivatives design (e.g. the arxiv March 2026 paper proposing a Token Price Index for settlement of inference-token futures) may consider VIPI as a candidate settlement reference. Volt HQ is open to collaboration on this front.

---

## 15. Out of Scope for v0.1.3

The following are explicitly deferred:

- Reasoning-model-specific index (planned v0.2 within 60 days).
- Quality-adjusted / hedonic variant (planned v0.2 research).
- Tokenizer normalization (Claude Opus 4.7 uses a new tokenizer that reportedly consumes ~35% more tokens for equivalent text; this affects per-token price comparability across model families. Deferred to v0.2).
- Usage-weighted variant (requires defensible usage-estimation methodology; deferred).
- Intraday "VIPI-Spot" live 5-minute series (publishing infrastructure only; methodology additive).
- Settlement-specific variants for derivatives contracts (requires engagement with derivatives designers).
- Latency-adjusted or reliability-adjusted variants (requires additional observation infrastructure beyond pricing).
- Regional sub-indices (US, EU, APAC). Currently all providers are treated as global.
- Context-window-tier sub-indices (short-context vs long-context).
- Proprietary-cached or proprietary-batch-discount variants (Section 15 note on headline vs effective pricing).
- Reasoning-token billing within non-reasoning models (e.g., extended-thinking tokens billed at output rates) is not separately tracked in v0.1. The published output price reflects the provider's standard output token rate.
- Detection of silent quantization or serving-tier changes by providers (where the model string is unchanged but the underlying weights or precision are altered) is not within scope of v0.1 automated monitoring. Such changes, if discovered, are handled as VCMI corrections.
- BMR Article 27(2)(e) market-stress-period procedures and 27(2)(f) error-handling procedures: substantive coverage is present in §7.4 (data-availability stress halt), §12.1 (corrections protocol), and §12.2 (non-data halt triggers including integrity, force majeure, and methodology transition), with cross-reference labels added to each. Formal alignment of the methodology's procedural taxonomy to the full BMR Article 27(2) sub-paragraph structure — including market-stress procedures distinct from data-availability stress, and a unified error-handling registry — is deferred to v0.1.4.

### Note on headline vs effective pricing

VIPI uses each provider's published non-cached, non-batched, standard-tier pricing. This is the direct analog to commodity spot benchmarks (e.g. Brent) which reflect non-discounted physical market value, not negotiated contract prices. Users evaluating their own effective AI inference costs should apply their actual cache-hit rates, batch usage, and tier discounts against the VIPI headline. An "effective-price" variant that attempts to model typical enterprise discounts is out of scope for v0.1 because the required usage-mix assumptions are not defensibly estimable without proprietary data.

---

## 16. Open Questions (v0.1 Draft)

To resolve before v0.2:

1. Should `VIPI-Best` (cheapest-provider-per-constituent variant) be promoted to a co-headline alongside the median-based VIPI? Current practice: published as secondary metric. Case for promotion: it answers a more natural buyer question ("what can I actually pay").

2. Should the 20-constituent cap grow over time as VCMI registry expands? Caps may become the limiting factor on VIPI representativeness. Proposal: review cap at each v0.x release.

3. How should constituent weighting evolve once usage data is defensibly estimable? Proposals range from equal-weighted (keep v0.1) to size-tier-capped (S&P-500-Equal-Weight precedent) to provider-count-weighted (measures market activity).

4. For proprietary models with `size = undisclosed` where multiple providers offer pass-through resale (e.g. Claude Opus 4.6 on Anthropic direct and DeepInfra resale), should the constituent price be the upstream author's direct price (representing "true" cost) or the median across resellers (representing "market availability cost")? Current rule: median across all high-confidence mappings, which includes both. Alternative: upstream-author-only when available.

5. Tokenizer normalization methodology. Approaches: (a) normalize to a reference tokenizer; (b) convert to bytes-per-input/output using observed tokenizer behavior; (c) publish two VIPI variants (raw-token and normalized-byte). Requires dedicated design work.

6. Should the monthly rebalancing cadence be shortened to bi-weekly in markets where new-model velocity exceeds monthly cadence? Current: monthly. Alternative: quarterly (too slow) or bi-weekly (too reactive). Monthly proposed as stable middle ground.

7. What is the appropriate threshold for "continuity" of observation? Currently 95% of daily close snapshots over 14 days. Alternatives: lower (allow more gappy data, more inclusive) or higher (stricter, more exclusive). No case for changing at v0.1.

8. How are derivatives settlement needs reflected in methodology? If VIPI becomes a settlement anchor (per the arxiv TPI proposal), additional precision in the close calculation (e.g. multiple closes per day for different regional settlement) may be warranted.

9. Relationship to per-model dispersion measurement. Volt HQ also maintains a separate per-model, per-bucket dispersion product (the Volt Dispersion Index family, methodology to be published separately) that measures price coefficient of variation across providers offering the same model. VIPI and VDI answer different questions — basket-level price level versus per-model cross-provider price agreement — and use different unit bases. The relationship between the two products and whether they should share a publication channel is under review.

---

## 17. IOSCO Adherence Roadmap

VIPI v0.1 is designed to be structurally consistent with IOSCO Principles for Financial Benchmarks (2013). A formal statement of adherence will accompany v1.0 public launch. The adherence framework:

| IOSCO Principle | v0.1 status | v1.0 commitment |
|---|---|---|
| 1. Overall Responsibility | Documented (Section 11) | Formalized with external oversight |
| 2. Oversight of Third Parties | N/A (no third-party submitters) | Maintained |
| 3. Conflicts of Interest | Documented policy (Section 11.2) | Registered and audited |
| 4. Control Framework | Internal documented | External audit annually |
| 5. Internal Oversight | Maintainer-led | Advisory Group established |
| 6. Benchmark Design | Documented (Sections 3–9) | Unchanged |
| 7. Data Sufficiency | Observed prices (Section 7.1) | Posted-price input class disclosed per-publication via §10.4 evidentiary_sub_class_publication_mix; substantive transactional anchoring deferred to v0.2+ research item. |
| 8. Hierarchy of Inputs | Primary feed + fallbacks | Per-mapping evidentiary_sub_class classification + per-determination publication mix published in daily audit record (§10.4); see Source Classification Policy in §11.3. |
| 9. Transparency | Methodology published | Methodology published + per-publication audit record (§10.4) including evidentiary_sub_class_publication_mix exposing the input-hierarchy distribution. |
| 10. Periodic Review | Annual methodology review | Unchanged |
| 11. Content of the Methodology | This document | Unchanged |
| 12. Changes to the Methodology | Documented (Section 12.3) | Unchanged |
| 13. Transition | To-be-written cessation plan | Required before v1.0 |
| 14. Submitter Code of Conduct | N/A (no submitters) | N/A |
| 15. Internal Controls | Documented | Audited |
| 16. Complaints Procedure | jack@volthq.dev intake | Formal process at v1.0 |
| 17. Audits | Internal + record retention | External audit annually |
| 18. Audit Trail | Daily audit record published | Unchanged |
| 19. Cooperation with Regulatory Authorities | Open policy | Formalized |

---

## 18. Contact

For corrections, questions, or collaboration during the private phase:

- Volt HQ: volthq.dev
- Administrator: jack@volthq.dev
- VIPI publication page (planned): volthq.dev/vipi
- Methodology updates: volthq.dev/vipi/methodology

---

*VIPI v0.1.3 is a draft methodology. It will evolve through private-phase review before public launch. Feedback from academic, industry, and regulatory reviewers is encouraged and will shape the v1.0 methodology. Substantive changes across versions are documented in the changelog.*
