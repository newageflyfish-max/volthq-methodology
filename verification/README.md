# verification/

This directory contains seventeen `.patch` files (the v0.1.3 commit exports from the source monorepo), two amend-disclosure attestation files, one draft-disclosure note, and one appendix-template-evidence file. Together they document the v0.1.3 chain of custody as commit-level evidence: each patch is the bit-level record of what its disclosed SHA introduced to the source monorepo at the time the patch was generated.

## What the patches are

Each `*.patch` file is the output of `git format-patch -1 --stdout <sha>` for a single commit on the source monorepo's `origin/main`. The SHA in the patch filename and on the patch's `From <sha>` mbox header is the same SHA enumerated in `VIPI_v0.1.3_chain_of_custody.md` §3 through §8.

To protect committer identity, the `From:` author header in each patch has been edited to `Volt HQ <noreply@volthq.dev>`. The underlying commit SHAs on the source monorepo's `origin/main` (visible on each patch's `From <sha>` mbox header and in the filename) are unchanged and remain the binding citation key.

## Coverage

The seventeen patches reconcile against the chain-of-custody §2 enumeration as follows:

- Sixteen of the seventeen patches map to the §3-§8 22-SHA tally (twenty primary entries plus two clerical placeholder-fill companions): all six §3 and §4 SHAs excluding the two clerical companions, all six §5.1 amendment SHAs, one of the five §6 plan-sync SHAs (`9250b31`, retained as `s05_errata-9250b31.patch` because it is the load-bearing evidence for the §5.2 mis-enumeration disclosure), the §7.1 inaugural-basket SHA, the §8.1 publication SHA, and the §8.2 publication-infrastructure SHA.
- The seventeenth patch is the §8.3 retrospective errata-reissue commit (`9e81ac935cd68eaae0437ec42ab4567d04506af8`), documented retrospectively in the chain of custody and not counted toward the §2 22-SHA total.
- The six §3-§8 SHAs that are NOT exported as patches here are: two clerical placeholder-fill companions (`45398cd` in §3.1 and `9dbe845` in §4.1, each with zero normative content per chain-of-custody review), and four §6 plan-sync commits (`97f38c4`, `36279e2`, `0cc1d0b`, `7b0e49e`, each touching only the internal not-for-publication implementation plan).

## How to use the patches

The patches are commit-level citations, not a re-application surface. A reader opens each patch and cross-references its metadata and diff against the chain-of-custody narrative:

- Filename SHA against the §3 through §8 enumeration in `VIPI_v0.1.3_chain_of_custody.md`
- Patch `From <sha>` mbox header against the same enumeration (must match filename SHA)
- Patch `Date:` against the §3 through §8 commit-date columns
- Patch `Subject:` against the §3 through §8 subject-line disclosures
- Patch diff content (file paths and line changes) against the §10 Command 5 file-list disclosure and the §4 and §5 per-commit prose

The verification surface is self-consistency between the patch evidence in this directory and the chain-of-custody narrative. The patches are not designed to be reapplied to the canonical files at the top of this repository — those files are the post-publication state, after every chain-enumerated amendment has landed and the §8.3 errata reissue has been applied, so single-commit forward diffs are not direct inverses of the current top-level files. The Administrator-side audit protocol that runs against a live git tree of the source monorepo is documented in `VIPI_v0.1.3_chain_of_custody.md` §10; because the monorepo is permanently private (see top-level `README.md` §11), §10 is executed by the Administrator and not reproducible by a third-party reviewer.

## Filename convention

`s{NN}-{short-sha}.patch` where `NN` is the chain-of-custody section number (§3, §4, §5, §7, §8). One exception: `s05_errata-9250b31.patch` uses the `s05_errata-` prefix to signal that this commit is the load-bearing evidence for the §5.2 mis-enumeration errata, not a §5.1 methodology-surface amendment.

## Amend attestation files

`s05-1cfc899.amend_attestation.txt` and `s05-d384b1f.amend_attestation.txt` document the two amend events disclosed in Chain of Custody §5.3. The attestations are best-effort: they cannot be cryptographically verified against the remote because the pre-amend SHAs never reached origin/main. Each attestation file states its limits explicitly.

## Draft-disclosure file

`s05-e14f435.draft_disclosure.md` discloses the existence and disposition of `docs/vipi/working/section8_amendment_draft.md`, a working-draft file that is visible in `s05-e14f435.patch` but is not part of the v0.1.3 published Methodology surface. The disclosure is governed by Chain of Custody §5.1's no-silent-elision requirement.

## Appendix template evidence

`appendix_TEMPLATE_evidence.md` is a copy of the reproducibility-appendix template (commit `46ac7b7`'s second file). Per Chain of Custody §8.2, the template predates the Inaugural Basket and is chain-of-custody evidence that the §8.1 publication commit used a pre-base-date template structure.
