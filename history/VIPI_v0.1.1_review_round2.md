# VIPI v0.1.1 Round 2 Pressure-Test

## Summary
- Blockers: **2**
- Non-blockers: **5**
- Nitpicks: **4**
- Publication verdict: **NOT READY** — two arithmetic errors in Section 13 and a gap in emergency-removal divisor handling in Section 9.3.

---

## Blockers

### Blocker 1: Section 13 — Divisor-initialization arithmetic uses wrong formula

- **Section:** 13, line 410
- **Quoted text:**
  > `D(base) = weighted_sum / 100 = 0.441667 / 100 = 0.00441667`
- **Problem:** The formula in Section 8.1 (line 193) is `VIPI_I(d) = (Σ w_c × blended_price) / D_I(d)`. To make VIPI(base) = 100: `100 = weighted_sum / D`, so `D = weighted_sum / 100`. The arithmetic here is correct: 0.441667 / 100 = 0.00441667. **This specific step checks out.**

  However, the verification calculation for `weighted_sum` is wrong. Line 406:
  > `weighted_sum = (1/3)(0.3875 + 0.6750 + 0.2625) = 1.3250 / 3 = 0.441667`

  Check: 0.3875 + 0.6750 + 0.2625 = 1.3250. 1.3250 / 3 = 0.441667. **This is correct.**

  Now check the blended prices themselves (line 398-400):
  - Llama: (3 × 0.25 + 0.80) / 4 = (0.75 + 0.80) / 4 = 1.55 / 4 = **0.3875** ✅
  - Qwen: (3 × 0.40 + 1.50) / 4 = (1.20 + 1.50) / 4 = 2.70 / 4 = **0.6750** ✅
  - GPT-5 Mini: (3 × 0.15 + 0.60) / 4 = (0.45 + 0.60) / 4 = 1.05 / 4 = **0.2625** ✅

  Base-date arithmetic: **All correct.**

  Now check the subsequent-day calculation (line 418-422):
  - GPT-5 Mini new blended: (3 × 0.15 + 0.50) / 4 = (0.45 + 0.50) / 4 = 0.95 / 4 = **0.2375** ✅
  - weighted_sum_new = (1/3)(0.3875 + 0.6750 + 0.2375) = 1.3000 / 3 = **0.433333** ✅
  - VIPI = 0.433333 / 0.00441667 = **98.11** — let me verify: 0.433333 / 0.00441667 = 98.113... ≈ **98.11** ✅

  Now the rebalancing example (lines 426-432):
  - Claude Haiku 5 blended: (3 × 1.00 + 5.00) / 4 = (3.00 + 5.00) / 4 = 8.00 / 4 = **2.00** ✅
  - New weighted_sum = (1/3)(0.3875 + 0.6750 + 2.00) = 3.0625 / 3 = **1.020833** ✅
  - D_new = 1.020833 / 98.11 = **0.010405** — let me verify: 1.020833 / 98.11 = 0.010405... ✅
  - Check: VIPI at change = 1.020833 / 0.010405 = 98.11 ✅

  **All arithmetic in Section 13 is correct. Blocker 1 is CLEARED.**

*Self-correction: I initially flagged this as a blocker during the review process, but upon completing full verification, all arithmetic checks out. Downgrading — this is NOT a blocker. See revised blocker count below.*

### Blocker 1 (actual): Section 9.3 — Emergency removal still says "does not trigger a re-weighting" but divisor method makes this ambiguous

- **Section:** 9.3, line 272
- **Quoted text:**
  > "Emergency removals are disclosed with rationale, take effect at the next trading day's close, and the vacated slot is left empty until the next scheduled rebalancing (reduced N for that cycle is accepted; does not trigger a re-weighting of remaining constituents)."
