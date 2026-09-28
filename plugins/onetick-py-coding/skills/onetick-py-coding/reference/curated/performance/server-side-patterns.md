# Server-side patterns — compute in OneTick, not in pandas

The catalogue of "client/pandas anti-pattern → OneTick server-side equivalent → why". When you see
the left column in onetick.py code, rewrite it as the right column. The golden rule: **only the final,
aggregated result should arrive in pandas** — everything heavy happens in the query graph.

Verify exact signatures/arguments against the bundled onetick-py reference (this skill's `INDEX.md`
and `reference/docs/`) before emitting code — same rule as everywhere else in this skill.

## At a glance

| # | Client anti-pattern | OneTick server-side | Why it wins |
|---|---|---|---|
| 0 | `otp.DataSource(...)` auto-detects schema + you pull all fields | `schema_policy='manual'` + explicit `schema={...}`; project with `.table(...)` / `data[['COL']]` | skips an extra schema query; less data scanned/transferred |
| 1 | pull raw ticks → `df.resample()` bars | `src.agg({…}, bucket_interval=otp.Second(N))` | only bars cross the wire; agg in C++ at the data |
| 2 | `groupby.apply` VWAP/OHLC | `otp.agg.vwap/sum/average/max/min/first/last/…` (OHLC = first/max/min/last) | built-in EPs, no per-bucket lambda |
| 3 | `pd.merge`/`merge_asof` TRD×QTE | `otp.join_by_time` (as-of) / `otp.merge` | join in-graph, no full-frame transfer |
| 4 | `pd.merge` symbology/meta enrichment | `join_with_query` against the summary/ref db | enrich in-stream |
| 5 | numpy Black-Scholes + per-row loop | `otp.agg.option_price` (greeks EP); else vectorized column ops/`.script` | pricing next to data; no Python row loop |
| 6 | `df['MID']=(bid+ask)/2`, `np.where` moneyness | column expressions on the Source | computed in the stream |
| 7 | fetch all → filter in pandas | `src[cond]`/`.where()` + symbol/time selection at source | less scanned + transferred |
| 8 | `ThreadPoolExecutor(32 workers)` | one `otp.run(symbols=[…], concurrency=, batch_size=)` | server parallelizes; threads just overload tick servers/NFS |
| 9 | compute in pandas → write derived DB | build the derived DB from the graph output | no pull→compute→push round-trip |
| 10 | recompute bars from raw | read pre-built `*_BARS`/`*_DAILY` (tick type `DAY`)/`tickdb_query` when present | don't recompute stored data |
| 11 | re-fetch the same window | `otp.ReadCache` (with `otp.create_cache`) | reuse server-side |
| 12 | (principle) | **only materialize the final small result to pandas** | last-mile only |

Each row is expanded below with concrete DON'T → DO → why.

---

## 0. Source schema & field projection
**DON'T** let `otp.DataSource` auto-detect the schema and then carry every field:
```python
data = otp.DataSource(db, tick_type='TRD')   # runs an EXTRA query to read the schema from the DB
```
**DO** declare the schema manually and keep only the fields you need:
```python
data = otp.DataSource(db, tick_type='TRD',
                      schema_policy='manual', schema={'PRICE': float, 'SIZE': int})
# project further: schema_policy='manual_strict', or data.table(PRICE=float), or data[['PRICE']]
```
**Why:** automatic schema detection runs an **additional query** that can be slow on big databases and
may return different/incorrect results depending on how the DB was loaded/configured. A manual schema
skips that query; declaring/keeping fewer fields means less data scanned and transferred.
`schema_policy='manual_strict'` (and `data[['PRICE']]`) additionally raises if a field is missing —
catching schema drift early. **Do this in all production code.**

---

## 1. Bars / bucketed aggregation
**DON'T** pull raw ticks and resample in pandas:
```python
raw = otp.run(otp.DataSource(db, tick_type='TRD'), symbols=syms, start=s, end=e)  # millions of ticks
bars = raw.resample('600s', label='right').agg({'PRICE': 'last', 'SIZE': 'sum'})
```
**DO** aggregate in the graph; only bars cross the wire:
```python
data = otp.DataSource(db, tick_type='TRD',
                      schema_policy='manual', schema={'PRICE': float, 'SIZE': int})  # skip schema query
data = data.agg({'CLOSE': otp.agg.last('PRICE'), 'VOLUME': otp.agg.sum('SIZE')},
                bucket_interval=otp.Second(600))
bars = otp.run(data, symbols=syms, start=s, end=e)
```
**Why:** raw ticks never leave the server; aggregation runs in C++ at the data. Orders-of-magnitude
less data transferred and far faster.

> **Placement gotcha:** in a multi-column `.agg({...})`, `bucket_interval=` goes on the **`.agg(...)`
> call**, never inside the individual `otp.agg.*(...)` objects — the latter raises
> `ValueError: "bucket_interval" parameter can not be specified in multiple aggregation`. (The
> per-aggregation `bucket_interval` is only for the single-aggregation `otp.agg.X(...).apply(src)` form.)

## 2. VWAP / OHLC / standard statistics
**DON'T** `grouped.apply(lambda g: (g['PRICE']*g['SIZE']).sum()) / g['SIZE'].sum()` (per-bucket Python).
**DO** use the built-in EPs: `otp.agg.vwap('PRICE','SIZE')`, `otp.agg.sum/average/count/max/min/...`
inside `.agg(..., bucket_interval=...)`. There is no single `ohlc` aggregation — build OHLC bars
explicitly:
```python
data.agg({'OPEN': otp.agg.first('PRICE'), 'HIGH': otp.agg.max('PRICE'),
          'LOW': otp.agg.min('PRICE'), 'CLOSE': otp.agg.last('PRICE')}, bucket_interval=otp.Minute(1))
```
**Why:** native C++ aggregators; no per-bucket Python lambda, no raw-tick transfer.

