# Section 17: Non-JS Fallback Traditional Search
## 85. Traditional WordPress Searching
- Tạo một page mới: Search
- Tạo file custome giao diện: page-search.php. **Bạn phải để trường input với name là s và action về root url
```php
<form method="get" action="<? php echo esc_url(site_url('/')); ?>">
    <input type="search" name="s" >
</form>
```
> return false trong openOverlay() để js lại hoạt động wow

## 86. Traditional WordPress Searching (Part 2)
- Custom css cho phần kết quả search
- Tạo file mới: **search.php**
- Copy nội dung index.php sang và sửa css =))
```php
get_search_query();
// esc_html(get_search_query(false));
```
- Phần searchform lặp lại cũng có thể tạo file riêng WP hỗ trợ: searchform.php
```
// searchform.php
<form method="get" action="<? php echo esc_url(site_url('/')); ?>">
    <input type="search" name="s" >
</form>
```
Và sử dụng
```
get_search_form()
```
