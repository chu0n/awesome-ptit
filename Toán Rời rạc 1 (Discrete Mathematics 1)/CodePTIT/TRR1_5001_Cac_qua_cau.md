## Đề bài

> **TRR1_5001 - Các quả cầu**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_5001

**Mô tả:** Hộp có `h` quả cầu với `m` màu và `n` kích thước khác nhau. Tìm số quả cầu ít nhất cần lấy để **chắc chắn** có `t` quả giống nhau cả màu lẫn kích thước.

**Giới hạn:** `h, m, n, t ≤ 10⁴`, `t ≥ 2` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `10 3 2 2` | `7` |
| Test 2 | `10 2 3 3` | `0` |

> **Giải thích Test 1:** Có `m×n = 6` loại quả cầu khác nhau. Tệ nhất lấy `6×(t-1)=6` quả mà chưa có `t` quả giống nhau → lấy thêm 1 → cần `7`.
> **Giải thích Test 2:** Cần lấy `13` quả nhưng hộp chỉ có `10` → không thực hiện được → in `0`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Các quả cầu |
| **Mã bài** | TRR1_5001 |
| **Loại bài** | Toán rời rạc — Nguyên lý Dirichlet (Pigeonhole) |
| **Mục đích** | Tìm số lần lấy tối thiểu để chắc chắn có t phần tử trùng nhau |
| **Kiến thức** | Nguyên lý Dirichlet, tổ hợp đếm |
| **Thuật toán** | Có `m×n` loại quả cầu → tệ nhất lấy `(t-1)×m×n` quả chưa đủ → cần thêm 1 → kết quả là `(t-1)×m×n + 1`; kiểm tra có vượt `h` không |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    long long h, m, n, t;
    cin >> h >> m >> n >> t;

    // Tổng số loại quả cầu khác nhau (phân biệt bởi màu + kích thước)
    long long types = m * n;

    // Nguyên lý Dirichlet: tệ nhất lấy (t-1) quả mỗi loại mà vẫn chưa đủ t quả giống nhau
    // → cần lấy thêm 1 quả nữa để chắc chắn → kết quả = (t-1)*types + 1
    long long need = (t - 1) * types + 1;

    // Nếu số cần lấy vượt quá số quả trong hộp → không thực hiện được
    cout << (need > h ? 0 : need);
    return 0;
}
```

---

**Giải thích nguyên lý Dirichlet:**

Coi mỗi **loại quả cầu** (màu + kích thước) là một "ngăn" — có tổng `m×n` ngăn. Trong trường hợp xấu nhất, mỗi ngăn chứa đúng `t-1` quả trước khi ta buộc phải có ngăn nào đó chứa `t` quả:

$$\text{need} = (t-1) \times m \times n + 1$$

| Test | types | need | h | Kết quả |
|:---:|:---:|:---:|:---:|:---:|
| 1 | 3×2=6 | 6×1+1=7 | 10 | **7** ✓ |
| 2 | 2×3=6 | 6×2+1=13 | 10 | **0** ✓ |