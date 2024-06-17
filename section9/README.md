# Section 9: Professors Post Type

## 41. Professors Post Type

- Tạo một post type mới cho professors
- Tạo quan hệ Professor dạy nhiều Programs

> wp_reset_postdata() để vừa hiển thị dữ liệu professor, vừa hiển thị dữ liệu events

- Giải thích (xem thêm video): Do hàm the_id() đã bị thay đổi trong while nên cần reset lại dữ liệu. Sau khi sử dụng hàm wp_reset_postdata(), hàm the_id() sẽ trở về giá trị mặc định query của WP

## 42. Featured Image (Post Thumbnail)
- Mặc định thumbnail không hỗ trợ
- Thêm vào hàm `function.php`
```php
add_theme_support('post-thumbnails');
```
- File `university-post-type.php` trong mu-plugins
```php
// Professor Post Type
register_post_type('professor', array(
    'supports' => array('title', 'editor', 'thumbnail'),
    ...
));
```
==> OK

- Hiển thị bên FE:
    - `the_post_thumbnail();` (hiển thị ảnh luôn nhé)
    - `the_post_thumbnail_url()` (cho src)
