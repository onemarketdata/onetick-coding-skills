# Bins
How to map numeric field into categories / bins using range values

```python
def split_to_bin(src: otp.Source, bins: list, bin_values: list, input_field_name: str, output_field_name: str) -> otp.Source:
    bins = [-otp.inf] + bins + [+otp.inf]

    def switch(tick):
        for i in range(len(bins) - 1):
            if bins[i] <= tick[input_field_name] < bins[i + 1]:
                break
        return bin_values[i]

    src[output_field_name] = src.apply(switch)
    return src
```

# Calculate hashcode of a field / column

The following code shows how to apply the hashing function to the TRADER_COL column using `otp.hash_code`

```python
daily_positions[TRADER_COL] = daily_positions[TRADER_COL].apply(
                lambda x: hashlib.sha512(x.encode("UTF-8")).hexdigest()
            )
```


# Database locator has a gap. What should do?

if a code like

```python
data = otp.Ticks(X=[1, 2, 3])
data.count()
```

gives an error that 'database locator has a gap' then
you could try to explicitly specify the date range to avoid issues with default date handling

```python
data.count(date=otp.dt(2024, 12, 3))
```
