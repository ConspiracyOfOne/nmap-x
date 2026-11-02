# AGENTS.md — Nmap XML Analyzer / Visualizer

## Overview
Single-file HTML/CSS/JS visualizer for Nmap XML scan reports. Everything lives in `nmap-x.html` (~140 KB). No build step, no Node.js, no npm, no framework — vanilla JS/CSS in one file.

## How to run / test
**Run tests:** Open `tests.html` in a browser (Chrome headless works). It auto-runs on load:
```powershell
chrome.exe --headless=new --disable-gpu --allow-file-access-from-files --dump-dom "file:///C:/path/to/tests.html"
```
Check `<title>` for `TESTS_DONE` or `TESTS_FAILED`, or read `<pre id="result">`.

**Run the app:** Open `nmap-x.html` directly in a browser — it's self-contained, no server needed.

## How the app works
- `window.NMAP_ANALYZER` is the public API:
  - `parseNmapXml(xmlText, filename)` — parses Nmap XML (asynchronous via `setTimeout`)
  - `getData()` — returns parsed data (`ips`, `ports`, `services`, `hostCount`, `metrics`)
- Port keys are protocol-agnostic: `port/protocol` (e.g. `443/tcp`, `22/tcp`)
- Theme: `data-theme` attribute on `<html>` (`dark` or `light`). Toggle via `toggleTheme()` or the Theme button (`#dockThemeToggle` / `#themeToggle`)
- All JS is inline in `nmap-x.html`. Only external dependency is Chart.js from CDN.

## Key files
| File | Purpose |
|------|---------|
| `nmap-x.html` | Entire app (HTML + CSS + JS inline) |
| `tests.html` | Test runner — loads `nmap-x.html` in an iframe, drives `NMAP_ANALYZER` |
| `svg/*.svg` | Icon assets |
| `nmap/*.xml` | Sample Nmap scan reports for testing |
| `release/v0.1/`, `release/v1/` | Historical export builds (do not edit) |
| `IMPLEMENTATION_CHECKLIST.md` | Task list — update when work is done |

## Port data model
- `PORT_KEY = port/protocol` (e.g. `443/tcp`)
- `DATA.ips[ip].ports[]` — array of port objects with fields: `port`, `proto`, `portKey`, `state`, `reason`, `reasonTtl`, `owner`, `svc`, `product`, `version`, `extrainfo`, `ostype`, `svcKey`, `cpe[]`, `scripts[{id, output, elems[]}]`, `mac`, `vendor`
- `DATA.ports[portKey]` — aggregate across hosts: `totalIps`, `openIps`, `services`, `total`, `open`, `ipList`

## Conventions / gotchas
- **No build step:** Edit `nmap-x.html` directly. No transpile, bundle, or lint.
- **`parseNmapXml` is asynchronous:** Wait (~500ms) after calling before calling `getData()`.
- **Theme state:** Stored in `data-theme` attribute on `<html>` — use `data-theme="dark"` or `data-theme="light"`.
- **Port keys:** Always use `port/protocol` format. Do not assume protocol-agnostic port numbers.
- **No git repo, no `.gitignore`, no `package.json`.** Do not look for Node tooling or a lockfile.
- **Icon files live in `svg/`** — when adding/changing UI icons, use files from this directory rather than inline SVGs where possible.
- **Theme colors use CSS variables** (`--bg`, `--layer`, `--layer-2`, `--text-01`, `--text-02`, `--interactive`, `--interactive-hover`, `--accent`, `--border`) defined in `:root` and `[data-theme="light"]`.
