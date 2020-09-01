# Multiple Joins In One Query

## There are many ways to write queries to get the same result.

1. Normally, using `sub-query` is slower than not using it.
2. `outer` joins are slower than `inner` joins.(`left join` is slower than `join`)
3. The more columns are added in to `group by` the slower it will be.

Two examples:

```mysql
select c.customer_id, c.first_name, c.last_name, count(r.rental_id) as total_rentals, a.address as store_address
from customer as c
left join rental as r
on r.customer_id = c.customer_id
left join address as a
on a.address_id = (select store.address_id from store where store.store_id = c.store_id)
group by c.customer_id, a.address
```

```mysql
select c.customer_id, c.first_name, c.last_name, count(r.rental_id) as total_rentals, a.address as store_address
from customer as c
left join rental as r
on r.customer_id = c.customer_id
left join store
on store.store_id = c.store_id
left join address as a
on a.address_id = store.address_id
group by c.customer_id, a.address
```

We should be able to remove the `a.address` from `group by` as the values for `a.address` are all the same and this will be faster.
