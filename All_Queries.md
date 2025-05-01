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

# 7- Advanced Managing tables & databases

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
