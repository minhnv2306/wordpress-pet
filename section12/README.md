# Section 12: Campus Post Type
## 55. Campus Post Type

> Sử dụng Google map API phải trả phí nhé

- Tạo Campus Post Type mới trong mu-plugins
- Tạo custom file Map Location, Type JQuery Google Map
> Lesson hướng dẫn tạo Goolge Map API, lấy token và sử dụng

- File function.php
```php
function universityMapKey($api) {
    $api['key'] = 'amJdkfgsdKKKKdv';
    
    return $api;
}
add_filter('acf/fields/google_map/api', 'universityMapKey');
```
- Load lại trang Campus, google map đã hoạt động. Awesome!

## 56. Campus Map on Front-End
- Tạo các file archive-campus.php và single-campus.php
- Hiển thị map
```php
$mapLocation = get_field('map_location');

<div class="marker" data-lat="<?php echo $mapLocation['lat'] ?>" data-lng="<? php echo $mapLocation['lng'] ?>"></div>
```
- Thêm xử lý js
- File: functions.php
```php
function university_files() {
    wp_enqueue_script('googleMap', '//maps.googleapis.com/maps/api/js?key=aaafdfdsf', NULL, '1.0', true);
    ...
}
```
- Import đã OK, xem code trong file src/modules/GoogleMap.js xử lý load lat, lng động nhé

## 57. Campuses Continued
- Click vào từng địa điểm sẽ hiển thị thông tin chi tiết địa điểm đó
```PHP
// archive-campus.php
<div class="marker" data-lat="<?php echo $mapLocation['lat'] ?>" data-lng="<? php echo $mapLocation['lng'] ?>">
<h3> <?php the_title(); ?></h3>
<?php echo $mapLocation['address']; ?>
</div>
```
- OK cải thiện thêm chút nữa link đến địa điểm nào
```PHP
// archive-campus.php
<div class="marker" data-lat="<?php echo $mapLocation['lat'] ?>" data-lng="<? php echo $mapLocation['lng'] ?>">
<h3> <a href="<?php the_permalink(); ?>"> <?php the_title(); ?></h3>
<?php echo $mapLocation['address']; ?>
</div>
```
- Tạo quan hệ campus với program

## 58. Final Campus Details
- Chỉnh custom query load hết campus thôi
