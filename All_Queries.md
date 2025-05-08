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

Select *,
count(*) over (partition by amount,customer_id)
from payment
order by customer_id

```
**Over() with Order By**
```
select * ,
sum (amount)over( order by payment_date)
from payment

select * ,
sum (amount)over( Partition By customer_id order by payment_date)
from payment

select flight_id,
departure_airport,
sum(actual_arrival-scheduled_arrival)
over (partition by departure_airport order by flight_id)
from flights

```

**Rank**
```
Select f.title ,c.name,f.length ,
Rank ()over ( order by length)
from film f
left join film_category fc on f.film_id=fc.film_id 
left join category c on c.category_id= fc.category_id

Select f.title ,c.name,f.length ,
Dense_Rank ()over ( order by length)
from film f
left join film_category fc on f.film_id=fc.film_id 
left join category c on c.category_id= fc.category_id

Select f.title ,c.name,f.length ,
Dense_Rank ()over ( Partition by name order by length)
from film f
left join film_category fc on f.film_id=fc.film_id 
left join category c on c.category_id= fc.category_id

Select * from (Select f.title ,c.name,f.length ,
Dense_Rank ()over ( Partition by name order by length) as Rank
from film f
left join film_category fc on f.film_id=fc.film_id 
left join category c on c.category_id= fc.category_id) a
where Rank=1  


Select * from(
Select name ,country ,count(*),
Rank () Over (Partition by country order by count(*) desc) 
from customer_list
left join payment on id=customer_id
group by name, country) a
where rank between 1 and 3


```


**First_value()**
```
Select * from(
Select name ,country ,count(*),
First_value(count(*)) Over (Partition by country order by count(*) asc) 
from customer_list
left join payment on id=customer_id
group by name, country) a
```


**Lead & Lag**
```
Select name ,country ,count(*),
lEAD(count(*)) Over (Partition by country order by count(*) asc) as rank
from customer_list 
left join payment on id=customer_id
group by name, country

Select name ,country ,count(*),
lEAD(name)) Over (Partition by country order by count(*) asc) as rank
from customer_list 
left join payment on id=customer_id
group by name, country


Select name ,country ,count(*),
lEAD(count(*)) Over (Partition by country order by count(*) asc) as rank,
lEAD(count(*)) Over (Partition by country order by count(*) asc) -count(*)
from customer_list 
left join payment on id=customer_id
group by name, country

Select name ,country ,count(*),
LAG(count(*)) Over (Partition by country order by count(*) asc) as rank,
LAG(count(*)) Over (Partition by country order by count(*) asc) -count(*)
from customer_list 
left join payment on id=customer_id
group by name, country


Select sum (amount),
date (payment_date),
Lag (sum(amount)) over (order by date(payment_date)) as Previous_day,
sum(amount)-Lag (sum(amount)) over (order by date(payment_date)) as difference
from payment
group by date (payment_date)


```

# 12 - Grouping sets,rollups,selfjoins
**Rollup**
```
Select
'Q'||To_char(payment_date,'Q') as quater,
Extract (month from payment_date) as month,
Date(payment_date),
sum(amount)

from payment
group by
rollup(

'Q'||To_char(payment_date,'Q') ,
Extract (month from payment_date) ,
Date(payment_date)
)
order by 1,2,3


Select 
extract (quarter from book_date) as quater,
Extract (month from book_date ) as month,
To_char (book_date,'w') as week_in_month,
Date(book_date),
sum(total_amount)
From bookings
group by
rollup(
extract (quarter from book_date),
Extract (month from book_date ) ,
To_char (book_date,'w') ,
Date(book_date)
)
order by 1,2,3,4
```

**Cube**
```
Select 
customer_id,
staff_id,
Date(payment_date),
sum(amount)
from payment
group by
cube(
customer_id,
staff_id,
Date(payment_date)
)
Order by 1,2,3


SELECT 
p.customer_id,
DATE(payment_date),
title,
SUM(amount) as total
FROM payment p
LEFT JOIN rental r
ON r.rental_id=p.rental_id
LEFT JOIN inventory i
ON i.inventory_id=r.inventory_id
LEFT JOIN film f
ON f.film_id=i.film_id
GROUP BY 
CUBE(
p.customer_id,
DATE(payment_date),
title
)
ORDER BY 1,2,3
```


**Self_joins**
```

CREATE TABLE employee (
	employee_id INT,
	name VARCHAR (50),
	manager_id INT
);

INSERT INTO employee 
VALUES
	(1, 'Liam Smith', NULL),
	(2, 'Oliver Brown', 1),
	(3, 'Elijah Jones', 1),
	(4, 'William Miller', 1),
	(5, 'James Davis', 2),
	(6, 'Olivia Hernandez', 2),
	(7, 'Emma Lopez', 2),
	(8, 'Sophia Andersen', 2),
	(9, 'Mia Lee', 3),
	(10, 'Ava Robinson', 3);

