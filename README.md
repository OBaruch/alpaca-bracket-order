# alpaca-bracket-order

Small Python scripts that send stock orders to the [Alpaca](https://alpaca.markets/) brokerage REST API (v2). One script places simple market orders on a paper-trading account. The other places a **bracket order**: a market entry with a take-profit and a stop-loss attached.

> **Original implementation notice**
> This repository keeps the original implementation of the project. The source code has deliberately not been refactored or modernized, so it still shows the historical context and the original way it was built. Everything in [`src/`](src/) is byte-for-byte the code from the initial commit.

---

## Project Overview

| | |
|---|---|
| **Type** | Technical experiment / API proof of concept (inferred) |
| **Language** | Python 3 (inferred from syntax) |
| **External service** | Alpaca Trading API v2 (`/v2/account`, `/v2/orders`) |
| **Size** | 2 executable scripts + 2 credential files |
| **Original commit date** | 2020-04-24 |

The scripts call the Alpaca REST API directly over HTTP with the `requests` library, without using an Alpaca SDK. They show the JSON payloads for two order types:

1. **Simple market orders** (`src/order.py`): buys `AAPL` and `MSFT` on the **paper** trading endpoint and prints the account's order list.
2. **Bracket order** (`src/bracket_order.py`): buys 1 share of `AAPL` at market, with a take-profit limit at `320` and a stop-loss at `314`, and prints the API response.

## Project Context

**Project origin: Technical Experiment / Proof of Concept (inferred, not confirmed).**

- **Confirmed:** the repository has one commit, `initial commit`, dated 2020-04-24. Git records its author as `financehacks`.
- **Confirmed:** there are no PDFs, Word documents, slides, images, notebooks or other documents. The original `README.md` only said `# alpaca` (kept in [`docs/original/README.original.md`](docs/original/README.original.md)).
- **Inferred:** the hard-coded tickers and prices, the commented-out experiments and the lack of any structure point to a hands-on exploration of the Alpaca order API. It does not look like a finished application.
- **Unknown:** whether this was part of a course, a tutorial, or a personal study. The repository does not say.

See [`docs/project-context.md`](docs/project-context.md) for the evidence behind each point.

## Problem Statement

Placing an order by hand means clicking through a broker UI. This project tries to send orders from code instead, including a bracket order, where the exit orders are defined together with the entry order (*inferred from the code*).

## Objective

Show, with as little code as possible, how to:

- authenticate against the Alpaca REST API with API-key headers;
- submit a simple market order;
- submit a bracket order (`order_class: "bracket"`) with `take_profit` and `stop_loss` legs;
- read back the account's orders.

## Repository Structure

```
alpaca-bracket-order/
├── README.md                  ← this file (added during reorganization)
├── AGENTS.md                  ← rules for automated contributors (preserve src/)
├── .gitignore                 ← added during reorganization
├── src/                       ← ORIGINAL source code, unchanged
│   ├── order.py               ← simple market orders (paper endpoint)
│   ├── bracket_order.py       ← bracket order (live endpoint!)
│   ├── config.py              ← credentials read by both scripts (empty)
│   └── sample_config.py       ← example credentials file
└── docs/
    ├── project-context.md     ← origin, evidence, confirmed/inferred/unknown
    ├── code-overview.md       ← file-by-file walkthrough
    ├── possible-improvements.md ← observations only, NOT applied
    ├── sdlc/
    │   ├── intent.md          ← why the project exists (reconstructed)
    │   ├── spec.md            ← as-built behavioral specification
    │   └── plan.md            ← as-built implementation + reorganization plan
    └── original/
        └── README.original.md ← the original one-line README
```

The repository has no data files, images, datasets or generated outputs, so it has no `data/` or `assets/` folders.

## Original Implementation

The files in `src/` were moved from the repository root without being edited. Their contents are unchanged, including:

- the naming mismatch between `config.py` (`API_KEY`) and `sample_config.py` (`API_KEY_ID`);
- the commented-out calls to `create_order`, which `bracket_order.py` does not define;
- hard-coded tickers, quantities and prices;
- the live-trading base URL in `bracket_order.py`.

These points are documented in [`docs/possible-improvements.md`](docs/possible-improvements.md), which also explains why they were left as they are.

## Technologies

Only technologies found in the code are listed:

- **Python** (the version is not specified; the syntax is Python 3 compatible)
- **[`requests`](https://pypi.org/project/requests/)**: HTTP client (third-party, imported by both scripts)
- **`json`**: standard library, used to parse responses
- **Alpaca Trading API v2**: `paper-api.alpaca.markets` and `api.alpaca.markets`

## How It Works

```
config.py ──(API_KEY, SECRET_KEY)──► HEADERS {APCA-API-KEY-ID, APCA-API-SECRET-KEY}
                                            │
          order.py ─────────────────────────┤──► POST/GET https://paper-api.alpaca.markets/v2/orders
          bracket_order.py ─────────────────┘──► POST     https://api.alpaca.markets/v2/orders
                                                        │
                                                        ▼
                                           JSON response → print()
```

1. Each script does `from config import *` to load `API_KEY` and `SECRET_KEY`.
2. It builds the Alpaca authentication headers.
3. It sends the order payload(s) with `requests.post(..., json=data)`.
4. It parses the response with `json.loads(r.content)` and prints it.

All the work happens at module level, so importing or running either script sends orders right away. See [`docs/code-overview.md`](docs/code-overview.md) for details.

## Inputs and Outputs

| | Description |
|---|---|
| **Input** | API credentials in `src/config.py`; order parameters are hard-coded in each script |
| **Output** | The JSON response from Alpaca, printed to stdout |
| **Side effects** | **Real orders are submitted to Alpaca.** `order.py` uses paper trading; `bracket_order.py` uses the **live** endpoint |

## Running the Project

> ⚠️ **`bracket_order.py` targets `https://api.alpaca.markets` (live trading).** With live credentials it submits a real order for 1 share of AAPL. It runs as soon as it is executed or imported.

The following is based on the code; the repository does not include original run instructions:

```bash
cd src
pip install requests          # the only third-party import
# edit config.py and set API_KEY and SECRET_KEY
python order.py               # paper account: buys 100 AAPL + 1000 MSFT, prints orders
python bracket_order.py       # LIVE account: bracket order on 1 AAPL
```

Notes:

- Run the scripts from inside `src/` so that `from config import *` finds `config.py`.
- `sample_config.py` defines `API_KEY_ID`, but the scripts read `API_KEY`. Copying the sample file as-is will fail. Use the variable names from `config.py`.
- The hard-coded bracket prices (`320` / `314`) were chosen for the market conditions of 2020. Alpaca may reject them today.
- The repository does not record the Python or `requests` versions the scripts were written with.

## Documentation

- [Project context](docs/project-context.md)
- [Code overview](docs/code-overview.md)
- [Possible improvements (not applied)](docs/possible-improvements.md)
- SDLC artifacts: [Intent](docs/sdlc/intent.md) · [Spec](docs/sdlc/spec.md) · [Plan](docs/sdlc/plan.md)
- [Original README](docs/original/README.original.md)

## Historical Note

This repository was later reorganized and documented to make it easier to read and to preserve the context of the original project. The source code is unchanged. The files were only moved from the repository root into `src/`.
