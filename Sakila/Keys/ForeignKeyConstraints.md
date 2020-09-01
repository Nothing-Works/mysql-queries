# Foreign Key Constraints

## A constraint allows you to protect the integrity of your database tables.

`posts` and `comments` are `one-to-many`, A `post` has many `comments`.

```mysql
alter table comments
add foreign key (post_id) references posts(id) on delete cascade on update cascade;
```

`on delete cascade` means that if a `post` was deleted all the related comments will be deleted as well.

Some of the options:

1. cascade
2. restrict
3. no action
4. set null
5. set default

This is the same for `on update`.
