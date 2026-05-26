# Volt Inference Price Index (VIPI) — Methodology

**Version:** 0.1.2 (draft, private)
**Administrator:** Volt HQ (volthq.dev)
**Status:** Pre-release. Private until public launch.
**Base date:** April 7, 2026, 16:00:00 UTC
**Base value:** 100.00
**Dependency:** Volt Canonical Model Identifier (VCMI) v0.1.2 or later
**Last updated:** April 20, 2026
**IOSCO status:** Designed to adhere to IOSCO Principles for Financial Benchmarks (2013). Formal statement of adherence planned for v1.0.

---

## 1. Purpose and Scope

The Volt Inference Price Index (VIPI) is the daily benchmark price of AI inference. It measures the per-million-token cost of running language-model inference across the commercially available market, as observed across a defined universe of inference providers. VIPI is published for the following purposes:

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

VIPI is computed entirely over the VCMI v0.1.2 canonical identity layer. This is structural, not accidental. Without canonical identity, cross-provider price comparison is not possible; with it, VIPI inherits the rigor of VCMI's weight-identity rules, confidence levels, and pass-through resale handling.

Specifically:

- Every VIPI constituent is a single VCMI entry with `status = active`.
- A constituent's price on a given day is derived from `provider_mappings` with `confidence = high` (see Section 7.2 for fallback rules).
- VCMI's Principle 6 (identity follows weights, not branding) ensures that provider brand suffixes cannot be gamed to create spurious constituents.
- VCMI's `gpu_type` and `serving_tier` fields on `provider_mappings` ensure that multi-price configurations are correctly differentiated.

Any change to VCMI that affects VIPI constituent identity is treated as a methodology event under Section 12 (Corrections and Halts) and disclosed in VIPI's changelog.

---

## 3. Design Principles

VIPI follows six operating principles, in order of priority:

1. **Integrity over responsiveness.** Methodology choices that preserve the benchmark's long-run credibility take precedence over choices that make the benchmark feel fresh or reactive in the short term.

2. **Observed prices only.** VIPI uses prices that providers have published via their official APIs or pricing pages. No estimates, no submissions, no survey data, no projections. This is the single most important structural difference from LIBOR: inputs are observed commercial prices rather than subjective submissions. However, the v0.1 provider panel (typically 2–4 mappings per constituent) is small by mature benchmark standards. A single provider's price change can shift a constituent's median; a coordinated pair among three providers could dominate it. VIPI's structural resistance to manipulation strengthens as provider coverage grows. Users evaluating VIPI's suitability as a settlement reference should account for the current panel size.

3. **Rules over judgment.** VIPI's monthly rebalancing and daily calculation are governed by published rules that a reader with access to the raw feed can reproduce independently. Expert judgment is invoked only at explicit, documented decision points (methodology revisions, retractions, halts) and always with reasoning disclosed.

4. **Transparency over competitive advantage.** Every input, rule, and calculation step is publicly disclosed. The methodology is a public good; Volt's commercial advantage derives from data collection, historical depth, and curation quality — not from withheld methodology.

5. **Conservative change management.** Minor corrections are routine. Material methodology changes require documented rationale, consultation where practical, minimum notice periods, and continuity provisions. Historical VIPI values are never retroactively revised except to correct data errors.

6. **No conflicts of interest.** Volt HQ holds no equity, debt, token, or revenue share in any provider whose prices contribute to VIPI. Any prospective arrangement that would create such an interest must be disclosed and the provider potentially excluded before the arrangement is established. This is a permanent structural rule, not a policy subject to change.

These principles are the operational translation of IOSCO Principle 1 (governance), Principle 7 (data sufficiency), Principle 9 (transparency of methodology), and Principle 13 (transition).

---

## 4. The Index Family (v0.1)

VIPI v0.1 publishes three indices. Each shares the same methodology; only the constituent universe differs.

| Index | Short name | Constituent universe |
|---|---|---|
| Volt Inference Price Index | **VIPI** | All VCMIs meeting inclusion criteria (the headline). |
| Volt Inference Price Index — Open | **VIPI-Open** | VCMIs with open-weight licenses (Apache-2.0, MIT, Llama-Community, Qwen-License, Apache 2.0 derivatives). |
| Volt Inference Price Index — Closed | **VIPI-Closed** | VCMIs with proprietary licenses (Anthropic, OpenAI, Google proprietary, Zhipu proprietary, etc.). |

