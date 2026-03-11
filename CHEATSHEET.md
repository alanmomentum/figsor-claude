# Figsor + Claude Code — Cheat Sheet

## One-time setup

```bash
claude mcp add --transport stdio --scope project figsor -- npx -y figsor
```

Or copy `figsor/.mcp.json` into your project root.

## Before each session

1. Open Figma
2. Run the Figsor plugin
3. Turn **Peer Design** off (for simple tasks)

## Prompt template

```
Use the Figsor MCP server.
Call get_connection_status, then get_design_craft_guide("skill").
[Your design request here — e.g. "Draw a smiley in Frame 1"]
Keep Peer Design off.
```

## Key tools

| Task | Tool |
|------|------|
| Check plugin connected | `get_connection_status` |
| Load design rules | `get_design_craft_guide` |
| Find nodes | `find_nodes` |
| Inspect node | `read_node_properties` |
| Create shapes | `create_frame`, `create_ellipse`, `create_rectangle`, `create_text`, `create_vector` |
| Style | `set_fill`, `set_stroke`, `set_auto_layout` |
| Edit | `modify_node` |

## Port conflict fix

```bash
lsof -tiTCP:3055 -sTCP:LISTEN | xargs kill
```

Then restart Claude’s MCP session once.

## Rules to follow

- Read `get_design_craft_guide("skill")` before designing
- Use `#111111` for text, not `#000000`
- Use frames + auto-layout for containers
- No indigo/violet accents unless asked
- 4px or 8px spacing grid
