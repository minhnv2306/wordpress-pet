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

- Thay đổi size ảnh: Nếu dùng hàm trên sẽ lấy ảnh gốc và size quá lớn

    Di chuyển vào thư mục upload và bạn sẽ thấy rất nhiều ảnh với các size khác nhau

![alt text](../images/Screen%20Shot%202022-02-16%20at%2021.43.50.png)

- File `function.php`
```php
add_image_size('professorLandscape', 400, 260, true); // true = cropping
add_image_size('professorPortrait', 480, 650, true); // true = cropping
```

- Tuỳ thuộc kích thước ảnh gốc mà WP sinh các ảnh mới với kích thước khác nhau (sinh 4 ảnh hoặc 6 ảnh có kích thước portrait). Do đó ta cần đồng bộ toàn bộ thumbnail cần 6 ảnh để tránh not found
- Plugin: search: regenerate thumbnails (1M+ active installs By Alex Mills)
- Sau khi cài plugin vào Tools => Regen Thumbnails. Sau đó nó sẽ sinh thêm cho bạn toàn bộ mỗi thumbnail 6 ảnh (chứ không còn 4 ảnh như trước nữa)

Tuyệt!

## 43. Featured Image Sizes & Cropping
- Sử dụng các ảnh đã custom kích thước ở trên
```php
the_post_thumbnail('professorPortrait');
the_post_thumbnail_url('professorLandscape');
```
- Crop: mặc định WP sẽ crop giữa ảnh (crop towards very center of the image)
```php
add_image_size('professorLandscape', 400, 260, array('left', 'top'));
```

Nhưng cách này áp dụng cho tất cả các ảnh, như vậy không hay lắm vì có thể bị lệch. Ta cần một cách phương crop cho từng ảnh hiển thị đẹp

- Plugin: manual image crop tomasz. Tuy plugin đã không được cập nhật từ 5 năm trước nhưng nó vẫn hoạt động ổn 😄
- Vào sửa một Professor, edit ảnh, lúc này sẽ thêm tuỳ chọn Crop Image

![alt text](../images/Screen%20Shot%202022-02-16%20at%2022.03.03.png)
![alt text](../images/Screen%20Shot%202022-02-16%20at%2022.07.09.png)

Ngon, ko cần thay đổi code, F5 lại web và cảm nhận ảnh được crop xịn xò =)) (Ảnh có thể bị cache lần đầu load trang nhưng không sao. Product có nhiều người xem nên người load lần đầu bị cache cũng không để ý đâu =)))