**Why three indices at launch.** The divergence between open-weight and closed-source pricing is the single most structurally informative observation in the inference market. An index family that cannot surface that divergence fails to illuminate the market's most important dynamic. A single headline index would also be competitively weak: at least one existing service (inferencepriceindex.com) already publishes tiered inference indices sourced from aggregator data, and VIPI's defense is depth, provenance, and methodology — not just one number.

**VIPI-Reasoning** is planned for v0.2 (target: within 60 days of v0.1 public launch). Reasoning models are excluded from v0.1 because they consume substantially more tokens per task, which makes per-million-token comparisons misleading relative to non-reasoning models. Treating them as a separate index preserves per-token interpretability for v0.1.

**Licensing edge case.** A VCMI whose license does not classify cleanly as either "open-weight" or "proprietary" (for example, "Commercial Use Restricted" source-available licenses) may qualify for the headline VIPI but be excluded from both VIPI-Open and VIPI-Closed. In such cases, the headline VIPI includes the constituent and the sub-indices do not. The sub-indices are subsets of, but not necessarily an exhaustive partition of, the headline basket.

---

## 5. Inclusion Criteria

A VCMI is eligible for inclusion in a VIPI sub-index at a monthly rebalancing date if and only if all of the following are true at the rebalancing reference date (the last day of the prior calendar month, 16:00 UTC):

1. **Active status.** The VCMI has `status = active` in the VCMI registry.
2. **Identity confidence.** The VCMI's upstream source URL is verified (`high` confidence on upstream author).
3. **Provider coverage.** The VCMI has at least two `provider_mappings` with `confidence = high` or `confidence = medium`. At least one mapping must be `confidence = high`.
4. **Continuity.** The VCMI has been continuously observed in the pricing feed for at least 14 consecutive days ending on the rebalancing reference date. "Continuously" means present in at least 95% of the daily 16:00 UTC close snapshots during the 14-day window, allowing for provider API outages.
5. **Non-reasoning.** The VCMI is not a reasoning-specialized model (identified by `variant` containing `reasoning`, or by explicit categorization in the VCMI notes field). Reasoning models are routed to VIPI-Reasoning when that index launches.
6. **Non-opaque pricing.** The VCMI's pricing is expressed in per-input-token and per-output-token terms (not per-GPU-hour, not subscription, not freemium). This excludes `pricePerGpuHour`-only offerings and subscription tiers.
7. **Sub-index specific criteria:**
   - **VIPI:** no additional criteria beyond 1–6.
   - **VIPI-Open:** VCMI's `license` field is one of a published list of open-weight licenses (Apache-2.0, MIT, Llama-*-Community, Qwen-License, Gemma license, or other licenses explicitly classified as open-weight by the maintainer).
   - **VIPI-Closed:** VCMI's `license` field contains `Proprietary-*` or is otherwise classified as proprietary by the maintainer.

**Dual inclusion:** A VCMI that qualifies for VIPI-Open or VIPI-Closed is also in the headline VIPI. The sub-indices are subsets of the headline, not substitutes.

**Ineligible license classifications** (e.g. "Commercial Use Restricted" source-available licenses) are excluded from both VIPI-Open and VIPI-Closed at v0.1 and flagged for v0.2 discussion.

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

---

## 7. Daily Price Determination

### 7.1. Assessment Window (Market-on-Close)

VIPI is calculated once daily using a **Market-on-Close (MOC) assessment window** ending at 16:00:00 UTC. The window runs from 15:55:00 UTC to 16:04:59 UTC — a 10-minute interval centered on the close.

Rationale for 16:00 UTC:
- After Asian market close (Tokyo, Shanghai, Singapore).
- During European end-of-day (London at 17:00, Frankfurt at 18:00).
- Before US market close (NYSE at 21:00 UTC during EST).
- Well-established convention for global reference rates serving multiple time zones.

