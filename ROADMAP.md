# Roadmap

This is a living document of planned and possible future work for BlueLock
Crypto Tracking. Nothing here is a commitment or a timeline — it's a list of
directions the project could go, roughly ordered by how well they fit the
current local-first, no-accounts architecture.

---

## Under consideration for v1.5

- **Sortable table columns** — click a Holdings/Prices column header to sort
  by price, ROI, value, etc.
- **Price & portfolio alerts** — notify when a coin crosses a price
  threshold, or when total portfolio value moves by a set percentage.
  Requires a background polling task; delivery channel (desktop notification
  vs. email/webhook) still to be decided.
- **Historical price chart** — a simple line chart per coin, likely fed by
  CoinGecko's historical market-data endpoint.
- **Allocation pie chart** — visualize portfolio composition by coin/category.
- **Full backup / restore** — export *raw* purchase entries (not just the
  calculated CSV summary) as JSON, and support re-importing them.
- **Realized vs. unrealized P&L** — split out profit from closed positions
  (once a "sell" transaction type exists) from paper gains on current
  holdings.

## Longer-term / needs more design work

- **Transaction types beyond "buy"** — sell, transfer, staking reward,
  mining income. This changes the data model meaningfully, so it needs its
  own design pass before implementation.
- **Multiple portfolios / account separation** (e.g. Personal, Cold Storage,
  Trading) — requires a schema change (an `accounts` table, entries gaining
  an `account_id`) and touches most existing endpoints.
- **Exchange import** (Coinbase, Kraken, Binance CSV/API) — each exchange has
  its own export format and, for API-based sync, its own auth flow and rate
  limits.
- **Tax lot accounting** (FIFO / LIFO / average cost) — a materially
  different cost-basis engine than the current weighted-average approach.
  Useful for tax reporting, but out of scope for casual tracking.
- **News/sentiment per coin** — would require a third-party news or
  sentiment API and a stance on which sources to trust.

## Explicitly not planned

- **Cloud accounts, sync, or hosted mode.** This project is local-first by
  design — see [SECURITY.md](SECURITY.md). Multi-device sync would require
  either a hosted backend or a self-hosted sync protocol, either of which is
  a different project with a different threat model.
- **Wallet connection / on-chain transaction signing.** BlueLock is a
  *tracker*, not a wallet. It never touches private keys or seed phrases,
  and that boundary is intentional.

---

Priorities shift based on what people actually ask for — the
[issue tracker](https://github.com/bluelocksystems-lab/BlueLockCryptoTracking/issues)
is the best signal for what moves up this list.
