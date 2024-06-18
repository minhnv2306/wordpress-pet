# Section 23: Plugin Development: PHP
## 123. Introduction to Plugin Development
3 Chapter
1. PHP (action, filters, admin settings, pages & menus)
2. JavaScript (Gutenberg Block Types, React)
3. Database (Custom Post Type vs Our Own DB Table)

Kết quả:
- Setting số lượng từ và thời gian đọc bài: Settings => Word Count
- Setting từ được config thành các kí tự đặc biệt hoặc khoảng trắng
- Thêm câu hỏi cho bài post
- Thêm thông tin Professor trong bài viết
- Pet và tìm kiếm Pet => Custom DB

WordPress is just too big of a topic with too many different features and APIs and sort of the whole point of plug ins is that the possibilities are infinite, endless.

Anything that you can think of that you want to extend or customize and WordPress, you can do that with a plugin.

So every single option, every parameter, every action or every filter that we can hook on to any setting that we can change anything is possible with plugins.

So it's impossible to cover it all in a course like this.

Trust me, the course would become 400 hours long and everyone would lose interest.

The idea here is just to show you the core basic building blocks and the most common features that you're going to need to work with in order to build plug ins from there.

## 124. Let's Create Our First Plugin
- Tạo thư mục: Vào wp_content/plugins tạo folder mới => our-first-unique-plugin
> wp_content/plugins/our-first-unique-plugin
- Tạo file: our-first-unique-plugin.php
```php
// our-first-unique-plugin.php
<?php
/*
	Plugin Name: Our Test Plugin
	Description: A truly amazing plugin
	Version: 1.0
	Author: Brad
	Author URI: https://www.abc.com
*/
```
Như vậy plugin đã hiển thị bên Admin Dashboard
- Action đầu tiên
```php
// our-first-unique-plugin.php
<?php
/*
	Plugin Name: Our Test Plugin
	Description: A truly amazing plugin
	Version: 1.0
	Author: Brad
	Author URI: https://www.abc.com
*/
add_filter('the_content', 'addToEndOfPost');

function addToEndOfPost($content) {
	if (is_single()) && is_main_query()) {
		return $content . '<p> My name is Ming </p>;
	}
	
	return $content;
}
```

## 125. Adding a Settings Page For Our Plugin
- Thêm Setting cho admin
```php
// our-first-unique-plugin.php
<?php
/*
	Plugin Name: Our Test Plugin
	Description: A truly amazing plugin
	Version: 1.0
	Author: Brad
	Author URI: https://www.abc.com
*/

class WordCountAndTilePlugin {
	function __construct() {
		add_action('admin_menu', array($this, 'adminPage'));
	}
	
	function adminPage() {
		add_options_page('Word Count Settings', 'Word Count', 'manage_options', 'word-cout-settings-page', array($this, 'ourHTML'));
	}

	function ourHTML() { ?>
		<div class="wrap">
			<p> Hello, I am Minh </p>
		</div>
	<?php
	}
}

$wordCountAndTilePlugin = new WordCountAndTilePlugin();
```

## 126. Settings API (Saving Settings Data)
- Xem database bảng wp_options. Chúng ta sẽ tạo dữ liệu vào bảng này
```
class WordCountAndTilePlugin {
	function __construct() {
        add_action('admin_init', 'array($this, 'settings'));
	}
    
    function settings() {
        add_setting_section('wcp_first_section', null, null, 'word-count-settings-page');
        add_settings_field('wcp_location', 'Display Location', array($this, 'locationHTML'), 'word-count-settings-page', 'wcp_first_section');
        register_setting('wordcountplugin', 'wcp_location', array('sanitize_callback' => 'sanitize_text_field', 'default' => '0'));
    }
    
    function locationHTML() { ?>
        <select name="wcp_location">
            ...
        <select>
    <?php }
    
    function ourHTML() { ?>
		<div class="wrap">
			<p> Word Count Setting </p>
            <form action="options.php" method="POST">
            <?php
                // Check security (nonce) for us
                settings_fields('wordcountplugin');
                
                do_setting_section('word-count-settings-page');
                submit_button();
            ?>
            </form>
		</div>
	<?php
	}
```
- Việc còn lại là của WP, tự render HTML cho chúng ta => Cool. Lưu và kiểm tra giá trị trong DB nhé

## 127. Finishing Our Settings Form
- Setting nốt các trường trong form nào!
```php
function settings() {
	add_settings_field('wcp_headline', 'Display Headline', array($this, 'headlineHTML'), 'word-count-settings-page', 'wcp_first_section');
    register_setting('wordcountplugin', 'wcp_headline', array('sanitize_callback' => 'sanitize_text_field', 'default' => 'Post Statitics'));
}

function headlineHTML() { ?>
	<input type="text" name="wcp_headline" value="<?php echo esc_attr(get_option('wcp_headline')); ?>">
<?php }

function locationHTML() {?>
	<seclect name="wcp_location">
		<option value="0" <?php selected(get_option('wcp_location'), '0') ?>> Beginning of post </option>
		<option value="1" <?php selected(get_option('wcp_location'), '1') ?>> End of post </option>
	</seclect>
<?php }
```

> Trường autoload trong bảng wp_option để cache hay không??

> Các trường checkbox xem nốt trong code nhé =))

- Validation: 
```php
register_setting('wordcountplugin', 'wcp_location', array('sanitize_callback' => array($this, 'sanitizeLocation'), 'default' => '0'));

function sanitizeLocation($input) {
	if ($input != '0' AND $input != '1') {
		add_setting_error('wcp_location', 'wcp_location_error', 'Display Localtion must be either 0 or 1');
		
		return get_option('wcp_location');
	}
	
	return $input;
}
```

