# Many-to-Many

## `many-to-many` relationship is done via a linking table.(you can also think of that it's two `one-to-many` relationships and from each side to the linking table)

### `php artisan make:migration create_$yourTableName_table`

```bash
post has many tags, tag has many posts.
posts and tags are many-to-many
// tags `belongsToMany` posts
// posts `belongsToMany` tags
```

## How to set up in `laravel`

In the `Migration` file.

```php
Schema::create('posts', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('user_id');
    $table->text('title')->nullable();
    $table->text('body')->nullable();
    $table->timestamps();

    $table->foreign('user_id')
          ->references('id')
          ->on('posts')
          ->onDelete('cascade');
});

Schema::create('tags', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('name');
    $table->timestamps();
});

Schema::create('post_tag', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('post_id');
    $table->unsignedBigInteger('tag_id');
    $table->timestamps();

    $table->foreign('post_id')->references('id')->on('posts')->onDelete('cascade');
    $table->foreign('tag_id')->references('id')->on('tags')->onDelete('cascade');
});
```

In the `Model` file.

```php
class Tag extends Model {
    public function posts()
    {
        return $this->belongsToMany(Post::class);
    }
}

class Post extends Model {
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}
```
