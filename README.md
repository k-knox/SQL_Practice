
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


* Provide the managers' names at each store, with the full address of each property (street address, district, city, and country).  

```sql
SELECT
    staff.first_name AS Manager_First_Name,
    staff.last_name AS Manager_Last_Name,
    address.address AS Store_Address,
    address.district,
    city.city,
    country.country
FROM staff
    INNER JOIN address ON staff.address_id = address.address_id
    INNER JOIN city ON address.city_id = city.city_id
    INNER JOIN country ON city.country_id = country.country_id;
```


* Pull a list of each inventory item stocked including the store_id, inventory_id, film name, rating, rental rate, and replacement cost. 

```sql
SELECT
    inventory.store_id,
    inventory.inventory_id,
    film.title,
    film.rating,
    film.rental_rate,
    film.replacement_cost
FROM inventory
    LEFT JOIN film
        ON inventory.film_id = film.film_id
GROUP BY inventory.inventory_id;
```


* Provide a summary level overview of the inventory list above. Show how many inventory are available with each rating at each store. 

```sql
SELECT
    inventory.store_id,
    film.rating,
    COUNT(inventory.inventory_id) AS Inventory_QTY
FROM inventory
    LEFT JOIN film
        ON inventory.film_id = film.film_id
GROUP BY inventory.store_id, film.rating;
```


* ~~Similarly, we want to understand how diversified the inventory is in terms of replacement cost. We want to see how big of a hit it would be if a certain category of film became unpopular at a certain store.~~ Provide the number of films, average replacement cost, and total replacement cost, sliced by store and film category. 

```sql
SELECT
    inventory.store_id,
    category.name,
    COUNT(inventory.inventory_id) AS Total_Films,
    AVG(film.replacement_cost) AS Average_Replacement_Cost,
    SUM(film.replacement_cost) AS Total_Replacement_Cost
FROM category
    INNER JOIN film_category ON category.category_id = film_category.category_id
    INNER JOIN inventory ON film_category.film_id = inventory.film_id
    INNER JOIN film ON inventory.film_id = film.film_id
GROUP BY inventory.store_id, category.name;
```

* Provide a list of all customer names, what store they go to, whether or not they are currently active, and their full addresses (street address, city, and country.)

```sql
SELECT
    customer.first_name,
    customer.last_name,
    customer.store_id AS Store_Preference,
    CASE
        WHEN customer.active = 1 THEN 'Active'
        WHEN customer.active = 0 THEN 'Inactive'
        ELSE 'Nope, Check Logic!'
    END AS Customer_Status,
    address.address,
    city.city,
    country.country
FROM customer
    INNER JOIN address ON customer.address_id = address.address_id
    INNER JOIN city ON address.city_id = city.city_id
    INNER JOIN country ON city.country_id = country.country_id
ORDER BY customer.last_name ASC, customer.first_name ASC; 
```


* Please pull together a list of customer names, their total lifetime rentals, and the sum of all payments you have collected from them. Show most valuable customers at the top of the list. 

```sql
SELECT
    customer.first_name,
    customer.last_name,
    COUNT(payment.rental_id) AS Total_Lifetime_Rentals,
    SUM(payment.amount) AS Total_Payments
FROM customer
    INNER JOIN payment ON customer.customer_id = payment.customer_id
GROUP BY customer.first_name, customer.last_name
ORDER BY total_payments DESC;
```


* Provide a list of advisor and investor names. Include which company the investors work for.

```sql
SELECT
    'Advisor' AS Type,
    advisor.first_name,
    advisor.last_name,
    NULL AS Company_Name
FROM advisor

UNION ALL

SELECT
    'Investor' AS Type,
    investor.first_name,
    investor.last_name,
    investor.company_name
FROM investor;
```

## Exploratory Queries (no questions)

```sql
SELECT DISTINCT
    title,
    CASE
        WHEN rental_duration <= 4 THEN 'Rental too short'
        WHEN rental_rate >= 3.99 THEN 'Too expensive'
        WHEN rating IN('NC-17', 'R') THEN 'Too adult'
        WHEN length NOT BETWEEN 60 AND 90 THEN 'Too short or too long'
        WHEN description LIKE '%Shark%' THEN 'Nope, has sharks'
        ELSE 'Great for my neice!'
    END AS Fit_For_Recommendations
FROM film;
```

```sql
SELECT
    first_name,
    last_name,
    CASE
        WHEN store_id = 1 AND active = 1 THEN 'Store 1 Active'
        WHEN store_id = 1 AND active = 0 THEN 'Store 1 Inactive'
        WHEN store_id = 2 AND active = 1 THEN 'Store 2 Active'
        WHEN store_id = 2 AND active = 0 THEN 'Store 2 Inactive'
        ELSE 'Nope, check logic!'
    END AS store_and_status
FROM customer;
```

```sql
SELECT
    film_id,
    COUNT(CASE WHEN store_id = 1 THEN inventory_id ELSE NULL END) AS Store_1_Copies,
    COUNT(CASE WHEN store_id = 2 THEN inventory_id ELSE NULL END) AS Store_2_Copies,
    COUNT(inventory_id) AS Total_Copies
FROM inventory
GROUP BY film_id
ORDER BY film_id;
```

```sql
SELECT
    store_id,
    COUNT(CASE WHEN active = 1 THEN customer_id ELSE NULL END) AS Active,
    COUNT(CASE WHEN active = 0 THEN customer_id ELSE NULL END) AS Inactive
FROM customer
GROUP BY store_id;
```

## Project Feedback
*To be updated*
