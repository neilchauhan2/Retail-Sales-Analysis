# SQL Sales Data Analysis Project

### 1. Database Setup

```sql
CREATE DATABASE sql_analysis_db;

CREATE TABLE IF NOT EXISTS sales (
	transactions_id	INT PRIMARY KEY,
	sale_date DATE,
	sale_time TIME,
	customer_id INT,
	gender VARCHAR(15),
	age INT,
	category VARCHAR(15),
	quantity INT,
	price_per_unit FLOAT,
	cogs FLOAT,
	total_sale FLOAT
);
```

### 2. Data Cleaning & Exploration

```sql
-- Check for NULL values
SELECT * FROM sales
WHERE
	transactions_id IS NULL
	OR
	sale_date IS NULL
	OR
	sale_time IS NULL
	OR
	gender IS NULL
	OR
	category IS NULL
	OR
	quantity IS NULL
	OR
	cogs IS NULL
	OR
	price_per_unit IS NULL
	OR
	total_sale IS NULL
;
```

```sql
-- delete the rows with null values
DELETE FROM sales
WHERE
	transactions_id IS NULL
	OR
	sale_date IS NULL
	OR
	sale_time IS NULL
	OR
	gender IS NULL
	OR
	category IS NULL
	OR
	quantiy IS NULL
	OR
	cogs IS NULL
	OR
	price_per_unit IS NULL
	OR
	total_sale IS NULL
;
```

```sql
-- What is the total no. of sales?
SELECT COUNT(*) as total_no_of_sales FROM sales;
```

```sql
-- No. of Customers?
SELECT COUNT(DISTINCT customer_id) as no_of_cusomters FROM sales;
```

```sql
-- List of product categories
SELECT DISTINCT category FROM sales;
```

### 3. Data Analysis

**Q1. Write a SQL Query to retrieve all columns for sales made on '2022-11-05'**

```sql
SELECT * FROM sales WHERE sale_date='2022-11-05';
```

**Q2. Write a SQL Query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**

```sql
SELECT * FROM sales
WHERE
category='Clothing'
AND
TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
AND
quantity >= 4;
```

**Q3. Write a SQL Query to find the total sales for each category.**

```sql
SELECT
category,
SUM(total_sale) as total_sales,
COUNT(*) as orders
FROM sales GROUP BY category;
```

**Q4. Write a SQL Query to find the average age of customers who purchased items from the 'Beauty' category.**

```sql
SELECT
ROUND(AVG(age), 2) as avg_age
FROM sales
WHERE category='Beauty';
```

**Q5. Write a SQL Query to find all transactions where the total_sale is greater than 1000.**

```sql
SELECT * FROM sales
WHERE total_sale > 1000;
```

**Q6. Write a SQL Query to find the total no. of transactions made by each gender in each category**

```sql
SELECT category, gender, COUNT(*) as no_of_transactions
FROM sales
GROUP BY category, gender
ORDER BY category;
```

**Q7. Write a SQL Query to calculate the average sale for each month. Find out the best selling month in each year.**

```sql
SELECT year, month, avg_sale FROM (
	SELECT
	EXTRACT(YEAR from sale_date) as year,
	EXTRACT(MONTH from sale_date) as month,
	AVG(total_sale) as avg_sale,
	RANK() OVER(PARTITION BY EXTRACT(YEAR from sale_date) ORDER BY AVG(total_sale) DESC) as rank
	FROM sales
	GROUP BY year, month
	ORDER BY year, avg_sale DESC
) as subquery
WHERE rank = 1;
```

**Q8. Write a SQL query to find the top 5 customers based on the highest total sales**

```sql
SELECT customer_id, SUM(total_sale) as total_sales
FROM sales
GROUP BY customer_id
ORDER BY total_sales DESC;
```

**Q9. Write a SQL query to find the number of unique customers who purchased items from each category.**

```sql
SELECT category,
COUNT(DISTINCT customer_id) as no_of_unique_customers
FROM sales
GROUP BY category;
```

**Q10. Write a SQL query to create each shift and number of orders per shift (Example Morning <= 12, Afternoon between 12 & 17, Evening > 17)**

```sql
WITH hourly_sales
AS
(
SELECT *,
	CASE
		WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
		WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
		WHEN EXTRACT(HOUR FROM sale_time) > 17 THEN 'Evening'
	END as shift
FROM sales
)
SELECT shift, COUNT(*) as no_of_orders
FROM hourly_sales
GROUP BY shift;
```
