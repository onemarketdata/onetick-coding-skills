

# Performance measurement

`onetick.py` and OneTick provide some tools and classes to run the query and measure its performance.

## measure_perf.exe

Function ``onetick.py.perf.measure_perf()`` can be used to call OneTick’s **measure_perf.exe** tool.

It runs specified query and prints the summary in the specified file.

## Parsing summary

Classes ``onetick.py.perf.PerformanceSummaryFile`` and ``onetick.py.perf.MeasurePerformance``
can be used to parse contents of the summary file to python format.

Additionally, configuration parameter `stack_info`
can be used to add some python debug information to the parsed result.

## Session metrics

You can also gather performance metrics for all queries made in a session.
To do this, set `gather_performance_metrics` parameter to `True`
when creating an ``otp.Session`` object.

Metrics could be accessed after session close via `otp.Session.performance_metrics` property.

Example of collecting performance metrics, returned data format and information about limitations you can find in the
``otp.Session`` documentation.
