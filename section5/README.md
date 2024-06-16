## Section 5: Pages

### 16. Interior Page Template
### 17. Parent & Children Page
`get_the_ID();` `wp_get_post_parent_id(get_the_ID());`

=> get dynamic `get_the_title(100)`
=> hàm `the_title()` lấy title của page hiện tại còn `get_the_title` lấy title của post bạn truyền ID vào `get_permalink(5)`

### 18. To Echo or Not To Echo
- Nếu tên hàm bắt đầu bằng get (get_the_id(), get_the_title()) thì nó sẽ trả về giá trị, không echo
- Nếu tên hàm bắt đầu the (the_title(), the_ID()), WP đã echo giá trị ra page cho bạn

Ngoài ra bạn có thể xem thêm 2 trang quan trọng sẽ nói cho các bạn biết hàm trả về giá trị hoặc output gì cho bạn
- https://codex.wordpress.org
- https://developer.wordpress.org

### 19. Menu of Child Page Links
Tạo các link cho Child Page
```php
wp_list_pages(array(
    'title_li' => NULL,
    'child_of' => get_the_ID(),
    'sort_column' => 'menu_order' // mặc định sắp xếp theo bảng chữ cái
));

get_pages(array(
    'child_of' => get_the_ID()
));
```

### 20. A Few Quick Edits / Improvements
Scale thiết bị
```
<meta name="viewport" content="width=device-width, initial-scale=1">
```

Ngôn ngữ web
```
<html <?php language_attributes(); ?>>
```

Charset
```
<meta charset="<?php bloginfo('charset'); ?>">
```

Lấy các class wp trong body
```
<body <?php body_class(); ?>>
```
