# VIPI v0.1.1 → v0.1.2 Codex Review (Step 1)

## Status
- Edits located: **7/7**
- Ambiguous edits: **0**

---

## Edit-by-edit review

### Edit 1: Emergency removal re-weighting (Section 9.3)
- **Location:** Section 9.3, line 272
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > Emergency removals are disclosed with rationale, take effect at the next trading day's close, and the vacated slot is left empty until the next scheduled rebalancing (reduced N for that cycle is accepted; does not trigger a re-weighting of remaining constituents).
- **Proposed replacement (from prompt):**
  > Emergency removals are disclosed with rationale and take effect at the next trading day's close. On removal, the remaining constituents are re-weighted equally to 1/(N−1) each (where N is the pre-removal constituent count), and the divisor is adjusted per Section 8.1 to preserve index continuity at the removal close. The vacated slot is filled at the next scheduled rebalancing per Section 9.2, at which point all constituents return to 1/N weights with a further divisor adjustment. This procedure follows standard index-maintenance practice for equal-weight benchmarks (e.g. S&P 500 Equal Weight delisting handling) and ensures the index continues to capture 100% of market movement between emergency events and scheduled rebalancings.
- **Status:** READY
- **Notes:** Single occurrence at line 272. Unambiguous. This resolves Round 2 Blocker 1 (emergency removal dampening).

---

### Edit 2: Remove "corrected" from Section 13 opening
- **Location:** Section 13, line 390
- **Current text matches expected:** Yes
- **Current text (quoted verbatim):**
  > The following illustrates the VIPI calculation under the divisor method using the corrected 3:1 input:output blended-price formula.
- **Proposed replacement (from prompt):**
  > The following illustrates the VIPI calculation under the divisor method using the 3:1 input:output blended-price formula.
- **Status:** READY
- **Notes:** Single occurrence of "corrected" in this context at line 390. The word "corrected" also appears at lines 356, 362, and 364 in Section 12.1 (Corrections) where it refers to VIPI value corrections, not to methodology fixes — those occurrences are correct and should NOT be changed. See Additional References below.

---

### Edit 3: Section 2 cross-reference ("Section 4" → "Section 7.2")
- **Location:** Section 2, line 40
- **Current text (quoted verbatim):**
  > - A constituent's price on a given day is derived from `provider_mappings` with `confidence = high` (see Section 4 for fallback rules).
- **Proposed replacement:**
  > - A constituent's price on a given day is derived from `provider_mappings` with `confidence = high` (see Section 7.2 for fallback rules).
- **Status:** READY
- **Notes:** Single occurrence at line 40. The fallback from `high` to `medium` confidence is documented in Section 7.2 Step 1 (line 155). Section 4 is "The Index Family" — no fallback rules there.

---

### Edit 4: Section 5 cross-reference ("Section 8" → "Section 9.2")
- **Location:** Section 5, line 105
- **Current text (quoted verbatim):**
  > This discretion is exercised consistent with the published retention criteria in Section 8 and is always disclosed in the rebalancing announcement.
- **Proposed replacement:**
  > This discretion is exercised consistent with the published retention criteria in Section 9.2 and is always disclosed in the rebalancing announcement.
- **Status:** READY
- **Notes:** Single occurrence at line 105. The retention-bias review (S&P 500-style rule) is defined in Section 9.2 step 4 (lines 257–260). Section 8 is "Index Calculation" — no retention criteria there.

---

### Edit 5: Section 9.3 VCMI version pin
- **Location:** Section 9.3, line 268
- **Current text (quoted verbatim):**
  > - The constituent's VCMI is retracted (Section 10 of VCMI spec) due to discovering the underlying model does not exist or was a phantom listing.
- **Proposed replacement:**
  > - The constituent's VCMI is retracted (Section 10 of VCMI v0.1.2 spec) due to discovering the underlying model does not exist or was a phantom listing.
- **Status:** READY
- **Notes:** Single occurrence at line 268. Pins the cross-reference to the specific VCMI version that VIPI depends on (per front matter line 8: "Dependency: Volt Canonical Model Identifier (VCMI) v0.1.2 or later").

---

### Edit 6: Section 15 version reference
- **Location:** Section 15 heading, line 463
- **Current text (quoted verbatim):**
  > ## 15. Out of Scope for v0.1
- **Proposed replacement:**
  > ## 15. Out of Scope for v0.1.1
- **Status:** READY
- **Notes:** Single occurrence at line 463. The heading uses "v0.1" while the document is v0.1.1 (and will become v0.1.2). Updating to "v0.1.1" matches the current methodology version. Alternatively, could use "v0.1.x" to cover the entire minor series — but "v0.1.1" is more precise and matches the front-matter version at the time this edit was planned.

