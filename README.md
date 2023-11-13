# E-commerce-sales-analysis

## Table of Contents
- [Project brief](#project-brief)
- [Objectives](#objectives)
- [Importing CSV file into PostgreSQL](#importing-csv-file-into-postgresql)
- [Customer analysis](#customer-analysis)
- [Cities analysis](#cities-analysis)


### Project brief
This project is my first project using SQL and PowerBI. The data consists of information on the customers, sales and products for an E-commerce business in the United States between 2014-2017. The aim of this project is to use both data analysis tools to deliver business insight and demonstrate my knowledge gained through learning both tools online via Udemy.

### Objectives
The objectives are the analyse the data through the following:

1. Customer analysis - Who are the 10 largest customers by total sales?*

2. Cities analysis - Create a report showing the top 3 cities per state ranked by their unique orders

3. Seasonality analysis - Analyse the Chair sub-category to determine if there seasonality in the sales.

4. Dashboard in PowerBI - The Sales Manager will be heading to a meeting with the Directors to review overall sales. Create a dashboard in PowerBI summarising the key metrics gained so far.

5. Recommendations - Make recommendations based on the above how the business can improve its sales.

### Importing CSV file into PostgreSQL
​
Before beginning any analysis it is important to load the data into Postgres SQL. To do so a new database named "Retail Sales" was created and clicking the query tool enters the command interface to runs commands needed to create and import the csv data into the relevant tables.
​
Once in the Query Tool tables are created using the CREATE TABLE command and data is imported from the csv files using the COPY FROM command for customer, product and sales. Below is the command for the customer table:

```sql
CREATE TABLE customer (
​
customer_id varchar primary key,
customer_name varchar,
segment varchar,
age int,
country varchar,
city varchar,
state varchar,
postal_code bigint,
region varchar);
​
COPY customer FROM 'C:\Program Files\PostgreSQL\15\data\1 Data Copy\SuperMart_DB\CSV files\Customer.csv' CSV header;
```
The data is now imported and looks fine. Using the SELECT * FROM command below are what each table look like:

Customer table

![Customer_table](https://github.com/Mazharul419/E-commerce-sales-analysis/assets/102329833/3f457344-dd69-4668-96e0-5ca98c17b6ab)

Product table

![Product_table](https://github.com/Mazharul419/E-commerce-sales-analysis/assets/102329833/3646cbd3-badd-4a69-98ce-5d82e66bbe28)

Sales table

![Sales_table](https://github.com/Mazharul419/E-commerce-sales-analysis/assets/102329833/1e72b9f1-678d-4049-a8bc-f28238500b1b)

A back-up database .SQL file was also created in a similar file location as the CSVs in the event of data corruption.

The data is now ready to be queried. Starting from the beginning:

### Customer analysis
Who are the 10 largest customers by total sales?

For this question the total sales value for each customer needs to be queried.

Most of the information required is in the sales table and will provide the top 5 total sales by customer id. However to understand who these customers are the associated customer name is also required. The JOIN function is used to extract the customer name from the customer table where the customer id matches the sales table. 

To obtain the total sales value the aggregate function SUM is used alongside the GROUP BY clause to obtain sales grouped by the customer id. It is then ordered by the total sales value using ORDER BY and finally LIMIT is used to give the top 5 results. 

Below is the input code: 
```sql
SELECT
	a.customer_name,
	b.customer_id,
	SUM(b.sales) AS total_sales
FROM customer AS a
RIGHT JOIN sales AS b
ON a.customer_id = b.customer_id
GROUP BY a.customer_name, b.customer_id
ORDER BY total_sales DESC
LIMIT 10;
```
Here is the output of the code:

![1_customer_analysis](https://github.com/Mazharul419/E-commerce-sales-analysis/assets/102329833/cfaeed96-81d5-4f19-9d46-383fcfca728b)

> The customer name, id and total sales is tallied for the top 5 customers.

### Cities analysis
Create a report showing the top 3 cities per state ranked by their unique orders.

Similar to the above scenario the required information needs to be queried from both customer and sales table. A similar method is employed using a LEFT JOIN to obtain data from both tables. Unique orders is required therefore COUNT and DISTINCT functions are used to obtain the number of these across each city. 

To rank these a windows function ROW_NUMBER is used to rank the top cities by order numbers within each state, saved as rank_n.

Finally since rank_n is needed to limit the cities to top 3 for each state however it is being defined in the same query, a sub-query is used to define, and then proceed to rank to avoid error.
```sql
SELECT* FROM
(SELECT 
	b.state, 
	b.city,
 	COUNT(DISTINCT(a.order_id)) AS order_num,
	row_number() over (partition by b.state order by (SELECT COUNT(DISTINCT(a.order_id)) AS order_num) desc) as rank_n
FROM sales as a
LEFT JOIN customer as b
on a.customer_id = b.customer_id
GROUP BY b.state,b.city) as c
WHERE c.rank_n<=3;
```
The output is as follows:

![2_cities_analysis](https://github.com/Mazharul419/E-commerce-sales-analysis/assets/102329833/9143e902-6aa3-4af2-9eca-4055ed36b895)

### Seasonality analysis
Analyse the Chair sub-category to determine if there seasonality in the sales.

For this the information required are month the order date falls in, the total sales during that month and a method to only select sub-category "Chair".

To obtain these the EXTRACT function is used to obtain the month from the order date, and the SUM function is once again used to obtain total sales. 

The above information is in the sales table and the Chair sub-category is in the product table. Both tables have product id in common. A subquery is used to only obtain product ids where the sub-category is in "Chair".
```sql
SELECT 
	EXTRACT(month FROM order_date) AS month_n, 
	SUM(sales) AS total_sales 
FROM sales
WHERE product_id IN (SELECT 
					 	product_id 
					 FROM product 
					 WHERE sub_category = 'Chairs')
GROUP BY month_n
ORDER BY month_n;
```
The result is as follows:

![3_seasonality_analysis](https://github.com/Mazharul419/E-commerce-sales-analysis/assets/102329833/eab75b29-c839-4a65-972c-453290d85c11)


