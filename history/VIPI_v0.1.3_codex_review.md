# VIPI v0.1.3 Codex Review — Locate and Quote

File: VIPI_v0.1.2_methodology.md (41,553 bytes confirmed)
Edits to apply: 19 (H7-5 excluded per administrator decision)

---

## Edit 1 (H1-1, non-blocker, Section 7.2 Step 4, line 169)
### old_str (verbatim, unique in file):
```
The 3:1 input-to-output weighting reflects the industry convention that typical inference workloads process approximately three input tokens for every output token (driven by system prompts, RAG-retrieved context, conversation history, and few-shot examples). This convention is documented in Artificial Analysis's published methodology ("we calculate a blended price assuming a 3:1 ratio of input to output tokens", artificialanalysis.ai/methodology) and used by Epoch AI's LLM price trend analysis. Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references.
```
### new_str (exact replacement):
```
The 3:1 input-to-output weighting reflects the Artificial Analysis published convention, which assumes a 3:1 ratio of input to output tokens ("we calculate a blended price assuming a 3:1 ratio of input to output tokens", artificialanalysis.ai/methodology). This convention is also used by Epoch AI's LLM price trend analysis. Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references.
```
### Notes:
Single occurrence at line 169. Removes fabricated causal story ("typical inference workloads process approximately three input tokens...driven by system prompts, RAG-retrieved context..."). Preserves the AA citation and Epoch AI reference. The AA quote remains unchanged. Edit 19 (H6-3) appends a new sentence after Step 4 — these compose cleanly because Edit 1 modifies line 169 (the explanatory paragraph after the formula) while Edit 19 appends after the end of that same paragraph.

---

## Edit 2 (H1-2, non-blocker, Section 4, line 78) — COMBINED WITH Edit 8 (H3-2)
### old_str (verbatim, unique in file):
```
**Why three indices at launch.** The divergence between open-weight and closed-source pricing is the single most structurally informative observation in the inference market. An index family that cannot surface that divergence fails to illuminate the market's most important dynamic. A single headline index would also be competitively weak: at least one existing service (inferencepriceindex.com) already publishes tiered inference indices sourced from aggregator data, and VIPI's defense is depth, provenance, and methodology — not just one number.
```
### new_str (exact replacement):
```
**Why three indices at launch.** The divergence between open-weight and closed-source pricing is a structurally important observation in the inference market. An index family that cannot surface that divergence fails to illuminate the market's most important dynamic. A single headline index would also be competitively weak: at least one existing service (inferencepriceindex.com) already publishes tiered inference indices sourced from aggregator data.
```
### Notes:
Combined edit: Edit 2 (H1-2) changes "the single most structurally informative" → "a structurally important". Edit 8 (H3-2) deletes ", and VIPI's defense is depth, provenance, and methodology — not just one number" from the end of the same paragraph. Both changes are in the same `old_str` block, so they are applied as one replacement to avoid composition conflicts. The preceding sentence about inferencepriceindex.com now ends the paragraph cleanly.

---

## Edit 3 (H1-3, non-blocker, Section 7.1, line 145)
### old_str (verbatim, unique in file):
```
Typically the window will include three snapshots at 15:55, 16:00, and 16:05 if the 16:05 snapshot is observed within the window, yielding robust central-tendency estimation.
```
### new_str (exact replacement):
```
Typically the window will include three snapshots at 15:55, 16:00, and 16:05 if the 16:05 snapshot is observed within the window, yielding a median-based close price.
```
### Notes:
Single occurrence at line 145. Changes only the final clause. Rest of bullet preserved.

---

## Edit 4 (H1-4, nitpick, Section 15, line 469)
### old_str (verbatim, unique in file):
```
- Tokenizer normalization (Claude Opus 4.7 uses a new tokenizer that consumes ~35% more tokens for equivalent text; this affects per-token price comparability across model families. Deferred to v0.2).
```
### new_str (exact replacement):
```
- Tokenizer normalization (Claude Opus 4.7 uses a new tokenizer that reportedly consumes ~35% more tokens for equivalent text; this affects per-token price comparability across model families. Deferred to v0.2).
```
### Notes:
Single occurrence at line 469. Inserts "reportedly" before "consumes". No other changes.

