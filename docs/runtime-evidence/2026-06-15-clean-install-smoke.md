# 2026-06-15 Clean Install Smoke Evidence

| Field | Value |
|---|---|
| Date | 2026-06-15 |
| Scenario | Clean repository install smoke |
| Route / provider | Repository tooling; no model provider runtime |
| Repository commit | `7033ff1b7a46f626856a13799a0f2f65bd304838` |
| Source location | Fresh clone at `C:\tmp\zilan-clean-install-20260615` |
| Redaction note | No secrets were present in these command outputs. |
| Runtime log entry | `docs/runtime-validation-log.md#2026-06-15-clean-install-smoke` |

## Commands

The checks were run sequentially:

```powershell
git clone https://github.com/RyanYao527/zilan-agent.git C:\tmp\zilan-clean-install-20260615
python scripts\validate_zilan_repo.py --check-generated --strict-yaml
python -m pytest
python -m ruff check scripts tests
python scripts\openai_api_harness.py --case ZC-02
python scripts\search_agama.py --terms "無我|非我|緣起" --limit 5
git status -sb
```

## Output Excerpts

Repository invariant and generated-file check:

```text
zilan-agent validation passed.
```

Pytest:

```text
collected 15 items
tests\test_openai_api_harness.py ...                                     [ 20%]
tests\test_search_agama.py ........                                      [ 73%]
tests\test_validate_zilan_repo.py ....                                   [100%]
15 passed
```

Ruff:

```text
All checks passed!
```

OpenAI API harness dry-run:

```text
mode: dry-run
model: gpt-5.5
case: ZC-02
endpoint: https://api.openai.com/v1/responses
reference_files:
  - SKILL.md
  - context/因明推理引擎.md
  - context/摄类学工具箱.md
```

Agama search smoke:

```text
Found 5 matches for /無我|非我|緣起/
《長阿含經》(T01n0001) 卷 1, context/agama/T0001-chang-agama.md:881
  | 若學決定法，知諸法無我；
《長阿含經》(T01n0001) 卷 2, context/agama/T0001-chang-agama.md:1119
  | ... 苦無我想 ...
《長阿含經》(T01n0001) 卷 3, context/agama/T0001-chang-agama.md:1829
  | 陰、界、入無我，乃名第一供。」
```

Final clean status:

```text
## main...origin/main
```

## Result

| Check | Result | Notes |
|---|---|---|
| Clean clone | `pass` | Repository cloned from GitHub into a fresh temp directory. |
| Repository invariant validation | `pass` | Included generated Agama idempotency check. |
| Pytest | `pass` | 15 tests passed in the clean clone. |
| Ruff | `pass` | No lint findings. |
| OpenAI API harness | `pass` | Dry-run request construction only; no live provider call. |
| Agama search smoke | `pass` | Returned local Markdown citations. |
| Final git status | `pass` | Clean clone remained clean after sequential checks. |

## Limitations

- This evidence validates installation and repository tooling, not agent answer quality.
- The OpenAI API harness output is dry-run only and does not promote OpenAI API from `harness-ready` to `tested`.
- Generated-file validation should be run sequentially before pytest; running it in parallel can create transient read/write races.
