# Section 8: Programs Post Type

## 37. Creating Relationships Between Content

Tạo post type mới trong mu-plugin
```php
// Program Post Type
register_post_type('program', array(
    'show_in_rest' => true,
    'supports' => array('title', 'editor'),
    'rewrite' => array('slug' => 'programs'),
    'has_archive' => true,
    'public' => true,
    'labels' => array(
        'name' => 'Programs',
        'add_new_item' => 'Add New Program',
        'edit_item' => 'Edit Program',
        'all_items' => 'Add Programs',
        'singular_name' => 'Program'
    ),
    'menu_icon' => 'dashicons-awards' // WP Dashicons
));
```

> Khi tạo post type mới, có thể lỗi link url. Khi đó vào Setting => Permalink Settings nhấn Save Changes để tự động sinh lại các link gồm post type mới

- Tạo file `single-program.php` và `archive-program.php`
- Custom lại query cho program
```php
function university_adjust_queries($query) {
    if (!is_admin() AND is_post_type_archive('program') AND $query->is_main_query()) {
        $query->set('posts_per_page', -1);
        $query->set('orderby', 'title');
        $query->set('order', 'ASC');
    }
    ...
}
```
- Tạo quan hệ event với program qua custom fields: Related Program(s)
    - Chọn field type: Relationship
    - Post Type: Program
