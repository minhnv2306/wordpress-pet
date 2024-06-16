## Section 3: First Coding Steps: PHP

### 8. Creating a New Theme
> Mọi thứ nhắc đến trong khoá học đều thao tác trong `app/public` của thư mục wordpress

Tạo thư mục trong `wp-content/themes` với tên `fictional-university-themes`. Trong thư muc tạo tiếp các file:
- index.php
- style.css
- screenshot.png
- single.php
- page.php (nếu không có file này, một page sẽ được coi là 1 post và sẽ đọc vào single.php)

Một vài hàm tham khảo

```php
bloginfo('name')
bloginfo('description')
have_posts(), the_post(), the_permalink(), the_title(), the_content()
```

## Section 4: WordPress Specific PHP
### 12. Header & Footer
- Tạo file `header.php`
- Để sử dụng thì gọi hàm `get_header()`
