# MCP Test Utils

[![version: 3.27.23](https://img.shields.io/badge/version-3.27.23-blue.svg)](https://github.com/JeenyJAI/mcp-test-utils/releases) [![tools: 31](https://img.shields.io/badge/tools-31-green.svg)](#tools-31)

MCP server for automated desktop UI testing. A single binary — no runtime, no dependencies, no installer.

> **Windows x64 only.** There are no macOS or Linux builds yet. If you need one, write to
> [JeenyJAI@gmail.com](mailto:JeenyJAI@gmail.com): requests decide what comes next.

Gives AI agents eyes and hands: screenshots, window management, mouse, keyboard, UI Automation, OCR, file search.
Replays saved scenarios as regression runs — after every build, without an agent. Records what you do into a draft
scenario, checks accessibility, watches the resources of the application under test.

## Why

AI agents can trigger actions in applications but can't see the screen. This server bridges that gap:

```
Agent triggers action → takes screenshot → sees the result →
switches window → clicks a button → verifies → writes report
```

Fully autonomous, no user involvement required.

## Demo

<table>
  <tr>
    <td align="center" width="50%">
      <a href="https://youtu.be/e05meGVe9Kg">
        <img src="https://img.youtube.com/vi/e05meGVe9Kg/maxresdefault.jpg" alt="MCP Test Utils 3.26 — a saved scenario replays 232 steps in 66 s" width="100%">
      </a>
      <br><b>3.26 — Scenario run</b>
      <br>Claude replays a saved test: 232 steps, one call, 66 s
    </td>
    <td align="center" width="50%">
      <a href="https://www.youtube.com/watch?v=kiICWA5zQTk">
        <img src="https://img.youtube.com/vi/kiICWA5zQTk/maxresdefault.jpg" alt="MCP Test Utils — installation, setup and the basic tools" width="100%">
      </a>
      <br><b>Installation and setup</b>
      <br>Setting up the server and the basic tools
    </td>
  </tr>
</table>

## Setup

1. Download `mcp-test-utils.exe` from [Releases](https://github.com/JeenyJAI/mcp-test-utils/releases) (the `.sig`
   file next to it is for the built-in updater — you do not need it).
2. Add it to your MCP client:

**Claude Desktop:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "test-utils": {
      "command": "D:\\path\\to\\mcp-test-utils.exe"
    }
  }
}
```

**Claude Code:**

```
claude mcp add test-utils -- "D:\path\to\mcp-test-utils.exe"
```

For other clients, see their documentation.

3. Restart the client.
4. In chat, try: _"Take a screenshot"_ — the agent will return an image of your desktop.

Optional settings (logging, file search, starting programs) go into `env` — see
[Environment variables](#environment-variables):

```json
{
  "mcpServers": {
    "test-utils": {
      "command": "D:\\path\\to\\mcp-test-utils.exe",
      "env": {
        "MCP_LOG_DIR": "D:\\path\\to\\logs",
        "MCP_SEARCH_DIRS": "D:\\Projects\\app1;D:\\Projects\\app2",
        "MCP_LAUNCH_APPS": "C:\\Program Files\\MyApp\\myapp.exe"
      }
    }
  }
}
```

## Tools (31)

Four of them appear only when configured: `search_in_files` and `find_files` with `MCP_SEARCH_DIRS`,
`enable_logging` and `disable_logging` with `MCP_LOG_DIR` — by default the server lists 27 tools.

### Vision

| Tool | Description |
|---|---|
| `take_screenshot` | Screenshot of the entire desktop; quality presets from full to minimal (fewer tokens) |
| `take_window_screenshot` | Screenshot of a specific window |
| `read_screen_text` | OCR the entire screen (Windows OCR) |
| `read_region_text` | OCR a screen region with precise word coordinates |

### Window management

| Tool | Description |
|---|---|
| `list_windows` | List windows with id, title, app, process id, position, size, state |
| `focus_window` | Bring a window to the front and restore it if minimized; optionally set its position and size |
| `launch_app` | Start a program from the allowlist of the server config (`MCP_LAUNCH_APPS`) and wait for its window |
| `close_window` | Close a window as its X button would and wait until it is gone; reports a "Save changes?" prompt |

### Input

| Tool | Description |
|---|---|
| `mouse_click` | Click an element by id or at coordinates (left / right / middle, double); can wait for the expected effect and retry |
| `mouse_move` | Move the cursor to a point or to a UI element |
| `mouse_drag` | Drag between elements or points at a human pace (HTML5 drag-and-drop works) |
| `mouse_scroll` | Scroll the mouse wheel over an element and wait until the scrolling stops |
| `keyboard_type` | Type text (full Unicode — Latin, Cyrillic, CJK, emoji) and check that the field shows what was typed |
| `keyboard_press` | Press a key (Enter, Tab, F1–F12, arrows, etc.), optionally several times |
| `keyboard_shortcut` | Press a key combination (Ctrl+S, Alt+F4, Ctrl+Shift+P, etc.) |

### Structured UI access

| Tool | Description |
|---|---|
| `list_ui_elements` | UI Automation tree — buttons, fields, menus with ids, values, states and exact coordinates; filters by name, value, class |
| `get_focused_element` | Which element has keyboard focus now, and in which window |
| `read_text` | Full text of a document or field through UI Automation (up to 100 000 characters per call), no OCR |
| `ui_action` | Act on an element without mouse or keyboard: invoke, toggle, expand, select an option, set a value, scroll into view |
| `check_accessibility` | Accessibility defects: unnamed controls, controls the Tab key cannot reach, duplicate ids, zero-size controls |

### Process

| Tool | Description |
|---|---|
| `process_info` | Memory, handles, GDI objects, threads and CPU of the app under test — call it repeatedly to spot leaks |

### Waiting, batching and scenarios

| Tool | Description |
|---|---|
| `wait_for` | Wait for a window, an element or its state instead of sleeping |
| `batch` | Run up to 20 tool calls in one request, with `find` and `assert` steps |
| `run_scenario` | Replay a saved scenario, a suite or data rows and get a pass/fail report (see [Regression runs](#regression-runs-without-an-agent)) |
| `record_scenario` | Record what you do in one window into a draft scenario (see [Recording](#recording)) |

### File search

| Tool | Description |
|---|---|
| `search_in_files` | Search text or regex in files within allowed directories (like VS Code Find in Files) |
| `find_files` | Find files and directories by name pattern (glob), like "Go to File" |

### Agent guide

| Tool | Description |
|---|---|
| `get_usage_guide` | Usage guide for agents: core rules + sections on request |
| `prepare_feedback` | Prepare a suggestions file for the author (personal data masked; you review it and send it by email — nothing is sent automatically) |

### Session logging

| Tool | Description |
|---|---|
| `enable_logging` | Start recording tool calls to JSONL + screenshots (opt-in) |
| `disable_logging` | Stop recording, get session stats |

## Regression runs without an agent

An agent finds a path through your application once and saves it as a **scenario** — a JSON file. After that the
scenario replays without an agent: fast, the same way every time, no tokens spent.

```
mcp-test-utils --check order-form.json     # validate the file, touch nothing
mcp-test-utils --run order-form.json       # run: report in the console and in order-form.report.json
```

- Exit codes: `0` — passed, `1` — a step failed, `2` — did not run. Ready for a CI job after each build.
- On a failure you get a screenshot, the element tree and a hint: either the scenario is stale or the application has changed.
- An agent runs a scenario with `run_scenario(path)` and learns the format from `get_usage_guide(section: "scenarios")`.
- **Suites and data rows:** `--suite` runs several scenario files in order with one summary; `--data rows.csv` runs
  a scenario once per row (columns become variables); `--junit report.xml` adds JUnit XML for CI.
  ```
  mcp-test-utils --suite regress.suite.json --junit regress.xml
  ```
- Without a license key a scenario runs up to 50 steps (see [License key](#license-key)).

## Recording

Do the steps yourself and get a draft scenario instead of describing them to an agent:

```
mcp-test-utils --record order-form.json --app myapp
```

Only that window and its dialogs are recorded, with a notice on screen; **Ctrl+Alt+Shift+R** (or Enter in the
console) stops the recording. Password fields are never read; text typed into a terminal is recorded only with `--keys-text`. An
agent can start the same recording with `record_scenario`.

## Environment variables

| Variable | Description | Default |
|---|---|---|
| `MCP_LOG_DIR` | Path for log sessions. Without it, logging tools are hidden | — |
| `MCP_LOG_MAX_MB` | Session size limit (warning when exceeded) | `500` |
| `MCP_LOG_RETAIN_DAYS` | Auto-delete sessions older than N days. `0` to disable | `30` |
| `MCP_SEARCH_DIRS` | Allowed directories for `search_in_files` and `find_files` (separated by `;`). Without it, both are hidden | — |
| `MCP_LAUNCH_APPS` | Programs (`.exe` files or folders, separated by `;`) that `launch_app` may start. Shells, script hosts and interpreters are refused even when listed. Without it, `launch_app` starts nothing | — |
| `MCP_NOTIFY` | `off` hides the tray icon and the live call log window | — |
| `MCP_INPUT_LOCK` | `off` disables the keyboard and mouse lock while the server sends input | — |
| `MCP_INPUT_LOCK_STALL_MS` | Server silence (ms) after which the lock releases your input | `60000` |

## Tray icon, updates and input lock

- A tray icon shows a live log of tool calls, one tab per session, errors in red; `MCP_NOTIFY=off` hides it.
- **Updates:** tray menu → **Check for updates…**. Only a release signed by the author is installed; afterwards restart
  your MCP client. The server goes online only when you click.
- **Input lock:** while the server types or clicks, your keyboard and mouse buttons are paused (the pointer still
  moves). Press **Esc** to stop the action and get your input back. `MCP_INPUT_LOCK=off` turns the lock off.

## Feedback

If an agent hits a gap in the tools, it offers to prepare a short suggestions file (`prepare_feedback`). The file
goes to `Documents\mcp-test-utils feedback\`, with personal data masked and no screenshots; review it and email it
to [JeenyJAI@gmail.com](mailto:JeenyJAI@gmail.com). Nothing is sent automatically.

## Use cases

- **Automated QA** — the agent navigates the app, clicks through flows, takes screenshots at each step, writes a test report
- **Regression runs** — replay saved scenarios after each build, from a script or a CI job, without an agent
- **Recorded tests** — walk through a path yourself once, get a draft scenario, let the agent turn it into a test
- **Desktop automation** — fill forms, copy data between windows, run workflows
- **Accessibility audit** — `check_accessibility` finds unnamed controls, controls the Tab key cannot reach, duplicate ids
- **Resource leaks** — `process_info` shows handles and GDI objects growing across repeated calls
- **Visual checks** — the agent compares screenshots between builds
- **Data extraction** — read document text through UI Automation, or OCR text from applications that don't expose it
- **Code search** — find patterns across multiple projects without leaving the agent session

## Security

- Responds only to requests from the MCP client; opens no network ports
- Writes files only when you ask: logs, scenario reports and JUnit files, recorded drafts, sealed scenarios, the
  feedback file, the license file — plus small settings files in `%LOCALAPPDATA%\mcp-test-utils\` and an update you
  install
- Sends no data externally; goes online only when you click Check for updates (and Download and install) in the
  tray window — an agent's tool call never does, and only a file signed by the author is installed
- **Holds your keyboard and mouse buttons only during a call, `batch` or scenario run that sends input** — Esc
  releases them at once
- **Starts only the programs you list** in `MCP_LAUNCH_APPS` (never shells or interpreters); a forced
  `close_window` terminates only a program it started
- **Records only on an explicit command**, with a notice on screen the whole time; password fields are never read
- **Screenshots capture the entire screen** — make sure no sensitive information is visible
- **File search is sandboxed** — only directories in `MCP_SEARCH_DIRS` are accessible

## How it works

MCP Test Utils is an MCP server over stdin/stdout: your MCP client starts the exe and calls its tools. It uses
native Windows APIs (UI Automation, Win32 input, Windows OCR) — no PowerShell, no helper tools, no network access
except the update check you start. Tested with Claude Desktop and Claude Code.

## License key

All tools are free. A license key only lifts the 50-step limit of scenario runs.

For now, keys are issued through **early access**: the first 30 users get a free one-year key in exchange for short
feedback after the first month — write to [JeenyJAI@gmail.com](mailto:JeenyJAI@gmail.com) with your email and the
**machine code** of your computer. A key is bound to that computer. Your agent sees the code in the second line of
`get_usage_guide`, or print it yourself:

```
mcp-test-utils --machine-id
```

Register the key once on that computer:

```
mcp-test-utils --set-license <email> <key>
```

Reinstalling Windows or replacing the motherboard alone keeps the key valid; a new computer needs a new key — just write again.

**A sealed scenario runs anywhere without the limit — that file only, exactly as sealed.** On the licensed computer
`mcp-test-utils --seal order-form.json` writes a seal into the file; a CI agent or a colleague without a key then
runs it in full. Any change to its steps (or to the files it includes) breaks the seal — seal it again on the
licensed computer.

## Support us

If you find it useful — [jeenyjai.github.io](https://jeenyjai.github.io/)

## License

Copyright 2026 JeenyJAI. All rights reserved.

<!-- mcp-name: io.github.JeenyJAI/mcp-test-utils -->

---

🚀 Created with [Claude](https://claude.ai)
