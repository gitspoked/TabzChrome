# Chrome Platform Updates — July 2026 Review

Research on what shipped in Chrome (roughly Chrome 140 → 150, Sept 2025 – June 2026)
and what's worth adopting in TabzChrome. Snapshot as of 2026-07-04, TabzChrome v1.6.0.

See also: `ChromeAPIResearch.md` (older API gap analysis from the 47-tool era).

---

## Headline: Split View (Chrome 145, Feb 2026)

Native side-by-side tabs in one window — right-click a tab → "Add tab to new split
view." Each pane scrolls independently, with a small toolbar for managing the split.
A stacked (top/bottom) variant ("Stacked Split Views") is in Canary testing as of
June 2026.

**What extensions get: read-only access only.**

- Chrome 140+ exposes `splitViewId` on tab objects
- Detectable via `tabs.query()` filters and tab change events — you can tell a tab
  is in a split and find its partner
- **No API to create, resize, arrange, or dissolve splits** — open request at
  [w3c/webextensions#967](https://github.com/w3c/webextensions/issues/967)

**TabzChrome fit:**

- `tabz_list_tabs` (`extension/background/browserMcp/tabs.ts`) could add
  `splitViewId` to its returned tab fields for free — Claude could then reason
  about paired tabs
- A `tabz_create_split_view` tool is **not possible yet**; `tabz_tile_windows`
  remains the closest equivalent
- Revisit if/when w3c/webextensions#967 lands a write API

## Directly useful for a side-panel extension

### `sidePanel.getLayout()` (Chrome 140)

Tells you whether the side panel is docked left or right (user-configurable
globally in Chrome settings; RTL installs default differently).

Most practical adoption for TabzChrome: the customize popover, drag hints, and any
edge-anchored UI currently assume one side. Small win, easy to adopt, no new
permissions.

### Side panel UI change (pin icon)

Chrome added a pin icon to the side panel toolbar and removed the global
side-panel toolbar icon. This changes how users discover/re-open the panel —
worth a one-line README note for onboarding ("pin TabzChrome from the panel
toolbar").

## Worth knowing, lower priority

| Change | Version | Relevance |
|--------|---------|-----------|
| Built-in AI APIs stable (Prompt, Summarizer, Translator, Language Detector — Gemini Nano, on-device) | Extensions since 138; web pages 148 | A `tabz_summarize_page` MCP tool needing no API key is feasible. Caveat: 2.7–4 GB model download, needs 22 GB free disk + 16 GB RAM or 4 GB VRAM GPU — must degrade gracefully |
| `browser.*` namespace for all extension APIs | 148 | Cross-browser (Firefox/Safari) alternative to `chrome.*`. Only matters if TabzChrome ever goes cross-browser |
| `tab.frozen` property | 132 | Tabs report whether Chrome froze them — another cheap field for `tabz_list_tabs` |
| `userScripts.execute()` | 135 | Arbitrary-time user-script injection; user scripts can bypass page CSP (the thing that blocks `tabz_execute_script` on GitHub etc.). Requires users to enable a developer-mode toggle, so probably not worth it |
| PDF annotations + Save to Google Drive | 145 | User-facing only, no extension API angle |
| Nothing new for tab management; vertical tabs still flag-only | 149 | Workspaces/session management remain extension-only territory |
| IndexedDB moves to SQLite backend | 150 | Harmless for TabzChrome — state lives in chrome.storage + tmux |
| Chrome DevTools for agents supports extension debugging (install/uninstall, reload, inspect popups & service workers) | I/O 2026 | Could improve our own AI-assisted dev workflow on this repo |

## Suggested bite-size 1.6.x

All backward-compatible, no new permissions:

1. Add `splitViewId` + `frozen` to `tabz_list_tabs` output
2. Adopt `sidePanel.getLayout()` for popover/edge-anchored UI positioning
3. README note about the new pin-icon discovery flow

## Sources

- [Chrome extensions what's new](https://developer.chrome.com/docs/extensions/whats-new)
- [Split View coverage (gHacks)](https://www.ghacks.net/2026/03/01/google-chromes-new-split-view-lets-users-view-two-tabs-side-by-side/)
- [MacRumors on Chrome 145](https://www.macrumors.com/2026/02/19/chrome-split-view-pdf-annotations/)
- [Split View extension API limits (tabgroupvault)](https://tabgroupvault.com/blog/chrome-split-view-guide)
- [w3c/webextensions#967 — split view write API request](https://github.com/w3c/webextensions/issues/967)
- [chrome.sidePanel reference](https://developer.chrome.com/docs/extensions/reference/api/sidePanel)
- [What's new in web extensions: I/O 2026 recap](https://developer.chrome.com/blog/extensions-io-2026)
- [Prompt API docs](https://developer.chrome.com/docs/ai/prompt-api)
- [Chrome 149 tab features](https://www.superchargebrowser.com/library/chrome-149-whats-coming-tab-users/)
