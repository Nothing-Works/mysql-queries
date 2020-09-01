# Primary Key (most of the tables will have it, not all though)

1. Definition: The unique identifier for a specific record(a row in the table).

   - **Note: There will not be two rows with the same primary key. It needs `primary key` (will make sure the key is unique.) and may need `auto_increment` (will auto increment the id when a new record is inserted).**

2. Why: It's a pointer for a record, easy to identify the record.

# Foreign Key (mostly for a relationship between tables)

1. Definition: It is a reference to a primary key from another table. (The key that is pointing to a record on a foreign table by referencing its primary key.)

   - **For `one-to-many`: the many side has the `foreign key`.**
