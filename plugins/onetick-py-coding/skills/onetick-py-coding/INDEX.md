# onetick-py reference index

521 bundled onetick-py reference files (API, guides, and curated examples). Each entry is a path plus a one-line _what it does & when to use it_ to help you pick the right file.

Find the function/class/topic below, then **read the listed file** under `reference/` for the exact signature, arguments, return type, and examples. Paths are relative to the skill's `reference/` directory.

## Curated examples (`reference/examples/`)

### CURATED — order-flow patterns (market price at order arrival; order execution vs market VWAP)

- `curated/order_flow/market_price_at_order_arrival.md` — **What was the market (trade) price at the time an order was placed / at order arrival** — Worked recipe: get the market trade/quote price at an order event (placed/filled/cancelled) by joining the order to market data via its MD_DB/MD_SYMBOL — the order's own PRICE is its limit price, n...
- `curated/order_flow/order_vs_market_vwap.md` — **Were my orders executed above or below the market VWAP (execution performance vs market)** — Worked recipe: judge execution quality by comparing each order's execution VWAP (VWAP of its fills) against the market VWAP over the order's active window

### CURATED — performance: compute server-side, not offload-to-pandas (pandas anti-pattern → OneTick equivalent catalogue)

- `curated/performance/server-side-patterns.md` — **Server-side patterns — compute in OneTick, not in pandas** — Shows pandas anti-patterns and OneTick graph equivalents for schema projection, aggregation, joins, enrichment, caching, and final-result materialization

### EXAMPLES — top level (ticks, quotes, trades, symbols, pit, data science)

- `examples/cloud.md` — **Check whether a give date was a holiday in the Cloud DB** — Shows how to query the Cloud OQD market calendar and test whether a given date returns any ALL_HOLIDAY rows for a cloud database
- `examples/data_inspection_databases_dates.md` — **List available databases** — Returns the database catalog, then lets you inspect a database’s available dates or last data date when choosing a source
- `examples/data_inspection_tick_types_schema.md` — **Available tick types for a given database** — Shows how to list tick types and inspect a database schema, optionally for a specific date, when exploring available data fields
- `examples/data_science.md` — **Correlation between two tickers** — Shows how to compute VWAP for two symbols, join them, and aggregate their correlation when comparing paired ticker behavior
- `examples/pit.md` — **Retrieve a data tick at a specified exact time** — Shows how to fetch the latest tick at an exact timestamp, using back_to_first_tick and last_tick when you need point-in-time state
- `examples/quotes_creating_quotes_ticks.md` — **creating quotes ticks with otp.Ticks** — Creates a Ticks object from column arrays for synthetic quote ticks, when you need to build example or test tick streams with explicit offsets and prices
- `examples/quotes_info.md` — **what is Quotes** — Explains QTE and NBBO quote ticks, their usual fields, and when to use them to inspect bid-ask spread data
- `examples/random_data_analysis.md` — **The largest number of consecutive upticks** — Shows how to bucket trades by consecutive nondecreasing prices and how to extract the highest-volume 10-minute interval
- `examples/random_utilities.md` — **Bins** — Shows how to map numeric values into bins, hash a field, and avoid database locator gaps by specifying dates explicitly
- `examples/symbols_cross_logic.md` — **The cross symbols logic** — Explains merging per-symbol pipelines across symbols, then post-processing the combined ticks when you need cross-symbol comparison or ranking
- `examples/symbols_listing.md` — **get all symbols from some database** — Returns a symbol list query for a database, used to enumerate available tickers or filter them by pattern before running it on a date
- `examples/ticks_creation_methods.md` — **Create ticks using pandas.DataFrame** — Constructs otp.Ticks from a pandas.DataFrame with a Time column, or from list and dict inputs with offset-based rows
- `examples/ticks_purpose_and_timing.md` — **otp.Ticks purpose** — Emits synthetic ticks for testing, mixing pandas or CSV inputs, or injecting static values; use it when you need controlled tick sequences or custom timestamps
- `examples/trades.md` — **Trades** — Explains trade ticks, their PRICE and SIZE schema, creating sample TRD ticks with otp.Ticks, and summing trade volume with otp.DataSource.agg

### EXAMPLES — schema references

- `examples/api/ticks_schema.md` — **onetick.py and OneTick types mapping** — Maps OneTick field types to Python and otp scalar types, and clarifies timestamp and boolean handling when defining schemas or reading data

### EXAMPLES — selecting time series (db, interval, symbols, tick types)

- `examples/selecting_time_series/database.md` — **how to set a database to fetch data** — Shows how to specify database, symbol, and tick type locators for DataSource when fetching data, especially when db is preferable or symbols are bound
- `examples/selecting_time_series/onetick_db_structure.md` — **OneTick database structure** — Explains ticks, daily archives, tick types, and symbols so you can identify the database components required to read or write data
- `examples/selecting_time_series/query_interval.md` — **Query intervals; start and end query interval** — Explains how otp.run and DataSource start/end or date define the global query interval, and when to widen it to cover all sources
- `examples/selecting_time_series/symbols.md` — **Selecting time series** — Explains bound, unbound, and mixed symbol assignment in queries, plus symbol-related errors and accessing the current symbol name
- `examples/selecting_time_series/tick_types.md` — **what is tick type and how to set it up** — Explains that every source should specify a tick type, shows how to set it on otp.DataSource, and warns missing or invalid types can error or return no ticks

### EXAMPLES — writing tests for onetick.py logic

- `examples/tests/example_of_test_for_logic_that_combines_bound_and_unbound_symbols.md` — **Example of test for logic that combines bound and unbound symbols** — Shows how to test a query mixing a bound DataSource with unbound symbols, then run it against multiple symbols and assert joined aggregation output
- `examples/tests/example_of_test_for_logic_with_bound_symbols_in_merge.md` — **Example of test for logic with bound symbols in merge** — Shows how to test merge logic across bound symbols by aggregating per-symbol volume, sorting, and asserting the top results
- `examples/tests/example_of_typical_issues_in_tests.md` — **Example of tgypical issues in tests** — Explains common mistakes in otp.TestSession, otp.DB, and otp.Ticks setup when writing OneTick tests and fixtures
- `examples/tests/example_test_for_logic_with_complex_script_logic.md` — **Example test for logic with complex script logic** — Shows how to test a source.script transformation with date fields and weekday-dependent branching when validating custom tick mutations
- `examples/tests/test_for_existing_otq.md` — **Example of test for an existing OTQ** — Shows how to load an OTQ query, feed input pins with tick data, and assert the output when testing existing queries
- `examples/tests/test_for_logic_in_join_with_query.md` — **Example of test for logic in join_with_query** — Shows how to test join_with_query by emulating subquery symbol and interval parameters with TestSession, DB fixtures, and assertions on joined results

## API reference (`reference/docs/api/`)

### API reference — top level (otp.run, config, performance)

- `docs/api/config.md` — **otp.config** — Provides access to OneTick configuration variables, including defaults and runtime overrides, when setting query time, database, timezone, or license settings
- `docs/api/performance.md` — **otp.perf** — Performance measurement helpers that run measure_perf.exe and parse summary files when profiling a query or Source
- `docs/api/root.md` — **API reference** — Introduces the otp and otq import shortcuts used throughout the API docs and examples
- `docs/api/run.md` — **otp.run** — Executes a query and returns its result; use it to run sources, OTQ files, graphs, or symbol lists with time and auth settings
- `docs/api/run_async.md` — **otp.run_async** — Runs otp.run asynchronously and returns a coroutine; use it with await or asyncio when you need nonblocking query execution

### API — aggregations (otp.agg.*: sum, vwap, ohlc, percentile, ...)

