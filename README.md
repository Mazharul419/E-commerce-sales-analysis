# E-commerce-sales-analysis

This project is my first project using SQL and PowerBI. The data consists of information on the customers, sales and products for an E-commerce business in the United States between 2014-2017. The aim of this project is to use both data analysis tools to deliver business insight and demonstrate my knowledge gained through learning both tools online via Udemy.

The objectives are to answer the following

### 1. Customer analysis

*Who are the 10 largest customers by total sales?*

### 2. Cities analysis ##

*Create a report showing the top 3 cities per state ranked by their unique orders*

### 3. Seasonality analysis

*Analyse the Chair sub-category to determine if there seasonality in the sales.*

### 4. Dashboard in PowerBI
*The Sales Manager will be heading to a meeting with the Directors to review overall sales. Create a dashboard in PowerBI with key Sales data including highest and lowest sale, highest and lowest profit and graphs for 10 largest customers by total sales and the sales in the top 3 cities of the top 4 states.*

### 5. Recommendations

*Finally make recommendations based on the above how the business can improve its sales.*

# Creating Database and importing CSV file
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

![image.png](attachment:2809c2f6-839e-473b-8434-6eff41c354d0.png)

Product table

![image.png](attachment:65f680d8-f84d-4a92-a40a-40ee7b64b918.png)

Sales table

![image.png](attachment:ee628834-69ca-437c-abe3-7cd75ed989a4.png)

A back-up database .SQL file was also created in a similar file location as the CSVs in the event of data corruption.
