# Section 13: Live Search (UI JavaScript)
## 59. Live Search
- Saerch truyền thống nhập từ khoá ấn search mới ra kết quả. Tác giả ko muốn kiểu đó mà muốn realtime để tăng trải nghiệm người dùng
- Tạo file src/modules/Search.js
```JavaScript
class Search {
    constructor() {
        alert("Hello I am search.");
    }
}

export default Search;
```
## 60. Quick Note About The Next Lesson
## 61. Open and Close Search Overlay
## 63. Keyboard Events in JavaScript
## 64. Managing Time in JavaScript
- Không search luôn khi keydown, chờ thời gian 2s rồi mới tìm
- setTimeout() kết hợp clearTimeout để đoạn logic chỉ chạy 1 lần

## 65. Waiting / Loading Spinner Icon
- Tạo spinner icon khi thay nhập text
- Kiểm tra không phải lúc nào cũng thay đổi html của spinner
- Nếu giá trị text trong input search không đổi, chỉ di chuyển chuột không hiển thị spinner
- Khi trống input search, thay html kết quả bằng empty ('')
- Check input, text is("focus")

> Phần này bạn có thể học cách tổ chức code JS

```JavaScript
$("body").addClass("body-no-scroll");
```

> keyup vs keydown? Keydown chạy lặp lại nhiều lần
