# VIPI v0.1 → v0.1.1 Codex Review (Step 1)

## Status
- Edits located: **14/14**
- Ambiguous edits: **0**
- Additional references found: **5**

---

## Edit-by-edit review

### Edit 1: Flip blended-price formula
- **Location:** Section 7.2 Step 4, lines 157–163
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > **Step 4 — Compute per-constituent close price.** The constituent's daily close price is the **median across all included provider mappings**, computed separately for input and output. Formally:
  >
  > - `input_price_{c,d}` = median over mappings of `input_price_{mapping,d}`
  > - `output_price_{c,d}` = median over mappings of `output_price_{mapping,d}`
  > - `blended_price_{c,d}` = (1 × `input_price_{c,d}` + 3 × `output_price_{c,d}`) / 4
  >
  > The 3:1 input-to-output weighting follows the Artificial Analysis and Epoch AI industry convention and ensures VIPI is directly comparable to existing academic and industry price-trend literature.
- **Proposed replacement (from prompt):** Full replacement of Step 4 with corrected formula `(3 × input + output) / 4` and expanded rationale citing Artificial Analysis's published methodology.
- **Status:** READY
- **Notes:** Single occurrence of the formula at line 161. The descriptive sentence at line 163 is also replaced. No other location contains this formula (confirmed via grep — see Additional References for related line 400 which references "3:1 blended ratio" and needs separate attention).

---

### Edit 2: Divisor-method calculation formula
- **Location:** Section 8.1, lines 182–196
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > ### 8.1. Calculation Formula
  >
  > For each sub-index I and trading day d, the index value is:
  >
  > ```
  > VIPI_I(d) = VIPI_I(d-1) × Σ_{c in constituents} w_c × (blended_price_{c,d} / blended_price_{c,d-1})
  > ```
  >
  > Where:
  > - `VIPI_I(d-1)` is the prior trading day's published value for sub-index I
  > - `w_c` is the constituent weight (1/N at v0.1)
  > - `blended_price_{c,d}` is the day-d blended price for constituent c (Section 7.2)
  > - The sum runs over all constituents of sub-index I
  >
  > This is a chained price-relative index, which is standard for equity and commodity benchmark indices. It preserves continuity across rebalancing dates even when constituents change, by computing each day's movement over the constituents valid for that day.
