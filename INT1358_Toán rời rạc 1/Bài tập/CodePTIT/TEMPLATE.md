# Hướng dẫn soạn tài liệu CodePTIT

Tài liệu dành cho người đã biết C++ cơ bản. Viết như giáo viên chữa bài, không viết như tài liệu API.

## 1. Giữ nguyên đề

- Không sửa mô tả, yêu cầu, giới hạn, input/output, bảng ví dụ hoặc lời giải thích mẫu.
- Không tự thêm dữ liệu, không sửa đáp án mẫu cho khớp code.
- Nếu mẫu công bố mâu thuẫn với ma trận, giữ nguyên mẫu và ghi chú kết quả tính được ngay dưới bảng.

## 2. Tên file và link

- Tên: `<MÃ_BÀI>_<Tên đầy đủ có dấu>.md`, UTF-8. Chỉ dùng `_` sau mã bài, phần tên dùng khoảng trắng.
- Link: `https://code.ptit.edu.vn/submit/<MÃ_BÀI>`.
- Đổi tên file thì cập nhật link tham chiếu tới file đó.

## 3. Bố cục

### Bài gọn

````markdown
# MÃ — Tên bài

> **Loại:** ... · **Thuật toán:** ... · **Thời gian:** ... · **Bộ nhớ:** ...

## Đề bài
(giữ nguyên)

## Ý tưởng
- Mô hình
- Công thức / cận
- Vì sao đúng

<details>
<summary><strong>Hướng 1 — ...</strong></summary>

```cpp
(lời giải chính)
```

### Giải thích nhanh
- Bảng 3–5 biến/trạng thái
- 3–5 bước chạy
- 3–5 bẫy thật
- Một dòng độ phức tạp

</details>

<details>
<summary><strong>Hướng 2 — ...</strong></summary>

```cpp
(lời giải thay thế)
```

1–3 câu đánh đổi.
</details>
````

### Bài chi tiết (7 bài cuối)

Dùng cùng khung trên, nhưng `### Giải thích code` có thêm:

- bảng biến/trạng thái
- luồng chạy từng bước
- giải thích 2–4 đoạn khó, chỉ trích 5–15 dòng mỗi đoạn
- vì sao công thức/cận đúng
- một bảng chạy tay
- 3–5 bẫy và 3–5 câu hỏi dễ gặp

Không chia thành chín mục máy móc. Không lặp lại toàn bộ code trong phần giải thích.

## 4. Bảng ví dụ

- Một bảng cho cả bài; mỗi ví dụ một hàng; ba cột `Input`, `Output`, `Giải thích`.
- Input/Output nằm trong `<pre><code>`, giữ xuống dòng thật; không dùng `<br>`.
- Giữ nguyên khối `<table>`, `<pre>` và style của các bài hiện có. Không đổi sang bảng Markdown vì không đặt được `<pre>` trong ô.
- Cột Giải thích giữ nội dung nguồn; danh sách dùng `<ul><li>...</li></ul>`.
- Đọc từng cột độc lập; không thêm dòng đệm cho cột ngắn hơn.

## 5. Code C++

- Mỗi bài có hai hướng giải; cả hai đặt trong `<details>` đóng mặc định.
- Hướng 1 chứa code, toàn bộ giải thích và chạy tay. Hướng 2 chỉ cần code và 1–3 câu đánh đổi.
- Không đặt giải thích hay chạy tay bên ngoài dropdown.
- Chỉ viết code hoàn chỉnh trong một khối `cpp` cho mỗi hướng.
- Dùng C++17, 4 spaces, brace Linux: hàm xuống dòng, `if/for/while` cùng dòng.
- Theo `~/.clang-format`; chạy `clang-format` trước khi kết thúc.
- OJ bảo đảm input hợp lệ: không thêm guard kiểm tra `n < 0`, `n > 50`, `k > n`, ...
- Không giải thích `#include`, `using namespace std`, `cin`, `cout` nếu không có gì đặc biệt.

## 6. Kiểm tra trước khi xong

- Đề bài và bảng ví dụ không đổi so với bản gốc.
- Đúng 31 bài; mỗi bài đúng 2 hướng giải.
- Code compile với `g++ -std=c++17`; chạy đúng mẫu.
- Code khớp `clang-format`; không còn guard input thừa.
- Ví dụ mâu thuẫn được giữ nguyên và có ghi chú.
- Không thêm test, script, README phụ hoặc file mới nếu không được yêu cầu.
