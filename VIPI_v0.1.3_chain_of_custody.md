# Volt Inference Price Index (VIPI) v0.1.3 — Chain of Custody

**Version:** 0.1.3 (snapshot, version-pinned)
**License:** CC-BY-4.0
**Administrator:** Volt HQ (volthq.dev)
**Status:** Public draft. Snapshot of v0.1.3 methodology provenance as of inaugural-basket commit and post-publication errata reissue. Subsequent methodology versions (v0.1.4 and later) have their own chain-of-custody documents under methodology §12.3.
**Pinned methodology version:** vipi-0.1.3
**Base date:** May 18, 2026, 16:00:00 UTC
**Base value:** 100.00
**Last updated:** May 20, 2026 (errata reissue under same atomic commit as this document's initial publication)
**IOSCO correspondence:** Principle 9 (transparency of methodology) — full chain disclosure; Principle 13 (transition) — version-pinned to vipi-0.1.3, future versions disclosed in their own chain-of-custody files; Principle 17 (audits) — 5-year retention per methodology §11.5.

---

## §1. Purpose

This document is the chain-of-custody record for VIPI v0.1.3. It enumerates every git commit on `origin/main` that materially contributed to the published v0.1.3 methodology surface — the methodology document, the curation policy, the pre-registration, and the reproducibility appendix — together with the inaugural-basket commit and the publication commits, in commit-timestamp order.

Its purpose is the reproducibility requirement: a reviewer who has zero context but a clone of the repository can reconstruct the exact provenance of every v0.1.3 published value by running the verification commands in §10.

This document does NOT replace the reproducibility appendix (commit `f173293`, errata-reissued under commit `9e81ac935cd68eaae0437ec42ab4567d04506af8` on 2026-05-20). The appendix establishes the post-Cluster-4 amendment chain that defends rules-finalized-before-basket-selection. This chain-of-custody document is broader: it covers v0.1.3 evolution from the pre-registration anchor through the inaugural-basket commit and publication, including the pre-Cluster-4 substantive methodology-surface commits that the appendix's post-Cluster-4 framing does not enumerate but which are part of v0.1.3's full provenance.

---

## §2. Scope

This document covers VIPI methodology version 0.1.3 only. The v0.1.3 published methodology surface consists of four files in `~/volthq/docs/vipi/`:

- `VIPI_v0.1.3_methodology.md`
- `VIPI_v0.1.3_curation_policy.md`
- `VIPI_v0.1.3_pre_registration.md`
- `VIPI_v0.1.3_reproducibility_appendix.md`

Together with the inaugural-basket migration (`packages/workers/vipi-cron/migrations/0009_seed_vipi_basket_inaugural.sql`) and the implementation-plan file (`docs/vipi/VIPI_implementation_plan.md`, internal not-for-publication).

Commits since the pre-registration anchor (`98ab34b`) that touched non-methodology infrastructure (price-aggregator adapters, VCMI registry expansions, worker scaffolding, schema migrations to other tables, dashboard refactors, operational tooling) are out of scope here and disclosed at category level in §9 ("Out-of-scope-but-verifiable"). A reviewer running the §9 enumeration command will find such commits in history; their omission from this document is intentional and bounded.

The total primary entries enumerated below: **20**.

| Category | Section | Count |
|---|---|---|
| Pre-registration anchor | §3 | 1 |
| Pre-Cluster-4 substantive methodology-surface evolution | §4 | 5 |
| Post-Cluster-4 amendment chain | §5 | 6 |
| Plan-sync commits (internal not-for-publication) | §6 | 5 |
| Inaugural basket | §7 | 1 |
| Publication commit | §8.1 | 1 |
| Publication infrastructure commit | §8.2 | 1 |
| **Total primary entries** | | **20** |

Plus **2 clerical placeholder-fill companions** disclosed parenthetically (`45398cd` in §3.1, `9dbe845` in §4.1), bringing the total SHA count disclosed in §3 through §8 to **22**. The clerical companions are not double-counted in the 20-primary total because their normative content is zero — they record the parent commit's own SHA inside the document body.

The errata reissue commit (`9e81ac935cd68eaae0437ec42ab4567d04506af8`, retrospective per §5.2 and §8.3) is documented but does not count toward the 22-SHA total because it post-dates the original publication event and is disclosed retrospectively.

---

## §3. Pre-Registration Anchor

A single commit fixes the pre-registration as the binding contract for v0.1.3 selection, compute, halt, and correction rules. This commit pre-dates every methodology-surface amendment that follows.

### §3.1. The anchor commit

**`98ab34b`** — Mon Apr 27 18:36:33 2026 — *"VIPI v0.1.3 pre-registration: lock eligibility/selection/compute/halt/correction at c520214"*

Files touched (per `git show --stat 98ab34b`): `docs/vipi/VIPI_v0.1.3_pre_registration.md` only.

This commit publishes the pre-registration document, anchored at parent commit `c520214` (the immediately-preceding plan commit, which adds the `confidence_fallback_constituents` audit key per §7.2 Step 1). The pre-registration is the binding contract: it declares the rules that govern selection, compute, halt, and correction for v0.1.3 at the moment of anchoring, with explicit allowance for subsequent pre-base-date revisions visible in git history.

**Clerical companion (parenthetical):** Commit **`45398cd`** (Mon Apr 27 18:40:19 2026, *"VIPI v0.1.3 pre-registration: fill commit hash placeholder"*) is a placeholder-fill commit that backfills a commit-hash reference inside the pre-registration. It contains no normative content; the placeholder it fills was an intentionally-empty field in `98ab34b`. Disclosed here for completeness; classified as clerical per diff-content review.

---

## §4. Pre-Cluster-4 Substantive Methodology-Surface Evolution

Between the pre-registration anchor (April 27) and the Cluster 4 amendment window (May 14 onward), five substantive commits modified the published methodology-surface files. These commits are not part of the post-Cluster-4 amendment chain disclosed in the reproducibility appendix; they predate it. They are nevertheless substantive — each adds, removes, or rewrites a normative rule, threshold, or section — and therefore form part of the v0.1.3 chain of custody.

Their disclosure here is what makes the rules-anchored-prior-to-selection chain complete: the rules in force at the May 18 inaugural-basket commit (`9d8d908`) reflect both this pre-Cluster-4 evolution AND the Cluster 4 amendments (§5).

### §4.1. The five pre-Cluster-4 substantive commits

**`57bfc7f`** — Tue Apr 28 14:27:28 2026 — *"VIPI v0.1.3 curation policy: lock §5(6) rulings + confidence policy + curator-discretion principles for Session 4"*

Files touched: `docs/vipi/VIPI_v0.1.3_curation_policy.md` (initial publication, 156-line new file).

This commit publishes the initial v0.1.3 curation policy as a new document. The file contains the §5(6) rulings (Akash exclusion, provider-confidence rubric), the high/medium/low confidence policy applied to provider mappings, and the curator-discretion principles that govern Session 4 basket selection. Classified as substantive per diff-content review: this is the first publication of the curation policy's normative content for v0.1.3, not a derivation from earlier methodology text.

**Clerical companion (parenthetical):** Commit **`9dbe845`** (Tue Apr 28 14:29:18 2026, *"VIPI v0.1.3 curation policy: fill commit hash placeholder"*) backfills a commit-hash reference inside the curation policy. Disclosed here for completeness; classified as clerical.

**`ec8b3dd`** — Tue May 12 10:16:22 2026 — *"vipi(methodology): add K=2 observation-count floor to §7.2"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`.

This commit adds a substantive numeric threshold to methodology §7.2: a minimum of two observation snapshots within the MOC window is required for a (provider, model, value_kind) tuple to contribute to the daily aggregate. The K=2 floor is a halt-precondition rule (constituents below the floor trigger the §7.4 data-availability stress halt). Classified as substantive.

**`9264dcf`** — Tue May 12 10:22:27 2026 — *"vipi(methodology): label BMR Art 27(2)(e)/(f) substance + §15 deferral"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`.

This commit adds the BMR Article 27(2)(e) and 27(2)(f) substantive-coverage labels to §7.4 and §12.1/§12.2, and adds the §15 deferral notice explaining that formal BMR-procedural-taxonomy alignment is deferred to v0.1.4. The BMR labels are normative metadata: they declare which substantive coverage in the methodology corresponds to which BMR procedural sub-paragraph. Classified as substantive.

**`eca9c34`** — Wed May 13 08:24:54 2026 — *"vipi(methodology): land prose batch 1 — §3 Principle 7, §6 30% cap forward-looking, §10.4 audit-record schema, §17 P7/P8/P9 IOSCO commitments"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`.

This commit lands four substantive prose additions: (a) §3 Principle 7 (a new IOSCO-correspondence principle); (b) §6 the 30% single-provider cap, declared forward-looking from the next rebalancing; (c) §10.4 the canonical audit-record schema; (d) §17 the IOSCO P7/P8/P9 commitments. Each is a normative rule or threshold. Classified as substantive.

**`b0731a0`** — Wed May 13 09:27:33 2026 — *"vipi(methodology): land Decision H — new §11.3 Source Classification Policy + §11 renumber"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`.

This commit adds the new §11.3 Source Classification Policy (Decision H — the api_inline_price / api_catalog_gated / static_by_design / gpu_hour_derived four-value classification) and renumbers subsequent §11 subsections. The §11.3 subsection is a new normative classification rule applied at evidentiary-tier determination. Classified as substantive. The §11 renumber is a structural side-effect of the §11.3 insertion and is encompassed by this commit.

---

## §5. Post-Cluster-4 Amendment Chain

The six commits in this section are the post-Cluster-4 pre-base-date amendment chain disclosed in the reproducibility appendix (commit `f173293`, errata-reissued under `9e81ac935cd68eaae0437ec42ab4567d04506af8`). They landed between May 14 and May 17, 2026 — after the pre-Cluster-4 substantive evolution in §4 and before the inaugural-basket commit in §7. Per pre-registration L15 (post-Cluster 4) these amendments are designated as pre-base-date pre-registration revisions taking effect at the May 18 inaugural base date; they are not §12.3 methodology transitions because no v0.1.3 value had yet been computed, published, or stabilized in force at the time of authoring.

### §5.1. The six amendments

**`a255fff`** — Thu May 14 14:38:52 2026 — *"docs(vipi): amend §5(3), §5(4), §7.2 for K4 sole-issuer pathway; sync pre-reg"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`, `docs/vipi/VIPI_v0.1.3_pre_registration.md`.

§5(3) split into clause (a) multi-provider corroboration and clause (b) sole-issuer proprietary. §5(4) continuity redefined: the 95% threshold is measured against MOC closes that produced at least one clean snapshot (Administrator-side outages excluded from both numerator and denominator; provider-side absences counted as missed observations in the numerator). This is the rule under which the `claude-opus-4.7-undisclosed` 4-day Administrator-side carve-out in migration 0009 (Apr 17-20 excluded from both numerator and denominator; continuity recorded as 10/10 over the adjusted 10-day window) is authorized. §7.2 sole-issuer pathway aligned (median over a one-element set returns that element directly; no degradation labeling). Pre-registration sync against the methodology changes.

**`e14f435`** — Fri May 15 12:08:35 2026 — *"methodology: §8.2 anchor expansion + §8.4 VIPI-Best provider selection clarification"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`, `docs/vipi/working/section8_amendment_draft.md`.

§8.2 anchor expanded from 3 anchors to 12 (sub_index, value_kind) pairs; the divisor extends to `D_{I,k}`. §8.4 clarifies VIPI-Best single-provider-per-constituent provider selection.

**Disclosure:** This commit also touched `docs/vipi/working/section8_amendment_draft.md`. The `working/` subtree was added to `.gitignore` at commit `46ac7b7` after this commit; the working file is therefore present in this commit's history but excluded from a fresh clone of the working tree after `46ac7b7`. A reviewer running `git show e14f435` will see the working-file diff in this commit's history. The working file is an internal not-for-publication draft and does not form part of the published methodology surface. Disclosure here serves the reproducibility requirement that no file touched by a methodology-surface amendment commit be silently elided.

**`af88fb0`** — Fri May 15 13:19:54 2026 — *"methodology §10.4: flip audit_json schema canonicity to methodology, not plan"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`.

§10.4 had deferred the canonical audit_json schema to the implementation plan §3.6; this commit flips canonicity so the methodology document is the canonical source for the audit-record schema, and the plan references the methodology. This commit closes a normative-precedence inversion present in earlier v0.1.3 drafts.

**`1cfc899`** — Sat May 16 10:11:52 2026 — *"methodology §11.3: publish inaugural source classification rule + IOSCO P8 publication mix"*

Files touched: `docs/vipi/VIPI_v0.1.3_methodology.md`.

§11.3 inaugural source classification rule (which evidentiary sub-class applies to each pricing source on the May 18 inaugural basket) and the IOSCO Principle 8 publication mix declaration. Closes L5 from the Day 75 prelaunch unknowns report.

**Amend disclosure (per §5.3 below):** This commit was authored with `git commit --amend` in the operator's local repository before being pushed to `origin/main`. The amend event is visible in the operator's local `git reflog` at the time of authoring (verified 2026-05-19 against `origin/main` at HEAD `46ac7b7`). The post-amend SHA `1cfc899` is stable on `origin/main` and reproducible by any reader.

**`d384b1f`** — Sat May 16 10:34:17 2026 — *"curation policy: codify silent-FALLBACK exclusion rule"*

Files touched: `docs/vipi/VIPI_v0.1.3_curation_policy.md`.

Codifies the silent-FALLBACK exclusion rule into the curation policy: provider mappings exhibiting silent fallback behavior (returning a different model than requested without surfacing the substitution) are excluded from VIPI constituent eligibility, with the deviation disclosed via the §11.3 audit JSON. Closes V1 from the Day 75 prelaunch unknowns report.

**Amend disclosure (per §5.3 below):** This commit was authored with `git commit --amend` before being pushed; same disclosure pattern as `1cfc899` above.

**`5141565`** — Sun May 17 14:16:42 2026 — *"pre-reg: L15 amendment-enumeration extension + new Sub-index publication scope subsection"*

Files touched: `docs/vipi/VIPI_v0.1.3_pre_registration.md`.

Extends pre-registration L15 to enumerate the Cluster 4 amendment chain explicitly, and adds the new "Sub-index publication scope" subsection (which sub-indices are published, value-kinds per sub-index, MOC publication mix). Closes V4 from the Day 75 prelaunch unknowns report.

### §5.2. Appendix mis-enumeration disclosure

The reproducibility appendix as originally published 2026-05-18 (commit `f173293`) listed seven commits in its "Pre-base-date amendment chain" at L21-29 and L436. Direct verification via `git show --stat 9250b31` shows that `9250b31` touches only `docs/vipi/VIPI_implementation_plan.md` — an internal not-for-publication artifact — and is therefore a plan-sync commit (see §6 below), not a methodology-surface amendment.

This mis-enumeration was corrected by a documentation errata reissue of the appendix at commit `9e81ac935cd68eaae0437ec42ab4567d04506af8` on 2026-05-20. The errata block on the face of the reissued appendix discloses the prior text (seven commits including `9250b31`), the corrected text (six commits, `9250b31` removed from the amendment chain), the reason (plan-sync commits do not modify the published methodology surface), and the affected sections (L21-29 bullet and L436 numbered-list entry). The original commit `f173293` is preserved in git history; the corrected enumeration appears in the reissued appendix and in this document.

This correction is documentation errata, not a methodology §12.1 value correction. Methodology §12.1 governs corrections to published VIPI values; no published VIPI value was affected by this enumeration error. The errata is disclosed on the face of the reissued appendix per standard documentation-errata practice.

### §5.3. Amend Disclosure

Two commits in this section — `1cfc899` and `d384b1f` — were authored with `git commit --amend` in the operator's local repository before being pushed to `origin/main`. The amend events are visible in the operator's local `git reflog` at the time of authoring (verified 2026-05-19 against `origin/main` at HEAD `46ac7b7`). Post-amend, both SHAs are stable on `origin/main` and reproducible by any reader. Reflog is local-only and may not appear on a fresh clone; the disclosure here serves the reproducibility requirement that no SHA in the amendment chain be silently rewritten.

No SHA in §3 through §8 was rebased or cherry-picked between authorship and push. Rebase events present in the operator's reflog pre-date the v0.1.3 pre-registration anchor (April 27, 2026) and concern VCMI v0.1.3 Gate 2 Tier 2 commits and an earlier pull --rebase from March 2026; both are out of scope for VIPI v0.1.3 chain of custody. Verification command:

```
git reflog show --all 2>/dev/null | grep -iE 'rebase|cherry-pick|cherry'
```

---

## §6. Plan-Sync Commits in the Same Window (Internal Not-for-Publication)

The implementation plan (`docs/vipi/VIPI_implementation_plan.md`) is an internal not-for-publication artifact that documents how the methodology rules are implemented in the production VIPI cron worker. Plan-sync commits do not modify the published methodology surface; they synchronize the plan's prose to whatever the canonical methodology / curation / pre-registration text has become.

Five plan-sync commits landed in the v0.1.3 window. They are disclosed here for reproducibility — a reviewer running `git log 98ab34b^..origin/main -- docs/vipi/VIPI_implementation_plan.md` will find them — but they are not part of the methodology-surface chain in §4 or §5.

| SHA | Date | Subject |
|---|---|---|
| `97f38c4` | 2026-05-14 15:08 | docs(vipi): sync plan §3.6 to §5(3)(b) — sole-issuer constituents excluded from confidence_fallback_constituents |
| `9250b31` | 2026-05-15 13:42 | plan §3.6 + §5.2: divisor bootstrap, 12-divisor regime, VIPI-Best selection rule, audit schema reference |
| `36279e2` | 2026-05-17 20:02 | plan(vipi): §5.2 step 15 row-count sync — 18 → 12 (Decision X R1a) |
| `0cc1d0b` | 2026-05-17 20:04 | plan(vipi): §5.2 step 14 + step 15 — internal consistency follow-up |
| `7b0e49e` | 2026-05-17 20:07 | plan(vipi): §6.2 daily.json example — sync to 4-value_kind publication shape |

Each touches `docs/vipi/VIPI_implementation_plan.md` exclusively (verified via `git show --stat`). `9250b31` is the commit that the original reproducibility appendix mis-enumerated as a methodology-surface amendment; see §5.2.

---

## §7. Inaugural Basket

A single commit seeds the inaugural basket on the May 18, 2026 base date.

### §7.1. The basket commit

**`9d8d908`** — Sun May 17 18:46:35 2026 — *"migrate(vipi): 0009 seed inaugural basket (46 constituents)"*

Files touched (per `git show --stat 9d8d908`): `packages/workers/vipi-cron/migrations/0009_seed_vipi_basket_inaugural.sql` only.

This commit seeds the inaugural basket via D1 migration 0009. The basket contains **46 constituent rows** across three sub-indices, comprising **27 distinct VCMIs**:

- VIPI: 20 rows
- VIPI-Open: 20 rows (19 shared with VIPI, 1 unique: `qwen-2.5-72b/fp8`)
- VIPI-Closed: 6 rows (sole-issuer constituents)
- 1 VCMI is unique to VIPI: `deepseek-v3-671b-a37b`

The 46-vs-27 count distinction is structural: a single VCMI may be a constituent of multiple sub-indices (e.g., an open-license model appears in both VIPI and VIPI-Open). Each (sub_index, VCMI) pair is one row in `vipi_basket`. The total of **46 rows / 27 VCMIs** is verified empirically via direct read of migration 0009's INSERT statements.

This commit's timestamp (May 17 18:46:35 -0600 = May 18 00:46:35 UTC) post-dates every amendment in §4 and §5 and pre-dates the May 18 16:00:00 UTC base date by approximately 15 hours. The commit-ordering proof — every rule was fixed before basket selection — holds at the SHA level: `git log` against `origin/main` shows `9d8d908` strictly after `5141565` (the latest §5 amendment, May 17 14:16 -0600) and after every §4 substantive pre-Cluster-4 commit. The 15-hour gap between commit-timestamp and base-date is intentional: the basket is staged before the MOC window opens; first-published VIPI values are written by the 2026-05-18T16:07Z cron firing (12 `vipi_daily` rows + 3 `vipi_audit_records` rows) using the basket seeded by this commit.

*Reviewer note:* Migration 0009's SQL header (L20-21) documents the CTE temporal filters that are commented out for the inaugural eligibility-query run. The chain-of-custody document does not duplicate that explanation; the migration file is the canonical source. A reviewer reading this section should consult `packages/workers/vipi-cron/migrations/0009_seed_vipi_basket_inaugural.sql` directly for the filter rationale.

---

## §8. Publication Commits

The v0.1.3 publication event comprises one publication commit (§8.1) and one publication infrastructure commit (§8.2). A third commit (§8.3, the errata reissue) is documented retrospectively per §5.2.

### §8.1. Publication commit

**`f173293`** — Mon May 18 12:22:59 2026 — *"docs(vipi): publish reproducibility appendix for inaugural basket"*

Files touched: `docs/vipi/VIPI_v0.1.3_reproducibility_appendix.md` only.

Publishes the reproducibility appendix for the inaugural basket. This is the original-issue appendix; its mis-enumeration of `9250b31` inside the post-Cluster-4 amendment chain is disclosed and corrected per §5.2. Publication is defined narrowly per methodology — the act of publishing the methodology rules and the artifacts that document the inaugural basket's reproducibility against those rules.

### §8.2. Publication infrastructure commit

**`46ac7b7`** — Mon May 18 12:43:34 2026 — *"chore: gitignore operational notes; track appendix template"*

Files touched: `.gitignore`, `docs/vipi/VIPI_v0.1.3_reproducibility_appendix_TEMPLATE.md`.

Publication-infrastructure commit (not a publication commit): adds `docs/vipi/working/` and operational scratchpad paths to `.gitignore`; tracks the reproducibility-appendix template file (`VIPI_v0.1.3_reproducibility_appendix_TEMPLATE.md`, authored pre-base-date, kept as chain-of-custody evidence that the template predated the basket). This commit publishes neither a VIPI value nor a methodology rule; it codifies repository hygiene around the published publication commit. Reclassified from "publication" to "publication infrastructure" on 2026-05-20 per the methodology's narrow definition of publication.

### §8.3. Errata reissue (retrospective)

**`9e81ac935cd68eaae0437ec42ab4567d04506af8`** — Wed May 20 2026 — *"docs(vipi): reproducibility appendix v2 errata reissue + chain_of_custody.md initial publication"*

Files touched: `docs/vipi/VIPI_v0.1.3_reproducibility_appendix.md`, `docs/vipi/VIPI_v0.1.3_chain_of_custody.md`.

Single atomic commit landing the appendix errata reissue (per §5.2) and this chain-of-custody document. The appendix's L21-29 bullet and L436 numbered-list entry are edited to remove `9250b31` from the post-Cluster-4 amendment chain. The errata block at the top of the reissued appendix discloses prior text, corrected text, reason, and affected sections, and links to §5.2 of this document for full audit trail.

This commit is documented retrospectively under §8.3 and is not counted toward the 22-SHA total in §2: it post-dates the original May 18 publication event and is disclosed here as the corrective action, not as a constituent of the original publication event. Once this commit lands on `origin/main`, Cmd 1 in §10 will return 16 commits (the 15 enumerated at the time of original authorship plus this reissue commit). The post-publication transition is explained in §10's Cmd 1 reconciliation prose.

---

## §9. Out-of-Scope-but-Verifiable

A reviewer running

```bash
git log 98ab34b^..origin/main -- \
  docs/vipi/ packages/workers/ packages/core/
```

will find the remaining commits since the pre-registration anchor that touch paths outside the four published methodology-surface files, the implementation plan, and migration 0009. These commits touched non-methodology infrastructure and are out of scope for this chain-of-custody document but verifiable in git history. They include:

- VCMI registry expansions and provider-mapping seed migrations (e.g., `a3bc208`, `3240d7517`, registry round-2 and round-3 commits)
- Price-aggregator adapter rate refreshes (e.g., OpenAI, Hyperbolic, Groq, Fireworks pricing updates)
- D1 schema migrations to tables outside the v0.1.3 methodology surface (e.g., evidentiary_tier column, pricing_methodology column on `vcmi_provider_mappings`)
- VIPI cron worker scaffolding and Session 5 implementation commits (e.g., `ffa0041`, `2fa1406`, `35cd117`, `4a7dcbf`, `66133ae`, `ef28823`, `fe29a88`)
- CI/backup workflow additions (e.g., weekly R2 backup)
- Earlier methodology stabilization commits prior to the v0.1.3 pre-registration anchor are also outside this range (the `98ab34b^..origin/main` range excludes any commit reachable from `98ab34b^` — the anchor's parent, which is the exclusive lower bound of the range — so `a47ec41` (2026-04-26 methodology initial draft) is excluded by construction)

These commits do not modify the published v0.1.3 methodology surface (the four files in §2). They are part of the broader VIPI platform's evolution and are documented at category level here for reviewer awareness. A reviewer who wishes to enumerate them runs the command above and excludes the 22 SHAs disclosed in §3 through §8 (20 primary + 2 clerical companions).

---

## §10. Verification Commands a Reviewer Runs

The following commands, run against a fresh clone of `origin/main`, reproduce the chain of custody disclosed in this document. Each command uses the commit-range argument `98ab34b^..origin/main` ("every commit reachable from `origin/main` that is not reachable from the pre-registration anchor's parent"), which is reproducible regardless of the reviewer's machine timezone. Multi-line commands use backslash continuations; copy-paste into a shell executes each as one logical command.

**Command 1: enumerate every commit on `origin/main` since the pre-registration anchor that touched the published methodology surface.**

```bash
git log --name-only --format="=== %H %ai === %s" 98ab34b^..origin/main -- \
  docs/vipi/VIPI_v0.1.3_methodology.md \
  docs/vipi/VIPI_v0.1.3_curation_policy.md \
  docs/vipi/VIPI_v0.1.3_pre_registration.md \
  docs/vipi/VIPI_v0.1.3_reproducibility_appendix.md
```

Expected output: **15 commits** at the time of original authorship —
`98ab34b`, `45398cd`, `57bfc7f`, `9dbe845`, `ec8b3dd`, `9264dcf`, `eca9c34`, `b0731a0`, `a255fff`, `e14f435`, `af88fb0`, `1cfc899`, `d384b1f`, `5141565`, `f173293`.

Of these 15:
- **13 are §2-table primary entries** (1 pre-reg anchor + 5 pre-Cluster-4 substantive + 6 Cluster 4 amendments + 1 publication commit).
- **2 are clerical placeholder-fill companions** (`45398cd` in §3.1, `9dbe845` in §4.1) disclosed parenthetically. They are part of git history but contain no normative content; §2's primary total of 20 does not include them.

Five additional SHAs enumerated in §2 are NOT returned by Cmd 1 because they fall outside the four-file path scope:

- 5 plan-sync commits (`97f38c4, 9250b31, 36279e2, 0cc1d0b, 7b0e49e` — touch `VIPI_implementation_plan.md`) → verified via Cmd 2
- 1 inaugural-basket commit (`9d8d908` — touches migration 0009 SQL) → verified via Cmd 3
- 1 publication infrastructure commit (`46ac7b7` — touches `.gitignore` and the appendix template, which sit outside the four methodology-surface files) → verified by `git show --stat 46ac7b7` directly

**Pre-publication tally:** 15 (Cmd 1, includes 2 clerical) + 5 plan-sync (Cmd 2) + 1 basket (Cmd 3) + 1 publication infrastructure (`46ac7b7` direct) = **22**, reconciling against the 22 SHAs disclosed in §3 through §8 (20 primary + 2 clerical).

**Post-publication note:** Once the errata-reissue commit (`9e81ac935cd68eaae0437ec42ab4567d04506af8`) lands on `origin/main`, Cmd 1 will return **16 commits** instead of 15 — the 16th is the errata-reissue commit itself, which touches `VIPI_v0.1.3_reproducibility_appendix.md` (and `VIPI_v0.1.3_chain_of_custody.md`). The errata-reissue commit is documented in §8.3 retrospectively and is not double-counted in §2's primary total. Post-publication tally: 16 (Cmd 1) + 5 + 1 + 1 = 23 SHAs visible on `origin/main`, of which 22 are §3-§8 enumerated and 1 is the §8.3 retrospective.

**Command 2: enumerate every commit on `origin/main` since the pre-registration anchor that touched the implementation plan.**

```bash
git log --format="%H %ai %s" 98ab34b^..origin/main -- \
  docs/vipi/VIPI_implementation_plan.md
```

Expected output: **5 commits** — `97f38c4`, `9250b31`, `36279e2`, `0cc1d0b`, `7b0e49e`. These match the §6 plan-sync enumeration exactly.

**Command 3: verify the inaugural-basket commit.**

```bash
git show --stat 9d8d908
```

Expected output: single file changed, `packages/workers/vipi-cron/migrations/0009_seed_vipi_basket_inaugural.sql`, with 129 line insertions (46 INSERT rows plus the SQL header documenting eligibility evaluation, §5(4) Administrator-side curator override, §6 cap + tie-breaker cascade, weighting, and idempotency).

**Command 4: verify the commit-ordering claim (every methodology-surface amendment lands before the basket).**

```bash
git log --format="%H %ai" 98ab34b^..origin/main -- \
  docs/vipi/VIPI_v0.1.3_methodology.md \
  docs/vipi/VIPI_v0.1.3_curation_policy.md \
  docs/vipi/VIPI_v0.1.3_pre_registration.md
git log --format="%H %ai" -1 9d8d908
```

Every methodology-surface commit timestamp from the first command must be strictly earlier than the `9d8d908` timestamp from the second.

**Command 5: verify file-list of every individual SHA in §4 and §5 (file lists are truth; subject lines are narrative).**

```bash
for sha in 57bfc7f 9dbe845 ec8b3dd 9264dcf eca9c34 b0731a0 \
           a255fff e14f435 af88fb0 1cfc899 d384b1f 5141565; do
    echo "=== $sha ==="
    git show --stat $sha
done
```

Expected: each SHA's file list matches the disclosure in §4 or §5 above.

---

## §11. Corrections and Versioning Policy Reference

This document is governed by the same retention and correction policies as the methodology it documents.

- **Corrections to this document** that affect substantive content (a missing SHA, a misclassified commit, a wrong file path) are disclosed on the face of a reissued document with prior text, corrected text, reason, and affected sections — the same documentation-errata protocol applied to the reproducibility appendix at `9e81ac935cd68eaae0437ec42ab4567d04506af8`. They are not methodology §12.1 corrections, since §12.1 governs published VIPI value corrections only.

- **This document is version-pinned to VIPI methodology v0.1.3.** Future methodology versions (v0.1.4 and later) under methodology §12.3 have their own chain-of-custody documents (e.g., `VIPI_v0.1.4_chain_of_custody.md`), each documenting the provenance of that version's published methodology surface. The v0.1.3 → v0.1.4 transition itself is disclosed in the v0.1.4 chain-of-custody under its §3 (pre-registration anchor) or §4 (pre-Cluster-N evolution) as appropriate.

- **Retention:** This document and the git history it references are retained for a minimum of 5 years from publication, per methodology §11.5 (IOSCO Principle 17, Audits).

- **References:** Methodology §12.1 (Corrections), §12.2 (Halts), §12.3 (Methodology Versioning).

---

*End of `VIPI_v0.1.3_chain_of_custody.md`.*
