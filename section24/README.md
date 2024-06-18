# Section 24: Plugin Development: Blocks, Gutenberg & React

## 135. Introduction to JavaScript Plugin Development
- Thêm các blog vào nội dung post
- Tạo folder mới trong plugins: are-you-paying-attention
- Tạo file test.js
```js
alert("Hello, from out new JS file");
```
- Tạo file index.php
```php
/*
	Thông tin Plugin
*/

class AreYouPayingAttention {
	function __construct() {
		add_action('enqueue_block_editor_assets', array($this, 'adminAssets');
	}
	
	function adminAssets() {
		wp_enqueue_script('ournewblocktype', plugin_dir_url(__FILE__) . 'test.js', array('wp-blocks', 'wp-element'));
	}
}

$areYouPayingAttention = new AreYouPayingAttention();
```
- Perfect, như vậy đoạn JS mới đã được thêm mỗi khi bạn chỉnh sửa post với editor
- Chỉnh sửa `test.js` file
```php
wp.blocks.registerBlockType("ourplugin/are-you-paying-attention", {
	title: "Are You Paying Attention?",
	icon: "smiley",
	category: "common",
	edit: function () {
		// Hiển thị nội dung bên Admin page
		return wp.element.createElement("h3", null, "Hello, this is from the admin editor screen");
	},
	save: function () {
		// Hiển thị nội dung bên Front-end
		return wp.element.createElement("h1", null, "This is the frontend.");
	}
});
```
- Bạn sẽ thấy block mới trong editor của post =))
- Theo dõi DB, bạn sẽ thấy một khối block mới
```
<!-- wp::ourplugin/are-you-paying-attention -->
<h1 class="wp-block-ourplugin-are-you-paying-attention"> This is the frontend </h1>
<!-- /wp::ourplugin/are-you-paying-attention -->
```

> Mặc dù phương thức createElement là cách WP tạo HTML từ bên trong JavaScript nhưng trên thực tế không ai thực sự trực tiếp sử dụng nó như thế. Hãy cùng xem JSX ngay sau đây nhé :D 

## 136. Introduction to JSX
- Vấn đề là ví dụ bạn tạo nhiều thẻ html
```js
edit: function () {
	// Hiển thị nội dung bên Admin page
	return wp.element.createElement("h3", null, [
		wp.element.createElement("h1", null, "This is H1");
		wp.element.createElement("h2", null, "This is H2");
		...
	]);
},
```
- Thay vì vậy
```js
edit: function () {
	return (
		<div>
			<h1> This is a h1 </h1>
			<h2> This is H2>
		</div>
	);
},
```
- Oh, cách viết này thì trình duyệt hok hiểu đâu, cần thêm các file biên dịch (build) để trình duyệt thực sự có thể load
- Cài đặt
	+ node
	+ @wordpress/scripts

```
npm init -y
npm install @wordpress/scripts
```
- Tạo thư mục /src. Tạo file /src/index.js
```js
// index.js
alert("Hello, this is index in JSX");
```
- Chỉnh lại js
```js
edit: function () {
	return <h3> This is a h3 from JSX. </h3>;
},
```
- Tiếp theo là nói cho WP biết cần JSX convert chỗ này
```js
// package.json
"scripts": {
	"build": "wp-scripts build",
	"start": "wp-scripts start",
}
```
- Run command
```js
npm run start
```
- Sửa file index.php
```php
function adminAssets() {
	wp_enqueue_script('ournewblocktype', plugin_dir_url(__FILE__) . 'build/index.js', array('wp-blocks', 'wp-element'));
}
```
- Awesome! Lên rồi nhé :D 

## 137. Block Type Attributes
- Mỗi khối HTML ở trên đang là cố định là bạn không thể nhấn vào để edit trực tiếp, nhưng chúng ta cần điều đó
```js
// src/index.js
attributes: {
	skyColor: {type: "string"},
	grassColor: {type: "string"}
},
edit: function (props) {
	function updateSkyColor(event) {
		props.setAttribute({skyColor: event.target.value})
	}
	
	function updateGrassColor(event) {
		props.setAttribute({grassColor: event.target.value})
	}
	
	return (
		<div>
			<input type="text" placeholder="sky color" value={props.attributes.skyColor} onChange={updateSkyColor} />
			<input type="text" placeholder="grass color" value={props.attributes.grassColor} onChange={updateGrassColor} />
		</div>
	)
},

save: function (props) {
	return (
		<p> Today the sky is {props.attributes.skyColor} and the grass is {props.attributes.grassColor}</p>
	)
}
```
- Xem dữ liệu trong DB thấy attribute
```
<!-- wp::ourplugin/are-you-paying-attention
{"skyColor": "gray", "grassColor": "green"} -->
...
<!-- /wp::ourplugin/are-you-paying-attention -->
```

## 138. Let's Discuss the Output of Our Block (Part 1)
- Mỗi lần thay đổi HTML hàm save là lỗi block, cần so sánh sự thay đổi
```
save: function (props) {
	return (
		<p> Today the sky is {props.attributes.skyColor} and the grass is {props.attributes.grassColor}</p>
	)
}

// Đang hiểu deprecate các attribute và function cũ
deprecated: [{
    attributes: {
        skyColor: {type: "string"},
        grassColor: {type: "string"}
    },
    save: function (props) {
        return (
            <p> Today the sky is {props.attributes.skyColor} and the grass is {props.attributes.grassColor}</p>
        )
    }
}]
```
- Tuy nhiên trong phần trên, cách `save` vẫn đang lưu html tĩnh trong database.  Hãy thử WP có thể hỗ trợ dynamic block

## 138. Let's Discuss the Output of Our Block (Part 2)
- Thay vì import js file, plugin sẽ import block
```
class AreYouPayingAttention {
	function __construct() {
		add_action('init', array($this, 'adminAssets');
	}
    
    function adminAssets() {
        wp_register_script('ournewblocktype', plugin_dir_url(__FILE__) . 'test.js', array('wp-blocks', 'wp-element'));
        register_block_type('ourplugin/are-you-paying-attention', array(
            'editor_script' => 'ournewblocktype',
            'render_callback' => array($this, 'theHTML')
        ));
    }
    
    function theHTML($attributes) {
        return '<p> Today the sky is ' . $attributes['skyColor'] . ' and the grass is ' . $attributes['grassColor'] . '</p>;
    }
     
```
- Lúc này có sửa HTML cũng không cần build lại block bên admin, F5 lại front end là kết quả được cập nhật => Awesome!

- Check trong DB và nội dung không hề chứa html tĩnh
```
<!-- wp::ourplugin/are-you-paying-attention
{"skyColor": "gray", "grassColor": "green"} -->
```
- Ngoài ra có thể refactor sử dụng `ob_start` và `ob_get_clear()`
