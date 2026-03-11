# Figsor-Claude

**Chat in Claude Code. Design in Figma.**

This is a fork of [Figsor](https://github.com/AsifKabirAntu/figsor) by [Asif Kabir](https://github.com/AsifKabirAntu), adapted for use with [Claude Code](https://docs.anthropic.com/en/docs/claude-code) instead of (or in addition to) Cursor.

Figsor is an MCP server that bridges your AI coding assistant to [Figma](https://www.figma.com), enabling chat-driven design creation and editing — directly on your Figma canvas.

```
Claude Code → MCP (stdio) → Figsor Server → WebSocket → Figma Plugin → Design on Canvas
```

> **Attribution:** All credit for Figsor goes to [Asif Kabir](https://github.com/AsifKabirAntu). This fork only adds documentation and configuration for Claude Code users. The server code is unchanged. If you find Figsor useful, consider supporting the original author with [Figsor Pro](https://asifkabirantu.gumroad.com/l/oxoopm).

---

## What This Fork Adds

- **Claude Code setup guide** — MCP configuration via `claude mcp add` or `.mcp.json`
- **`for-claude.md`** — detailed usage notes, prompt templates, and troubleshooting for Claude Code
- **`CHEATSHEET.md`** — quick-reference card for daily use
- **No code changes** — the Figsor server and Figma plugin are identical to upstream

---

## Setup

### 1. Install the Figma Plugin (Sideload)

Clone this repo and import the plugin into Figma:

```bash
git clone https://github.com/alanmomentum/figsor-claude.git
```

In Figma: **Plugins → Development → Import plugin from manifest** → select `figsor-claude/figma-plugin/manifest.json`

### 2. Add to Claude Code

The recommended way:

```bash
claude mcp add --transport stdio --scope project figsor -- npx -y figsor
```

Or for all projects:

```bash
claude mcp add --transport stdio --scope user figsor -- npx -y figsor
```

Alternatively, add a `.mcp.json` file to your project root:

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

### 3. Add to Cursor (Optional)

If you also use Cursor, add to your Cursor MCP settings:

```json
{
  "mcpServers": {
    "figsor": {
      "command": "npx",
      "args": ["-y", "figsor"]
    }
  }
}
```

### 4. Start Designing

1. Open a Figma file
2. Run the Figsor plugin (Plugins → Development → Figsor)
3. Chat in Claude Code:

> "Use the Figsor MCP server. Call get_connection_status, then get_design_craft_guide('skill'). Create a mobile login screen with email and password fields in Frame 1."

---

## Claude Code Tips

- **Always start** by calling `get_connection_status` to verify the plugin is connected
- **Always load** `get_design_craft_guide("skill")` before designing — it contains spacing, color, and typography rules
- **Turn Peer Design off** for simple tasks (drawing shapes, editing nodes, changing colors)
- **Use `#111111` for text**, not pure black — this is a Figsor design craft rule
- **Use frames + auto-layout** for containers, with 4px or 8px spacing
- **One client at a time** — don't run Cursor and Claude Code against the same Figsor port simultaneously

See [`for-claude.md`](for-claude.md) for the full guide and prompt templates, or [`CHEATSHEET.md`](CHEATSHEET.md) for a quick reference.

### Port Conflict Fix

If you get `EADDRINUSE` or the server disappears:

```bash
lsof -tiTCP:3055 -sTCP:LISTEN | xargs kill
```

Then restart the Claude MCP session once.

---

## Available Tools (45+)

### Create & Layout

| Tool | Description |
|------|-------------|
| `create_frame` | Create frames (screens, sections, cards) |
| `create_text` | Add text with font, size, weight, color |
| `create_rectangle` | Create rectangles and shapes |
| `create_ellipse` | Create circles and ovals |
| `create_line` | Create lines and dividers |
| `create_svg_node` | Create icons and vector graphics from SVG |
| `set_auto_layout` | Configure flexbox-style auto-layout |
| `modify_node` | Edit any existing element |
| `set_stroke` | Add borders and strokes |
| `set_effects` | Add shadows and blur effects |
| `delete_node` | Remove elements |
| `move_to_parent` | Restructure the layer hierarchy |

### Read & Inspect

| Tool | Description |
|------|-------------|
| `get_selection` | Read the current selection |
| `get_page_structure` | Get the full page tree |
| `read_node_properties` | Inspect any node's properties |
| `find_nodes` | Search for elements by name or type |
| `set_selection` | Select and zoom to elements |
| `get_local_styles` | Read the file's design tokens |
| `list_components` | Browse available components |
| `create_component_instance` | Use existing components |
| `detach_instance` | Convert instances to frames |

### Vector Drawing & Advanced Fills

| Tool | Description |
|------|-------------|
| `create_vector` | Draw custom shapes with the pen tool |
| `boolean_operation` | Union, subtract, intersect, or exclude shapes |
| `flatten_nodes` | Flatten nodes into a single editable vector |
| `set_fill` | Apply solid colors, linear/radial/angular/diamond gradients, multiple fills |

### Image, Typography & Constraints

| Tool | Description |
|------|-------------|
| `set_image_fill` | Place image fills on nodes |
| `style_text_range` | Apply mixed styling within text |
| `set_constraints` | Set responsive constraints |
| `list_available_fonts` | Discover available fonts |

### Component & Variable Tools

| Tool | Description |
|------|-------------|
| `create_component` | Create a new main component |
| `create_component_set` | Combine components into a variant set |
| `create_variable_collection` | Create a design token collection with modes |
| `create_variable` | Create a COLOR, FLOAT, STRING, or BOOLEAN token |
| `bind_variable` | Bind a token to a node property |
| `get_variables` | List all variable collections and tokens |

### SVG Export & Animation

| Tool | Description |
|------|-------------|
| `export_as_svg` | Export any node as SVG markup |
| `show_animation_preview` | Live animated SVG previews + ZIP download |

### AI-Powered SVG (Quiver)

| Tool | Description |
|------|-------------|
| `quiver_generate_svg` | Generate SVG graphics from text prompts |
| `quiver_vectorize_svg` | Convert raster images to clean SVG |

### Peer Design (Multi-Agent)

| Tool | Description |
|------|-------------|
| `spawn_design_agent` | Spawn AI designer agents with visible cursors |
| `dismiss_design_agent` | Remove an agent cursor |
| `dismiss_all_agents` | Remove all agent cursors |

### Design Craft Guide

| Tool | Description |
|------|-------------|
| `get_design_craft_guide` | Professional design rules — typography, color, spacing, anti-AI-slop |

## Pro: Design System Integration

Connect your Figma design system libraries so the AI uses YOUR components, not generic ones.

- Scan & import library components
- Search across your design system
- Save & switch between libraries
- Generate designs with your DS

**[Get Figsor Pro →](https://asifkabirantu.gumroad.com/l/oxoopm)** — $9 one-time purchase (supports the original author)

## Requirements

- **Node.js** 18 or later
- **Figma** desktop or web app
- **Claude Code** with MCP support (and/or Cursor)

## Configuration

| Environment Variable | Default | Description |
|---------------------|---------|-------------|
| `FIGSOR_PORT` | `3055` | WebSocket server port |

## License

MIT © [Asif Kabir](https://github.com/AsifKabirAntu)

This fork is maintained by [@alanmomentum](https://github.com/alanmomentum).
