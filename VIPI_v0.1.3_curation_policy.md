# VIPI v0.1.3 Curation Policy

## Purpose

This document locks the curator-judgment principles for the Session 4 inaugural basket mapping seed (`0008_seed_vcmi_provider_mappings_inaugural.sql`) and the inaugural basket selection (`0009_seed_vipi_basket_inaugural.sql`). The pre-registration (`VIPI_v0.1.3_pre_registration.md`) names six curator-discretion zones and binds the maintainer to a published policy for each. This policy is that publication. It converts each discretion zone into a written rule the curator applies the same way across every (provider, VCMI) pair on the inaugural eligibility universe of approximately 188 pairs across eight providers.

Edits to this policy after May 18, 2026 trigger a v0.1.4 release per methodology §13. Edits before May 18, 2026 are tracked in the changelog at the top of this file and do not require a methodology version bump.

---

## Anchor and provenance

This document is committed at 57bfc7f968581310b061e44008b4b656691bb3a9.

It cites methodology, plan, and pre-registration at HEAD `b3727f9325dd9bba8181a4312fba413c4a915f52` on `origin/main` (commit message: "openai adapter: refresh gpt-5.4-mini rate to $0.75/$4.50 per primary source"). The pre-registration anchor is `45398cd281677ce0639bb460a2ae410838a983f9`.

Primary sources cited below resolve at the anchor commit. Subsequent edits to those primary sources do not retroactively re-bind this policy. The Day 55 verification block in this document is the audit trail.

---

## §5(6) eligibility rulings (locked)

Methodology §5 criterion 6 reads: "Non-opaque pricing. The VCMI's pricing is expressed in per-input-token and per-output-token terms (not per-GPU-hour, not subscription, not freemium). This excludes `pricePerGpuHour`-only offerings and subscription tiers." The eligibility query enforces this criterion before any confidence assignment runs.

### Akash (excluded)

The Akash adapter (`packages/workers/price-aggregator/src/adapters/akash.ts`) pulls per-GPU-hour rates from `console-api.akash.network` and converts them to per-token estimates via `gpuHourToPerToken()` from `@volthq/core`. The adapter header self-documents the conversion. Per-token rates produced this way are derived, not posted, and the underlying market structure is GPU-hour rental rather than per-token billing.

Ruling: Akash mappings are classified `pricing_methodology = 'gpu_hour_derived'` and fail §5(6). All four Akash pairs on the inaugural eligibility universe are excluded.

AkashML (`akashml.com`) is a separate Akash product offering real per-token rates per `akash.network/blog/akashml-managed-ai-inference-on-the-decentralized-supercloud/`. AkashML publishes static HTML pricing only, has no machine-readable API, and has no adapter in the codebase as of Day 55. AkashML eligibility is deferred to v0.2 (post-launch). When the AkashML adapter is built, the AkashML mappings are evaluated against §5(6) at that time.

### All other eight providers (eligible under §5(6))

The remaining eight providers post per-input-token and per-output-token rates as the canonical pricing form. Each is classified `pricing_methodology = 'per_token'`:

| Provider | Pricing form | Source |
|---|---|---|
| Anthropic | Per-token, posted | `anthropic.com/pricing` (static adapter) |
| Cerebras | Per-token, Developer tier | `inference-docs.cerebras.ai/pricing` (static adapter) |
| DeepInfra | Per-token, live API | `deepinfra.ts` reads `pricing.cents_per_input_token` / `pricing.cents_per_output_token` |
| Fireworks | Per-token, parameter-bucketed | `fireworks.ai/pricing` (static adapter, bucketed) |
| Groq | Per-token, Developer tier | `groq.com/pricing` (static adapter) |
| Hyperbolic | Per-token, posted | `docs.hyperbolic.xyz/docs/hyperbolic-pricing` (static adapter) |
| OpenAI | Per-token, posted | `developers.openai.com/api/docs/pricing` (static adapter) |
| Together | Per-token, live API | `together.ts` reads `pricing.per_million_input` / `pricing.per_million_output` |

The Day 55 damage assessment confirmed each adapter rate matches the cited primary source within the tolerances stated below. Eligibility under §5(6) does not by itself imply confidence under §6; confidence is assigned separately per the next section.

---

## Silent-FALLBACK exclusion (locked)

### Rule

A provider mapping is excluded from the inaugural eligibility universe if its `provider_model_id` appears in the static `FALLBACK_MODELS` array of a catalog-gated adapter (`cerebras.ts`, `fireworks.ts`, `groq.ts`, `hyperbolic.ts`) AND is not independently confirmed via that adapter's live model-catalog endpoint at curation time.

