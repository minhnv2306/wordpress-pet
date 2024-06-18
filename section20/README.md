# Section 20: Like or "Heart" Count for Professors

## 103. Let Users "Like" Content (Part 1)
- Thêm heart icon cho professors
- Tạo thêm Like Post Type
```php
// Program Post Type
register_post_type('like', array(
    'supports' => array('title'),
    'show_ui' => true,
    'public' => false,
    'labels' => array(
        'name' => 'Likes',
        'add_new_item' => 'Add New Like',
        'edit_item' => 'Edit Like',
        'all_items' => 'Add Likes',
        'singular_name' => 'Like'
    ),
    'menu_icon' => 'dashicons-heart'
));
```
- Tạo custom field quan hệ professor và like, type Number tham chiếu professor ID (Liked Professor ID)
- Hiển thị ở FE
```php
// single-professor.php

<?php
$likeCount = new Query(array(
	'post_type' => 'like'
	'meta_query' => array(
		array(
			'key' => 'liked_professor_id',
			'compare' => '=',
			'value' => get_the_ID(),
		)
	)
));

$likeCount->found_posts;
```
- Check xem user hiện tại đã like post hay chưa?

```php
// single-professor.php

<?php
$existQuery = new Query(array(
	'author' => get_current_user_id(),
	'post_type' => 'like'
	'meta_query' => array(
		array(
			'key' => 'liked_professor_id',
			'compare' => '=',
			'value' => get_the_ID(),
		)
	)
));
```

## 104. Let Users "Like" Content (Part 2)
- Chỉ js cho phần click like

## 105. Creating Custom POST and DELETE Endpoints
- Tạo file mới inc/like-route.php tương tự như search thôi

```php
add_action('rest_api_init', universityLikeRoutes');

function universityLikeRoutes() {
    // POST /wp-json/university/v1/manageLike
    register_rest_route('university/v1', 'manageLike', array(
        'methods' => 'POST',
        'callback' => 'createLike',
    ));
    
    // DELETE /wp-json/university/v1/manageLike
    register_rest_route('university/v1', 'manageLike', array(
        'methods' => 'DELETE',
        'callback' => 'deleteLike',
    ));
}

function createLike() {
}

function deleteLike() {
}
```

## 106. Programmatically Create a Post
- Lập trình php tạo một Post
```php
function createLike($data) {
    $professor = sanitize_text_field($data['professorId']);
    
    return wp_insert_post(array(
        'post_type' => 'like',
        'post_status' => 'publish',
        'post_title' => 'Our PHP Create Post Test',
        // 'post_content' => 'Hello world 123',
        // Add custom field
        'meta_input' => array(
            'liked_professor_id' => $professor
        )
    ));
}

function deleteLike() {
}
```

## 107. Enforce Limit of One Like Per User/Teacher Combo
- Thực thi Permissions & Logic / Restrictions
- Đầu tiên check login hay chưa
```php
function createLike($data) {
    if (is_user_logged_in()) {
    } else {
        die("Only logged in users can create a like");
    }
    
    if (current_user_can('publish_note')) {
    }
}
```
> Đoạn code trên đã OK nhưng bạn cần truyền giá trị nonce bên AJAX để is_user_logged_in hợp lệ nhé

- Mỗi user chỉ được tạo 1 like cho một professor => Áp dụng limit đã học trước đó

## 109. Completing the LikeBox
- Chỉnh js cho like và delete like
- Sửa nốt PHP file
```php
function deleteLike($data) {
    $likeId = sanitize_text_field($data['like']);
    
    if (get_current_user_id() == get_post_field('post_author', $likeId) AND get_post_type($likeId) == 'like') {
        wp_delete_post($likeId, true);
        
        return "OK";
    } else {
        die("Permission denied");
    }
}
```

## 111. jQuery Free LikeBox
