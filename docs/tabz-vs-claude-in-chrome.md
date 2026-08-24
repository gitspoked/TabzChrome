# TabzChrome MCP Tools vs Claude in Chrome

A side-by-side comparison of TabzChrome's 85 `tabz_*` MCP tools against Anthropic's
first-party **Claude in Chrome** (CiC) browser extension (~22 `mcp__claude-in-chrome__*`
tools). Snapshot as of 2026-08-24 (TabzChrome v1.6.2), compared against the live CiC
toolset in a connected Claude Code session.

**TL;DR:** Of TabzChrome's 85 tools, roughly **20 overlap** with Claude in Chrome,
**~65 have no CiC equivalent**. CiC wins on *hands-on page interaction* (real
mouse/keyboard, vision-based clicking, GIF recording). TabzChrome wins everywhere
else: terminals, TTS, bookmarks, history, cookies, tab groups, windows/multi-monitor,
device emulation, notifications, downloads, and performance profiling.

---

## Claude in Chrome toolset (for reference)

`tabs_context_mcp`, `tabs_create_mcp`, `tabs_close_mcp`, `navigate`, `computer`
(mouse/keyboard/screenshot), `read_page`, `get_page_text`, `find` (natural-language
element lookup), `form_input`, `javascript_tool`, `read_console_messages`,
`read_network_requests`, `gif_creator`, `file_upload`, `upload_image`,
`resize_window`, `shortcuts_list`, `shortcuts_execute`, `browser_batch`,
`list_connected_browsers`, `select_browser`, `switch_browser`.

---

## Category-by-category

### Page interaction — **CiC wins**

| TabzChrome | Claude in Chrome | Notes |
|---|---|---|
| `tabz_click` | `computer`, `find` | CiC clicks via real input events + vision; works on canvas/shadow-DOM where selectors fail |
| `tabz_fill` | `form_input`, `computer` | CiC handles selects, checkboxes, date pickers, contenteditable |
| — | `find` | Natural-language element lookup — no Tabz equivalent |
| — | `file_upload` | Upload local files into page file inputs |
| — | `shortcuts_list` / `shortcuts_execute` | Trigger site keyboard shortcuts |
| — | `browser_batch` | Batch multiple actions in one call |

### Reading pages — split

| TabzChrome | Claude in Chrome | Notes |
|---|---|---|
| `tabz_get_page_info` | `tabs_context_mcp` | Tie |
| `tabz_get_dom_tree` | `read_page` | CiC returns an accessibility tree (better for "understand the page"); Tabz returns real DOM via chrome.debugger (better for exact structure) |
| `tabz_get_element` | — | Selector-precise HTML/computed styles/bounds for CSS debugging — no CiC equivalent |
| — | `get_page_text` | Clean text extraction |

### Screenshots & visual — split

| TabzChrome | Claude in Chrome | Notes |
|---|---|---|
| `tabz_screenshot` | `computer` (screenshot action) | Tie for viewport |
| `tabz_screenshot_full` | — | Full scrollable-page capture in one image |
| — | `gif_creator` | Multi-step interaction recordings — unique to CiC |
| `tabz_download_image` | `upload_image` (different direction) | Tabz saves page images to disk; CiC pushes local images into pages |

### Navigation & tabs — split

| TabzChrome | Claude in Chrome | Notes |
|---|---|---|
| `tabz_open_url` | `navigate`, `tabs_create_mcp` | Tabz allowlists domains (GitHub/GitLab/Vercel/localhost); CiC is general-purpose with per-site permissions |
| `tabz_list_tabs` | `tabs_context_mcp` | Tabz returns richer metadata |
| `tabz_switch_tab` | `tabs_context_mcp` (implicit) | Tie |
| `tabz_rename_tab` | — | Custom tab labels (used for worker context %) |
| — | `tabs_close_mcp` | Tabz has no tab-close tool |
| — | `list/select/switch_browser` | CiC can drive multiple browsers |

### Debugging — mostly tie, Tabz deeper

| TabzChrome | Claude in Chrome | Notes |
|---|---|---|
| `tabz_get_console_logs` | `read_console_messages` | Tie (both filter) |
| `tabz_enable_network_capture`, `tabz_get_network_requests`, `tabz_clear_network_requests` | `read_network_requests` | Tabz has an explicit capture lifecycle + clearing |
| `tabz_execute_script` | `javascript_tool` | Tie (both CSP-limited) |
| `tabz_profile_performance` | — | Timing/memory/DOM metrics |
| `tabz_get_coverage` | — | Unused JS/CSS analysis |

