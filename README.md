List SQL of disposable email domains
========================

This repo contains a list SQL of disposable and temporary email address domains often used to register dummy users in order to spam or abuse some services.

We cannot guarantee all of these can still be considered disposable but we do basic checking so chances are they were disposable at one point in time.

Example Usage
=============
**Laravel**

1. Make sure the passed email is valid. You can check that with [filter_var](https://secure.php.net/manual/en/function.filter-var.php)

```
if( filter_var($email, FILTER_VALIDATE_EMAIL))
{
    /*
     * Rest of your code
     */
}
```

2. Create a model instance is using the make:model Artisan command:

```
php artisan make:model DisposableDomain
// Model created successfully.
```

Edit model app/DisposableDomain.php

```
<?php

namespace App;
use Illuminate\Database\Eloquent\Model;

class DisposableDomain extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */

    protected $table = 'disposable_email_domains';
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [ 'id', 'domain'];
}
```

3. Use explode function and model in logic

```
<?php

namespace App\Http\Controllers\Auth;
use App\DisposableDomain;

class AuthController extends Controller
{
    public function filter(Request $request)
    {
      $email = $request->input('email');

      $domain = explode('@', $email);
      $valid = DisposableDomain::where('domain', $domain[1])
                                 ->first();
      if ($valid) {
          // Email not valid
      } else {
          // email valid
      }
    }
}
```
