# Images

This folder holds the diagrams used by the docs, plus optional slots for your
own screenshots.

## Diagrams (already here)

| File | Used by | What it shows |
|---|---|---|
| `how-it-works.svg` | `docs/non-developer-start-here.md` | You → AI tool → MCP → Illustrator |
| `mcp-tools-panel.svg` | `docs/non-developer-start-here.md` | The **Preferences › MCP & Tools** panel — Cursor, Claude Code, Codex and Others rows, tokens masked |

These are original illustrations, not screenshots — modelled on the real panel
with the bearer tokens redacted. They render directly on GitHub.

Both are drawn on an opaque white card so the near-black type stays readable in
GitHub's dark theme, and both carry small CSS animations (a pulse travelling
down the pipeline, ticks drawing in, masked tokens shimmering). GitHub loads
them through an `<img>` tag, so declarative CSS animation runs but scripting
does not — keep them script-free. Both honour `prefers-reduced-motion`.

## Adobe's official screenshots — link, don't copy

Adobe's own help pages already show real screenshots of the **MCP & Tools**
panel and the **Connect** buttons. We **link** to those pages instead of copying
the images, because they are Adobe's copyright and they change when Adobe
updates the UI. See `docs/non-developer-start-here.md` for the links.

## Optional: add your own screenshots

If you want screenshots inside this repo, capture them yourself **in a clean
state** and drop them here. Before committing any screenshot, confirm it does
**not** show:

- client artwork, document names, or thumbnails
- bearer tokens, API keys, or full MCP connection strings
- account names, email addresses, or local file paths
- the recent-files list

Suggested shot list (filenames the guide will pick up if present):

| Filename | Capture this | Scrub before saving |
|---|---|---|
| `01-preferences-mcp.png` | **Preferences › MCP & Tools** with **Enable MCP server** ticked | The bearer tokens in the Claude Code / Codex / Others rows |
| `02-tool-connected.png` | Your AI tool after a successful test prompt | Client document names |

Keep the artwork blank or disposable for every capture, and **black out the
token fields** before saving — the tokens are real secrets.

> If you give me a screenshot saved to a file path in this repo (e.g.
> `docs/images/raw.png`), I can redact the token fields for you with Pillow and
> replace it. Pasted/streamed images aren't reachable by the file tools — they
> have to land on disk first.
