# Specification (as built)

> **Retroactive artifact.** This spec describes what the original code in [`../../src/`](../../src/) **does**, including its quirks. It was not written before the code. Any gap between this spec and the code is a documentation bug; the code itself must not change. For the motivation see [`intent.md`](intent.md).

## 1. Configuration

| ID | Requirement | Source |
|---|---|---|
| CFG-1 | Credentials are read from a Python module `config` on the import path, which exposes `API_KEY` and `SECRET_KEY`. | `from config import *` |
| CFG-2 | The shipped `config.py` has both values set to empty strings. | `src/config.py` |
| CFG-3 | `sample_config.py` is an example that defines `API_KEY_ID` and `SECRET_KEY`. It does **not** satisfy CFG-1 as written. | `src/sample_config.py` |

## 2. Authentication

| ID | Requirement |
|---|---|
| AUTH-1 | Every request sends the headers `APCA-API-KEY-ID: <API_KEY>` and `APCA-API-SECRET-KEY: <SECRET_KEY>`. |

## 3. Endpoints

| ID | Script | Base URL | Environment |
|---|---|---|---|
| EP-1 | `order.py` | `https://paper-api.alpaca.markets` | Paper trading |
| EP-2 | `bracket_order.py` | `https://api.alpaca.markets` | **Live trading** |

Both scripts use `/v2/account` and `/v2/orders` under their base URL.

## 4. Functional behavior

### 4.1 `order.py`

| ID | Behavior |
|---|---|
| ORD-1 | `create_order(symbol, qty, side, type, time_in_force)` POSTs `{symbol, qty, side, type, time_in_force}` as JSON to `/v2/orders` and returns the parsed JSON body. |
| ORD-2 | `get_orders()` GETs `/v2/orders` and returns the parsed JSON body. With no query parameters, the result is Alpaca's default order listing. |
| ORD-3 | `get_account()` GETs `/v2/account` and returns the parsed JSON body. It is never called. |
| ORD-4 | When executed, the script submits, in this order: a market GTC buy for 100 `AAPL`, then a market GTC buy for 1000 `MSFT`. |
| ORD-5 | It then prints the result of `get_orders()` to stdout. |

### 4.2 `bracket_order.py`

| ID | Behavior |
|---|---|
| BRK-1 | When executed, the script POSTs one order to `/v2/orders` with this body: |

```json
{
  "symbol": "AAPL",
  "qty": 1,
  "side": "buy",
  "type": "market",
  "time_in_force": "gtc",
  "order_class": "bracket",
  "take_profit": { "limit_price": "320" },
  "stop_loss":   { "stop_price": "314" }
}
```

| ID | Behavior |
|---|---|
| BRK-2 | It prints the parsed JSON response to stdout. |
| BRK-3 | It defines `get_account()` and `get_orders()`, identical to ORD-3 and ORD-2 except for the base URL. Neither is called. |
| BRK-4 | It does not define `create_order`. The references to it are commented out. |

## 5. Non-functional characteristics (observed)

| ID | Characteristic |
|---|---|
| NF-1 | All side effects run at module level: importing a script sends its orders. |
| NF-2 | HTTP status codes are not checked. Error bodies are parsed and printed like successful ones. |
| NF-3 | A body that is not JSON raises an unhandled exception in `json.loads`. |
| NF-4 | The only third-party dependency is `requests`. The Python version is not specified. |
| NF-5 | There is no logging, retry logic or rate-limit handling. |

## 6. Acceptance (how to verify this spec against the code)

- Reading `src/*.py` confirms every row above; no other behavior exists.
- The SHA-256 hashes of `src/*.py` match the files from the initial commit (see [`plan.md`](plan.md), Phase 3).
