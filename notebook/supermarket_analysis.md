# Supermarket Case Study by Oliver Roberts

---

### Project Overview

This data analysis project aims to provide insights into customer trends, sales patterns and client satisfaction in order to drive informed decision making for a Supermarket.

The growth of supermarkets in highly populated cities are increasing and competition is high. Therefore using data to derive a deeper understanding of customer behaviour is of the utmost importance to ensure sales growth and customer satisfaction. This dataset consists of historical sales of a supermarket which was recorded in 3 different branches over a period of 3 months.

---

### Defining the business tasks

**Q1** : Determine the best performing product line, both in total sales and in profit margin over the first quarter of 2019.


**Q2** : Determine the peak hour of sales during the day.


**Q3** : Determine the peak and lowest performing weekdays per month for sales.


**Q4** : Explore customer ratings to determine the best rated product lines, and the worst rated product lines.


**Q5** : Explore monthly sales patterns per branch.


**Q6** : Explore ratings by branch.


**Q7** : Calculate sales by payment method.


**Q8** : Calculate sales by product line, split by gender. 


**Q9** : Determine profit by customer type.

---

### Data Sources

This publicly available data was obtained on Kaggle [here](https://www.kaggle.com/datasets/lovishbansal123/sales-of-a-supermarket)

The table used in this case study is supermarket_sales.csv

---

### Tools used in analysis

- Microsoft SQL Server Management Studio 20, using T-SQL for cleaning tasks, exploratory data analysis and manipulation
- Tableau for related charts and a dashboard

---

### Data Cleaning and EDA 

In the initial data preparation phase, the following data cleaning tasks are to be done: 

- Renaming columns to improve readability and avoid clashing with functions
- Checking for missing values

The following manipulation also need to be done:

- Creating new variables needed in analysis
- Changing data formatting for existing variables

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

```sql
SELECT COUNT(invoice_id) AS hourly_orders
FROM dbo.supermarket_sales
GROUP BY
DATEPART(HOUR, transaction_time) 
ORDER BY DATEPART(HOUR, transaction_time) 
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/40bb0081-5694-4e61-b98c-4af73e7bdd75)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8f34ff07-238a-4317-af83-a25ec334a185)

---

**Q3** : Determine the peak weekdays per month for sales

---

```sql
SELECT month_name_full, week_day, SUM(order_value) AS daily_sales
FROM dbo.supermarket_sales
WHERE month_name_full IS NOT NULL
GROUP BY month_name_full, week_day
ORDER BY 
	CASE WHEN month_name_full = 'January' THEN 1
             WHEN month_name_full = 'February' THEN 2
             WHEN month_name_full = 'March' THEN 3
             WHEN week_day = 'Monday' THEN 1
             END,
             daily_sales DESC
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/3f68f034-0a1a-48d2-9fb0-ccd7f87962e2)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/04d1816e-16f0-444a-b1e8-549c859d8ef9)

---

**Q4** : Explore customer ratings to determine the best and worst rated product lines

---

```sql
SELECT product_line,
COUNT (
CASE WHEN rating >= 7 then 'Positive' END) AS positive_rating,
COUNT (
CASE WHEN rating BETWEEN 5 AND 6 THEN 'Average' END) AS moderate_rating,
COUNT (
CASE WHEN rating < 5 THEN 'Negative' END) AS negative_rating  
FROM dbo.supermarket_sales
WHERE product_line IS NOT NULL
GROUP BY product_line
ORDER BY positive_rating DESC;
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/86e9c739-a1f5-4d2e-a4b2-438254dd2f27)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/334a2d2f-1dfc-4571-acb6-2cfcd17f77b4)


---

**Q5** : Explore monthly sales patterns per branch

---

```sql
SELECT city, month_name_full AS month_name, SUM(order_value) AS total_sales
FROM dbo.supermarket_sales
WHERE city IS NOT NULL
GROUP BY city, month_name_full
ORDER BY city
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/70a89d6d-d114-484a-9062-87b6a0d4fda1)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/17f62ef0-cec0-426a-b2d2-81331a93816d)

---

**Q6** : Explore ratings by branch

---

```sql
SELECT city,
COUNT (
CASE WHEN rating >= 7 then 'Positive' END) AS positive_rating,
COUNT (
CASE WHEN rating BETWEEN 5 AND 6 THEN 'Average' END) AS moderate_rating,
COUNT (
CASE WHEN rating < 5 THEN 'Negative' END) AS negative_rating  
FROM dbo.supermarket_sales
WHERE city IS NOT NULL
GROUP BY city
ORDER BY positive_rating DESC;
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/61923a3d-07b3-49ea-a3a3-e5b6e5bfabac)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/3297d5b5-da03-40c1-a677-e7b34c6fe0d7)

---

**Q7** : Calculate total sales by payment method 

---

```sql
SELECT payment_method, SUM(order_value) AS sales_total
FROM dbo.supermarket_sales
WHERE payment_method IS NOT NULL
GROUP BY payment_method
ORDER BY sales_total DESC
```
---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/dd1bf87f-20ad-4b1c-86dd-dfff9c7e0c2f)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8d2979b5-b2da-4f2b-b77a-57abab4cb916)

---

**Q8** : Product line performance calculated by gender

---

```sql
SELECT product_line, gender, SUM(order_value) AS total_sales
FROM dbo.supermarket_sales
WHERE product_line IS NOT NULL
GROUP BY product_line, gender
ORDER BY product_line, gender, total_sales DESC
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/dfd8afcb-82c4-4d9a-981c-80e876520383)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/8d5ca9b9-f840-442e-a237-5f9e83669de4)

