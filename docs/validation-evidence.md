# Runtime Validation Evidence Policy

> Last updated: 2026-06-15

This policy defines what counts as validation evidence for Zilan platform routes and how to record it without leaking secrets or overclaiming support.

## Evidence Levels

| Level | Counts As | Does Not Prove |
|---|---|---|
| Repository CI | Required files, YAML shape, generated Agama idempotency, tests, lint, and smoke scripts are coherent. | Agent answer quality or provider runtime behavior. |
| Dry-run harness | A request can be built deterministically without credentials. | The external provider accepted or answered the request. |
| Manual runtime summary | A human-reviewed run records prompt set, model/runtime, observed behavior, failures, and checks. | Full transcript auditability unless transcript excerpts are attached. |
| Transcript-backed runtime | Redacted transcript excerpts or saved outputs support the summary. | That all future provider versions behave the same way. |
| Live provider run | A dated external API/runtime response was observed with model, commit, and prompt set recorded. | Scholarly correctness beyond the supplied corpus and review method. |

## Where To Record Evidence

- Summary evidence belongs in `docs/runtime-validation-log.md`.
- Platform status belongs in `docs/platform-validation.md` and `agents/openai.yaml`.
- Optional transcript excerpts may be committed under a future `docs/runtime-evidence/` directory when they are safe and useful.
- Large raw transcripts, private account logs, and provider dashboards should not be committed. Summarize them instead.

## Minimum Runtime Entry

Every manual runtime entry should include:

- date
- route and provider
- model ID and tool/runtime version
- repository commit
- prompt set or case IDs
- whether local context was supplied and how
- tools used, especially search or file-write tools
- pass/fail result per case
- observed failures and follow-up fixes
- transcript status
- repository checks run

## Redaction Rules

Never commit:

- API keys, bearer tokens, cookies, or account identifiers
- private billing, organization, or dashboard metadata
- private user content unrelated to the validation case
- raw provider payloads that include secrets or durable request IDs tied to private accounts

Use short markers such as `[redacted-api-key]`, `[private-account]`, or `[omitted-private-context]` when a detail is necessary for the audit trail.

## Status Promotion Rules

Move a route to `tested` only when all of the following are true:

- a dated runtime or live provider run exists
- the exact prompt set or equivalent task is documented
- model ID and runtime/tool version are recorded
- the route used the intended local context or documented context bundle
- failures are recorded, not just final successes
- repository checks pass after any prompt, metadata, or harness changes

Keep a route at `harness-ready` when a runnable harness exists but no live provider response is recorded.

Keep a route at `config-only` when metadata exists but there is no runnable harness or dated runtime evidence.

Use `blocked` only when validation is intentionally blocked by missing access, provider behavior, or an unresolved integration issue that has been observed and documented.

## Transcript Quality

Transcript-backed evidence is strongest when it includes:

- the exact user prompt
- the route/model/runtime line
- the key context files or search commands used
- representative answer excerpts
- explicit boundary statements
- citations or generated file paths
- any failed first attempts that affected the final fix

Do not commit full transcripts by default. Prefer small, redacted excerpts that explain the behavioral claim being made.
