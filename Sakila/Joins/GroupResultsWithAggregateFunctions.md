# Group Results With Aggregate Functions

## Aggregate - a whole formed by combining several elements.

## Aggregate is very similar to `reduce` function.

```mysql
select customer.customer_id, customer.first_name, customer.last_name, count(rental.rental_id) rentals_checked_out
from customer
left join rental
on customer.customer_id = rental.customer_id
group by customer.customer_id
```

- aggregate and group by kind of go together.

## Common errors

1. no aggregate, only `group by`

```mysql
select customer.customer_id, customer.first_name, customer.last_name, rental.rental_id
from customer
left join rental
on customer.customer_id = rental.customer_id
group by customer.customer_id
```

This will not give you the expected result, because, you are trying to group by `customer_id` however for each row `rental_id` is different so mysql does not know what to do with it. It may cause error(saying that `rental_id` is not an aggregate column) or it just gives you the first `rental_id` value which is not correct in almost all cases.

You may add `rental.rental_id` in the `group by` as well, it will group by the combination of `rental.rental_id` and `customer.customer_id` which means that we do not need to do the aggregate on `rental.rental_id`. (in this case it does not make any sense at all but in other cases it may be useful)

2. no `group by`, only aggregate

```mysql
select customer.customer_id, customer.first_name, customer.last_name, count(rental.rental_id) total
from customer
left join rental
on customer.customer_id = rental.customer_id
```

This will not give you the expected result either, because, you are trying to do the aggregate(in this example `count()`), but you did not tell mysql how you want to group the data. It may cause error(saying that `customer_id` is not an aggregate column) or it is going to treat the whole data set as a group and give you the result(use the first row columns other than the aggregate one as the value),again this is also not going to give you the correct result in almost all cases.

## So `aggregate` functions and `group by` go together most of the time.
