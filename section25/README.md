# Section 25: Plugin: Multiple Choice Block Type (React)
## 141. Starting Our Multiple Choice Block Type
- Tiếp tục chỉnh sửa plugin trên để tạo giao diện mới
```js
// src/index.js
import {TextControl} from "@wordpress/components"


...
edit: EditComponent,
...

function EditComponent(props) {
	function updateSkyColor(event) {}
	function updateGrassColor(event) {}
	
	return (
		<div>
			<TextControl />
		</div>
	)
}
```
Cần chỉnh thêm Dependency Injection 'wp-editor' cho index.php
```diff
// index.php

function adminAssets() {
+	wp_register_script('ournewblocktype', plugin_dir_url(__FILE__) . 'test.js', array('wp-blocks', 'wp-element', 'wp-editor'));
}
```
- Render đã lên, chỉnh thêm component nào
```js
import "./index.scss"

// src/index.js
function EditComponent(props) {
	...
	return (
		<div className="paying-attention-edit-block">
			<TextControl label="Question: " />
		</div>
	)
}
```

Tạo thêm scss file: src/index.scss
```js
// src/index.scss
.paying-attention-edit-block {
	padding: 20px;
	borderL 1px solid #f1f1f1;
}
```

Nói với PHP load thêm index.css
```js
function adminAssets() {
	wp_register_style('quizeditcss', plugin_dir_url(__FILE__) . 'build/index.css');
	register_block_type('ourplugin/are-you-paying-attention', array(
		...
		'editor_style' => 'quizeditcss',
		...
	));
}
```
- Perfect, kết quả đã lên css đẹp nha!
- Tạo nốt component cho câu trả lời
```js
import {TextControl, Flex, FlexBlock, FlexItem, Icon} from "@wordpress/components"

// src/index.js
function EditComponent(props) {
	...
	return (
		<div className="paying-attention-edit-block">
			<TextControl label="Question: " />
			<p> Answers: </p>
			<Flex>
				<FlexBlock>
					<TextControl />
				</FlexBlock>
				<FlexItem>
					<Button>
						<Icon icon="start-empty" />
					</Button>
				</FlexItem>
				<FlexItem>
					<Button> Delete
					</Button>
				</FlexItem>
			</Flex>
		</div>
	)
}
```
## 142. Styling Our Block
- CSS cho các component thôi
```css
&:hover {
	transform: scale(1.25) rotate(12deg)
}
```

## 143. Event Handling & Updating Block Attributes
- Trước tiên thay đổi thuộc tính
```
attributes: {
	question: {type: "string"},
	answers: {type: "array", default: []}
},
```
- Render trong return (tự xử lý nhé)
- Xử lý luôn các sự kiện Thêm, Xóa Answers qua update props của React

## 145. Setting Up The Correct Answer
```
attributes: {
	question: {type: "string"},
	answers: {type: "array", default: []},
	correctAnswer: {type: "number", default: undefined}
},
```
- Xử lý thêm chọn đáp án đúng
- Test WP trên trình duyệt, console
```
wp.data.select("core/block-editor").getBlocks()
```
- Validation nếu không chọn đáp án đúng sẽ không cho nhấn Update nội dung Post: Bắt theo JS
```js
function ourStartFunction() {
	let locked = false;
	
	wp.data.subscribe(function() {
		// Method được gọi mỗi khi thay đổi nội dung post
		// console.log("Hello")
		
		const results = wp.data.select("core/block-editor").getBlocks().filter(function(block) {
			return block.name == "ourplugin/are-you-paying-attention" && block.attributes.correctAnswer == undefined
		})
		
		if (results.length && locked == false) {
			locked = true;
			wp.data.dispatch("core/editor").lockPostSaving("noanswer")
		}
		
		if (!results.length && locked) {
			locked = false;
			wp.data.dispatch("core/editor").unPostSaving("noanswer")
		}
	});
}
```
- Ngon vãi

## 147. How To Use React on The Front-End of WordPress
- Hiển thị thông tin question ra front-end
- Thêm file src/frontend.js
```js
import "./frontend.scss"

alert("Hello");
```
- Chỉnh sửa script build trong `package.json`
```json
"scripts": {
	"build": "wp-scripts build src/index.js src/frontend.js",
	"start": "wp-scripts start src/index.js src/frontend.js",
}
```
- Import css, js vào file index.php
```php
function theHTML($attributes) {
	if (!is_admin()) {
		// Tham số thứ 3 là Dependency, wp-element đã bao gồm React nhé
		wp_enqueue_script('attentionFrontend', plugin_dir_url(__FILE__ . 'build/frontend.js', array('wp-element');
		wp_enqueue_style('attentionFrontendStyles', plugin_dir_url(__FILE__ . 'build/frontend.css');
	}
}
```

> We can actually look at our again when we loaded our front end JavaScript, remember, we gave it and dependency of wp-element.

> Now I can tell you that WP dash element is really just WordPress version of react.

> They've essentially abstracted react into their own script.


> Nhờ có dependency này mà bạn không cần phải down file react.js trên cdn

