# Example of test for an existing OTQ

This example is particularly useful for developers who need to write tests for their OTQ queries. It provides a clear and concise way to set up input data, apply a query, and verify the results. This example also show of how to use input and output pins in testing.

```python
def test_query_boundness_before_query(session):
    # Create tick data with symbols
    t1 = otp.Ticks({"X": ["a"]}, symbol="A")
    t2 = otp.Ticks({"X": ["b"]}, symbol="B")

    # Load the OTQ query
    q = otp.query(os.path.join("otqs", "merge.otq::merge"))

    # Apply the query to the input tick data
    res = q(IN1=t1, IN2=t2)["OUT"]

    # Run the query and assert the output
    assert len(otp.run(res)) == 2
```
