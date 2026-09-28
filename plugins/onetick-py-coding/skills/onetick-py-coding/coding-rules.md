# onetick-py coding rules (avoid common mistakes)

These are distilled `onetick.py` (`otp`) coding rules, drawn from real failure modes. **Follow them
exactly** when writing or fixing onetick.py code.

When a rule and a bundled doc/example disagree, prefer the doc/example for *syntax* and these rules
for *behavior/conventions* — and verify against `reference/` before emitting code.

---

## Answer exactly the question — with the simplest correct construct

This is the most common failure mode: producing *runnable but over-built* code that computes
something adjacent to, but not exactly, what was asked. Before writing, restate what the result must
contain (which rows, which columns, one value vs a time series) and build the **minimum** that
produces it.

- **Don't add machinery the task doesn't need.** Reach for the simplest source/op that answers it:
  - distinct values of a field → `src.distinct('FIELD')`, not `agg(...).merge(...)`.
  - a filter → `src[condition]` (returns `(passed, failed)`) or `src.where(condition)`, not a
    group-by.
  - "how many / total" of one thing → a single aggregation, not a multi-stage query.
  - Only use `otp.merge` + `otp.Symbols` when the task truly spans **multiple symbols**; a
    single-symbol question doesn't need cross-symbol merge.
  - Only use aggregation/`group_by`/multistage (FSQ) when the question requires grouping or
    cross-stage symbol logic. Aggregations and joins are the usual source of over-construction.
- **Match the output shape exactly.** Return only the columns asked for, with the requested names.
  Don't aggregate when a per-tick series was asked for, and don't return a raw series when a single
  total was asked for. If the task names a result field, use that exact name.
- **`result` is the `otp.run(...)` output — a DataFrame — never a bare Python scalar.** Even when the
  question reads like a single value ("what was the price …"), assign `result = otp.run(...)` (the
  one-row DataFrame with the relevant column), NOT `result = df['PRICE'].iloc[0]`. Collapsing to a
  scalar discards the run output that downstream tooling expects. Keep the value inside the
  DataFrame/Series; don't `.iloc[0]` it out.
- **"Simplest" means the simplest construct that *fully* answers the question** — not the fewest
  lines. Don't drop a required step (the right interval, the prevailing-tick logic, the market-data
  join) just to keep it short; under-answering fails as surely as over-building.
- **Apply the domain definition the question implies, not a generic transform.** E.g. "open orders"
  means orders not fully executed (`F`) and not cancelled (`C`) — filter to that, don't just list all
  order IDs. Re-read the Orders / terminology sections below and encode the actual definition.
- **Simplicity beats cleverness.** A short construct that returns the asserted result beats an
  elaborate one that returns something plausible-looking but wrong. When two approaches both look
  correct, pick the smaller one and verify each step against `reference/`.

---

## Run it before you trust it — execute and iterate (when a session is available)

The most common onetick.py failures are **invisible until you run the code**: a field that isn't on
that tick type (`AttributeError: There is no 'X' column`), an invalid per-tick `.script`
(`PER_TICK_SCRIPT … syntax error`), an empty result from a wrong date/symbol/db/interval, or a
result of the wrong shape. No amount of reading prevents all of these. **If you have any way to
execute** (a OneTick session + a shell/REPL/notebook), do not hand over code you have not run:

1. **Run it** — `otp.run(...)` against the data.
2. **If it raises, fix the actual cause** the error names — don't guess:
   - `AttributeError: There is no 'X' column` → that field isn't in this source/tick type; you're
     reading the wrong db/tick_type, or you need a join (e.g. order → market-data via MD_DB/MD_SYMBOL).
   - `PER_TICK_SCRIPT … syntax error` → replace the `.script` with vectorized column ops /
     a column `.apply(lambda x: ...)`; reserve `.script` for genuinely stateful logic.
   - locator / symbol / "WebAPI mode" errors → check `db`, `tick_type`, `symbol`, and where symbols
     are bound (`otp.run`/`otp.merge`, not duplicated on the source).