- **Problem:** Under the old chained-price-relative formula (v0.1), "does not trigger a re-weighting" broke the formula because weights summed to <1. The v0.1.1 divisor method (Section 8.1) fixes this differently: Section 8.1 says "On every constituent change (scheduled rebalancing per Section 9.2 or emergency removal per Section 9.3), the divisor is adjusted to preserve index continuity." This means the divisor IS adjusted on emergency removal, which DOES preserve continuity even with reduced N.

  But Section 9.3 still says "does not trigger a re-weighting of remaining constituents." Under the divisor method, the question is: after an emergency removal, do remaining constituents keep weight 1/N_original or become 1/(N-1)? Section 9.3 says no re-weighting (keep 1/N_original). Section 8.1 says divisor adjusts. These are compatible — you can keep weights at 1/N_original and adjust the divisor — but the result is that the sum of weights is N-1/N (less than 1), which means the index is structurally dampened: it captures only (N-1)/N of the market movement.

  Under standard index practice (S&P 500), when a constituent is removed, the remaining constituents are re-weighted OR a divisor adjustment absorbs the change such that the index value is unchanged and subsequent movements reflect full market exposure. Section 8.1's divisor adjustment ensures value continuity at the moment of removal, but if weights aren't updated, subsequent days still show dampened movement.

  **The contradiction:** Section 8.1 promises "ensures continuity across composition changes." Section 9.3 says "does not trigger a re-weighting." The divisor adjustment preserves the *level* at the change point but not the *sensitivity* going forward. If you go from 20 to 19 constituents at 1/20 weight each, the index only captures 19/20 = 95% of subsequent price movements. This is a real methodological flaw, not a theoretical one.

- **Why blocking:** An IOSCO auditor or quantitative reviewer will immediately identify that the index loses sensitivity on every emergency removal. After 3 emergency removals (N drops from 20 to 17), the index captures only 85% of actual market movements. This is not disclosed and contradicts Section 8.1's continuity promise.

- **Suggested fix:** Either:
  (a) Change Section 9.3 to say emergency removals DO trigger re-weighting to 1/(N-1), with a divisor adjustment to preserve continuity. This is the S&P 500 approach.
  OR
  (b) Keep the no-re-weighting rule but explicitly disclose: "The reduced-N basket captures only (N-remaining)/N_original of subsequent market movements. This dampening effect is accepted as a conservative choice that avoids mid-cycle weight changes and is corrected at the next scheduled rebalancing."
  Option (a) is cleaner. Option (b) is acceptable if disclosed.

### Blocker 2: Section 13 — "corrected 3:1 input:output" phrasing implies prior version was wrong

- **Section:** 13, line 390
- **Quoted text:**
  > "The following illustrates the VIPI calculation under the divisor method using the corrected 3:1 input:output blended-price formula."
- **Problem:** The word "corrected" implies the prior formula was an error. In a published methodology document, admitting a correction in the sample calculation's opening line undermines confidence. A reader encountering v0.1.1 for the first time (without having seen v0.1) will wonder: "What was wrong before? How many other corrections are there? Can I trust this?"
- **Why blocking:** This is a presentation issue, not an arithmetic issue, but it's blocking for publication readiness. A methodology document must present itself as authoritative, not as a correction of itself. The correction history belongs in a changelog, not in the body text.
- **Suggested fix:** Remove "corrected" — change to: "The following illustrates the VIPI calculation under the divisor method using the 3:1 input:output blended-price formula." The changelog (not yet written, but planned) is where "corrected from v0.1" belongs.

---

## Non-blockers

### Non-blocker 1: Section 2 cross-reference error — "see Section 4 for fallback rules"

- **Section:** 2, line 40
- **Quoted text:**
  > "A constituent's price on a given day is derived from `provider_mappings` with `confidence = high` (see Section 4 for fallback rules)."
- **Problem:** The fallback rules (high → medium confidence) are in Section 7.2 Step 1, not Section 4. Section 4 is "The Index Family" which has no fallback rules.
- **Suggested fix:** Change "Section 4" to "Section 7.2."

### Non-blocker 2: Section 5 references "Section 8" for retention criteria, but retention is in Section 9.2

- **Section:** 5, line 105
- **Quoted text:**
  > "This discretion is exercised consistent with the published retention criteria in Section 8"
- **Problem:** Retention criteria (the S&P 500-style retention-bias review) are defined in Section 9.2 step 4, not Section 8. Section 8 is "Index Calculation."
- **Suggested fix:** Change "Section 8" to "Section 9.2."

### Non-blocker 3: Section 9.3 references "Section 10 of VCMI spec" — should be Section 10 of VCMI v0.1.2

- **Section:** 9.3, line 268
- **Quoted text:**
  > "The constituent's VCMI is retracted (Section 10 of VCMI spec)"
- **Problem:** The VCMI v0.1.2 spec's retraction rules are in Section 10. But the cross-reference doesn't pin the VCMI version. If VCMI v0.3 renumbers its sections, this reference breaks. Given that VIPI's front matter already pins VCMI v0.1.2, this is a minor versioning hygiene issue.
- **Suggested fix:** Change to "retracted (per VCMI v0.1.2 Section 10)" for precision.

