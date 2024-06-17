# Section 9: Professors Post Type

## 41. Professors Post Type

- Tạo một post type mới cho professors
- Tạo quan hệ Professor dạy nhiều Programs

> wp_reset_postdata() để vừa hiển thị dữ liệu professor, vừa hiển thị dữ liệu events

- Giải thích (xem thêm video): Do hàm the_id() đã bị thay đổi trong while nên cần reset lại dữ liệu. Sau khi sử dụng hàm wp_reset_postdata(), hàm the_id() sẽ trở về giá trị mặc định query của WP