3. **If it runs, sanity-check the result** before trusting it: right number of rows, the requested
   columns/names, non-empty, plausible values. An **empty** result almost always means a wrong
   date / symbol / db / `[start, end)` interval — not "no data".
4. **Iterate** a few times (small budget) until it runs clean and the result actually answers the
   question; only then present it.

Running is cheap and catches the execution-sensitive tail that static rules can't. Prefer one quick
run-and-check over shipping unverified code.

---

## OneTick database & ticks (mental model)

- A OneTick database contains ticks **ordered by timestamp**.
- Timestamps in OneTick are a **number of nanoseconds**.
- Ticks are identified by **symbol** (a.k.a. ticker / instrument), **tick type**, and **date
  range** — think of them as indexes. You cannot retrieve ticks until all three are specified.

## Parameter specification

- Every `otp.DataSource` should specify the `tick_type` and `db` parameters.
- In coding tasks use **ONLY** database names and tick types from the provided context or the
  user's instructions. **NEVER make up** a database name or tick type.

## Type hints (time arithmetic)

- The difference between two timestamps (e.g. `data['TS1'] - data['TS2']`) yields a **milliseconds**
  precision result.
- Do **NOT** convert time differences by dividing by numeric constants
  (e.g. `/ 1_000_000_000`, `/ 60_000_000_000`).
- To get another granularity, wrap the difference in the appropriate time-delta constructor, e.g.
  `otp.Hour(t1 - t2)`.
- Use `otp.Nano`, `otp.Milli`, `otp.Second`, `otp.Minute`, `otp.Hour`, `otp.Month`, `otp.Year` to
  express time-delta units.

## Symbols

- Symbols are ticker names of a trading instrument. Every `otp.DataSource` must know its symbol(s)
  to retrieve ticks.
- Symbols set in `otp.run()` apply to **all** `otp.DataSource` sources of a query except sources
  that define symbols explicitly.
- **Never** specify symbols on the `otp.DataSource` if you also specify them in `otp.run()` or
  `otp.merge()`.
- Symbols can be set explicitly via the `symbol` / `symbols` parameter on an `otp.DataSource`.
- Symbols can be string constants or dynamic sub-queries:
  - one symbol → `symbols='SYMBOLNAME'`
  - multiple symbols → `symbols=['SYMBOL1', 'SYMBOL2']`
  - all symbols → `symbols=otp.Symbols(db='DBNAME')`
- `otp.Symbols()` gets symbols dynamically from a database and can transform them before applying.
  Use `for_tick_type` to select only the required tick type.
- `otp.merge()` combines results **across multiple symbols** into a single dataframe.
- To run a query for multiple symbols with **separate** dataframes (one per symbol), set `symbols`
  in `otp.run` (python list of strings, or an otp query, often `otp.Symbols`).
- To run a query for multiple symbols and get a **single** dataframe, merge with `otp.merge` and set
  its `symbols` parameter.
- `otp.Symbols`, `otp.Ticks`, `otp.CSV`, and any other source can specify symbols for the main query.
- `SYMBOL_NAME` and `TICK_TYPE` columns do **not** exist by default. They are added by `otp.merge()`
  with `identify_input_ts=True`.
- To fetch data for a query across multiple symbols, you **must** use merge:

  ```python
  # example 1: all symbols in DB
  query = otp.DataSource(db='S_ORDERS_FIX', tick_type='ORDER')
  merged_query = otp.merge([query], symbols=otp.Symbols(db='S_ORDERS_FIX'))
  df = otp.run(merged_query, start=yesterday, end=today)

  # example 2: specific symbols
  query = otp.DataSource(db='S_ORDERS_FIX', tick_type='ORDER')
  merged_query = otp.merge([query], symbols=['AAPL', 'NVDA'])
  df = otp.run(merged_query, start=yesterday, end=today)
  ```

- If a query has no symbols specified, `otp.config.default_symbol` is used by default in `otp.run`.
- The current symbol and its fields are available via the special `Symbol` property on a source,
  e.g. `data.Symbol.name` gives the current processing symbol name.

