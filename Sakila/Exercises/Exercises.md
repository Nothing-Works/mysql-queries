# Exercises

```mysql
select users.id, users.name, count(*) as readers
from users
left join post_reads
on post_reads.post_id in (
	select id from posts where user_id = users.id
)
group by users.id
order by readers desc
limit 10
```

you can also do the `left join` `posts` and then `left join` `post_reads`.

```mysql
select round(avg(total_rentals)) as average_rentals
from (
     select date(rental_date) as day, count(*) total_rentals
from rental
group by day
order by day desc
         ) as rentals
```
