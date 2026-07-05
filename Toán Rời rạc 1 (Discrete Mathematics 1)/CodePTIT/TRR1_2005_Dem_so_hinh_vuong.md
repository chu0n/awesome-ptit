## Đề bài
> **TRR1_2005 - Đếm số hình vuông**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_2005

**Mô tả:** Cho trước `n` ô vuông đơn vị với cạnh là 1.

**Yêu cầu:** Tìm số lượng `t` lớn nhất các hình vuông có kích thước cạnh là các số nguyên khác nhau có thể tạo được từ `n` ô vuông đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa số nguyên dương `n` không vượt quá `2^18` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

| | Input | Output |
|---|---|---|
| Test 1 | `10` | `2` |

> **Giải thích Test 1:** Giá trị lớn nhất `t = 2` vì có thể tạo được hai hình vuông cạnh 1 và cạnh 2.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Đếm số hình vuông |
| **Mã bài** | TRR1_2005 |
| **Loại bài** | Toán rời rạc — Tổ hợp đếm |
| **Mục đích** | Tìm số lượng hình vuông tối đa có thể tạo từ n ô vuông đơn vị |
| **Kiến thức** | Tổng bình phương, tìm kiếm tham lam |
| **Thuật toán** | Tham lam — lần lượt dùng hình vuông cạnh 1, 2, 3,... (tốn `i²` ô), cộng dồn đến khi vượt quá n |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    long long n;
    cin >> n;

    // Tham lam: lấy hình vuông cạnh 1, 2, 3,... theo thứ tự tăng dần
    // Mỗi hình vuông cạnh i tốn i*i ô vuông đơn vị
    // Dừng lại khi không đủ ô để tạo hình vuông tiếp theo
    long long t = 0, used = 0;
    for (long long i = 1; used + i*i <= n; i++) {
        used += i*i;    // Dùng i² ô cho hình vuông cạnh i
        t++;            // Tăng số lượng hình vuông
    }

    cout << t;
    return 0;
}
```

---

**Giải thích logic:**

Để tối đa hóa số lượng hình vuông, ta dùng chiến lược **tham lam**: ưu tiên hình vuông nhỏ nhất trước (tốn ít ô nhất), các cạnh phải **khác nhau** nên lần lượt chọn cạnh `1, 2, 3, ...`

$$\text{Dừng khi: } \sum_{i=1}^{t} i^2 \leq n < \sum_{i=1}^{t+1} i^2$$

| Bước | Cạnh i | Ô cần (i²) | Tổng ô dùng | Còn lại (n=10) |
|:---:|:---:|:---:|:---:|:---:|
| 1 | 1 | 1 | 1 | 9 |
| 2 | 2 | 4 | 5 | 5 |
| 3 | 3 | 9 | 14 | — vượt quá → dừng |

→ **t = 2** ✓
