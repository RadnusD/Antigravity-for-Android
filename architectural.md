# Architectural Documentation: Antigravity for Android

- **Timestamp**: 2026-11-03T09:28:45+05:30
- **Version**: v1.1.1

## 1. High-Level System Architecture

The project delivers a standalone local Web GUI bridge for Google Antigravity AI on Android devices via Termux without requiring root, VNC, X11, or remote servers.

```
+--------------------------------------------------------------------------+
|                          Android Operating System                        |
+--------------------------------------------------------------------------+
       |                                              |
       v                                              v
+-----------------------------+               +----------------------------+
|        Termux App           |               |   Android Mobile Browser   |
| (POSIX Linux Subsystem)     |               |  (Chrome / PWA Web Client) |
+-----------------------------+               +----------------------------+
       |                                              ^
       v                                              | HTTP (Port 4400)
+-----------------------------------------------------+--------------------+
|  install.sh / patch_gui.sh (Setup, Launchers, Bionic PATH Bridge)        |
+--------------------------------------------------------------------------+
       |
       +---> [1] Official Google Engine: ~/.local/bin/agy (Native Bionic)
       |         - Serves embedded React GUI on http://127.0.0.1:4400
       |         - Listens for OAuth tokens via ~/.gemini/antigravity-cli/
       |
       +---> [2] agy-gui / agy-service (Foreground / Background Daemon)
       |         - CPU wake-lock management (termux-wake-lock)
       |         - Auto-browser opener (xdg-open -> termux-open-url)
       |
       +---> [3] Documentation & Distribution Subsystem
                 - GitHub Repository: github.com/RadnusD/Antigravity-for-Android
                 - Raw Assets & Scripts: raw.githubusercontent.com/RadnusD/...
                 - GitHub Pages Web: radnusd.github.io/Antigravity-for-Android
```

## 2. Module Boundaries and Data Flows

| Subsystem | File / Component | Upstream Source / Dependency | Inbound / Outbound Data Flow |
| :--- | :--- | :--- | :--- |
| **Installer** | `install.sh` | `https://antigravity.google/cli/install.sh` | Fetches official Google native CLI, resolves symlinks in `$PREFIX/bin`, configures DNS `no-aaaa` in `resolv.conf`, installs `agy-gui` and `agy-service`. |
| **Customizer / Patch** | `patch_gui.sh` | Local binary + `wallentx` legacy fallback | Applies UI title/branding patch, sets up auto-update hooks, verifies Python 3 dependencies. |
| **Uninstaller** | `revert_gui.sh` | Local files + `wallentx` pristine refresh | Kills background daemon PID, purges launcher scripts, restores stock binary. |
| **Landing Web** | `docs/index.html` | GitHub Pages (`radnusd.github.io`) | Serves mobile-optimized landing page, copy-install commands, and links to repository releases. |
| **SEO & Crawlers** | `docs/robots.txt`, `sitemap.xml` | Search engines | Declares canonical XML sitemap and indexing boundaries. |

## 3. Formatting Invariants
- Zero ambiguous URLs: All raw links explicitly target `https://raw.githubusercontent.com/RadnusD/Antigravity-for-Android/main/<script>`.
- Zero broken canonical references: Domain paths are strictly bound to `radnusd.github.io`.
- Autonomous execution immunity: Shell scripts rely solely on official upstream endpoints and relative environment variables (`$PREFIX`, `$HOME`).
