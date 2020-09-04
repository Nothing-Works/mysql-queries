# Sakila DB.

## [Download DB Link](https://dev.mysql.com/doc/index-other.html)

Download the sakila database `.zip` file.

## [Installation](https://dev.mysql.com/doc/sakila/en/sakila-installation.html)

Or import the `sakila-schema.sql` first and then import `sakila-data.sql` from any UI app.

## For `Laravel` get the `sql` query.

```php
DB::listen(function ($sql) {
    var_dump($sql->sql, $sql->bindings);
});
```

## SP for inserting data.

```mysql
DROP PROCEDURE IF EXISTS test;
DELIMITER #
CREATE PROCEDURE test()
BEGIN
DECLARE i INT UNSIGNED DEFAULT 1;
WHILE i < 100000 DO
    insert into video_downloads (user_id, video_id, download_at) values ((rand() * 10000),(rand() * 1000),FROM_UNIXTIME(
    UNIX_TIMESTAMP('2015-04-30 14:53:27') + FLOOR(0 + (RAND() * 315360000))
));
    SET i = i + 1;
END WHILE;
COMMIT;
END #
DELIMITER ;
```
