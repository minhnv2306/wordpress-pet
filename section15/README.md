# Section 15: Customizing the REST API
## 73. REST API: Add New Custom Field
- Thêm các trường tên tác giả (author_name), các trường custom field trong kết quả trả về API
- File: function.php
```php
function university_custom_rest() {
    register_rest_field('post', 'authorName', array(
        'get_callback' => function() {
            return get_the_author();
        }
    ));
    
    register_rest_field('post', 'perfecltyCroppedImageURL', array(
        'get_callback' => function() {
            return ...;
        }
    ));
}

add_action('rest_api_list', 'university_custom_rest');
```

## 74. REST API: Add New Custom Route (URL)
> Để REST API mặc định work, lúc custom thêm post_type, bạn cần có tuỳ chon 'show_in_rest' => true
- Custom để gộp search theo nhiều post_type và thêm các điều kiện quan hệ

![alt text](../images/Screen%20Shot%202022-02-20%20at%2010.06.38.png)

- Tạo file trong theme inc/search-route.php
```php
// inc/search-route.php
<?php

add_action('rest_api_init', 'universityRegisterSearch');

function universityRegisterSearch() {
    register_rest_route('university/v1', 'search', array(
        'methods' => 'GET' // WP_REST_SERVER::READABLE,
        'callback' => 'universitySearchResults'
    ));
}

function universitySearchResults() {
    return 'Congratulations, you created a route';
}
// URL: http://localhost:3000/wp-json/university/v1/search
```
- Require trong function.php
```
require get_theme_file_path('/inc/search-route.php');
```

## 75. Create Your Own Raw JSON Data
Tiếp tục sửa file inc/search-route.php để return JSON nào

> WP tự convert các dữ liệu trong API sang JSON, bạn không cần lo lắng về JSON syntax nhé ^^

```php
...
function universitySearchResults() {
    $professors = new WP_Query(array(
        'post_type' => 'professor'
    ));
    
    $professorResults = array();
    
    while(professors->have_posts) {
        $professors->the_post();
        
        // Chỉ lấy title và permalink để hiển thị
        array_push($professorResults, array(
            'title' => get_the_title(),
            'permalink' => get_the_parmalink()
        ));
    }
    
     return $professorResults;
}
```

## 76. WP_Query and Keyword Searching
- Custom điều kiện search theo title

```
...
function universitySearchResults($data) {
    $professors = new WP_Query(array(
        'post_type' => 'professor',
        's' => sanitize_text_field($data['term']) // Kiểu dữ liệu trong request /search?term=abc
    ));
    
    ...
}
```

## 77. Working With Multiple Post Types
Làm điều này khá đơn giản vì chỉ thay post_type dạng mảng

```php
...
function universitySearchResults($data) {
    $professors = new WP_Query(array(
        'post_type' => array('post', 'page', 'professor'),
        ...
    ));
    
    ...
}
```
- Chỉnh sửa lại chút response data
```php
...
function universitySearchResults() {
    $mainQuery = new WP_Query(array(
        'post_type' => array('post', 'page', 'professor', 'program', 'campus', 'event'),
        's' => sanitize_text_field($data['term'])
    ));
    
    $result = array(
        'generalInfo' => array(),
        'professors' => array(),
        'programs' => array(),
        'events' => array(),
        'campuses' => array(),
    );
    
    while(mainQuery->have_posts) {
        $mainQuery->the_post();
        
        if (get_post_type() == 'post' OR get_post_type() == 'page') {
            array_push($result['generalInfo'], array(
                'title' => get_the_title(),
                'permalink' => get_the_parmalink()
            ));
        }
        
        // Các post type khác tương tự
        ...
    }
    
     return $result;
}
```