- Tiếp theo sẽ render component react
```php
function theHTML($attributes) {
	if (!is_admin()) {
		wp_enqueue_script('attentionFrontend', plugin_dir_url(__FILE__ . 'build/frontend.js', array('wp-element');
		wp_enqueue_style('attentionFrontendStyles', plugin_dir_url(__FILE__ . 'build/frontend.css');
	}
	
	ob_start(); ?>
	<div class="paying-attention-update-me">
		
	</div>
	<?php return ob_get_clean(); ?>
}
```
fontend.js
```js
import React from 'react'
import ReactDOM from 'react-dom'
import "./frontend.scss"

const divsToUpdate = document.querySelectorAll(".paying-attention-update-me")

divsToUpdate.forEach(function(div) {
	// Apply React Component
	ReactDOM.render(<Quiz />, div)
	div.classList.remove("paying-attention-update-me")
})

function Quiz() {
	return (
		<div className="paying-attention-frontend">
			Hello from React
		</div>
	)
}
```
- Awesome! Component đã được render lên FE


## 148. Passing Block Data From PHP Into JavaScript / React
- Tiếp tục xử lý block hiển thị đúng dữ liệu. Xử lý truyền dữ liệu từ PHP sang JS
```php
// index.php
function theHTML($attributes) {
	...
	// Debug: Đoạn này kiểu echo hiển thị xem dữ liệu là gì?
	<div class="paying-attention-update-me"> <pre><?php echo wp_json_encode($attributes) ?></pre></div>
	</div>
}
```
Tiếp tục
```js
// src/frontend.js
divsToUpdate.forEach(function(div) {
	const data = JSON.parse(div.querySelector("pre").innerHTML)
	ReactDOM.render(<Quiz {...data} />, div)
	div.classList.remove("paying-attention-update-me")
})

function Quiz() {
	return (
		<div className="paying-attention-frontend">
			<p>{props.question}</p>
			// Render answer, bạn tự làm nhé :D
		</div>
	)
}
```

## 149. Letting Users Click On (Guess) An Answer
- Xử lý click câu trả lời
```js
function handleAnswer(index) {
	if (index == props.correctAnswer) {
		alert("Congrats")
	} else {
		alert("Sorry")
	}
}
```
> **Bootstrap Icon**
- Sử dụng state để render đáp án nhé
```js
function Quiz() {
	const [isCorrect, setIsCorrect] = useState(undefined);
	
	function handleAnswer(index) {
		if (index == props.correctAnswer) {
			setIsCorrect(true)
		} else {
			setIsCorrect(false)
		}
	}
}
```

## 150. Attention To Detail
- Fix bug click câu trả lời sai 1 lần, các lần khác không render lại

> If you asked 100 developers, you'd get 100 different answers on how to set this up.

- Sử dụng useEffect
```
useEffect(() => {
	if (isCorrect === false) {
		setTimeout(() => {
			setIsCorrect(undefined)
		}, 2600)
	}
}, [isCorrect])
```
- Xử lý disbale các câu trả lời khác (tụ xử lý nhé)

## 152. Let Admin Choose Background Color of Block
- Chọn background color cho phần câu hỏi từ Admin Dashboard
```js
// index.js
...
import { PanelBody, PanelRow } from "@wordpress/components"
import {InspectorControls} from "@wordpress/block-editor"

...
return (
	<div className="paying-attention-edit-block">
		<InspectorControls>
			<PanelBody title="Background Color" initialOpen={true}>
				<PanelRow>
					Hello!
				</PanelRow>
			</PanelBody>
		</InspectorControls>
	</div>
)
```
- Đến lúc này bạn click vào block này, sẽ hiện khối chọn Background Color bên phải
- Thay thế Hello
```js
// index.js
...
import { ColorPicker} from "@wordpress/components"

// Thêm attributes nữa
attributes: {
	...
	bgColor: {type: "string", default: "#EBEBEB"}
}

...
return (
	<div className="paying-attention-edit-block" style={{backgroundColor: props.attributes.bgColor}}>
		<InspectorControls>
			<PanelBody title="Background Color" initialOpen={true}>
				<PanelRow>
					<ColorPicker color={props.attributes.bgColor} onChangeComplete={x => props.setAttributes({bgColor: x.hex})} />
				</PanelRow>
			</PanelBody>
		</InspectorControls>
	</div>
)
```

> Ngoài ra bạn có thể sử dụng thư viện React Color, không hỗ trợ mặc định trong WP, có nhiều style màu sắc hơn cho bạn chọn

```
npm install react-color
```

## 153. Block Text Alignment & Block Preview
- Tiếp tục chỉnh Block
```js
// index.js
...
import { BlockControls, AlignmentToolbar } from "@wordpress/block-editor"

// Thêm attributes nữa
attributes: {
	...
	theAligment: {type: "string", default: "left"}
}

...
return (
	<div className="paying-attention-edit-block" style={{backgroundColor: props.attributes.bgColor}}>
		<BlockControls>
			<AlignmentToolbar value={props.attributes.theAligment} onChange={x => props.setAttributes({theAligment: x})} />
		</BlockControls>
		...
	</div>
)
```
- OK, đã có align cho bạn chọn
- Làm nốt phần Preview khi chọn Block nào :D
```js
// index.js

wp.blocks.registerBlockType("ourplugin/are-you-paying-attention", {
	...
	example: {
		attributes: {
			question: "What is my name?",
			correctAnswer: 3,
			answers: ['Huhu', 'Haha', 'Hoho', 'Hiuhiu'],
			theAligment: "center"
			bgColor: "#CFE8F1"
		}
	},
}
```
- Ok, ngon rồi đó
- Thêm mô tả để hiển thị lúc chọn Block
```js
// index.js

wp.blocks.registerBlockType("ourplugin/are-you-paying-attention", {
	...
	description: "Give your question",
}
```

## 154: Using The "block.json" File
