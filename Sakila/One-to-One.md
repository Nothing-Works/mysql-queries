# One-to-One

## Where should we define the `FK`?

### It can be on either side depends on how you call it. (it's on the `child` side)

```bash
one user has a profile // FK will be on the profile side.
// user `hasOne` profile
// profile `belongsTo` user
```

## You can always inline the child in you parent table.(just depends on your need)

## How to set up in `laravel`

In the `Migration` file.

```php
Schema::create('profiles', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('user_id');
    $table->text('website')->nullable();
    $table->text('twitter_handle')->nullable();
    $table->timestamps();

    $table->foreign('user_id')
          ->references('id')
          ->on('users')
          ->onDelete('cascade');
});
```

In the `Model` file.

```php
class User extends Model {

    //this will make sure that every query from User model will include the `profile` relationship.
    protected $with = ['profile'];

    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
}

class Profile extends Model {
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```