- `docs/api/aggregations/average.md` — **otp.agg.average** — Computes the average of a column or operation, with bucketed or running windows when you need mean aggregation over ticks
- `docs/api/aggregations/correlation.md` — **otp.agg.correlation** — Returns Pearson correlation coefficient between two numeric fields when you need bucketed or running correlation over tick streams
- `docs/api/aggregations/count.md` — **otp.agg.count** — Returns the number of ticks in each bucket, sliding window, or grouped sub-bucket when you need tick counts rather than sums
- `docs/api/aggregations/distinct.md` — **otp.agg.distinct** — Outputs one tick per distinct key combination, choosing first or last occurrence, when you need unique values by attribute set
- `docs/api/aggregations/exp_tw_average.md` — **otp.agg.exp_tw_average** — Computes an exponentially time-weighted average per bucket, with decay set by lambda or half-life index when you need recency-weighted aggregation
- `docs/api/aggregations/exp_w_average.md` — **otp.agg.exp_w_average** — Computes an exponentially weighted average of a numeric field per bucket; use it when recent ticks should count more than older ones
- `docs/api/aggregations/find_value_for_percentile.md` — **otp.agg.find_value_for_percentile** — Returns the value whose percentile rank is closest to a requested percentile, or an interpolated/threshold value when you need percentile-based bucket summaries
- `docs/api/aggregations/first.md` — **otp.agg.first** — Returns the first value in each bucket or running window, used when you need the earliest tick’s field value under OneTick bucketing
- `docs/api/aggregations/first_tick.md` — **otp.agg.first_tick** — Selects the first n ticks in each bucket or running window when you need earliest events, boundary handling, or grouped bucket output
- `docs/api/aggregations/first_time.md` — **otp.agg.first_time** — Returns the timestamp of the first tick in each bucket; use it when you need bucket start timing or first-event time
- `docs/api/aggregations/generic.md` — **otp.agg.generic** — Runs custom aggregation logic per bucket via query_fun when built-in aggregators don’t fit and you need apply()-based or Source.agg() aggregation
- `docs/api/aggregations/high_tick.md` — **otp.agg.high_tick** — Selects the n ticks with the highest values in a column, when you need top-value ticks per bucket or running window
- `docs/api/aggregations/high_time.md` — **otp.agg.high_time** — Returns the timestamp of the tick with the highest input value; use it to locate when a peak occurred within each bucket or group
- `docs/api/aggregations/implied_vol.md` — **otp.agg.implied_vol** — Computes implied volatility for each bucket’s last tick from Black-Scholes inputs when you need option volatility aggregation
- `docs/api/aggregations/last.md` — **otp.agg.last** — Returns the last value of a column, used when you need the most recent tick value in each bucket or running window
- `docs/api/aggregations/last_tick.md` — **otp.agg.last_tick** — Selects the last n ticks from each bucket or running window when you need the most recent event(s) per interval
- `docs/api/aggregations/last_time.md` — **otp.agg.last_time** — Returns the timestamp of the last tick in each bucket; use it when you need the most recent event time per aggregation window
- `docs/api/aggregations/linear_regression.md` — **otp.agg.linear_regression** — Computes slope and intercept for Y versus X within each bucket when you need linear trend parameters from tick fields
- `docs/api/aggregations/low_tick.md` — **otp.agg.low_tick** — Selects the n lowest-valued ticks by a column, used when you need the smallest observations per bucket or running window
- `docs/api/aggregations/low_time.md` — **otp.agg.low_time** — Returns the timestamp of the tick with the lowest input value; use it when you need the time of the minimum observation
- `docs/api/aggregations/max.md` — **otp.agg.max** — Returns the maximum value of a column or operation; use it when you need bucketed, running, or grouped maxima
- `docs/api/aggregations/mean.md` — **otp.agg.mean** — Computes the average of one or more fields; use it when you need the mean aggregation result from grouped ticks
- `docs/api/aggregations/median.md` — **otp.agg.median** — Computes the median of a column or operation over each bucket or sliding window when you need robust central tendency aggregation
- `docs/api/aggregations/min.md` — **otp.agg.min** — Returns the minimum value of a column or operation; use it when you need the lowest value per bucket, running window, or group
- `docs/api/aggregations/multi_portfolio_price.md` — **otp.agg.multi_portfolio_price** — Computes weighted portfolio price per bucket across multiple portfolios; use when you need portfolio-valued aggregation from portfolio-symbol mappings
- `docs/api/aggregations/num_distinct.md` — **otp.agg.num_distinct** — Counts distinct values across specified key fields, used when you need unique-value counts per bucket or sliding window
- `docs/api/aggregations/ob_num_levels.md` — **otp.agg.ob_num_levels** — Returns the number of order-book levels at each bucket end; use it to measure ASK, BID, or both sides over time
- `docs/api/aggregations/ob_size.md` — **otp.agg.ob_size** — Returns total order-book size across selected levels at each bucket end; use it when you need depth by side, grouping, or custom bucketing
- `docs/api/aggregations/ob_snapshot.md` — **otp.agg.ob_snapshot** — Returns order book state at each bucket end, including price, size, side, and last update time for selected levels
- `docs/api/aggregations/ob_snapshot_flat.md` — **otp.agg.ob_snapshot_flat** — Returns one tick per bucket containing flattened order-book level snapshots; use it to compute book depth over time with optional grouping and bucket controls
- `docs/api/aggregations/ob_snapshot_wide.md` — **otp.agg.ob_snapshot_wide** — Returns side-by-side order book levels at each bucket end, with price, size, and last update time when you need book snapshots
- `docs/api/aggregations/ob_summary.md` — **otp.agg.ob_summary** — Computes order-book summary stats like VWAP, best and worst price, total size, and level count when you need book-level aggregation
- `docs/api/aggregations/ob_vwap.md` — **otp.agg.ob_vwap** — Returns size-weighted order book price across selected levels at each bucket end; use when you need VWAP from book depth
- `docs/api/aggregations/option_price.md` — **otp.agg.option_price** — Computes option price and optional Greeks with Black-Scholes or Cox-Ross-Rubinstein when ticks carry option inputs and expiration data
- `docs/api/aggregations/partition_evenly_into_groups.md` — **otp.agg.partition_evenly_into_groups** — Partitions ticks into a specified number of groups so each group’s weight sum is as balanced as possible; use when you need weighted bucket assignment by a field
- `docs/api/aggregations/percentile.md` — **otp.agg.percentile** — Computes running quantiles per bucket from selected numeric fields, with optional grouping and flexible bucket boundaries when you need percentile-style aggregation
- `docs/api/aggregations/portfolio_price.md` — **otp.agg.portfolio_price** — Computes weighted portfolio price per bucket when you need bucketed price aggregation across symbols with optional grouping and flexible bucket boundaries
- `docs/api/aggregations/ranking.md` — **otp.agg.ranking** — Computes a running rank or percentile for ticks within each bucket, when you need order statistics without reordering the stream
- `docs/api/aggregations/return_ep.md` — **otp.agg.return_ep** — Computes the return ratio between bucket end and start prices, or running return from query start when you need interval performance
- `docs/api/aggregations/root.md` — **Aggregations** — Explains aggregation bucket creation, running windows, and all_fields behavior when choosing bucket_interval and output rows for agg queries
- `docs/api/aggregations/standardized_moment.md` — **otp.agg.standardized_moment** — Computes the standardized moment of a column to degree k over each bucket; reach for skewness- or higher-moment-style aggregation
- `docs/api/aggregations/stddev.md` — **otp.agg.stddev** — Computes standard deviation over a column or operation, with running windows, fixed buckets, grouping, and flexible bucket-end conditions
- `docs/api/aggregations/sum.md` — **otp.agg.sum** — Computes the sum of a column or per-tick operation, used for bucketed, running, or grouped aggregations
- `docs/api/aggregations/tw_average.md` — **otp.agg.tw_average** — Computes time-weighted average of a column or operation; use it when averaging values by elapsed time across buckets or running windows
- `docs/api/aggregations/variance.md` — **otp.agg.variance** — Computes biased or unbiased variance over a column or operation, when you need bucketed or running dispersion aggregation
- `docs/api/aggregations/vwap.md` — **otp.agg.vwap** — Returns volume weighted average price from price and size columns, used when you need VWAP over buckets or running windows

### API — data inspection (databases, dates, tick types, schema)

- `docs/api/data_inspection/databases.md` — **otp.databases** — Returns visible database names and DB objects, or a DataFrame of database metadata, when you need to inspect available databases
- `docs/api/data_inspection/db.md` — **otp.inspection.DB** — Represents a database returned by otp.databases(), exposing access info, configuration, available dates, and ACL-aware date bounds
- `docs/api/data_inspection/derived_databases.md` — **otp.derived_databases** — Returns available derived databases as DB objects or a DataFrame, when you need to list all, current, or child derived databases
- `docs/api/data_inspection/root.md` — **Data Inspection**

### API — datetime helpers (otp.dt, otp.datetime, date ranges)

- `docs/api/datetime/date.md` — **otp.date** — Represents a date value for query start/end times and Source column operations, when you need a OneTick-compatible date object
- `docs/api/datetime/dt.md` — **otp.dt (otp.datetime)** — Represents a timestamp with nanosecond precision, used for query bounds, column operations, parsing, replacement, and datetime arithmetic
- `docs/api/datetime/now.md` — **otp.now** — Returns the current GMT time in milliseconds since the UNIX epoch when you need a timestamp for a field or expression
- `docs/api/datetime/root.md` — **Datetime**
- `docs/api/datetime/timedelta.md` — **otp.timedelta** — Represents a timestamp delta and converts from strings, integers, pandas or datetime timedeltas, or keyword offsets when you need to add or compare time spans

### API — time offsets (otp.Nano/Milli/Second/Minute/Hour/Day/Month/Year)

- `docs/api/datetime/offsets/day.md` — **otp.Day** — Represents a day datetime offset added to or subtracted from otp.datetime values and datetime columns, or used to compute day differences
- `docs/api/datetime/offsets/hour.md` — **otp.Hour** — Represents an hour datetime offset; add or subtract it from otp.datetime values or datetime columns, or convert datetime differences to hours
- `docs/api/datetime/offsets/milli.md` — **otp.Milli** — Represents a millisecond datetime offset you add or subtract from otp.datetime values or datetime columns, or use to convert datetime differences into milliseconds
- `docs/api/datetime/offsets/minute.md` — **otp.Minute** — Represents a minute datetime offset you add or subtract from otp.datetime values or datetime columns, including datetime-difference expressions
- `docs/api/datetime/offsets/month.md` — **otp.Month** — Represents a month datetime offset you add or subtract from otp.datetime values or datetime columns, or derive month differences from date subtraction
- `docs/api/datetime/offsets/nano.md` — **otp.Nano** — Represents a nanosecond datetime offset you add or subtract from otp.datetime values or datetime columns, or wrap a datetime difference
- `docs/api/datetime/offsets/quarter.md` — **otp.Quarter** — Represents a quarter datetime offset you add or subtract from otp.datetime values or datetime columns, or derive from date differences
- `docs/api/datetime/offsets/root.md` — **Datetime offsets** — Objects representing datetime offsets that can be added to or subtracted from otp.datetime values or datetime Source columns
- `docs/api/datetime/offsets/second.md` — **otp.Second** — Creates a second-based datetime offset you add or subtract from otp.datetime values or datetime columns, including fractional seconds and datetime differences
- `docs/api/datetime/offsets/week.md` — **otp.Week** — Represents a week datetime offset that adds or subtracts whole weeks from otp.datetime values or datetime columns
- `docs/api/datetime/offsets/year.md` — **otp.Year** — Represents a year datetime offset you add or subtract from otp.datetime values or datetime columns, or derive from datetime differences

### API — functions (otp.merge, otp.join, otp.join_by_time, otp.apply, ...)

- `docs/api/functions/coalesce.md` — **otp.coalesce** — Merges multiple sources into one stream, filling gaps and preferring higher-priority ticks when timestamps match within max_source_delay
- `docs/api/functions/cut.md` — **otp.cut** — Bins numeric column values into discrete intervals and returns an assignable object when you need pandas.cut-style bucketing
- `docs/api/functions/format.md` — **otp.format** — Formats strings with positional or keyword values, float precision, or datetime patterns when building readable string columns
- `docs/api/functions/join.md` — **otp.join** — Joins two sources by key, condition, or size; use it when combining left and right streams with inner or left outer matching
- `docs/api/functions/join_by_time.md` — **otp.join_by_time** — Joins multiple sources by tick timestamps, with optional inner/outer behavior, leader selection, and same-time matching when aligning streams
- `docs/api/functions/join_with_aggregated_window.md` — **otp.join_with_aggregated_window** — Computes aggregations on agg_src and joins them to each pass_src tick; use it for windowed joins with boundary and delay control
- `docs/api/functions/merge.md` — **otp.merge** — Merges multiple sources into one timestamp-ordered time series, with options for symbol expansion, schema alignment, and tie ordering
- `docs/api/functions/oqd.md` — **otp.corp_actions** — Returns a source with corporate-action adjustments applied to selected fields; use it to back-adjust prices or sizes by action type and date
- `docs/api/functions/qcut.md` — **otp.qcut** — Discretizes a numeric column into quantile bins, returning assignable bin labels or interval strings when you need percentile-based bucketing
- `docs/api/functions/root.md` — **Functions**
- `docs/api/functions/save_sources_to_single_file.md` — **otp.functions.save_sources_to_single_file** — Saves one or more otp.Source queries into a single .otq file, when you need named queries, per-source time bounds, symbols, or query properties

### API — query caching functions

- `docs/api/functions/cache/create_cache.md` — **otp.create_cache** — Reserves a cache name for a query, then lets ReadCache populate it on first access when you need session or server-side caching
- `docs/api/functions/cache/delete_cache.md` — **otp.delete_cache** — Deletes a named cache, optionally across all symbols, time intervals, or matching OTQ parameters, when you need to remove cached results
- `docs/api/functions/cache/modify_cache_config.md` — **otp.modify_cache_config** — Modifies a cache configuration parameter such as granularity, timezone, or access flags when you need to update an existing cache
- `docs/api/functions/cache/root.md` — **Cache related functions**

### API — math functions

