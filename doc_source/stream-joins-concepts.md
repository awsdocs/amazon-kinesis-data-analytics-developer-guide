# Streaming Data Operations: Stream Joins<a name="stream-joins-concepts"></a>

You can have multiple in\-application streams in your application\. You can write `JOIN` queries to correlate data arriving on these streams\. For example, suppose that you have the following in\-application streams:
+ **OrderStream** – Receives stock orders being placed\.

  ```
  (orderId SqlType, ticker SqlType, amount SqlType, ROWTIME TimeStamp)
  ```
+ **TradeStream** – Receives resulting stock trades for those orders\.

  ```
  (tradeId SqlType, orderId SqlType, ticker SqlType, amount SqlType, ticker SqlType, amount SqlType, ROWTIME TimeStamp)
  ```

The following are `JOIN` query examples that correlate data on these streams\.

## Example 1: Report Orders Where There Are Trades Within One Minute of the Order Being Placed<a name="join-ex1"></a>

In this example, your query joins both the `OrderStream` and `TradeStream`\. However, because we want only trades placed one minute after the orders, the query defines the 1\-minute window over the `TradeStream`\. For information about windowed queries, see [Sliding Windows](sliding-window-concepts.md)\.

```
SELECT STREAM
     ROWTIME, 
     o.orderId, o.ticker, o.amount AS orderAmount,
     t.amount AS tradeAmount
FROM OrderStream AS o
JOIN TradeStream OVER (RANGE INTERVAL '1' MINUTE PRECEDING) AS t
ON   o.orderId = t.orderId;
```

You can define the windows explicitly using the `WINDOW` clause and writing the preceding query as follows: 

```
SELECT STREAM
    ROWTIME, 
    o.orderId, o.ticker, o.amount AS orderAmount,
    t.amount AS tradeAmount
FROM OrderStream AS o
JOIN TradeStream OVER t
ON o.orderId = t.orderId
WINDOW t AS
    (RANGE INTERVAL '1' MINUTE PRECEDING)
```

When you include this query in your application code, the application code runs continuously\. For each arriving record on the `OrderStream`, the application emits an output if there are trades within the 1\-minute window following the order being placed\. 

The join in the preceding query is an inner join where the query emits records in `OrderStream` for which there is a matching record in `TradeStream` \(and vice versa\)\. Using an outer join you can create another interesting scenario\. Suppose that you want stock orders for which there are no trades within one minute of stock order being placed, and trades reported within the same window but for some other orders\. This is example of an *outer join*\. 

```
SELECT STREAM
    ROWTIME, 
    o.orderId, o.ticker, o.amount AS orderAmount,
    t.ticker, t.tradeId, t.amount AS tradeAmount,
FROM OrderStream AS o
OUTER JOIN TradeStream OVER (RANGE INTERVAL '1' MINUTE PRECEDING) AS t
ON    o.orderId = t.orderId;
```