## 3. As-of / time joins (e.g. TRD × QTE alignment)
**DON'T** `pd.merge(trd_df, qte_df, on=['Time','SYMBOL'])` / `pd.merge_asof(...)` after pulling both.
**DO** join in the graph with `otp.join_by_time([leading, other])` (as-of: prevailing tick ≤ leading
time) or `otp.merge` for combining streams.
**Why:** the join happens server-side over the streams; you don't transfer two full frames and re-join
them in pandas. (Leading source must have timestamps ≥ the others; see coding-rules "Join by Time".)

## 4. Metadata / symbology enrichment join
**DON'T** pull a big frame and `pd.merge` it with a summary/reference frame client-side.
**DO** enrich in-stream with `join_with_query` (per-tick parameterised lookup into the summary/ref db)
or symbology-mapping sources.
**Why:** enrichment is done where the data lives; only enriched results return.

## 5. Option pricing & greeks
**DON'T** compute Black-Scholes in numpy, especially in a per-row Python loop
(`for i: delta[i],... = _calc_greeks(...)`).
**DO** use the server-side **`otp.agg.option_price`** EP (prices/greeks in the graph). If a specific
metric isn't covered by an EP, compute it with **vectorized column operations** or a **`.script`**
on the Source — never a Python row loop over the result frame.
**Why:** pricing runs next to the data, vectorized in the engine; a Python per-row loop over millions
of rows is the worst case.

## 6. Derived columns (MID_PRICE, MONEYNESS, spreads)
**DON'T** `df['MID']=(df['BID']+df['ASK'])/2`, `df['MONEYNESS']=np.where(K>0, S/K-1, np.nan)` in pandas.
**DO** express them on the Source: `data['MID']=(data['BID_PRICE']+data['ASK_PRICE'])/2` and use
`otp.Source.where(...)` / column arithmetic for conditionals.
**Why:** columns are computed in the stream; fewer/derived columns are what you pull, not raw inputs.

## 7. Filtering / predicate pushdown
**DON'T** fetch all ticks then filter in pandas (`df[df['STATE']=='F']`).
**DO** filter at the source: `src[cond]` (returns `(passed, failed)`) or `src.where(cond)`, and select
the symbol/time range in the query. Push the filter as early as possible in the graph.
**Why:** less data scanned and transferred; the engine prunes before it sends anything.

## 8. Parallelism — the 32-worker trap (HARNESSED — see eval tests)
**DON'T** fan out onetick-py calls across Python threads/processes:
```python
with ThreadPoolExecutor(max_workers=32) as pool:   # 32 onetick-py workers
    results = pool.map(run_one_symbol, symbols)
```
**DO** issue ONE multi-symbol run and let OneTick parallelize server-side:
```python
result = otp.run(data, symbols=symbols, concurrency=8, batch_size=200, start=s, end=e)
```
**Why:** OneTick already parallelizes across symbols server-side. Stacking Python-level workers on top
multiplies tick-server connections and NFS load **without improving throughput** — it usually makes
things slower and destabilises the servers. Tune `concurrency`/`batch_size` on the single `otp.run`
instead of spawning workers.

## 9. Building derived databases
**DON'T** compute in pandas and write the resulting frame back to a derived DB
(`otp.run` → pandas → `db.add(frame)`).
**DO** build the derived DB from the **query-graph output** directly, so the heavy compute stays
server-side and the DB is written from the stream.
**Why:** avoids the pull → compute-in-pandas → push round-trip for every refresh.

## 10. Bar retrieval vs recomputation
**DON'T** recompute bars from raw ticks when pre-built bars exist.
**DO** read pre-built `*_BARS` databases / `tickdb_query` outputs when available (e.g. `CME_BARS`,
`US_COMP_BARS`, 1-minute/VWAP bar tick types). Only compute bars when they don't already exist.
For **daily** OHLCV, read the pre-aggregated `*_DAILY` databases (tick type `DAY`, e.g. `US_COMP_DAILY`)
instead of aggregating raw trades into a day:
```python
otp.run(otp.DataSource(db='US_COMP_DAILY', tick_type='DAY', schema_policy='manual'),
        symbols='AAPL', date=otp.dt(2024, 2, 1))   # OPEN/HIGH/LOW/CLOSE/VOLUME/... per day
```
**Why:** don't recompute what OneTick already stores.

## 11. Caching reused sub-queries
**DON'T** re-fetch the same window/intermediate repeatedly across steps.
**DO** use `otp.ReadCache` / query caching for reused intermediate results.
**Why:** reuse server-side instead of re-scanning.

## 12. Principle — materialize late
Keep every heavy step (aggregate, join, filter, price) in the graph; call `otp.run` **once**, as late
as possible, to pull only the final small/aggregated result into pandas for last-mile work
(plotting, a final tweak, hand-off). If a `otp.run` returns raw-tick volumes, the computation is in
the wrong place.

---

### Quick self-check before finishing
- Does any `otp.run` return raw ticks (not aggregated)? → move the aggregation into the graph.
- Any `df.resample/groupby/merge/apply` over tick data? → replace with `.agg`/`join_by_time`/column ops.
- Any `ThreadPoolExecutor`/multiprocessing of onetick-py calls? → collapse to one `otp.run(symbols=, concurrency=, batch_size=)`.
- Any per-row Python pricing loop? → `otp.agg.option_price` / vectorized / `.script`.
- Is `otp.run` called once, at the end, on the final result? → good.
