# cài đặt laragon and tạo dự án laravel đầu tiên

## cài đặt laragon
- cài đặt phiên bản Laragon 6 (free life- time) <https://github.com/leokhoa/laragon/releases/tag/6.0.0> chọn laragon-wamp.exe để cài đặt

- còn từ phiên bản Laragon từ 7.0 thì sẽ bắt thu phí từ nhà phát hành

### cách sử dụng laragon
- tải thêm php <https://windows.php.net/download/> chọn `Zip` tải về và giải nén -> copy folder php vào thư mục laragon/bin/php 
- tạo dự án laravel: trong laragon(chuột phải) chọn quick app/laravel
## cấu trúc folder laravel 12
`laravel` là 1 framework php mạnh mẽ và phổ biến, được thiết kế với cấu trúc thư mục rõ ràng, giúp lập trình viên dễ dàng quản lý mã nguồn .
- `public`: thư mục chứa file static
- `storage` : thư mục chứa file cache, log, session
- `boostrap`: thực mục chứa file autoload, helper, service provider
- `app` thư mục chứa file controller, model, request, view
- `database`: thư mục chứa file migration, seed
- `resouces`: chứa file view, layout, component
- `tests` : chứa file test
- `vendor`: chứ file thư viện
- `composer.json`: file chứa thông tin về dự án
- `composer.look` file chứa thông tin về thư viện
- `routes`: file chứa thông tin đường dẫn
- `config` chứa file cấu hình
- `public/index.php`: file chứa thông tin về khởi tạo dự án

### hiểu rõ cấu trúc thư mục trong laravel

1. `app/` - trung tâm logic của ứng dụng

đây là nơi chứa **logic cốt lõi** của laravel. bạn sẽ làm việc ở đây rất thường xuyên.

**các thư mục con**
- `Http/`: thư mục này cầu nối giữa **request từ người dùng** và **phản hồi từ ứng dụng** gồm 3 phần quan trọng : Controllers, Middleware, Form Request
    + `Controllers`(bộ điều khiển): chịu trách nhiệm **xử lý request** , gọi model, và trả về responsive (thường là view or JSON). Mỗi controller thường đại diện cho 1 nhóm chức năng như thêm-xoá-sửa, vd: UserController, ProductController. tạo controller bằng lệnh `php artisan make:controller UserController` lúc này file sẽ được tạo trong `Http/Controllers`
    + `Middleware`: lớp bào vệ giữa request và controller: dùng để kiểm tra hoặc can thiệp vào request trước khi nó đến controller. ví dụ xác thực người dùng (auth), kiểm tra quyền (isAdmin), giới hạn truy cập Ip... tạo middleware bằng lệnh `php artisan make:middleware AuthMiddleware`
    + `Requests`: kiểm tra dữ liệu đầu vào: đây là nơi xử lý form request validation - kiểm tra dữ liệu người dùng gửi lên (từ form hoặc api). giúp tách phần kiểm tra ra khỏi controller => sạch sẽ, dễ bảo trì.
    ví dụ: `LoginRequest`, `CreateUserRequest`
    tạo request bằng câu lệnh `php artisan make: request LoginRequest`

- `Models/` nơi đặt các class model , đại diện cho các bảng trong csdl
- `Providers/` nơi đăng ký các service providers (khởi tạo login hoặc dịch vụ)

2. `boostrap` khởi động ứng dụng

- chứa file `app.php` để khởi tạo và boostrap ứng dụng
- thư mục `cache/` : chứa file cache cấu hình giyps tăng tốc độ xử lý

3. `config/` cấu hình hệ thống

chứa tất cả các tệp cấu hình của ứng dụng.

**ví dụ**
- `app.php` cấu hình tên app, timezone, locale,...
- `database.php`: thông tin kết nối với csdl...
- `mail.php` cấu hình gửi mail
**tip**: laravel tự động load tất cả các file trong thư mục `config`

4. `database/` tất cả liên quan tới dữ liệu

- `migrations/` : quản lý các thay đổi cấu trúc bảng
- `seeders/` thêm dữ liệu mẫu cho bảng
- `factories` tạo dữ liệu mẫu để test


**gợi ý** có thể chạy `php artisan migrate --seed` để tạo bảng và dữ liệu mẫu nhanh chóng

5. `public/` cổng vào của ứng dụng

- đây là thư mục duy nhất trình duyệt web truy cập được. 
- chứa `index.php` - file chạy chính của laravel
- chứa tài nguyên tĩnh : ảnh, css, javascript

**nếu deploy online** thì cấu hình web server trỏ vào thư mục `public/`

6. `resources/` giao diện người dùng và ngôn ngữ

- `views` : nơi chứa các file Blade template(html).
- `lang/` chứa file đa ngôn ngữ
- `js`,`css` : nơi viết front end (nếu dùng laravel Mix/vite)

**thường dùng nhiều nhất là views/** để tạo giao diện.

7. `routes/` định nghĩa các đường dẫn trong ứng dụng (url)

- `web.php` route cho giao diện web
- `api.php` route cho Restful API
- `console.php` định nghĩa lệnh Artisan
- `channels.php` dùng cho broadcast event qua websocket

**khi bạn truy cập 1 đường dẫn** thì laravel sẽ kiểm tra route ở đây trước

8. `storage` lưu trữ tạm và người dùng

- `app/` lưu các file upload từ người dùng
- `framework` cache, sessions, views..
- `logs` ghi log hoạt động ứng dụng
**khi deploy** thì thư mục này cần phân quyền ghi

9. `tests/` kiểm thử ứng dụng

- `Feature` kiểm thử tính năng (sát với thực tế)
- `Unit` kiểm thử đơn vị (logic nhỏ, độc lập)

**Laravel sử dụng phpUnit** để test, bạn có thể viết test để kiểm tra tính ổn định hệ thống

10. `vendor/` thư viện bên thứ 3 (package)

chứa toàn bộ package được cài qua composer. bạn k nên chỉnh sửa trực tiếp file trong này

11. các tệp gốc quan trọng

- `.env` cấu hình môi trường (database, private key,...)
- `composer.json` khai báo các dependency
- `artisan` CLI giúp chạy các lệnh laravel như migrate, route