---

### Edit 7: Footer version reference
- **Location:** Footer, line 545
- **Current text (quoted verbatim):**
  > *VIPI v0.1 is a draft methodology. It will evolve through private-phase review before public launch. Feedback from academic, industry, and regulatory reviewers is encouraged and will shape the v1.0 methodology. Substantive changes across versions are documented in the changelog.*
- **Proposed replacement:**
  > *VIPI v0.1.1 is a draft methodology. It will evolve through private-phase review before public launch. Feedback from academic, industry, and regulatory reviewers is encouraged and will shape the v1.0 methodology. Substantive changes across versions are documented in the changelog.*
- **Status:** READY
- **Notes:** Single occurrence at line 545 (last line of file). Only the first "v0.1" in the line changes to "v0.1.1"; the "v1.0" reference later in the same sentence is correct and unchanged.

---

## Additional references

### Remaining "v0.1" references (not followed by ".1" or ".2")

After Edits 6 and 7, the following "v0.1" references remain in the file. Each is reviewed for whether it should also be updated:

| Line | Text snippet | Should change? | Reasoning |
|---|---|---|---|
| 29 | "VIPI v0.1 covers..." | **No** | Refers to the v0.1 series label, not the specific patch version. |
| 54 | "the v0.1 provider panel" | **No** | Generic series reference. |
| 68 | "## 4. The Index Family (v0.1)" | **No** | Section heading — v0.1 is the family launch version. |
| 70 | "VIPI v0.1 publishes three indices" | **No** | Series reference. |
| 80 | "within 60 days of v0.1 public launch" | **No** | Refers to the first public launch, which is the v0.1 series. |
| 103 | "excluded... at v0.1" | **No** | Policy scope. |
| 197 | "1/N at v0.1" | **No** | Methodology scope. |
| 224 | "VIPI v0.1 has no backfilled historical period" | **No** | Series reference. |
| 309 | "private phase (v0.1 through...)" | **No** | Phase range. |
| 315 | "same party at v0.1" | **No** | Series reference. |
| 392 | "actual v0.1 basket" | **No** | Series reference. |
| 449 | "9 providers at v0.1 launch" | **No** | Series reference. |
| 456 | "VIPI v0.1 is unadjusted" | **No** | Series reference. |
| 480 | "out of scope for v0.1" | **No** | Covered by Edit 6 for the heading; body text is fine as series label. |
| 484 | "## 16. Open Questions (v0.1 Draft)" | **Consider** | Could update to "v0.1.1 Draft" for consistency with Edit 6, but "v0.1" is also defensible as the series label. |
| 492 | "keep v0.1" | **No** | Series reference. |
| 508 | "VIPI v0.1 is designed..." | **No** | Series reference. |
| 510 | "v0.1 status" (IOSCO table column) | **No** | Table header — v0.1 status column is the series-level assessment. |

**Verdict:** All remaining "v0.1" references are series-level labels (referring to the v0.1.x family, not the specific patch). They are correct as-is. Only Edits 6 and 7 need "v0.1" → "v0.1.1" changes. Line 484 (Section 16 heading) could optionally be updated but is not required.

### "Section 4" and "Section 8" in cross-reference contexts

| Line | Text | Covered by edit? |
|---|---|---|
| 40 | "see Section 4 for fallback rules" | **Yes — Edit 3** |
| 105 | "retention criteria in Section 8" | **Yes — Edit 4** |

No other "Section 4" or "Section 8" cross-references found in potentially incorrect contexts.

### Other "corrected" occurrences

| Line | Text | Covered by edit? | Should change? |
|---|---|---|---|
| 356 | "A constituent's VCMI metadata is retroactively corrected" | No | **No** — refers to VCMI corrections, not methodology corrections. Correct usage. |
| 362 | "the corrected value are both disclosed" | No | **No** — refers to disclosing corrected VIPI values. Correct usage. |
| 364 | "directed to the corrected value as authoritative" | No | **No** — same context. Correct usage. |
| 390 | "using the corrected 3:1 input:output" | **Yes — Edit 2** | Remove "corrected." |

**Verdict:** Only line 390 uses "corrected" in the problematic sense (implying the methodology itself was wrong). Lines 356, 362, 364 use "corrected" correctly in the Section 12.1 error-correction protocol.

---

## Summary

All 7 edits located unambiguously. Zero ambiguities. No missing targets. No collateral references that need attention beyond what the 7 edits cover.

**Recommendation: READY for Step 2.** Jack should issue the Step 2 prompt to apply all 7 edits, plus update front-matter version from 0.1.1 → 0.1.2 and last-updated date (which is not among the 7 edits but will be needed — suggest adding as Edit 8 and Edit 9 in the Step 2 prompt).
