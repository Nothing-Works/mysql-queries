# Auto indexes

## Normally, Database will create indexes for both `PK` and `FK` automatically, `PK` is the clustered index and `FK` non-clustered index.

```php
Schema::create('post_reads', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('post_id');
    $table->unsignedBigInteger('user_id');
    $table->timestamps();

    $table->foreign('post_id')->references('id')->on('posts')->onDelete('cascade');
    $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
});
```
