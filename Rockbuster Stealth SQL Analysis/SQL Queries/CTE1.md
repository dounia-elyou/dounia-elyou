
# The Average Amount Paid by the Top 5 Customers

## Description

This SQL query calculates the average amount paid by the top 5 customers from the top 10 cities based on customer activity. The query first identifies the top cities by customer count, then extracts the top 5 customers from those cities based on their total payments, and finally calculates the average payment amount from those top customers.

## SQL Query

```sql
WITH top_cities AS (
    SELECT city.city
    FROM customer
    INNER JOIN address ON customer.address_id = address.address_id
    INNER JOIN city ON address.city_id = city.city_id
    INNER JOIN country ON city.country_id = country.country_id
    GROUP BY city.city, country.country
    ORDER BY COUNT(customer.customer_id) DESC
    LIMIT 10
),
top_5_customers AS (
    SELECT customer.customer_id AS Customer_ID, 
           customer.first_name AS "First Name", 
           customer.last_name AS "Last Name", 
           country.country AS Country, 
           city.city AS City, 
           SUM(payment.amount) AS Total_Amount_Paid
    FROM customer
    INNER JOIN payment ON customer.customer_id = payment.customer_id
    INNER JOIN rental ON payment.rental_id = rental.rental_id
    INNER JOIN inventory ON rental.inventory_id = inventory.inventory_id
    INNER JOIN store ON inventory.store_id = store.store_id
    INNER JOIN address ON customer.address_id = address.address_id
    INNER JOIN city ON address.city_id = city.city_id
    INNER JOIN country ON city.country_id = country.country_id
    WHERE city.city IN (SELECT city FROM top_cities)
    GROUP BY customer.customer_id, customer.first_name, customer.last_name, country.country, city.city
    ORDER BY Total_Amount_Paid DESC
    LIMIT 5
)
SELECT AVG(top_5_customers.Total_Amount_Paid) AS Average_Amount_Paid
FROM top_5_customers;
```