The rule applies to catalog-gated adapters only. Static-by-design adapters (`anthropic.ts`, `openai.ts`) emit a single hardcoded model list with no live-fetch path and are governed by §6 confidence rules rather than the silent-FALLBACK exclusion. The `api_inline_price` adapters (`deepinfra.ts`, `together.ts`) source prices directly from live API responses and have no comparable silent-FALLBACK pathway.

### Operationalization

Silent-FALLBACK omission is applied at the worksheet-generation layer during inaugural basket construction rather than through a query-time SQL exclusion filter. The Administrator operationalizes the rule through `tools/coverage_check.py --registry-state 0013 --verbose`, which decodes the curator worksheet against the current adapter source and omits mappings whose `provider_model_id` remains confined to a catalog-gated adapter's static `FALLBACK_MODELS` array without live catalog confirmation. Each scheduled rebalancing re-runs the same coverage-check process against the then-current adapter state, allowing mappings that subsequently appear in the provider's live catalog response to re-enter the eligibility universe at the next reconstitution event.

### Disclosure

Per-day silent-FALLBACK exclusions are disclosed in the daily audit record under `vipi_audit_records.audit_json.silent_fallback_exclusions`, parallel in shape to the `outlier_exclusions` key defined at methodology §10.4. Methodology §11.3 references this disclosure as the per-determination publication mechanism for silent-FALLBACK exclusion events.

---

## Confidence-level policy (locked)

VCMI v0.1.3 §6 defines `high`, `medium`, and `low` confidence and lists four sufficient grounds for `high`: (a) identical upstream source URL declared by the provider, (b) byte-level weight verification, (c) direct written confirmation, or (d) trivial decomposition of the provider's model string into VCMI components with documented brand-suffix meaning. The maintainer applies the rules below to assign confidence at the time of seed.

### Live-API providers (DeepInfra, Together)

Eligible for `high` confidence under §6(d) when the live-API model string trivially decomposes into the VCMI components: family + version + size match the registry entry, and any brand suffix maps to a documented quantization or serving tier.

When the brand suffix is undocumented, the mapping is recorded at `medium` and flagged in the `notes` field per VCMI §6 brand-suffix guidance step 4.

### Static-source verified providers (Anthropic, Cerebras, Groq, Hyperbolic, OpenAI)

Eligible for `high` confidence when all three conditions hold:

1. The provider's model string trivially decomposes into the VCMI components per §6(d).
2. The adapter's hardcoded rate matches the primary-source page within published rounding tolerance, verified within the trailing 30 days.
3. Quantization is declared by the provider or unambiguous from the model name (e.g., a model published only at native precision).

Where the third condition fails (quantization undeclared and ambiguous), the mapping is recorded at `medium` with the ambiguity captured in `notes`.

### Parameter-bucket pricing (Fireworks)

Fireworks publishes per-million-token rates bucketed by parameter range (4B–16B, >16B, MoE 56.1B–176B) per `fireworks.ai/pricing`. The adapter at `fireworks.ts:31-40` applies bucket rates to specific model names. The rate is real per-token; the price-discovery mechanism is the parameter bucket, not a per-model contract.

Ruling: Fireworks mappings cap at `medium` confidence. The §6(d) "trivial decomposition" criterion assumes per-model pricing. Parameter-bucket pricing is a structural deviation from that assumption: the rate is observed at parameter-tier granularity, not negotiated per-model.

The curator note required in the `notes` field for every Fireworks mapping reads: "Rate applies at parameter-tier granularity per fireworks.ai/pricing, not negotiated per-model."

### FREEMIUM_QUALIFIED providers (Cerebras, Groq)

Cerebras and Groq publish Developer-tier per-token rates alongside free rate-limited tiers. Methodology §15 specifies "standard-tier pricing" as the rate of record. The Developer tier is the commercial standard; the free tier is rate-limited and not a substitute for paid usage.

Ruling (Day 55, locked by operator): FREEMIUM_QUALIFIED providers are eligible for `high` confidence when the static-source criteria above are met. Multi-tier offerings (free + paid) do not by themselves cap confidence. Anthropic, OpenAI, and other providers with batch, cached, or regional tiers receive identical treatment: the standard interactive on-demand tier is the rate of record, and the existence of cheaper or specialized tiers does not lower confidence.

The curator note required in the `notes` field for every Cerebras and Groq mapping reads: "Rate is the paid Developer tier; free rate-limited tier exists alongside per provider pricing page."

---

## Curation atom and grouping

The curation atom is the triple (`provider_id`, `model`, `quantization`), not the pair (`provider_id`, `model`). The Hyperbolic adapter ingests the same model identifier at multiple quantizations as separate offerings: `meta-llama/Llama-3.1-70B-Instruct` appears at FP8 ($0.40/M) and BF16 ($0.55/M) as two distinct rows. Treating these as a single curation pair would conflate distinct VCMIs (`vcmi:llama-3.1-70b/fp8` and `vcmi:llama-3.1-70b`) into one mapping.