### Multistage queries (FSQ)

- Multistage queries have ≥2 sub-queries; each sub-query except the last produces a list of symbols
  for the next sub-query.
- The sub-query that produces symbols is the **First Stage Query (FSQ)**. It may also produce symbol
  parameters usable in the main query.
- The FSQ **must** put symbols in a column named `SYMBOL_NAME`.
- If the user provides CSV input, use `otp.CSV` in the FSQ:

  ```python
  csv_content = '''AAPL,some,other,fields\nNVDA,more,other,fields'''
  csv_query = otp.CSV(
      file_contents=csv_content,
      first_line_is_title=False,
      names=['SYMBOL_NAME', 'SYMBOL_PARAM1', 'SYMBOL_PARAM2', 'SYMBOL_PARAM3'],
  )
  query = otp.DataSource(db='S_ORDERS_FIX', tick_type='ORDER')
  merged_query = otp.merge([query], symbols=csv_query, identify_input_ts=True)
  df = otp.run(merged_query, start=yesterday, end=today)
  ```

  All CSV columns are strings by default; convert to needed types explicitly. Use
  `data['col'].str.to_datetime(format="<fmt>")` to convert a string column to a timestamp.
- Multistage queries must define symbols for **every** stage query.

### Symbol object (when logic is expressed as a function)

- Code may express a symbol-object passed automatically as a parameter when logic is a function:

  ```python
  def main_logic(symbol) -> otp.Source:
      # `symbol` is the immutable symbol-object: access processing symbol + its properties
      data = otp.DataSource(...)
      data['SYMBOL_NAME'] = symbol.name      # processing symbol name
      data['SOME_VALUE'] = symbol['X_str']   # a symbol property
      return data

  fsq_symbols['SYMBOL_NAME'] = ...  # mandatory: defines ticker to process in the main query
  fsq_symbols['X_str'] = 'abc'
  dfs = otp.run(main_logic, symbols=fsq_symbols)
  ```

- Optional FSQ meta-fields `_PARAM_START_TIME_NANOS` / `_PARAM_END_TIME_NANOS` let you adjust the
  query interval per symbol name.
- The symbol-object has **no** `Time` / `TIMESTAMP` columns, **cannot** be used in join or merge,
  exists only in multistage-query contexts when logic is a function, and always has the reserved
  name `"symbol"`.

### Cross-symbol logic

- When a query runs for multiple symbols, all calculations run **independently and concurrently**
  per symbol; combine results with `otp.merge`:

  ```python
  query = otp.DataSource(db='S_ORDERS_FIX', tick_type='ORDER')
  query = query.agg({'AVG_PRICE': otp.agg.average('PRICE')})  # runs per symbol in parallel
  merged_query = otp.merge([query], symbols=['AAPL', 'NVDA'], identify_input_ts=True)
  df = otp.run(merged_query, start=yesterday, end=today)
  ```

## Query interval

- The query interval can be specified:
  1. in `otp.run` via `start` / `end`;
  2. on any `otp.DataSource` via `start` / `end`;
  3. by combining (1) and (2);
  4. via symbol parameters `_PARAM_START_TIME` / `_PARAM_START_TIME_NANOS` and
     `_PARAM_END_TIME` / `_PARAM_END_TIME_NANOS` (ms/ns) in the FSQ — different intervals per symbol.
- If an `otp.DataSource` doesn't specify an interval, the `otp.run` interval is used.
- If an `otp.DataSource` specifies an interval, the `otp.run` interval is **ignored for that source**.
- **NEVER** specify the same interval explicitly on every `otp.DataSource`; set it once in `otp.run`.
- **NEVER** use the `date` parameter in `otp.run` / `otp.DataSource` for the interval; use explicit
  `start` and `end`.
- A query retrieves ticks with timestamps in `[start, end)` — **excluding** `end`, except the corner
  case `start == end`, when only ticks with timestamp `== start` are retrieved.
