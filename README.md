# BloggerEasy

[![Python 3.11+](https://img.shields.io/badge/python-3.11%2B-blue.svg)](https://www.python.org/downloads/)
[![Version](https://img.shields.io/badge/version-0.3.63-0E8A16.svg)](pyproject.toml)
[![Qt GUI](https://img.shields.io/badge/GUI-PySide6-41CD52.svg)](src/bloggereasy/gui/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**BloggerEasy** is a Python toolkit that converts **website URLs**, **local HTML files**, or **design mockup images** into **importable Blogger XML themes**. It parses page structure, extracts layout and styling, and produces validated XML that can be uploaded directly through Blogger's Theme → Backup/Restore flow.

```text
URL  ──┐
       ├──► parse / palette ──► Blogger XML theme ──► upload to Blogger
Image ─┘
```

---

## Table of contents

- [What is BloggerEasy](#what-is-bloggereasy)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
  - [Quick install with pip](#quick-install-with-pip)
  - [Install with uv](#install-with-uv)
  - [Editable install for development](#editable-install-for-development)
- [Usage](#usage)
  - [Command-line interface](#command-line-interface)
  - [Desktop GUI](#desktop-gui)
  - [Generating from a URL](#generating-from-a-url)
  - [Generating from an image](#generating-from-an-image)
  - [Generating from a local HTML file](#generating-from-a-local-html-file)
  - [Using built-in templates](#using-built-in-templates)
  - [Validating a theme](#validating-a-theme)
  - [Running the offline demo](#running-the-offline-demo)
- [Templates](#templates)
- [Importing into Blogger](#importing-into-blogger)
- [Generated output structure](#generated-output-structure)
- [Configuration](#configuration)
- [SDK usage](#sdk-usage)
- [Development](#development)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## What is BloggerEasy

BloggerEasy solves a specific problem: you see a website design you like, or you have a mockup image, and you want that look as a Blogger theme. Instead of hand-coding XML and CSS, BloggerEasy automates the conversion:

1. **Fetch** — download HTML from a public URL, read a local file, or open an image
2. **Parse** — extract semantic structure (header, nav, posts, sidebar, footer)
3. **Extract** — pull color palette, typography, and layout patterns
4. **Build** — generate Blogger XML with `<b:skin>`, layout sections, and widgets
5. **Validate** — check the XML is well-formed and Blogger-compatible

The result is a `.xml` file you upload to Blogger in under a minute.

---

## Features

| Capability | Description |
| --- | --- |
| **URL → theme** | Fetch a public page → structure → validated Blogger XML |
| **Image → theme** | Sample palette from a mockup/screenshot → theme skin |
| **HTML file** | Offline local HTML samples or your own page |
| **8 built-in templates** | `simple`, `portfolio`, `news`, `dark`, `magazine`, `docs`, `landing`, `from-image` |
| **Desktop GUI** | Modern PySide6 app with preview and one-click export |
| **Validation** | Check theme XML before upload |
| **Offline demo** | Batch-generate all bundled samples with one command |
| **SDK** | Python API for integration into other tools |

---

## Requirements

- **Python 3.11** or newer
- **pip** or **uv** package manager
- For GUI: a desktop environment (Qt widgets)
- For image input: Pillow (included in `[gui]` or `[vision]` extras)

---

## Installation

### Quick install with pip

```bash
pip install bloggereasy
```

Or install from a local clone:

```bash
git clone https://github.com/mergeos-bounties/BloggerEasy.git
cd BloggerEasy
pip install .
```

### Install with uv

[uv](https://docs.astral.sh/uv/) is the recommended package manager for this project (a `uv.lock` file is included):

```bash
# Clone and enter the project
git clone https://github.com/mergeos-bounties/BloggerEasy.git
cd BloggerEasy

# Sync all dependencies (creates .venv automatically)
uv sync

# Install with extras for full functionality
uv sync --extra gui --extra dev
```

Run commands through uv:

```bash
uv run bloggereasy version
uv run bloggereasy demo
```

### Editable install for development

```bash
# With pip
pip install -e ".[dev,gui]"

# With uv
uv sync --extra dev --extra gui
```

---

## Usage

### Command-line interface

All commands are accessed through the `bloggereasy` CLI:

```bash
bloggereasy --help          # Show all available commands
bloggereasy version         # Show version and available templates
bloggereasy samples         # List bundled HTML fixtures
bloggereasy stats           # Quick project inventory
```

### Desktop GUI

The PySide6 desktop app provides a visual workflow — paste a URL or drop an image, pick a template, and export:

```bash
bloggereasy-gui
# or equivalently:
bloggereasy gui
```

The GUI includes tabs for:

- **Create theme** — input via Website URL, Design image, or Local HTML
- **Output** — set path, validate, preview XML, open file/folder
- **Templates** — pick a preset style
- **Import to Blogger** — step-by-step restore instructions
- **Sample demo** — offline batch generation from bundled HTML files

### Generating from a URL

```bash
bloggereasy gen url -u "https://example.com/" -t portfolio -o data/out/from_url.xml
```

Options:

| Flag | Description | Default |
| --- | --- | --- |
| `-u`, `--url` | Public website URL to fetch | (required) |
| `-t`, `--template` | Template preset (`simple`, `portfolio`, `news`, …) | `simple` |
| `-o`, `--out` | Output file path | `data/out/<slug>.xml` |
| `--timeout` | Request timeout in seconds | `15` |

### Generating from an image

```bash
bloggereasy gen image -i path/to/mockup.png --title "My Blog" -o data/out/from_image.xml
```

Options:

| Flag | Description | Default |
| --- | --- | --- |
| `-i`, `--image` | Path to design image (PNG, JPG, …) | (required) |
| `--title` | Blog title in the generated theme | `"My Blog"` |
| `-o`, `--out` | Output file path | `data/out/from_image.xml` |

### Generating from a local HTML file

```bash
# From a bundled sample
bloggereasy gen html -i data/samples/html/portfolio.html -t portfolio

# From your own HTML file
bloggereasy gen html -i path/to/page.html -t dark -o data/out/custom.xml

# From a URL via the html subcommand
bloggereasy gen html --url "https://example.com/" --timeout 10 -t portfolio
```

### Using built-in templates

```bash
# Magazine card-grid layout
bloggereasy gen --template magazine --out data/out/magazine.xml

# SaaS landing page
bloggereasy gen --template landing --out data/out/landing.xml

# Documentation sidebar layout
bloggereasy gen --template docs --out data/out/docs.xml
```

Run `bloggereasy templates list` to see all available presets.

### Validating a theme

Always validate before uploading to Blogger:

```bash
bloggereasy validate -f data/out/magazine.xml
```

The validator checks XML well-formedness, required Blogger sections, and `<b:skin>` presence.

### Running the offline demo

Generate themes for all bundled HTML samples:

```bash
bloggereasy demo
```

Outputs are written to `data/out/demo/`.

---

## Templates

| Template | Best for |
| --- | --- |
| `simple` | Clean default blog |
| `portfolio` | Portfolio / personal brand |
| `news` | Dense editorial |
| `magazine` | Magazine-style card grid with left rail, main posts, and right sidebar |
| `docs` | Documentation-style sidebar + article layout |
| `landing` | Single-column hero, CTA, and feature cards |
| `dark` | Dark developer look |
| `from-image` | Image palette (auto for image mode) |

Bundled HTML samples are in `data/samples/html/`.

---

## Importing into Blogger

1. Open [Blogger](https://www.blogger.com/) → select your blog → **Theme**
2. Click **⋮** (three dots) → **Backup** to save your current theme
3. Click **Restore** → **Upload** and select the generated `.xml` file
4. Confirm — layout, CSS, and widgets apply immediately
5. Use Theme → Customize for fine-tuning if needed

Always back up your current theme before restoring a new one.

---

## Generated output structure

```
data/out/
├── from_url.xml          # Theme generated from a website
├── from_image.xml       # Theme generated from a design image
├── custom.xml            # Theme from local HTML
└── demo/                 # Offline batch outputs
    ├── portfolio.xml
    ├── news_portal.xml
    └── ...
```

---

## Configuration

BloggerEasy uses sensible defaults but can be adjusted via environment variables:

| Variable | Description | Default |
| --- | --- | --- |
| `BLOGGEREASY_OUT_DIR` | Base output directory | `data/out` |
| `BLOGGEREASY_TIMEOUT` | URL fetch timeout (seconds) | `15` |

---

## SDK usage

BloggerEasy can be used as a Python library:

```python
from bloggereasy.integrations.sdk import generate_from_url, generate_from_image

# From a URL
xml_path = generate_from_url("https://example.com/", template="portfolio")
print(f"Theme saved to: {xml_path}")

# From an image
xml_path = generate_from_image("mockup.png", blog_title="My Design")
```

---

## Development

```bash
# Run tests
uv run pytest -q

# Lint
uv run ruff check src tests

# Run demo
uv run bloggereasy demo

# Refresh GUI screenshots
uv run python scripts/capture_gui_shots.py
```

---

## Troubleshooting

| Problem | Solution |
| --- | --- |
| `ModuleNotFoundError: No module named 'bloggereasy'` | Run `pip install -e .` or `uv sync` from the project root |
| URL fetch fails / timeout | Check internet connectivity; try increasing `--timeout` |
| GUI does not launch | Ensure PySide6 is installed (`pip install -e ".[gui]"`) |
| Image palette extraction is off | Use a higher-contrast mockup; ensure the image is PNG or JPG |
| Validation error: missing `b:skin` | Use a built-in template; custom HTML may lack required structure |
| Blogger upload rejects the XML | Re-run `bloggereasy validate -f <file>` and fix reported issues |

---

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## License

MIT · MergeOS / ThanhTrucSolutions