# Case Study #3: Foodie-Fi

### Data Analysis Solutions
1. How many customers has Foodie-Fi ever had?
```
SELECT
	COUNT(DISTINCT customer_id) AS num_customers
FROM subscriptions;
```
To find out how many customers Foodie-fi has, we can use COUNT and DISTINCT to return different values of customer_id.

2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
```
SELECT
	MONTH(start_date) AS months,
	COUNT(customer_id) AS num_customers
FROM subscriptions
GROUP BY months;
```
because we want to find the monthly distribution, we can use MONTH statement to extract the month of start_date.

3. What plan 'start_date' values occur after the year 2020 for our dataset? Show the breakdown by count of events for each 'plan_name'
```
SELECT
  p.plan_name,
  p.plan_id,
  COUNT(*) AS cnt_event
FROM subscriptions s
INNER JOIN plans p ON p.plan_id = s.plan_id
WHERE s.start_date >= '2021-01-01'
GROUP BY p.plan_id,p.plan_name
ORDER BY p.plan_id;
```
because plan_name and start_date are not in the same tables, we have to JOIN the tables. We can use INNER JOIN clause to select records that have matching values in both tables.
Then, filter the result with WHERE clause. In this case we filter the start_date after the year 2020.

4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```
SELECT
    COUNT(*) AS cust_churn,
    ROUND(COUNT(*) * 100 / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions),1) AS perc_churn
FROM subscriptions
WHERE plan_id = 4;
```
We can use the WHERE clause to filter only the customers who have churned the subscriptions and also we use COUNT statement to count the records.
To calculate the percentage, total records multiplied by 100 and divided by total of customers. We use ROUND clause to rounded the percentage to 1 decimal place.

5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
```
WITH cte_churn AS (
	SELECT
		*,
		LAG(plan_id, 1) OVER(PARTITION BY customer_id ORDER BY plan_id) AS prev_plan
	FROM subscriptions)
SELECT
	COUNT(prev_plan) AS cnt_churn,
    ROUND(COUNT(*) * 100/(SELECT COUNT(DISTINCT customer_id) FROM subscriptions),0) AS perc_churn
FROM cte_churn
WHERE plan_id = 4 and prev_plan = 0;
```