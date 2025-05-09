
# Top 10 Countries for Rockbuster in Terms of Customer Numbers

## Description
This SQL query identifies the top 10 countries for Rockbuster based on the number of customers. The query uses `INNER JOIN` to connect the `customer`, `address`, `city`, and `country` tables, ensuring that only countries with customers are included. It groups the data by country and counts the number of customers per country. The results are then sorted in descending order and limited to the top 10 countries.

## SQL Query

```sql
SELECT 
    country.country AS Country,
    COUNT(customer.customer_id) AS Customer_Count
FROM 
    customer
INNER JOIN address ON customer.address_id = address.address_id
INNER JOIN city ON address.city_id = city.city_id
INNER JOIN country ON city.country_id = country.country_id
GROUP BY country.country
ORDER BY Customer_Count DESC
LIMIT 10;
```