---

**Q9** : Determine profit by customer type

---

```sql
SELECT customer_type, month_name_full, SUM(profit) AS total_profit
FROM dbo.supermarket_sales
WHERE customer_type IS NOT NULL
GROUP BY customer_type, month_name_full
ORDER BY 
CASE	WHEN month_name_full = 'January' THEN 1
		WHEN month_name_full = 'February' THEN 2
		WHEN month_name_full = 'March' THEN 3
		END
		, customer_type;
```

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/21e3ab73-f4ca-4150-9f15-61e5cdb67a16)

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/a6c51b02-de55-4b87-9de4-11fb4815fc7f)

---

#### Descriptive Analysis and Recommendations

For business task **Q1** : Determine the best performing product line, both in total sales and in profit margin

- The top performing product line is Food and Beverages, which is to be expected from a supermarket providing daily produce for customers.

- With profit margin being identical across product lines, Food and Beverages also brings in the most profit for the business.

> [!NOTE]
> There is not a drastic difference in product line performance with the top 5 product lines being within $4K of the top over a 3 month period

#### Recommendations

- Ensure accurate stock ordering of the Food and Beverage product line to make sure there is always stock.

- Look into why the Health and Beauty product line is the lowest performing range of products. If it is pricing then offer discounts, if it is product quality or range then a focus on sourcing should be looked at.

---

For business task **Q2** : Determine the peak hour of sales during the day

- Peaks in orders occur at 10AM, 1PM, 3PM and at the highest point 7PM
  
- Dips in customer purchases occur at 2PM and 5PM

- Possible reasons for peak times include:
  
	- 10AM : Customers purchasing daily consumables like bread and milk.
   	- 1PM : Conventional work lunch break.
   	- 3PM : Unconventional work lunch break, or customers wanting to dodge the other busy times of the day.
   	- 7PM : Post work shopping and/or dinner consumables.

#### Recommendations

- The opening time of 10AM is quite late according to industry norm, opening shop at 7AM would no doubt boost revenue.

- Scheduling of breaks for employees should be centred around these busy periods of the day.

- Restocking of shelves, deliveries and stock checks should be conducted during the least busy hours, namely 2PM and 5PM.

- Ensure security staff are at attention during the busiest hours to minimize losses

---

For business task **Q3** : Determine the peak weekdays per month for sales

- Above average peaks are observed on Tuesday, Thursday and Saturday in January.

- Above average peaks are observed on Friday and Sunday in February.

- Above average peaks are observed on Tuesday, Friday and Saturday in March.

#### Recommendations

- Plan staff shifts to ensure the shop is fully staffed on the peak days observed.

- Ensure that staff members off days are on the quietest days.

- These data could be used to inform the buying department on when to schedule stock deliveries during quieter days.

---

For business task **Q4** : Explore customer ratings to determine the best and worst rated product lines

- The Home and Lifestyle product line received the most 4 star ratings and the least 10 star ratings.

- The Fashion Accessories product line received the most 10 star ratings and the least 4 star ratings.

- The distribution of positive, moderate and negative ratings is fairly even amongs the remaining product lines. 


#### Recommendations

- Take a deeper look into the specific ratings, and determine if there is a common thread among user experiences to inform strategy. 

- If there are problem products in the Home and Lifestyle product line that are leading to the negative ratings, consider replacing them with superior products.

- Assign a staff member to investigate negative ratings, in an attempt to resolve these experiences for clients.

---

For business task **Q5** : Explore monthly sales patterns per branch

- Naypyitaw had the highest monthly sales total for the first quarter, being January at $ 40432.

- Yangon had the largest dip in monthly sales, being the lowest performing month of February at $ 29856, but had the highest monthly sales for March at $ 37663.

- Mandalay shows the most consistent sales pattern for the first quarter, and the highest sales for February of the three branches at $ 34421.

#### Recommendations

- February is the poorest performing month for sales across the board, which needs to be investigated and addressed. Looking into the products sold and operational efficiency at the Mandalay branch during this month would be a good starting point, as they had the best February of the three branches. 

- Considering that the highest monthly sales for January and March are achieveable at the Naypyitaw and Yangon branches, this would suggest that it should be possible to remedy the poor sales experienced at these branches during the month of February. 

- Possible reasons may include : Insufficient marketing spend during February, customers having less capital post holiday period, tourism being at a low during that month, product availability during February. Ideally, more data are needed to answer the specific question of why.

--- 

For business task **Q6** : Explore ratings by branch

- Yangon had the highest number of Positive ratings, with 210 being between 7 and 10 stars.

- Naypyitaw had the least Negative ratings, with 25 ratings of 4 stars, and 199 Positive ratings between 7 and 10 stars. 

- Mandalay had the poorest performance of the three branches, with 30 Negative ratings of 4 stars and 118 Moderate ratings between 5 and 6 stars. 

#### Recommendations

- Investigate operational differences between Yangon and Mandalay branches, to determine how to improve the number of positive ratings for the Mandalay branch.

- Investigate operational differences between Naypyitaw and Mandalay branches, to determine how to reduce the number of negative ratings for the Mandalay branch.

- As with the product line ratings, the answer will be in the details of the customer ratings, so investigating them individually would yield actionable insights.

---

For business task **Q7** : Calculate sales by payment method

- Cash purchases accounted for 35% of sales.

- Ewallet purchases accounted for 34% of sales.

- Credit Card purchases accounted for 31% of sales.

#### Recommendations

- Incentivize customers to use cash to avoid credit card cost to company.

- 





  
