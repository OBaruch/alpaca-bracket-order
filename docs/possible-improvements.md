# Possible Improvements

> **None of these improvements have been applied.** The code in [`../src/`](../src/) is intentionally left as the original implementation. This list is only a reference for a hypothetical future version.

## Safety

1. **Live endpoint in `bracket_order.py`.** `BASE_URL` is `https://api.alpaca.markets`, so a run with live credentials trades real money. A future version could default to paper trading and require explicit opt-in for live trading, for example through an environment variable.
2. **Side effects on import.** Both scripts send orders at module level. A `main()` function with an `if __name__ == "__main__":` guard would prevent accidental execution.
3. **Credentials in a tracked Python file.** `config.py` is committed. Reading credentials from environment variables, or from an untracked file listed in `.gitignore`, would lower the risk of leaking keys.

## Correctness

4. **Config naming mismatch.** `sample_config.py` defines `API_KEY_ID`, but the code reads `API_KEY`.
5. **Dangling references.** `bracket_order.py` has commented-out calls to `create_order`, which that file does not define.
6. **No HTTP error handling.** `r.status_code` is never checked, so errors from Alpaca (403, 422 and others) are printed as if they were normal results.
7. **Stale hard-coded prices.** The bracket levels `320` / `314` date from 2020 (before AAPL's 4:1 split in August 2020). Computing them relative to the current price would keep the example valid.

## Maintainability

8. **Duplicated code.** The URL constants, `HEADERS`, `get_account` and `get_orders` are repeated in both scripts and could live in a shared module.
9. **Wildcard import.** `from config import *` hides where names come from.
10. **Shadowed built-in.** The parameter `type` in `create_order` shadows Python's built-in `type`.
11. **Parameters.** Symbols, quantities and prices could come from CLI arguments instead of being hard-coded.
12. **Dependency declaration.** No `requirements.txt` or version pin exists for `requests`.
13. **Official SDK.** Alpaca publishes an official Python SDK that could replace the raw HTTP calls.

## Tooling (optional)

14. Unit tests with a mocked HTTP layer, a linter and a formatter. None of these existed in the original project, and they were deliberately not added.
