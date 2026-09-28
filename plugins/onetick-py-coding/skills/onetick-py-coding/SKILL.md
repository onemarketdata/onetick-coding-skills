---
name: onetick-py-coding
description: >-
  Write, debug, or review onetick.py (otp) code for the OneTick tick database — data retrieval,
  aggregations, joins, orders/executions, symbols/multistage queries, bars,
  best-execution analytics, and OneTick SQL queries. Bundles the full onetick-py API reference
  (every function/class/method as a lookup-able markdown file), curated examples, and distilled
  onetick.py coding rules that prevent the most common mistakes. Use this skill WHENEVER the task involves
  `onetick.py`, `otp.`, `otq.`, `otp.DataSource`/`otp.Ticks`/`otp.run`/`otp.merge`/`otp.agg`,
  OneTick databases (US_COMP, CME, S_ORDERS*, *_BARS), tick types (TRD/QTE/ORDER), OneTick SQL,
  or OneTick order analysis — even if the user doesn't name the library explicitly but is
  clearly querying tick/trade/quote/order market data on OneTick. Also use it for onetick.py
  **performance** — writing or optimizing otp so the work runs server-side in the OneTick query graph
  (bars/VWAP, TRD×QTE joins, option greeks, multi-symbol scans) instead of pulling raw ticks into
  pandas; trigger on pandas `resample`/`groupby`/`merge` over ticks, `ThreadPoolExecutor`, or
  "slow query"/"optimize"/"too much data". Always consult the bundled reference instead of writing otp
  code from memory; the API surface is large and easy to get wrong.
---

# onetick-py coding

`onetick.py` (imported as `otp`) is the Python interface to OneTick, a high-performance tick
database for financial market data. Its API is large, idiomatic, and easy to get subtly wrong from
memory (symbol binding, query intervals, schema deduction, joins, order semantics). This skill
gives you three things so you don't have to guess:

1. **`coding-rules.md`** — distilled onetick.py coding rules that encode real failure modes
   ("avoid common mistakes"). Read this first.
2. **`INDEX.md`** — a grouped index of all ~550 bundled reference markdown files (every API
   function/class/method + guides + curated examples), each with its title and one-line purpose.
   Use it to locate the exact file to open.
3. **`reference/`** — the actual docs:
   - `reference/curated/` — **skill-maintained worked examples for tricky patterns** (e.g. market
     price at order arrival via MD_DB/MD_SYMBOL; order execution vs market VWAP; sliding/rolling
     per-tick state without bucket aggregation) plus
     `reference/curated/performance/server-side-patterns.md` — the **compute-server-side catalogue**
     (pandas anti-pattern → OneTick equivalent). **Check here first** for orders / market-data-join
     tasks and for any performance work — these are the highest-signal end-to-end recipes.
   - `reference/docs/` — the auto-generated onetick-py API reference and guides.
   - `reference/examples/` — curated, hand-written onetick.py examples (orders, symbols,
     sanity checks, testing).
   When you look for examples, **consult `INDEX.md` (it lists `reference/curated/` at the top) — don't
   just glob `reference/examples/`**, or you'll miss the curated recipes.

## Workflow — follow this every time you write or fix otp code

1. **Read `coding-rules.md`.** It's short and prevents the mistakes that matter (time arithmetic,
   symbol/`otp.run` binding, query-interval rules, schema policy, order states, P&L, SQL bars,
   as-of joins). Skipping it is the #1 cause of wrong code.

2. **Find the right reference file via `INDEX.md`.** Before using any `otp.*` symbol, look it up.
   Don't trust a remembered signature — open the file and confirm the exact arguments, defaults,
   return type, and examples. The index is grouped so you can scan to the right place:
   - Need a **data source**? → `INDEX.md` → "API — sources" (`otp.DataSource`, `otp.Ticks`,
     `otp.CSV`, `otp.Query`, ...).
   - Need a **Source method** (`.agg`, `.join`, `.where`, `.script`, `.first`, `.high`, ...)?
     → "API — Source methods".
   - Need an **aggregation** (`otp.agg.sum`, `vwap`, `ohlc`, `percentile`, ...)? → "API — aggregations".
   - Need a **top-level function** (`otp.merge`, `otp.join`, `otp.join_by_time`, `otp.run`, ...)?
     → "API — functions" / "API reference — top level".
   - Need **column operations** (`.str.*`, `.dt.*`, casts, math)? → "API — column operations".
   - Need **datetime/offsets** (`otp.dt`, `otp.Minute`, ...)? → "API — datetime helpers/offsets".
   - Working with **orders, symbols, or want a worked example**? → the
     "Curated examples" sections first — they show idiomatic end-to-end code.
   - Asked for the **market/trade/quote price at an order event** ("price when the order was
     placed / filled / cancelled")? That is **never a column on the order** (the order's `PRICE` is
     its own limit price) — it's a market-data join via the order's `MD_DB`/`MD_SYMBOL` (FSQ).
     Open `reference/curated/order_flow/market_price_at_order_arrival.md` and follow it.
   - Asked something like "count of trades per order in the trailing 30 seconds, updated on every
     trade", "the worst bid/ask price seen while an order sits open, dropping price levels once they
     age out of the order's window", "a running tally that carries across ticks and resets once the
     timestamp changes", or "the time gap since the last order from this same participant" —
     especially if bucket aggregation is explicitly ruled out? Don't reach for
     `.agg(bucket_interval=...)` — that only re-evaluates on a fixed calendar grid, not on every tick.
     This is a **state variables** problem: "API — state variables (otp.state.*)"
     (`tick_deque`/`tick_list`/`tick_set`/`var`, used inside `.script`/`.update`).
   - Need to **reconstruct or query the order book** (depth, top-of-book, book VWAP, snapshots)?
     → "API — order book sources" (`otp.ObSnapshot`/`otp.ObVwap`/`otp.ObSummary`/`otp.ObSize`/...) and
     "API — snapshot sources" — don't hand-roll book state with `otp.state.*`; these dedicated
     sources already do it.
   - Need to **check what databases/dates/tick types actually exist**? Never invent one (see
     `coding-rules.md`) — look it up via "API — data inspection" (`otp.databases()`,
     `otp.inspection.DB`).
   - Need to **execute/run** a script and no OneTick session is already active? → "API — session /
     config / locator / databases": `otp.TestSession()` for a self-contained runnable example
     (demo-ready defaults), `otp.Session()` for a real session (needs explicit config, e.g.
     `default_symbol`).
   - Need a **how-to / concept** (schema deduction, calc graph, bars, best-ex, real-time)?
     → "Guides & concepts" and "use cases".
   - **Optimizing for performance** (slow query, `resample`/`groupby`/`merge` over ticks,
     `ThreadPoolExecutor`, "too much data")? → the "Performance" section of `coding-rules.md` and
     `reference/curated/performance/server-side-patterns.md` (compute server-side, not offload-to-pandas).

