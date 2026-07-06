# Changelog

All notable changes to BlueLock Crypto Tracking are documented here.

---

## [1.4] — 2026-07-05

### Added
- **Watchlist tab** — track coins you don't own yet, independent of your Portfolio holdings.
  - Add any supported coin with an optional personal note (e.g. "Buy under $80k").
  - Live price shown per watched coin, driven by the existing price cache.
  - Double-click a note to edit it inline.
  - New backend table (`watchlist`) and endpoints: `GET/POST /api/watchlist`, `DELETE /api/watchlist/{symbol}`, `PUT /api/watchlist/{symbol}`.
- **Delete Holdings from the Portfolio table** — previously the only way to remove a coin was to open its detail modal and delete purchases one by one, which made deletion feel broken/hidden.
  - Each Holdings row now has 👁 View, ➕ Add Purchase, and 🗑 Delete Holdings buttons.
  - Deleting opens a confirmation dialog showing how many purchase entries exist for that coin, with explicit **Delete All Purchases**, **Manage Individually** (opens the detail modal), and **Cancel** choices — no accidental one-click data loss.
  - New backend endpoint `DELETE /api/portfolio/symbol/{symbol}` (wired to the existing `delete_all_entries_for_symbol()` database function, which previously had no route).
- **Multi-select bulk delete** on the Holdings table — checkboxes per row plus a "select all," with a "Delete Selected (N)" action that appears once something is checked.
- **`entry_count`** added to the coin stats returned by `calculate_coin_stats()`, so the frontend can show "You currently have N purchase entries" without an extra API call.
- **Dynamic version display** — the header, footer, and page title now pull the version live from `/api/health` (`config.APP_VERSION`) instead of a hardcoded string, so the two can no longer drift out of sync.

### Changed
- Version bumped to 1.4 in `config.py`.

### Documentation
- Added `ROADMAP.md` outlining planned and possible future work.
- Added version/license/Python badges to `README.md`.
- Updated `README.md` clone URL, Features table (Watchlist, improved delete UX), and privacy description.
- Added a security-contact link to the issue template picker (`.github/ISSUE_TEMPLATE/config.yml`).

---

## [1.3] — 2026-06-03

### Changed
- Version bumped to 1.3
- Improved Windows launcher reliability (`run.bat` rewritten):
  - All pip commands now use `venv\Scripts\python.exe -m pip` to prevent pip.exe lock errors
  - Browser opens after server is ready (4-second delay) — eliminates race condition
  - Removed `--reload` flag to avoid `watchfiles` Rust dependency on clean Windows installs
  - Added Python 3.10+ version check with clear download instructions
  - venv detection now checks for `venv\Scripts\python.exe` instead of folder presence
  - Per-command error handling with actionable error messages
  - Package import verification step before server starts
- `uvicorn[standard]` replaced with plain `uvicorn` in requirements.txt — removes native compilation dependency on Windows
- `.gitignore` updated to block `portfolio.db-shm` and `portfolio.db-wal` from being committed

---

## [1.2] — 2026-06-01

### Security
- **Restricted CORS** — `allow_origins` changed from `"*"` to `http://127.0.0.1:8765` to prevent cross-origin requests from other local pages.
- **purchase_date validation** — Date fields now enforce `YYYY-MM-DD` format server-side. Invalid formats return a clear validation error.
- **Thread-safe price cache** — Added a `threading.Lock` to prevent concurrent CoinGecko API requests under load.

### Fixed
- **CSV export route shadowed** — Moved `/api/portfolio/export/csv` before `/api/portfolio/{symbol}` in route registration so the export is always reachable.
- **Rate limiter memory leak** — Empty IP key entries are now cleaned up after their sliding window expires.
- **Coin dropdown out of sync** — `populateCoinSelects()` now builds dropdowns dynamically from the `/api/prices` API response instead of a hardcoded JS list. Adding a coin to `config.py` now automatically propagates to the frontend.
- **Locale hardcoded to en-US** — `formatLocalTime()` now uses the browser's locale for date/time display.

### Changed
- **FastAPI lifespan** — Migrated `@app.on_event("startup")` to the modern `lifespan` context manager (the `on_event` decorator is deprecated in current FastAPI).
- **Auto-refresh pauses on hidden tabs** — The 60-second auto-refresh now checks `document.hidden` and skips fetches when the browser tab is not visible.
- Removed unused `MIN_AMOUNT` constant from `config.py`.

### Added
- **Unit test suite** — `tests/test_portfolio.py` with 16 tests covering the calculation engine (single entry, DCA, price unavailable, edge cases, summary rollup, top gainer/loser).
- **GitHub Actions CI** — `.github/workflows/ci.yml` runs `pytest` and `ruff` on Python 3.10/3.11/3.12 on every push and pull request.
- **GitHub issue templates** — Bug report and feature request templates with Blue Lock Systems LLC privacy guidelines.
- **Pull request template** — Checklist including privacy/security requirements.
- **`SECURITY.md`** — Responsible disclosure policy and security design notes.
- **`run.sh`** — Linux and macOS one-click launcher (equivalent to `run.bat`).

---

## [1.1] — 2026-06-01

### Added
- **Live Purchase Preview** — as you fill in the Add Purchase form, the app instantly shows your Cost Basis, Current Market Price, Current Value, Profit/Loss, and ROI. No calculator needed.
- **Help text on all form fields** — every input now has a plain-language hint below it explaining what to enter.
- **Required field markers** and an `(Optional)` label on the date picker so the form is immediately clear to new users.
- **Large "Add Purchase" button** — more prominent and easier to find.
- **Top Gainer / Top Loser cards** on the Dashboard — automatically populated from your portfolio holdings.
- **Colored API Status** — the Dashboard API Status card now shows green `ONLINE` or red `OFFLINE`.
- **Local timestamp formatting** — Last Updated times are displayed in your local timezone instead of UTC.
- **`formatROI()` helper** — handles very large ROI values (e.g. +19,900%) cleanly without scientific notation.
- **Coin dropdown grouped by category** — Major, Privacy, and Stablecoin optgroups make selection faster.
- **`data/.gitkeep`** — ensures the `data/` folder is present in fresh clones so the server starts without errors.
- **`CHANGELOG.md`** — this file.
- **`CONTRIBUTING.md`** — contribution guide.

### Changed
- Portfolio table "Price Unavailable" now shows styled muted italic text instead of a bare dash.
- `About` page coin list now shows category badges alongside each coin.
- Version string bumped to `1.1` in `config.py` and the About tab.

### Fixed
- Dashboard Top Gainer / Top Loser grid was rendered but never shown — now correctly wired to portfolio data.
- Friendly validation messages on the Add Purchase form now read as natural sentences rather than terse error codes.

---

## [1.0] — Initial Release

- Live prices for 11 cryptocurrencies via CoinGecko public API (60-second cache).
- Portfolio tracker with multi-purchase support and weighted average cost calculation.
- Automatic calculation of Cost Basis, Current Value, Profit/Loss, and ROI.
- Price Calculator panel.
- Favorites system with Dashboard pinning.
- Search and category filtering on both Prices and Portfolio tabs.
- Portfolio Summary card (total value, total cost, P&L, overall ROI).
- Coin detail modal with full purchase history, edit, and delete.
- Export portfolio to CSV.
- SQLite local database — all data stays on your machine.
- Auto-refresh every 60 seconds.
- Windows launcher (`run.bat`) with automatic venv setup.
- Rate limiting, input validation, and graceful API failure handling.
