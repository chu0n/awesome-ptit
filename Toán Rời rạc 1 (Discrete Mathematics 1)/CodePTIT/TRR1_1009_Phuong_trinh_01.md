## Đề bài
> **TRR1_1009 - Phương trình 01**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_1009

**Mô tả:** Cho trước hai số thực `a` và `b`. Xét mệnh đề `p` = "Phương trình `ax + b = 0` có ít nhất một nghiệm thực âm".

**Yêu cầu:** Xác định giá trị của `p` với hai số thực `a` và `b` đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa hai số thực `a` và `b` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn là giá trị của `p`.

| | Input | Output |
|---|---|---|
| Test 1 | `5.0 7.5` | `1` |

> **Giải thích Test 1:** Phương trình `5.0*x + 7.5 = 0` có 1 nghiệm thực âm.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Phương trình 01 |
| **Mã bài** | TRR1_1009 |
| **Loại bài** | Toán rời rạc — Mệnh đề logic |
| **Mục đích** | Xác định giá trị chân lý (True/False) của mệnh đề p |
| **Kiến thức** | Mệnh đề logic, phương trình bậc nhất một ẩn |
| **Thuật toán** | Phân tích các trường hợp nghiệm của `ax + b = 0`, kiểm tra nghiệm có âm không |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    double a, b;
    cin >> a >> b;

    // Phương trình ax + b = 0
    // TH1: a != 0 → nghiệm duy nhất x = -b/a, kiểm tra x < 0
    // TH2: a == 0, b == 0 → vô số nghiệm (gồm cả nghiệm âm) → p = true
    // TH3: a == 0, b != 0 → vô nghiệm → p = false

    bool p;
    if (a != 0)       p = (-b / a) < 0;   // Nghiệm duy nhất, kiểm tra âm
    else if (b == 0)  p = true;            // Mọi x đều là nghiệm, có nghiệm âm
    else              p = false;           // Vô nghiệm

    cout << p;
    return 0;
}
```

---

**Giải thích các trường hợp:**

| Điều kiện | Nghiệm | p |
|---|---|---|
| `a ≠ 0` | `x = -b/a` duy nhất → kiểm tra `< 0` | `1` hoặc `0` |
| `a = 0, b = 0` | Vô số nghiệm (mọi x ∈ ℝ) → có nghiệm âm | `1` |
| `a = 0, b ≠ 0` | Vô nghiệm | `0` |
