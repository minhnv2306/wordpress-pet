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

## 38. Displaying Relationships (Front-End)
```php
$relatedPrograms = get_field('related_programs');

foreach($relatedPrograms as $program) {
    echo get_the_title($program);
}
```
Trỏ ngược lại từ program ra cha là event. Chúng ta sẽ không phải tạo custom field program là event nhé? So what should we do?

Chúng ta sẽ sử dụng custom queries hoặc 1 thứ gì đó tương tự.

```php
$homepageEvents = new WP_Query(array(
    'posts_per_page' => 2,
    'post_type' => 'event',
    'meta_key' => 'event_date',
    'orderby' => 'meta_value_num',
    'order' => 'DESC',
    'meta_query' => array( // kiểu and where
        array(
            'key' => 'event_date',
            'compare' => '>=',
            'value' => date('Ymd'),
            'type' => 'numeric'
        ),
        array(
            'key' => 'related_programs',
            'compare' => 'LIKE', // contains (xem thêm video giải thích LIKE là OK)
            'value' => '"' . get_the_ID() . '"',
        )
    )
));
```

## 39. Quick Program Edits
Chỉnh css với custom hiển thị dữ liệu trống thôi 😄
