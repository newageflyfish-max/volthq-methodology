# VIPI v0.1.3 — Reproducibility Appendix

**Status:** Published. Populated reproducibility appendix for the VIPI v0.1.3 inaugural basket. Sign-off date: 2026-05-18 (Day 78, base date).

> **Errata (v2, 2026-05-20).** This appendix was originally published 2026-05-18 (commit `f173293`). In the original publication, commit `9250b31` was enumerated inside the **Pre-base-date amendment chain** (this section's bullet for Day 75, and the Verification chain at the end of this document). Empirically, `9250b31` touches only `docs/vipi/VIPI_implementation_plan.md` (an internal-not-for-publication artifact) and is therefore a **plan-sync** commit, not a methodology / curation / pre-registration amendment. The amendment chain proper consists of six commits: `a255fff`, `e14f435`, `af88fb0`, `1cfc899`, `d384b1f`, `5141565`. `9250b31` and its sibling plan-sync commits in the same window are disclosed separately in `VIPI_v0.1.3_chain_of_custody.md` §6. No published VIPI value is affected by this correction; the underlying chain-of-custody integrity is preserved (the inaugural-basket commit `9d8d908` is still preceded by every amendment and every plan-sync commit, in commit-timestamp order). See `VIPI_v0.1.3_chain_of_custody.md` §5.2 for full disclosure.

---

## Purpose

This appendix demonstrates that running the locked §5 eligibility query (with the §5(4) continuity sub-query) against the locked `vcmi_provider_mappings` rows seeded by `0008` + `0015` and the `vcmi_registry` rows seeded by `0007` + `0011` + `0012` + `0013`, evaluated as of the rebalancing reference date `2026-04-30` (last day of April per methodology §5 preamble), produces the constituent set seeded by `0009_seed_vipi_basket_inaugural.sql`.

In other words: the appendix is the chain-of-custody proof that the rules pre-existed the basket. A reader holding this appendix + the locked pre-registration commit (`98ab34b…`) + the methodology version `vipi-0.1.3` can independently verify that the inaugural basket falls out of the locked rules.

This deliverable discharges the commitments at:
- `docs/vipi/VIPI_v0.1.3_pre_registration.md:165` — "A reproducibility appendix is published the same day as a separate document under `docs/vipi/`."
- `docs/vipi/VIPI_v0.1.3_curation_policy.md:154` — "A reproducibility appendix … demonstrates basket reproducibility from the locked rules in this policy plus the locked mappings in the seed migration. The appendix is published before May 18."

## Anchor

**Methodology version:** `vipi-0.1.3`
**Pre-registration anchor:** `98ab34b8b3b87f49b5b2dd315324688e40e93dc5` (per `docs/vipi/VIPI_v0.1.3_pre_registration.md:11`).
**Pre-base-date amendment chain (per pre-reg L15, post-Cluster 4):**
- Day 73 (May 14, 2026): methodology §§5(3), 5(4), 7.2 amendments — commit `a255fff`
- Day 75 (May 15, 2026): methodology §§8.2, 8.4, 10.4 — commits `e14f435`, `af88fb0`
- Days 76 and 77 (May 16, May 17, 2026): methodology §11.3 + curation policy silent-FALLBACK + pre-reg Sub-index publication scope — commits `1cfc899`, `d384b1f`, `5141565`

Plan-sync commits in the same window are disclosed separately, see chain_of_custody.md §6.

**Base date and time:** May 18, 2026, 16:00:00 UTC (per methodology §8.2 + pre-reg).
**Inaugural-basket commit:** `9d8d908`
**This appendix's own commit:** (see `git log -- docs/vipi/VIPI_v0.1.3_reproducibility_appendix.md` for this file's publication commit; commit ordering against the inaugural-basket commit 9d8d908 is verifiable via git history)

The pre-registration's commit-ordering proof requires the pre-base-date amendment commit chain (Day 73 → Day 77) to precede the inaugural-basket commit. Git log against `origin/main` will show that order with timestamps; the operator's published Substack issue must cite the relative commit timestamps verbatim.

## Reproduction recipe

### Step 1 — §5 eligibility query

Source: `tools/queries/vipi_v0_1_3_eligibility_query.sql`. Substitute `:reference_date='2026-04-30'` and `:continuity_start='2026-04-17'` (14 days inclusive). Execute against remote D1 via:

```bash
cd ~/volthq/packages/workers/price-aggregator
npx wrangler d1 execute volt-snapshots --remote --file ../../../tools/queries/vipi_v0_1_3_eligibility_query.sql
```

