# controller & resource controller

## controller là gì?
- controller là nơi xử lý logic giữa route với view (hoặc response JSON)
- laravel áp dụng mô hình MVC -> controller nằm giữa model và view, giúp tách biệt logic

## vị trí và tạo controller
- tạo controller bằng câu lệnh `php artisan make:controller UserController`
- vị trí nằm ở `app/Http/Controllers/`

## gắn controller vào route
- gắn route đơn lẻ
```php
// file app/Http/Controllers/UserController.php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\View\View;

class UserController extends Controller
{
    /**
     * Show the profile for a given user.
     */
    public function show(string $id): View
    {
        return view('user.profile', [
            'user' => User::findOrFail($id)
        ]);
    }
}
// gắn controller vào route - web.php
use App\Http\Controllers\UserController;
 
Route::get('/user/{id}', [UserController::class, 'show']);

```
- single action controller: khi bạn muốn controller thực thi 1 hành động duy nhất, thì chỉ cần thêm phương thức `__invoke` hoặc thêm file bằng lệnh `php artisan make:controller ProvisionServer --invokable`

```php
<?php

namespace App\Http\Controllers;

class ProvisionServer extends Controller
{
    /**
     * Provision a new web server.
     */
    public function __invoke()
    {
        // ...
    }
}
// file route 
use App\Http\Controllers\ProvisionServer;
 
Route::post('/server', ProvisionServer::class);
```
- group controller

## đăng ký middleware cho controller
- middleware có thể được gán cho các route của controller trong file route
```php
Route::get('/profile', [UserController::class, 'show'])->middleware('auth');
```
## Resource controller là gì?

## đăng ký resource controller trong Routes

## controller thường với Resource controller có gì khác nhau
- **controller thường**: 
    * tuỳ biến cao, viết tay từng route
    * phù hợp với logic đơn lẻ
    * ngắn gọn nhưng cần tổ chức cẩn thận
- **Resource controller**
    * laravel định nghĩa sẵn 7 method chuẩn
    * phù hợp với CRUD model
    * cấu trúc rõ ràng, chuẩn RESTful