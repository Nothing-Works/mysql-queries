# Index

## A phone book is a very old, classic, simple and easy way to show how and `Index` works.

## When to use index: (From mysql docs)Indexes are less important for queries on small tables, or big tables where report queries process most or all of the rows. When a query needs to access most of the rows, reading sequentially is faster than working through an index. Sequential reads minimize disk seeks, even if not all the rows are needed for the query. So you will not benefit too much for small tables and can make the performance worse if you use the index to access all or most of the rows.

## Applying Index (Normally, `FK` and `PK` are automatically indexed)

## Use `explain` to see how the query is being executed.

```mysql
select *
from video_downloads
where user_id = 860
```

If we do not have index on `user_id` it's going to be slow, it's much faster after we added index on `user_id`.

## An Index is an ordered representation of the indexed data.(Normally, Indexes are in the memory)

## Example

If the `amount` is not indexed in the `payment` table.

```mysql
select * from payment
where amount = 11.99
```

It's about `40-50 ms`.

We can add `EXPLAIN` before any sql query to show how the query is executed. `type` and `rows` are two important columns to look at.(`All` and `16086` without index)

1. `type`: how the database is going to execute this query.
   - `all` means that it's going to look at all the rows, no filtering, no sorting.
2. `rows`: how many records is the database going to look at.
3. `possible_keys`: the index may be used for the query.
4. `key`: the index is used for the query.

```mysql
EXPLAIN select * from payment
where amount = 11.99
```

After applying index on `amount`, it's about `30 ms`. And `type` is `ref`, `row` is `10`

## Some types we may face:

1.  `CONST/EQ_REF`: Performs a `B-Tree` traversal to find a single value. (super fast, do not need to optimize)

    - Basically binary search.
    - This can only get used that you can guarantee the uniqueness which means only one record will be returned(FK, Unique on column).
    - `limit 1` does not mean uniqueness, you may still fetch many rows but you just pick one.

2.  `REF/RANGE`: Performs a `B-Tree` traversal to find the starting point, and then scans values from that point on. It stops if it finds a value that does not meet the criteria.

    - Also known as an index range scan.
    - It limits the total number of rows that the database needs to inspect to perform the query.
    - It's a good thing, as it's less work for the database.

3.  `Index`: Starts at the very first leaf node, scan all the leaf nodes to the end.

    - Traverses through the entire index.
    - No filtering, but still using the index.
    - Also known as a full index scan.

4.  `All`: Basically, when `type` is `all`, it's the slowest.

    - Also know as a full table scan.
    - Does not use an index at all.
    - Loads every column of every row from the table.
    - Scans through all of them and emits or discards accordingly.

## Common pitfalls

1.  `Functions`: This code below will work for the `created_at` index.

    - Solutions:

      - Use function base index if possible.(try not to use the computed column if possible.)

      - You can try to do this way: `where created_at between '2013-01-01 00:00:00' and '2013-12-31 23:59:59'`. In this way, you will have the `possible_keys` having `created_at` but the `key` is still null. You may try to use `force index (orders_created_at_idx)`. But this will make the query very very slow. This is because we are doing `sum(total)` the database needs to fetch each row to get the total and sum all(read form disk). So, **do not use force index**. Instead you need to add the total as index as well, so it does not need to read from disk any more, it's from the memory. For the `Extra` column it's `Using where; Using Index`, but it actually means that `Using Index Only` which means that `mysql` does not need to fetch from disk it can get all the results from memory, as `mysql` saves data in memory.

      - `Using Index Only` is very powerful but it's very aggressive.

```mysql
select sum(total)
from orders
where year(created_at) = '2012'
```

2. The index column order matters! So you have to use the index from left to right, you can not skip column/s. `index a and b is not the same as index b and a`.

3. Inequality Operators. As soon as you hit inequality operators the index stops there.

## This point is that there is no one index can satisfy all the queries.

For details https://www.youtube.com/watch?v=HubezKbFL7E&t
