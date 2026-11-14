# APCTT Hackathon 2026: TRL Assessment of Technical Reports

Our entry for the [2nd APCTT Hackathon 2026: AI for Technology Readiness](https://apctt.org/events/2nd-apctt-hackathon-2026-ai-technology-readiness) ([problem statement](https://apctt.org/apctt-hackathon-2026-problem-statement)).

## The problem

A **Technology Readiness Level (TRL)** rates how mature a technology is, from 1 (basic idea) to 9 (proven in real operation). Experts normally work it out by reading technical reports.

Our job is to build a Python module that reads a report PDF and returns:

1. A status for each of 12 checklist criteria: `met`, `not met` or `insufficient evidence`.
2. For every `met` criterion, the page number and a short quote from that page.
3. One TRL from 1 to 9, or an abstention if the report can't support one.

## Scoring

`total = 0.45 × A + 0.40 × B + 0.15 × C`

| Part | Weight | What it checks |
|---|---|---|
| A | 45% | TRL matches the experts (exact = 1, off by 1 = 0.5, abstain = 0.25) |
| B | 40% | Criterion statuses and cited pages match the experts' pages |
| C | 15% | Quotes really appear on the cited page and support the claim |

A failed case (crash, timeout, invalid output) scores 0, so the module must never crash.

## Key rules

- Max 3 minutes per case, including model loading.
- No network. The Python environment is fixed and no extra packages are allowed.
- Only the 23 catalogue models (see the kit's `MODEL_CATALOG.md`) or weights we train ourselves.
- Our own code must be MIT licensed.
- Up to 2 submissions. The latest valid one before the deadline counts.
- **Stage 1 deadline: 15 November 2026, 11:59 pm IST.**
- Final round: 16–17 December 2026 at Techfest, IIT Bombay.

## Approach

CPU-only, rules plus retrieval, with no LLM:

1. Extract text page by page (pypdfium2, with pypdf as a fallback).
2. Score sentences against each criterion using keyword and regex patterns.
3. Mark a criterion `met` only when a strong match exists, and quote the exact sentence.
4. Choose the TRL from the highest level with evidence.
5. Guard against timeouts and errors, and always return a valid result.

## Repo layout

```text
context/
  links.md                           official event and problem links
  USGS_SDG_open_data_set_corrected.xlsx   100 USGS reports with estimated TRLs
  apctt-participant-kit-0.5.0-*/     organizer's kit (server, tools, docs)
my_submission/                       our module (to be built)
```

## Quick start (macOS or Linux/WSL2)

The kit does not run on native Windows. From the kit folder:

```bash
uv venv --python 3.12 .venv
uv pip install --python .venv/bin/python -r requirements-dev.lock
uv pip install --python .venv/bin/python --no-deps -e .
.venv/bin/python tools/make_env.py --base-dir ../apctt-env/base
.venv/bin/python tools/get_public_cases.py --download --smoke --dir ../public_cases/pdf
```

See the kit's `README.md` for the full 10-step guide, including running cases and packing the submission with `tools/make_submission.py`.

## Status

- [x] Gather context and rules
- [ ] Build the `AssessmentModule`
- [ ] Test on public sample cases
- [ ] Pack and submit

## Licence

Our code is MIT. USGS reports are U.S. public domain (credit: U.S. Geological Survey).