- **Proposed replacement (from prompt):** Complete replacement with divisor-method formula, divisor initialization, divisor adjustment on composition change, and chaining rule for new constituents.
- **Status:** READY
- **Notes:** This is the largest single edit. The replacement eliminates `VIPI_I(d-1)` and `blended_price_{c,d-1}` from Section 8.1. After this edit, those terms should only appear in the Additional References check (lines 187, 191 — both inside the replaced block, so they're gone). The replacement also resolves blocking issues B2 (emergency removal breaks formula) and B3 (missing chaining rule) from the Round 1 review.

---

### Edit 3: Base date change
- **Location:** Section 8.2, lines 198–204
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > ### 8.2. Base Value
  >
  > - **VIPI base:** 100.00 on March 23, 2026, 16:00:00 UTC.
  > - **VIPI-Open base:** 100.00 on March 23, 2026, 16:00:00 UTC.
  > - **VIPI-Closed base:** 100.00 on March 23, 2026, 16:00:00 UTC.
  >
  > The base date is Volt's first continuous pricing snapshot date. All three sub-indices share the same base date to enable direct relative comparison across the family.
- **Proposed replacement (from prompt):** Changes base date to April 7, 2026 with rationale (15 calendar days after first snapshot, ensures 14-day continuity and 7-day trailing median).
- **Status:** READY
- **Notes:** Three "March 23" references at lines 200, 201, 202 are inside this replaced block — they become "April 7." The front-matter reference at line 6 is handled separately by Edit 14. See Additional References for remaining "March 23" occurrences at lines 208 and 210 (handled by Edit 4).

---

### Edit 4: Inception and bootstrap window
- **Location:** Section 8.3, lines 206–210
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > ### 8.3. Historical Backfill
  >
  > VIPI v0.1 publishes a continuous series from the base date (March 23, 2026) to the current trading day at publication. Backfilled values are computed using the same methodology as live values, using the pricing data actually observed during the backfill period. Backfilled values are permanent once published; any subsequent correction to a backfilled value follows the correction protocol in Section 12.
  >
  > Constituents for the backfill period (March 23 to first rebalancing date) are selected per the inclusion criteria applied retrospectively: the inaugural VIPI v0.1 constituent set is the set of VCMIs that would have qualified under Section 5 rules as of the launch date, applied to the full backfill period.
- **Proposed replacement (from prompt):** Renames section to "Inception and Historical Record." Eliminates backfill entirely. Defines March 23–April 6 as "bootstrap window" used only for criteria evaluation. Explicitly eliminates look-ahead bias.
- **Status:** READY
- **Notes:** This replacement handles the remaining two "March 23" references at lines 208 and 210. After this edit, "March 23" will appear only in the bootstrap-window context (correct) and in the front matter (handled by Edit 14). Also replaces the "permanent once published" wording flagged in Round 1 nitpick #3.

---

### Edit 5: Small-panel qualification
- **Location:** Section 3, Principle 2, lines 54–54
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > 2. **Observed prices only.** VIPI uses prices that providers have published via their official APIs or pricing pages. No estimates, no submissions, no survey data, no projections. This is the single most important structural difference from LIBOR and is the reason VIPI is inherently less manipulable than LIBOR ever was.
- **Proposed replacement (from prompt):** Retains "observed prices only" core but qualifies the LIBOR comparison. Adds honest disclosure of small-panel vulnerability (2–4 mappings per constituent) and notes that resistance to manipulation strengthens with provider coverage.
- **Status:** READY
- **Notes:** Single occurrence at line 54. Unambiguous location.

---

### Edit 6: Trading day definition (INSERTION)
- **Location:** After the last paragraph of Section 7.1, line 145
- **Current text (anchor for insertion):**
  > Median (rather than mean) is robust to single-snapshot outliers caused by provider API glitches.
- **Action:** Insert new paragraph immediately after line 145.
- **Status:** READY
- **Notes:** Single occurrence of the anchor text. Insertion, not replacement — existing text preserved.

---

### Edit 7: Medium-confidence escalation
- **Location:** Section 7.2, Step 1, line 151
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > **Step 1 — Collect all provider mappings.** Retrieve all `provider_mappings` with `confidence = high` for this VCMI. If fewer than two `high`-confidence mappings exist, fall back to including `confidence = medium` mappings for price determination (but note the degradation in the daily calculation audit trail).
- **Proposed replacement (from prompt):** Retains original Step 1 text verbatim, then appends a new "Escalation for sustained fallback" paragraph with the 5-consecutive-day escalation rule.
- **Status:** READY
- **Notes:** Single occurrence at line 151. Unambiguous.

---

### Edit 8: License edge case (INSERTION)
- **Location:** After the last paragraph of Section 4, line 80
- **Current text (anchor for insertion):**
  > **VIPI-Reasoning** is planned for v0.2 (target: within 60 days of v0.1 public launch). Reasoning models are excluded from v0.1 because they consume substantially more tokens per task, which makes per-million-token comparisons misleading relative to non-reasoning models. Treating them as a separate index preserves per-token interpretability for v0.1.
- **Action:** Insert new "Licensing edge case" paragraph immediately after line 80.
- **Status:** READY
- **Notes:** Single occurrence of the anchor text. Insertion, not replacement.

---

### Edit 9: Provider manipulation risk (INSERTION)
- **Location:** After the last sentence of Section 11.2, line 307
- **Current text (anchor for insertion):**
  > Changes to this register are disclosed within 10 business days of arising.
- **Action:** Insert new "Provider-side manipulation risk" paragraph immediately after line 307.
- **Status:** READY
- **Notes:** Single occurrence of the anchor text at line 307. Unambiguous.

---

### Edit 10: Remove OpenRouter specific naming
- **Location:** Section 14, line 406
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > - **Data depth.** VIPI uses direct 5-minute resolution pricing from each provider's own API or pricing page. Existing references typically rely on aggregator pricing (e.g. OpenRouter) or periodic manual collection, which is lower-fidelity and slower to reflect pricing events.
- **Proposed replacement (from prompt):** Removes "(e.g. OpenRouter)" and changes "lower-fidelity" to "lower-frequency" and "slower" to "may lag."
- **Status:** READY
- **Notes:** Single occurrence at line 406. Unambiguous.

---

### Edit 11: Citation format
- **Location:** Section 10.3, line 283
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > > Arnot, J. (2026). *The Volt Inference Price Index: a methodology for benchmark construction in the AI inference market, version 0.1.* Volt HQ. volthq.dev/vipi/methodology.
- **Proposed replacement (from prompt):** Removes "version 0.1" from the title, adds "[retrieval-date]" placeholder.
- **Status:** READY
- **Notes:** Single occurrence at line 283. Unambiguous.

---

### Edit 12: Complete rewrite of Section 13
- **Location:** Section 13, lines 368–392
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):** Full section from "## 13. Sample Calculation (Illustrative)" through "VIPI-Closed (which would include Claude Sonnet but not the Llama and Qwen constituents) would be roughly flat."
- **Proposed replacement (from prompt):** Complete rewrite using:
  - Corrected 3:1 input:output formula `(3 × input + output) / 4`
  - Divisor-method calculation (matches new Section 8.1)
  - April 7 base date (matches new Section 8.2)
  - New illustrative constituent set (replaces `claude-sonnet-4.6-undisclosed` with `gpt-5-mini-undisclosed`)
  - Base-date initialization, subsequent-day calculation, and rebalancing example with divisor adjustment
  - Removes editorial commentary about VIPI-Closed
