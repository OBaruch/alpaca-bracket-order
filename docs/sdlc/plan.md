# Plan

This plan has two parts:

- **Part A** reconstructs how the original code appears to have been built (retroactive).
- **Part B** is the plan for the 2026 repository reorganization: what was done and the guardrails it followed.

Related: [`intent.md`](intent.md) · [`spec.md`](spec.md)

---

## Part A: Original implementation (reconstructed, inferred)

The order below is inferred from the code: `bracket_order.py` reuses the header of `order.py` and keeps its calls as comments.

| Step | Work | Artifact | Spec coverage |
|---|---|---|---|
| A1 | Put the credentials in a local module | `config.py`, `sample_config.py` | CFG-1..3 |
| A2 | Build the auth headers and endpoint constants for paper trading | `order.py` | AUTH-1, EP-1 |
| A3 | Write thin wrappers for account, orders and create-order | `order.py` | ORD-1..3 |
| A4 | Try out market orders and list the results | `order.py` | ORD-4, ORD-5 |
| A5 | Copy the script, comment out the old calls, change the base URL | `bracket_order.py` | EP-2, BRK-3, BRK-4 |
| A6 | Build and submit a bracket order payload | `bracket_order.py` | BRK-1, BRK-2 |

---

## Part B: Repository reorganization (2026)

### Guardrails

1. **Do not change source code.** No edits, reformatting, renames or fixes inside `src/*.py`.
2. **Preserve everything.** No file is deleted. Superseded material is moved to `docs/original/`.
3. **Do not invent anything.** Every claim in the docs is labeled Confirmed, Inferred or Unknown.
4. **Do not over-engineer.** No CI, containers, package managers, linters or test frameworks.

### Phases

| Phase | Task | Result |
|---|---|---|
| 1. Discover | Inventory every file, read all code, inspect git history | 4 Python files, 1 one-line README, 1 commit (2020-04-24). No PDFs, Word files, images or data |
| 2. Recover context | Cross-check code, comments and history; record contradictions | [`../project-context.md`](../project-context.md) |
| 3. Restructure | `git mv` the `*.py` files → `src/`; original `README.md` → `docs/original/README.original.md` | File contents unchanged; SHA-256 checked before and after (below) |
| 4. Document | README, code overview, possible improvements, intent/spec/plan | `README.md`, `docs/*.md`, `docs/sdlc/*.md` |
| 5. Guard | `AGENTS.md` (contributor and agent rules), minimal `.gitignore` | Root files |
| 6. Review | Deliver on a dedicated branch through a pull request | Reviewable diff: renames plus new docs only |

### Integrity check (Phase 3)

The SHA-256 hashes of the original files must be identical before and after the move:

```
a2bed30423e452a0bf788f60a98c31e110ccc9ede6766f92231303e7802dec83  bracket_order.py
0bc34350a69a5ee480ab0aa8a5a8d2e25db9c28aec9615713186494bf93dbaf7  config.py
b454530ece641c09d5c766f5eefd92ec3759e657ee49e3bcb7c2ad5271da13a3  order.py
0c78d5aa8d6ec7b28c51c6fadc2379f4b092ed70af1b042eede4f2764addc1bc  sample_config.py
```

To check: `cd src && sha256sum *.py`.

### Out of scope

Everything listed in [`../possible-improvements.md`](../possible-improvements.md). Any future change to the code needs a new intent, spec and plan, and should go in a separate location (for example a new `v2/` directory), so that `src/` stays the historical implementation.