### Non-blocker 4: Section 15 title says "Out of Scope for v0.1" but document is v0.1.1

- **Section:** 15, line 463
- **Quoted text:**
  > `## 15. Out of Scope for v0.1`
- **Problem:** Minor version mismatch. The document version is 0.1.1 but this section header says "v0.1." The out-of-scope items are the same, so this is cosmetic.
- **Suggested fix:** Change to "Out of Scope for v0.1.x" or leave as-is if the intent is that these items span the entire v0.1 series.

### Non-blocker 5: Section 545 footer says "VIPI v0.1 is a draft methodology" — should say v0.1.1

- **Section:** Footer, line 545
- **Quoted text:**
  > "*VIPI v0.1 is a draft methodology.*"
- **Problem:** Version mismatch with front matter (v0.1.1).
- **Suggested fix:** Change to "VIPI v0.1.1 is a draft methodology."

---

## Nitpicks

1. **Line 29:** "VIPI v0.1 covers..." — should this be "VIPI v0.1.1" for consistency? Or is "v0.1" the intended series label?

2. **Line 169:** The Artificial Analysis URL is written as plain text (`artificialanalysis.ai/methodology`) without a markdown link or explicit `https://` prefix. In a formal methodology document, all URLs should be complete and linkified.

3. **Line 396:** Table header says `Blended (3:1)` without the "input-to-output" qualifier that appears everywhere else. Contextually clear but inconsistent with the effort made at lines 234 and 442 to add the qualifier.

4. **Line 484:** Section 16 title says "Open Questions (v0.1 Draft)" — should be "(v0.1.1 Draft)" for consistency.

---

## IOSCO Principle Walkthrough

| # | Principle | Assessment | Notes |
|---|-----------|-----------|-------|
| 1 | Overall Responsibility | **Pass** | Section 11.1 names Administrator, defines responsibilities. |
| 2 | Oversight of Third Parties | **Pass (N/A)** | No third-party submitters. Correctly noted. |
| 3 | Conflicts of Interest | **Pass** | Section 11.2 has conflicts register. Section 11.2 addendum addresses provider-side risk. |
| 4 | Control Framework | **Partial** | "Internal documented" — no specific controls described beyond record retention and audit trail. Acceptable for v0.1 private draft; needs strengthening for v1.0. |
| 5 | Internal Oversight | **Partial** | Single maintainer. Advisory Group planned for v1.0. Acceptable for private phase. |
| 6 | Benchmark Design | **Pass** | Sections 3–9 comprehensively describe design. Divisor method, MOC window, inclusion criteria, rebalancing — all present. |
| 7 | Data Sufficiency | **Pass** | Observed prices from 9 providers, 5-minute resolution, quality_flag filtering, outlier exclusion. Strong. |
| 8 | Hierarchy of Inputs | **Pass** | Primary: high-confidence mappings. Fallback: medium-confidence. Clearly documented in Section 7.2 Step 1. |
| 9 | Transparency | **Pass** | This document IS the transparency. Full methodology, full formula, full sample calculation. |
| 10 | Periodic Review | **Pass** | Annual methodology review committed. |
| 11 | Content of Methodology | **Pass** | All IOSCO-required content elements present: definition, scope, calculation, data sources, outlier rules, changes. |
| 12 | Changes to Methodology | **Pass** | Section 12.3: 30-day notice, 14-day consultation, changelog, permanent URLs. |
| 13 | Transition | **Fail (acknowledged)** | Cessation plan not written. Section 17 honestly says "To-be-written cessation plan; Required before v1.0." Acceptable for private draft but must be present before any public claim of IOSCO adherence. |
| 14 | Submitter Code of Conduct | **Pass (N/A)** | No submitters. |
| 15 | Internal Controls | **Partial** | Documented but not audited. Planned for v1.0. |
| 16 | Complaints Procedure | **Partial** | Email intake (jack@volthq.dev). Informal for v0.1. Formal process at v1.0. |
| 17 | Audits | **Pass** | 5-year record retention (Section 11.4). External audit committed for v1.0 (Section 11.5). Daily audit trail (Section 10.1). |
| 18 | Audit Trail | **Pass** | Daily audit record includes constituents, mappings, observation counts, outlier exclusions. Comprehensive. |
| 19 | Cooperation with Authorities | **Pass** | "Open policy" stated. |

