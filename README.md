# SQL Sales Analysis Project

## Overview

This project contains a collection of SQL queries used to analyze customer, product, and sales data from a data warehouse schema. The analysis focuses on business insights such as revenue generation, customer distribution, product performance, and time-based sales trends.

The SQL script performs exploratory data analysis (EDA) and business intelligence reporting using tables from the `gold` schema.

---

## Database Schema

The project uses the following tables:

### 1. `gold.dim_customers`

Contains customer-related information.

**Key Columns:**

* `customer_key`
* `first_name`
* `last_name`
* `gender`
* `country`

### 2. `gold.dim_products`

Contains product details.

**Key Columns:**

* `product_key`
* `category`
* `cost`

### 3. `gold.fact_sales`

Contains transactional sales records.

**Key Columns:**

* `order_date`
* `customer_key`
* `product_key`
* `sales_amount`
* `quantity`
* `price`

---

# Project Objectives

The SQL queries in this project are designed to:

* Explore customer and product data
* Analyze product categories
* Measure revenue generation
* Study customer demographics
* Track sales trends over time
* Perform cumulative and performance analysis
* Generate business insights for decision-making

---

# SQL Analysis Included

## 1. Data Exploration

Basic queries to inspect all records from:

```sql
SELECT * FROM gold.dim_customers;
SELECT * FROM gold.dim_products;
SELECT * FROM gold.fact_sales;
```

---

## 2. Product Category Analysis

### Find Total Products by Category

```sql
SELECT category,
       COUNT(product_key) AS total_products
FROM gold.dim_products
WHERE category IS NOT NULL
GROUP BY category
ORDER BY total_products DESC;
```

### Insights

* Identifies the most populated product categories
* Helps understand product distribution

---

## 3. Customer Demographics Analysis

### Total Customers by Country

```sql
SELECT country,
       COUNT(customer_key) AS total_customers
FROM gold.dim_customers
GROUP BY country
ORDER BY total_customers DESC;
```

### Total Customers by Gender

```sql
SELECT gender,
       COUNT(customer_key) AS total_customers
FROM gold.dim_customers
GROUP BY gender
ORDER BY total_customers DESC;
```

### Insights

* Understands customer distribution across countries
* Identifies gender-based customer segmentation

---

## 4. Product Cost Analysis

### Average Cost by Category

```sql
SELECT category,
       AVG(cost) AS avg_cost
FROM gold.dim_products
GROUP BY category
ORDER BY avg_cost DESC;
```

### Insights

* Helps identify premium product categories
* Supports pricing and procurement decisions

---

## 5. Revenue Analysis

### Total Revenue by Product Category

```sql
SELECT p.category,
       SUM(f.sales_amount) AS total_revenue
FROM gold.fact_sales f
LEFT JOIN gold.dim_products p
       ON p.product_key = f.product_key
GROUP BY p.category
ORDER BY total_revenue DESC;
```

### Total Revenue by Customer

```sql
SELECT c.customer_key,
       c.first_name,
       c.last_name,
       SUM(f.sales_amount) AS total_revenue
FROM gold.fact_sales f
LEFT JOIN gold.dim_customers c
       ON c.customer_key = f.customer_key
GROUP BY c.customer_key,
         c.first_name,
         c.last_name
ORDER BY total_revenue DESC;
```

### Insights

* Identifies top-performing categories
* Helps recognize high-value customers

---

## 6. Time-Based Sales Analysis

### Yearly Sales Performance

```sql
SELECT YEAR(order_date) AS order_year,
       SUM(sales_amount) AS total_sales,
       COUNT(DISTINCT customer_key) AS total_customers,
       SUM(quantity) AS total_quantity
FROM gold.fact_sales
WHERE order_date IS NOT NULL
GROUP BY YEAR(order_date)
ORDER BY YEAR(order_date);
```

### Monthly Sales Performance

```sql
SELECT YEAR(order_date) AS order_year,
       MONTH(order_date) AS order_month,
       SUM(sales_amount) AS total_sales,
       COUNT(DISTINCT customer_key) AS total_customers,
       SUM(quantity) AS total_quantity
FROM gold.fact_sales
WHERE order_date IS NOT NULL
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY YEAR(order_date), MONTH(order_date);
```

