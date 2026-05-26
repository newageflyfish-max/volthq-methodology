# VIPI v0.1 Methodology Review — Round 1 Findings

## Summary
- 5 blocking issues
- 7 non-blocking issues
- 4 nitpicks

---

## Finding #0: Verification discrepancy

The verification block states "3:1 input:output blend as headline price." The spec text (Section 7.2 Step 4) says "The 3:1 input-to-output weighting follows the Artificial Analysis and Epoch AI industry convention."

But the formula is:

> `blended_price_{c,d} = (1 × input_price_{c,d} + 3 × output_price_{c,d}) / 4`

This weights **output** 3x, not input. The formula computes a **1:3 input:output** blend (or equivalently, 3:1 output:input). The text and verification both say "3:1 input-to-output" which reads as input = 3, output = 1 — the opposite of the formula.

The formula is correct (the industry convention weights output more heavily because typical inference tasks generate ~3 output tokens per input token). The descriptive text is backwards. This is promoted to Blocking Issue B1 below.

All other verification facts match the spec.

---

## Blocking issues

### Issue B1: Blended ratio text contradicts the formula

- **Section:** 7.2 Step 4
- **Problem:** Text says "The 3:1 input-to-output weighting" but the formula is `(1 × input + 3 × output) / 4`, which weights output 3x. "3:1 input-to-output" in standard English means input gets 3 parts and output gets 1 part — the opposite of the formula. The industry convention (Artificial Analysis, Epoch AI) assumes ~3 output tokens per input token, hence output is weighted 3x in the blended price. The formula is correct; the text is backwards.
- **Why blocking:** An external reviewer (academic or regulatory) comparing the formula to the text will immediately flag the contradiction. In a methodology document designed for IOSCO adherence, the core pricing formula cannot have an ambiguous description. This is the kind of error that destroys first-read credibility.
- **Proposed fix:** Change "The 3:1 input-to-output weighting" to "The 1:3 input-to-output weighting (reflecting the assumption that typical inference tasks generate approximately 3 output tokens per input token)." Also update any other reference to "3:1 input:output" throughout the spec and related materials.

### Issue B2: Emergency removal breaks the index formula

- **Section:** 9.3 and 8.1
- **Problem:** Section 9.3 says emergency removal "does not trigger a re-weighting of remaining constituents." If N was 10 and one constituent is removed, the remaining 9 constituents keep weight 1/10 each. The weights sum to 0.9, not 1.0. In the formula `VIPI(d) = VIPI(d-1) × Σ w_c × (price_c,d / price_c,d-1)`, if Σ w_c = 0.9 instead of 1.0, the index is structurally dampened: even if all remaining prices double, the index rises only 90% of the pure movement. Over multiple days this compounds into a persistent downward drag.
- **Why blocking:** The formula produces mathematically incorrect values when N is reduced mid-cycle without re-weighting. A hostile quantitative reviewer will reproduce this in 5 minutes. Standard index practice (S&P 500, DJIA) uses either immediate re-weighting or a divisor adjustment to maintain continuity.
- **Proposed fix:** Either (a) re-weight remaining constituents to 1/(N-1) on the effective day of the emergency removal, with the weight change disclosed in the audit trail; or (b) introduce a divisor D such that `VIPI(d) = (1/D) × Σ (price_c,d / price_c,d-1)` and adjust D on removal to preserve continuity — this is the Dow Jones method. Option (a) is simpler and sufficient for v0.1. Update Section 9.3 accordingly.

### Issue B3: Missing chaining rule on rebalancing days

- **Section:** 8.1 and 9.2
- **Problem:** The formula requires `price_c,d-1` for every constituent on day d. On the third trading day of a new month (when the new constituent set becomes effective per Section 9.2), a newly added constituent has no `price_c,d-1` within the prior constituent set. The formula is undefined. Section 8.1 says "preserves continuity across rebalancing dates even when constituents change, by computing each day's movement over the constituents valid for that day" — but doesn't specify where the d-1 price for a new constituent comes from.
- **Why blocking:** Without an explicit chaining rule, the formula cannot be computed on any rebalancing day where constituents change. A reader trying to reproduce VIPI independently would be stuck. Standard chained-index methodology is well-established but the spec must state it.
- **Proposed fix:** Add to Section 8.1 or Section 9.2: "On the rebalancing effective day (third trading day), each newly added constituent's `price_c,d-1` is the constituent's blended price as observed on the prior trading day (second trading day of the month), even though the constituent was not part of the index on that day. This ensures the new constituent's first-day contribution to the index captures only the price movement from d-1 to d, not the level difference between the new and old constituent sets. This is the standard chain-linking method used in Laspeyres, Paasche, and other major benchmark families."