**Summary:** 13 Pass, 3 Partial, 1 Fail (acknowledged), 2 N/A. The Partial and Fail items are all honestly acknowledged in Section 17 with v1.0 commitments. No principle is claimed as met that isn't actually met. This is strong for a private draft.

---

## LIBOR Manipulation Stress Test

### Worst-case adversarial scenario

**Setup:** VIPI is adopted as a settlement reference for inference-token futures. Provider X serves 3 VIPI constituents and is one of only 3 high-confidence mappings for each. Provider X wants VIPI to move up to profit from a long futures position.

**Attack:**
1. On the day before futures settlement, Provider X raises its published per-token prices by 50% on all 3 constituents at 15:50 UTC (5 minutes before the MOC window opens).
2. Each constituent has 3 providers. Provider X's price is now an outlier for the current snapshot but NOT yet for the 7-day trailing median (the new price has only been observed for 1 snapshot).
3. Under Section 7.3 outlier rules: the 50% increase exceeds 3× the 7-day trailing median? Only if 1.5× current > 3× median. If the trailing median is approximately the current price (stable market), then 1.5× current = 1.5× median, which is BELOW the 3× threshold. **The outlier rule does not trigger.**
4. The MOC window captures Provider X's inflated price at 15:55 and 16:00. The constituent's price is the median across 3 providers. With 3 providers: [normal, normal, inflated]. Median = normal. **Attack fails.**

**But with 2 providers:**
If Provider X is one of only 2 mappings for a constituent: [normal, inflated]. Median = (normal + inflated) / 2. **Attack partially succeeds — the index moves by 25% of the manipulation amount.** With equal weighting across 20 constituents and the attack on 3 of them, the headline VIPI moves by approximately 3/20 × 25% = 3.75% × manipulation percentage.

**With provider collusion (2 of 3):**
If Provider X colludes with Provider Y (2 of 3 mappings): [inflated, inflated, normal]. Median = inflated. **Attack fully succeeds for that constituent.** Impact on headline: 3/20 × 50% manipulation = 7.5% index movement.

### Does Section 11.2 + Principle 2 defeat this?

**Partially.** The three mitigations in Section 11.2:
- (a) "Manipulation would require offering suboptimal prices to all customers" — **TRUE for price increases** (provider overcharges everyone). For price decreases (shorting futures), manipulation requires undercharging everyone, which costs the provider real revenue. This asymmetry is not noted.
- (b) "Median limits any single provider's influence" — **TRUE for 3+ providers, FALSE for 2-provider constituents.** The spec allows constituents with as few as 2 providers (Section 5 criterion 3).
- (c) "Warrants dedicated monitoring" — deferred to v1.0. **No protection today.**

**Verdict:** The LIBOR protections are honest and mostly adequate for a non-settlement reference rate. The 2-provider vulnerability is real but disclosed (Principle 2 amendment in v0.1.1). If VIPI ever becomes a settlement reference, the minimum provider count for inclusion should be raised from 2 to 3. This is a non-blocker for v0.1.1 because the spec explicitly says "Users evaluating VIPI's suitability as a settlement reference should account for the current panel size."

---

## Revised Summary

After completing full arithmetic verification (all Section 13 math is correct), the blocker count is revised:

- **Blockers: 2** (emergency-removal dampening in Section 9.3; "corrected" language in Section 13 opening)
- **Non-blockers: 5** (cross-reference errors in Sections 2 and 5; VCMI section pinning; version mismatches in Sections 15 and 545)
- **Nitpicks: 4** (URL formatting, table header consistency, version labels)
- **Publication verdict: NOT READY** — Blocker 1 (Section 9.3 emergency removal creates undisclosed dampening under divisor method) is a genuine methodological flaw. Blocker 2 (the word "corrected" in Section 13) is a 5-second fix but important for credibility. Both must be resolved before external review.

### Path to READY

1. Fix Section 9.3: either re-weight to 1/(N-1) on emergency removal (cleanest) or add explicit dampening disclosure.
2. Remove the word "corrected" from Section 13 line 390.
3. Fix cross-references: Section 2 ("Section 4" → "Section 7.2"), Section 5 ("Section 8" → "Section 9.2").
4. Fix version labels: Section 15 heading, line 545 footer.
5. Write a v0.1.1 changelog (currently missing — the v0.1 → v0.1.1 changes are documented in the Codex review but not in the methodology document itself).

After those 5 items, the document is publication-ready for private-phase external review.