```
```
select 
emp.employee_id,
emp.name as employee,
mng.name as manager

from employee emp
Left join employee mng
on emp.manager_id=mng.manager_id

```
```
select 
emp.employee_id,
emp.name as employee,
mng.name as manager,
mng2.name as manager_of_manager
from employee emp
Left join employee mng
on emp.manager_id=mng.manager_id
Left join employee mng2
on mng.manager_id=mng2.manager_id

```

**Cross_joins**
```
Select staff_id,
store.store_id
from staff
cross join store


Select staff_id,
store.store_id,
last_name,
store.store_id*staff_id
from staff
cross join store
```


**Natural_joins**
```
Select * from payment
Natural left join customer
```

# 14 - Pro: stored procedures,user defined functions

**user defined functions**
```
CREATE OR REPLACE FUNCTION count_rr (min_rr decimal(4,2), max_rr decimal(4,2))
RETURNS int
LANGUAGE plpgsql
AS
$$
DECLARE
    movie_count int;
BEGIN
    SELECT count(*) INTO movie_count 
    FROM film 
    WHERE rental_rate BETWEEN min_rr AND max_rr;
    
    RETURN movie_count;
END;
$$;


DROP FUNCTION IF EXISTS count_rr(decimal(4,2), decimal(4,2));

Select count_rr (0,6)

```
**Transactions & Commit**
```

CREATE TABLE acc_balance (
    id SERIAL PRIMARY KEY,
    first_name TEXT NOT NULL,
	last_name TEXT NOT NULL,
    amount DEC(9,2) NOT NULL    
);

INSERT INTO acc_balance
VALUES 
(1,'Tim','Brown',2500),
(2,'Sandra','Miller',1600)

SELECT * FROM acc_balance;

Begin;
Update acc_balance
set amount= amount-100
where id=1

commit

drop table acc_balance
```

**Roll Back**
```
Begin;
update acc_balance
set amount=amount+100
where id =2;
Delete From acc_balance
where id =1;

select  * from acc_balance

Rollback
```

**Roll Back to savepoint**
```
Begin;
update acc_balance
set amount=amount+100
where id =2;
savepoint s1;
Delete From acc_balance
where id =1;

select  * from acc_balance

Rollback to savepoint s1;

```

**stored_procedures**
```

Create or Replace Procedure sp_transfer
(tr_amount int,sender int, recipient int)
Language plpgsql
as
$$
Begin
-- add balance
Update acc_balance
set amount = amount+tr_amount
where id= recipient;

-- subtract balance
Update acc_balance
set amount = amount-tr_amount
where id= sender;
commit;
end;
$$


Call sp_transfer(500,1,2)

select * from acc_balance


DROP PROCEDURE IF EXISTS sp_transfer;
DROP PROCEDURE IF EXISTS sp_transfer(INT, INT, INT);


DO $$
DECLARE
    proc_record RECORD;
BEGIN
    FOR proc_record IN 
        SELECT oid, proname, pg_get_function_identity_arguments(oid) as args
        FROM pg_proc
        WHERE proname = 'sp_transfer'
    LOOP
        EXECUTE 'DROP PROCEDURE IF EXISTS ' || proc_record.proname || '(' || proc_record.args || ')';
    END LOOP;
END $$;
ROLLBACK;

```


# 15 - Pro:indexes partining and query optimization

**Create User role**
```
Create user *****
with password '********';

CREATE ROLE **** WITH LOGIN PASSWORD '*******';
```

**Grant & Revoke Privileges**
```
-- Create users

CREATE USER ria
WITH PASSWORD 'ria123';


CREATE USER mike
WITH PASSWORD 'mike123';


-- Create roles
CREATE ROLE read_only;
CREATE ROLE read_update;


-- Grant usage (already granted)
GRANT USAGE
ON SCHEMA public
TO read_only;

-- Grant SELECT on tables
GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO read_only;

-- Assign read_only to read_update role
GRANT read_only
TO read_update;

-- Grant all privileges on all tables in public to role
GRANT ALL
ON ALL TABLES IN SCHEMA public
TO read_update;


-- Revoke some privileges
REVOKE DELETE, INSERT
ON ALL TABLES IN SCHEMA public
FROM read_update;

-- Assign role to users
GRANT read_update
TO ria;


-- DROP roles
DROP ROLE mike;

DROP ROLE read_update;



-- Removing dependancies
DROP OWNED BY read_update;
DROP ROLE read_update;

```


**INdexes:B-tree,Bitmap  indexes , creating indexes**
```
SELECT * FROM flights f2
WHERE flight_no < (SELECT MAX(flight_no)
				  FROM flights f1
				   WHERE f1.departure_airport=f2.departure_airport
				   )
--

CREATE INDEX flight_no_index
ON flights
(departure_airport,flight_no)


