# Section 10: Cleaner Code (Less Duplication)

## 46. Reduce Duplicate Code - Create Our Own Function
- Chúng ta sẽ để phần header ra một file riêng
- File `function.php`
```php
function pageBanner() {
    ?>
    <h1> This is page banner </h1>
<?php }
```
- Sử dụng ở view html: `single-professor.php`, `page.php`
```php
pageBanner();
```
- Ngoài ra chúng ta có thể "nâng cấp" hàm này để flexable hơn
```php
pageBanner(array(
    'title' => 'Hello there this is the title',
    'subtitle' => 'Hi, this is subtitle',
))
```
```php
// function.php
function pageBanner($args = NULL) {
  
  if (!$args['title']) {
    $args['title'] = get_the_title();
  }
 
  if (!$args['subtitle']) {
    $args['subtitle'] = get_field('page_banner_subtitle');
  }
 
  if (!$args['photo']) {
    if (get_field('page_banner_background_image') AND !is_archive() AND !is_home() ) {
      $args['photo'] = get_field('page_banner_background_image')['sizes']['pageBanner'];
    } else {
      $args['photo'] = get_theme_file_uri('/images/ocean.jpg');
    }
  }
}
```

> get_theme_file_uri('/images/ocean.jpg');

## 49. Using Our "pageBanner" Function
Truyền các tham số để sử dụng pageBanner function thôi
```php
pageBanner(array(
    'title' => 'All Events'
    'subtitle' => 'See what is going on in our world.',
));
```