### Windows & displays (7 tools) — **Tabz only**

`tabz_list_windows`, `tabz_create_window`, `tabz_update_window`, `tabz_close_window`,
`tabz_get_displays`, `tabz_tile_windows`, `tabz_popout_terminal`

CiC has only `resize_window`. Multi-monitor layouts, tiling, and popout terminals
have no CiC equivalent.

### Tab groups (8 tools) — **Tabz only**

`tabz_list_groups`, `tabz_create_group`, `tabz_update_group`, `tabz_add_to_group`,
`tabz_ungroup_tabs`, `tabz_claude_group_add`, `tabz_claude_group_remove`,
`tabz_claude_group_status`

### Bookmarks (6 tools) — **Tabz only**

`tabz_get_bookmark_tree`, `tabz_search_bookmarks`, `tabz_save_bookmark`,
`tabz_create_folder`, `tabz_move_bookmark`, `tabz_delete_bookmark`

### History (5 tools) — **Tabz only**

`tabz_history_search`, `tabz_history_recent`, `tabz_history_visits`,
`tabz_history_delete_url`, `tabz_history_delete_range`

### Sessions (3 tools) — **Tabz only**

`tabz_sessions_recently_closed`, `tabz_sessions_restore`, `tabz_sessions_devices`

### Cookies (5 tools) — **Tabz only**

`tabz_cookies_get`, `tabz_cookies_set`, `tabz_cookies_list`, `tabz_cookies_delete`,
`tabz_cookies_audit`

### Device emulation (6 tools) — **Tabz only**

`tabz_emulate_device`, `tabz_emulate_network`, `tabz_emulate_geolocation`,
`tabz_emulate_media`, `tabz_emulate_vision` (colorblind simulation), `tabz_emulate_clear`

CiC's `resize_window` covers only the viewport-size slice of responsive testing.

### Downloads & page saving (5 tools) — **Tabz only**

`tabz_download_file`, `tabz_get_downloads`, `tabz_cancel_download`,
`tabz_download_image`, `tabz_save_page` (MHTML archive)

### Notifications (4 tools) — **Tabz only**

`tabz_notification_show`, `tabz_notification_update` (progress bars),
`tabz_notification_clear`, `tabz_notification_list`

### Audio / TTS (3 tools) — **Tabz only**

`tabz_speak`, `tabz_list_voices`, `tabz_play_audio`

### Terminals (3 tools) — **Tabz only, the core differentiator**

`tabz_list_terminals`, `tabz_send_keys`, `tabz_capture_terminal`

CiC cannot see or touch the Chrome side panel at all.

### Terminal profiles (7 tools) — **Tabz only**

`tabz_list_profiles`, `tabz_list_categories`, `tabz_get_profile`, `tabz_spawn_profile`,
`tabz_create_profile`, `tabz_update_profile`, `tabz_delete_profile`

### Plugins & docs (6 tools) — **Tabz only**

`tabz_list_plugins`, `tabz_list_skills`, `tabz_get_skill`, `tabz_plugins_health`,
`tabz_toggle_plugin`, `tabz_docs`

---

## Scorecard

| | TabzChrome | Claude in Chrome |
|---|---|---|
| Total tools | 85 | ~22 |
| Unique capability areas | Terminals, TTS, bookmarks, history, cookies, sessions, tab groups, windows/multi-monitor, emulation, notifications, downloads, perf/coverage, plugins | Vision-based interaction, natural-language element finding, GIF recording, file upload, keyboard shortcuts, multi-browser |
| Overlapping tools | ~20 (interaction, reading, screenshots, console/network, JS, tabs) | |
| Works headless (no user watching) | Yes — backend + WebSocket, usable by spawned workers/cron | Requires connected Chrome session + per-site permission grants |

## Practical guidance

- **Use CiC when** you need to *act on an arbitrary page like a human*: click through
  flows, fill complex forms, visually verify UI, record a GIF of a repro.
- **Use TabzChrome when** you need *anything Chrome-API-shaped*: terminals and worker
  orchestration, TTS notifications, bookmarks/history/cookies, tab groups and window
  layouts, device emulation, performance profiling — or when running headless
  (workers, cron) where CiC isn't connected.
- **The ~20 overlapping tools are not dead weight**: they make TabzChrome
  self-sufficient in sessions without CiC. In sessions with both, prefer CiC for
  interaction and Tabz for inspection.
- A trimmed profile (e.g. the 29-tool preset) that drops most interaction/screenshot
  overlap and keeps the unique categories is the sweet spot when CiC is also connected.