- `docs/api/functions/math/acos.md` — **otp.math.acos** — Returns the inverse cosine (arccos) of a numeric value or expression when you need an angle from a cosine input
- `docs/api/functions/math/acot.md` — **otp.math.acot** — Returns the inverse cotangent of a value as an Operation; use it when you need arccot or the alias otp.math.arccot
- `docs/api/functions/math/asin.md` — **otp.math.asin** — Returns the inverse sine (arcsin) of a value; use it when you need an angle from a sine input, with arcsin as an alias
- `docs/api/functions/math/atan.md` — **otp.math.atan** — Returns the inverse tangent of a value as an Operation; use it when you need arctan or the otp.math.arctan alias
- `docs/api/functions/math/ceil.md` — **otp.math.ceil** — Returns the smallest float greater than or equal to a value; use it to round numbers, columns, or operations upward
- `docs/api/functions/math/cos.md` — **otp.math.cos** — Returns the cosine of a radian value; use it when you need a trigonometric transform of a number, column, or operation
- `docs/api/functions/math/cot.md` — **otp.math.cot** — Returns the cotangent of a radian value as an Operation; use it when you need cot(value) from a scalar, Column, or Operation
- `docs/api/functions/math/div.md` — **otp.math.div** — Computes the quotient of value1 divided by value2; use when you need a division Operation from scalars or columns
- `docs/api/functions/math/exp.md` — **otp.math.exp** — Computes the natural exponent of a value and returns an Operation when you need e raised to a constant, column, or expression
- `docs/api/functions/math/floor.md` — **otp.math.floor** — Returns the greatest float less than or equal to a value; use it to round numbers down, including Operation or Column inputs
- `docs/api/functions/math/frand.md` — **otp.math.frand** — Returns a pseudo-random Operation between min_value and max_value, and use it when you need generated values with optional deterministic seeding
- `docs/api/functions/math/gcd.md` — **otp.math.gcd** — Computes the greatest common divisor of two values and returns an Operation when you need integer factor reduction or divisibility checks
- `docs/api/functions/math/ln.md` — **otp.math.ln** — Computes the natural logarithm of a numeric value or expression; use it when you need log-transformed columns or constants
- `docs/api/functions/math/log10.md` — **otp.math.log10** — Computes the base-10 logarithm of a value when you need a log10 transform as an Operation
- `docs/api/functions/math/max.md` — **otp.math.max** — Returns the maximum of same-typed values or expressions; use it to compare scalars, Columns, or Operations and emit the larger one
- `docs/api/functions/math/min.md` — **otp.math.min** — Returns the minimum of same-typed values or expressions; use it to pick the smallest number or column result among inputs
- `docs/api/functions/math/mod.md` — **otp.math.mod** — Computes the remainder of value1 divided by value2; use it when you need modulo arithmetic as an Operation
- `docs/api/functions/math/pi.md` — **otp.math.pi** — Returns the constant pi as an Operation when you need a numeric pi value in an expression or computed field
- `docs/api/functions/math/pow.md` — **otp.math.pow** — Computes base raised to exponent and returns an Operation when you need per-row power calculations from constants, Columns, or Operations
- `docs/api/functions/math/rand.md` — **otp.math.rand** — Returns a pseudo-random integer between min_value and max_value inclusive, with optional seed for repeatable query results
- `docs/api/functions/math/root.md` — **Math functions** — Lists math-related functions and when you need numeric transformations beyond basic arithmetic
- `docs/api/functions/math/round.md` — **otp.math.round** — Rounds a value to a chosen precision and tie-breaking method, used when you need decimal or digit-place rounding with control over half-way cases
- `docs/api/functions/math/sign.md` — **otp.math.sign** — Returns the sign of a value as 1, 0, or -1; use it when you need to classify positive, zero, or negative inputs
- `docs/api/functions/math/sin.md` — **otp.math.sin** — Returns the sine of a value in radians; use it when you need trigonometric sine on a number, Column, or Operation
- `docs/api/functions/math/sqrt.md` — **otp.math.sqrt** — Computes the square root of a numeric value or expression and use it when you need a derived square-root column or operation
- `docs/api/functions/math/tan.md` — **otp.math.tan** — Returns the tangent of a radian value as an Operation; use it when you need tan for a constant, column, or expression

### API — misc functions

- `docs/api/functions/misc/bit_and.md` — **otp.misc.bit_and** — Computes the bitwise AND of two integers, Columns, or Operations when you need to mask or combine flag bits
- `docs/api/functions/misc/bit_at.md` — **otp.misc.bit_at** — Returns the bit at a zero-based position from the end of an integer value; use it to extract a specific binary flag from a value, column, or operation
- `docs/api/functions/misc/bit_not.md` — **otp.misc.bit_not** — Computes the bitwise NOT of an int, Column, or Operation when you need to invert all bits of a value
- `docs/api/functions/misc/bit_or.md` — **otp.misc.bit_or** — Performs bitwise OR on two ints, Columns, or Operations when you need to combine corresponding bits into one result
- `docs/api/functions/misc/bit_xor.md` — **otp.misc.bit_xor** — Computes the bitwise XOR of two ints, Columns, or Operations when you need to combine corresponding bits into a new Operation
- `docs/api/functions/misc/get_onetick_version.md` — **otp.misc.get_onetick_version** — Returns the OneTick build string from the executing server when you need to stamp results or verify server version
- `docs/api/functions/misc/get_symbology_mapping.md` — **otp.misc.get_symbology_mapping** — Translates a symbol into a destination symbology string, used when you need the mapped identifier for a given source symbol and date
- `docs/api/functions/misc/get_username.md` — **otp.misc.get_username** — Returns the authenticated username string for the current query, used when you need to stamp results with the executing user
- `docs/api/functions/misc/hash_code.md` — **otp.misc.hash_code** — Returns a hexadecimal hash string for a value using a chosen hash algorithm when you need SHA, lookup3, metro/city/murmur, or byte-sum hashing
- `docs/api/functions/misc/root.md` — **Miscellaneous functions**

### API — misc (otp.Symbols, eval, math, logging, ...)

- `docs/api/misc/__build__.md` — **otp.__build__** — Returns the current OneTick build number string when you need to check the installed build version
- `docs/api/misc/__main_one_tick_dir__.md` — **otp.__main_one_tick_dir__** — Returns the OneTick installation root directory path when you need to locate the main OneTick install on disk
- `docs/api/misc/__version__.md` — **otp.__version__** — Returns the current onetick.py version string when you need to check the installed package release
- `docs/api/misc/adaptive.md` — **otp.adaptive** — Sentinel default value for parameters whose meaning depends on other arguments, config, or context; use when None is ambiguous or deferred
- `docs/api/misc/by_symbol.md` — **otp.by_symbol** — Creates a separate series for each unique symbol_field value from src output; use it to split one source query into per-symbol queries
- `docs/api/misc/callback.md` — **otp.CallbackBase** — Base class for run() callbacks that receive ticks, metadata, sorting, and error notifications; subclass it to handle query results
- `docs/api/misc/default_by_type.md` — **otp.default_by_type** — Returns the default value for a OneTick base type when you need a zero, empty string, or typed null-like initializer
- `docs/api/misc/eval.md` — **otp.eval** — Creates a saved subquery or dynamic value from a query/function, used to supply symbols, filters, or parameterized query results later
- `docs/api/misc/expr.md` — **otp.expr** — Lets an EP parameter take an expression evaluated before parameters are passed to event processors; use when a parameter must be computed dynamically
- `docs/api/misc/meta_fields.md` — **otp.meta_fields** — Provides pseudo-columns like Time, _START_TIME, _TIMEZONE, and _DBNAME for direct access or Expr references when building ticks
- `docs/api/misc/multi_output_source.md` — **otp.MultiOutputSource** — Wraps several connected Source branches into one multi-output source, used when running or saving graphs with named result branches
- `docs/api/misc/oneticklib.md` — **otp.OneTickLib** — Singleton wrapper for otq.OneTickLib initialization, logging, auth token setup, config overrides, and cleanup when managing the library lifecycle
- `docs/api/misc/param.md` — **otp.param** — Creates a parameter placeholder Operation for query_params, used when a field, argument, or aggregation setting must be supplied at run time
- `docs/api/misc/query.md` — **otp.query** — Constructs a query from an .otq path with parameters and output-column config, then apply or inspect its output pins
- `docs/api/misc/query_parameters.md` — **otp.QueryParameters** — Holds per-query settings like symbol_date, concurrency, batch_size, running, and query_properties when constructing or overriding a query
- `docs/api/misc/range.md` — **otp.range** — Represents a start/stop range object for split() conditions when you need to route values by interval
- `docs/api/misc/raw.md` — **otp.raw** — Wraps a raw OneTick expression with a declared dtype when you need to embed literal OneTick syntax such as _TIMEZONE
- `docs/api/misc/remote.md` — **otp.remote** — Decorator that preserves a function’s source for use inside apply() when running Remote OTP with Ray
- `docs/api/misc/render.md` — **otp.utils.render_otq** — Renders one or more .otq queries to an image path, when you need a Graphviz view of query structure from files
- `docs/api/misc/root.md` — **Misc**
- `docs/api/misc/sql.md` — **otp.SqlQuery** — Constructs a SQL query source from a SQL statement, used when you need OneTick to execute SQL joins, filters, or aggregates directly
- `docs/api/misc/symbol_param.md` — **Symbol Parameters Objects** — Describes internal read-only symbol-parameter containers returned by onetick.py methods, used when accessing or passing symbol parameter columns
- `docs/api/misc/tmp_dir.md` — **otp.utils.TmpDir** — Creates a temporary directory path that auto-deletes on process exit, when you need a writable scratch directory for generated files or test artifacts
- `docs/api/misc/tmp_file.md` — **otp.utils.TmpFile** — Creates a temporary file path that auto-deletes on exit, when you need a writable scratch file with optional name, suffix, and directory control

### API — column operations (otp.Column / _Operation: arithmetic, casts, ...)

- `docs/api/operation/__getitem__.md` — **otp.Column.__getitem__** — Returns a tick-shifted column value from past, current, or future rows; use it to reference neighboring ticks by integer offset
- `docs/api/operation/apply.md` — **otp.Operation.apply** — Converts a column by type-casting or by translating a Python callable into a CASE-like expression when deriving new values
- `docs/api/operation/astype.md` — **otp.Operaion.astype** — Casts an operation result to a target type via apply(); reach for it when you need string, int, or float conversion before further expressions
- `docs/api/operation/cumsum.md` — **otp.Column.cumsum** — Returns the cumulative sum of a column; use it when creating or updating a column from running totals
- `docs/api/operation/dtype.md` — **otp.Operation.dtype** — Returns the Python type of a column or operation result; inspect it when you need to confirm a field’s inferred datatype
- `docs/api/operation/expr.md` — **otp.Operation.expr** — Returns the expression used in EP parameters when you need to inspect or reuse an operation’s configured expression
- `docs/api/operation/fillna.md` — **otp.Operation.fillna** — Fills nan values with a constant or another Operation, used when you need to replace missing tick values inline
- `docs/api/operation/isin.md` — **otp.Operation.isin** — Returns 1.0 when a column value matches any listed item, or 0.0 otherwise; use it for membership tests and filters
- `docs/api/operation/map.md` — **otp.Operation.map** — Maps column values to replacement values from a dict, with a fallback default when no key matches
- `docs/api/operation/root.md` — **otp.Operation** — Represents a column expression container produced by operations on Column or other Operation objects, then reused in assignments or function arguments
- `docs/api/operation/round.md` — **otp.Operation.round** — Rounds an input column to a specified decimal or integer precision, with half-way values rounded up; use it when you need controlled numeric rounding

### API — decimal column operations

- `docs/api/operation/decimal/cmp.md` — **otp.Operation.decimal.cmp** — Compares two decimal values with absolute and relative epsilon, returning 0, 1, or -1 when you need tolerant ordering or equality
- `docs/api/operation/decimal/root.md` — **Operation.decimal** — Provides decimal-specific methods on an Operation when the underlying values are decimal and you need decimal-only accessors
- `docs/api/operation/decimal/str.md` — **otp.Operation.decimal.str** — Converts a decimal operation to a string operation with fixed fractional precision when you need formatted decimal output

### API — datetime column operations (.dt.*)