(Substitute the date placeholders inline before execution; D1's wrangler client does not bind named parameters across `--file`.)

**Output rows:**

Total candidates per sub_index (post-Administrator-side overlay applied per Step 2 below): VIPI = 32, VIPI-Open = 26, VIPI-Closed = 6.

The 78 (vcmi, candidate_sub_index) pairs returned by the eligibility query, sorted by `sub_index` (vipi → vipi_open → vipi_closed) then alphabetically by `vcmi`. The `eligibility_pass` column is the raw query output prior to the §5(4) Administrator-side overlay (Step 2 below); `vcmi:claude-opus-4.7-undisclosed` shows `FAIL_CONTINUITY` here and is promoted to PASS under the override.

| vcmi | sub_index | eligibility_pass |
|---|---|---|
| `vcmi:claude-haiku-4.5-undisclosed` | `vipi` | `PASS` |
| `vcmi:claude-opus-4.6-undisclosed` | `vipi` | `PASS` |
| `vcmi:claude-opus-4.7-undisclosed` | `vipi` | `FAIL_CONTINUITY` |
| `vcmi:claude-sonnet-4.5-undisclosed` | `vipi` | `PASS` |
| `vcmi:claude-sonnet-4.6-undisclosed` | `vipi` | `PASS` |
| `vcmi:deepseek-v3-0324-671b-a37b` | `vipi` | `PASS` |
| `vcmi:deepseek-v3-671b-a37b` | `vipi` | `PASS` |
| `vcmi:gemma-4-31b` | `vipi` | `PASS` |
| `vcmi:glm-4.5-air-106b-a12b` | `vipi` | `FAIL_NO_MAPPINGS` |
| `vcmi:glm-4.5-air-106b-a12b/fp8` | `vipi` | `FAIL_NO_HIGH_CONF` |
| `vcmi:glm-4.6-357b-a32b` | `vipi` | `PASS` |
| `vcmi:gpt-5.4-undisclosed` | `vipi` | `PASS` |
| `vcmi:gpt-oss-120b` | `vipi` | `PASS` |
| `vcmi:gpt-oss-20b` | `vipi` | `PASS` |
| `vcmi:kimi-k2.6-1000b-a32b` | `vipi` | `FAIL_NO_MAPPINGS` |
| `vcmi:llama-3.1-405b` | `vipi` | `PASS` |
| `vcmi:llama-3.1-405b/fp8` | `vipi` | `PASS` |
| `vcmi:llama-3.1-70b` | `vipi` | `PASS` |
| `vcmi:llama-3.1-70b+nvidia/nemotron` | `vipi` | `PASS` |
| `vcmi:llama-3.1-70b/fp8` | `vipi` | `PASS` |
| `vcmi:llama-3.1-8b` | `vipi` | `PASS` |
| `vcmi:llama-3.1-8b/fp8` | `vipi` | `PASS` |
| `vcmi:llama-3.3-70b` | `vipi` | `PASS` |
| `vcmi:llama-3.3-70b+sao10k/euryale-v2.3` | `vipi` | `FAIL_NO_MAPPINGS` |
| `vcmi:llama-3.3-70b/fp8` | `vipi` | `PASS` |
| `vcmi:llama-4-maverick-400b-a17b` | `vipi` | `FAIL_NO_MAPPINGS` |
| `vcmi:llama-4-maverick-400b-a17b/fp8` | `vipi` | `PASS` |
| `vcmi:llama-4-scout-109b-a17b` | `vipi` | `PASS` |
| `vcmi:mistral-small-2501-24b` | `vipi` | `PASS` |
| `vcmi:nemotron-3-super-120b-a12b` | `vipi` | `PASS` |
| `vcmi:qwen-2.5-72b` | `vipi` | `PASS` |
| `vcmi:qwen-2.5-72b/fp8` | `vipi` | `PASS` |
| `vcmi:qwen-3-235b-a22b-instruct-2507` | `vipi` | `PASS` |
| `vcmi:qwen-3-235b-a22b-instruct-2507/fp8` | `vipi` | `PASS` |
| `vcmi:qwen-3-32b` | `vipi` | `PASS` |
| `vcmi:qwen-3-coder-480b-a35b` | `vipi` | `FAIL_NO_MAPPINGS` |
| `vcmi:qwen-3-coder-480b-a35b/fp8` | `vipi` | `PASS` |
| `vcmi:qwen-3-next-80b-a3b` | `vipi` | `PASS` |
| `vcmi:qwen-3-next-80b-a3b/fp8` | `vipi` | `PASS` |
| `vcmi:qwen-3.6-35b-a3b` | `vipi` | `FAIL_NO_MAPPINGS` |
| `vcmi:deepseek-v3-0324-671b-a37b` | `vipi_open` | `PASS` |
| `vcmi:gemma-4-31b` | `vipi_open` | `PASS` |
| `vcmi:glm-4.5-air-106b-a12b` | `vipi_open` | `FAIL_NO_MAPPINGS` |
| `vcmi:glm-4.5-air-106b-a12b/fp8` | `vipi_open` | `FAIL_NO_HIGH_CONF` |
| `vcmi:glm-4.6-357b-a32b` | `vipi_open` | `PASS` |
| `vcmi:gpt-oss-120b` | `vipi_open` | `PASS` |
| `vcmi:gpt-oss-20b` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-405b` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-405b/fp8` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-70b` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-70b+nvidia/nemotron` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-70b/fp8` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-8b` | `vipi_open` | `PASS` |
| `vcmi:llama-3.1-8b/fp8` | `vipi_open` | `PASS` |
| `vcmi:llama-3.3-70b` | `vipi_open` | `PASS` |
| `vcmi:llama-3.3-70b+sao10k/euryale-v2.3` | `vipi_open` | `FAIL_NO_MAPPINGS` |
| `vcmi:llama-3.3-70b/fp8` | `vipi_open` | `PASS` |
| `vcmi:llama-4-maverick-400b-a17b` | `vipi_open` | `FAIL_NO_MAPPINGS` |
| `vcmi:llama-4-maverick-400b-a17b/fp8` | `vipi_open` | `PASS` |
| `vcmi:llama-4-scout-109b-a17b` | `vipi_open` | `PASS` |
| `vcmi:mistral-small-2501-24b` | `vipi_open` | `PASS` |
| `vcmi:nemotron-3-super-120b-a12b` | `vipi_open` | `PASS` |
| `vcmi:qwen-2.5-72b` | `vipi_open` | `PASS` |
| `vcmi:qwen-2.5-72b/fp8` | `vipi_open` | `PASS` |
| `vcmi:qwen-3-235b-a22b-instruct-2507` | `vipi_open` | `PASS` |
| `vcmi:qwen-3-235b-a22b-instruct-2507/fp8` | `vipi_open` | `PASS` |
| `vcmi:qwen-3-32b` | `vipi_open` | `PASS` |
| `vcmi:qwen-3-coder-480b-a35b` | `vipi_open` | `FAIL_NO_MAPPINGS` |
| `vcmi:qwen-3-coder-480b-a35b/fp8` | `vipi_open` | `PASS` |
| `vcmi:qwen-3-next-80b-a3b` | `vipi_open` | `PASS` |
| `vcmi:qwen-3-next-80b-a3b/fp8` | `vipi_open` | `PASS` |
| `vcmi:qwen-3.6-35b-a3b` | `vipi_open` | `FAIL_NO_MAPPINGS` |
| `vcmi:claude-haiku-4.5-undisclosed` | `vipi_closed` | `PASS` |
| `vcmi:claude-opus-4.6-undisclosed` | `vipi_closed` | `PASS` |
| `vcmi:claude-opus-4.7-undisclosed` | `vipi_closed` | `FAIL_CONTINUITY` |
| `vcmi:claude-sonnet-4.5-undisclosed` | `vipi_closed` | `PASS` |
| `vcmi:claude-sonnet-4.6-undisclosed` | `vipi_closed` | `PASS` |
| `vcmi:gpt-5.4-undisclosed` | `vipi_closed` | `PASS` |

### Step 2 — §5(4) continuity check

Source: `tools/queries/vipi_v0_1_3_continuity_query.sql`. Window: 2026-04-17 → 2026-04-30 inclusive (14 days ending at reference date per methodology §5(4) L115). Execute:

```bash
cd ~/volthq/packages/workers/price-aggregator
npx wrangler d1 execute volt-snapshots --remote --file ../../../tools/queries/vipi_v0_1_3_continuity_query.sql
```

(Substitute `:start_date='2026-04-17'`, `:end_date='2026-04-30'`, `:vcmi_filter=NULL` inline.)

**Output (one row per VCMI; deduplicated from the per-candidate-sub_index output of Step 1; rows where continuity could not be computed because the VCMI has no provider mappings are marked `n/a`):**

| vcmi | total_eligible_closes | vcmi_observations | continuity_pct | pass_5_4_threshold |
|---|---|---|---|---|
| `vcmi:claude-haiku-4.5-undisclosed` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:claude-opus-4.6-undisclosed` | 14 | 14 | 1 | PASS_5_4 |
| **`vcmi:claude-opus-4.7-undisclosed`** | **14** | **10** | **0.7142857142857143** | **FAIL_5_4** |
| `vcmi:claude-sonnet-4.5-undisclosed` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:claude-sonnet-4.6-undisclosed` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:deepseek-v3-0324-671b-a37b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:deepseek-v3-671b-a37b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:gemma-4-31b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:glm-4.5-air-106b-a12b` | — | — | — | n/a (no mappings) |
| `vcmi:glm-4.5-air-106b-a12b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:glm-4.6-357b-a32b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:gpt-5.4-undisclosed` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:gpt-oss-120b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:gpt-oss-20b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:kimi-k2.6-1000b-a32b` | — | — | — | n/a (no mappings) |
| `vcmi:llama-3.1-405b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.1-405b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.1-70b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.1-70b+nvidia/nemotron` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.1-70b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.1-8b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.1-8b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.3-70b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-3.3-70b+sao10k/euryale-v2.3` | — | — | — | n/a (no mappings) |
| `vcmi:llama-3.3-70b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-4-maverick-400b-a17b` | — | — | — | n/a (no mappings) |
| `vcmi:llama-4-maverick-400b-a17b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:llama-4-scout-109b-a17b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:mistral-small-2501-24b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:nemotron-3-super-120b-a12b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-2.5-72b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-2.5-72b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3-235b-a22b-instruct-2507` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3-235b-a22b-instruct-2507/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3-32b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3-coder-480b-a35b` | — | — | — | n/a (no mappings) |
| `vcmi:qwen-3-coder-480b-a35b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3-next-80b-a3b` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3-next-80b-a3b/fp8` | 14 | 14 | 1 | PASS_5_4 |
| `vcmi:qwen-3.6-35b-a3b` | — | — | — | n/a (no mappings) |

**Administrator-side-outage overlay** (per §5(4) L117):

- **Days affected:** 2026-04-17, 2026-04-18, 2026-04-19, 2026-04-20 (4 MOC closes).
- **Affected VCMI:** opus-4.7 (Anthropic).
- **Reason:** opus-4.7 launched 2026-04-16; the Volt adapter was patched 2026-04-20T19:45Z. Between launch and adapter patch, Volt did not collect opus-4.7 observations — an Administrator-side (Volt-side) data-collection gap, not an upstream availability failure.
- **Continuity adjustment per methodology §5(4) L117:** the 4 MOC closes are excluded from BOTH the numerator (vcmi_observations) AND the denominator (total_eligible_closes) of opus-4.7's continuity calculation. Adjusted continuity: 10/10 = 100%, passes the §5(4) threshold.
- **Disclosure basis:** mandated by methodology §5(4) and pre-registration L15 (post-Cluster 4 pre-base-date amendment chain).

### Step 3 — Curator-decision pass

Source: `~/Desktop/volt-session-logs/day77_0009_candidate_constituents.md`. The operator-review section enumerates REQUIRES_DECISION items.

**Resolutions (operator's curator decisions, with rationale):**

- **D-1 (late-added VCMI continuity treatment):**

  **Curator decision:** include opus-4.7 in the inaugural VIPI-Closed basket under the §5(4) Administrator-side-override clause. opus-4.7 is the highest-tier proprietary frontier model at base date; its exclusion under a naive 14-day continuity check would render VIPI-Closed unrepresentative of the proprietary inference market the sub-index is designed to measure.

  **Continuity treatment:** per the Administrator-side-outage overlay (Step 2), the 4 MOC closes between launch (2026-04-16) and adapter patch (2026-04-20T19:45Z) are excluded from both numerator and denominator of opus-4.7's continuity calculation. Adjusted continuity is 10/10 = 100%, passes the §5(4) threshold.

  **Methodology citation:** §5(4) L115-117 (Administrator-side-outage clause).
  **Pre-registration citation:** L15 (post-Cluster 4 pre-base-date amendment chain; see Anchor section).

- **D-2 (cap application + tie-breaker cascade):**

  - **sub_index `vipi`:** 32 eligible candidates exceed N=20 cap. Tie-breaker cascade applied per methodology §6 L141-146 with sort key `mapping_count DESC, high_conf_count DESC, alphabetical` (steps 1, 2, 5 — steps 3 and 4 produce no movement once steps 1+2+5 have run, as documented in the `0009_seed_vipi_basket_inaugural.sql` header L52-L59). Top 20 admitted to 0009; bottom 12 dropped. Dropped VCMIs (in alphabetical order): `vcmi:claude-haiku-4.5-undisclosed`, `vcmi:claude-opus-4.6-undisclosed`, `vcmi:claude-sonnet-4.5-undisclosed`, `vcmi:claude-sonnet-4.6-undisclosed`, `vcmi:gpt-5.4-undisclosed`, `vcmi:llama-3.1-405b/fp8`, `vcmi:llama-3.3-70b`, `vcmi:nemotron-3-super-120b-a12b`, `vcmi:qwen-2.5-72b/fp8`, `vcmi:qwen-3-235b-a22b-instruct-2507/fp8`, `vcmi:qwen-3-next-80b-a3b`, `vcmi:qwen-3-next-80b-a3b/fp8`. The 5 proprietary VCMIs (claude × 4 + gpt-5.4) rank lowest at step 1 by virtue of their sole-issuer single-mapping coverage; this is methodologically intentional — the §6 cascade prioritizes multi-provider coverage, which routes sole-issuer proprietary models to `vipi_closed` (their natural sub-index) and reserves `vipi`-headline for multi-provider open-license models; the 7 open-license drops are mostly single-mapping VCMIs or 2-mapping VCMIs ranking below admitted 2-mapping siblings at step 2 (`high_conf_count` tiebreaker) or step 5 (alphabetical) within their mapping-count tier.

  - **sub_index `vipi_open`:** 26 eligible candidates exceed N=20 cap. Same cascade applied. Top 20 admitted; bottom 6 dropped. Dropped: `vcmi:llama-3.1-405b/fp8`, `vcmi:llama-3.3-70b`, `vcmi:nemotron-3-super-120b-a12b`, `vcmi:qwen-3-235b-a22b-instruct-2507/fp8`, `vcmi:qwen-3-next-80b-a3b`, `vcmi:qwen-3-next-80b-a3b/fp8`.

  - **sub_index `vipi_closed`:** 6 eligible candidates ≤ N=20 cap. No tie-breaker application required.

  Citation: methodology §6 L141-146 (tie-breaker cascade).

- **D-3 (sole-issuer semantic verification):**

  The query below verifies that each of the 6 VIPI-Closed constituents maps to its issuer-of-record provider with confidence='high', satisfying the sole-issuer property required by pre-registration Sub-index publication scope L40.

  ```json
  [
    {
      "vcmi": "vcmi:claude-haiku-4.5-undisclosed",
      "provider_id": "anthropic",
      "confidence": "high"
    },
    {
      "vcmi": "vcmi:claude-opus-4.6-undisclosed",
      "provider_id": "anthropic",
      "confidence": "high"
    },
    {
      "vcmi": "vcmi:claude-opus-4.7-undisclosed",
      "provider_id": "anthropic",
      "confidence": "high"
    },
    {
      "vcmi": "vcmi:claude-sonnet-4.5-undisclosed",
      "provider_id": "anthropic",
      "confidence": "high"
    },
    {
      "vcmi": "vcmi:claude-sonnet-4.6-undisclosed",
      "provider_id": "anthropic",
      "confidence": "high"
    },
    {
      "vcmi": "vcmi:gpt-5.4-undisclosed",
      "provider_id": "openai",
      "confidence": "high"
    }
  ]
  ```

  Verified: 6 distinct VCMIs, 5:1 provider distribution (Anthropic:OpenAI), all confidence='high', issuer namespace in every VCMI matches its provider_id (`vcmi:claude-*` → anthropic per the family-prefix convention observed in the live registry; `vcmi:gpt-*` → openai).

- **D-4 silent-FALLBACK cross-check** (per curation policy d384b1f):

  The coverage-check script (run against REGISTRY_STATE=0013) enumerates (provider, VCMI) mapping pairs omitted under the silent-FALLBACK rule (curation policy d384b1f, applied to catalog-gated adapters: cerebras, fireworks, groq, hyperbolic). Note: `tools/coverage_check.py`'s `--registry-state` flag enumeration tops at `0013`, which is the highest registry-modifying migration (0014 and 0015 modify `vcmi_provider_mappings`, not `vcmi_registry`). The 40-VCMI registry state evaluated by the script matches the live `volt-snapshots` D1 vcmi_registry row count.

  ```
  Worksheet rows: 191
  Registry size (post-0013): 40
  Mappable: 72
  Threshold: 70
  Status: PASS

  === Per-VCMI hit count ===
      5  vcmi:llama-3.1-8b
      4  vcmi:deepseek-v3-671b-a37b
      3  vcmi:deepseek-v3-0324-671b-a37b
      3  vcmi:gpt-oss-120b
      3  vcmi:gpt-oss-20b
      3  vcmi:llama-3.1-70b
      3  vcmi:llama-3.1-70b/fp8
      3  vcmi:llama-3.1-8b/fp8
      3  vcmi:llama-3.3-70b/fp8
      3  vcmi:llama-4-scout-109b-a17b
      3  vcmi:mistral-small-2501-24b
      3  vcmi:qwen-2.5-72b
      3  vcmi:qwen-3-235b-a22b-instruct-2507
      3  vcmi:qwen-3-coder-480b-a35b/fp8
      2  vcmi:gemma-4-31b
      2  vcmi:glm-4.6-357b-a32b
      2  vcmi:llama-3.1-405b
      2  vcmi:llama-3.1-70b+nvidia/nemotron
      2  vcmi:llama-4-maverick-400b-a17b/fp8
      2  vcmi:qwen-2.5-72b/fp8
      2  vcmi:qwen-3-32b
      2  vcmi:qwen-3-next-80b-a3b
      1  vcmi:claude-haiku-4.5-undisclosed
      1  vcmi:claude-opus-4.6-undisclosed
      1  vcmi:claude-sonnet-4.5-undisclosed
      1  vcmi:claude-sonnet-4.6-undisclosed
      1  vcmi:glm-4.5-air-106b-a12b/fp8
      1  vcmi:gpt-5.4-undisclosed
      1  vcmi:llama-3.1-405b/fp8
      1  vcmi:llama-3.3-70b
      1  vcmi:nemotron-3-super-120b-a12b
      1  vcmi:qwen-3-235b-a22b-instruct-2507/fp8
      1  vcmi:qwen-3-next-80b-a3b/fp8
  ```

  Verified: no 0009 constituent's surviving mapping set falls below the §5(3) provider-coverage threshold — for multi-provider clause (a), the rule is ≥2 distinct provider mappings with at least one `confidence='high'`; for sole-issuer clause (b), the rule is ≥1 high-confidence provider mapping to the issuer of record. All 20 0009 vipi constituents and all 20 0009 vipi_open constituents appear in the coverage-check output with ≥2 mappings; all 6 0009 vipi_closed sole-issuer constituents (including `vcmi:claude-opus-4.7-undisclosed`, which is absent from this coverage-check enumeration because the script's worksheet predates the 0015 supplement but which is verified present in `vcmi_provider_mappings` via the D-3 sole-issuer query above) have exactly 1 high-confidence mapping to their issuer of record. Silent-FALLBACK exclusions affect upstream-confidence quality, not eligibility.

### Step 4 — 0009 basket-seed migration

**File:** `packages/workers/vipi-cron/migrations/0009_seed_vipi_basket_inaugural.sql`

**Commit:** `9d8d908`

**SQL VALUES section (verbatim from the committed migration):**

```sql
INSERT INTO vipi_basket (
  effective_from, effective_to, sub_index, vcmi, weight, added_reason, rebalance_announcement_url, notes
) VALUES
  -- ── sub_index='vipi' (headline; 20 constituents; weight = 1/20 = 0.05) ──
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:deepseek-v3-0324-671b-a37b', 0.05, 'inaugural', NULL, 'multi-provider, restricted-license headline-only admit per pre-reg L33'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:deepseek-v3-671b-a37b', 0.05, 'inaugural', NULL, 'multi-provider, restricted-license headline-only admit per pre-reg L33'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:gemma-4-31b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:glm-4.6-357b-a32b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:gpt-oss-120b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:gpt-oss-20b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.1-405b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.1-70b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.1-70b+nvidia/nemotron', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.1-70b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.1-8b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.1-8b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-3.3-70b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-4-maverick-400b-a17b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:llama-4-scout-109b-a17b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:mistral-small-2501-24b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:qwen-2.5-72b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:qwen-3-235b-a22b-instruct-2507', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:qwen-3-32b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi', 'vcmi:qwen-3-coder-480b-a35b/fp8', 0.05, 'inaugural', NULL, NULL),

  -- ── sub_index='vipi_open' (open-license subset; 20 constituents; weight = 1/20 = 0.05) ──
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:deepseek-v3-0324-671b-a37b', 0.05, 'inaugural', NULL, 'NOTE: pack assigned to vipi_open; verify license class is open per pre-reg L33 (DeepSeek-License classified restricted in 0011_*.sql)'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:gemma-4-31b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:glm-4.6-357b-a32b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:gpt-oss-120b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:gpt-oss-20b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.1-405b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.1-70b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.1-70b+nvidia/nemotron', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.1-70b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.1-8b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.1-8b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-3.3-70b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-4-maverick-400b-a17b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:llama-4-scout-109b-a17b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:mistral-small-2501-24b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:qwen-2.5-72b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:qwen-2.5-72b/fp8', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:qwen-3-235b-a22b-instruct-2507', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:qwen-3-32b', 0.05, 'inaugural', NULL, NULL),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_open', 'vcmi:qwen-3-coder-480b-a35b/fp8', 0.05, 'inaugural', NULL, NULL),

  -- ── sub_index='vipi_closed' (closed-license sole-issuer; 6 constituents; weight = 1/6) ──
  -- Per pre-reg "Sub-index publication scope" subsection at 5141565:
  -- "six sole-issuer constituents — five Anthropic models and one OpenAI model"
  -- Per §5(4) Administrator-side curator override (documented in header):
  -- claude-opus-4.7-undisclosed admitted with continuity 10/10 over adjusted window.
  ('2026-05-18T16:00:00Z', NULL, 'vipi_closed', 'vcmi:claude-haiku-4.5-undisclosed', 0.16666666666666666, 'inaugural', NULL, 'sole-issuer per §5(3)(b); static_by_design per §11.3.2'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_closed', 'vcmi:claude-opus-4.6-undisclosed', 0.16666666666666666, 'inaugural', NULL, 'sole-issuer per §5(3)(b); static_by_design per §11.3.2'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_closed', 'vcmi:claude-opus-4.7-undisclosed', 0.16666666666666666, 'inaugural', NULL, 'sole-issuer per §5(3)(b); §5(4) Administrator-side curator override applied (4 days excluded from numerator and denominator: 2026-04-17, 18, 19, 20); continuity 10/10 over adjusted 10-day window; mandatory disclosure pending in reproducibility appendix'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_closed', 'vcmi:claude-sonnet-4.5-undisclosed', 0.16666666666666666, 'inaugural', NULL, 'sole-issuer per §5(3)(b); static_by_design per §11.3.2'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_closed', 'vcmi:claude-sonnet-4.6-undisclosed', 0.16666666666666666, 'inaugural', NULL, 'sole-issuer per §5(3)(b); static_by_design per §11.3.2'),
  ('2026-05-18T16:00:00Z', NULL, 'vipi_closed', 'vcmi:gpt-5.4-undisclosed', 0.16666666666666666, 'inaugural', NULL, 'sole-issuer per §5(3)(b); static_by_design per §11.3.2');