- **Status:** READY
- **Notes:** The replacement removes all references to the old chained price-relative formula. The old blended values ($0.6625, $1.225, $12.00) are replaced with new values computed under `(3 × input + output) / 4`. Verify: Llama 3.3 70B: (3 × 0.25 + 0.80) / 4 = (0.75 + 0.80) / 4 = 1.55 / 4 = 0.3875 ✓. Qwen3 235B: (3 × 0.40 + 1.50) / 4 = (1.20 + 1.50) / 4 = 2.70 / 4 = 0.6750 ✓. GPT-5 Mini: (3 × 0.15 + 0.60) / 4 = (0.45 + 0.60) / 4 = 1.05 / 4 = 0.2625 ✓. All arithmetic checks out.

---

### Edit 13: Version and date update (front matter)
- **Location:** Lines 3 and 9
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > Line 3: `**Version:** 0.1 (draft, private)`
  > Line 9: `**Last updated:** April 20, 2026`
- **Proposed replacement (from prompt):**
  > Line 3: `**Version:** 0.1.1 (draft, private)`
  > Line 9: `**Last updated:** [today's date in the format "Month DD, YYYY"]`
- **Status:** READY
- **Notes:** Two separate single-line edits. Both unambiguous. The date should resolve to the date the edit is applied (today is April 20, 2026 — but if applied later, use that date).

---

### Edit 14: Base date in front matter
- **Location:** Line 6
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > `**Base date:** March 23, 2026, 16:00:00 UTC`
- **Proposed replacement (from prompt):**
  > `**Base date:** April 7, 2026, 16:00:00 UTC`
- **Status:** READY
- **Notes:** Single occurrence at line 6. This is the last "March 23" reference that changes to "April 7" (the other two at lines 208 and 210 are handled by Edit 4's replacement, which introduces "March 23" in the new bootstrap-window context where it's correct).

---

## Additional references

After locating all 14 edits, checked for remaining references that may need attention:

### "March 23" references

| Line | Context | After edits | Action needed? |
|------|---------|-------------|----------------|
| 6 | Front matter base date | Changed to "April 7" by Edit 14 | ✅ Handled |
| 200 | Section 8.2 base value | Replaced by Edit 3 (changes to "April 7") | ✅ Handled |
| 201 | Section 8.2 base value | Replaced by Edit 3 | ✅ Handled |
| 202 | Section 8.2 base value | Replaced by Edit 3 | ✅ Handled |
| 208 | Section 8.3 backfill | Replaced by Edit 4 — new text uses "March 23" in bootstrap-window context (first continuous snapshot date) | ✅ Correct usage, no change needed |
| 210 | Section 8.3 backfill | Replaced by Edit 4 — eliminated entirely | ✅ Handled |

