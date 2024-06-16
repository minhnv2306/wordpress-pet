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

### 26. Custom Queries
Well, so far in the course, you and I have not had to manually query any content or data ourselves.

WordPress automatically queries the appropriate content for us based on the particular URL that we are visiting.

Query bất cứ gì bạn muốn. Sử dụng class `WP_Query`
```
$homepagePosts = new WP_Query(array(
    'posts_per_page' => 2
));

while ($homepagePosts->have_posts()) {
    $homepagePosts->the_post();
}
```

```
wp_trim_words(get_the_content(), 18);
wp_reset_postdata();
```
