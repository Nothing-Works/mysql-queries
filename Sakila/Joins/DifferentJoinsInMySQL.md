# The Difference Between MySQL Join Types

1. join/inner join: they are the same.

   - Only get the match up results. Will not include any result if there is no match.
   - Returns records that have matching values in both tables.

```mysql
select *
from store
join address
on address.address_id = store.address_id
```

```mysql
select *
from store
inner join address
on address.address_id = store.address_id
```

2. left join/left outer join: they are the same.
   - All the left table results will be included, Will include all results from the left table if there is no match. Also include all matches.
   - Returns all records from the left table, and the matched records from the right table.

```mysql
select *
from store
left join address
on address.address_id = store.address_id
```

3. right join/right outer join: they are the same.
   - All the right table results will be included, Will include all results from the right table if there is no match. Also include all matches.
   - Returns all records from the right table, and the matched records from the left table.

```mysql
select *
from store
right join address
on address.address_id = store.address_id
```

4. full join/full outer join: they are the same.
   - All results from both tables will be included, Will include all results from the right and left table if there is no match. Also include all matches.
   - Returns all records when there is a match in either left or right table.

```mysql
select *
from store
full join address
on address.address_id = store.address_id
```

5. cross join

   - If you don't specify a join condition when joining two tables, database system combines each row from the first table with each row from the second table. This type of join is called a cross join or a Cartesian product.

6. union

   - The `UNION` operator is used to combine the results of two or more `SELECT` queries into a single result set. The union operation is different from using joins that combine columns from two tables. The union operation creates a new table by placing all rows from two source tables into a single result table, placing the rows on top of one another.
   - These are basic rules for combining the result sets of two `SELECT` queries by using `UNION`:
     - The number and the order of the columns must be the same in all queries.
     - The data types of the corresponding columns must be compatible.
   - When these criteria are met, the tables are union-compatible:
   - The `UNION` operation eliminates the duplicate rows from the combined result set, by default.
   - If you want to keep the duplicate rows you can use the `ALL` keyword. `UNION ALL`
