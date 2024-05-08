# Data Analytics Project

---

![image](https://github.com/robertsoli/Supermarket_Analysis/assets/156069037/57e16eea-fef1-435f-824f-fbdc9acc3de3)

---

## Supermarket Analysis

---

#### Project Overview

As part of the learning journey, this project is based on a fictional supermarket's data and aims to provide insights into customer purchases, product performance and recommendations for optimizing the way the business operates. 

---

#### Codes and Resources Used

The following tools and software were used in creating this project :

 - Microsoft SQL Server Management Studio 20 for all cleaning and EDA
 - Tableau Public for charts and visualizations

---

#### Installation and Setup

- Tableau Public was used to host the completed charts, so it can be viewed without the need to download software
- Github was used to host the project publically, so there is no need for the viewer to download any software there either

---

#### Data Structure

The files name is supermarket_sales.csv and its structure is as follows: 

 - 1000 observations

 - 17 variables namely 

  - **Invoice id**: Computer generated sales slip invoice identification number.

  - **Branch**: Branch of supercenter (3 branches are available identified by A, B and C).

  - **City**: Location of supercenters.

  - **Customer type**: Type of customers, recorded by Members for customers using member card and Normal for without member card.

  - **Gender**: Gender type of customer.

  - **Product line**: General item categorization groups - Electronic accessories, Fashion accessories, Food and beverages, Health and beauty, Home and lifestyle, Sports and travel.

  - **Unit price**: Price of each product in $.

  - **Quantity**: Number of products purchased by customer.

  - **Tax**: 5% tax fee for customer buying.

  - **Total**: Total price including tax.

  - **Date**: Date of purchase (Record available from January 2019 to March 2019).

  - **Time**: Purchase time (10am to 9pm).

  - **Payment**: Payment used by customer for purchase (3 methods are available – Cash, Credit card and Ewallet).

  - **COGS**: Cost of goods sold.

  - **Gross margin percentage**: Gross margin percentage.

  - **Gross income**: Gross income.

  - **Rating**: Customer stratification rating on their overall shopping experience (On a scale of 1 to 10).

---

#### Data Preprocessing

All cleaning and manipulation tasks were done in Microsoft Sql Server Management Studio 20.

**Cleaning tasks** :

 - Renaming columns to improve readability and avoid clashing with functions
 - Checking for missing values
 - Checking for duplicate entries

**Data Manipulation** :

 - Creating new variables needed in analysis
 - Changing data formatting for existing variables

---

#### Citations

The following [dataset](https://www.kaggle.com/datasets/lovishbansal123/sales-of-a-supermarket) was obtained on the platform Kaggle, made public by the [owner](https://www.kaggle.com/lovishbansal123)


