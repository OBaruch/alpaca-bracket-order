# Intent

> **Retroactive artifact.** This intent was reconstructed in 2026 from the code that already exists (initial commit, 2020-04-24). It describes what the original code shows the author was trying to do. It is not a new requirement. Labels: **Confirmed** / **Inferred** / **Unknown**, as defined in [`../project-context.md`](../project-context.md).

## Why

Learn and demonstrate how to place stock orders programmatically through the Alpaca Trading API, and in particular how to submit a **bracket order**. A bracket order defines the entry, the profit target and the protective stop in a single request. *(Inferred from the code and the repository name.)*

## Who

- **Primary user:** the author, running the scripts locally with their own Alpaca API keys. *(Inferred: there is no CLI, packaging or multi-user concern.)*
- **Secondary audience today:** readers of a technical portfolio who want to see a minimal, working-shaped example of Alpaca REST calls.

## Desired outcomes

| # | Outcome | Status in original code |
|---|---|---|
| O1 | Authenticate to Alpaca with API-key headers | Confirmed: implemented |
| O2 | Submit a simple market order | Confirmed: `order.py` |
| O3 | List the account's orders | Confirmed: `order.py` |
| O4 | Submit a bracket order with take-profit and stop-loss legs | Confirmed: `bracket_order.py` |
| O5 | Read account information | Partially: `get_account()` exists but is never called |

## Non-goals

These are confirmed by their absence from the code:

- Trading strategies, signals or market-data consumption.
- Position or risk management beyond the bracket's own stop.
- Reusable library, CLI, service or UI.
- Tests, packaging, deployment.

## Constraints

- Plain HTTP with `requests`; no Alpaca SDK. *(Confirmed)*
- Credentials are supplied through a local `config.py` module. *(Confirmed)*
- The order parameters are hard-coded. *(Confirmed)*

## Open questions (Unknown)

- Was the live endpoint in `bracket_order.py` intended, or left in by accident?
- Was the project part of a course, a tutorial or personal study?