### Using `DATETRUNC`

```sql
SELECT DATETRUNC(YEAR, order_date) AS order_date,
       SUM(sales_amount) AS total_sales,
       COUNT(DISTINCT customer_key) AS total_customers,
       SUM(quantity) AS total_quantity
FROM gold.fact_sales
WHERE order_date IS NOT NULL
GROUP BY DATETRUNC(YEAR, order_date)
ORDER BY DATETRUNC(YEAR, order_date);
```

### Using `FORMAT`

```sql
SELECT FORMAT(order_date, 'yyyy-MMM') AS order_date,
       SUM(sales_amount) AS total_sales,
       COUNT(DISTINCT customer_key) AS total_customers,
       SUM(quantity) AS total_quantity
FROM gold.fact_sales
WHERE order_date IS NOT NULL
GROUP BY FORMAT(order_date, 'yyyy-MMM')
ORDER BY FORMAT(order_date, 'yyyy-MMM');
```

### Insights

* Tracks yearly and monthly business growth
* Measures customer engagement over time
* Monitors sales quantity trends

---

## 7. Cumulative Analysis

### Running Total Sales

```sql
SELECT order_date,
       total_sales,
       SUM(total_sales) OVER (
           PARTITION BY order_date
           ORDER BY order_date
       ) AS running_total_sales
FROM
(
    SELECT DATETRUNC(month, order_date) AS order_date,
           SUM(sales_amount) AS total_sales
    FROM gold.fact_sales
    WHERE order_date IS NOT NULL
    GROUP BY DATETRUNC(month, order_date)
) t;
```

### Moving Average Price

```sql
SELECT order_date,
       total_sales,
       SUM(total_sales) OVER (
           PARTITION BY order_date
           ORDER BY order_date
       ) AS running_total_sales,
       AVG(avg_price) OVER (
           ORDER BY order_date
       ) AS moving_average_price
FROM
(
    SELECT DATETRUNC(year, order_date) AS order_date,
           SUM(sales_amount) AS total_sales,
           AVG(price) AS avg_price
    FROM gold.fact_sales
    WHERE order_date IS NOT NULL
    GROUP BY DATETRUNC(year, order_date)
) t;
```

### Insights

* Calculates cumulative sales growth
* Tracks moving average pricing trends
* Uses SQL window functions for advanced analytics

---

## 8. Performance Analysis

The project also begins yearly product performance analysis by comparing:

* Current year sales
* Average sales performance
* Previous year sales

This section uses:

* Common Table Expressions (CTEs)
* Window functions
* Year-over-year comparisons

---

# SQL Concepts Used

The project demonstrates the following SQL concepts:

* `SELECT`
* `WHERE`
* `GROUP BY`
* `ORDER BY`
* Aggregate Functions

  * `COUNT()`
  * `SUM()`
  * `AVG()`
* `JOIN`
* `LEFT JOIN`
* Window Functions

  * `OVER()`
  * `PARTITION BY`
* Date Functions

  * `YEAR()`
  * `MONTH()`
  * `DATETRUNC()`
  * `FORMAT()`
* Common Table Expressions (CTEs)

---

# Tools & Technologies

* SQL Server / T-SQL
* Data Warehouse Schema
* Business Intelligence Analysis

---

# How to Run

1. Open SQL Server Management Studio (SSMS) or any SQL editor.
2. Connect to your database.
3. Execute the SQL script file:

```bash
SQLQuery1.sql
```

4. Review the query outputs for business insights.

---

# Use Cases

This project can be used for:

* Portfolio projects
* Data analyst practice
* SQL interview preparation
* Business intelligence reporting
* Sales trend analysis
* Customer analytics

---

# Future Improvements

Potential enhancements:

* Dashboard integration with Power BI or Tableau
* Advanced KPI reporting
* Predictive sales analysis
* Customer segmentation models
* Product profitability analysis

---

# Author

Created as part of a SQL analytics and business intelligence practice project.
