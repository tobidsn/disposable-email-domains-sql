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
use Illuminate\Support\Str;
use App\DisposableDomain;

class AuthController extends Controller
{
    public function filter(Request $request)
    {
      $email = $request->input('email');

      $domain = Str::after($email, '@');
      $valid = DisposableDomain::where('domain', $domain)->first();
      if ($valid) {
          // Email not valid
      } else {
          // email valid
      }
    }
}
```

4. Create Validation rule

```
php artisan make:rule FakeEmail
// Rule created successfully.
```

Final code app/Rules/FakeEmail.php

```
<?php

namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;
use Illuminate\Support\Str;
use App\DisposableDomain;

class FakeEmail implements Rule
{
    /**
     * Create a new rule instance.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Determine if the validation rule passes.
     *
     * @param  string  $attribute
     * @param  mixed  $value
     * @return bool
     */
    public function passes($attribute, $value)
    {
        $email = filter_var($value, FILTER_VALIDATE_EMAIL);
        if ($email) {
            $domain = Str::after($email, '@');

            $exist = DisposableDomain::where('domain', $domain)->first();
            return $exist ? false : true;
        }

        return false;
    }

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
        return 'The :attribute must be a valid, non-disposable domain';
    }
}

```

Example Project
=============
[Example Laravel 7 Project with Authentication & Validation Fake Email](https://github.com/tobidsn/laravel-7-with-auth-validation-fake-email)


[Rules FakeEmail](https://github.com/tobidsn/laravel-7-with-auth-validation-fake-email/blob/master/app/Rules/FakeEmail.php)

[Controller RegisterController ](https://github.com/tobidsn/laravel-7-with-auth-validation-fake-email/blob/master/app/Http/Controllers/Auth/RegisterController.php)

[Model DisposableDomain ](https://github.com/tobidsn/laravel-7-with-auth-validation-fake-email/blob/master/app/DisposableDomain.php)