### Issue B4: Backfill methodology has look-ahead bias

- **Section:** 8.3
- **Problem:** The spec says: "the inaugural VIPI v0.1 constituent set is the set of VCMIs that would have qualified under Section 5 rules as of the launch date, applied to the full backfill period." This means the constituent set is selected with knowledge of which models survived to the launch date (April/May 2026) and then applied retroactively to March 23, 2026. This is survivorship bias: models that were listed on March 23 but delisted by the launch date are excluded from the backfill, making the backfilled index look better (or at least different) than it would have been if computed live.
- **Why blocking:** Any academic reviewer will flag look-ahead bias in a backfilled index as methodologically unsound. The NBER working paper on LLM markets, the arxiv Token Price Index paper, and standard financial econometrics all treat survivorship bias as a first-order concern. A methodology document claiming IOSCO adherence cannot have an acknowledged look-ahead bias in its inaugural series.
- **Proposed fix:** Explicitly define the backfill as a "pro-forma historical series" (not a live series) and disclose the look-ahead bias. Alternatively, apply the inclusion criteria as of the base date (March 23) — but this creates a bootstrap problem (see B5 below). The cleanest fix: move the base date forward to a date where all inclusion criteria can be properly evaluated (e.g., April 7, which is 14 days after the first clean snapshot), and define the inaugural constituent set as "VCMIs meeting Section 5 criteria evaluated as of the base date." This eliminates the look-ahead bias entirely.

### Issue B5: Base date is day 1 of clean data — bootstrap paradox

- **Section:** 8.2, 7.3, 5 (criterion 4)
- **Problem:** The base date is March 23, 2026 — Volt's first continuous pricing snapshot date. On this date:
  - Section 5 criterion 4 requires 14 consecutive days of observation. On March 23, every model has exactly 1 day of history. No model qualifies.
  - Section 7.3's outlier rule requires a 7-day trailing median. On March 23, there is no trailing data. The outlier rule cannot fire.
  - The spec does not address either the inaugural eligibility evaluation or the outlier-rule cold-start.
- **Why blocking:** The index is undefined on its own base date under its own rules. The 14-day continuity criterion means the earliest date any constituent could qualify is April 6 (14 days after March 23). The 7-day outlier rule means the first 7 days of the index accept all prices without outlier protection. Combined with B4 (the look-ahead bias workaround), the backfill period is methodologically unsound.
- **Proposed fix:** Move the base date to April 7, 2026 (14 days after the first clean snapshot). This ensures: (a) all inaugural constituents have 14 days of continuity, (b) the 7-day trailing median is available from day 1, (c) the backfill look-ahead bias is eliminated because there is no backfill period. Add an explicit cold-start note: "The 7-day trailing median for outlier exclusion (Section 7.3) is available from the base date because the base date is set 14 days after the first clean snapshot. During the interval March 23–April 6, the outlier rule is not applicable because no trailing median exists; this period is excluded from the published VIPI series."

---

## Non-blocking issues

### Issue N1: Small panel vulnerability understated

- **Section:** 3 (Principle 2), 7.2
- **Problem:** Section 3 Principle 2 says "VIPI is inherently less manipulable than LIBOR ever was." Section 7.2 Step 1 shows that a constituent's price is computed as the median across provider mappings. With 2–3 high-confidence mappings per constituent (typical in the current dataset — only 2 models have 4 providers), a single provider changing its published price can shift the median. With 2 providers, one bad actor controls 50% of inputs. With 3, a coordinated pair controls 67%.
- **Why not blocking:** The structural difference from LIBOR (observed prices vs estimates) IS real and important. The small-panel risk is mitigable as provider coverage grows. But the "inherently less manipulable" claim is overstated for the v0.1 panel size.
- **Proposed fix:** Add a qualification: "...inherently less manipulable than LIBOR ever was, because its inputs are observed commercial prices rather than subjective estimates. However, the v0.1 provider panel (2–4 mappings per constituent) is small by mature benchmark standards, and the index's resistance to manipulation will strengthen as provider coverage increases. Users should consider this when evaluating VIPI's suitability as a settlement reference."

