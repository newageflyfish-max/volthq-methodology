# VIPI v0.1.3 — Public Methodology Mirror

## 1. What this repository is

This is the public methodology mirror of the Volt Inference Price Index (VIPI) at version 0.1.3. The Volt HQ source monorepo is permanently private; this mirror is the canonical public audit surface for the v0.1.3 Methodology, Chain of Custody, Curation Policy, Pre-Registration, and Reproducibility Appendix, together with the verification artifacts that allow a reviewer to reconstruct the Inaugural Basket's provenance from first principles. The published version-pinned methodology page lives at https://volthq.dev/methodology/v0.1.3 and is the URL referenced in `CITATION.cff`.

## 2. What VIPI is

VIPI is a daily-fix price index for AI inference across a curated basket of providers, administered by Volt HQ. The Inaugural Basket comprises 46 Constituents across three sub-indices. The base date is 2026-05-18 at 16:00 UTC; the base value is 100.00. The Methodology, basket-selection rules, anomaly handling, and chain-of-custody disclosure are documented in the version-pinned artifacts in this repository.

## 3. Canonical files

Six artifacts constitute the v0.1.3 published methodology surface:

- `VIPI_v0.1.3_methodology.md` — Methodology rules
- `VIPI_v0.1.3_curation_policy.md` — Curation Policy
- `VIPI_v0.1.3_pre_registration.md` — Pre-Registration binding contract
- `VIPI_v0.1.3_reproducibility_appendix.md` — Reproducibility Appendix (v2, post-errata reissue)
- `VIPI_v0.1.3_chain_of_custody.md` — Chain of Custody provenance record
- `CITATION.cff` — citation metadata

`LICENSE` (CC-BY-4.0) and this `README.md` complete the canonical surface. These files are version-pinned to v0.1.3. Future Methodology versions (v0.1.4 and later) will live under separate version-pinned artifacts; the Methodology §12.3 versioning rule governs the version-pinning side, and the per-version Chain of Custody convention is established in `VIPI_v0.1.3_chain_of_custody.md` §11.

## 4. Verification

The `verification/` subdirectory contains seventeen `.patch` files exported from the source monorepo using `git format-patch -1 --stdout <sha>`, two `.amend_attestation.txt` files documenting the §5.3 amend-disclosed commits, an appendix-template evidence file, and a per-section disclosure note for the working-draft exposure in commit `e14f435`. These artifacts are commit-level citations: each patch is the bit-level record of what its disclosed SHA introduced to the source monorepo at the time the patch was generated, and together they satisfy IOSCO Principle 18 (Audit Trail) by preserving an evidentiary diff for each substantive Methodology-surface amendment commit enumerated in `VIPI_v0.1.3_chain_of_custody.md` §3 and §4 and §5, the §5.2 mis-enumeration evidence commit, the inaugural-basket migration commit (§7.1), the publication commit (§8.1), the publication-infrastructure commit (§8.2), and the §8.3 retrospective errata-reissue commit. The two clerical placeholder-fill companion commits disclosed parenthetically in §3.1 and §4.1, and four of the five §6 plan-sync commits, are documented in the chain of custody but not exported as patches; the reasons for their omission are stated in `verification/README.md`.

The patches are evidence, not a re-application surface. The canonical files at the top of this repository are the post-publication state — after every chain-enumerated amendment has landed and the §8.3 errata reissue has been applied — so single-commit forward diffs are not direct inverses of the current files. A reader uses the patches by opening each one and cross-referencing the patch's `From <sha>` mbox header, `Date:`, `Subject:`, and diff content against the corresponding §3 through §8 entry in `VIPI_v0.1.3_chain_of_custody.md`. The §10 Command 5 file-list disclosure is the audit hook to which patch contents map for the twelve SHAs it enumerates (eleven of which are present as patches; the twelfth, `9dbe845`, is the clerical companion noted above).

The Administrator-side audit protocol that runs against a live git tree is documented in `VIPI_v0.1.3_chain_of_custody.md` §10. Those commands operate on a clone of the source monorepo and are run by the Administrator; the source monorepo is permanently private (see §11), so the §10 commands are not reproducible by a third-party reviewer. The public reproducibility surface for a third-party reviewer is the patch evidence in this directory plus the chain-of-custody narrative.

See `verification/README.md` for the full reviewer workflow.

## 5. History

The `history/` subdirectory retains prior Methodology versions and pre-publication review artifacts:

- `VIPI_v0.1.1_methodology.md`, `VIPI_v0.1.2_methodology.md` — superseded Methodology versions
- `VIPI_v0.1_review_round1.md`, `VIPI_v0.1.1_codex_review.md`, `VIPI_v0.1.1_review_round2.md`, `VIPI_v0.1.2_codex_review.md`, `VIPI_v0.1.2_review_round3.md`, `VIPI_v0.1.3_codex_review.md` — pre-publication review artifacts retained as part of the v0.1.3 evolution record

Prior versions are not authoritative. v0.1.3 is the only Methodology version currently in force. Retention of superseded versions follows the convergent practice across S&P Platts, ICE Benchmark Administration, IOSCO Principle 12 (Changes to the Methodology), and academic reproducibility repositories: supersession is signalled, not deleted.

## 6. Errata and corrections

Two distinct error-handling mechanisms apply:

