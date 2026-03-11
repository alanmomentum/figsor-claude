# Figsor With Claude Code

Short answer: no, you do not need Cursor open.

What you do need:

- `Figma` open with the `Figsor` plugin running
- `Node.js 18+`
- `Claude Code` with MCP enabled
- one `figsor` server instance listening on port `3055`

The actual flow is:

`Claude Code -> MCP (stdio) -> Figsor server -> WebSocket -> Figma plugin -> canvas`

## Recommended Setup

From the project where you want Claude Code to use Figsor, add it as an MCP server:

```bash
claude mcp add --transport stdio --scope project figsor -- npx -y figsor
```

If you want it available across all projects instead:

```bash
claude mcp add --transport stdio --scope user figsor -- npx -y figsor
```

Claude Code stores project-scoped servers in `.mcp.json` and user/local-scoped servers in `~/.claude.json`.

## Manual Config

If you prefer to edit config manually, use this shape:

```json
{
  "mcpServers": {
    "figsor": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "figsor"]
    }
  }
}
```

Use that in:

- `.mcp.json` for project scope
- `~/.claude.json` for user scope

## Before You Ask Claude To Design

1. Open a Figma file.
2. Run the `Figsor` plugin inside Figma.
3. Wait for the plugin to be idle and ready.
4. Make sure `Peer Design` is off unless you specifically want the multi-agent cursor behavior.

For simple edits and drawings, keeping `Peer Design` off makes things much more reliable.

## What Claude Should Do

When Claude uses Figsor, it should follow this sequence:

1. Call `get_connection_status`
2. Call `get_design_craft_guide` with `"skill"`
3. If needed, also call `get_design_craft_guide` with `"typography"`, `"color"`, or `"anti-ai-slop"`
4. Find the target node with `find_nodes`
5. Inspect it with `read_node_properties`
6. Create or modify nodes with tools like:
   - `create_frame`
   - `create_rectangle`
   - `create_ellipse`
   - `create_text`
   - `create_vector`
   - `modify_node`
   - `set_fill`
   - `set_stroke`
   - `set_auto_layout`

Important Figsor-specific rules:

- Read `get_design_craft_guide("skill")` before creating a design
- Use `#0B0B0B` or `#111111` instead of pure black text
- Use frames plus auto-layout for containers
- Do not use indigo or violet accents unless you explicitly want purple
- Use a `4px` or `8px` spacing rhythm

## Good Prompt Template For Claude

Use prompts like this:

```text
Use the Figsor MCP server.

First:
- call get_connection_status
- call get_design_craft_guide with "skill"

Then:
- find the node named "Frame 1"
- inspect it
- make the requested change inside that frame

Constraints:
- keep Peer Design off unless needed
- use simple shapes first
- keep the result centered and clean
- tell me what nodes you created or changed
```

Example:

```text
Use the Figsor MCP server to draw a smiley face in "Frame 1".

First call get_connection_status, then get_design_craft_guide("skill").
Find "Frame 1", inspect it, and draw the smiley centered inside it using basic shapes.
Keep Peer Design off.
```

## Troubleshooting

### Problem: Claude sees the server, then it disappears

This usually means the stdio client disconnected but the old `figsor` process kept running.

Check port `3055`:

```bash
lsof -nP -iTCP:3055 -sTCP:LISTEN
```

If a stale `figsor` process is still there, stop it:

```bash
lsof -tiTCP:3055 -sTCP:LISTEN | xargs kill
```

Then restart the Claude MCP session once, not repeatedly.

### Problem: `EADDRINUSE` on port `3055`

That means another `figsor` process is already listening on the port.

Fix:

1. Kill the stale process holding `3055`
2. Keep the Figma plugin open
3. Start one clean Claude MCP connection

### Problem: Claude can call tools but nothing appears in Figma

Usually the Figma plugin is not connected to the same running `figsor` instance.

Checklist:

- Figma is open
- the `Figsor` plugin is open in the active file
- the plugin has connected after the MCP server started
- there is only one `figsor` process using `3055`

### Problem: simple tasks feel overcomplicated

Turn `Peer Design` off.

It adds agent cursor workflow rules that are useful for collaborative-style generation, but unnecessary for tasks like:

- drawing a face
- editing a button
- changing colors
- moving a few nodes

## Suggested First Test

After setup, ask Claude:

```text
Use the Figsor MCP server.
Call get_connection_status and tell me whether the Figma plugin is connected.
```

If that works, move on to a harmless edit:

```text
Use the Figsor MCP server.
Call get_connection_status, then get_design_craft_guide("skill").
Find "Frame 1" and draw a small smiley face centered inside it.
```

## Practical Advice

- Do not run Cursor and Claude Code against the same Figsor port at the same time
- Keep one client session active at a time
- Keep the Figma plugin window open while Claude is working
- For small tasks, ask Claude to use basic shapes before vectors
- If the connection gets weird, kill the stale `figsor` process and reconnect cleanly