### Issue N2: "Trading day" not defined

- **Section:** Throughout (7.1, 9.1, 9.2, 10.1, etc.)
- **Problem:** The spec uses "trading day" extensively but never defines it. AI inference providers operate 24/7/365. Is every calendar day a trading day? Are weekends excluded? Are holidays excluded? The spec seems to assume every day is a trading day (the MOC window runs daily, the publication schedule says "each trading day"), but this is never stated.
- **Proposed fix:** Add to Section 7.1: "Every calendar day is a trading day. Inference providers operate continuously; there are no market holidays or weekend closures."

### Issue N3: Degradation to medium-only pricing not escalated

- **Section:** 7.2 Step 1
- **Problem:** A constituent can be admitted with 1 high + 1 medium mapping (Section 5 criterion 3). If the high mapping goes dark mid-month (zero observations in MOC window), Step 1 falls back to medium mappings. Step 3 excludes mappings with zero observations. The price is then determined entirely from the medium mapping. Section 7.2 notes this "in the daily calculation audit trail" but there's no escalation: no threshold for how many days a constituent can run on medium-only, no flag for emergency review. A constituent could run on medium-only data for an entire month.
- **Proposed fix:** Add: "If a constituent operates on medium-confidence-only pricing for more than 5 consecutive trading days, the Administrator must evaluate whether the high-confidence mapping loss is transient or structural. If structural, the constituent is flagged for potential emergency removal under Section 9.3."

### Issue N4: VIPI ≠ VIPI-Open ∪ VIPI-Closed for some licenses

- **Section:** 4, 5 (criterion 7)
- **Problem:** Section 5 says "Ineligible license classifications (e.g. 'Commercial Use Restricted' source-available licenses) are excluded from both VIPI-Open and VIPI-Closed." Such a VCMI could still meet VIPI headline criteria (1–6, no additional requirement per 7a). This means a constituent can be in the headline but in neither sub-index. Section 4 says sub-indices are "subsets of the headline" — mathematically, the headline ⊇ (VIPI-Open ∪ VIPI-Closed), which is consistent.
- **Proposed fix:** Add a clarifying note to Section 4: "The headline VIPI may include constituents with licenses that do not classify cleanly as 'open-weight' or 'proprietary.' Such constituents contribute to the headline but not to either sub-index."

### Issue N5: Tokenizer normalization is the most threatening deferred question

- **Section:** 16 (Open Question 5), 15
- **Problem:** Section 15 acknowledges "Claude Opus 4.7 uses a new tokenizer that consumes ~35% more tokens for equivalent text." VIPI measures $/million tokens. If different models' tokens are non-equivalent in content-per-token, the index compares prices in non-equivalent units. A model that costs more per token but packs more content per token might be cheaper per unit of actual work. This undermines the fundamental unit of measurement.
- **Why not blocking for v0.1:** The existing academic literature (Epoch AI, A16Z LLMflation) also does not normalize for tokenizers, so VIPI is consistent with the state of the art. The issue primarily affects cross-family comparisons (Claude vs GPT vs Llama). It IS the highest-priority v0.2 item.

### Issue N6: IOSCO Principle 3 covers administrator but not provider conflicts

- **Section:** 11.2, 17 (IOSCO table)
- **Problem:** The conflicts register covers Volt HQ's interests. It does not address the risk that providers — whose published prices are the sole input to VIPI — might manipulate prices if VIPI becomes a settlement reference. LIBOR's core failure was submitter self-interest; VIPI's providers are structurally analogous to LIBOR's panel banks, with the critical improvement that prices are commercial (not estimates). But the self-interest risk is not zero, especially if futures settle on VIPI.
- **Proposed fix:** Add to Section 11.2: "Volt HQ does not and cannot regulate provider pricing decisions. The risk that a provider manipulates its published prices to influence VIPI is mitigated by: (a) provider prices are actual commercial rates at which customers transact, not estimates; (b) manipulation would require a provider to offer suboptimal prices to all customers, not just to the index; (c) the median-across-providers computation limits any single provider's influence. However, as VIPI adoption grows and potential settlement applications emerge, this risk warrants monitoring and is flagged for v1.0 review."

### Issue N7: Competitive landscape names OpenRouter