**Documentation errata.** Corrections to Chain of Custody content (and, by extension of the same documentation-errata protocol, to Methodology, Curation Policy, Pre-Registration, or Reproducibility Appendix content) — a missing SHA, a misclassified commit, an enumeration error, a typographical error affecting normative substance — are governed by `VIPI_v0.1.3_chain_of_custody.md` §11. Corrections are disclosed on the face of the reissued document with prior text, corrected text, reason, and affected sections. The worked example is the Reproducibility Appendix errata reissue at commit `9e81ac9`, which corrected the §5.2 mis-enumeration. This mechanism is documentation errata; it is not the Methodology §12.1 protocol.

**Published-value corrections.** Corrections to a published VIPI value are governed by Methodology §12.1. This mechanism is out of scope for this repository because no VIPI value lives here. Published values reside at `https://volthq.dev` and are subject to the Methodology §12.1 correction protocol.

## 7. Methodology changes

Material changes to the Methodology follow the version-pinning rule: v0.1.3 is frozen, and v0.1.4 and later versions will be published as new artifacts with their own Chain of Custody documents. This satisfies IOSCO Principle 12 (Changes to the Methodology): material changes are disclosed in writing, subject to the 14-day stakeholder consultation window (Methodology §12.3), and traceable to a version-pinned artifact.

## 8. Retention

Per IOSCO Principle 17 (Audits), Methodology §11.5 establishes a 5-year minimum retention requirement for inputs, calculations, audit records, correction logs, and rebalancing announcements. The retention surface in this repository — Git history, the `verification/` patches, the `history/` superseded versions, and the canonical files — exceeds this baseline indefinitely. A Zenodo concept-DOI and Software Heritage push are planned to extend third-party preservation beyond the GitHub mirror's availability.

## 9. Periodic review

Per IOSCO Principle 10 (Periodic Review), the Methodology is reviewed annually alongside the Source Classification Policy (Methodology §11.3). A published summary of each review will be disclosed in this repository under the version-pinned Chain of Custody for the version then in force.

## 10. Citation

Cite VIPI v0.1.3 using the metadata in `CITATION.cff`. A Zenodo concept-DOI and per-version DOI are planned post-launch; the README will be updated and `CITATION.cff` will be amended to include the DOI fields when the deposit is complete.

## 11. Relationship to source monorepo

The Volt HQ source monorepo `github.com/newageflyfish-max/volthq` is permanently private. This mirror is the canonical public audit surface for v0.1.3. Each `verification/*.patch` file is a `git format-patch -1 --stdout <sha>` export of a single commit on the monorepo's `origin/main`; the SHA in the filename and on the patch's `From <sha>` mbox header is the same SHA enumerated in `VIPI_v0.1.3_chain_of_custody.md` §3 through §8.

A reviewer's public verification path is cross-reference, not replay. Filename SHA against the §3-§8 enumeration; patch `Date:`, `Subject:`, and diff content against the §10 Command 5 file-list disclosure and the §4 and §5 per-commit prose; and the `verification/` inventory of seventeen patches against the §2 22-SHA reconciliation as restated in `verification/README.md`. The verification surface is self-consistency between the patch evidence in this mirror and the chain-of-custody narrative.

The §10 audit commands themselves operate on a clone of the source monorepo and are run by the Administrator. Because the monorepo is private, a reviewer cannot independently execute §10; the Administrator's §10 execution is the load-bearing live-git-tree audit, and the patch evidence published here is the citation surface against which a reviewer cross-checks the §10 narrative.

## 12. License

CC-BY-4.0. See `LICENSE`. Identical to the source declaration in `CITATION.cff`.

## 13. Contact

For methodology questions or documentation-errata reports, open an issue in this repository. The `CITATION.cff` author contact is the Administrator of record.

---

## Reading guide for the Chain of Custody

A reviewer opening `VIPI_v0.1.3_chain_of_custody.md` for the first time may benefit from two reading conventions before reaching the §5.1 amendment chain.

**Generic rules, enumerated applications.** Where the Chain of Custody §5.1 names a Constituent alongside a Methodology amendment — for example, the §5(4) Continuity rule co-disclosed with the four-day Administrator-side exclusion applied to `claude-opus-4.7-undisclosed` — the named Constituent is the inaugural-basket application of a generically-stated rule, not a constraint on the rule's scope. The rule applies to any Constituent whose Continuity is reduced by Administrator-side gaps. The Chain of Custody discloses both the rule and its application together because the alternative — silent application — would be disqualifying under IOSCO Principle 9 (Transparency of Benchmark Determinations). The Chain of Custody §11 documentation-errata protocol governs any subsequent clarification of an entry's scope.

**Publication narrowly defined.** The Chain of Custody distinguishes publication commits (§8.1) from publication-infrastructure commits (§8.2). Publication, in the Methodology's narrow definition as stated in Chain of Custody §8.1, is the release of Methodology rules and the artifacts that document the Inaugural Basket's reproducibility against those rules. Repository hygiene — `.gitignore` entries and the appendix-template tracking — is publication-infrastructure. The Chain of Custody applies this distinction to its own commits: `46ac7b7` was originally classified as publication and reclassified as publication-infrastructure on 2026-05-20. A reviewer encountering the reclassification entry may read it as a quality-control failure — methodology authors changing their classification after publication. The balance of audit-trail discipline is that reclassification under the §8.1 narrow Publication definition is itself the audit-strength signal: the alternative — silent persistence of the original classification — would be disqualifying under IOSCO Principle 9. The reclassification record is documented in Chain of Custody §8.2; the Chain of Custody §11 documentation-errata protocol governs any subsequent clarification; no published VIPI value depends on the reclassification.