---

## Edit 5 (H2-1, nitpick, Section 14, line 448)
### old_str (verbatim, unique in file):
```
Existing references typically rely on aggregator pass-through pricing or periodic manual collection, which is lower-frequency and may lag actual provider pricing events.
```
### new_str (exact replacement):
```
Existing references often rely on aggregator pass-through pricing or periodic manual collection, which is lower-frequency and may lag actual provider pricing events.
```
### Notes:
Single occurrence at line 448. Changes "typically" → "often". Note: "Typically" also appears at line 145 (Edit 3 context, but Edit 3 doesn't change it) and line 54 (defensible usage per Round 3 assessment). Only this occurrence is changed.

---

## Edit 6 (H2-3, nitpick, Section 7.1, line 142)
### old_str (verbatim, unique in file):
```
- Well-established convention for global reference rates serving multiple time zones.
```
### new_str (exact replacement):
```
- Well-established convention for global reference rates serving multiple time zones (cf. WM/Reuters FX benchmark at 16:00 UTC London).
```
### Notes:
Single occurrence at line 142.

---

## Edit 7 (H3-1, non-blocker, Section 1, line 16)
### old_str (verbatim, unique in file):
```
The Volt Inference Price Index (VIPI) is the daily benchmark price of AI inference.
```
### new_str (exact replacement):
```
The Volt Inference Price Index (VIPI) is a daily price index for AI inference.
```
### Notes:
Single occurrence at line 16.

---

## Edit 8 — COMBINED with Edit 2 above. See Edit 2 entry.

---

## Edit 9 (H3-3, non-blocker, Section 14, lines 438-440)
### old_str (verbatim, unique in file):
```
## 14. Relationship to Existing Benchmarks and Reference Points

VIPI is designed to be interoperable with and comparable to existing academic and industry price references where possible. Specifically:
```
### new_str (exact replacement):
```
## 14. Relationship to Existing Benchmarks and Reference Points

The following comparison is provided for reader orientation, not as a quality judgment on existing references.

VIPI is designed to be interoperable with and comparable to existing academic and industry price references where possible. Specifically:
```
### Notes:
Inserts one new paragraph between the Section 14 heading and the original first sentence. Original text preserved unchanged.

---

## Edit 10 (H3-4, nitpick, Section 2, line 35)
### old_str (verbatim, unique in file):
```
VIPI is computed entirely over the VCMI v0.1.2 canonical identity layer. This is structural, not accidental. Without canonical identity, cross-provider price comparison is not possible;
```
### new_str (exact replacement):
```
VIPI is computed entirely over the VCMI v0.1.2 canonical identity layer. Without canonical identity, cross-provider price comparison is not possible;
```
### Notes:
Single occurrence at line 35. Deletes "This is structural, not accidental. " (the sentence plus trailing space). The preceding and following sentences connect cleanly.

---

## Edit 11 (H4-1, BLOCKER, Section 7.2 Step 2, line 159)
### old_str (verbatim, unique in file):
```
**Step 2 — Collect prices in the MOC window.** For each included mapping, retrieve all `offering_prices` rows with `quality_flag = 'clean'` where `timestamp >= 15:55:00 UTC` and `timestamp < 16:05:00 UTC` on the trading day.

**Step 3 — Compute per-mapping close price.**
```
### new_str (exact replacement):
```
**Step 2 — Collect prices in the MOC window.** For each included mapping, retrieve all `offering_prices` rows with `quality_flag = 'clean'` where `timestamp >= 15:55:00 UTC` and `timestamp < 16:05:00 UTC` on the trading day. Note: VIPI uses each provider's published standard-tier, non-cached, non-batched pricing. Effective prices paid by users may differ due to caching discounts, batch-API pricing, volume commitments, and negotiated enterprise rates. See Section 15 for the rationale for this choice.

**Step 3 — Compute per-mapping close price.**
```
### Notes:
The insertion goes at the end of the Step 2 paragraph, before the blank line and Step 3 heading. Using the Step 3 heading as trailing context ensures the insertion point is unambiguous.

---

## Edit 12, 13 (H4-2 + H4-3, non-blockers, Section 15, lines 476-477) — COMBINED
### old_str (verbatim, unique in file):
```
- Proprietary-cached or proprietary-batch-discount variants (Section 15 note on headline vs effective pricing).

### Note on headline vs effective pricing
```
### new_str (exact replacement):
```
- Proprietary-cached or proprietary-batch-discount variants (Section 15 note on headline vs effective pricing).
- Reasoning-token billing within non-reasoning models (e.g., extended-thinking tokens billed at output rates) is not separately tracked in v0.1. The published output price reflects the provider's standard output token rate.
- Detection of silent quantization or serving-tier changes by providers (where the model string is unchanged but the underlying weights or precision are altered) is not within scope of v0.1 automated monitoring. Such changes, if discovered, are handled as VCMI corrections.

### Note on headline vs effective pricing
```
### Notes:
Combined insertion of two new bullets at the end of the Section 15 deferred-items list, before the "### Note on headline vs effective pricing" subheading. Formatting matches existing list: each item is a single `- ` prefixed bullet. The existing last bullet and the subheading serve as unambiguous anchors.

---

## Edit 14 + Edit 17 + Edit 20 (H4-4 + H6-1 + H7-3, Section 7.3, line 173) — COMBINED
### old_str (verbatim, unique in file):
```
Within Step 2's observation set, any individual snapshot price that exceeds 3× the 7-day trailing median for that mapping, or is below 1/3 of the 7-day trailing median, is flagged as an outlier and excluded from the median computation. This rule implements the IOSCO-endorsed practice of "outlier elimination based on time-series information" and protects against single-snapshot data-pipeline errors. Excluded outliers are logged in the daily audit record but do not affect published VIPI values.
```
### new_str (exact replacement):
```
Within Step 2's observation set, any individual snapshot price that exceeds 3× the 7-day trailing median for that mapping, or is below 1/3 of the 7-day trailing median, is flagged as an outlier and excluded from the median computation. The 3× and 1/3 thresholds are initial values chosen as conservatively permissive starting points. They will be reviewed against observed data at the first scheduled methodology review. A tighter threshold (e.g., 2× / 1/2) may be appropriate once sufficient price-change events are observed to calibrate empirically. This rule implements the IOSCO-endorsed practice of "outlier elimination based on time-series information" and protects against single-snapshot data-pipeline errors. Excluded outliers are logged in the daily audit record but do not affect published VIPI values.

The outlier rule is designed to filter transient data-pipeline errors, not sustained promotional pricing. If a provider offers sustained loss-leader pricing that is below commercial sustainability thresholds, the Administrator may classify this as an integrity concern under Section 9.3 and exclude the mapping. The Administrator monitors for price anomalies concentrated in the MOC window period. If a provider's pricing shows patterns consistent with window-dressing (prices that diverge from non-window observations), the Administrator may reclassify the provider's confidence level or exclude the mapping under Section 9.3.
```
### Notes:
Combined three edits that all target Section 7.3's single paragraph:
- Edit 17 (H6-1): threshold-derivation note inserted after the first sentence (after "1/3 of the 7-day trailing median,")
- Edit 14 (H4-4): promotional-pricing caveat appended as a new paragraph
- Edit 20 (H7-3): MOC-window surveillance language appended to the same new paragraph
This combined approach is chosen because applying them sequentially would require each subsequent edit to match the output of the prior. Single combined replacement is cleaner for Codex. Structural choice: Edit 20 is placed in Section 7.3 (not Section 11.2) because it is a data-quality rule adjacent to the outlier rule, not a governance disclosure.

---

## Edit 15 (H5-1, non-blocker, front matter line 8 + Section 2 line 35)

### Part A — Front matter (line 8)
### old_str (verbatim, unique in file):
```
**Dependency:** Volt Canonical Model Identifier (VCMI) v0.1.2 or later
```
### new_str (exact replacement):
```
**Dependency:** Volt Canonical Model Identifier (VCMI) v0.1.2 (or any later patch in the v0.1.x series)
```

### Part B — Section 2 body (line 35) + new sentence insertion
### old_str (verbatim, unique in file):
```
VIPI is computed entirely over the VCMI v0.1.2 canonical identity layer. Without canonical identity, cross-provider price comparison is not possible; with it, VIPI inherits the rigor of VCMI's weight-identity rules, confidence levels, and pass-through resale handling.
```
### new_str (exact replacement):
```
VIPI is computed entirely over the VCMI v0.1.2 (or any later patch in the v0.1.x series) canonical identity layer. Without canonical identity, cross-provider price comparison is not possible; with it, VIPI inherits the rigor of VCMI's weight-identity rules, confidence levels, and pass-through resale handling. Adoption of VCMI v0.2 or later requires a VIPI methodology revision under Section 12.3 and is not automatic.
```
### Notes:
**COMPOSITION DEPENDENCY:** Edit 10 (H3-4) also modifies line 35 — it deletes "This is structural, not accidental." from the same sentence. Edit 10 must be applied BEFORE Edit 15 Part B, because Edit 15 Part B's `old_str` assumes Edit 10 has already removed that sentence. The `old_str` above reflects the post-Edit-10 state.

"v0.1.2 or later" scan: appears only once in the file (line 8, front matter). The body text at line 35 says "VCMI v0.1.2 canonical identity layer" without "or later" — so Part B inserts the bounded phrase as a parenthetical.

---

## Edit 16 (H5-2 Part A, nitpick, Section 5 after criterion list, ~line 105)
### old_str (verbatim, unique in file):
```
**Retention bias (S&P 500-style rule):** A constituent that marginally violates one or more inclusion criteria at a rebalancing date is NOT automatically removed.
```
### new_str (exact replacement):
```
**New-family ineligibility window.** Models from families not yet in the VCMI controlled vocabulary are ineligible until a VCMI vocabulary update is published. The Administrator maintains automated monitoring for new model families at tracked providers; vocabulary extensions require Administrator review per VCMI Principle 6 and are processed as a priority matter.

**Retention bias (S&P 500-style rule):** A constituent that marginally violates one or more inclusion criteria at a rebalancing date is NOT automatically removed.
```
### Notes:
Inserts a new disclosure paragraph immediately before the existing "Retention bias" paragraph. The "Retention bias" paragraph is preserved unchanged and serves as the trailing anchor. Insertion point is after criterion 7 / dual-inclusion / ineligible-license paragraphs and before retention bias — logically correct (it's an eligibility disclosure adjacent to other eligibility rules). Confirmed: no specific-day SLA appears in the `new_str`.

---

## Edit 18 (H6-2, non-blocker, Section 7.4, lines 179-182)
### old_str (verbatim, unique in file):
```
then that sub-index is **not published** for that day. The missing day is later backfilled only if the underlying data issue is resolved and the audit trail supports a reconstructed calculation; otherwise the day remains a gap. Halts are disclosed immediately in the VIPI publication channel. This rule ensures VIPI is never published with known-degraded inputs.
```
### new_str (exact replacement):
```
then that sub-index is **not published** for that day. The 30% and 3-constituent thresholds are initial values; the Administrator will review them against observed halt-trigger frequency after 90 days of live publication. The missing day is later backfilled only if the underlying data issue is resolved and the audit trail supports a reconstructed calculation; otherwise the day remains a gap. Halts are disclosed immediately in the VIPI publication channel. This rule ensures VIPI is never published with known-degraded inputs.
```
### Notes:
Inserts the threshold-review note as the second sentence, immediately after "not published for that day." This placement keeps the threshold caveat close to where the thresholds are defined (lines 179-180) rather than at the end of the paragraph.

---

## Edit 19 (H6-3, non-blocker, Section 7.2 after Step 4, line 169)
### old_str (verbatim, unique in file):
```
Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references.

### 7.3. Outlier Exclusion
```
### new_str (exact replacement):
```
Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references. The two-stage median (within-provider, then across-provider) is chosen over a pooled single-stage median to prevent providers with more frequent snapshot observations from having outsized influence on the constituent price.

### 7.3. Outlier Exclusion
```
### Notes:
Appends one sentence at the very end of the Step 4 explanatory paragraph, before the Section 7.3 heading. Uses the Section 7.3 heading as trailing anchor for unambiguous placement.

**COMPOSITION CHECK:** Edit 1 modifies the body of line 169 (the AA convention paragraph). Edit 19 appends after the last sentence of that same paragraph. Edit 1's `new_str` ends with "...existing academic and industry price references." — which is the same trailing text as the original. So Edit 19's `old_str` ("Using the same ratio ensures VIPI values are directly comparable to existing academic and industry price references.") remains valid after Edit 1 is applied. **No conflict.**

---

## Edit 21 (H7-6, non-blocker, Section 5 criterion 3, line 92)
### old_str (verbatim, unique in file):
```
3. **Provider coverage.** The VCMI has at least two `provider_mappings` with `confidence = high` or `confidence = medium`. At least one mapping must be `confidence = high`.
4. **Continuity.**
```
### new_str (exact replacement):
```
3. **Provider coverage.** The VCMI has at least two `provider_mappings` with `confidence = high` or `confidence = medium`. At least one mapping must be `confidence = high`. Confidence levels (`high`, `medium`, `low`) are defined in VCMI v0.1.2 Section 6. In summary: `high` requires either identical upstream source URL confirmation, byte-level weight verification, direct provider confirmation, or trivial decomposition of the provider string into VCMI components with documented brand-suffix meaning.
4. **Continuity.**
```
### Notes:
Appends the confidence-definition summary to the end of criterion 3, before criterion 4. Uses the "4. **Continuity.**" heading as trailing anchor.

**COMPOSITION CHECK with Edit 16:** Edit 16 inserts a new paragraph after the criteria list (before "Retention bias"). Edit 21 modifies criterion 3 (line 92), well before the insertion point. **No conflict.**

---

## Composition and ordering summary

Edits that modify the same region and must be applied in order:

1. **Section 2 (line 35):** Edit 10 (delete "This is structural, not accidental.") → then Edit 15 Part B (insert VCMI version bound + new sentence). Edit 15 Part B's `old_str` is written for the post-Edit-10 state.

2. **Section 4 (line 78):** Edit 2+8 combined — single replacement, no ordering issue.

3. **Section 7.2 (lines 155-169):** Edit 11 (Step 2 insertion) → Edit 1 (Step 4 paragraph rewrite) → Edit 19 (append after Step 4). These target different paragraphs within 7.2 and do not overlap. No ordering dependency.

4. **Section 7.3 (line 173):** Edit 14+17+20 combined — single replacement, no ordering issue.

5. **Section 5 (lines 92-105):** Edit 21 (criterion 3 annotation) → Edit 16 (new-family ineligibility window before retention bias). These target different locations within Section 5. No ordering dependency.

6. **Section 15 (lines 469-477):** Edit 4 (tokenizer "reportedly") and Edit 12+13 (new bullets) target different lines. No ordering dependency.

**Recommended Codex application order:** 10 → 15B → 15A → 2+8 → 7 → 3 → 6 → 1 → 11 → 19 → 14+17+20 → 18 → 21 → 16 → 4 → 5 → 9 → 12+13. (Front-matter bumps last.)

---

## Closing checklist

- **Total edits located:** 19 of 19 (Edit 8 combined with Edit 2; Edits 12+13 combined; Edits 14+17+20 combined)
- **Edits where target passage could not be located:** 0
- **Edits where target passage appeared more than once:** 0 (all `old_str` blocks are unique in the file)
- **Composition conflicts between edits:** 1 identified — Edit 10 must precede Edit 15 Part B (documented above with ordering)
- **Confirmation that H7-5 was NOT located:** Confirmed. H7-5 (spurious precision) is excluded per administrator decision. Not present in this review.
- **Confirmation that no specific-day SLA appears in Edit 16's new_str:** Confirmed. Edit 16 contains "processed as a priority matter" with no numeric day count.