- For a **full-day** query, `end` MUST be greater than `start`: add 1 day to `end` for a full day.
- Use `back_to_first_tick` on the `otp.DataSource` to pick ticks when the interval is small or
  `end == start`; also use it to get the latest tick before a specified time.
- The query timezone can be set **only** via the `timezone` parameter in `otp.run` (affects all
  `start`/`end`/`date`).
- Do not use deprecated timezone names like `US/Eastern` (they are not supported on all systems),
  instead use IANA timezone names like `America/New_York`.
- `otp.Ticks` can specify generated tick timestamps only via the special `offset` field, never via
  the `Time` field.
- **EXCEPTION — SQL queries (`otp.SqlQuery`):** the interval goes **inside the SQL** as
  `start_time` / `end_time` conditions in the `WHERE` clause, **not** into `otp.run(start=, end=)` —
  a `SqlQuery` run without in-SQL time conditions fails with *"start time condition not specified"*.
  See the "SQL query rules" section.

## Common function hints

- If an existing function already implements the logic, **use it** — don't re-implement or duplicate.
- `otp.join` has an efficient mode `on='same_size'` for joining sources with exactly the same number
  of ticks (e.g. after equal-bucket aggregations).
- `otp.join` takes two sources as **positional** params: `otp.join(left_src, right_src, ...)`.
- After using the prevailing-tick addressing technique `[-N]`, you **must** drop the first N ticks.
- For a **simple per-tick conditional** (e.g. a buy/sell branch, a clamp, a flag), prefer
  **vectorized column arithmetic** or a column `.apply(lambda x: a if cond else b)` over `.script` —
  it's cleaner and avoids per-tick-script compilation errors. A classic idiom for side is a numeric
  **direction**:
  `(BUY_FLAG - 0.5) * 2` (→ +1 buy / -1 sell), then one comparison
  (e.g. `(MARKET_VWAP - ORDER_VWAP) * DIRECTION > 0`) instead of an `if/else`.
- Reserve `.script` for genuinely complex/stateful, multi-statement per-tick logic that can't be
  expressed as column operations. When you do use `.script`, keep it to supported constructs (a
  bad/over-rich script body raises a `PER_TICK_SCRIPT` compilation error at run time).
- Cross-tick state (a sliding/rolling window that recomputes on every tick rather than a fixed
  bucket, a running counter, "have I seen this ID before") is a `state_vars` problem, not an
  `.agg(bucket_interval=...)` problem — declare `src.state_vars['NAME'] = otp.state.tick_deque(...)`
  / `tick_list(...)` / `tick_set(...)` / `var(...)` and read/mutate it inside `.script`/`.update` —
  push on arrival, evict what aged out with `pop_front`/`erase`, measure with `get_size()`.
