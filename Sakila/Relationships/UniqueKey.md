# UniqueKey (within `many-to-many` relationship).

## Use unique keys to avoid duplication.

```php
Schema::create('post_tag', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('post_id');
    $table->unsignedBigInteger('tag_id');
    $table->timestamps();

    //those two keys must be unique.
    //also index will be applied.
    $table->unique(['post_id', 'tag_id']);

    $table->foreign('post_id')->references('id')->on('posts')->onDelete('cascade');
    $table->foreign('tag_id')->references('id')->on('tags')->onDelete('cascade');
});
```
