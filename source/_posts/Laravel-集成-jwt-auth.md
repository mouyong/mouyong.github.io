---
title: Laravel 集成 jwt-auth
date: 2020-08-22 19:40:13
tags:
- Laravel
- jwt-auth
english_title: Laravel-integrated-with-JWT-auth
---

# Laravel 集成 jwt-auth

## 安装

### 1. 安装 jwt-auth

`composer require tymon/jwt-auth`

### 2. 发布配置

`php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"`

### 3. 生成密钥

`php artisan jwt:secret`

### 4. 配置环境变量

添加环境变量至相关文件: .env.example、.env etc...

```
# jwt 配置
JWT_SECRET=
JWT_TTL=60 # jwt token 失效时间，单位分钟，默认值 1 小时
JWT_REFRESH_TTL=20160 # jwt refresh token 失效时间，单位分钟，默认值 2 周
```

### 5. 用户认证

快速开始：https://jwt-auth.readthedocs.io/en/develop/quick-start/

#### 1). 更新 `User` 模型

实现 `Tymon\JWTAuth\Contracts\JWTSubject` 契约，在 `User` 模型中实现 `getJWTIdentifier()` 函数和 `getJWTCustomClaims()` 函数

```php
<?php

namespace App;

use Tymon\JWTAuth\Contracts\JWTSubject;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements JWTSubject
{
    use Notifiable;

    // Rest omitted for brevity

    /**
     * Get the identifier that will be stored in the subject claim of the JWT.
     *
     * @return mixed
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * Return a key value array, containing any custom claims to be added to the JWT.
     *
     * @return array
     */
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

#### 2). 配置身份认证守卫

- 修改 `config/auth.php` 的 `defaults.guard` 为 `api`
- 修改 `config/auth.php` 的 `guards` 信息，参考如下

```
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    # laravel 自带的 api 认证，使用数据库相关字段存储 api token 信息
    'laravel-api' => [
        'driver' => 'token',
        'provider' => 'users',
        'hash' => false,
    ],

    'api' => [
        'driver' => 'jwt',
        'provider' => 'users',
    ],
],
```

#### 3). 添加一些基本的身份验证路由

在 `routes/api.php` 中添加如下路由：

```
use App\Http\Controllers as Api;

Route::group([
    'middleware' => 'api',
    'prefix' => 'auth'
], function ($router) {
    Route::post('login', [Api\AuthController::class, 'login']);
    Route::post('logout', [Api\AuthController::class, 'logout']);
    Route::post('refresh', [Api\AuthController::class, 'refresh']);
    Route::post('me', [Api\AuthController::class, 'me']);
});
```

#### 4). 创建 `AuthController` 控制器

输入命令 `php artisan make:controller AuthController` 创建控制器

```
<?php

namespace App\Http\Controllers;

use Illuminate\Support\Facades\Auth;
use App\Http\Controllers\Controller;

class AuthController extends Controller
{
    /**
     * Create a new AuthController instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth:api', ['except' => ['login']]);
    }

    /**
     * Get a JWT via given credentials.
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function login()
    {
        $credentials = request(['email', 'password']);

        if (! $token = auth()->attempt($credentials)) {
            return response()->json(['error' => 'Unauthorized'], 401);
        }

        return $this->respondWithToken($token);
    }

    /**
     * Get the authenticated User.
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function me()
    {
        return response()->json(auth()->user());
    }

    /**
     * Log the user out (Invalidate the token).
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function logout()
    {
        auth()->logout();

        return response()->json(['message' => 'Successfully logged out']);
    }

    /**
     * Refresh a token.
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function refresh()
    {
        return $this->respondWithToken(auth()->refresh());
    }

    /**
     * Get the token array structure.
     *
     * @param  string $token
     *
     * @return \Illuminate\Http\JsonResponse
     */
    protected function respondWithToken($token)
    {
        return response()->json([
            'access_token' => $token,
            'token_type' => 'bearer',
            'expires_in' => auth()->factory()->getTTL() * 60
        ]);
    }
}
```

## 参考

- jwt-auth 文档: https://jwt-auth.readthedocs.io/en/develop/laravel-installation/