```

### Step 5 — Apply to remote D1

```bash
cd ~/volthq/packages/workers/price-aggregator
npx wrangler d1 execute volt-snapshots --remote --file ../vipi-cron/migrations/0009_seed_vipi_basket_inaugural.sql
```

**Output:**

```
├ Checking if file needs uploading
│
├ 🌀 Uploading 9918dd5d-0474-417d-95fa-4dc2a923b48b.ee631ba5e67c45fc.sql
│ 🌀 Uploading complete.
│
[
  {
    "results": [
      {
        "Total queries executed": 1,
        "Rows read": 46,
        "Rows written": 138,
        "Database size (MB)": "3219.65"
      }
    ],
    "success": true,
    "finalBookmark": "00004810-0000000a-0000506f-ea63566b29724cf7d33cb3bc4876e807",
    "meta": {
      "served_by": "v3-prod",
      "served_by_region": "WNAM",
      "served_by_colo": "DEN",
      "served_by_primary": true,
      "timings": {
        "sql_duration_ms": 4.1336
      },
      "duration": 4.1336,
      "changes": 47,
      "last_row_id": 46,
      "changed_db": true,
      "size_after": 3219652608,
      "rows_read": 46,
      "rows_written": 138,
      "num_tables": 11,
      "total_attempts": 1
    }
  }
]
```

Post-apply verification: `SELECT COUNT(*) FROM vipi_basket WHERE effective_from='2026-05-18T16:00:00Z'` returned 46 rows (20 VIPI + 20 VIPI-Open + 6 VIPI-Closed), confirmed in Phase 0 step 12 of this populating run.

## Verification: rule-then-basket chain

The pre-registration's central defense (`pre_registration.md:7-15`) is that the maintainer publishes the rules first and then publishes the basket against them. The commit-ordering chain on `origin/main` is the provenance proof:

1. **Pre-registration anchor:** `98ab34b…` (April 27, 2026; commit timestamp predates any cluster-amendment commit).
2. **Pre-base-date methodology amendment chain (Days 73 → 77):** commits `a255fff`, `e14f435`, `af88fb0`, `1cfc899`, `d384b1f`, `5141565`. All land BEFORE the inaugural-basket commit. Each was explicitly designated per pre-reg L15 (post-Cluster 4) as a pre-base-date pre-registration revision — NOT a §12.3 transition. Plan-sync commits in the same window are disclosed separately, see chain_of_custody.md §6.
3. **Inaugural-basket commit:** `9d8d908` (May 18, 2026; commit timestamp post-dates the cluster-amendment chain).
4. **This appendix commit:** (see `git log -- docs/vipi/VIPI_v0.1.3_reproducibility_appendix.md` for this file's publication commit; commit ordering against the inaugural-basket commit 9d8d908 is verifiable via git history).
5. **First published VIPI values:** 12 vipi_daily rows + 3 vipi_audit_records rows, written by the first 16:07 UTC cron firing on May 18.

Any reader can reconstruct the chain by:

```bash
git clone https://github.com/newageflyfish-max/volthq.git       # NOTE: repo currently
                                                                  # private; if it becomes
                                                                  # public per the launch
                                                                  # plan, this command works.
                                                                  # Otherwise the commits
                                                                  # are visible to anyone
                                                                  # with read access.
