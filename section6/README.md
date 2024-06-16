## Section 6: Building the Blog Section
### 23. Blog Listing Page (index.php vs front-page.php)
Cấu hình bên Admin: Setting => Reading Setting. Setting các static page

File front-page.php
```php
the_excerpt();
the_author_posts_link();
the_time();
```

### 24. Blog Continued
Tạo pagination: paginate_link();

### 25. Blog Archives (archive.php)
Trang category và author sử dụng `single_cat_title()`

Từ 2014 WP có hàm chung cho `archive the_archive_title();` `the_archive_description();`