- `docs/api/operation/dt/date.md` — **otp.Operation.dt.date** — Returns a date-only nsectime operation from datetimes; use it when you need to strip time components from a timestamp field
- `docs/api/operation/dt/date_trunc.md` — **otp.Operation.dt.date_trunc** — Truncates a datetime to year through nanosecond precision; use it when you need aligned timestamps at a chosen granularity
- `docs/api/operation/dt/day_name.md` — **otp.Operation.dt.day_name** — Returns the weekday name for each datetime value; use it when you need Monday–Sunday labels, optionally in a specified timezone
- `docs/api/operation/dt/day_of_month.md` — **otp.Operation.dt.day_of_month** — Returns the day number within a month from a datetime; use it when you need month-day extraction with an optional timezone
- `docs/api/operation/dt/day_of_week.md` — **otp.Operation.dt.day_of_week** — Returns the weekday number for each timestamp, using a chosen week start and optional timezone when you need ISO-style day labels
- `docs/api/operation/dt/day_of_year.md` — **otp.Operation.dt.day_of_year** — Returns the day number within the year from a datetime; use it when you need a 1–366 calendar ordinal, optionally in a specified timezone
- `docs/api/operation/dt/hour.md` — **otp.Operation.dt.hour** — Returns the hour component from a datetime operation, using an optional timezone when extracting local hour values
- `docs/api/operation/dt/minute.md` — **otp.Operation.dt.minute** — Returns the minute component of a datetime; use it when you need minute-of-hour extraction, optionally in a specified timezone
- `docs/api/operation/dt/month.md` — **otp.Operation.dt.month** — Returns the month number from a datetime operation, using an optional timezone when extracting month values from timestamps
- `docs/api/operation/dt/month_name.md` — **otp.Operation.dt.month_name** — Returns the month name abbreviation for a datetime operation, used when you need month labels like Mar or Oct with an optional timezone
- `docs/api/operation/dt/quarter.md` — **otp.Operation.dt.quarter** — Returns the quarter number from a datetime operation, using an optional timezone when extracting quarter values from timestamps
- `docs/api/operation/dt/root.md` — **Operation.dt** — Provides datetime-specific methods on an Operation when the underlying values are datetime-like and you need datetime accessors
- `docs/api/operation/dt/second.md` — **otp.Operation.dt.second** — Returns the second component of a datetime, optionally using a timezone argument when extracting from a timestamp column or operation
- `docs/api/operation/dt/strftime.md` — **otp.Operation.dt.strftime** — Formats a datetime nanoseconds value into a string using strftime tokens and an optional timezone; use it when you need date/time text output
- `docs/api/operation/dt/week.md` — **otp.Operation.dt.week** — Returns the week number from a datetime operation, using an optional timezone string, operation, or column when extracting calendar week values
- `docs/api/operation/dt/year.md` — **otp.Operation.dt.year** — Returns the year from a datetime operation, optionally using a timezone string, operation, or column when extracting calendar year values

### API — float column operations

- `docs/api/operation/float/cmp.md` — **otp.Operation.float.cmp** — Compares two float values with absolute and relative epsilon, returning -1, 0, or 1 when ordering or equality checks need tolerance
- `docs/api/operation/float/eq.md` — **otp.Operation.float.eq** — Compares two double values with relative tolerance, returning an Operation when you need approximate equality via abs(column - other) <= delta
- `docs/api/operation/float/root.md` — **Operation.float** — Provides float-specific methods on an Operation when the underlying value is a float
- `docs/api/operation/float/str.md` — **otp.Operation.float.str** — Converts a float to a string with specified length and precision; use it when formatting numeric columns or literals for output

### API — math column operations

- `docs/api/operation/math/abs.md` — **onetick.py.Operation.__abs__** — Returns the absolute value of a float or int column when you need to remove negative signs from numeric fields
- `docs/api/operation/math/add.md` — **onetick.py.Operation.__add__** — Returns the sum of a column and another value; use it when adding numbers, strings, offsets, or another column in expressions
- `docs/api/operation/math/and.md` — **onetick.py.Operation.__and__** — Returns a logical AND filter expression when combining two conditions with & in where or other filter operations
- `docs/api/operation/math/eq.md` — **onetick.py.Operation.__eq__** — Returns a filter operation testing equality to another value, used when building where clauses like t['A'] == 1
- `docs/api/operation/math/ge.md` — **onetick.py.Operation.__ge__** — Returns a >= filter expression for comparing an operation to another value when building where clauses or boolean conditions
- `docs/api/operation/math/gt.md` — **onetick.py.Operation.__gt__** — Returns a greater-than filter expression; use it when comparing an Operation to another value inside where clauses
- `docs/api/operation/math/invert.md` — **onetick.py.Operation.__invert__** — Returns the logical inversion of a filter operation; use it to negate a condition inside where() expressions
- `docs/api/operation/math/le.md` — **onetick.py.Operation.__le__** — Returns a <= filter operation, used when comparing an operation to another value or expression in a where clause
- `docs/api/operation/math/lt.md` — **onetick.py.Operation.__lt__** — Returns a less-than filter operation; use it when comparing an Operation to another value or expression with <
- `docs/api/operation/math/mod.md` — **onetick.py.Operation.__mod__** — Returns the modulo of an integer column by another int or Column; use it when building `%` expressions in tick pipelines
- `docs/api/operation/math/mul.md` — **onetick.py.Operation.__mul__** — Multiplies a column by a scalar, string, or another column when you need elementwise scaling or string repetition in expressions
- `docs/api/operation/math/ne.md` — **onetick.py.Operation.__ne__** — Returns a filter operation testing inequality against other, used when you need rows where an expression is not equal to a value
- `docs/api/operation/math/neg.md` — **onetick.py.Operation.__neg__** — Returns the negated value of a float or int column when you need unary minus on an Operation
- `docs/api/operation/math/or.md` — **onetick.py.Operation.__or__** — Returns a logical OR filter expression, used to combine conditions with | inside where clauses
- `docs/api/operation/math/radd.md` — **onetick.py.Operation.__radd__** — Returns the result of right-hand addition when an Operation appears on the right side of +, such as scalar or field + operation expressions
- `docs/api/operation/math/rmul.md` — **onetick.py.Operation.__rmul__** — Returns the result of right-side multiplication; use it when a scalar or other operand appears on the left of an Operation
- `docs/api/operation/math/root.md` — **Math operations**
- `docs/api/operation/math/round.md` — **onetick.py.Operation.__round__** — Rounds an Operation to a specified precision, with half-way values rounded up; use it when you need rounded tick expressions
- `docs/api/operation/math/rsub.md` — **onetick.py.Operation.__rsub__** — Computes the reflected subtraction result when an Operation appears on the right of `-`; use it for expressions like `other - operation`
- `docs/api/operation/math/rtruediv.md` — **onetick.py.Operation.__rtruediv__** — Returns the result of dividing another value by an Operation, used when the left operand does not implement division
- `docs/api/operation/math/sub.md` — **onetick.py.Operation.__sub__** — Subtracts a scalar, offset, or column from an operation result; use it when building expressions like t['A'] - t['B'] or shifting datetimes by otp.Day(1)
- `docs/api/operation/math/truediv.md` — **onetick.py.Operation.__truediv__** — Divides a column by a scalar or another column and returns the quotient when writing expressions like t['A'] / t['B'] or t['B'] / 2

### API — string column operations (.str.*)

- `docs/api/operation/str/concat.md` — **otp.Operation.str.concat** — Returns a concatenated string from this value and a string, Column, or Operation when building combined text fields
- `docs/api/operation/str/contains.md` — **otp.Operation.str.contains** — Returns a boolean Operation indicating whether a string contains a substring; use it for substring tests or filtering rows, not regex
- `docs/api/operation/str/endswith.md` — **otp.Operation.str.endswith** — Returns a boolean-like match for whether each string ends with the given value; use it to filter or flag suffix matches against constants, columns, or operations
- `docs/api/operation/str/extract.md` — **otp.Operation.str.extract** — Returns the first regex match, optionally rewritten with capture groups and case modifiers; use it to pull or reformat substrings
- `docs/api/operation/str/find.md` — **otp.Operation.str.find** — Returns the starting index of a substring or -1 when absent; use it to locate text within string columns with optional start offset
- `docs/api/operation/str/first.md` — **otp.Operation.str.first** — Returns the first count symbols from a string column or expression, used to extract leading characters from each value
- `docs/api/operation/str/get.md` — **otp.Operation.str.get** — Returns the character at a 0-based position, or empty string past the end; use it to extract one character by constant or computed index
- `docs/api/operation/str/ilike.md` — **otp.Operation.str.ilike** — Returns a case-insensitive SQL-like pattern match boolean, used to test strings with % and _ wildcards or filter ticks by text
- `docs/api/operation/str/insert.md` — **otp.Operation.str.insert** — Returns a string with length characters deleted at start and value inserted there; use it to splice or replace substrings
- `docs/api/operation/str/last.md` — **otp.Operation.str.last** — Returns the last count characters from each string, or the whole string when count is 1; use it to trim suffixes or extract trailing text
- `docs/api/operation/str/len.md` — **otp.Operation.str.len** — Returns the string length, or the position of the first null byte if present; use it when you need per-row string length from an Operation
- `docs/api/operation/str/like.md` — **otp.Operation.str.like** — Returns True when a string matches an SQL-like pattern with % and _ wildcards; use it for pattern filtering or tick selection
- `docs/api/operation/str/lower.md` — **otp.Operation.str.lower** — Converts each string value to lowercase and returns a string Operation when normalizing text fields before comparison or display
- `docs/api/operation/str/ltrim.md` — **otp.Operation.str.ltrim** — Removes leading whitespace from a string operation when you need left-trimmed text
- `docs/api/operation/str/match.md` — **otp.Operation.str.match** — Returns a boolean Operation that tests whether text matches a POSIX extended regular expression, used for regex-based columns or filters
- `docs/api/operation/str/regex_replace.md` — **otp.Operation.str.regex_replace** — Returns a string with regex matches replaced by a replacement pattern; use it to rewrite substrings, optionally all matches or case-insensitively
- `docs/api/operation/str/repeat.md` — **otp.Operation.str.repeat** — Duplicates a string a non-negative number of times, returning repeated text when you need per-row string expansion or multiplication-like behavior
- `docs/api/operation/str/replace.md` — **otp.Operation.str.replace** — Replaces occurrences of a pattern with a replacement string; use it to rewrite string columns from literals or other columns
- `docs/api/operation/str/root.md` — **Operation.str** — Provides string-specific methods on an Operation when the underlying value is text
- `docs/api/operation/str/rtrim.md` — **otp.Operation.str.rtrim** — Removes trailing whitespace from a string operation when you need right-side trimming only
- `docs/api/operation/str/slice.md` — **otp.Operation.str.slice** — Returns a sliced string from start and stop positions, including negative offsets and column or operation inputs, when extracting substrings
- `docs/api/operation/str/startswith.md` — **otp.Operation.str.startswith** — Returns whether each string starts with a given prefix; use it to compare a string column or operation against a constant, column, or operation
- `docs/api/operation/str/strptime.md` — **otp.Operation.str.strptime** — Converts formatted date-time strings to nanosecond timestamps, or parses millisecond/ns epoch strings when unit is set
- `docs/api/operation/str/substr.md` — **otp.Operation.str.substr** — Returns a substring starting at a given index, with optional length and right-trimming when you need fixed or tail-based slices
- `docs/api/operation/str/to_datetime.md` — **otp.Operation.str.to_datetime** — Converts a formatted string to nanosecond datetime; use it to parse timestamps with custom format, timezone, or ms/ns epoch strings
- `docs/api/operation/str/token.md` — **otp.Operation.str.token** — Returns the token at zero-based position n after splitting a string by sep, or empty string when the index is out of range
- `docs/api/operation/str/trim.md` — **otp.Operation.str.trim** — Removes whitespace from both ends of a string Operation when you need to clean padded text fields before further processing
- `docs/api/operation/str/upper.md` — **otp.Operation.str.upper** — Converts each string to uppercase when you need normalized text values or case-insensitive comparisons

### API — OneTick Query Designer / OQD interop