Rationale for 10-minute median window (vs single-snapshot close):
- Volt's pricing feed is sampled at 5-minute intervals. A 10-minute window contains two scheduled snapshots (15:55 and 16:00) at minimum. Typically the window will include three snapshots at 15:55, 16:00, and 16:05 if the 16:05 snapshot is observed within the window, yielding robust central-tendency estimation.
- Mitigates the LIBOR-era criticism that single-point benchmarks with narrow windows and subjective timing ("just prior to 11am") enable manipulation.
- Median (rather than mean) is robust to single-snapshot outliers caused by provider API glitches.

**Definition of trading day.** For VIPI, every calendar day is a trading day. Inference providers operate continuously (24/7/365); there are no market holidays, weekend closures, or exchange-observance adjustments. The MOC window is computed every calendar day at 16:00:00 UTC regardless of the day of the week.

### 7.2. Per-Constituent Price

For each constituent VCMI on each trading day:

**Step 1 — Collect all provider mappings.** Retrieve all `provider_mappings` with `confidence = high` for this VCMI. If fewer than two `high`-confidence mappings exist, fall back to including `confidence = medium` mappings for price determination (but note the degradation in the daily calculation audit trail).

**Escalation for sustained fallback.** If a constituent operates on medium-confidence-only pricing (zero high-confidence mappings with observations in the MOC window) for more than 5 consecutive trading days, the Administrator evaluates whether the high-confidence mapping loss is transient (e.g., provider API outage) or structural (e.g., provider delisted the model). If structural, the constituent is flagged for potential emergency removal under Section 9.3. The evaluation is disclosed in the daily audit record on the day the escalation criterion is met and on each subsequent day the constituent remains in medium-only status.

**Step 2 — Collect prices in the MOC window.** For each included mapping, retrieve all `offering_prices` rows with `quality_flag = 'clean'` where `timestamp >= 15:55:00 UTC` and `timestamp < 16:05:00 UTC` on the trading day.

**Step 3 — Compute per-mapping close price.** For each mapping, the close price is the median of its `priceInputPerMillion` (and separately, `priceOutputPerMillion`) values observed in the window. If only one observation is present, that value is used. If zero observations are present for a mapping in the window, that mapping is excluded from this day's calculation.

**Step 4 — Compute per-constituent close price.** The constituent's daily close price is the **median across all included provider mappings**, computed separately for input and output. Formally:

- `input_price_{c,d}` = median over mappings of `input_price_{mapping,d}`
- `output_price_{c,d}` = median over mappings of `output_price_{mapping,d}`
- `blended_price_{c,d}` = (3 × `input_price_{c,d}` + `output_price_{c,d}`) / 4

The 3:1 input-to-output weighting reflects the industry convention that typical inference workloads process approximately three input tokens for every output token (driven by system prompts, RAG-retrieved context, conversation history, and few-shot examples). This convention is documented in Artificial Analysis's published methodology ("we calculate a blended price assuming a 3:1 ratio of input to output tokens", artificialanalysis.ai/methodology) and used by Epoch AI's LLM price trend analysis. Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references.

### 7.3. Outlier Exclusion

Within Step 2's observation set, any individual snapshot price that exceeds 3× the 7-day trailing median for that mapping, or is below 1/3 of the 7-day trailing median, is flagged as an outlier and excluded from the median computation. This rule implements the IOSCO-endorsed practice of "outlier elimination based on time-series information" and protects against single-snapshot data-pipeline errors. Excluded outliers are logged in the daily audit record but do not affect published VIPI values.

### 7.4. Insufficient-Data Halt

If, on any trading day:

- More than 30% of a sub-index's constituents have zero valid observations in the MOC window, OR
- More than 3 constituents of any sub-index are in outlier-excluded state simultaneously,

then that sub-index is **not published** for that day. The missing day is later backfilled only if the underlying data issue is resolved and the audit trail supports a reconstructed calculation; otherwise the day remains a gap. Halts are disclosed immediately in the VIPI publication channel. This rule ensures VIPI is never published with known-degraded inputs.

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

- **VIPI base:** 100.00 on April 7, 2026, 16:00:00 UTC.
- **VIPI-Open base:** 100.00 on April 7, 2026, 16:00:00 UTC.
- **VIPI-Closed base:** 100.00 on April 7, 2026, 16:00:00 UTC.