- **Section:** 14
- **Problem:** "Existing references typically rely on aggregator pricing (e.g. OpenRouter) or periodic manual collection, which is lower-fidelity and slower to reflect pricing events." Naming a specific company in a methodology document as "lower-fidelity" reads as editorial. A methodology document should describe its own approach, not critique competitors.
- **Proposed fix:** Change to: "Existing references typically rely on aggregator pass-through pricing or periodic manual collection, which is lower-frequency and may lag actual provider pricing events."

---

## Nitpicks

1. **Section 13:** The sentence "VIPI-Closed (which would include Claude Sonnet but not the Llama and Qwen constituents) would be roughly flat" is commentary, not methodology. Fine in a blog post; feels out of place in a spec.

2. **Section 7.2 Step 4:** The blended formula description says "1 × input" and "3 × output" — the coefficient "1 ×" is unnecessary. Just write "(input_price + 3 × output_price) / 4" for clarity.

3. **Section 8.3:** "Backfilled values are permanent once published; any subsequent correction to a backfilled value follows the correction protocol in Section 12" — the word "permanent" contradicts "subject to correction" in the next clause. Say "Backfilled values are treated identically to live values for correction purposes."

4. **Section 10.3 citation format:** The academic citation lists "version 0.1" — this should update with each methodology version, or use "current version" with a retrieval date.

---

## What's strong

- **Observed-prices-only principle (Section 3, Principle 2)** is the single most important structural advantage over any LIBOR-analogous benchmark. This cannot be overstated and should not change.
- **Daily audit trail requirement (Section 10.1)** with explicit listing of constituents, mappings, observation counts, and outlier exclusions. This is genuine IOSCO-grade transparency and exceeds what most existing crypto/commodity indices publish.
- **Halt rule (Section 7.4)** is well-calibrated — the >30% missing OR >3 outlier-excluded thresholds are conservative enough to prevent degraded publication without being so aggressive that minor API outages halt the index.
- **Separation of identity (VCMI) and price (VIPI)** is clean architecture. The dependency on VCMI v0.1.2 means model-identity disputes are resolved in a separate standard, keeping the pricing methodology focused on pricing.
- **Equal-weighted rationale (Section 6)** is honest: "No defensible alternative weighting exists in this market." Admitting the limitation while explaining why equal-weight is the right v0.1 choice builds credibility. Don't soften this.
- **Emergency-removal constraints (Section 9.3)** — "constituents may be removed but not added" between rebalancings — prevents mid-cycle gaming. This is the correct conservative choice even though the formula needs fixing (B2).

---

## Items explicitly verified correct

- **A3 (Q3):** VIPI ≠ VIPI-Open ∪ VIPI-Closed is consistent with "subsets of the headline" framing. Non-blocking, noted as N4 for clarification.
- **D7 (Q7):** A VCMI with only 10 days of observation is cleanly excluded at the current rebalancing (14-day criterion not met) and eligible at the next. Works correctly.
- **D9 (Q9):** DST transitions, leap seconds, and feed outages are not material threats. UTC is used throughout; the halt rule covers feed outages; leap seconds add at most 1 second to a 10-minute window.
- **D10 (Q10):** A price change mid-MOC-window is handled correctly by the median. With 3 snapshots [old, old, new], the median is `old`. With 2 snapshots [old, new], the median is the midpoint. This is the intended smoothing behavior, not an artifact.
- **E11 (Q11):** VCMI v0.1.2 defines confidence as `high`, `medium`, `low` — matches VIPI's usage in Sections 5 and 7.2.
- **E12 (Q12):** VCMI Principle 6 correctly differentiates DeepInfra `-Turbo` (FP8 quantization → different VCMI) from Together `-tput` (serving tier → same VCMI, different mapping). VIPI inherits this resolution cleanly.
- **F13 (Q13):** All arithmetic in the sample calculation is correct: 0.6625/0.65 = 1.01923 ✓, 1.225/1.22 = 1.00410 ✓, (1.01923 + 1.00410 + 1.00000)/3 = 1.00778 ✓, 94.20 × 1.00778 = 94.93 ✓. The "1.9% increase" claim matches 1.923%. ✓
- **G14 (Q14):** Competitive landscape claim is general enough to be defensible but naming OpenRouter is slightly editorial. Noted as N7.
- **C5/IOSCO Principles 7, 9, 11, 18:** The spec delivers on its v0.1 status claims for Data Sufficiency (observed 5-minute prices), Transparency (this document), Content of Methodology (complete), and Audit Trail (daily records specified). IOSCO Principle 3 (Conflicts) is weaker — noted as N6.
