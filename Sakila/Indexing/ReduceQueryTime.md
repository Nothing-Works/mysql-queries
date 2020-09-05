# Reduce Query Time

## Example

```mysql
select *
from video_downloads
where user_id = 1770
and downloaded_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
```

```mysql
select *
from payment
where customer_id = 1
and payment_date between '2005-01-01 00:00:00' and '2005-07-01 00:00:00'
```

This is ok because we had an index for `user_id` or `customer_id`, so the database does not need to inspect all the rows.

### However

If you do this:

```mysql
select *
from video_downloads
where downloaded_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
```

```mysql
select *
from payment
where payment_date between '2005-01-01 00:00:00' and '2005-07-01 00:00:00'
```

It's much slower than before because there is no index for `downloaded_at` or `payment_date`.

It can be even worse:

```mysql
select customer_id, count(*)
from payment
where payment_date between '2005-01-01 00:00:00' and '2005-07-01 00:00:00'
group by customer_id
```

```mysql
select user_id, count(*)
from video_downloads
where downloaded_at between '2019-01-01 00:00:00' and '2020-01-01 00:00:00'
group by user_id
```

Why it's so slow? Let's have a look at the `explain sql`:

- `type`: `index`.
- `rows`: the whole table rows.
- `key`: the name of the `user_id` index.

It's telling us that it's using `index` which is the index on `user_id`. But for this the query, we also have the `downloaded_at` filter so we can not only use `index` to get the result we have to use the `index` get the `rowId` find the record compare the `datetime` and decide whether we select it or not. Each compare is one `I/O`. So we need to do the total number of rows `I/O`(read from disk) and it's random read rather than reading data sequentially. So it's very slow.

Why it's even slower than a full table scan? (`type` is `ALL`).

- Because if the database knows it's a full table scan anyway, it's going to batch reading a few thousands rows at a time and also read all the data sequentially. The total `I/O` is much fewer than what we had.

More about the `type` `index`: you have an `index` but we are not using it in any efficient way. if you are using the `index` only then that's that not too bad. But if you have other columns involved then this may cause a big problem.

So if the type is `index`:

- The `Extra` has `Using Index`(which means `Using Index Only`), it's may be ok.
- The `Extra` does not have `Using Index`, the total number `I/O` will be the same as the number under `rows`.

if the type is `all`, it may be ok as the database will normally do batch reading.

So we need an index on the `downloaded_at` or `payment_date`.

Do not use `Functions` for indexed columns, like `year(download_at)`, the index will not work.(unless the database provide function based index)

(From `Kai`) What's happening in the first case (where the user*id index gets used) is that the database is performing a FULL INDEX SCAN. This means that it is using the index, but it is not \_limiting* the number of rows it has to look at. That's why the `rows` column still says 3.5 million. The problem is that you're also selecting `max(video_id)`. That column is not on the index. So that means for each of those 3.5 mio entries in the index, the database has to go back to the disk and _read_ the corresponding row in order for it too look at the video_id. That's 3.5 million reads from disk.

The full table scan performs much better, because it knows that it has to read the whole table anyways, so it's going to be smart about it and not read it one row at a time. Instead, it will read the table in batches and perform significantly fewer reads from disk.

What's confusing is, why the database would even use the index on `user_id` . Have you tried running an `ANALYZE video_downloads`? Maybe the index data is stale.

If you really want to speed up the query, you could put a composite index on ` (user_id, video_id)`. That way, the database could perform an `INDEX ONLY SCAN` because all the data the query needs is on the index. It could also take advantage of the fact that the video_id is sorted.

This is the [link](https://stackoverflow.com/questions/63739988/mysql-explain-type-index-vs-all-performance-question) from SO.

(From mysql docs) Sometimes MySQL does not use an index, even if one is available. One circumstance under which this occurs is when the optimizer estimates that using the index would require MySQL to access a very large percentage of the rows in the table. (In this case, a table scan is likely to be much faster because it requires fewer seeks.) However, if such a query uses LIMIT to retrieve only some of the rows, MySQL uses an index anyway, because it can much more quickly find the few rows to return in the result.
