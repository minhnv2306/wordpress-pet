## Section 7: Events Post Type

### 29. Custom Post Types
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