## 128. Actually Counting the Words, Characters, and Read Time
- Sử dụng action hook
```php
function __construct() {
	...
	add_action('the_content', 'array($this, 'ifWrap'));
}

function ifWrap($content) {
	if (is_main_query() AND is_single() AND (get_option(''wcp_wordcount', '1') OR get_option('wcp_charactercount', '1') OR get_option('wcp_readtime', '1')) {
		return $this->createHTML($content);
	}
	
	return $content;
}

function createHTML($content) {
	// Đoạn này bạn tự xử lý hoặc xem code nhé :D
	// str_word_count(strip_tags($content)), str_len(strip_tags($content))
	
	return $content . ' hello';
}
```

## 129. Translations / Localization (For PHP)
```php
// our-first-unique-plugin.php
<?php
/*
	Plugin Name: Our Test Plugin
	Description: A truly amazing plugin
	Version: 1.0
	Author: Brad
	Author URI: https://www.abc.com
	Text Domain: wcpdomain
	Domain Path: /languages
*/
	...
	
	function adminPage() {
		add_options_page('Word Count Settings', __('Word Count', 'wcpdomain'), 'manage_options', 'word-cout-settings-page', array($this, 'ourHTML'));
	}
```
- Tiếp tục chỉnh code
```php
	function __construct() {
		add_action('init', array($this, 'languages'));
	}
	
	function languages() {
		load_plugin_textdomain('wcpdomain', false, dirname(plugin_basename(__FILE__)) . '/languages');
	}
```
- Tiếp theo tạo thư mục **languages**

> Plugin: Loco Translate By Tim Whitlock

- Plugin này sẽ tạo file trong thư mục **/languages** cho bạn
- Chuyển ngôn ngữ cho web. Setting => Site Language. Load lại web và đến đây là OK nha

> Chút recurity esc_html() để nội dung không render HTML

## 130. Admin Sub-Menu
- Làm tiếp chức năng Word Filter, lọc các từ được config bằng các kí tự đặc biệt
- Đầu tiên sẽ làm sub-menu
- Tạo plugin mới. Thư mục: **our-word-filter-plugin**. File **index.php**
```
...
class OurWordFilterPlugin {
    function __construct() {
        add_action('admin_menu', array($this, 'ourMenu'));
    }
    
    function ourMenu() {
        add_menu_page('Words To Filter', 'Word Filter', 'manage_options', 'ourwordfilter', array($this, 'wordFilterPage'), 'dashicons-smiley', 100);
        
        // Add submenu
        add_submenu_page('ourwordfilter', 'Word Filter Options', 'Options', 'manage_options', 'word-filter-options', array($this, 'optionsSubPage'));
    }
    
    function wordFilterPage() { ?>
       Hello world
   <?php  }
   
   function optionsSubPage() { ?>
       Hello world Submenu
   <?php }
}

$ourWordFilterPlugin = new OurWordFilterPlugin();
```
> Bạn có thể thấy 2 submenu mặc dù chỉ tạo một? Nhưng submenu đầu tiên là mặc định của WP sẽ tạo ra nếu có submenu, ví dụ như Posts bạn ấn vào sẽ actions submenu All Posts vì nó là mặc định. Nếu bạn muốn custom text trong submenu mặc định

```
// Thêm dòng sau để ghi đè submenu mặc định
add_submenu_page('ourwordfilter', 'Word To Filter', 'Words List', 'manage_options', 'ourwordfilter', array($this, 'wordFilterPage'));
```

## 131. Custom Admin Menu Icon
- Custom SVG Icon & Admin Custom CSS
- Dùng hàm btoa(\`svg_html\`) để lấy đoạn base64 của svg điền vào đoạn dưới =))
```
add_menu_page('Words To Filter', 'Word Filter', 'manage_options', 'ourwordfilter', array($this, 'wordFilterPage'), 'data::image/svg+xml;base64,aaa', 100);
```
- Bạn cũng có thể custom theo path của file
```
add_menu_page('Words To Filter', 'Word Filter', 'manage_options', 'ourwordfilter', array($this, 'wordFilterPage'), plugin_dir_url(__FILE__) . 'custom.svg', 100);
```
- Tạo form cho trang word filter, xem đoạn HTML trong bài giảng
- Custom css cho textarea
```
function ourMenu() {
        $mainPageHook = add_menu_page('Words To Filter', 'Word Filter', 'manage_options', 'ourwordfilter', array($this, 'wordFilterPage'), 'dashicons-smiley', 100);
        add_action("load-{$mainPageHook}", array($this, 'mainPageAssets'));
        ...
}

function mainPageAssets() {
    wp_enqueue_style('filterAdminCss', plugin_dir_url(__FILE__) . 'style.css');
}
```

## 133. Alternative: Manually Handling Admin Form Submit
- Bạn xử lý submit form luôn tại bước này
```
function handleForm() {
    update_option('plugin_words_to_filter', $_POST['plugin_words_to_filter']);
    ...
}

...
<?php if ($_POST['justsubmitted'] == "true" $this->handleForm() ?>

<form method="POST">
    <input type="hidden" name="justsubmitted" value="true">
</form>
```

> esc_textarea(), sanitize_text_field()
- Và để chống XSS

```
<form method="POST">
    <?php wp_nonce_field('saveFilterWords', 'ourNonce') ?>
</form>

...
// Check nonce
function handleForm() {
    if (wp_verify_nonce($_POST['ourNonce'], 'saveFilterWords') AND current_user_can('manager_options')) {
        // Update DB
    } else {
        // Render error
    }
}
```

## 134. Finishing Word Filter Plugin
- add_filter content post, thay thế các từ trong options plugin_words_to_filer bằng các kí tự config trong replacement-text option thôi :D
