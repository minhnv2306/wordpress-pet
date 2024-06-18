# Section 18: User Roles and Permissions
## 87. User Roles and Permissions
- Menu: User => Add New
- Role:
    - Subscriber
    - Contributor
    - Author
    - Editor
    - Administrator
- Login URL: /wp-admin

> Chúng ta có thể tạo một role mới với các quyền bạn muốn (ví dụ Event Planner)? => Plugin

- Plugin: members role editor. Members - Membership & User Role Editor Plugin - by MemberPress (200.000+)
- Truy cập Members => Roles ở Sidebar
- Tiếp theo chỉnh trong code để có quyền mới

```
register_post_type('event', array(
    'capability_type' => 'event', // 'post' is default value
    'map_meta_cap' => true
));
```
>  'map_meta_cap' => true
That's how flexible WordPress is. It would allow you to set something like that up. But most of the time, we do not need that.

So this line of code is our way of just saying, hey, WordPress, can you please reasonably require event related permissions in order to edit and manage event post types?

Xuất hiện chỉnh sửa các quyền cho Events => Cool

## 88. Open Registration

- Admin Dashboard => Setting. Tại đây sẽ có tùy chọn **Membership** và **New User Default Role**
- Link đăng ký: /wp-signup.php
> Lesson cần chưa setting email, do đó tạm vào admin để cập nhật mật khẩu cho tài khoản đăng kí để có thể login

- Quyền mặc định là Subscriber nên sẽ không có nhiều menu bên Sidebar
- Phần tiếp theo custom các link login, signup và logout tùy trạng thái user =))
> is_user_logged_in(), wp_logout_url(), wp_login_url(), wp_registration_url(), get_avatar(get_current_user_id(), 60)
- Chỉnh sửa login front-end user, redirect homepage thay vì admin dashboard
```PHP
// function.php
// Redirect subscriber accounts out of admin and onto homepage
add_action('admin_init', 'redirectSubsToFrontend');

function redirectSubsToFrontend() {
    $ourCurrentUser = wp_get_current_user();
    
    if (count($ourCurrentUser->roles) == 1 AND $ourCurrentUser->roles[0] == 'subscriber') {
        wp_redirect(site_url('/'));
        
        exit;
    }
}
```
- Bỏ admin header
```PHP
// function.php
// Redirect subscriber accounts out of admin and onto homepage
add_action('wp_loaded', 'noSubsAdminBar');

function noSubsAdminBar() {
    $ourCurrentUser = wp_get_current_user();
    
    if (count($ourCurrentUser->roles) == 1 AND $ourCurrentUser->roles[0] == 'subscriber') {
        show_admin_bar(false);
    }
}
```

## 89. Open Registration (Part 2)
- Custom màn hình login (Frontend)
```PHP
// function.php
// Customize Login Screen
add_filter('login_headerurl', 'ourHeaderURL');

function ourHeaderURL() {
    return esc_url(site_url('/'));
}
```
- Mặc định WP ko sử dụng custom css trong login, bạn cần thêm
```PHP
// function.php
// Customize CSS
add_action('login_enqueue_scripts', 'outLoginCSS');

function outLoginCSS() {
    wp_enqueue_style(...);
}
```
- File css của bạn custom lại các css mặc định WP là được
- Thay đổi tiêu đề "Powered by WordPress"
```php
// function.php
add_filter('login_headertitle', 'ourLoginTitle');

function ourLoginTitle() {
    return get_bloginfo('name');
}
```
