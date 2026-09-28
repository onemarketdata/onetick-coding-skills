# Correlation between two tickers

The provided Python script is designed to calculate the correlation between the Volume Weighted Average Prices (VWAP) of two stock symbols, AAPL (Apple Inc.) and MSFT (Microsoft Corporation), using data from the US_COMP database for 2023/12/20

```python
import onetick.py as otp

def query_vwap(symbol, start, end):
    '''This function constructs a query for a specified stock symbol to fetch trade data (PRICE and SIZE) from the US_COMP database.
    It performs an aggregation to calculate the VWAP within 1-minute intervals (buckets), using OneTick's aggregation functionality (otp.agg.vwap).
    '''

    # Define the data source for the given symbol
    q = otp.DataSource('US_COMP', tick_type='TRD', symbol=symbol)
    q = q[['PRICE', 'SIZE']]
    # Aggregate to calculate VWAP in 1-minute buckets
    return q.agg({
        'vwap': otp.agg.vwap('PRICE', 'SIZE')
    }, bucket_interval=60)

def main():
    start_date = otp.dt(2023, 12, 20)  # Replace with your actual start date
    end_date = otp.dt(2023, 12, 21)    # Replace with your actual end date

    # Get VWAP queries for AAPL and MSFT
    aapl_vwap_query = query_vwap('AAPL', start_date, end_date)
    msft_vwap_query = query_vwap('MSFT', start_date, end_date).add_prefix('msft_')

    # Join the VWAP data on the same size and calculate correlation
    joined_data = otp.join(aapl_vwap_query, msft_vwap_query, on='same_size')
    correlation_query = joined_data.agg({'correlation': otp.agg.correlation('vwap', 'msft_vwap')})

    # Run the query to get the correlation result
    correlation_result = otp.run(correlation_query, start=start_date, end=end_date)
    print("Correlation between AAPL and MSFT VWAP:", correlation_result)

if __name__ == "__main__":
    main()
```
