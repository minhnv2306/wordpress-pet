## Section 7: Events Post Type

### 28. Custom Post Types
Mặc định WP có 2 post type: Post và Page Sử dụng `function.php`
```php
register_post_type('event', array(
    'public' => true,
    'labels' => array(
        'name' => 'Events',
        'add_new_item' => 'Add New Event',
        'edit_item' => 'Edit Event',
        'all_items' => 'Add Events',
        'singular_name' => 'Event'
    ),
    'menu_icon' => 'dashicons-calendar' // WP Dashicons
));
```

Nếu bạn muốn custom type, phải sử dụng plugin

> MUST USE PLUGIN

- Tách ra mu-plugins/file_name.php
- Đặt ở đây thì có thay đổi plugin, themes, code event vẫn được load

### 29. Using The Modern Block Editor For Our Custom Post Type

### 30. Displaying Custom Post Types
Sử dụng custom query
```
$homepageEvents = new WP_Query(array(
    'posts_per_page' => 2,
    'post_type' => 'event',
));
```

Hiển thị event

Tạo file: single-event.php. Để event có thể có link /event
```php
register_post_type('event', array(
    'rewrite' => array('slug' => 'events'),
    'has_archive' => true,
    ...
));
```

Trang toàn bộ event

Tạo file: `archive-event.php`

### 31. Quick Timeout: Misc Updates
- Sử dụng excerpt để hiển thị blog thay vì description
- Event lại ko có trường này, cần config thêm trong register_post_type
```php
register_post_type('event', array(
    'show_in_rest' => true, // hiển thị đẹp như Post =)) - modern block. Kết hợp editor ở dưới
    'supports' => array('title', 'editor', 'excerpt'),
    ...
));
```

### 32. Custom Fields
Thêm trường cho date

Đầu tiên thêm custom fields

```php
register_post_type('event', array(
    'supports' => array('title', 'editor', 'excerpt', 'custom-fields'),
    ...
));
```

Nhưng

> Don't revinvent the wheel

> It's probably not a good idea.

> It wouldn't be a very efficient use of our time because other super talented people have already spent hundreds, if not thousands of hours creating the perfect custom field solutions.

Sử dụng Plugin (1 trong 2)
- **Advanced Custom Fields (ACF) - Ellit Condon**
- **CMB2 (Custom Metaboxes 2)**

Xoá 'custom-fields' bên trên đi và cài plugin

Sử dụng
```php
the_field('event_date');
```