# Middleware 

## Middleware là gì?
-`middleware` là 1 lớp trung gian giữa `request` của người dùng và `ứng dụng laravel`.
- nó kiểm soát và xử lý request trước khi được gửi đến controller.
- **được dùng để**
* xác thực người dùng
* kiểm tra quyền truy cập
* ghi log request
* bảo vệ CSRF
* xử lý header, CORS, ...

*bạn có thể tưởng tượng middleware giống như các trạm kiểm tra an ninh sân bay. trước khi hành khách(request) đến được cổng lên máy bay (controller), họ phải đi qua các bước kiểm tra (middleware)

## Middleware hoạt động ntn?
- luồng request: `người dùng -> middleware -> controller -> view -> người dùng`
- laravel có sẵn nhiều middleware mặc định :
* **auth** -> xác thực đăng nhập
* **verified** -> xác thực email
* **throttle** -> giới hạn số lần truy cập
* **csrf** -> bảo vệ chống tấn công CSRF

## Mục đích
- `xác thực người dùng(Authentication)`: kiểm tra xem người dùng đã đăng nhập hay chưa. nếu chưa, middleware có thể chuyển hướng đến trang đăng nhập
- `bảo vệ CSRF`: chống tấn công `Cross-site-request forgeny`. đối với các form html sử dụng phương thức post, put, patch hoặc delete trong file route `web.php` bạn cần thêm 1 trường CSRF token `(@csrf)` để yêu cầu k bị từ chối bới middleware này
- `ghi nhật ký (logging)`: middleware này có thể ghi lại tất cả yêu cầu đến ứng dụng của bạn
- `quản lý CORS`: middleware `HandleCors` được tự động bao gồm trong stack global middleware của laravel để xử lý các yêu cầu http options của CORS
- `giới hạn tốc độ(Rate Limiting)` middleware `throttle` được sử dụng để giới hạn số lượng request đến 1 route hoặc nhóm route
## vị trí và cách tạo

- tất cả middleware đều nằm trong thư mục `app/Http/Middleware`
- tạo middleware bằng lệnh `php artisan make:middleware EnsureTokenIsValid` -> sẽ tạo 1 file với tên EnsureTokenIsValid.php trong thư mục middleware của ứng dụng

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureTokenIsValid
{
    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    // chứa logic xử lý chính trong phương thức handle
    // $request đại diện cho yêu cầu http đang đến
    // $next : là 1 Closure đại diện cho middleware tiếp theo trong chuỗi hoặc hành động cuối cùng của route
    // 
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->input('token') !== 'my-secret-token') {
            return redirect('/home');
        }

        return $next($request); // để cho phép yêu cầu tiếp tục xử lý đi qua middleware phải gọi $next($request)
    }
}
```

## đăng ký middleware (register middleware)

laravel cung cấp nhiều cách để đăng ký và sử dụng middleware

- `global middleware`: middleware chạy trên mọi yêu cầu http đến ứng dụng.
thêm nó vào stack middleware global trong file `boostrap/app.php` và sử dụng `(append)` ở cuối hoặc đầu `(prepend)`
```php
use App\Http\Middleware\EnsureTokenIsValid;
 
->withMiddleware(function (Middleware $middleware) {
     $middleware->append(EnsureTokenIsValid::class);
})
```

- `gán cho Route cụ thể (Assigning Middleware to Routes)`: sử dụng phương thức `middleware()` khi định nghĩa route. bạn có thể truyền class middleware hoặc allas. có thể thêm nhiều middleware bằng cách truyền 1 mảng

```php
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::get('/profile', function () {
    // ...
})->middleware(EnsureTokenIsValid::class); // sử dụng middleware class cho route cụ thể
// or dùng mảng truyền nhiều middleware
Route::get('/', function () {
    // ...
})->middleware([First::class, Second::class]);
```

- `loại trừ middleware (Excluding Middleware)` : sử dụng phương thức `withoutMiddleware()` .
phương thức này chỉ loại bỏ route middleware , k áp dụng cho global middleware

```php
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::middleware([EnsureTokenIsValid::class])->group(function () {
    Route::get('/', function () {
        // ...
    });
 
    Route::get('/profile', function () {
        // ...
    })->withoutMiddleware([EnsureTokenIsValid::class]);
});
// or
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
    Route::get('/profile', function () {
        // ...
    });
});
```
- `nhóm middleware (middleware group)`: gộp nhiều middleware dưới 1 tên duy nhất. điều này giúp dễ dàng gán cùng 1 tập hợp middleware cho nhiều route
* laravel có 2 nhóm middleware mặc định là `web` và `api`
* nhóm `web` tự động áp dụng cho các route trong `routes/web.php` bao gồm các tính năng như session state và CSRF protection
* nhóm `api` tự động áp dụng cho các route trong `routes/api.php` và chúng là stateless
```php
use App\Http\Middleware\First;
use App\Http\Middleware\Second;
 
