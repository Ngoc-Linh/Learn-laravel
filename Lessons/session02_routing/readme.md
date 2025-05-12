# routing

Routing là cách laravel xử lý các request đến từ trình duyệt. nó xác định url nào sẽ gọi đến hàm xử lý nào.
## vị trí file routes
 được nằm trong thư mục routes
- `routes/web.php` - dành cho các route giao diện người dùng , mô hình mvc
- `routes/api.php` dành cho các API

## tạo route cơ bản

bạn có thể định nghĩa 1 route với các phương thức http như GET, POST, PUT, PATCH, DELETE,...

```php
// trường hợp sử dụng view 
use Illuminate\Support\Facades\Route;
 
Route::get('/greeting', function () {
    return 'Hello World';
});
// or 
Route::get('/', function () {
    return view('welcome');
});
// nếu sử dụng controller
use App\Http\Controllers\UserController;
 
Route::get('/user', [UserController::class, 'index']);
```

## tạo route với tham số

- vd : users/{id} => apply tất cả đường dẫn /users/1 , /users/2...
- chúng ta có thể truyền tham số vào url như /users/5 và lấy nó trong route bằng  cách đặt {id}. ngoài ra có thể giới hạn kiểu dữ liệu bằng regex
- có 2 loại tham số: 

1. tham số bắt buộc : `Required Parameters`

```php
Route::get('/user/{id}', function (int $id) {
    return 'User '.$id; // 'User {$id}'
});
```
trường hợp kết hợp với dependency

```php
use Illuminate\Http\Request;
 
Route::get('/user/{id}', function (Request $request, string $id) {
    return 'User '.$id;
});
```

2. tham số không bắt buộc: `optional parameters`
bằng cách sử dụng thêm ký tự `?` trước url và khai báo giá trị mặc định của biến khi k truyền name

```php
Route::get('/user/{name?}', function (?string $name = null) {
    return $name;
});
 
Route::get('/user/{name?}', function (?string $name = 'John') {
    return $name;
});
```

3. ràng buộc biểu thức chính quy : `regular expression constraints`

- sử dụng `where` đẻ ràng buộc định dạng tham số bằng regex.
```php
Route::get('/user/{name}', function (string $name) {
    // ...
})->where('name', '[A-Za-z]+');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->where('id', '[0-9]+');
 
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```
- laravel cung cấp các helper method tiện ích như `whereNumber()`, `whereAlpha()`,`whereAlphaNumeric()`,`whereUuid()`, `whereUlid()`,`whereIn()` nếu k khớp với ràng buộc , laravel trả về lỗi 404
```php
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->whereNumber('id')->whereAlpha('name'); // id phải là kiểu số, name chỉ chứa chữ cái
 
Route::get('/user/{name}', function (string $name) {
    // ...
})->whereAlphaNumeric('name'); // name chữ hoặc số và k có ký tự đặc biệt
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUuid('id');// id là chuỗi Uuid hợp lệ (vd 550e8400-e29b-41d4-a716-446655440000)
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUlid('id'); // id là chuỗi Ulid (vd 01F8MECHZX3TBDSZ7XRADM79XV)
// chấp nhận các giá trị nằm trong mảng
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', ['movie', 'song', 'painting']); // category phải là các giá trị trong mảng như movie, song, painting

enum CategoryEnum: string {
    case Movie = 'movie';
    case Song = 'song';
    case Painting = 'painting';
}
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', CategoryEnum::cases());// thay vì truyền giá trị trong mảng cố định. dùng enum
```
## Đặt tên cho route(name route)

- bạn có thể đặt tên cho route để sử dụng trong helper function `route()` giúp code dễ bảo trì hơn. 
- khi đặt tên cho route, bạn có thể thay đổi url mà k ảnh hưởng tới các nơi sử dụng nó - rất tiện khi refactor
- bằng cách thêm `->name('...')` vào định nghĩa route. và tên route nên là duy nhất.
```php
Route::get('/user/profile', function () {
    // ...
})->name('profile');
// or đối với controller
Route::get(
    '/user/profile',
    [UserProfileController::class, 'show']
)->name('profile');
// or khi có key & value
Route::get('/user/{id}/profile', function (string $id) {
    // ...
})->name('profile');
 
$url = route('profile', ['id' => 1, 'photos' => 'yes']);
// /user/1/profile?photos=yes
```

## route group (gộp route vào 1 nhóm)

- tổ chức route gọn gàng, dễ đọc
- dùng `prefix` hoặc `middleware` để nhóm các route có cùng đặc điểm
1. `Route Prefixes` khi dùng `prefix` thêm url cho tất cả route trong nhóm

```php
Route::prefix('admin')->group(function () {
    Route::get('/users', function () {
        // Matches The "/admin/users" URL
    });
});
// or 
Route::name('admin.')->group(function () {
    Route::get('/users', function () {
        // Route assigned name "admin.users"...
    })->name('users');
});
```
2. `Middleware` áp dụng route group middleware

```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second middleware...
    });
 
    Route::get('/user/profile', function () {
        // Uses first & second middleware...
    });
});
```

3. `controllers` group route: định nghĩa controller chung nhóm
`controller(ControllerClass::class)`
```php
use App\Http\Controllers\OrderController;
 
Route::controller(OrderController::class)->group(function () {
    Route::get('/orders/{id}', 'show');
    Route::post('/orders', 'store');
});
```

4. `Subdomain`: xử lý route theo subdomain `domain('{subdomain}.example.com')`

```php
Route::domain('{account}.example.com')->group(function () {
    Route::get('/user/{id}', function (string $account, string $id) {
        // ...
    });
});
```

## Fallback route - xử lý lỗi 404

- `Route::fallback()` sẽ được gọi khi k có route nào khớp
- thay vì hiện lỗi 404 mặc định. bạn có thể hiển thị 1 trang tuỳ chỉnh cho đẹp và chuyên nghiệp hơn
```php
Route::fallback(function () {
    // ...
});
```

## xem dnah sách route
- sử dụng câu lệnh artisan `php artisan route:list`
- để kiểm tra toàn bộ routes trong ứng dụng, bạn dùng lệnh php artisan `route:list` . rất hữu ích khi debug hoặc kiểm tra tên route.
