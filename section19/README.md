# Section 19: User Generated Content
## 90. "My Notes" Feature
- CRUD từ phía người dùng (front-end)
- Tạo page: my-notes. Custom view với file page-my-notes.php
- Cần check nếu user chưa login, redirect về homepage chứ không được xem my notes page

```php
// page-my-notes.php
<?php
if (!is_user_logged_in()) {
    wp_redirect(site_url('/'));
    exit;
}
```
- OK, vậy chúng ta sẽ lưu các note vào đâu => Tạo một post type mới là note trong function.php
```PHP
// Program Post Type
register_post_type('note', array(
    'show_in_rest' => true,
    'supports' => array('title', 'editor'),
    'public' => false, //needed setting, nhưng lại ẩn trong admin
    'show_ui' => true, // show in admin
    'labels' => array(
        'name' => 'Notes',
        'add_new_item' => 'Add New Note',
        'edit_item' => 'Edit Note',
        'all_items' => 'Add Notes',
        'singular_name' => 'Note'
    ),
    'menu_icon' => 'dashicons-welcome-write-blog'
));
```
- Tiếp theo truy vấn hiển thị notes trong page thôi
```php
<?php
$userNotes = new WP_Query(array(
    'post_type' => 'note',
    'posts_per_page' => -1,
    'author' => get_current_user_id()
));
?>
```
- Hiển thị nội dung là html: `wp_strip_all_tags(get_the_content());`

## 91. "My Notes" Front-end Part 1
- Delete note trước
- Xử lý js trước. Tham khảo MyNote.js

## 92. "My Notes" Front-end Part 2
- Connect REST API
- Các API
	+ `localhost:3000/wp-json/wp/v2/note`
	+ `localhost:3000/wp-json/wp/v2/note/137`
- Delete request
```js
deleteNote() {
	$.ajax({
		url: universityData.root_url + '/wp-json/wp/v2/note/137'
		type: 'DELETE'
		success: (response) => {
			console.log("Congrats");
			console.log(response);
		}
		error: () => {
			console.log("Sorry");
			console.log(response);
		}
	});
}
```
- Lúc này sẽ bị lỗi 401 Unauthorized. Để khắc phục lỗi này, cần thêm nonce vào script. Trở lại function.php
```php
// function.php
function university_files() {
	...
	wp_localize_script('main-university-js', 'universityData', array(
		'rool_url' => get_site_url(),
		'nonce' => wp_create_nonce('wp_rest')
	));
}
```
```js
// MyNote.js
deleteNote() {
	$.ajax({
		beforeSend: (xhr) => {
			xhr.setRequestHeader('X-WP-Nonce', universityData.nonce);
		},
		...
	});
}
```

## 94. Delete Posts with the REST API
- Fix sửa hard code 137 phần trên => sử dụng data-id
- Xử lý js remove element nữa

## 95. Edit / Update Posts with the REST API
```js
updateNote() {
	var ourUpdatedPost = {
		'title': thisNote.find(".note-title-field").val(),
		'content': thisNote.find(".note-body-field").val(),
	}
	
	$.ajax({
		url: universityData.root_url + '/wp-json/wp/v2/note/137'
		type: 'POST',
		data: ourUpdatedPost,
		success: (response) => {
			console.log("Congrats");
			console.log(response);
		}
		error: () => {
			console.log("Sorry");
			console.log(response);
		}
	});
}
```

## 96. Creating New Notes
```js
createNote() {
	var ourNewPost = {
		'title': $(".new-note-title").val(),
		'content': $(".new-note-body").val(),
		'status': 'publish' // or 'private'
	}
	
	$.ajax({
		url: universityData.root_url + '/wp-json/wp/v2/note/'
		type: 'POST',
		data: ourNewPost,
		success: (response) => {
			console.log("Congrats");
			console.log(response);
		}
		error: () => {
			console.log("Sorry");
			console.log(response);
		}
	});
}
```

## 97. Creating New Notes (Part 2)
- Chỉnh js append post

## 98. Note Permissions and Security (Part 1)
- Cần để post ở chế độ private
```php
// function.php

// Force note posts to be private
add_filter('wp_inser_post_data', 'makeNotePrivate');

function makeNotePrivate($data) {
	if ($data['post_type'] == 'note' AND $data['post_status'] != 'trash') {
		$data['post_status'] = "private";
	}
	
	return $data;
}
```

## 99. Note Permissions and Security (Part 2)
- Lọc filter HTML
- Admin Sidebar => Users => General => Unfiltered HTML
> esc_attr(get_the_content()); esc_html(get_the_content()); esc_textarea(get_the_content());

- Escape luôn từ lúc thêm vào
```php
// function.php

// Sanitize textarea các thẻ html luôn với editor
function makeNotePrivate($data) {
	if ($data['post_type'] == 'note') {
		$data['post_content'] = sanitize_textarea_field($data['post_content']);
		$data['post_title'] = sanitize_textarea_field($data['post_title']);
	}
	...
}
```

## 100. Per-User Post Limit
- Giới hạn số bài post cho mỗi người dùng
```php
// function.php

add_filter('wp_inser_post_data', 'makeNotePrivate', 10, 2);

// Escape các thẻ html luôn với editor
function makeNotePrivate($data, $postarr) {
	if ($data['post_type'] == 'note') {
		if (count_user_posts(get_current_user_id(), 'note') > 4 AND !$postarr['ID']) { // check cho trường hợp tạo mới
			die("You have reached your note limit");
		}
	}
	...
}
```

## 102. jQuery Free My Notes
- Không sử dụng jQuery, sử dụng JS thuần =))