VCMI v0.1.3 §6 explicitly handles quantization in confidence assignment: a provider serving FP8 is mapped to the FP8 VCMI; a provider serving BF16 native precision is mapped to the native VCMI. The eligibility query, the seed migration, and the basket selection all treat (`provider_id`, `model`, `quantization`) as the unit.

This was a Day 54 grouping artifact corrected on Day 55. Any Day 54 analyses that grouped on (`provider_id`, `model`) are superseded.

---

## Curator-discretion zones from pre-registration

The pre-registration explicitly lists six curator-discretion zones. The principle for each is locked here.

### Inaugural basket selection

The maintainer selects inaugural constituents by running the §5 inclusion query, then applying the §6 cap and tie-breaker cascade as written. Each inclusion or exclusion call is documented in the `0009` commit message with one line per constituent: VCMI, sub-index, decision, citation. The first scheduled rebalancing in June 2026 follows the same pattern.

### Retention bias at rebalancing

Methodology §5 retention bias permits the maintainer to leave a constituent in place for one additional rebalancing cycle when a violation is transient. This rule does not apply to the inaugural basket because no prior cycle exists. Retention bias is first available at the first scheduled rebalancing on July 3, 2026. The principle is deferred to that date.

### Reasoning-model classification

Methodology §5(5) excludes reasoning-specialized models from VIPI and routes them to VIPI-Reasoning when that index launches. Classification is applied via the VCMI `notes` field where the `variant` string is ambiguous.

The default is non-reasoning (eligible for VIPI) unless one of two conditions holds:
1. The `variant` field contains the string `reasoning`.
2. The VCMI `notes` field explicitly categorizes the model as reasoning-specialized.

Borderline cases (a model that supports a reasoning mode as a configurable option but defaults to non-reasoning) default to non-reasoning. The categorization decision is recorded in the VCMI `notes` field and re-reviewed at v0.2.

### Unrecognized-license classification

Methodology §5(7) and the licensing edge case at §5 line 96 specify that licenses not classifying cleanly as open-weight or proprietary may qualify for the headline VIPI but are excluded from VIPI-Open and VIPI-Closed.

The default for an unrecognized license is: eligible for headline VIPI, ineligible for VIPI-Open, ineligible for VIPI-Closed. The exclusion remains in place until the maintainer publishes a classification update at the next methodology release. Unrecognized-license rulings are recorded in the VCMI `notes` field with a one-line citation of the upstream license document.

### Sustained loss-leader / window-dressing exclusions

Methodology §7.3 (outlier exclusion) and §9.3 (sustained loss-leader treatment) grant the maintainer discretion to exclude observations that distort the index. Any exclusion is announced in the daily audit record (`vipi_audit_records.audit_json.outlier_exclusions`) per plan §3.6.

This discretion is not applied to the inaugural basket. The inaugural basket has no prior observation history of a sustained loss-leader pattern. Exclusions of this type require a documented prior pattern in the daily audit record across at least 14 consecutive days. The principle is first available after Day 67 (May 18 + 14 days).

### Provider-mapping confidence levels

The confidence-level policy in the previous section locks this zone. The curator applies the rules above to every mapping in the inaugural seed. Each mapping carries a `notes` field explaining the confidence assignment when the ground is anything other than a literal §6(a) URL match.

---

## Implementation note

§5(6) enforcement is captured as a typed `pricing_methodology` column on `vcmi_provider_mappings`, not as a hardcoded WHERE clause in the eligibility query. Values are `per_token`, `gpu_hour_derived`, `subscription`, and `freemium`. The eligibility query filters where `pricing_methodology = 'per_token'`.

This shape was selected over a hardcoded clause because it preserves the §5(6) ruling as queryable data: a future audit can reproduce the inaugural exclusion of Akash by reading the seed migration, without needing to inspect query source code. It also leaves room for future per-token classifications (for example, if AkashML ships an adapter and is classified `per_token` at v0.2).

Migration `0010_add_pricing_methodology.sql` adds the column before `0008` runs. The migration prompt is a separate Codex step and is not part of this document.

---

## Verification commitment

On May 18, 2026, the `0008` and `0009` SQL migrations land on `origin/main` with commit messages documenting curator decisions per this policy. Each commit message includes a one-line citation per mapping or constituent.

A reproducibility appendix (a separate document targeted for Day 65 to Day 70) demonstrates basket reproducibility from the locked rules in this policy plus the locked mappings in the seed migration. The appendix is published before May 18.

Any deviation between this policy and the realized `0008` mappings is disclosed under methodology §12.1, reason class `metadata_correction`. Deviation discovered before May 18 is corrected in the seed migration before initial deployment. Deviation discovered after May 18 follows the §12.1 correction workflow.
