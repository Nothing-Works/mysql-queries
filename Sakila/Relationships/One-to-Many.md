# One-to-Many

## Where should we define the `FK`?

### It has to be on the `child` side.

```bash
one user has many posts // FK will be on the post side.
// user `hasMany` posts
// post `belongsTo` user
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
```

In the `Model` file.

```php
class User extends Model {
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}

class Post extends Model {
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```