- `docs/api/oqd/corporate_actions.md` — **otp.oqd.CorporateActions** — Returns corporate action records for a symbol over a date range, with each row timestamped at the EX-Date when you need dividends, splits, or similar events
- `docs/api/oqd/descriptive_fields.md` — **otp.oqd.DescriptiveFields** — Returns a time series of symbol descriptive-field changes, with OID, END_DATE, and metadata columns, when you need corporate or fund reference history
- `docs/api/oqd/ohlcv.md` — **otp.oqd.OHLCV** — Retrieves daily unadjusted OHLCV price ticks for a symbol from a chosen pricing exchange when you need OPEN, HIGH, LOW, CLOSE, VOLUME, CURRENCY, and EXCH fields
- `docs/api/oqd/root.md` — **OneQuantData™ (OQD)** — Describes OQD reference and pricing databases, required setup, supported source classes, and direct OQD database access examples
- `docs/api/oqd/shares_outstanding.md` — **otp.oqd.SharesOutstanding** — Retrieves a stock’s total shares outstanding time series, used when you need published outstanding-share history for a security

### API — session / config / locator / databases

- `docs/api/session/acl.md` — **otp.session.ACL** — Represents a OneTick database access list file and use it when you need to point a session at a custom ACL or temporary ACL file
- `docs/api/session/config.md` — **otp.session.Config** — Creates or wraps a session config with custom config, locator, ACL, paths, license, and variables when you need to control session setup
- `docs/api/session/db.md` — **otp.DB** — Creates or wraps a local OneTick database, adds source data, and configures locator/ACL entries when using Session.use
- `docs/api/session/fault_tolerance.md` — **otp.FaultTolerance** — Configures prioritized primary and backup tick-server sockets for RemoteTS, used when you need failover across servers or load-balancing groups
- `docs/api/session/load_balancing.md` — **otp.LoadBalancing** — Configures client-side load balancing across multiple tick servers, used when creating RemoteTS or FaultTolerance connections
- `docs/api/session/locator.md` — **otp.session.Locator** — Represents a database locator file and lets you create temporary, copied, or empty locators when configuring database access
- `docs/api/session/ref_db.md` — **otp.RefDB** — Creates a reference database and loads reference sections when you need symbol history or other lookup data for a continuous archive
- `docs/api/session/remote_ts.md` — **otp.RemoteTS** — Represents a remote tick-server connection target, used when configuring Session or local databases with host, port, protocol, resource, or load-balancing settings
- `docs/api/session/root.md` — **Session**
- `docs/api/session/session.md` — **otp.Session** — Creates and manages a OneTick session, including config files, environment setup, cleanup, and optional performance metrics when you need scoped access
- `docs/api/session/test_session.md` — **otp.TestSession** — Session subclass that predefines default otp.config values for timezone, database, symbol, and date range when you want demo-ready defaults

### API — Source methods (the core query object: agg, join, where, script, ...)

