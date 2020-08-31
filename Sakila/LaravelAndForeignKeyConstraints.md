# Laravel and Foreign Key Constraints

## Laravel migration examples.

```php
Schema::create('posts', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('title');
    $table->text('body');
    $table->timestamps();
    $table->timestamp('published_at')->nullable();
});
```

```php
Schema::create('comments', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('post_id');
    $table->unsignedBigInteger('user_id');
    $table->text('body');
    $table->timestamps();

    $table->foreign('post_id')
          ->references('id')
          ->on('posts')
          ->onDelete('cascade');
});
```
