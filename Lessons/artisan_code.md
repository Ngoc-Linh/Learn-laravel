# các câu lệnh thường dùng 

1. tạo project và cấu trúc

```bash
laravel new project-name # tạo project laravel mới (nếu dùng laravel installer)
composer create-project laravel/laravel project-name # cách khác để tạo project
```

2. server và debug

```bash
php artisan serve # chạy server local tại http://127.0.0.1:8000
php artisan route:list # xem tất cả route đã đăng ký
php artisan config:cache # cache lại file cấu hình
php artisan config:clear #xoá cache cấu hình
php artisan route:clear #xoá cache route
php artisan view:clear #xoá cache blade view
php artisan cache:clear #xoá cache hệ thống
```

3. Migratison và database
```bash
php artisan migrate #chạy các file migrartion
php artisan migrate:rollback # quay lại migration trước đó
php artisan migrate:refresh # rollback + migration lại toàn bộ
php artisan migrate:fresh # xoá toàn bộ bảng và migrate lại

```
4. model , controller ...
```bash
php artisan make:model Product # tạo model product
php artisan make:controller ProductController #tạo controller với file ProductController.php
php artisan make:migrartion create_product_table # tạo file migration
php artisan make:seeder ProductSeeder # tạo seeder
php artisan make:factory ProductFactory # tạo factory
php artisan make:middleware CheckAdmin # tạo middleware với file CheckAdmin.php
```

5. seeder và factory
```bash
php artisan db:seed               # Chạy seeder
php artisan db:seed --class=ProductSeeder  # Chạy seeder cụ thể
php artisan migrate:fresh --seed # Xóa DB và seed lại dữ liệu
```

6. Auth & scaffolding (Laravel Breeze, Jetstream, etc.)

```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
```
7. khác

```bash
php artisan tinker               # Môi trường dòng lệnh tương tác với Laravel
php artisan queue:work          # Xử lý hàng đợi (queue)
php artisan schedule:run        # Thực thi các tác vụ được lập lịch

```