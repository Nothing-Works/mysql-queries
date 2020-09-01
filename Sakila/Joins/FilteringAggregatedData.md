# Filtering Aggregated Data

## `where` VS `having`

### When using `where` you must always reference the actual column not the alias like: `count(*) as rentals`.

### `having` is very similar to `where`, but it will act on the rows after they've been to grouped together.

```mysql
select film.title, sum(payment.amount) as sales, count(*) as rentals
from rental
join payment
on payment.rental_id = rental.rental_id
join inventory
on inventory.inventory_id = rental.inventory_id
join film
on film.film_id = inventory.film_id
group by film.film_id
having sales > 200
order by sales desc
```