- `docs/api/source/Symbol.md` — **otp.Source.Symbol** — Provides access to the current symbol’s name and parameters; use it when joining symbol metadata into tick pipelines
- `docs/api/source/__call__.md` — **otp.Source.__call__** — Runs a Source object with the given arguments; reach for it only when maintaining older code, since it is deprecated in favor of otp.run
- `docs/api/source/__getitem__.md` — **otp.Source.__getitem__** — Returns a column, filtered sources, or a reordered subset when indexing by name, expression, eval, list, or slice
- `docs/api/source/__setitem__.md` — **otp.Source.__setitem__** — Adds or updates a source column by assignment; use it when setting constants, copying columns, or building derived fields with [] syntax
- `docs/api/source/add_fields.md` — **otp.Source.add_fields** — Adds new columns to a Source from a field-value dict, or overwrites existing fields when override=True; use when constructing derived columns
- `docs/api/source/add_prefix.md` — **otp.Source.add_prefix** — Adds a prefix to selected source columns, excluding TIMESTAMP/Time, when you need to rename fields without changing the time column
- `docs/api/source/add_suffix.md` — **otp.Source.add_suffix** — Adds a suffix to selected source columns, excluding TIMESTAMP/Time, when you need to rename outputs without changing values
- `docs/api/source/agg.md` — **otp.Source.agg** — Applies one or more aggregations over buckets or sliding windows, when you need grouped summaries, running calculations, or bucketed output ticks
- `docs/api/source/apply.md` — **otp.Source.apply** — Applies an external query, callable, type conversion, or GraphQuery to a source when you need to transform ticks or derive a column
- `docs/api/source/cache.md` — **otp.Source.cache** — Creates a session-only cache from a query and returns a Source that reads it; use when reusing query results or controlling cache refresh and access
- `docs/api/source/character_present.md` — **otp.Source.character_present** — Propagates ticks whose field contains any listed character, or the opposite when discard_on_match=True, when filtering string-like values by character membership
- `docs/api/source/copy.md` — **otp.Source.copy** — Builds a copied calculation graph with shared node ids, so merged or joined copies glue common pre-copy nodes together
- `docs/api/source/corp_actions.md` — **otp.Source.corp_actions** — Returns a new source with corporate-action adjustments applied to selected fields, used to back-adjust prices or sizes before analysis
- `docs/api/source/correct_tick_filter.md` — **otp.Source.correct_tick_filter** — Filters a source to reflect corrections and cancellations as of a chosen time, when you need historical or current tick visibility
- `docs/api/source/count.md` — **otp.Source.count** — Returns the total tick count and executes the query; use it in Jupyter to quickly check whether data is present
- `docs/api/source/deepcopy.md` — **otp.Source.deepcopy** — Copies all graph and change ids for every node when you need an independent duplicate of a Source graph
- `docs/api/source/diff.md` — **otp.Source.diff** — Compares two time series and returns differing, matching, or all ticks when you need field-by-field alignment and mismatch inspection
- `docs/api/source/distinct.md` — **otp.Source.distinct** — Outputs one tick per distinct key combination, keeping either only key fields or the first/last tick for each unique set
- `docs/api/source/drop.md` — **otp.Source.drop** — Removes columns by literal names, Column objects, or regex patterns; use it to prune fields before running or chaining sources
- `docs/api/source/dropna.md` — **otp.Source.dropna** — Drops ticks with NaN values by any or all fields, or a subset, when you need to filter incomplete rows
- `docs/api/source/dump.md` — **otp.Source.dump** — Dumps selected tick columns to stdout when a condition matches, and use it for debugging or tracing intermediate results
- `docs/api/source/estimate_ts_delay.md` — **otp.Source.estimate_ts_delay** — Computes delay and correlation between two correlated tick series; reach for it when aligning one series against another by estimated lag
- `docs/api/source/execute.md` — **otp.Source.execute** — Executes operations for their side effects without saving results to columns, returning a modified Source unless inplace=True
- `docs/api/source/exp_tw_average.md` — **otp.Source.exp_tw_average** — Computes exponentially time-weighted averages per bucket; use it when you need decay-weighted aggregation with configurable bucket timing and running windows
- `docs/api/source/exp_w_average.md` — **otp.Source.exp_w_average** — Computes exponentially weighted average per bucket; use it when recent ticks should count more than older ones
- `docs/api/source/fillna.md` — **otp.Source.fillna** — Replaces NaN in floating-point fields with a constant, previous tick, or Operation value when cleaning missing values selectively or in place
- `docs/api/source/find_value_for_percentile.md` — **otp.Source.find_value_for_percentile** — Returns the value whose percentile rank is closest to a requested percentile, or an interpolated/threshold value when configured
- `docs/api/source/first.md` — **otp.Source.first** — Selects the first n ticks from each bucket or sliding window when you need earliest events per interval or group
- `docs/api/source/get_name.md` — **otp.Source.get_name** — Returns the source name, optionally sanitizing unsupported query-name characters when you need a valid .otq identifier
- `docs/api/source/head.md` — **otp.Source.head** — Executes the query and returns the first n ticks as a pandas DataFrame; use it in Jupyter to inspect initial rows quickly
- `docs/api/source/high.md` — **otp.Source.high** — Selects the n ticks with highest values in a column, used when you need top-ranked ticks per bucket or running window
- `docs/api/source/high_time.md` — **otp.Source.high_time** — Returns the timestamp of the tick with the highest input value; use it for deprecated high-time aggregation, now replaced by high_time()
- `docs/api/source/if_else.md` — **otp.Source.if_else** — Returns a column that selects if_expr when condition is true and else_expr otherwise; use it for per-tick conditional assignment
- `docs/api/source/implied_vol.md` — **otp.Source.implied_vol** — Computes implied volatility per bucket from PRICE and OPTION_PRICE using Black-Scholes when you need option-volatility aggregation
- `docs/api/source/insert_at_end.md` — **otp.Source.insert_at_end** — Adds a delimiter field and emits an extra end-of-stream tick at query end; use when you need a terminal marker or only the final tick
- `docs/api/source/insert_data_quality_event.md` — **otp.Source.insert_data_quality_event** — Inserts data quality event ticks before or after selected input ticks, when you need to mark arrivals as OK, MISSING, or another supported type
- `docs/api/source/insert_tick.md` — **otp.Source.insert_tick** — Inserts one or more ticks before or after selected input ticks, when you need to add synthetic rows with copied, default, or explicit field values
- `docs/api/source/intercept_data_quality.md` — **otp.Source.intercept_data_quality** — Removes data quality messages from the stream, returning a modified Source or None when you want to suppress delivery to the client
- `docs/api/source/intercept_symbol_errors.md` — **otp.Source.intercept_symbol_errors** — Removes per-symbol errors from the source stream, and use it when you want symbol-level failures to stop reaching the client
- `docs/api/source/join_with_collection.md` — **otp.Source.join_with_collection** — Joins ticks from a state-variable collection onto each input tick, when you need per-tick enrichment from TickSet, TickList, TickDeque, or TickSetUnordered
- `docs/api/source/join_with_query.md` — **otp.Source.join_with_query** — Executes a query per input tick and joins its result, when you need per-row lookup, symbol mapping, or conditional enrichment
- `docs/api/source/join_with_snapshot.md` — **otp.Source.join_with_snapshot** — Joins each input tick with saved snapshot fields, or fills outer-join defaults when the snapshot is absent or unmatched
- `docs/api/source/last.md` — **otp.Source.last** — Selects the last n ticks from each bucket or running window when you need trailing values, latest observations, or end-of-interval snapshots
- `docs/api/source/lee_and_ready.md` — **otp.Source.lee_and_ready** — Classifies each trade as buy, sell, or unknown using the Lee and Ready quote-match algorithm; use it to add BuySellFlag from trades and quotes
- `docs/api/source/limit.md` — **otp.Source.limit** — Propagates only the first N regular ticks, optionally skipping an initial offset or counting across symbols, when you need to truncate a stream
- `docs/api/source/linear_regression.md` — **otp.Source.linear_regression** — Computes slope and intercept for dependent versus independent fields per bucket; reach for trend fitting or relationship estimation over ticks
- `docs/api/source/logf.md` — **otp.Source.logf** — Calls LOGF to emit formatted ERROR, WARNING, or INFO messages on selected ticks when you need conditional logging in a source pipeline
- `docs/api/source/low.md` — **otp.Source.low** — Selects the n ticks with the lowest values in a column, used when you need per-bucket minima or lowest-ranked events
- `docs/api/source/low_time.md` — **otp.Source.low_time** — Returns the timestamp of the tick with the lowest input value; reach for it when you need the time of a minimum rather than the minimum itself
- `docs/api/source/meta_fields.md` — **otp.Source.meta_fields** — Provides pseudo-columns like Time, _START_TIME, _TIMEZONE, and _SYMBOL_NAME when you need tick metadata inside Expr or column access
- `docs/api/source/mkt_activity.md` — **otp.Source.mkt_activity** — Adds MKT_ACTIVITY session-flag strings to each tick, using a named or default calendar when you need market-session context
- `docs/api/source/modify_query_times.md` — **otp.Source.modify_query_times** — Changes a source query’s start and end times, and use it when you need to shift query bounds or remap output timestamps
- `docs/api/source/modify_symbol_name.md` — **otp.Source.modify_symbol_name** — Returns a Source with its input symbol name replaced; use it to retarget ticks to another SYMBOL_NAME or expression
- `docs/api/source/multi_portfolio_price.md` — **otp.Source.multi_portfolio_price** — Computes weighted portfolio prices for multiple portfolios per bucket; use when aggregating portfolio-valued symbols from a portfolios query
- `docs/api/source/ob_num_levels.md` — **otp.Source.ob_num_levels** — Returns the number of order-book levels at each bucket end; use it to measure ASK, BID, or both sides over time
- `docs/api/source/ob_size.md` — **otp.Source.ob_size** — Returns total order-book size across selected levels at each bucket end; use it to aggregate depth by time, side, or groups
- `docs/api/source/ob_snapshot.md` — **otp.Source.ob_snapshot** — Returns order book state at each bucket end, including price, size, side, and last update time for selected depth levels
- `docs/api/source/ob_snapshot_flat.md` — **otp.Source.ob_snapshot_flat** — Returns a single-tick order-book snapshot with one field group per level; use it to flatten depth into bucketed snapshots
- `docs/api/source/ob_snapshot_wide.md` — **otp.Source.ob_snapshot_wide** — Returns side-by-side order book levels with price, size, and last update time at each bucket end when you need wide book snapshots
- `docs/api/source/ob_summary.md` — **otp.Source.ob_summary** — Computes order-book summary statistics like VWAP, best and worst price, total size, and level counts when you need book snapshots or side-specific summaries
- `docs/api/source/ob_vwap.md` — **otp.Source.ob_vwap** — Computes size-weighted order-book price across selected levels at bucket ends; reach for VWAP-style book snapshots with optional bucketing and grouping
- `docs/api/source/option_price.md` — **otp.Source.option_price** — Computes option price and optional Greeks using Black-Scholes or Cox-Ross-Rubinstein when you need CALL/PUT valuation from strike, expiration, volatility, and rate inputs
- `docs/api/source/partition_evenly_into_groups.md` — **otp.Source.partition_evenly_into_groups** — Partitions ticks into a specified number of groups so each group’s weight sum is as equal as possible; use when balancing a field by weight within buckets
- `docs/api/source/percentile.md` — **otp.Source.percentile** — Computes running percentile quantiles per bucket from selected numeric fields, with optional grouping and flexible bucket boundaries
- `docs/api/source/plot.md` — **otp.Source.plot** — Executes the query and returns a pandas DataFrame plot result; use it in Jupyter to visualize query output with DataFrame.plot
- `docs/api/source/pnl_realized.md` — **otp.Source.pnl_realized** — Computes realized PnL per tick using FIFO matching; use it to attach profit or loss to buy and sell trades
- `docs/api/source/point_in_time.md` — **otp.Source.point_in_time** — Joins each input tick to matching ticks from another source at specified time or tick offsets, when you need sparse point-in-time lookups
- `docs/api/source/portfolio_price.md` — **otp.Source.portfolio_price** — Computes weighted portfolio price per bucket; use it when aggregating prices across symbols with optional grouping and flexible bucket boundaries
- `docs/api/source/primary_exch.md` — **otp.Source.primary_exch** — Filters ticks by the security’s primary exchange, or splits primary versus non-primary ticks when discard_on_match is True
- `docs/api/source/print_otq.md` — **otp.Source.print_otq** — Prints a temporary .otq query generated from Source.to_otq(), then deletes the file; use it to inspect the emitted OTQ text
- `docs/api/source/process_by_group.md` — **otp.Source.process_by_group** — Groups ticks by fields and runs a per-group source transform, returning merged outputs when you need group-specific query logic or async processing
- `docs/api/source/ranking.md` — **otp.Source.ranking** — Computes running rank or percentile fields over buckets from specified tick fields, when you need ordered positions without reordering ticks
- `docs/api/source/rename.md` — **otp.Source.rename** — Renames source columns by exact names or regex patterns, with optional skips and in-place update when you need to reshape field names
- `docs/api/source/render.md` — **otp.Source.render** — Renders a calculation graph with graphviz and use it to inspect or debug the underlying event-processor graph, especially in Jupyter
- `docs/api/source/render_otq.md` — **otp.Source.render_otq** — Renders the current Source graph to an image path, which you’d use to inspect or debug a query visually
- `docs/api/source/return_ep.md` — **otp.Source.return_ep** — Computes return ratio between bucket end and start prices, or running latest-vs-first price, when you need interval or sliding-window returns
- `docs/api/source/root.md` — **otp.Source** — Base execution-graph class for all onetick.py sources; use it when checking source inheritance or constructing a raw source from query parameters
- `docs/api/source/save_snapshot.md` — **otp.Source.save_snapshot** — Saves the last ticks per symbol or group into a named snapshot, then use it when you need later ReadSnapshot access to those ticks
- `docs/api/source/schema.md` — **otp.Source.schema** — Returns the source’s column-name-to-type schema and lets you inspect, copy, set, or update it after sink adjustments
- `docs/api/source/script.md` — **otp.Source.script** — Applies a per-tick script to each tick, using a Python callable, script string, or file path when you need tick-by-tick mutation or derived fields
- `docs/api/source/set_name.md` — **otp.Source.set_name** — Sets the internal source name used in generated .otq filenames and query names when you want a stable query label
- `docs/api/source/show_corrected_ticks.md` — **otp.Source.show_corrected_ticks** — Returns corrected and cancellation ticks with TICK_STATUS codes, used when you need both original and replacement/cancel records
- `docs/api/source/show_data_quality.md` — **otp.Source.show_data_quality** — Shows data quality events in the query interval; call it when you need inserted quality markers to appear in results
- `docs/api/source/show_hidden_ticks.md` — **otp.Source.show_hidden_ticks** — Propagates all tick fields and reveals normally hidden nonzero-status ticks when you need original and correction ticks in one sequence
- `docs/api/source/show_symbol_errors.md` — **otp.Source.show_symbol_errors** — Propagates per-symbol error ticks so symbol-level failures become visible in the output when you need to inspect thrown symbol errors
- `docs/api/source/show_symbol_name_in_db.md` — **otp.Source.show_symbol_name_in_db** — Adds SYMBOL_NAME_IN_DB to input ticks so you can inspect the database symbol name instead of the artificial continuous symbol
- `docs/api/source/sink.md` — **otp.Source.sink** — Appends an EP node to a Source and connects its output pin when you need to attach onetick.query objects to a source, optionally returning a copy
- `docs/api/source/skip_bad_tick.md` — **otp.Source.skip_bad_tick** — Discards or keeps ticks whose field value jumps beyond a threshold versus neighboring ticks; use it to filter out bad price spikes or keep only anomalies
- `docs/api/source/sort.md` — **otp.Source.sort** — Sorts ticks by one or more columns, with per-column ascending control, when you need ordered output or inplace reordering
- `docs/api/source/split.md` — **otp.Source.split** — Splits a source into multiple outputs by matching an expression against listed values or ranges, when you need branch-specific streams
- `docs/api/source/standardized_moment.md` — **otp.Source.standardized_moment** — Computes the standardized moment of a column over buckets; use it to measure higher-order shape like skewness or kurtosis
- `docs/api/source/state_vars.md` — **otp.Source.state_vars** — Provides a dict of state variables accessible by name when you need to inspect or use declared processor state
- `docs/api/source/switch.md` — **otp.Source.switch** — Splits a source into multiple outputs by an expression and case values, when you need separate streams for matched values or ranges
- `docs/api/source/table.md` — **otp.Source.table** — Sets output field order and schema from field types or default values, and use it to select, add, or reorder tick columns
- `docs/api/source/tail.md` — **otp.Source.tail** — Executes the query and returns the last n ticks as a pandas DataFrame when you want to inspect recent values in Jupyter
- `docs/api/source/throw.md` — **otp.Source.throw** — Throws an exception, error, or warning when a condition is true; use it to abort execution, stop propagation, or validate placeholder queries
- `docs/api/source/time_filter.md` — **otp.Source.time_filter** — Filters ticks by time window and day pattern, used to keep or discard events within specific trading hours or dates
- `docs/api/source/time_interval_change.md` — **otp.Source.time_interval_change** — Shifts a source’s start and end bounds, expanding or shrinking the query window when you need border ticks reassigned to the original endpoints
- `docs/api/source/time_interval_shift.md` — **otp.Source.time_interval_shift** — Shifts a source’s query time window and rewrites tick timestamps to fit the original range when you need a different database slice
- `docs/api/source/to_df.md` — **otp.Source.to_df** — Returns a DataFrame from a Source query result; reach for it only when maintaining older code, since otp.run replaces it
- `docs/api/source/to_graph.md` — **otp.Source.to_graph** — Constructs a GraphQuery from a source, when you need to inspect or render the query graph with optional symbols and time bounds
- `docs/api/source/to_otq.md` — **otp.Source.to_otq** — Saves a Source graph to an .otq file and returns file_name::query_name when you need to export a query definition
- `docs/api/source/to_symbol_param.md` — **otp.Source.to_symbol_param** — Creates a read-only symbol-parameter source with the same columns except Time, used after a first-stage query with symbol params
- `docs/api/source/transpose.md` — **otp.Source.transpose** — Joins ticks into one or splits one tick into many; use it to reshape rows into suffixed columns or reverse that layout
- `docs/api/source/update.md` — **otp.Source.update** — Updates Source fields or state variables conditionally from if_set and else_set mappings, when you need per-row branching assignments
- `docs/api/source/update_timestamp.md` — **otp.Source.update_timestamp** — Assigns alternative timestamps to ticks, sorting out-of-order results and handling delays or zero timestamps when remapping event time
- `docs/api/source/value_present.md` — **otp.Source.value_present** — Filters or propagates ticks whose field value contains any listed values, or excludes matches when discard_on_match=True
- `docs/api/source/virtual_ob.md` — **otp.Source.virtual_ob** — Creates virtual order-book ticks from best bid/ask updates, with optional quote grouping, stale-quote timeout, and full-detail output when synthesizing books
- `docs/api/source/where.md` — **otp.Source.where** — Filters ticks by a condition and returns a new Source; use it to keep, invert, or stop at matching ticks
- `docs/api/source/where_clause.md` — **otp.Source.where_clause** — Splits a source into matching and non-matching branches; use it to route ticks by a condition, invert it, or stop after first mismatch
- `docs/api/source/write.md` — **otp.Source.write** — Saves a query result into a OneTick database, choosing symbol, tick type, date range, timestamp, and append behavior when loading data
- `docs/api/source/write_parquet.md` — **otp.Source.write_parquet** — Writes a tick series to Parquet, choosing partitioned or single-file output when exporting ticks and controlling compression, row groups, and propagation
- `docs/api/source/write_text.md` — **otp.Source.write_text** — Writes a tick series to text output or files, with headers, field ordering, timestamp formatting, and optional file redirection

### API — sources (otp.DataSource, otp.Ticks, otp.CSV, otp.Query, ...)