->withMiddleware(function (Middleware $middleware) {
    $middleware->appendToGroup('group-name', [
        First::class,
        Second::class,
    ]);
 
    $middleware->prependToGroup('group-name', [
        First::class,
        Second::class,
    ]);
})
// or
Route::get('/', function () {
    // ...
})->middleware('group-name');
 
Route::middleware(['group-name'])->group(function () {
    // ...
});
```
- `Alias middleware` đặt bí danh ngắn gọn cho các class middleware dài và được định nghĩa trong file `boostrap/app.php` bằng phương thức `alias()`

```php
use App\Http\Middleware\EnsureUserIsSubscribed;
 
->withMiddleware(function (Middleware $middleware) {
    $middleware->alias([
        'subscribed' => EnsureUserIsSubscribed::class
    ]);
})
// sử dụng
Route::get('/profile', function () {
    // ...
})->middleware('subscribed');
```
* alias có 1 số middleware mặc định như `auth cho middleware Authenticate`,..
* bảng tổng hợp alias mặc định

| Alias              | Middleware Class                                                                 |
|--------------------|----------------------------------------------------------------------------------|
| `auth`             | Illuminate\Auth\Middleware\Authenticate                                          |
| `auth.basic`       | Illuminate\Auth\Middleware\AuthenticateWithBasicAuth                             |
| `auth.session`     | Illuminate\Session\Middleware\AuthenticateSession                                |
| `cache.headers`    | Illuminate\Http\Middleware\SetCacheHeaders                                       |
| `can`              | Illuminate\Auth\Middleware\Authorize                                              |
| `guest`            | Illuminate\Auth\Middleware\RedirectIfAuthenticated                               |
| `password.confirm` | Illuminate\Auth\Middleware\RequirePassword                                       |
| `precognitive`     | Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests                 |
| `signed`           | Illuminate\Routing\Middleware\ValidateSignature                                  |
| `subscribed`       | \Spark\Http\Middleware\VerifyBillableIsSubscribed                                |
| `throttle`         | Illuminate\Routing\Middleware\ThrottleRequests or ThrottleRequestsWithRedis      |
| `verified`         | Illuminate\Auth\Middleware\EnsureEmailIsVerified                                 |

- `sorting middleware (sắp xếp middleware)`: định nghĩa thứ tự ưu tiên thực hiện middleware bằng phương thức `priority()` trong `boostrap/app.php`

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->priority([
        \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Foundation\Http\Middleware\ValidateCsrfToken::class,
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ]);
})
```

## tham số middleware (middleware parameters)

- middleware có thể nhận thêm các tham số, vd có thể truyền role để kiểm tra người dùng
- tham số này truyền vào phương thức `handle()` sau tham số `$next()`

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureUserHasRole
{
    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    // dùng parameter role middleware
    public function handle(Request $request, Closure $next, string $role): Response
    {
        if (! $request->user()->hasRole($role)) {
            // Redirect...
        }

        return $next($request);
    }
}
```
- 
Bạn chỉ định tham số khi định nghĩa route bằng cách nối tên middleware và tham số bằng dấu `:`
```php
use App\Http\Middleware\EnsureUserHasRole;

Route::put('/post/{id}', function (string $id) {
    // ...
})->middleware(EnsureUserHasRole::class.':editor');
```
- Nếu có nhiều tham số, chúng được phân tách bằng dấu phẩy `,`
```php
Route::put('/post/{id}', function (string $id) {
    // ...
})->middleware(EnsureUserHasRole::class.':editor,publisher');
```