The base date is set 15 calendar days after Volt's first continuous pricing snapshot (March 23, 2026). This ensures every inaugural constituent can satisfy the 14-day continuity requirement (Section 5, criterion 4) on the base date and provides an initial 7-day trailing median for the outlier-exclusion rule (Section 7.3) from day one of publication. All three sub-indices share the same base date to enable direct relative comparison across the family.

### 8.3. Inception and Historical Record

VIPI v0.1 has no backfilled historical period. Publication begins on the base date (April 7, 2026) using the live constituent set selected per Section 5 inclusion criteria as evaluated at the base date.

The period from March 23, 2026 (first continuous pricing snapshot) to April 6, 2026 (day prior to base date) is termed the **bootstrap window**. Data collected during the bootstrap window is used exclusively for computing inclusion criteria (the 14-day continuity requirement in Section 5, criterion 4) and for initializing the 7-day trailing median required by the outlier-exclusion rule (Section 7.3). No VIPI values are published for the bootstrap window. This design eliminates look-ahead bias: VIPI's published series reflects only the constituent decisions that could have been made with information available at each date.

Published VIPI values are treated identically to live values for correction purposes (Section 12). Historical values are not recomputed under revised methodology versions except to correct errors in the input data.

### 8.4. Published Values

Each trading day, three values are published per sub-index:

- **Blended VIPI** (3:1 input-to-output weighted; the headline).
- **VIPI-Input** (input-token-only variant; equivalent calculation using input prices only).
- **VIPI-Output** (output-token-only variant; equivalent calculation using output prices only).

Plus a secondary metric per sub-index:

- **VIPI-Best** — computed identically to VIPI but using the **cheapest provider per constituent** rather than the median. Useful as a "best-available" benchmark analogous to Platts' "most competitive grade" concept. Published alongside but not considered the headline number.

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

- The constituent's VCMI is retracted (Section 10 of VCMI v0.1.2 spec) due to discovering the underlying model does not exist or was a phantom listing.
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

**Provider-side manipulation risk.** Volt HQ does not regulate provider pricing decisions and cannot prevent a provider from setting prices that influence VIPI. This risk is structurally analogous to LIBOR's submitter-panel self-interest, with three critical mitigations: (a) providers' published prices are actual commercial rates at which customers transact, not estimates — any manipulation would require a provider to offer suboptimal prices to all customers simultaneously, incurring direct commercial cost; (b) the median-across-providers computation (Section 7.2) limits any single provider's influence on a constituent's price; (c) as VIPI adoption grows and potential settlement applications emerge, provider-side manipulation risk will warrant dedicated monitoring. The Administrator commits to formal provider-manipulation analysis at v1.0 and inclusion of provider-side conflict monitoring in the Advisory Group's remit (Section 11.3).

### 11.3. Oversight (Public Phase)

For the public phase (v1.0 and later), the Administrator commits to establishing a **VIPI Advisory Group** of 3–5 external members drawn from:

- Academic economics (market microstructure, price-index methodology)
- Buyer-side representation (enterprise FinOps, procurement)
- Industry-adjacent (not provider-side to avoid conflicts)

The Advisory Group reviews material methodology changes, consults on inclusion-criteria revisions, and reviews the annual compliance statement. The Advisory Group is advisory; final methodology decisions remain with the Administrator.

### 11.4. Record Retention

All inputs, calculations, audit records, correction logs, and rebalancing announcements are retained for a minimum of **5 years** from the date of publication. This meets IOSCO Principle 17 (Audits) requirements.

### 11.5. External Audit

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

### 12.2. Halts

A sub-index is halted (not published) for a trading day under the conditions in Section 7.4 (insufficient data). Additional halt triggers:

- An active integrity investigation concerning data inputs or provider pricing manipulation.
- A material methodology change in transition that prevents a clean calculation under either the prior or new methodology.
- A force majeure event affecting the Administrator's ability to publish (e.g. widespread infrastructure outage).

Halts are disclosed immediately in the publication channel. A halted day is not backfilled unless the underlying issue is resolved and a defensible reconstructed value can be computed under the audit trail.

### 12.3. Methodology Versioning

Every VIPI value is associated with the methodology version under which it was computed. When methodology changes take effect:

