# Group by query.

1. When group by a column, you can not do `select *`. You have to manually select some existing columns or aggregate columns.

2. `SUM(liked) likes` the total number of `liked` column where it is true as `likes` variable.

3. `SUM(!liked) dislikes` the total number of `liked` column where it is false as `dislikes` variable.

```sql
SELECT tweet_id, SUM(liked) likes, SUM(!liked) dislikes FROM likes
GROUP BY tweet_id
```

# Sub-query.

1. We can use `joins` with sub-query. `LEFT JOIN (...)`, the `...` is a sub-query which is explained before.

2. `likes` is the variable name that from the sub-query, `likes.tweet_id = tweets.id` is the condition.

3. This query: `tweet` is `left join` with the sub-query and then we name the sub-query as `likes` on condition of `likes.tweet_id = tweets.id`.

```sql
SELECT * FROM tweets
LEFT JOIN (
    SELECT tweet_id, SUM(liked) likes, SUM(!liked) dislikes FROM likes
    GROUP BY tweet_id
) likes on likes.tweet_id = tweets.id
```
