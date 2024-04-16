# Supermarket Case Study by Oliver Roberts

---

### Project Overview

This data analysis project aims to provide insights into customer trends, sales patterns and client satisfaction in order to drive informed decision making for a Supermarket.

The growth of supermarkets in highly populated cities are increasing and competition is high. Therefore using data to derive a deeper understanding of customer behaviour is of the utmost importance to ensure sales growth and customer satisfaction. This dataset consists of historical sales of a supermarket which was recorded in 3 different branches over a period of 3 months.

---

### Defining the business tasks

Q1 : Determine the best performing product line, both in total sales and in profit margin


Q2 : Determine the peak hour of sales during the day


Q3 : Determine the peak weekday for sales, as well as lowest performing day to determine when to run promotions and discounts


Q4 : Explore customer ratings to determine the best rated products, and the worst rated products 


Q5 : Calculate the sales total by location


Q6 : Determine ratings by location


Q7 : Calculate gross income by payment method 


Q8 : Calculate the sales total by product line, split by Gender


Q9 : Determine gross income by customer type


As well as exploring the correlation between:

- Sales total and age
- Sales total by category and age
- Customer type vs ratings
- Customer type vs sales volume
- Ratings vs city
- Ratings vs gender

---

### Data Sources

This publicly available data was obtained on Kaggle [here](https://www.kaggle.com/datasets/lovishbansal123/sales-of-a-supermarket)

The table used in this case study is supermarket_sales.csv

---

### Tools used in analysis

- SQL for cleaning tasks, exploratory data analysis and manipulation
- Tableau for charts and a dashboard

---

### Data Cleaning and EDA 

In the initial data preparation phase, the following data cleaning tasks are to be done: 

- Renaming columns to improve readability and avoid clashing with functions
- Checking for missing values
- Checking that data formatting is correct for variables

The following manipulations also need to be done:

- Creating columns for Weekday and for Month
- Creating a day and night variable based on time of transaction

---

#### Renaming of variables

```sql

EXEC sp_rename 'dbo.supermarket_sales.Invoice id', 'invoice_id', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Branch', 'branch', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.City', 'city', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Customer type', 'customer_type', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Gender', 'gender', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Product line', 'product_line', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Unit price', 'unit_price', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Quantity', 'quantity', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Tax 5%', 'tax_five_percent', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Total', 'order_value', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Date', 'transaction_date', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Time', 'transaction_time', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Payment', 'payment_method', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Cogs', 'cogs', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Gross margin percentage', 'gross_margin_percentage', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.gross income', 'profit', 'COLUMN'
GO

EXEC sp_rename 'dbo.supermarket_sales.Rating', 'rating', 'COLUMN'
GO;

```

#### Checking for missing values

```sql

SELECT *
FROM dbo.supermarket_sales
WHERE invoice_id IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE branch IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE city IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE customer_type IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE gender IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE product_line IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE unit_price IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE quantity IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE tax_five_percent IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE order_value IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE transaction_date IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE transaction_time IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE payment_method IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE cogs IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE gross_margin_percentage IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE profit IS NULL
GO

SELECT *
FROM dbo.supermarket_sales
WHERE rating IS NULL
GO;

```

#### Changing format of the transaction_date variable to date format

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN transaction_date date;

```

#### Changing format of transaction_time variable to time format

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN transaction_time time(0);

```

#### Creating a weekday variable from the transaction_date column

```sql

SELECT *
,DATENAME(weekday,transaction_date) AS week_day
FROM dbo.supermarket_sales;

```

#### Creating time_of_day variable from transaction_time column

```sql

SELECT *
     , CASE WHEN transaction_time <  '06:00' THEN 'Night'
            WHEN transaction_time >= '18:00' THEN 'Night'
            ELSE 'Day' END AS time_of_day
FROM  dbo.supermarket_sales;

```

#### Creating month variable from transaction_date:

```sql

SELECT *
,
  MONTH(dbo.supermarket_sales.transaction_date) AS month
FROM dbo.supermarket_sales;

```

#### Changing data format of order_value to decimal:

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN order_value decimal;

```

#### Changing data format of profit to decimal:

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN profit decimal;

```

#### Changing data format of unit_price to decimal

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN unit_price decimal;

```

#### Changing data format of quantity to int

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN quantity int;

```

#### Changing data format of tax_five_percent to decimal

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN tax_five_percent decimal(5,2);

```

#### Changing the format of cogs to decimal

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN cogs decimal;

```

#### Changing data format of gross_margin_percentage to decimal

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN gross_margin_percentage decimal;

```

#### Changing data format of rating to int

```sql

ALTER TABLE dbo.supermarket_sales
ALTER COLUMN rating int;

```

---

#### Now on to the exploratory phase : 

**Q1** : Determine the best performing product line, both in total sales and in profit margin

```sql

SELECT product_line,
	SUM(order_value) AS total_order_value,
	SUM(profit) AS total_profit
FROM dbo.supermarket_sales
GROUP BY product_line;

```
