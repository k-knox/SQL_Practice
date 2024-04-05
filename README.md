
# SQL Practice: Answering Business Questions using MySQL 

## Overview: 

In this project I will be demonstrating MySQL skills by answering a variety of practice business questions. 

### Scenario 

I’m co-owner of a recently acquired movie rental business **Maven Movies**. As a new owner I need to have a deep understanding of my organization, which means conducting a basic exploratory analysis of my product inventory, staff, and customer purchasing behaviors. 


### Additional Information: 
Editor | Database Name
:---|:---
MySQL Workbench (Version 8.031) | mavenmovies (17 tables) 

## Questions

* Provide a list of all staff members, including their first and last names, email addresses, and the store identification number where they work. 

```sql
SELECT 
    first_name, 
    last_name, 
    email, 
    store_id
FROM staff;
```

* Provide separate counts of inventory items held at each store.

```sql
SELECT
  store_id,
  COUNT(film_id) AS Total_Inventory
FROM inventory
GROUP BY store_id;
```


* Provide a seprate count of active customers for each store. 

```sql
SELECT
  store_id,
  COUNT(customer_id) AS Total_Active_Customers
FROM customer
WHERE active = 1
GROUP BY store_id;
```

* In order to assess the liability of a data breach, provide a count of all customer email addresses stored in the database. 

```sql
SELECT
  COUNT(email) AS Total_Number_Emails
FROM customer;
```

* ~~We are interested in how diverse your film offering is as a means of understanding how likely you are to keep customers engaged in the future.~~ Provide a count of unique film titles in inventory at each store and then provide a count of the unique categories of films. 

```sql
SELECT
  store_id,
  COUNT(DISTINCT film_id) AS Total_Inventory
FROM inventory
GROUP BY store_id;
```
```sql
SELECT
  category_id,
  COUNT(DISTINCT film_id) AS Total_Inventory
FROM film_category
GROUP BY category_id;
```
```sql
SELECT
  COUNT(DISTINCT name) AS Unique_Categories
FROM category;
```

* Provide the replacement cost for the film that is least expensive to replace, the most expensive to replace, and the average of all films you carry.

```sql
SELECT
  MIN(replacement_cost) AS Lowest_RC,
  MAX(replacement_cost) AS Highest_RC,
  AVG(replacement_cost) AS Avg_RC
FROM film;
```

* ~~We are interested in having you put payment monitoring systems and maximum payment processing restrictions in place in order to minimize the future risk of fraud by your staff.~~ Please provide the average payment you process, as well as the maximum payment you have processed.

```sql
SELECT
  AVG(amount) AS Avg_Payment,
  MAX(amount) AS Max_Payment
FROM payment;
```

* Provide a list of all customer identification values, and a count of rentals they have made all-time, with your highest volume customers at the top of the list.

```sql
SELECT
  customer_id,
  COUNT(rental_id) AS Total_Rentals
FROM rental
GROUP BY customer_id
ORDER BY total_rentals DESC;
```