- `docs/api/sources/csv.md` — **otp.CSV** — Constructs a CSV source from a file path, buffer, or string contents when you need to load tabular data as ticks
- `docs/api/sources/data_file.md` — **otp.DataFile** — Reads Arrow or JSON files or file contents into ticks and queries them by symbol, when you need file-backed source data
- `docs/api/sources/data_source.md` — **otp.DataSource** — Constructs a source that reads ticks from one or more databases by symbol, tick type, and time range when starting a query from stored data
- `docs/api/sources/empty.md` — **otp.Empty** — Creates an empty source that returns no rows, or a schema-only placeholder when you need column definitions without data
- `docs/api/sources/load_ticks_from_dataframe.md` — **otp.LoadTicksFromDataFrame** — Loads a pandas DataFrame as a tick source when you need DataFrame-backed ticks that bypass otp.run symbol filtering
- `docs/api/sources/odbc.md` — **otp.ODBC** — Reads ticks from an ODBC-compatible database and maps SQL rows into time series when querying external relational data by symbol and time range
- `docs/api/sources/point_in_time.md` — **otp.PointInTime** — Returns ticks from a source at specified timestamps plus millisecond or tick offsets, when you need sparse point lookups with TICK_TIME and OFFSET
- `docs/api/sources/query.md` — **otp.Query** — Creates a data source from an .otq file or query object, used when you need query output as a source with symbol and time bounds
- `docs/api/sources/read_cache.md` — **otp.ReadCache** — Reads a named cached query, optionally creating or refreshing it on demand when cache-only, query-only, or automatic retrieval is needed
- `docs/api/sources/read_from_dataframe.md` — **otp.ReadFromDataFrame** — Loads a pandas DataFrame as a data source, when you need to feed tabular rows into an otp graph with timestamp and symbol mapping
- `docs/api/sources/read_from_kdb.md` — **otp.ReadFromKdb** — Retrieves historical or real-time ticks from KDB tables or qSQL queries when you need a KDB-backed source node
- `docs/api/sources/read_parquet.md` — **otp.ReadParquet** — Reads ticks from a Parquet file or URL, and use it when you need a source with field selection, row filtering, or schema control
- `docs/api/sources/ref_data.md` — **otp.RefData** — Returns reference-data ticks for corporate actions, symbol history, calendars, symbology, or continuous-contract names when you need database metadata
- `docs/api/sources/root.md` — **Sources**
- `docs/api/sources/split_query_output_by_symbol.md` — **otp.SplitQueryOutputBySymbol** — Dispatches a query’s output ticks by a field value, when you need each symbol replica to receive only matching results
- `docs/api/sources/symbology_mapping.md` — **otp.SymbologyMapping** — Returns symbol translation mappings from the reference database; use it when you need a destination symbology code for a security on a given symbol date
- `docs/api/sources/symbols.md` — **otp.Symbols** — Returns database symbol names, optionally filtered by pattern, tick type, symbology, or CEP symbol-return mode when you need a symbol list source
- `docs/api/sources/tick.md` — **otp.Tick** — Generates one synthetic tick per bucket or query interval with fixed columns and timestamps; use it to create test or placeholder event streams
- `docs/api/sources/ticks.md` — **otp.Ticks** — Generates synthetic ticks from dict, list, DataFrame, or keyword data; reach for it when you need a tick source with controllable timestamps and offsets

### API — order book sources

- `docs/api/sources/order_book/ob_num_levels.md` — **otp.ObNumLevels** — Constructs a source that returns order-book level counts for a database, when you need book depth as a data source shortcut
- `docs/api/sources/order_book/ob_size.md` — **otp.ObSize** — Constructs a source that returns order book level counts from a database, when you need book depth size as a query source
- `docs/api/sources/order_book/ob_snapshot.md` — **otp.ObSnapshot** — Constructs an order book snapshot source from a db, typically when you need snapshot rows via DataSource plus ob_snapshot()
- `docs/api/sources/order_book/ob_snapshot_flat.md` — **otp.ObSnapshotFlat** — Constructs a flat order-book snapshot source from a database when you need snapshot rows instead of raw book events
- `docs/api/sources/order_book/ob_snapshot_wide.md` — **otp.ObSnapshotWide** — Constructs a wide order-book snapshot source from a database, used when you need snapshot rows with configurable bucketing and grouping
- `docs/api/sources/order_book/ob_summary.md` — **otp.ObSummary** — Constructs an order book summary source from a db, used when you want shortcut access to ob_summary aggregation output
- `docs/api/sources/order_book/ob_vwap.md` — **otp.ObVwap** — Constructs a source that computes size-weighted order-book price over selected levels, when you need VWAP from book depth
- `docs/api/sources/order_book/root.md` — **Order book sources**

### API — snapshot sources

- `docs/api/sources/snapshot/find_snapshot_symbols.md` — **otp.FindSnapshotSymbols** — Returns snapshot symbols as SYMBOL_NAME ticks, optionally translating symbology and filtering by pattern when you need to enumerate snapshot contents
- `docs/api/sources/snapshot/read_snapshot.md` — **otp.ReadSnapshot** — Reads saved snapshot ticks by snapshot name and symbol from memory or memory-mapped storage when you need to query previously saved data
- `docs/api/sources/snapshot/root.md` — **Snapshot sources**
- `docs/api/sources/snapshot/show_snapshot_list.md` — **otp.ShowSnapshotList** — Lists snapshot names stored in memory, memory-mapped files, or both, when you need to inspect snapshots saved by Source.save_snapshot()

### API — state variables (otp.state.*) for stateful per-tick logic

- `docs/api/state/dynamic_tick.md` — **otp.state.dynamic_tick** — Creates a dynamic tick local variable for per-tick scripts, then add or read fields when tick sequences must change shape at runtime
- `docs/api/state/root.md` — **State Variables**
- `docs/api/state/tick_deque.md` — **otp.state.tick_deque** — Creates a stateful tick deque with optional default ticks, scope, and schema when you need deque operations inside per-tick scripts
- `docs/api/state/tick_deque_tick.md` — **otp.state.tick_deque_tick** — Provides a per-tick tick object for tick-deque access and field-reading helpers when scripting over stored tick sequences
- `docs/api/state/tick_list.md` — **otp.state.tick_list** — Creates a mutable tick-list state variable, then lets you push, erase, sort, size, clear, or dump ticks during scripts or source operations
- `docs/api/state/tick_list_tick.md` — **otp.state.tick_list_tick** — Provides a tick-list tick handle for per-tick scripts, with typed field access and timestamp retrieval when iterating state tick lists
- `docs/api/state/tick_sequence_tick.md` — **otp.state.tick_sequence_tick** — Provides per-tick script tick access with navigation and typed field getters when iterating or mutating tick sequences
- `docs/api/state/tick_set.md` — **otp.state.tick_set** — Creates a keyed tick-set state variable, then dump, update, or find ticks by key when maintaining per-source state
- `docs/api/state/tick_set_tick.md` — **otp.state.tick_set_tick** — Defines a per-tick local tick object for tick-set methods when iterating or querying tick-set state inside script functions
- `docs/api/state/tick_set_unordered.md` — **otp.state.tick_set_unordered** — Creates an unordered tick set state variable keyed by selected fields, when you need keyed tick storage with oldest/latest overwrite policy
- `docs/api/state/var.md` — **otp.state.var** — Defines a state variable with a default int, float, or string value, and use it when assigning query-scoped, branch-scoped, or cross-symbol state

### API — types (otp.string, otp.nsectime, otp.decimal, ...)

- `docs/api/types/byte.md` — **otp.byte** — Represents a OneTick byte integer type for Tick fields when you need a signed byte-valued schema entry or literal wrapper
- `docs/api/types/decimal.md` — **otp.decimal** — Creates a 128-bit base-10 decimal value from int, float, or string when exact numeric representation matters
- `docs/api/types/inf.md` — **otp.inf** — Represents infinity and can be used wherever a float value is expected, such as tick values or arithmetic results
- `docs/api/types/int.md` — **otp.int** — Alias of _int for constructing integer-typed values when a OneTick API expects otp.int
- `docs/api/types/long.md` — **otp.long** — Represents a signed long integer value; use it when constructing fields that must store large integer counts or identifiers
- `docs/api/types/msectime.md` — **otp.msectime** — Represents a millisecond-precision datetime value and use it when declaring source columns or assigning new timestamp fields
- `docs/api/types/nan.md` — **otp.nan** — Represents a NaN float value you can pass anywhere a float is expected, such as tick fields or arithmetic results
- `docs/api/types/nsectime.md` — **otp.nsectime** — Represents a nanosecond-precision datetime value; use it when declaring Source columns or converting values to OneTick timestamps
- `docs/api/types/root.md` — **Types**
- `docs/api/types/short.md` — **otp.short** — Represents a short integer tick type with constructor range checking; use it when you need OneTick short-typed fields or schema values
- `docs/api/types/string.md` — **otp.string** — Represents fixed-length or varstring fields, with length set by indexing or Ellipsis when defining or converting schema columns
- `docs/api/types/uint.md` — **otp.uint** — Unsigned integer scalar type for Tick fields; use it when constructing values that must validate as nonnegative integers
- `docs/api/types/ulong.md` — **otp.ulong** — Represents an unsigned long integer type; use it when constructing Tick fields that must hold nonnegative 64-bit-style integer values
- `docs/api/types/varstring.md` — **otp.varstring** — Shortcut for otp.string[...] when you need a variable-length string type alias

## Guides & concepts (`reference/docs/static/`)

### GUIDE — intro (package version, docs link)

- `docs/intro.md` — **Welcome to the onetick.py** — Intro page linking to the public onetick.py documentation when you need the package overview and generated docs version

### GUIDE — overview & changelog

- `docs/static/changelog.md` — **Changelog** — Lists release notes, added features, fixes, and removals; check it to spot version-specific API changes or new capabilities
- `docs/static/overview.md` — **Overview** — Introduces onetick.py as a Pandas-like Python interface to OneTick, explaining capabilities, installation, and hosted or local deployment options
- `docs/static/root.md` — **Getting started** — Intro page pointing to the first getting started article and a video tutorial when you are beginning with the docs

### GUIDE — concepts (data model, calc graph, schema, symbols, start/end)

- `docs/static/concepts/calc_graph.md` — **Calculation graph** — Explains how source operations accumulate as immutable calculation graphs, when copies, merges, joins, and graph gluing collapse shared nodes
- `docs/static/concepts/data_model.md` — **Data structures and functions** — Explains Source, Column, and Operation basics, field naming rules, and when to use column or source methods and functions
- `docs/static/concepts/onetick_data_processing.md` — **How Onetick analytical engine works** — Explains tick flow, event processor ordering, accumulation, and aggregation behavior when designing performant query graphs
- `docs/static/concepts/onetick_parallelization.md` — **How Onetick parallelizes query execution** — Explains how query work splits across cores, symbols, batches, and bound or unbound branches when tuning execution parallelism
- `docs/static/concepts/python_callable_parser.md` — **Python callable parsing** — Explains how apply and script translate Python callables into OneTick CASE expressions or per-tick scripts, and when callable bodies are executed versus parsed
- `docs/static/concepts/root.md` — **Concepts**
- `docs/static/concepts/schema.md` — **Schema** — Describes source field names and types, when to inspect or set schemas manually, and how schema deduction and type changes work
- `docs/static/concepts/start_end.md` — **Query start / end flow** — Explains how query intervals are set on otp.run or DataSource, when defaults apply, and how otp.dt and timezone affect execution
- `docs/static/concepts/symbols.md` — **Databases, symbols, and tick types** — Explains how DataSource and merge select db, symbol, and tick type, and when to use bound, unbound, or dynamic symbols
- `docs/static/concepts/use_onetick_query.md` — **How to use onetick.query with onetick.py** — Explains when to bypass otp wrappers and call onetick.query directly for source processors, sink processors, or built-in expressions
- `docs/static/concepts/webapi.md` — **Remote access with WebAPI** — Explains running onetick-py against remote OneTick servers, what client-side features are unsupported, and when WebAPI replaces local binaries

### GUIDE — configuration & logging

- `docs/static/configuration/configuration.md` — **Configuration parameters** — Lists OTP_WEBAPI and otp.config defaults for timezone, context, time range, database, and symbol when configuring query execution
- `docs/static/configuration/logging.md` — **Logging** — Explains otp.config.logging, which sets log severity or a config file path when you need stderr logging or custom logging setup
- `docs/static/configuration/root.md` — **Configuration**

