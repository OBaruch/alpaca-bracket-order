# Project Context

This document records what can and cannot be determined about the origin of this project. Each statement is labeled:

- **Confirmed**: directly supported by files or git history.
- **Inferred**: a reasonable deduction from the repository.
- **Unknown**: cannot be determined from the repository.

## Classification

**Project origin: Technical Experiment / Proof of Concept (inferred)**

The repository contains no evidence of academic origin: no assignment, course name, university, rubric or report. For that reason it is **not** classified as an academic project.

## Sources reviewed

| Source | Present? | Notes |
|---|---|---|
| PDF / Word / PowerPoint | No | None in the repository |
| Images / diagrams | No | None in the repository |
| Notebooks / datasets / outputs | No | None in the repository |
| Original README | Yes | Only one line: `# alpaca` |
| Source code | Yes | `order.py`, `bracket_order.py`, `config.py`, `sample_config.py` |
| Code comments | Yes | Only commented-out calls in `bracket_order.py` |
| Git history | Yes | One commit |

## Evidence

| Statement | Status | Evidence |
|---|---|---|
| The project uses the Alpaca Trading API v2 | Confirmed | URLs `paper-api.alpaca.markets` / `api.alpaca.markets` and paths `/v2/account` and `/v2/orders` |
| It is written in Python and uses `requests` | Confirmed | `import requests, json` |
| The original commit is dated 2020-04-24 | Confirmed | `git log`: `initial commit`, Fri Apr 24 18:59:55 2020 -0700 |
| The git author of the original commit is `financehacks` | Confirmed | `git log` author field |
| The repository is now hosted under the `OBaruch` GitHub account | Confirmed | Git remote URL |
| `order.py` was written before `bracket_order.py` | Inferred | `bracket_order.py` repeats the header of `order.py` and keeps its `create_order` calls as comments, without the function definition |
| The project was an exploration of order types, not a product | Inferred | Hard-coded values, no CLI or arguments, no error handling, top-level execution |
| The bracket prices (`320` take-profit, `314` stop-loss) were set for AAPL's price at the time | Inferred | Plausible for pre-split AAPL (the 4:1 split was in August 2020); the exact entry price is not recorded |
| Switching `bracket_order.py` to the live endpoint was intentional | Unknown | Nothing in the repository explains the change from `paper-api` to `api` |
| Whether the project came from a course, a tutorial or personal study | Unknown | The original repository does not provide enough information to determine this |
| How `financehacks` (commit author) relates to the `OBaruch` account | Unknown | The history might come from an import or a different git identity; this cannot be confirmed |

## Scope

- **In scope (confirmed):** authentication headers, submitting market orders, submitting one bracket order, listing orders. `get_account()` is defined but never called.
- **Out of scope (confirmed by absence):** strategy logic, market data, position management, scheduling, persistence, tests, packaging.

## Contradictions found

- `sample_config.py` defines `API_KEY_ID`, while `config.py` and both scripts use `API_KEY`. The template therefore does not match the code that uses it. This is documented here and has **not** been fixed.
- `order.py` uses the paper-trading URL, while `bracket_order.py` uses the live-trading URL. The repository does not say which environment was intended for the bracket order.
