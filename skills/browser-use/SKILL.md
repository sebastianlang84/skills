---
name: browser-use
description: Use when automating browser tasks via CLI — navigating URLs, clicking elements, extracting data, taking screenshots, or scraping pages using the browser-use tool
---

# browser-use CLI

## Overview

`browser-use` controls a persistent headless Chromium daemon via CLI. Commands are stateful within a session — navigate once, then interact. The daemon keeps the browser alive between CLI calls.

**Install check:** `browser-use doctor`

## Global Flags

| Flag | Effect |
|------|--------|
| `--headed` | Show browser window (debugging) |
| `--session <id>` | Use named session (multiple parallel browsers) |
| `--json` | Machine-readable JSON output |
| `--mcp` | Run as MCP server |

## Core Workflow

```bash
# 1. Navigate
browser-use open https://example.com

# 2. Inspect state (gets URL, title, interactive elements with indices)
browser-use state

# 3. Interact by element index (from state output) or coordinates
browser-use click 3
browser-use click 450 200       # x y coordinates
browser-use type "search query"
browser-use input "search query"  # type into specific focused element

# 4. Wait for dynamic content
browser-use wait selector "#results"
browser-use wait text "Loading complete"

# 5. Extract data
browser-use get text "#main"        # CSS selector → text
browser-use get html                # full page HTML
browser-use get title
browser-use extract "all product names and prices"  # LLM-based semantic extraction

# 6. Screenshot
browser-use screenshot /tmp/page.png
browser-use screenshot --full /tmp/full.png   # full page
```

## Quick Reference

| Command | Usage |
|---------|-------|
| `open <url>` | Navigate |
| `back` | Browser back |
| `state` | Get page state + element indices |
| `click <idx\|x y>` | Click by index or coords |
| `type <text>` | Type (focused element) |
| `input <text>` | Type into specific element |
| `hover <idx>` | Hover |
| `dblclick <idx>` | Double-click |
| `scroll <direction>` | Scroll page |
| `keys <key>` | Send keyboard keys (e.g. `Enter`, `Tab`) |
| `select <option>` | Select dropdown option |
| `wait selector <css>` | Wait for CSS selector |
| `wait text <text>` | Wait for text to appear |
| `get title\|html\|text\|value\|attributes\|bbox` | Get page/element data |
| `extract "<query>"` | LLM-based semantic extraction |
| `eval "<js>"` | Execute JavaScript |
| `screenshot [--full] [path]` | Screenshot (base64 if no path) |
| `cookies` | Cookie operations |
| `switch <idx>` | Switch tab |
| `close-tab` | Close current tab |
| `sessions` | List active sessions |
| `close` | Close browser daemon |
| `upload <path>` | Upload file to file input |

## Pattern: Reconnaissance → Action

Always call `state` before clicking — it returns numbered interactive elements:

```bash
browser-use open https://example.com/login
browser-use state
# Output: [1] input#username, [2] input#password, [3] button "Login"
browser-use click 1
browser-use type "myuser"
browser-use click 2
browser-use type "mypass"
browser-use click 3
browser-use wait text "Dashboard"
```

## `extract` vs `get`

- `get text <selector>` — precise, CSS selector required, raw text
- `extract "<natural language query>"` — LLM reads the page and answers semantically; no selector needed; slower but handles complex/dynamic layouts

```bash
# When you know the structure:
browser-use get text ".product-price"

# When structure is unclear or dynamic:
browser-use extract "all product names with their prices"
```

## Common Mistakes

- **Interacting before page loads:** Add `browser-use wait selector <key-element>` after navigation
- **Wrong element index:** Indices change after DOM updates — re-run `state` after any interaction that modifies the page
- **Daemon not running:** First `open` starts the daemon automatically; `close` shuts it down

## Setup

```bash
browser-use doctor          # check installation
browser-use install         # install Chromium (requires sudo)
browser-use --headed open https://example.com  # debug with visible browser
```
