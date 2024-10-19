# HEVO Data Assignment 
Step 1: Install PostgreSQL (PGSQL) Using Docker
	1.Install Docker if you don’t already have it on your machine.
	2.Pull the PostgreSQL Docker image and run a container:
		docker pull postgres
		docker run --name postgres-container -e POSTGRES_PASSWORD=passpass -d postgres
	3.Once the container is running, access it via psql (PostgreSQL’s command-line tool):
		docker exec -it postgres-container psql -U postgres

Step 2: Create Tables in PostgreSQL and Load CSV Data
	1.Create the required tables (raw_orders, raw_customers, and raw_payments) in your PostgreSQL database by connecting to your instance:
		-- Drop and create raw_customers table
		DROP TABLE IF EXISTS raw_customers;

		CREATE TABLE raw_customers (
			id SERIAL PRIMARY KEY,
			first_name VARCHAR(100),
			last_name VARCHAR(100)
		);

		-- Drop and create raw_orders table
		DROP TABLE IF EXISTS raw_orders;

		CREATE TABLE raw_orders (
			id SERIAL PRIMARY KEY,
			user_id INT,
			order_date DATE,
			status VARCHAR(50)
		);

		-- Drop and create raw_payments table
		DROP TABLE IF EXISTS raw_payments;

		CREATE TABLE raw_payments (
			id SERIAL PRIMARY KEY,
			order_id INT,
			payment_method VARCHAR(50),
			amount DECIMAL
		);

	2. Import raw_customers,raw_orders,raw_payments to pgsql.
Step 3: Create Snowflake Account and Once set up, create a database and warehouse in Snowflake:
			CREATE DATABASE hevo_exercise;
			CREATE WAREHOUSE hevo_wh;
Step 4: 	Create Pipeline in hevo data
Step 5: DBT Code 
WITH raw_customers AS (
  SELECT *
  FROM {{ ref('raw_customers') }}
),
raw_orders AS (
  SELECT *
  FROM {{ ref('raw_orders') }}
),
raw_payments AS (
  SELECT *
  FROM {{ ref('raw_payments') }}
)

SELECT
  rc.customer_id,
  rc.customer_name,
  rc.customer_email,
  COUNT(ro.order_id) AS total_orders,
  SUM(ro.order_amount) AS total_order_amount,
  COUNT(rp.payment_id) AS total_payments,
  SUM(rp.payment_amount) AS total_payment_amount
FROM raw_customers rc
LEFT JOIN raw_orders ro ON rc.customer_id = ro.customer_id
LEFT JOIN raw_payments rp ON ro.order_id = rp.order_id
GROUP BY rc.customer_id, rc.customer_name, rc.customer_email;




		