Select (select avg(amount) from payment p2
where p2.rental_id=p1.rental_id)
from payment p1

Create index index_rental_id_payment
on payment
(rental_id)

```
# 16 - CTE'S Common Table Expression
**CTE'S Common Table Expression - example**
```
-- 2 Query withour CTE

Select 	film_id, title,rental_count
from (
Select f.film_id,f.title,count(r.rental_id) as rental_count
From film f
join inventory i on f.film_id = i.film_id
join rental r on i.inventory_id=r.inventory_id
Group By f.film_id,f.title
)
as film_rentals
where rental_count >30


--1 query with cte
with subquery as (Select f.film_id,f.title,count(r.rental_id) as rental_count
From film f
join inventory i on f.film_id = i.film_id
join rental r on i.inventory_id=r.inventory_id
Group By f.film_id,f.title
)
Select 	film_id, title,rental_count
from subquery as film_rentals
where rental_count >30

```

**Using Multiple CTEs**
```
-- Step 1: Create a CTE to calculate the total rental count and total rental amount for each customer
 
WITH customer_totals AS (
    SELECT c.customer_id, c.first_name, c.last_name,
           COUNT(r.rental_id) AS rental_count,
           SUM(p.amount) AS total_amount
    FROM customer c
    JOIN rental r ON c.customer_id = r.customer_id
    JOIN payment p ON c.customer_id = p.customer_id AND p.rental_id = r.rental_id
    GROUP BY c.customer_id, c.first_name, c.last_name
),
 
-- Step 2: Calculate the average rental count across all customers
 
average_rental_count AS (
    SELECT AVG(rental_count) AS avg_rental_count
    FROM customer_totals
)
,
 
-- Step 3: Identify customers who have rented more than the average number of films (high-rental customers)
 
high_rental_customers AS (
    SELECT ct.customer_id, ct.first_name, ct.last_name, ct.rental_count, ct.total_amount
    FROM customer_totals ct
    JOIN average_rental_count arc ON ct.rental_count > arc.avg_rental_count
)
 
-- Step 4: List the details of the films rented by these high-rental customers
 
SELECT hrc.customer_id, hrc.first_name, hrc.last_name, hrc.rental_count, hrc.total_amount, f.film_id, f.title
FROM high_rental_customers hrc
JOIN rental r ON hrc.customer_id = r.customer_id
JOIN inventory i ON r.inventory_id = i.inventory_id
JOIN film f ON i.film_id = f.film_id;




Explanation of the Query
CTE for Customer Totals (customer_totals):

Calculate the total rental count and total rental amount for each customer.

Group by customer_id, first_name, and last_name.

WITH customer_totals AS (
    SELECT c.customer_id, c.first_name, c.last_name,
           COUNT(r.rental_id) AS rental_count,
           SUM(p.amount) AS total_amount
    FROM customer c
    JOIN rental r ON c.customer_id = r.customer_id
    JOIN payment p ON c.customer_id = p.customer_id AND p.rental_id = r.rental_id
    GROUP BY c.customer_id, c.first_name, c.last_name
),
CTE for Average Rental Count (average_rental_count):

Calculate the average rental count across all customers.

average_rental_count AS (
    SELECT AVG(rental_count) AS avg_rental_count
    FROM customer_totals
),
CTE for High-Rental Customers (high_rental_customers):

Identify customers who have rented more than the average number of films.

high_rental_customers AS (
    SELECT ct.customer_id, ct.first_name, ct.last_name, ct.rental_count, ct.total_amount
    FROM customer_totals ct
    JOIN average_rental_count arc ON ct.rental_count > arc.avg_rental_count
)
Final Query:

List the details of the films rented by high-rental customers.

SELECT hrc.customer_id, hrc.first_name, hrc.last_name, hrc.rental_count, hrc.total_amount, f.film_id, f.title
FROM high_rental_customers hrc
JOIN rental r ON hrc.customer_id = r.customer_id
JOIN inventory i ON r.inventory_id = i.inventory_id
JOIN film f ON i.film_id = f.film_id;
```
**Recurisive CTEs**
```
--Step 1: Use a recursive CTE to find all subordinates of a given employee.
WITH RECURSIVE subordinate_tree AS (
    -- Anchor member: Select the given employee with level 1
    SELECT e.employee_id, e.name, e.manager_id, 1 AS level
    FROM employee e
    WHERE e.employee_id = 1  -- Start with the given employee (Alice)
    
    UNION ALL
    
    -- Recursive member: Select subordinates of the current set of employees and increment the level
    SELECT e.employee_id, e.name, e.manager_id, st.level + 1 AS level
    FROM employee e
    INNER JOIN subordinate_tree st ON e.manager_id = st.employee_id
)
 
-- Step 2: Select all subordinates from the recursive CTE
SELECT employee_id, name, manager_id, level
FROM subordinate_tree;
```