- You may **not** use onetick-py columns directly in top-level Python `if`/`else`/`while` statements
  — express the condition as column arithmetic, a column `.apply(lambda x: ... if ... else ...)`
  (the lambda's `if/else` is compiled by OneTick), or `src.where(cond)` to branch; reserve `.script`
  for genuinely stateful logic.

### Aggregations (bucketing)

- To bucket a **multi-column** aggregation, pass `bucket_interval=` (and `running=`, `bucket_time=`,
  `bucket_units=`, …) on the **`.agg(...)` call**, NOT inside the individual `otp.agg.*(...)` objects:
  ```python
  data.agg({'VWAP': otp.agg.vwap('PRICE', 'SIZE')}, bucket_interval=otp.Minute(10))   # correct
  ```
  Putting `bucket_interval` inside an aggregation used in the dict `.agg({...})` form raises
  `ValueError: "bucket_interval" parameter can not be specified in multiple aggregation`. The
  per-aggregation `bucket_interval` (which the `otp.agg.vwap(...)` signature lists) is only for the
  **single-aggregation** `otp.agg.X(...).apply(src)` form, e.g.
  `otp.agg.vwap('PRICE', 'SIZE', bucket_interval=otp.Minute(10)).apply(data)`.
- Use the time-delta constructors for `bucket_interval` (`otp.Second(n)`, `otp.Minute(n)`, …) so you
  don't have to set `bucket_units`; a bare number means **seconds**.

### Performance — compute server-side, not offload-to-pandas

OneTick runs aggregation, joins, filtering and pricing **in the engine, next to the data (C++)**. The
dominant performance mistake in AI-written otp is **offload-then-pandas**: pull raw ticks →
aggregate/join/price them in pandas/numpy → (optionally) fan out `ThreadPoolExecutor` workers. Push the
computation into the query graph and only materialize the final, small result in pandas.

- Prefer **aggregations over joins** whenever possible — they are faster.
- **Bars / stats:** `src.agg({...}, bucket_interval=otp.Second(N))` with `otp.agg.vwap/sum/first/last/
  max/min` — never pull raw ticks and `df.resample()` / `groupby().apply()`.
- **As-of TRD×QTE / combining streams:** `otp.join_by_time` (as-of) / `otp.merge` in the graph — never
  `pd.merge` / `merge_asof` on pulled frames.
- **Multi-symbol:** one `otp.run(symbols=[...], concurrency=, batch_size=)` and let OneTick parallelize
  server-side — never a `ThreadPoolExecutor` of per-symbol `otp.run` calls (extra Python workers only
  overload the tick servers / NFS without improving throughput).
- **Filtering / derived columns / pricing:** `src[cond]` / `.where()`, column expressions on the Source,
  `otp.agg.option_price` (or vectorized column ops / `.script`) — never a pandas filter or a per-row
  numpy loop over the result frame.
- **Materialize late:** call `otp.run` once, at the end; if it returns raw-tick volumes the computation
  is in the wrong place.

**Full catalogue** — 13 "pandas anti-pattern → OneTick server-side equivalent → why" rewrites with
DON'T→DO code: **`reference/curated/performance/server-side-patterns.md`**. Consult it for any
bars/VWAP/OHLC, TRD×QTE alignment, option greeks / vol-surface, multi-symbol scan, derived-database, or
"slow query / too much data / optimize" task.

### Comments

- Comment complex/non-obvious logic; explain the **why**, not just the what.

## Schemas

### Common ticks schema

- Sources have **no** `SYMBOL_NAME` field by default; it appears after `otp.merge` with
  `identify_input_ts=True`.
- Every `otp.DataSource` has `Time` and `TIMESTAMP` fields by default.
- `.add_prefix` / `.add_suffix` do **not** rename `Time` / `TIMESTAMP`.

### Quotes (QTE) schema — commonly present (be flexible)

- `ASK_PRICE` float, `ASK_SIZE` int, `BID_PRICE` float, `BID_SIZE` int.

### Trades (TRD) schema — commonly present (be flexible)

- `PRICE` float (executed trade price), `SIZE` int (executed trade quantity).

### Manual schema (when used)

- Set `schema_policy='manual'` on every `otp.DataSource` and define `schema` with the expected fields.
- Do **not** add `Time` / `TIMESTAMP` to `schema` — they exist by default; adding them is an error.

> Note: the default `schema_policy` deduces the schema automatically but is **not recommended for
> production code**. See `reference/docs/static/concepts/schema.md`.

## Common terminology

- `spread` applies only to quotes (QTE) or BBO (Best Bid and Offer).
- Trade `volume` = price × size of the trade.
- To filter out empty buckets (no data) after an aggregation, use `.where()` **after** the
  aggregation step.

## Join by time

- In `otp.join_by_time`, the **leading** source must have timestamps not less than the other sources
  being joined.
- The resulting timestamp is the **leading** source's timestamp; to keep timestamps from other
  sources, copy their `Time` column into another field first.
- Use `.add_suffix` / `.add_prefix` to resolve conflicting field names in joins (but never on `Time`
  / `TIMESTAMP`).

## Orders

- An order database contains client orders. An order is a sequence of events (ticks) showing how it
  changes over time; all ticks of one order share the same `ID`.

### Order states

- Starts with placement `STATE='N'`; finishes with cancellation `STATE='C'` or full fill `STATE='F'`.
- May have one or more partial fills `STATE='PF'` between placement and final state.
- Time sequence: `N <= PF <= F or C`.
- **Fully executed** = ends with `F`. **Partially executed** = has ≥1 `PF` or was fully executed.
  **Open** = neither fully executed nor cancelled.
- Order **arrival** = first tick (`STATE='N'`); order **exit** = final-state tick (`F` or `C`).

### Order tick type

- When querying an `S_ORDER_*` database without a tick type, you **MUST** use the `ORDER` tick type
  for any source (e.g. `otp.DataSource`, `otp.Symbols`).

### Order price vs market price

- The order's `PRICE` is the order's own (limit/quote) price and `PRICE_FILLED` is its execution
  price — **neither is "the market/trade/quote price"**. A question like *"what was the market/trade/
  quote price when the order was placed / filled / cancelled"* — the market's value at **any order
  event** — is always a **market-data join**, never a column read off the order (reading the order's
  `PRICE` runs fine and silently returns the wrong number):
  1. take the order **event** tick (filter by `ID` and `STATE`: `'N'` arrival / `'F'`/`'PF'` fill /
     `'C'` cancel) and drop the order's own price columns before joining;
  2. read `TRD` (or `QTE`) for **that order's instrument** — bind the sub-query to the order's
     `MD_DB` / `MD_SYMBOL` fields via a first-stage query:
     `otp.DataSource(db=symbol['MD_DB'], symbol=symbol['MD_SYMBOL'], tick_type='TRD')` (give it an
     explicit `schema={...}` — the schema can't be deduced from a parameterised db);
  3. as-of join the event to the prevailing market tick: `otp.join_by_time([event, market], ...)`
     with the event leading.
  Full worked recipe: `reference/curated/order_flow/market_price_at_order_arrival.md` — the same
  pattern answers arrival, fill, and cancel questions.

### P&L

- For realized P&L use execution fields: `PRICE_FILLED` (price_field) and `QTY_FILLED` (size_field);
  filter to `STATE='F'` **and** `STATE='PF'`.
- `PNL_REALIZED` needs a buy/sell flag where **0 = Buy, 1 = Sell**. Source `BUY_FLAG` is 1=Buy/0=Sell,
  so create a temporary column `(1 - BUY_FLAG)` and use it as `buy_sell_flag_field`.
- **NEVER** aggregate the result of `pnl_realized` unless the user explicitly asks for a summary
  (e.g. "total"/"sum"). By default output the raw time-series of execution ticks with `PNL_REALIZED`
  per fill / partial fill.

### Order flow processing

- To get the current snapshot at the moment of an order, fetch all order ticks from the trading
  session start (start of day if unspecified) up to that moment.
- Speed of execution = time between arrival and exit. An order's execution price is its VWAP.
- To compare market VWAP with order execution, use `.join_with_query` between arrival and exit.
- Distinguish buy vs sell orders when comparing orders with the market.
- Use `.agg` grouped by `ID` to get per-order info: final state, total size, volume, etc.
- "my trades" and "transactions" both mean **orders**.
- If the market-data database isn't specified, use the `MD_DB` and `MD_SYMBOL` fields to identify it:

  ```python
  def first_stage_query() -> otp.Source:
      orders = otp.DataSource(db='SOME_ORDERS_DB', tick_type='ORDER',
                              schema={'MD_DB': str, 'MD_SYMBOL': str})
      orders = orders.first()
      orders['SYMBOL_NAME'] = orders.Symbol.name  # FSQ must define the ticker to process
      return otp.merge(orders, symbols=otp.Symbols(db='SOME_ORDERS_DB'))

  def main_query_logic(symbol) -> otp.Source:
      orders = otp.DataSource(db='SOME_ORDERS_DB', tick_type='ORDER')  # takes symbol from SYMBOL_NAME
      trades = otp.DataSource(db=symbol['MD_DB'], symbol=symbol['MD_SYMBOL'], tick_type='TRD')
      ...  # join orders and trades

  merged_data = otp.merge(main_query_logic, symbols=first_stage_query())
  df = otp.run(merged_data, date=...)
  ```

### Common order schema fields (when client schema unknown)

| Field | Type | Meaning |
|---|---|---|
| `ID` | str | Order/execution identifier; links all states of an order |
| `STATE` | str | N=new, F=filled, PF=partially filled, C=cancelled, REJ=rejected, REP=replaced |
| `ORDTYPE` | str | MARKET, LIMIT, STOP, STOP_LIMIT, etc. |
| `PRICE` | float | The order's **own** limit/quote price (NaN for market orders) — **not** the market/traded price; for that, join market data via `MD_DB`/`MD_SYMBOL` (see "Order price vs market price") |
| `PRICE_FILLED` | float | Execution price; for fills (F) and partial fills (PF) only |
| `QTY` | float | Order quantity; same value across all events of an order |
| `QTY_FILLED` | float | Executed size; non-zero for fills/partial fills only |
| `SIDE` | str | BUY, SELL, SELL_SHORT |
| `BUY_FLAG` | int | 1 = buy, 0 = sell |
| `CONTRACT_SIZE` | float | Contracts→shares ratio (1.0, 100, 1000, ...) |
| `ACCOUNT`/`TRADER`/`PARTICIPANT` (+`_ID`) | str | Identifier of a trader/participant/account |
| `MD_DB` | str | Primary market-data database name; same per symbol; always populated |
| `MD_SYMBOL` | str | Instrument symbol in the primary market-data database; always populated |

## Cloud / database specifics

- Use `US_COMP` instead of `NYSE_TAQ` (deprecated).
- Use `US_COMP` instead of `US_COMP_SAMPLE` unless the user explicitly asks for sample data.
- Avoid `*_SAMPLE` databases unless specified by the user.
- **Calculate vs retrieve bars:**
  - To *calculate* bars → use `otp.agg()`.
  - To *retrieve* bars → use the specific `*_BARS` databases (e.g. `CME_BARS`, `US_COMP_BARS`) with
    the right tick type, e.g. CME has `TRD_1M` / `QTE_1M` (1-minute), `VWAP_1H` (1-hour VWAP), `DAY`
    (daily price & stats: closing/settlement price, open interest).

## Relative dates (in generated Python)

- When the user gives **relative / natural-language dates** ("last week", "yesterday", "past 30
  days"), generate code that computes the absolute dates **at runtime** using `datetime.date.today()`
  / `datetime.datetime.today()` as the reference. Do **not** hard-code dates unless the user gives an
  absolute date.

  ```python
  from datetime import date, timedelta
  start_date = date.today() - timedelta(days=7)
  end_date = date.today()
  ```

## Writing & running the code

- Write standard, self-contained, runnable Python: import `onetick.py as otp`, set up the session if
  needed, build the query, call `otp.run(...)`, then inspect the resulting `pandas.DataFrame`.
- Don't assume any particular execution environment. The code may run as a plain script, in a REPL,
  in a notebook, or be embedded by a caller — so don't rely on a feature of one of them.
- `otp.run` returns a regular `pandas.DataFrame`. To show results, `print()` it (or `display()` in
  environments that provide it) — don't assume output is auto-rendered.
- Assign the run output to `result` (`result = otp.run(...)`). Don't reduce it to a Python scalar
  via `.iloc[0]`; a single value belongs inside the returned one-row DataFrame/Series.
- Match the user's context: if they're clearly working in a notebook, notebook idioms are fine; if
  they ask for a script/module, a `def main(): ...` / `if __name__ == "__main__":` entry point is
  appropriate. Default to a straightforward top-to-bottom script when unspecified.

### Plotting

- `matplotlib`, `pandas.DataFrame.plot()`, and `seaborn` are the usual choices. You can also use
  onetick-py's own plotting, e.g. `data.plot(y='X', kind='bar')`.
- Default to the `Time` field on the x-axis for time series.
- **matplotlib safety:** passing a pandas Series directly can raise
  `ValueError: Multi-dimensional indexing is no longer supported`. Convert Series to NumPy with
  `.to_numpy()` for `plt.plot` / `plt.scatter` / `plt.fill_between` / etc.:

  ```python
  plt.plot(df['Time'].to_numpy(), df['HIGH'].to_numpy())
  ```

- Use `plt.show()` to render and `plt.savefig(...)` to persist a figure, depending on what the user
  wants — don't assume figures render inline automatically.

---

# SQL query rules (only when the user asks for a SQL query)

OneTick also supports SQL via `otp.run(otp.SqlQuery(sql))`. Apply these **only** when generating SQL.

- Store the SQL in a variable named `sql`; execute with `otp.run(otp.SqlQuery(sql))`.
- Time ranges map to `start_time` / `end_time`; a mentioned symbol maps to `symbol_name`.
- Table names use `DATABASE_NAME.TICK_TYPE` format, e.g. `FROM US_COMP.TRD`. Do **not** use
  `FROM US_COMP` with a `WHERE tick_type = ...` clause.
- Symbol filtering is in `WHERE` via `symbol_name`: `= 'SYM'`, or `IN ('S1','S2')`. In a JOIN, give
  an explicit `symbol_name` condition for **each** table.
- Timestamps:
  - If the `WHERE` filters on `TIMESTAMP`, do **not** alias any `SELECT` column as `TIMESTAMP` — use
    another name.
  - `WHERE` timestamp conditions use simple string literals `'YYYY-MM-DD HH:MM:SS <timezone>'`. Do
    **not** use `TIMESTAMP(...)` or other date functions.
  - Every data table has a `TIMESTAMP` field by default.
- Counting: use `SUM(CASE WHEN cond THEN 1 ELSE 0 END)` for conditional counts —
  `COUNT(*) FILTER (WHERE ...)` is **not** supported. Use `COUNT(*)` for row counts (NULLs aren't
  supported, so `COUNT(column)` is identical and unnecessary).
- `EXTRACT` is **not** supported — never use it.
- `HAVING` filters grouped data after `GROUP BY`; `WHERE` filters rows before aggregation. To drop
  empty buckets that produce NULLs, use `HAVING COUNT(*) > 0`.
- Aggregate functions with >1 parameter must use **named** parameters
  (e.g. `STANDARDIZED_MOMENT(INPUT_FIELD_NAME=PRICE, DEGREE=4)`); don't name a single-arg function's
  parameter.
- **Bars (SQL):** default to trade ticks if the source isn't specified. `time_bucket` / `ticks_bucket`
  take a **single** `INTERVAL` argument and go **only** in `GROUP BY`, never in `SELECT`:
  - Correct: `GROUP BY time_bucket(INTERVAL '10' MINUTE)`
  - Forbidden: `time_bucket(INTERVAL '10' MINUTE, TIMESTAMP)` or with extra args.
  - For the bucket timestamp, select an aggregate of `TIMESTAMP` (e.g. `MIN(TIMESTAMP)` /
    `MAX(TIMESTAMP)`).
  - Restrict `INTERVAL 'N' unit` to MILLISECOND, SECOND, MINUTE, HOUR, DAY, WEEK.
- **As-of join (time-based):** use `sametime_as_existing(leading.timestamp, joined.timestamp, 0)` in
  `WHERE`; the first arg is the leading source. It retrieves the prevailing record from the second
  source whose timestamp ≤ the leading source's timestamp. Alias conflicting field names; select only
  the leading source's time as `TIMESTAMP`, alias other timestamps uniquely.
- **Window functions:** use Window functions + `OVER` for **running** aggregations (e.g. running
  sums); use `RANGE` frame clauses to group by time periods (not `PARTITION BY`); do **not** use
  `GROUP BY`/`ORDER BY` in running aggregations; don't use Window functions for standard (non-running)
  aggregations.
- Provide exactly **one** SQL query per response; no semicolons (`;`) in the output.
