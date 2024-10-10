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
**1. What is the total amount each customer spent at the restaurant?**

- I created a database, tables and inserted the values respoectively.

  ```
  CREATE DATABASE 8_Weeks_SQL_Challenge
  
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
  ```
- I then created a view table to combine the 3 tables together, which helped me in answering the first question easily.
```
Create view VW_DannyDinner_TBL
as 
Select SALES.Customer_id, SALES.Order_date, MENU.Product_name, MENU.Price,
	CASE
		WHEN MEMBERS.Customer_id IS NOT NULL 
			THEN 'Y'
		ELSE 'N'
	END AS Membership
from SALES
full join MENU
on MENU.Product_id = SALES.Product_id
full join MEMBERS
on MEMBERS.Customer_id = SALES.Customer_id
```
THE TABLE IMAGE FOR COMBO

I used the new table created ``` VW_DannyDinner_TBL ``` to **SUM** up the order ```price``` made per customer and **GROUP**ed by the ``` Customer_id ```
```
select customer_id, sum(price) as TotalTransactionPerCustomer from [dbo].[VW_DannyDinner_TBL]
group by Customer_id
```
Answer
| Customer_id | TotalTransactioPerCustomer |
| - | - |
| A | 76 |
| B | 74| 
| C | 36 |

![1](https://github.com/user-attachments/assets/57d066a6-af25-4c33-90aa-feb74687feb6)

**- Customer A, B, and C spent $76, $74, and $36 respectively, which also means that Customer A spent the most**

2. How many days has each customer visited the restaurant?

- A customer can make more than one order in a day, so a day (visit) can have multiple enteries in the data. Hence i used the **DISTINCT** function to **COUNT** the number of days visited by each customer, while still referencing ```VW_DannyDinner_TBL```
```
select customer_id, sum(price) as TotalTransactionPerCustomer from [dbo].[VW_DannyDinner_TBL]
group by Customer_id
```
Answer
| Customer_id | TotalNumberVisisted |
| - | - |
| A | 4 |
| B | 6| 
| C | 2 |

![2](https://github.com/user-attachments/assets/df6858f4-2678-48b3-afea-9dd31615dcda)

**- Customer B visited Danny Diner the most at 6 times**

3. What was the first item from the menu purchased by each customer?
- I basically just need to rank the orders against the customers. To do this i made use of **CTE** to create a temporary table result set **RankedOrders** to rank the orders by their ```Order_date``` using **ROW_NUMBER** function, which will further rank all orders by each customer from the earliest (1) to the latest.
- Then limit the output to display only those ranked at 1

```
With RankedOrders 
AS(
	Select customer_id, Product_name, 
	ROW_NUMBER() over(Partition by customer_id Order by Order_date)
AS ROWNUMBER
From [dbo].[VW_DannyDinner_TBL]
)
Select customer_id, Product_name from RankedOrders Where ROWNUMBER = 1
```
![3](https://github.com/user-attachments/assets/49622982-fcf7-4e2f-988d-fa2d3a8584eb)

**- Customer A's first orders are Curry and Sushi**
**- Customer B's first order is Curry**
**- Customer C's first order is Ramen**

4. What is the most purchased item on the menu and how many times was it purchased by all customers?

- I made used of the ```VW_DannyDinner_TBL``` table created earlier, **COUNT** by each product, **GROUP**, and **ORDER** the products in descending order

```
SELECT Product_name, COUNT(Product_name) AS Max_CountOfPurchase
FROM [dbo].[VW_DannyDinner_TBL]
Group by Product_name
Order by Count (Product_name) Desc
Top 1
```
![4](https://github.com/user-attachments/assets/fa0a4ca6-5774-4557-bbca-4f68caa6e56e)


5. Which item was the most popular for each customer?
- Remember **CTE**?, I created a temporary table to count the ```Product_name```, i then used the **RANK** function to rank and separate(***PARTITION***) the rows of the products and **COUNT** in descending order as **ROWNUMBER** from ```VW_DannyDinner_TBL``` table with respect to their products.
- Then limit the output to display Only products ranked 1 as the most popular products for each customer.

```
With PopularProducts 
AS(
	Select customer_id, Product_name, Count(Product_name) AS Order_Count,
	Rank() over(Partition by customer_id Order by count(Product_name) Desc)
AS ROWNUMBER
From [dbo].[VW_DannyDinner_TBL] Group by Customer_id, Product_name
) vdxdddfdffddffdvfd
Select customer_id, Product_name, Order_Count from PopularProducts Where ROWNUMBER = 1
```
![5](https://github.com/user-attachments/assets/df858a84-d8a2-4b44-9f0a-73e3954b85ea)

6. Which item was purchased first by the customer after they became a member?
```
With FirstProduct 
AS(
	Select customer_id, Product_name, Count(Product_name) AS Order_Count,
	Rank() over(Partition by customer_id Order by count(Product_name) Desc)
AS ROWNUMBER
From [dbo].[VW_DannyDinner_TBL] Group by Customer_id, Product_name
)
Select customer_id, Product_name, Order_Count from FirstProduct Where ROWNUMBER = 1
```



7. Which item was purchased just before the customer became a member?

- I first created a view table to make all questions relating to rank easier to answer.
```
Create view VW_Ranking_Tbl
AS
Select S.Order_date, Mr.Customer_id, Mr.Join_date, M.Product_name, M.Price, M.Product_id
	From SALES s
	Full Join MEMBERS Mr On S.Customer_id = Mr.Customer_id
	Full Join MENU M On S.Product_id = M.Product_id

	Select S.Order_date, Mr.Customer_id, Mr.Join_date, M.Product_name, M.Price, M.Product_id
	From SALES s
	Join MEMBERS Mr On S.Customer_id = Mr.Customer_id
	Join MENU M On S.Product_id = M.Product_id
```
![7 (1)](https://github.com/user-attachments/assets/bb284621-2361-4dcd-a098-7add229e2005)

- I then proceed to answer the question with reference to the newly created table ```Create view VW_Ranking_Tbl```

```
With RankedOrders AS (
Select* ,
	Dense_Rank() Over(Partition by Customer_id Order by order_date Desc) AS previous_Product
	From [dbo].[VW_Ranking_Tbl]
	)
	Select Customer_id, Order_date, Product_name
	From RankedOrders
Where Order_date < Join_date
Order by Order_date Desc
Top 1
```


8. What is the total items and amount spent for each member before they became a member?

```
Select Customer_id, COUNT(Distinct Product_name) AS Total_items,
SUM(Price) AS Amount_spent
From [dbo].[VW_Ranking_Tbl]
Where Order_date < Join_date
Group by Customer_id
```
![8](https://github.com/user-attachments/assets/61032f36-2eac-4425-95a4-fce0a29cae69)


9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

```
Select Customer_id, SUM(
	CASE
		WHEN Product_name = 'Sushi'
		THEN 20 * Price
	ELSE 10 * Price
	END
	)
AS Total_points From SALES s
left Join MENU M On S.Product_id = M.Product_id
Group by Customer_id
```
![9](https://github.com/user-attachments/assets/adadef66-6d64-478a-aaf4-e043d135b5ed)


10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi
