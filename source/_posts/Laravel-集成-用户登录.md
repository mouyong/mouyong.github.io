---
title: Laravel 集成用户登录
date: 2020-08-22 19:09:38
tags:
- Laravel
- 用户登录
english_title: Laravel-integrated-user-login
---

# Laravel 集成用户登录

## 安装

### 1. 安装 `laravel/ui`

`composer require laravel/ui`

### 2. 生成身份验证脚手架

`php artisan ui vue --auth`

### 3. 编译最新的脚手架资源

```
npm install
npm run dev
```

### 4. 更新 `App\Http\Controllers\Auth\LoginController`

重写 `username()` 与 `guard()`

```
<?php

namespace App\Http\Controllers\Auth;

use App\Models\User;
use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Auth;
use App\Providers\RouteServiceProvider;
use Illuminate\Foundation\Auth\AuthenticatesUsers;

class LoginController extends Controller
{
    /*
    |--------------------------------------------------------------------------
    | Login Controller
    |--------------------------------------------------------------------------
    |
    | This controller handles authenticating users for the application and
    | redirecting them to your home screen. The controller uses a trait
    | to conveniently provide its functionality to your applications.
    |
    */

    use AuthenticatesUsers;

    /**
     * Where to redirect users after login.
     *
     * @var string
     */
    protected $redirectTo = RouteServiceProvider::HOME;

    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('guest')->except('logout');
    }

    public function username()
    {
        return User::username();
    }

    public function guard()
    {
        return Auth::guard('web');
    }
}
```

### 5. 更新 `User` 模型

新增 `username()`、`getJWTIdentifier()`、`getJWTCustomClaims()`

```
<?php

namespace App;

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Tymon\JWTAuth\Contracts\JWTSubject;

class User extends \Jenssegers\Mongodb\Auth\User implements JWTSubject
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];

    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = [
        'email_verified_at' => 'datetime',
    ];

    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    public function getJWTCustomClaims()
    {
        return [];
    }

    public static function username()
    {
        $username = \request('username');

        $value = filter_var($username, FILTER_VALIDATE_INT);
        if ($value) return 'mobile';

        $value = filter_var($username, FILTER_VALIDATE_EMAIL);
        if ($value) return 'email';

        return 'name';
    }
}
```

#### 6. 确保 `RouteServiceProvider` 中包含 `mapWebRoutes()`

```
<?php

namespace App\Providers;

<...>

class RouteServiceProvider extends ServiceProvider
{
    <...>
    public function map()
    {
        <...>
        $this->mapWebRoutes();
        <...>
    }

    protected function mapWebRoutes()
    {
        Route::middleware('web')
            ->namespace($this->namespace)
            ->group(base_path('routes/web.php'));
    }
    <...>
}
```

#### 7. 确保 `routes/web.php` 包含 `/` 路由

```
<?php

use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Route;

<...>
Route::get('/', function () {
    return view('welcome');
});

Auth::routes();

Route::get('/home', 'HomeController@index')->name('home');
<...>
```

#### 8. 确保 `App\Exceptions\Handler` 可以正常抛出 `http` 错误

```
<?php


namespace App\Exceptions;

<...>
use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;

class Handler extends ExceptionHandler
{
    <...>
    public function render($request, Throwable $exception)
    {
        <...>

        if (! $request->wantsJson()) {
            return parent::render($request, $exception);
        }

        <...>
        return $this->renderApi($request, $exception);
    }
    <...>
}

```

## 参考

文档：https://learnku.com/docs/laravel/7.x/authentication/7474
