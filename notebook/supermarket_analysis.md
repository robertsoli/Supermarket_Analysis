# Supermarket Case Study by Oliver Roberts

---

### Project Overview

This data analysis project aims to provide insights into customer trends, sales patterns and client satisfaction in order to drive informed decision making for a Supermarket.

The growth of supermarkets in highly populated cities are increasing and competition is high. Therefore using data to derive a deeper understanding of customer behaviour is of the utmost importance to ensure sales growth and customer satisfaction. This dataset consists of historical sales of a supermarket which was recorded in 3 different branches over a period of 3 months.

---

### Defining the business tasks

**Q1** : Determine the best performing product line, both in total sales and in profit margin over the first quarter of 2019


**Q2** : Determine the peak hour of sales during the day to inform staff lunch breaks and over/understaffed hours


**Q3** : Determine the peak weekday for sales, as well as lowest performing day to determine when to run promotions and discounts


**Q4** : Explore customer ratings to determine the best rated products, and the worst rated products 


**Q5** : Calculate the sales total by location


**Q6** : Determine ratings by location


**Q7** : Calculate sales by payment method to see if there is a disproportionate payment method


**Q8** : Product line sales split by gender to determine which product lines sell more commonly to a certain gender


**Q9** : Determine profit by customer type, to determine whether to motivate non members to attain membership


---

### Data Sources

This publicly available data was obtained on Kaggle [here](https://www.kaggle.com/datasets/lovishbansal123/sales-of-a-supermarket)

The table used in this case study is supermarket_sales.csv

---

### Tools used in analysis

- Microsoft SQL Server Management Studio 20, using T-SQL for cleaning tasks, exploratory data analysis and manipulation
- Tableau for charts and a dashboard

---

### Data Cleaning and EDA 

In the initial data preparation phase, the following data cleaning tasks are to be done: 

- Renaming columns to improve readability and avoid clashing with functions
- Checking for missing values
- Checking that data formatting is correct for variables

The following manipulation also need to be done:

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

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/2f42f145-be8f-4ff1-b8ab-bb83a34d4037)

---

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

ALTER TABLE dbo.supermarket_sales
ADD week_day AS DATENAME(weekday,transaction_date);

```

#### Creating time_of_day variable from transaction_time column

```sql

ALTER TABLE dbo.supermarket_sales ADD time_of_day VARCHAR(50)
GO

UPDATE dbo.supermarket_sales set time_of_day =
       (CASE WHEN transaction_time <  '08:00' THEN 'Night'
        WHEN transaction_time >= '18:00' THEN 'Night'
        ELSE 'Day' END);

```

#### Creating month variable from transaction_date:

```sql

ALTER TABLE dbo.supermarket_sales
ADD month_name_full  AS DATENAME(MONTH, transaction_date);

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
GROUP BY product_line
ORDER BY total_order_value DESC;

```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/fc4e80d1-b856-444d-b481-38fd52161130)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8c93c451-df28-4f00-8db5-bed99b972e65)

---

**Q2** : Determine the peak hour of sales during the day

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8f34ff07-238a-4317-af83-a25ec334a185)

---

**Q3** : Determine the peak weekday for sales

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/be5d8507-98cf-4165-929c-28ac1e8b6bc5)

---

**Q4** : Explore customer ratings to determine the best and worst rated product lines

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/334a2d2f-1dfc-4571-acb6-2cfcd17f77b4)


---

**Q5** : Calculate total sales by branch

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/17f62ef0-cec0-426a-b2d2-81331a93816d)

---

**Q6** : Determine ratings by branch

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/3297d5b5-da03-40c1-a677-e7b34c6fe0d7)

---

**Q7** : Calculate total sales by payment method 

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8d2979b5-b2da-4f2b-b77a-57abab4cb916)

---

**Q8** : Product line performance calculated by gender

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8d5ca9b9-f840-442e-a237-5f9e83669de4)

---

**Q9** : Determine profit by customer type

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/3e76e99f-fa5d-4972-93b0-88ed20e0c57c)

---
