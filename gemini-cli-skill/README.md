# Draw.io Skill for Gemini CLI

A Gemini CLI skill that generates native `.drawio` files, with optional export to PNG, SVG, or PDF (with embedded XML so the exported file remains editable in draw.io). No MCP setup required.

## How It Works

When you ask the Gemini CLI agent to create a diagram, it will:

1. **Generate draw.io XML** directly in `mxGraphModel` format.
2. **Write the XML** to a `.drawio` file in your current working directory.
3. **Handle Exports**: If you requested an image format, it uses the `drawio` desktop CLI to export the file.
4. **Clean Up**: If exported, it deletes the intermediate `.drawio` file (the XML is embedded in the PNG/SVG/PDF).

## Prerequisites

- **[Gemini CLI](https://github.com/google/gemini-cli)** installed.
- **[draw.io Desktop](https://github.com/jgraph/drawio-desktop/releases)** installed (required for CLI export functionality).
- **Headless Support**: For Linux environments without a display (like remote servers or Docker), ensure `xvfb` is installed.

## Installation

You can install the skill directly from your local clone of this repository:

```bash
# Navigate to the drawio-mcp repo
gemini skills install ./gemini-cli-skill/drawio --scope user
```

After installation, notify your active Gemini CLI session to load the new skill:

```bash
/skills reload
```

## Usage

```text
"Create a flowchart for our deployment process."
```

By default, this writes a `.drawio` file. To export to an image format, simply mention the format in your request:

```text
"Create a png flowchart for the user login flow." → login-flow.drawio.png
"Generate an svg ER diagram for the database."   → db-schema.drawio.svg
"Export a pdf of the system architecture."       → architecture.drawio.pdf
```

## Export Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| (default) | `.drawio` | Native XML, editable in draw.io, no desktop CLI needed. |
| `png` | `.drawio.png` | Viewable everywhere, embedded XML, editable in draw.io. |
| `svg` | `.drawio.svg` | Scalable vector, embedded XML, editable in draw.io. |
| `pdf` | `.drawio.pdf` | Printable document, embedded XML, editable in draw.io. |

The `.drawio.*` double extension signals that the file contains embedded diagram XML. You can upload these files back to [app.diagrams.net](https://app.diagrams.net) to resume editing.

## Technical Details

For Linux users, the skill automatically detects if it needs to run headlessly. It uses the following command pattern to ensure compatibility with containerized or root environments:

```bash
xvfb-run -a drawio --no-sandbox -x -f png -e -b 10 -o output.png input.drawio
```

## Other Variants

This repository offers multiple ways to integrate draw.io with AI assistants:

- **[MCP App Server](../mcp-app-server/README.md)** — Inline diagrams in chat (Claude.ai, VS Code)
- **[MCP Tool Server](../mcp-tool-server/README.md)** — Opens diagrams in browser via MCP (Claude Desktop)
- **[Skill + CLI (Claude)](../skill-cli/README.md)** — Native skill for Claude Code
- **[Project Instructions](../project-instructions/README.md)** — Claude.ai Projects, no install needed
