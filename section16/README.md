# Section 16: Combining Front-End & Back-End
## 78. 3 Column Layout for Search Overlay
## 79. Custom Layout & JSON based on Post Type
Hai phần này chỉ hiển thị dữ liệu ra font-end

## 81. Search Logic That's Aware of Relationships
- Professor có Programs là Biology đang không ra, nhiệm vụ là cần search ra
- inc/search-route.php
```php
...
function universitySearchResults() {
    // Các post type khác tương tự
    ...
    
    $programsRelationshipQuery = new WP_Query(array(
        'post_type' => 'professor',
        'meta_query' => array(
            array(
                'key' => 'related_programs',
                'compare' => 'LIKE'
                'value' => '"97"'
            )
        )
    ));
    
    while($programsRelationshipQuery->have_post()) {
        $programsRelationshipQuery->the_post();
        
        // Push into professors result
        ...
    }
    
    $result['professors'] = array_values(array_unique($results['professors'], SORT_REGULAR));
    
    return $result;
}
```

## 82. Search Logic That's Aware of Relationships (Part 2)
- Tạo dynamic cho query reletionship thay vì fix cứng giá trị 97 như phần trước
```php
$programsRelationshipQuery = new WP_Query(array(
    'post_type' => 'professor',
    'meta_query' => array(
        array(
            'key' => 'related_programs',
            'compare' => 'LIKE'
            'value' => '"' . $results['programs'][0]['id'] . '"'
        )
    )
));
```

> Ngoài ra mặc định điều kiện trong **meta_query** là AND, nên nếu muốn truy vấn OR bạn cần thêm key **relation**

```php
$programsRelationshipQuery = new WP_Query(array(
    'post_type' => 'professor',
    'meta_query' => array(
        'relation' => 'OR',
        array(
            'key' => 'related_programs',
            'compare' => 'LIKE'
            'value' => '"' . $results['programs'][0]['id'] . '"'
        ),
        array(
            'key' => 'related_programs',
            'compare' => 'LIKE'
            'value' => '"' . $results['programs'][1]['id'] . '"'
        ),
        array(
            'key' => 'related_programs',
            'compare' => 'LIKE'
            'value' => '"' . $results['programs'][2]['id'] . '"'
        )
    )
));
```
- Còn một vấn đề nữa: mặc định WP tìm kiếm theo cả title và description chứa từ khoá, nhưng tôi chỉ muốn tìm kiếm theo title?

=> Giải pháp ở đây là tạo custom field cho description, không sử dụng description của WP. Và cần xoá description mặc định của WP. Quay trở lại hàm lúc đăng kí post type mới trong mu-plugins
```php
register_post_type('program', array(
    'supports' => array('title') // remove 'editor' key
));
```

## 83. Completing Our Search Overlay
- Custom tìm kiếm theo events thêm relationship với program
- Custom tìm kiếm theo campus thêm relationship với program

## 84. jQuery Free Live Search
> Our Search Feature Without JQuery?

- Sử dụng truy vấn JS thường thay vì jQuery
- Nếu được thì chuyển về JS thường, nhẹ hơn, các trình duyệt cũng đọc nhanh hơn
