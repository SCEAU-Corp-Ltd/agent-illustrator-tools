# CLAUDE.md

This file describes the repository for AI assistants working in this codebase.

## Repository Purpose

Public notes and examples for using Adobe Illustrator MCP server tools with
AI-assisted automation workflows. The primary automation pattern is AI-driven
Illustrator control (recoloring, layout, export) through the Adobe MCP server,
with orchestration handled through any MCP-capable AI assistant (Claude, Codex,
Cursor, or a compatible Illustrator Claw agent runner).

This depends on Adobe Illustrator (Beta) and its MCP server feature.

## Repository Structure

```
agent-illustrtator-tools/
├── data/
│   └── illustrator-menu-commands.csv   # MIT-licensed AiCommandPalette data
├── docs/
│   ├── adobe-illustrator-mcp-tools.md  # 43-function MCP surface snapshot
│   ├── ai-tool-connections.md          # Notes for Codex, Claude Code, Cursor
│   ├── illustrator-claw-automation-blueprints.md
│   ├── illustrator-claw-public-setup.md
│   ├── illustrator-menu-command-links.md  # Generated index (do not edit by hand)
│   └── public-boundary.md              # What belongs in this repo
├── skills/
│   └── illustrator-claw/
│       ├── SKILL.md                    # Codex skill definition
│       └── agents/openai.yaml          # Agent interface metadata
├── tools/
│   ├── build-menu-command-links.py     # Regenerates docs/illustrator-menu-command-links.md
│   └── listener-playground/
│       ├── listener.py                 # Local HTTP listener for MCP/MCPO experiments
│       ├── send-test-event.ps1
│       └── README.md
└── workflows/
    ├── illustrator-recolor.md          # Step-by-step recolor workflow
    └── mcp-listener-environment.md     # How to run the local listener
```

## Key Conventions

### Public Boundary

This repository contains only generic, public, reusable documentation. Before
committing anything, confirm it contains none of:

- API keys, bearer tokens, auth files, session files, logs, or caches
- Private Illustrator Claw workspace names, hostnames, gateway details, or agent logs
- Client artwork, document names, screenshots, file paths, or production prompts
- Machine-specific setup notes, local profiles, or private absolute paths

Use placeholders like `[local MCP URL]` or `[private Illustrator Claw workspace]`
when an example needs a value.

Pre-commit check:

```bash
git diff --check
rg -n "auth[.]json|session_index|state_[0-9]+[.]sqlite|logs_[0-9]+[.]sqlite|BEGIN (RSA|OPENSSH|PRIVATE) KEY" README.md CONTRIBUTING.md NOTICE.md SECURITY.md docs data tools workflows skills
```

### Generated Files

`docs/illustrator-menu-command-links.md` is generated from
`data/illustrator-menu-commands.csv`. Do not edit it by hand. Regenerate it after
any CSV change:

```bash
python3 -m py_compile tools/build-menu-command-links.py  # syntax check
python3 tools/build-menu-command-links.py                 # regenerate
```

### Gitignored Paths

The following are never committed (see `.gitignore`):

- `.tmp/`, `logs/`, `cache/`, `sessions/`, `archived_sessions/`
- `*.env`, `*.local.*`, `auth.json`, `cap_sid`, `installation_id`
- `profiles/`, `machines/`, `exports/`, `screenshots/private/`, `screenshots/local/`
- `__pycache__/`, `*.py[cod]`, `*.bak`

## Adobe Illustrator MCP Tools

The MCP surface has 43 callable functions organized in these categories:

| Category | Functions |
|---|---|
| Document | `CreateDocument`, `ListDocuments`, `OpenDocument`, `SwitchDocument` |
| Artboards | `CreateArtboard`, `DeleteArtboard`, `DuplicateArtboard`, `FitArtboard`, `GetActiveArtboard`, `ListArtboards`, `MoveArtboards`, `RenameArtboard`, `ScaleArtboards`, `SetActiveArtboard` |
| Structure | `GetCanvasStructure`, `GetArtboardStructure`, `GetObjectStructure`, `VisualizeSelection` |
| Objects & Layers | `CreateLayer`, `CreateGroup`, `CreateClippingMask`, `MoveObjectsToContainer`, `ArrangeArt`, `RenameObject`, `DeleteObjects`, `SelectObjects`, `DuplicateObjects` |
| Layout & Transform | `AlignObjects`, `DistributeObjects`, `MoveObjects`, `RotateObjects`, `ScaleObjects`, `GetBounds`, `GetGeometry` |
| Appearance & QA | `SetAppearance`, `GetVisualAppearance`, `EditSimpleText`, `GetTypographyMetrics`, `GetImageProperties`, `GetLinksInformation`, `RunPreflightChecks`, `CapturePreview`, `Export` |

Full attributes for each function are in `docs/adobe-illustrator-mcp-tools.md`.

