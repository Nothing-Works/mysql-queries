# Why indexing?

Improve query performance, however, if you are not careful and do not know what you are doing you can make it worse and sometimes you do not need it.

1. For small tables you may not need it.
2. For big tables If you need to read all the data or most of them you need to use it wisely. Either make it `Using index` only or do not use it.

# What is an Index: an index is an ordered representation of the index data(in the memory).

## Searching an ordered data is a lot more faster then not ordered data.

# The `B-TREE` is balanced tree not binary tree.(but is still a tree)

## Searching is very fast and does not matter how big the data is, it's still going to perform well.

## It improves the read time but it makes writing slow(insert, update, delete), as you also need to update the index. So you need to have as many as necessary and as few as possible.

All the nodes on the left are smaller than the root node and all the nodes on the right are bigger than the root node, all the leaf nodes(bottom level) are all at the same level(same depth) of the tree so that's why it's called balanced tree.

## The leaf nodes also formed `W-link` list which can point each other.

It's important because we normally spend a lot of time searching the leaf nodes, as all the nodes pointing to each other so we can easily scan all the leaf nodes rather than going to the top and finding the leaf node every time.

## What data is stored in the `index`?

An index only contains the values of the columns that you actually put the index on.
So it only contains the data you put the index on.

But what if I need the data that is not on the index? how do I get back to the table if I want to find out what the other field values.

There is one other information is also stored in the index called `ROWID`, it uses this to find the actual row in the database and it is not `PK`.(it's a database internal identifier that identifies a particular row in the database.) So we can use it going back to the table and find and fetch the row.

# Understanding the execution plan

Just add `explain` or `EXPLAIN FORMAT=JSON`

```mysql
explain select * from video_downloads
```

We can add `EXPLAIN` before any sql query to show how the query is executed. `type` and `rows` are two important columns to look at

1. `type`: how the database is going to execute this query, how the database is going to access the data(access type).
   - `all` means that it's going to look at all the rows, no filtering, no sorting.
2. `rows`: how many records is the database going to look at.
3. `possible_keys`: the index may be used for the query.
4. `key`: the index is used for the query.

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
    - If you need more data that is not in the index tree then this is the worst case(it reads the data randomly and one by one and it's a read from disk) so this will cause a lot of `I/O`.

4.  `All`: Basically, when `type` is `all`, it's maybe the slowest.

    - Also know as a full table scan.
    - Does not use an index at all.
    - Loads every column of every row from the table.
    - Scans through all of them and emits or discards accordingly.
    - It does batching reading and read data sequentially, this may not cause as many `I/O` as it sounds.

# Examples & Pitfalls

1. Functions inside sql query does not normally work, unless your database have the function base index feature. (try to avoid computed columns)

```mysql
select sum(video_id)
from video_downloads
where 2019 = year(download_at)
```

This is correct(171446186), but it's slow so we need to improve. So we add an `index` on `download_at` column.

But it's still the same slow after adding the `index`. Why? (let's see the query plan using `explain`)

The `type` is `ALL` and the `rows` is all the tables rows. `possible_keys` and `key` are null so the database does not think that there is any key can be used and not going to use any keys.

Because in the where we have a function call `year`, so the database does not know how to use the index on the column even you have an index on it.

So the solution is use native datetime comparison(right?):

```mysql
select sum(video_id)
from video_downloads
where download_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
```

But, that's still `type` `ALL`, there is the index for `possible_keys` but for the `key` it's still not using it.

You may want to try this by forcing the index:

```mysql
select sum(video_id)
from video_downloads force index (video_downloads_download_at_index)
where download_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
```

But it's still slow.

The reason is that we only have an index on `download_at` but not `video_id` and the query asking for the `video_id` as well. So the database needs to do a lot of read from disk one by one randomly whereas if it's `all` which is full table scan it's going to do batching reading and sequentially so it's much faster and much less `I/O`.
(sometimes even the `key` is null but the `possible_keys` may still take effect which is super strange)

So, we need to add two columns in the index. `download_at` and `video_id`.

```mysql
select sum(video_id)
from video_downloads
where download_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
```

`Using index` means using index only so this query only uses the index and can be performed in the memory.(no need to read from disk)

And then problem solved!

But for another query when we add another condition `user_id = 860` it's slow again:

```mysql
select sum(video_id)
from video_downloads
where download_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
and user_id = 860
```

Why because the `user_id` is not indexed so it's full table scan again.
So we just need to add the `user_id` to the index. But the rows is still not restrict to its full extend.

2. Multi column index only works from left to right. But you cannot skip column. The column order matters. An index of a and b is not the same as index of b and a.

So we will move the `user_id` in the second position. But still the same thing.

3. Inequality Operators stop the index.

So we will move the `user_id` in the first position. And everything works.

However now this is going to slow this query

```mysql
select sum(video_id)
from video_downloads
where download_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
```

Because `download_at` is not sorted again so it's slow, so there is no index that can satisfy all the queries.

Fun fact that sometimes that `possible_keys` and `key` are not always telling the truth, you need to check the query speed to judge whether it's reading from disk one by one or not.

Full talk can be found [here](https://www.youtube.com/watch?v=HubezKbFL7E)
