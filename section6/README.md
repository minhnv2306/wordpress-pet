## Section 6: Building the Blog Section
### 23. Blog Listing Page (index.php vs front-page.php)
Cấu hình bên Admin: Setting => Reading Setting. Setting các static page

File front-page.php
```php
the_excerpt();
the_author_posts_link();
the_time();
```
