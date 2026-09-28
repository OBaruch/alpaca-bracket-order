# AGENTS.md

These are the rules for any contributor, human or automated, working in this repository.

## Project in one line

Original 2020 Python scripts that submit market orders and a bracket order to the Alpaca Trading API v2. Start with the [README](README.md).

## Hard rules

1. **`src/` is read-only.** It holds the original implementation. Do not edit, reformat, lint, rename or "fix" anything in it. The integrity hashes are listed in [`docs/sdlc/plan.md`](docs/sdlc/plan.md).
2. **Do not run the scripts casually.** They send orders when executed or imported. `src/bracket_order.py` targets the **live** trading endpoint.
3. **Never commit real credentials** to `src/config.py` or anywhere else.
4. **Do not delete historical material.** Keep it in `docs/original/`.
5. **Label claims** in the documentation as Confirmed, Inferred or Unknown. Do not invent context.

## Source of truth

| Question | Document |
|---|---|
| Why does this exist? | [`docs/sdlc/intent.md`](docs/sdlc/intent.md) |
| What exactly does the code do? | [`docs/sdlc/spec.md`](docs/sdlc/spec.md) |
| How was it built and reorganized? | [`docs/sdlc/plan.md`](docs/sdlc/plan.md) |
| Where does it come from? | [`docs/project-context.md`](docs/project-context.md) |
| What could be better (not applied)? | [`docs/possible-improvements.md`](docs/possible-improvements.md) |

## Workflow for changes

Documentation changes: edit the Markdown files, keep relative links working, and open a pull request.

Code changes: do not modify `src/`. Write a new intent, spec and plan first, then build the new code in a separate directory.
