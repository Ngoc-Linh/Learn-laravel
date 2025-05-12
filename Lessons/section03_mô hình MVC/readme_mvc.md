# Hiểu rõ mô hình MVC

- `MVC` viết tắt model-view-controller

## Khái niệm 
- là 1 mô hình kiến trúc phần mềm giúp chia tách giao diện và logic xử lý
- **Model**: quản lý dữ liệu và tương tác database
- **View** : hiển thị dữ liệu người dùng (giao diện)
- **Controller** : xử lý logic, trung gian giữa model và View.

*MVC giống như 1 quy tắc để tổ chức mã nguồn sao cho rõ ràng, dễ mở rộng. Laravel tuân theo chuẩn MVC này giúp chúng ta dễ bảo trì ứng dụng hơn*

`người dùng` -> `request(url)` -> `controller (controller -> model -> trả về dữ liệu)` -> `View (view <- dữ liệu từ controller)` -> `trả kết quả ra trình duyệt`

**hãy tưởng tượng MVC như 1 đội nhóm:**
- `model` là nhân viên kỹ thuật lo dữ liệu
- `controller` là quản lý - nhận yêu cầu, điều phối công việc 
- `View` là nhân viên thiết kế - lo phần hiển thị cho khách hàng

## cấu trúc thư mục trong laravel theo MVC

- **Model**: app/Models/User.php, Post.php,...
- **View**: resources/views/
- **Controller** : app/Http/Controllers/

*trong laravel mọi thứ đều được tách biệt rõ ràng theo MVC. khi bạn tạo 1 controller hoặc model bằng artisan , nó sẽ được đặt đúng chổ luôn*

- cách tạo controller bằng câu lệnh `php artisan make:controller UserController`
- cách tạo model bằng câu lệnh `php artisan make:model Post`