cd volthq
git log --oneline -- docs/vipi/VIPI_v0.1.3_methodology.md \
                     docs/vipi/VIPI_v0.1.3_pre_registration.md \
                     docs/vipi/VIPI_v0.1.3_curation_policy.md \
                     docs/vipi/VIPI_implementation_plan.md
# Verify all amendment commits dated 2026-05-14 through 2026-05-17,
# preceding any commit dated 2026-05-18.
```

A reviewer who confirms (i) the listed commits exist on `origin/main`, (ii) their dates fall before the inaugural-basket commit's date, and (iii) the eligibility + continuity queries against the post-migration `volt-snapshots` D1 reproduce the basket in `0009`, has independently verified the rule-then-basket chain.

## Verification: numeric reconciliation

For each constituent in `0009`, the operator must be able to point to a specific eligibility-query row and a continuity-query row whose verdicts justify inclusion. Reverse: for each ELIGIBLE candidate NOT in `0009`, the operator must be able to cite either (a) cap-application tie-breaker exclusion per methodology §6, or (b) curator-discretion exclusion under a named retention-bias / silent-FALLBACK / sole-issuer ruling.

**Reconciliation table:**

| sub_index    | candidates_eligible | constituents_in_0009 | dropped | drop_reason |
|--------------|---------------------|----------------------|---------|-------------|
| vipi         | 32                  | 20                   | 12      | §6 L141-146 tie-breaker cascade (mapping_count DESC, high_conf_count DESC, alphabetical); see D-2 above for enumerated drops |
| vipi_open    | 26                  | 20                   | 6       | §6 L141-146 tie-breaker cascade (same sort key); see D-2 above for enumerated drops |
| vipi_closed  | 6                   | 6                    | 0       | All 6 sole-issuer constituents qualify; pre-reg Sub-index publication scope L40 explicitly ratifies (5 Anthropic + 1 OpenAI; opus-4.7 via §5(4) override). |

## Out-of-appendix items

- **Reasoning models:** methodology §5(5) excludes any VCMI whose variant string contains 'reasoning' OR is curator-categorized as reasoning. None of the 40 active VCMIs match the variant-string clause; operator manual-categorization at curation time (per curation policy L112-120) is documented in each VCMI's registry-row `notes` field. This appendix does not re-litigate categorization.
- **VIPI-Reasoning:** out of scope for v0.1.3 per methodology §4 L96 + §15 L539. Planned for v0.2 (within 60 days post-launch).
- **VIPI-Best multi-provider observability expansion for VIPI-Closed:** per pre-reg Sub-index publication scope L42, v0.1.4-or-later milestone.

## Signoff

Operator (Administrator) signature: Jack Arnot, Volt HQ.
Date of populating this appendix from template: 2026-05-18 (Day 78, base date).
Commit hash at sign-off: (see `git log -- docs/vipi/VIPI_v0.1.3_reproducibility_appendix.md`).

The methodology, plan, curation policy, pre-registration, and migrations cited here are at the commit indicated above. Subsequent edits to these files after the sign-off date constitute methodology changes under §12.3 and require the 30-day-announcement + 14-day-consultation regime.

---

*This appendix is published under CC-BY-4.0 alongside the methodology document. Citation format follows methodology §10.3.*
