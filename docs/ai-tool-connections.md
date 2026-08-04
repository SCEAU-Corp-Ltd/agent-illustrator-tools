# AI Tool Connections

Adobe documents the Illustrator MCP server as a Beta feature for connecting
desktop AI tools to Illustrator (Beta).

The workflows in this repository work with any MCP-capable AI assistant.
Connect your assistant to the Adobe MCP server, then follow the same tool
sequence regardless of which AI tool you use. Per-tool notes are in the table
below.

## Adobe for creativity in Claude (recent update)

Adobe now provides an **Adobe for creativity** connector that runs inside Claude and exposes a broader creative toolset across multiple Adobe apps.

For vector-related work, this means you can ask Claude for outcomes like:

- converting raster artwork into vector-style results
- preparing logo-style vector assets
- continuing edits in Illustrator for manual cleanup and refinement

Start here:

- Adobe connector landing page: https://developer.adobe.com/adobe-for-creativity/
- Adobe launch post: https://blog.adobe.com/en/publish/2026/04/28/adobe-for-creativity-connector
- Illustrator vectorize reference: https://www.adobe.com/products/illustrator/vectorize-image.html

Practical guidance:

- Treat Claude prompts as the orchestration layer ("what result you want").
- Use Illustrator final-pass controls (anchor cleanup, path simplification, shape edits) before production export.
- Keep all tokens and account credentials out of this repository.

## Tool Notes

| AI tool | Public note |
|---|---|
| Claude Code | Adobe documents Claude Code as a supported desktop AI tool path. Adobe's "Adobe for creativity" connector in Claude can also orchestrate vector-oriented tasks directly; ask Claude for the outcome, then finalize in Illustrator if needed. See the "Adobe for creativity in Claude" section above. |
| Codex | Use the Adobe MCP server connection and ask Codex to adapt the workflow into Codex-compatible tool calls. |
| Cursor | Adobe documents Cursor as a supported desktop AI tool path. Ask Cursor to adapt the workflow to its MCP settings format. |
| Illustrator Claw | Point an Illustrator Claw workspace at this repo, configure MCP servers privately, and keep tokens out of git. Start with `docs/illustrator-claw-public-setup.md`; agents can use `skills/illustrator-claw/SKILL.md` as `$illustrator-claw`. |
| Other tools | Use the Adobe MCP server URL/key pattern only if the tool supports compatible MCP configuration. Ask the AI tool to adjust the workflow accordingly. |

## Public Safety

Do not paste real MCP keys or full connection commands into this repository.
Connection commands can include authentication material unique to an Illustrator
(Beta) installation.

For Illustrator Claw, keep workspace names, hostnames, bearer tokens, API keys, local
paths, and agent logs in private Illustrator Claw state. Public docs should explain what
needs to exist and how to verify it, without copying the private values.

## Official References

- [About using desktop AI tools with Adobe Illustrator (Beta)](https://helpx.adobe.com/au/illustrator/desktop/connect-with-other-apps-and-tools/about-using-ai-tools-with-illustrator.html)
- [Connect Adobe Illustrator (Beta) to AI tools](https://helpx.adobe.com/illustrator/desktop/connect-with-other-apps-and-tools/connect-illustrator-to-ai-tools.html)
- [Work with Adobe Illustrator (Beta) documents from AI tools](https://helpx.adobe.com/illustrator/desktop/connect-with-other-apps-and-tools/work-with-illustrator-documents-from-ai-tools.html)
