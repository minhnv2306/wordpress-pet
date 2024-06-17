# Section 14: WordPress REST API (AJAX)
## 67. Load WP Content with JS
> http://localhost:3000/wp-json/wp/v2/posts

```
$.getJSON(url, function(posts) {
    ...
});
```

## 68. Generate HTML Based on JSON
## 69. Conditional Logic Within Template Literal

```php
wp_localize_script('main-univeristy-js', 'universityData', array(
    'root_url' => get_site_url()
));
```
=> Đoạn code thêm 1 đoạn mã JS vào để lấy được link rool_url

![alt text](../images/Screen%20Shot%202022-02-19%20at%2022.52.44.png)

## 70. Quick Misc Edits
- Tách phần html search overlay ra file js
```js
// Search.js

addSearchHTML() {
    $("body").append(`
        <h1> This is overlay searching </h1>
    `);
}
```
- Thêm focus khi mở modal
```
openOverlay() {
    setTimeout(function () {this.searchField.focus()}, 301) // cần setTimeout để có thời gian css div này
}
```
- Xoá giá trị input search khi open lại modal

## 71. Synchronous vs Asynchronous (Part 1)
(REST API: Working Witch Multiple Post Types)
- WP không hỗ trợ REST API với nhiều post type
- Cách đơn giản là gọi AJAX trong AJAX
```js
$.getJSON(url_search_post, function(posts) {
    $.getJSON(url_search_page, function(pages) {
        var combineResults = posts.concat(pages);
        
        // Show result html
        ...
    });
});
```
- Đây là cách nhanh nhất nhưng không phải là ý tưởng tốt dùng thực tế, chúng ta có thể có cách khác thay thế tốt hơn

> Synchronous vs Asynchronous

Để 2 AJAX chạy đồng bộ và khi xong hẳn cả 2 mới hiển thị kết quả ^^

## 72. Synchronous vs Asynchronous (Part 2)
Chúng ta cụ thể hoá ý tưởng trong bài trên
```js
$.when(a, b).then((posts, pages) => {
}, () => {
    // Hanlding error
}); // Khi load xong AJAX a.b thì then hiển thị kết quả
```