**Verdict:** All "March 23" references are either changed to "April 7" or correctly retained as the first-snapshot/bootstrap-window date. No stale references remain.

### "1 × input" references

| Line | Context | After edits | Action needed? |
|------|---------|-------------|----------------|
| 161 | Section 7.2 formula | Replaced by Edit 1 (formula becomes `3 × input + output`) | ✅ Handled |

**Verdict:** No remaining "1 × input" references after Edit 1.

### "VIPI_I(d-1)" and "blended_price_{c,d-1}" references

| Line | Context | After edits | Action needed? |
|------|---------|-------------|----------------|
| 187 | Section 8.1 formula | Inside replaced block (Edit 2 — divisor method) | ✅ Handled |
| 191 | Section 8.1 "Where" list | Inside replaced block (Edit 2) | ✅ Handled |
| 382 | Section 13 daily return | Inside replaced block (Edit 12) | ✅ Handled |
| 384–386 | Section 13 return computations | Inside replaced block (Edit 12) | ✅ Handled |
| 388–390 | Section 13 "prior-day" references | Inside replaced block (Edit 12) | ✅ Handled |

**Verdict:** All `d-1` references in Sections 8.1 and 13 are inside replaced blocks. No stale references remain.

### Additional reference: "3:1 blended ratio" in Section 14

| Line | Context | After edits | Action needed? |
|------|---------|-------------|----------------|
| 400 | "**3:1 blended ratio** matches Artificial Analysis and Epoch AI conventions" | **NOT touched by any of the 14 edits** | ⚠️ **Needs attention** |

**Issue:** Line 400 says "3:1 blended ratio" which, after Edit 1, should read "3:1 input-to-output blended ratio" or be reworded for clarity. Under the corrected formula (3 × input + 1 × output) / 4, the ratio IS 3:1 input:output — so "3:1 blended ratio" is technically correct but could be read ambiguously without the "input-to-output" qualifier.

**Recommendation:** Add a 15th edit to change line 400 from:
> `- **3:1 blended ratio** matches Artificial Analysis and Epoch AI conventions, enabling direct comparison of VIPI to their published price trends.`

to:
> `- **3:1 input-to-output blended ratio** matches the Artificial Analysis published methodology ("a 3:1 ratio of input to output tokens") and Epoch AI conventions, enabling direct comparison of VIPI to existing price-trend literature.`

This is not blocking for Step 2 but should be addressed.

### Additional reference: "Blended VIPI (3:1 weighted; the headline)" in Section 8.4

| Line | Context | After edits | Action needed? |
|------|---------|-------------|----------------|
| 216 | Section 8.4 published values | **NOT touched by any of the 14 edits** | ⚠️ **Needs attention** |

**Issue:** Line 216 says "Blended VIPI (3:1 weighted; the headline)." This is correct under the new formula (input IS weighted 3:1 over output), but the parenthetical is terse. Consider expanding to "(3:1 input-to-output weighted; the headline)" for consistency with the Edit 1 rationale text.

**Recommendation:** Minor wording change. Not blocking.

---

## Summary

All 14 edits are located, unambiguous, and ready to apply. Zero ambiguous edits. No missing targets. The replacement text has been verified against the source where arithmetic is involved (Edit 12 blended-price calculations all check out).

**Two additional references** in Sections 8.4 (line 216) and 14 (line 400) use "3:1" phrasing that should be reviewed for consistency with the corrected formula direction. Neither is blocking — the "3:1" label is technically correct under the new formula (input IS weighted 3x), but adding "input-to-output" as a qualifier prevents any reader ambiguity. Recommend addressing as Edits 15 and 16 if Jack wants full consistency, or deferring to a future pass.

**Ready for Step 2.** Jack should review this file, confirm the 14 edit locations are correct, decide whether to add Edits 15–16 for the Section 8.4/14 references, and then issue the Step 2 prompt to apply all edits to the methodology document.
