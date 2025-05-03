# **Postgres_sql_studied_queries**


# 7- Advanced Union & subqueries

# union
```markdown
SELECT first_name FROM actor
UNION 
SELECT first_name FROM customer
order by first_name
```
# union all
```markdown

SELECT first_name FROM actor
UNION ALL
SELECT first_name FROM customer
order by first_name
```

# union all
```markdown
SELECT first_name, 'actor' as origin FROM actor
UNION ALL
SELECT first_name, 'customer' FROM customer
order by first_name
```

# subqueires in where
```markdown
SELECT * from payment 
where customer_id=( select customer_id from customer where first_name='ADAM')
```
````
SELECT * from FILM
WHERE length > (select avg(length)from film)
````

````
SELECT * from FILM
WHERE  film_id In
(select film_id from inventory
where store_id=2
group by film_id
Having count(*) >3)
````
````
SELECT first_name, last_name from customer
WHERE  customer_id In
(select customer_id from payment
where Date(payment_date)='2020-01-25')

````

# subqueires in FROM
```
SELECT Round(AVG(total_amount),2) as avg_lifetime_spent
From
(SELECT customer_id,sum(amount) as total_amount from payment
Group By customer_id) as subquery
```
```
SELECT Round(AVG (amount_per_day),2) as Daily_Revenue_avg
from
(select sum(amount) as amount_per_day,
date(payment_date)
From payment
Group By date(payment_date) ) a
```

# subqueires in SELECT
```
SELECT *,
(select Round(Avg(amount),2)from payment)
from payment
```
```
SELECT *,
(select amount from payment Limit 1)
from payment
```
```
SELECT *,
(select Max(amount) from payment)- amount as Difference from payment

```

# corelated subqueires in WHERE
```
SELECT * from payment p1
where amount = ( select Max(amount) from payment p2
where p1.customer_id=p2.customer_id)
order by customer_id
```
```
SELECT title ,film_id,replacement_cost ,rating from film f1
where replacement_cost = (select min (replacement_cost) from film f2
where f1.rating=f2.rating)
```

```
SELECT title ,film_id,length ,rating from film f1
where length = (select max (length) from film f2
where f1.rating=f2.rating)
```
# corelated subqueires in SELECT

```
SELECT * , (select max(amount ) from payment p2
where p1.customer_id=p2.customer_id)
from payment p1
order by customer_id
```

```
SELECT first_name, Max(amount) from payment p1
inner join customer c
on p1.customer_id=c.customer_id
Group By first_name
```

# 9- Advanced Managing tables & databases

**Manging_tables**
```
Data_Defination
->Create
->Alter
->Drop
*Data structures

Data_Manipulation
->Insert
->Update
->Delete
* data itself

Data_types
Constraints
Primary_key
Foreign_key
views

```

**Create Database**
```
Create Database Company_x
with encoding='UTF-8';

Comment on Database company_x is 'That is our Database'
```

**Constraints**
```
Not Null- Ensure not null value
Unique -Ensure all the values in the column are diffecnt
Default-Sets a default value for a column if no value is specified
Primary Key-A combination of a not null and unique .Uniqueloy identifies each row in table
Refenences - Ensure referral integrity ( only values of another columns can be used
Check -Ensures that the values of the columns has specific condition

```

**Create Table**
```
Create Table Director
(
director_id serial Primary key,
Director_account_name Varchar(20) Unique,
First_name Varchar(50),
last_name Varchar(50) Default 'Not Specfied',
Date_of_birth Date,
address_id int Refrences address (address_id))
```

**Create Table**
```
CREATE TABLE online_sales (
transaction_id SERIAL PRIMARY KEY,
customer_id INT REFERENCES customer(customer_id),
film_id INT REFERENCES film(film_id),
amount numeric(5,2) NOT NULL,
promotion_code VARCHAR(10) DEFAULT 'None'
)
```
**Insert**
```
Insert into online_sales (customer_id , film_id,amount)
values(269,13,10.9),(270,12,22.99)
```

**Alter**
```
Alter Table staff
Drop column if exists first_name

Alter Table staff
Add column date_of_birth Date

Alter Table staff
Alter Column ADDRESS_ID Type small_int

Alter Table staff
Rename column first_name to name

Alter Table staff
Alter Column store_id set Default 1

Alter Table <Table Name>
Alter Column <column_name> Drop Not Null

Alter table director
Alter column director_account_name Type Varchar (30),
Alter Column last_name Drop Default,
Alter Column last_name set Not Null,
Add column email varchar (40),
Rename Director_account_name to account_name

```
**Drop & Truncate**

```

Drop Table <Table Name> : Deletes the table,
Truncate Table <Table Name> - Deletes all the data in the table 

```

**Check**
```
Create Table director( name text check (length (name)>1)

Alter Table director Add constraint date_check Check (start_date <end_date)

```

# 10 - Advanced Views & Data Manipulations


**Update**
```
Update songs SET genre ='Country music' where song_id =4

Update songs price =song _id+0.99

update customer set email = lower( email)

```

**Delete**
```
Delete from songs
where song_id=4

Delete From songs where song_id in (4,5)
Returning song_name , song_id 
```

**Create Table AS**
```
Create Table Customer Spendings
AS
Select first_name ||' '||  last_name AS name ,
SUM (amount) as Total_amount
from customer c
Left Join payment p
on c.customer_id=p.customer_id
Group By first_name ||' '||  last_name 
```

**View**
```
CREATE VIEW films_category
AS
SELECT 
title,
name,
length
FROM film f
LEFT JOIN film_category fc
ON f.film_id=fc.film_id
LEFT JOIN category c
ON c.category_id=fc.category_id
WHERE name IN ('Action','Comedy')
ORDER BY length DESC
```

**Managing View**
```
Create or Replace View V_customer_info
AS new_query

```

# 11 - Pro Window Functions
**Over() with partion By**
```
Agg (agg_column) over( partition by partion_column)
```

```
Select transaction_id, payment_type,customer_id,price_in_transaction,
sum(price_in_transaction) over (partition by customer_id)
from sales s


Select transaction_id, payment_type,customer_id,price_in_transaction,
count(*) over( partition by customer_id)
from sales s

Select *,
sum (amount) over (partition by customer_id)
from payment
order by 1

```

