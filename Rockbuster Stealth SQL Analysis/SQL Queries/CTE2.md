
# Number of Top 5 Customers per Country

## Description

This SQL query identifies the top 10 cities with the highest customer counts and selects the top 5 customers from those cities based on their total payments. It then calculates the total number of top 5 customers in each country. The results are grouped by country and ordered by the number of top 5 customers in descending order.

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
           customer.first_name AS First_Name, 
           customer.last_name AS Last_Name, 
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
SELECT country.country AS Country,
       COUNT(DISTINCT customer.customer_id) AS all_customer_count,
       COUNT(DISTINCT top_5_customers.Customer_ID) AS top_customer_count
FROM customer
INNER JOIN address ON customer.address_id = address.address_id
INNER JOIN city ON address.city_id = city.city_id
INNER JOIN country ON city.country_id = country.country_id
LEFT JOIN top_5_customers ON country.country = top_5_customers.Country
GROUP BY country.country
ORDER BY top_customer_count DESC;
```
