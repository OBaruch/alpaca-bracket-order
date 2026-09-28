# Code Overview

This is a file-by-file walkthrough of the original code in [`../src/`](../src/). The code is described here but not changed.

## Dependency graph

```
config.py ◄── from config import * ── order.py
    ▲
    └──────── from config import * ── bracket_order.py

sample_config.py   (not imported by anything; example only)
```

The two scripts do not import each other. Each one is a separate script that does all of its work at module level.

---

## `src/config.py`

```python
API_KEY = ""
SECRET_KEY = ""
```

- Holds the credentials. Both scripts load them with `from config import *`.
- Committed with empty values (**confirmed**).

## `src/sample_config.py`

```python
API_KEY_ID = "yourapikey"
SECRET_KEY = "yoursecretkey"
```

- An example credentials file with placeholder values.
- **Naming mismatch (confirmed):** it defines `API_KEY_ID`, but the scripts read `API_KEY`. Nothing imports this file.

## `src/order.py`: simple market orders (paper trading)

**Constants**

| Name | Value |
|---|---|
| `BASE_URL` | `https://paper-api.alpaca.markets` |
| `ACCOUNT_URL` | `{BASE_URL}/v2/account` |
| `ORDERS_URL` | `{BASE_URL}/v2/orders` |
| `HEADERS` | `{'APCA-API-KEY-ID': API_KEY, 'APCA-API-SECRET-KEY': SECRET_KEY}` |

**Functions**

| Function | HTTP | Returns |
|---|---|---|
| `get_account()` | `GET /v2/account` | Parsed JSON (not called anywhere) |
| `create_order(symbol, qty, side, type, time_in_force)` | `POST /v2/orders` with the five fields as the JSON body | Parsed JSON |
| `get_orders()` | `GET /v2/orders` | Parsed JSON |

**Top-level execution flow**

1. `create_order("AAPL", 100, "buy", "market", "gtc")`
2. `create_order("MSFT", 1000, "buy", "market", "gtc")`
   (the `response` variable is overwritten and never used)
3. `orders = get_orders()`
4. `print(orders)`

## `src/bracket_order.py`: bracket order (live trading URL)

**Constants:** the same as `order.py`, except that `BASE_URL = "https://api.alpaca.markets"` (**live** trading).

**Functions:** `get_account()` and `get_orders()`, both copied from `order.py`. Neither is called. `create_order` is **not** defined in this file, even though the commented-out lines refer to it.

**Commented-out block (kept from `order.py`)**

```python
#response = create_order("AAPL", 100, "buy", "market", "gtc")
#response = create_order("MSFT", 1000, "buy", "market", "gtc")
#orders = get_orders()
#print(orders)
```

**Top-level execution flow**

1. Builds the bracket order payload:

   | Field | Value |
   |---|---|
   | `symbol` | `"AAPL"` |
   | `qty` | `1` |
   | `side` | `"buy"` |
   | `type` | `"market"` |
   | `time_in_force` | `"gtc"` |
   | `order_class` | `"bracket"` |
   | `take_profit.limit_price` | `"320"` |
   | `stop_loss.stop_price` | `"314"` |

2. `requests.post(ORDERS_URL, json=data, headers=HEADERS)`
3. `json.loads(r.content)` → `print(response)`

A bracket order creates three linked orders: the market entry, then a take-profit limit sell and a stop-loss stop sell. These two exit orders are one-cancels-other: when one fills, Alpaca cancels the other.

## Shared patterns

- The URL constants, `HEADERS`, `get_account` and `get_orders` are copied between the two scripts instead of being shared.
- Responses are parsed with `json.loads(r.content)` and HTTP status codes are not checked.
- There is no `if __name__ == "__main__":` guard, so importing a script sends its orders.
