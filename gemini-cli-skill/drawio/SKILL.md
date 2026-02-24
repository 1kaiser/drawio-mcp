---
name: drawio
description: Generate draw.io diagrams as .drawio files, optionally export to PNG/SVG/PDF with embedded XML. Use when asked to create flowcharts, architecture diagrams, ER diagrams, or any visual schema.
---

# Draw.io Diagram Skill for Gemini CLI

Generate draw.io diagrams as native `.drawio` files. Optionally export to PNG, SVG, or PDF with the diagram XML embedded (so the exported file remains editable in draw.io).

## Workflow

1. **Generate draw.io XML** in mxGraphModel format for the requested diagram.
2. **Write the XML** to a `.drawio` file in the current working directory.
3. **If the user requested an export format** (png, svg, pdf), export using the `drawio` CLI.
   - Use `xvfb-run -a drawio --no-sandbox -x -f <format> -e -b 10 -o <output> <input.drawio>` for headless environments.
4. **Delete the source `.drawio` file** if an export was successful.
5. **Open the result** using `xdg-open` (on Linux).

## Choosing the Output Format

- `png`: Viewable everywhere, editable in draw.io (uses `-e` to embed XML).
- `svg`: Scalable, editable in draw.io (uses `-e` to embed XML).
- `pdf`: Printable, editable in draw.io (uses `-e` to embed XML).
- `drawio`: Native XML format (default if no format specified).

## draw.io CLI Usage (Headless Linux)

Export command:
```bash
xvfb-run -a drawio --no-sandbox -x -f <format> -e -b 10 -o <output> <input.drawio>
```

Key flags:
- `xvfb-run -a`: Run headlessly.
- `--no-sandbox`: Required for root/containerized environments.
- `-x` / `--export`: Export mode.
- `-f` / `--format`: Output format (png, svg, pdf, jpg).
- `-e` / `--embed-diagram`: Embed diagram XML in the output (PNG, SVG, PDF only).
- `-o` / `--output`: Output file path.
- `-b` / `--border`: Border width around diagram (default: 0).

## XML Format (mxGraphModel)

### Basic Structure
```xml
<mxGraphModel>
  <root>
    <mxCell id="0"/>
    <mxCell id="1" parent="0"/>
    <!-- Diagram cells go here with parent="1" -->
  </root>
</mxGraphModel>
```

### Common Styles
- **Rounded rectangle**: `style="rounded=1;whiteSpace=wrap;"`
- **Diamond (decision)**: `style="rhombus;whiteSpace=wrap;"`
- **Arrow (edge)**: `style="edgeStyle=orthogonalEdgeStyle;"`
- **Labeled arrow**: Add `value="Label"` to the edge `mxCell`.

### Style Properties
- `fillColor=#dae8fc`, `strokeColor=#6c8ebf`, `fontColor=#333333`
- `shape=cylinder3` (Database)
- `shape=mxgraph.flowchart.document` (Document)
- `ellipse` (Circles), `rhombus` (Diamonds), `dashed=1` (Dashed lines)

## Critical Constraints
- **NEVER use double hyphens (`--`) inside XML comments.**
- Escape special characters in attribute values: `&amp;`, `&lt;`, `&gt;`, `&quot;`
- Always use unique `id` values for each `mxCell`.
- Use descriptive, hyphen-cased filenames (e.g., `system-architecture.drawio.png`).
