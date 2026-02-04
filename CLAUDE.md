# Draw.io MCP Server

This is the official draw.io MCP (Model Context Protocol) server that enables LLMs to open and create diagrams in the draw.io editor.

## Overview

This repository provides two ways to enable draw.io diagram generation in Claude:

1. **MCP Server** (`src/index.js`) - For Claude Desktop, requires installation
2. **Project Instructions** (`claude-project-instructions.txt`) - For Claude.ai and Claude Desktop, no installation required

Both approaches support:
- **XML diagrams**: Native draw.io/mxGraph XML format
- **CSV data**: Tabular data that draw.io converts to diagrams
- **Mermaid.js**: Text-based diagram definitions

## Project Instructions (No MCP)

The `claude-project-instructions.txt` file contains instructions that can be pasted into a Claude Project's custom instructions. Claude then uses its built-in Python analysis tool to generate compressed draw.io URLs directly.

**Advantages over MCP:**
- No installation required
- Works in Claude.ai (web) and Claude Desktop
- No system access needed
- User can inspect the URL before clicking

## MCP Server

## MCP Server Tools

### `open_drawio_xml`

Opens the draw.io editor with XML content.

**Parameters:**
- `content` (required): Draw.io XML content or URL to XML file
- `lightbox` (optional): Open in read-only lightbox mode (default: false)
- `dark` (optional): Dark mode - "true" or "false" (default: false)

**Example XML:**
```xml
<mxGraphModel>
  <root>
    <mxCell id="0"/>
    <mxCell id="1" parent="0"/>
    <mxCell id="2" value="Hello" style="rounded=1;" vertex="1" parent="1">
      <mxGeometry x="100" y="100" width="120" height="60" as="geometry"/>
    </mxCell>
  </root>
</mxGraphModel>
```

### `open_drawio_csv`

Opens the draw.io editor with CSV data that gets converted to a diagram.

**Parameters:**
- `content` (required): CSV content or URL to CSV file
- `lightbox` (optional): Open in read-only lightbox mode (default: false)
- `dark` (optional): Dark mode - "true" or "false" (default: false)

**Example CSV (Org Chart):**
```csv
## Example org chart
# label: %name%<br><i style="color:gray;">%title%</i>
# style: label;image=%image%;whiteSpace=wrap;html=1;rounded=1;
# parentstyle: swimlane;whiteSpace=wrap;html=1;childLayout=stackLayout;horizontal=1;
# namespace: csvimport-
# connect: {"from":"manager","to":"name","invert":true,"style":"curved=1;endArrow=blockThin;endFill=1;"}
# width: auto
# height: auto
# padding: 15
# ignore: id,image
# nodespacing: 40
# levelspacing: 100
# edgespacing: 40
# layout: auto
## CSV data starts below this line
name,title,manager,image
Evan Miller,CEO,,https://cdn-icons-png.flaticon.com/512/3135/3135715.png
Emily Brown,VP Sales,Evan Miller,https://cdn-icons-png.flaticon.com/512/3135/3135789.png
Daniel Lee,VP Engineering,Evan Miller,https://cdn-icons-png.flaticon.com/512/3135/3135715.png
```

### `open_drawio_mermaid`

Opens the draw.io editor with a Mermaid.js diagram definition.

**Parameters:**
- `content` (required): Mermaid.js syntax or URL to Mermaid file
- `lightbox` (optional): Open in read-only lightbox mode (default: false)
- `dark` (optional): Dark mode - "true" or "false" (default: false)

**Example Mermaid:**
```
graph TD
    A[Start] --> B{Is it working?}
    B -->|Yes| C[Great!]
    B -->|No| D[Debug]
    D --> B
```

**Supported Mermaid diagram types:**
- Flowcharts (`graph TD`, `graph LR`)
- Sequence diagrams (`sequenceDiagram`)
- Class diagrams (`classDiagram`)
- State diagrams (`stateDiagram-v2`)
- Entity Relationship diagrams (`erDiagram`)
- Gantt charts (`gantt`)
- Pie charts (`pie`)
- And more...

## Usage Patterns

### Creating a New Diagram

When an LLM needs to create a diagram, it should:

1. **For structured data** (org charts, tables): Use `open_drawio_csv`
2. **For flowcharts and sequences**: Use `open_drawio_mermaid`
3. **For complex or custom diagrams**: Use `open_drawio_xml`

### Viewing Existing Diagrams

Set `lightbox: true` to open diagrams in read-only view mode.

### From URLs

All tools accept URLs instead of direct content. The server will fetch the content automatically:

```
open_drawio_xml(content: "https://example.com/diagram.xml")
```

## Technical Details

### URL Generation

The server generates draw.io URLs using the `#create` hash parameter:
1. Content is encoded with `encodeURIComponent`
2. Compressed using pako deflateRaw
3. Encoded as base64
4. Wrapped in a JSON object with type and compression flags
5. Appended to the draw.io URL as `#create={...}`

### Content Types

| Tool | Type | Use Case |
|------|------|----------|
| `open_drawio_xml` | xml | Native draw.io format, full control |
| `open_drawio_csv` | csv | Tabular data, org charts, bulk import |
| `open_drawio_mermaid` | mermaid | Text-based diagrams, quick creation |

## Best Practices for LLMs

1. **Choose the right format**: Mermaid is easiest for simple flowcharts; CSV for data-driven diagrams; XML for precise control

2. **Keep it simple**: Start with Mermaid for most diagram needs

3. **Validate syntax**: Ensure Mermaid/CSV/XML syntax is correct before sending

4. **Use URLs for large content**: For very large diagrams, consider hosting the content and passing a URL

5. **Return the URL to users**: Always provide the generated URL so users can open the diagram in their browser