### GUIDE — getting started (retrieval, filtering, aggregations, joins)

- `docs/static/getting_started/accessing_lower_level_api.md` — **Accessing a Lower Level API** — Shows how to call otq operators through otp.DataSource.sink when a feature is missing from the higher-level API and then extend the schema manually
- `docs/static/getting_started/aggregations.md` — **Aggregating** — Shows how to aggregate ticks with otp.DataSource.agg using sums, VWAP, counts, fixed buckets, running windows, and group_by
- `docs/static/getting_started/corporate_actions.md` — **Corporate Actions** — Shows how to adjust price or volume for splits with corp_actions and how to retrieve corporate action records for a symbol
- `docs/static/getting_started/daily_OHLCV.md` — **Daily OHLCV (with closing prices)** — Shows how to query daily OHLCV rows from DAY tables with or without symbology, including composite exchange filtering for US equities
- `docs/static/getting_started/data_inspection.md` — **Data inspection** — Explains otp.databases and DB methods for listing databases, dates, tick types, symbols, and schemas when building queries or checking data availability
- `docs/static/getting_started/data_retrieval.md` — **Data Retrieval** — Shows how to list databases, tick types, and symbols, then query and merge time series with otp.DataSource, otp.Symbols, and otp.merge
- `docs/static/getting_started/filtering.md` — **Filtering** — Shows how to filter ticks with where, string matching, character_present, branching, and positional selection when narrowing OneTick queries
- `docs/static/getting_started/order_book_analytics.md` — **Order Book Analytics** — Shows order-book snapshots, imbalance, sweep, and market-by-order examples when analyzing depth, liquidity, and execution priority
- `docs/static/getting_started/real_time_processing.md` — **Real-time processing** — Shows how to run queries continuously with callbacks, using a golden-cross signal example and otp.run in real-time or historical mode
- `docs/static/getting_started/rendering_graphs.md` — **Rendering query graphs** — Shows how to render onetick-py and OTQ queries as graphs for debugging nested, multi-source, or scripted pipelines
- `docs/static/getting_started/root.md` — **Getting Started** — Introduces basic configuration, cloud authentication, and runnable starter queries when you need setup and first end-to-end examples
- `docs/static/getting_started/session.md` — **Session: configuring OneTick databases and ACL** — Explains creating otp.Session, customizing otp.Config and ACL, and registering temporary, existing, derived databases and contexts
- `docs/static/getting_started/symbologies.md` — **Symbologies** — Explains symbol_date and supported symbology prefixes for querying renamed securities, and shows mapping between database and alternate symbol formats
- `docs/static/getting_started/time_based_joins.md` — **Time-Based Joins** — Shows join_by_time for matching each tick with the active tick from another series, and join_with_query for per-tick lookups
- `docs/static/getting_started/variables_and_data_structures.md` — **Variables and Data Structures** — Explains state variables, tick sets, lists, and deques for carrying values across ticks, lookup joins, and FIFO-style collections

### GUIDE — use cases (bars, VWAP, markouts, prevailing quote, ...)

- `docs/static/getting_started/use_cases/creating_bars.md` — **Creating Bars** — Shows how to build 1-minute OHLCV bars from trades with agg and bucket_interval, or query precomputed TRD_1M bars with apply_times_daily
- `docs/static/getting_started/use_cases/market_vwap.md` — **Interval Metrics (e.g., VWAP)** — Shows how to compute VWAP over a query interval and join it to each order’s arrival-to-exit window
- `docs/static/getting_started/use_cases/markouts.md` — **Point-in-time benchmarks: BBO at different markouts** — Shows how to join trades with NBBO quotes shifted by chosen markout intervals to inspect prevailing bid and ask before or after each trade
- `docs/static/getting_started/use_cases/prevailing_quote.md` — **Prevailing quote at the time of a trade** — Joins trades with the most recent NBBO quote at each trade timestamp, when you need quote context for executions
- `docs/static/getting_started/use_cases/real_time.md` — **Real-time processing: Signal Generation** — Shows how to compute moving-average crossover buy and sell signals and stream them through a callback with otp.run
- `docs/static/getting_started/use_cases/realized_profit.md` — **Realized P&L on the FIFO basis** — Shows FIFO realized profit calculation from buy and sell trades, using pnl_realized or a manual deque-based script when matching executions
- `docs/static/getting_started/use_cases/retrieving_tick_data.md` — **Retrieving Tick Data** — Shows how to run a DataSource query over a time window for selected symbols and timezone when fetching raw ticks
- `docs/static/getting_started/use_cases/root.md` — **Use cases**
- `docs/static/getting_started/use_cases/up_down_ticks.md` — **Upticks / Downticks** — Shows how to label each trade as an uptick, downtick, or unchanged by comparing PRICE to the previous trade, using apply when you need row-by-row custom logic

### GUIDE — best-execution use cases

- `docs/static/getting_started/use_cases/bestex/arrival_prices.md` — **Arrival ask / bid / mid prices for an order** — Shows how to join new orders with quotes at arrival time and compute the arrival mid-price from ask and bid
- `docs/static/getting_started/use_cases/bestex/effective_spread.md` — **Effective spread** — Shows how to compute effective spread from orders and quotes using VWAP, prevailing mid-price, and trade direction
- `docs/static/getting_started/use_cases/bestex/etq.md` — **Effective to quoted spread (ETQ)** — Shows how to compute ETQ from VWAP, mid, bid, ask, and side for aggressive orders using joins and aggregation
- `docs/static/getting_started/use_cases/bestex/exit_ask_bid_prices.md` — **Exit ask/bid prices for an order** — Shows how to filter completed or canceled orders, join quotes by time, and extract first ask and bid prices per order ID
- `docs/static/getting_started/use_cases/bestex/far_near_touch.md` — **FT (far touch) and NT (near touch)** — Shows how to derive near-touch and far-touch prices from arrival bid/ask quotes for buy and sell orders
- `docs/static/getting_started/use_cases/bestex/implementation_shortfall.md` — **Implementation shortfall (slippage)** — Shows how to compute implementation shortfall per order from arrival mid-price, VWAP, side, and filled quantity using otp.DataSource, joins, and aggregations
- `docs/static/getting_started/use_cases/bestex/market_impact.md` — **Market impact** — Explains the market impact formula and a worked example joining orders with shifted quotes to compute markout-based MI
- `docs/static/getting_started/use_cases/bestex/notional_spread.md` — **Notional spread (quoted spread)** — Shows how to compute each order’s notional spread from executed quantity and arrival ask/bid prices, when you need quoted spread per client order
- `docs/static/getting_started/use_cases/bestex/num_spreads.md` — **Number spreads** — Computes Num_Spreads as abs((VWAP - FT) / (FT - NT)) with nan when FT equals NT, for order execution slippage analysis
- `docs/static/getting_started/use_cases/bestex/offside_value.md` — **Offside value** — Computes order offside value as executed quantity times VWAP minus far touch times direction, when measuring aggressive-order impact
- `docs/static/getting_started/use_cases/bestex/opportunity_cost.md` — **Opportunity cost** — Shows how to compute order opportunity cost from direction, unexecuted quantity, and arrival versus exit mid-price after joining orders and quotes
- `docs/static/getting_started/use_cases/bestex/price_imrpovements.md` — **Price improvement** — Computes execution price improvement in basis points from VWAP and far touch, used to score buy or sell order execution quality
- `docs/static/getting_started/use_cases/bestex/pwp.md` — **Participation weighted price (PWP)** — Shows how to compute participation weighted price from trades and join it to orders when you need VWAP up to a participation threshold
- `docs/static/getting_started/use_cases/bestex/relative_performance_measure.md` — **Relative Performance Measure (RPM)** — Explains the RPM formula and shows how to compute execution quality versus market volume using orders and trades
- `docs/static/getting_started/use_cases/bestex/reversion.md` — **Reversion** — Explains the reversion metric in basis points and shows how to compute post-execution price reversal from orders and quotes
- `docs/static/getting_started/use_cases/bestex/right_way.md` — **Right way** — Shows how to label each order as favorable or unfavorable by comparing next and current mid prices after joining orders and quotes
- `docs/static/getting_started/use_cases/bestex/root.md` — **BestEx & TCA**
- `docs/static/getting_started/use_cases/bestex/takeout_success.md` — **Takeout success** — Shows how to flag orders that fully take available market size at the quote side, using joined order and quote data
- `docs/static/getting_started/use_cases/bestex/volatility.md` — **Volatility** — Shows how to compute price volatility as standard deviation divided by average price times 100 after loading TRD trades from US_COMP_SAMPLE
- `docs/static/getting_started/use_cases/bestex/window_ask_bid.md` — **Window ask and window bid** — Shows how to join orders with quotes over a before-and-after time window to compute windowed ask and bid prices
- `docs/static/getting_started/use_cases/bestex/windowed_nt_ft.md` — **Windowed FT (far touch) and Windowed NT (far touch)** — Shows how to compute Window_NT and Window_FT from order side and windowed bid/ask quotes, then join them back to orders

### GUIDE — installation (pip, on-prem, WebAPI)

- `docs/static/installation/onprem.md` — **onetick-py with local OneTick binaries** — Explains installing onetick-py alongside on-prem OneTick binaries and configuring MAIN_ONE_TICK_DIR or PYTHONPATH when running locally without WebAPI
- `docs/static/installation/pip.md` — **Other installation options** — Lists pip-based install paths, offline wheel setup, strict dependencies, and local pip configuration when standard PyPI installation is insufficient
- `docs/static/installation/webapi.md` — **Installation** — Explains how to install onetick-py with the webapi extra for remote REST access to OneTick Cloud or other WebAPI servers
- `docs/static/installation/webapi_onprem.md` — **WebAPI with on-prem OneTick server** — Explains server port and cache configuration plus client installation steps needed before connecting onetick-py WebAPI to an on-prem OneTick server

### GUIDE — Ray distributed execution

- `docs/static/ray/ray_examples.md` — **Ray usage examples** — Shows how to wrap otp code in ray.remote functions, pass arguments, run otp.run remotely, and retrieve pandas results
- `docs/static/ray/ray_installation.md` — **Ray client installation** — Explains how to install and configure the deprecated Ray client, set Ray and TLS environment variables, and connect remotely when running on Ray instead of local OneTick
- `docs/static/ray/ray_remote.md` — **Remote OTP with Ray** — Shows how to run onetick.py code inside a Ray remote function, initialize and shut down Ray, and fetch results with ray.get
- `docs/static/ray/ray_server_guide.md` — **Ray server installation** — Explains how to install and start a Ray head node for OneTick, open required ports, and connect clients with ray.init
- `docs/static/ray/ray_troubleshooting.md` — **Troubleshooting** — Explains installation and Ray client connection failures, including PYTHONPATH, TLS certificates, port checks, and gRPC timeout debugging

### GUIDE — testing onetick.py code

- `docs/static/testing/debug.md` — **Debugging** — Shows stack traces, tick dumps, OTQ export, graph rendering, and symbol logging when diagnosing incorrect query construction or runtime issues
- `docs/static/testing/features.md` — **onetick-py-test plugin features** — Lists pytest fixtures from onetick-py-test, especially cur_dir, par_dir, and keep_generated_dir for locating test and generated-output directories
- `docs/static/testing/first_test.md` — **Your first test** — Shows how to write and run a pytest test for onetick-py code, import the plugin, and validate a simple source transformation
- `docs/static/testing/performance.md` — **Performance measurement** — Explains measure_perf.exe, parsing performance summary files, and session performance metrics when you need query timing or profiling data
- `docs/static/testing/prereqs.md` — **Prerequisites** — Explains the pytest-based testing setup and onetick-py-test plugin installation when preparing to run or debug OneTick tests