- The change is announced at least **30 calendar days** before it becomes effective.
- A consultation window of at least 14 calendar days is provided for stakeholder feedback.
- The change is disclosed in the methodology changelog with full rationale.
- Prior methodology versions remain accessible at permanent URLs.
- Historical VIPI values are NOT recomputed under new methodology versions except when the change is a bug-fix correction (Section 12.1).

---

## 13. Sample Calculation (Illustrative)

The following illustrates the VIPI calculation under the divisor method using the 3:1 input:output blended-price formula. Exact values will depend on the inaugural constituent set at the April 7, 2026 base date.

**Scenario.** Three-constituent illustrative basket (the actual v0.1 basket may include up to 20 constituents per sub-index).

**Base date (April 7, 2026, 16:00 UTC).** Prices observed in the MOC window (15:55:00–16:04:59 UTC):

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

VIPI is designed to be interoperable with and comparable to existing academic and industry price references where possible. Specifically:

- **3:1 input-to-output blended ratio** matches Artificial Analysis and Epoch AI conventions, enabling direct comparison of VIPI to their published price trends.
- **Median-across-providers** for per-constituent pricing matches Epoch AI's approach for models without first-party APIs.
- **VCMI identity layer** provides the canonical identity infrastructure that earlier analyses (including the NBER working paper on the emerging LLM market) have had to construct ad-hoc.

VIPI differs from existing references in:

- **Data depth.** VIPI uses direct 5-minute resolution pricing from each provider's own API or pricing page. Existing references typically rely on aggregator pass-through pricing or periodic manual collection, which is lower-frequency and may lag actual provider pricing events.
- **Provider coverage.** VIPI covers 9 providers at v0.1 launch (including DePIN providers Akash and Hyperbolic), versus the centralized-only coverage typical of existing comparisons.
- **Methodology transparency.** Full methodology is published (this document). Existing indices have largely undocumented or lightly documented methodologies.
- **Governance.** IOSCO-principle-adherent governance structure planned from v1.0.

VIPI is **not** designed to replace:

- Hardware-level GPU price indices (e.g. Silicon Data's SDH100, SDB200RT) — those measure a different layer of the stack (rental hardware) and are complementary.
- Quality-adjusted price indices (e.g. a16z's LLMflation, BLS hedonic cloud computing PPI). VIPI v0.1 is unadjusted for model quality. A quality-adjusted variant is flagged for v0.2 research.
- Usage-weighted indices. VIPI is equal-weighted.

Researchers working on derivatives design (e.g. the arxiv March 2026 paper proposing a Token Price Index for settlement of inference-token futures) may consider VIPI as a candidate settlement reference. Volt HQ is open to collaboration on this front.

---

## 15. Out of Scope for v0.1.2

The following are explicitly deferred:

- Reasoning-model-specific index (planned v0.2 within 60 days).
- Quality-adjusted / hedonic variant (planned v0.2 research).
- Tokenizer normalization (Claude Opus 4.7 uses a new tokenizer that consumes ~35% more tokens for equivalent text; this affects per-token price comparability across model families. Deferred to v0.2).
- Usage-weighted variant (requires defensible usage-estimation methodology; deferred).
- Intraday "VIPI-Spot" live 5-minute series (publishing infrastructure only; methodology additive).
- Settlement-specific variants for derivatives contracts (requires engagement with derivatives designers).
- Latency-adjusted or reliability-adjusted variants (requires additional observation infrastructure beyond pricing).
- Regional sub-indices (US, EU, APAC). Currently all providers are treated as global.
- Context-window-tier sub-indices (short-context vs long-context).
- Proprietary-cached or proprietary-batch-discount variants (Section 15 note on headline vs effective pricing).

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
| 7. Data Sufficiency | Observed prices (Section 7.1) | Unchanged |
| 8. Hierarchy of Inputs | Primary feed + fallbacks | Unchanged |
| 9. Transparency | Methodology published | Unchanged |
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

*VIPI v0.1.2 is a draft methodology. It will evolve through private-phase review before public launch. Feedback from academic, industry, and regulatory reviewers is encouraged and will shape the v1.0 methodology. Substantive changes across versions are documented in the changelog.*
