# AGENTS.md

Repository overview and conventions live in `CLAUDE.md` (public boundary rules,
MCP tool surface, recolor workflow, menu-command reference). Read it first.

## Cursor Cloud specific instructions

This is a documentation + small-utility repo. The tools are **pure Python
standard library** (Python 3.12 is preinstalled) — there is nothing to `pip
install`, and there is no test/lint framework configured. Do not add
`requirements.txt`/`pyproject.toml` unless a task genuinely needs a third-party
dependency.

Services / scripts and how to exercise them:

- Syntax "lint": `python3 -m py_compile tools/build-menu-command-links.py tools/listener-playground/listener.py`. This is the only build/lint gate.
- Generated doc: `docs/illustrator-menu-command-links.md` is produced from `data/illustrator-menu-commands.csv` by `python3 tools/build-menu-command-links.py`. Never hand-edit it; regenerate and confirm `git status` is clean (the generator is idempotent).
- Listener service (`tools/listener-playground/listener.py`): a runnable local HTTP server. Start with `python3 tools/listener-playground/listener.py --host 127.0.0.1 --port 8765`. Keep it bound to `127.0.0.1`. The repo docs use PowerShell (`Invoke-RestMethod`); on this Linux VM use `curl` instead, e.g. `curl -s http://127.0.0.1:8765/health` and `curl -s -X POST http://127.0.0.1:8765/test/x -H 'Content-Type: application/json' -d '{"m":"hi"}'`, then `curl -s http://127.0.0.1:8765/events/latest`. Captured events go to `.tmp/listener-events.jsonl` (gitignored).

The Adobe Illustrator MCP workflows described in `CLAUDE.md` require Adobe
Illustrator (Beta) with its MCP server — that desktop app is not present in this
Linux VM, so those flows cannot be run here.
