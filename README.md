# 8-Weeks-SQL-Challenge
## Case Study 1: Danny's Diner

![Danny ma](https://github.com/user-attachments/assets/9002e488-a2ab-4a7f-8abc-044e7dbc3028)


This is a case study created by Dannny Ma. You can find all the details of this challenge [here](https://8weeksqlchallenge.com/case-study-1/).
I used SQL Server, and these are the functions explored:
- Common Table Expressions (CTE)
- Aggregate Functions (Count, Sum)
- SQL Operators and clauses
- Window Functions
- Joins
### Introduction
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

### Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:
- Sales
- Menu
- Members

### Entity Relationship Diagram


![CB05573E-DAAD-4A3F-A531-B971C765F657](https://github.com/user-attachments/assets/be907571-75ae-40a1-938c-9ec2e46b415f)


### Case Study Questions
1. What is the total amount each customer spent at the restaurant?

- I created a database, tables and inserted the values.

  ```CREATE DATABASE 8_Weeks_SQL_Challenge
  
  CREATE TABLE SALES (
  Customer_id VARCHAR(1) not null,
  Order_date DATE,
  Product_id INT
)

Insert into SALES (Customer_id, Order_date, Product_id)
VALUES	('A', '2021-01-01', '1'),
		('A', '2021-01-01', '2'),
		('A', '2021-01-07', '2'),
		('A', '2021-01-10', '3'),
		('A', '2021-01-11', '3'),
		('A', '2021-01-11', '3'),
		('B', '2021-01-01', '2'),
		('B', '2021-01-02', '2'),
		('B', '2021-01-04', '1'),
		('B', '2021-01-11', '1'),
		('B', '2021-01-16', '3'),
		('B', '2021-02-01', '3'),
		('C', '2021-01-01', '3'),
		('C', '2021-01-01', '3'),
		('C', '2021-01-07', '3');

CREATE TABLE MENU (
  Product_id INT,
  Product_name VARCHAR (5),
  Price INT
)

INSERT INTO menu (Product_id, Product_name, Price)
VALUES	('1', 'Sushi', '10'),
		('2', 'Curry', '15'),
		('3', 'Ramen', '12')

CREATE TABLE MEMBERS (
  Customer_id VARCHAR (1),
  Join_date DATE
)

INSERT INTO MEMBERS (Customer_id, Join_date)
VALUES	('A', '2021-01-07'),
		('B', '2021-01-09')

