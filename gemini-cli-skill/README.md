# Gemini CLI Skill for Draw.io

This folder contains a native **Gemini CLI skill** that enables the agent to create and export draw.io diagrams directly from the command line.

## Prerequisites

- **Gemini CLI** installed.
- **draw.io Desktop** installed (for CLI export functionality).
- **xvfb** installed (for headless Linux environments).

## Installation

1.  **Clone this repository** (if you haven't already).
2.  **Package the skill**:
    ```bash
    # You'll need the gemini-cli internal packaging script
    # Alternatively, you can just point to the directory
    ```
    Actually, the easiest way for a user is to install the packaged `.skill` file. I'll provide instructions for that.

3.  **Install via Gemini CLI**:
    ```bash
    gemini skills install ./gemini-cli-skill/drawio --scope user
    ```

4.  **Reload Skills**:
    In your active Gemini session, run:
    ```bash
    /skills reload
    ```

## Usage

You can now ask Gemini to create diagrams:

- "Create a flowchart for our deployment process and export it as a PNG."
- "Generate an architecture diagram for a microservices system in SVG format."
- "Create a database schema (ER diagram) as a .drawio file."

## Technical Details

The skill uses the `drawio` desktop CLI with the `--embed-diagram` flag. This means exported PNG, SVG, and PDF files contain the original vector XML data, making them **fully editable** if you upload them back to [app.diagrams.net](https://app.diagrams.net).