Important attribute rules:
- `uuids` is always an array, even for a single object
- Artboard indices are zero-based
- Canvas coordinates are in points, Y increasing downward
- Only use `force`, `locked=false`, or `hidden=false` when the user explicitly requests it

## Automation Safety Defaults

1. Begin every automation with a read-only inventory step (list tools, list
   documents, inspect artboard, capture preview, run preflight).
2. Use dry runs before transforms, recolors, cleanup, or exports.
3. Require human approval before destructive, bulk, locked-art, or hidden-art edits.
4. Prefer explicit MCP functions over menu commands when both can do the job.
5. Store generated logs, screenshots, previews, and exports outside this repo.
6. Use `Export` with `overwrite=false` unless overwrite is explicitly approved.

## Recolor Workflow (Summary)

Full workflow: `workflows/illustrator-recolor.md`

Tool sequence:
1. `ListDocuments` / `GetActiveArtboard` — confirm target document
2. `GetCanvasStructure` / `GetObjectStructure` — identify target objects
3. `GetVisualAppearance` — read current color state
4. `GetImageProperties` + `GetLinksInformation` — check for raster/linked assets before promising editable color
5. `SetAppearance` — apply color changes (group UUIDs by final color, one call per color group)
6. `CapturePreview` — verify result
7. `RunPreflightChecks` — required before final export
8. `Export` — only when a file output is requested

### Symbol Content Limitation

`GetObjectStructure` treats symbol instances as opaque. `SetAppearance` on a
symbol container places the fill behind internal content and does not visibly
recolor the artwork.

To recolor paths inside a symbol, use the macOS AppleScript bridge to enter and
exit isolation mode:

```bash
osascript -e 'tell application "Adobe Illustrator" to do javascript "app.executeMenuCommand(\"enterFocus\")"'
```

The MCP server refuses all calls while in isolation mode — only the AppleScript
bridge or an `ExecuteMenuCommand` tool (when available) can operate there.

## Menu Command Reference

`data/illustrator-menu-commands.csv` and `docs/illustrator-menu-command-links.md`
provide a searchable index of 786 Illustrator menu command values sourced from
the MIT-licensed AiCommandPalette dataset.

Rules for using menu commands:
- Use the `value` field as the command identifier
- Check `docRequired` before running commands that need an open document
- Check `selRequired` before running commands that need a selection
- Respect `minVersion` and `maxVersion`
- Avoid `ignore`-marked rows unless testing compatibility
- `executeMenuCommand` returns `undefined` — verify success by inspecting document state

Common command values:

| Value | Menu path | Use |
|---|---|---|
| `enterFocus` | Other Object > Isolate Selected Object | Enter symbol isolation |
| `exitFocus` | Other Object > Exit Isolation Mode | Return to document root |
| `undo` | Edit > Undo | Undo last action |
| `selectall` | Edit > Select All | Select all at current level |
| `Expand3` | Object > Expand... | Expand selected objects |
| `expandStyle` | Object > Expand Appearance | Expand appearance attributes |

## Listener Playground

`tools/listener-playground/listener.py` is a local HTTP debug listener for
MCP/MCPO event inspection. Run it only on `127.0.0.1`; do not bind to
`0.0.0.0`. Runtime output goes to `.tmp/listener-events.jsonl` (gitignored).

```bash
# Start listener
python tools/listener-playground/listener.py --host 127.0.0.1 --port 8765

# PowerShell: health check
Invoke-RestMethod http://127.0.0.1:8765/health

# PowerShell: send test event
powershell -ExecutionPolicy Bypass -File tools/listener-playground/send-test-event.ps1
```

Full workflow: `workflows/mcp-listener-environment.md`

## Illustrator Claw Skill

`skills/illustrator-claw/SKILL.md` defines the `$illustrator-claw` skill for
Codex-compatible agents. Invoke it with:

```text
Use $illustrator-claw to connect my Illustrator Claw workspace to this repo
and run a safe read-only verification.
```

The skill enforces the public boundary rules automatically and provides the
correct load-order for reference docs.

## Contributing

Accepted: generic workflow notes, public Adobe documentation links, public
reference data with attribution, fully redacted examples, small test utilities
with no private credentials.

Not accepted: MCP keys, bearer tokens, private profiles, client or project
names, generated logs, sessions, caches, or private Illustrator Claw agent
details. See `CONTRIBUTING.md` for the full list.

## Official Adobe References

- [About using desktop AI tools with Adobe Illustrator (Beta)](https://helpx.adobe.com/au/illustrator/desktop/connect-with-other-apps-and-tools/about-using-ai-tools-with-illustrator.html)
- [Connect Adobe Illustrator (Beta) to AI tools](https://helpx.adobe.com/illustrator/desktop/connect-with-other-apps-and-tools/connect-illustrator-to-ai-tools.html)
- [Work with Adobe Illustrator (Beta) documents from AI tools](https://helpx.adobe.com/illustrator/desktop/connect-with-other-apps-and-tools/work-with-illustrator-documents-from-ai-tools.html)
