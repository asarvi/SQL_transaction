# SQL_transaction

To solve this, I first aggregate the transactions at the daily level by summing the transaction amounts for each date. This simplifies the dataset so the rolling calculation is based on one value per day rather than individual transactions.

Then I calculate the rolling 3-day average using a window function. The window ROWS BETWEEN 2 PRECEDING AND CURRENT ROW takes the current day and the previous two days and computes the average of those daily totals.

This approach returns the rolling average for each day in January 2021 and can easily be extended to other time periods if needed.


```sql
-- First calculate daily totals
WITH daily_totals AS (
    SELECT 
        DATE(transaction_time) AS txn_date,
        SUM(transaction_amount) AS daily_total
    FROM transactions
    WHERE DATE(transaction_time) BETWEEN '2021-01-01' AND '2021-01-31'
    GROUP BY DATE(transaction_time)
)

-- Then calculate the rolling 3-day average
SELECT
    txn_date,
    daily_total,
    AVG(daily_total) OVER (
        ORDER BY txn_date
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS rolling_3day_avg
FROM daily_totals
ORDER BY txn_date;
