# Clustered Index

A clustered index defines the order in which data is physically stored in a table. Table data can be sorted in only way, therefore, there can be only one clustered index per table. In SQL Server/MySQL, the primary key constraint automatically creates a clustered index on that particular column.

# Non-Clustered Indexes

A non-clustered index doesn’t sort the physical data inside the table. In fact, a non-clustered index is stored at one place and table data is stored in another place. This is similar to a textbook where the book content is located in one place and the index is located in another. This allows for more than one non-clustered index per table.

## When a table has a clustered index, the table is called a clustered table. If a table has no clustered index, its data rows are stored in an unordered structure called a heap.

## Normally, when we are talking about indexes we are talking about `Non-Clustered Indexes`. Because, we normally use the default `PK` as the Clustered Index. So we have the `PK` as the clustered index and some other `indexes` as `Non-Clustered Indexes`.