3. **Open the file(s) and read the relevant parts.** For a function/class, confirm the signature
   and parameter semantics. For a task (e.g. "VWAP of an order"), prefer the matching curated
   example as a template.

4. **Write the code** following `coding-rules.md`. First restate exactly what the result must
   contain (which rows/columns, one value vs a series), then build the **simplest construct** that
   produces it — don't add aggregation/`merge`/multistage machinery the task doesn't need
   (over-built-but-wrong is the #1 failure; see "Answer exactly the question" in `coding-rules.md`).
   Write standard, self-contained, runnable Python (import `otp`, build the query, `otp.run(...)`,
   inspect the resulting `pandas.DataFrame`). Don't assume a specific execution environment; match
   the user's context (script, REPL, or notebook).

5. **Run it and iterate — if you can execute** (a OneTick session + shell/REPL/notebook). Most
   onetick.py errors (missing columns, `.script` compile errors, empty results from a wrong
   interval, wrong result shape) are invisible until you run. Execute `otp.run(...)`, read any
   error and fix its actual cause, sanity-check the result (rows/columns/non-empty/plausible),
   iterate a few times, and only then present. See "Run it before you trust it" in `coding-rules.md`.

6. **Sanity-check against the rules** before finishing: tick_type+db on every `otp.DataSource`;
   symbols set in exactly one place; interval in `otp.run` (not `date`); never invent db/tick-type
   names; use real signatures from the reference.

## How the reference is laid out

```
reference/docs/
├── api/                 # one markdown file per function / class / method
│   ├── sources/         #   otp.DataSource, otp.Ticks, otp.CSV, otp.Query, order_book/, snapshot/
│   ├── source/          #   Source methods (the core query object) — the largest section
│   ├── aggregations/    #   otp.agg.* (sum, vwap, ohlc, percentile, correlation, ...)
│   ├── functions/       #   otp.merge, otp.join, otp.join_by_time, otp.apply, math/, misc/, cache/
│   ├── operation/       #   column ops: str/, dt/, math/, float/, decimal/
│   ├── datetime/        #   otp.dt, otp.datetime, offsets/ (Nano..Year)
│   ├── types/ state/ session/ misc/ oqd/ data_inspection/
│   └── run.md, config.md, performance.md, root.md
└── static/              # narrative guides
    ├── concepts/        #   data model, calc graph, schema deduction, symbols, start/end
    ├── getting_started/ #   retrieval, filtering, aggregations, joins, real-time, use_cases/
    ├── installation/ configuration/ testing/ ray/
    └── overview.md, changelog.md

reference/examples/      # curated, hand-written examples (highest signal)
├── selecting_time_series/  # db, query interval, symbols, tick types
├── tests/               # writing tests for onetick.py logic
└── *.md                 # ticks, quotes, trades, symbols, point-in-time, data science
```

## Why this matters (don't shortcut it)

The onetick.py API has many sources, ~46 aggregations, ~100 Source methods, and several
non-obvious conventions (lazy calc graph, schema deduction, symbol binding in `otp.run`/`otp.merge`,
`[start, end)` intervals, order state machines). Writing from memory produces code that imports
fine but returns wrong results or raises at run time. The index + per-symbol docs let you verify
the exact contract cheaply, and `coding-rules.md` captures the conventions that aren't discoverable
from a single function's docstring. Spend the few seconds to look things up — it's far cheaper than
debugging a silently-wrong query.

The `reference/` bundle ships with the skill and is kept current — just use it; there is nothing
to install or refresh.
