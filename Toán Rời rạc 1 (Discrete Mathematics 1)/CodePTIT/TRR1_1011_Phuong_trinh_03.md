## Đề bài
> **TRR1_1011 - Phương trình 03**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_1011

**Mô tả:** Cho trước ba số thực `a, b` và `c`. Xét mệnh đề `p` = "Phương trình `ax^4 + bx^2 + c = 0` có ít nhất một nghiệm thực".

**Yêu cầu:** Xác định giá trị của `p` với ba số thực `a, b` và `c` đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa ba số thực `a, b` và `c` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn là giá trị của `p`.

| | Input | Output |
|---|---|---|
| Test 1 | `5.0 -7.5 1.3` | `1` |

> **Giải thích Test 1:** Phương trình bậc bốn trùng phương `5.0*x^4 - 7.5*x^2 + 1.3 = 0` có 4 nghiệm thực phân biệt.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Phương trình 03 |
| **Mã bài** | TRR1_1011 |
| **Loại bài** | Toán rời rạc — Mệnh đề logic |
| **Mục đích** | Xác định giá trị chân lý của mệnh đề p |
| **Kiến thức** | Mệnh đề logic, phương trình trùng phương, đặt ẩn phụ |
| **Thuật toán** | Đặt `t = x²` (t ≥ 0) → giải PT bậc 2 theo t → kiểm tra có nghiệm t ≥ 0 không → suy ra PT gốc có nghiệm thực |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    double a, b, c;
    cin >> a >> b >> c;

    // Phương trình trùng phương: ax^4 + bx^2 + c = 0
    // Đặt t = x^2 (t >= 0) → bài toán quy về: at^2 + bt + c = 0 có nghiệm t >= 0 không?
    // Nếu có t >= 0 thỏa mãn thì x = ±sqrt(t) là nghiệm thực của PT gốc.

    bool p = false;

    if (a != 0) {
        // --- PT bậc 2 theo t: at^2 + bt + c = 0 ---
        double delta = b*b - 4*a*c;

        if (delta < 0) p = false;                           // Vô nghiệm thực (theo t)
        else if (delta == 0) p = (-b / (2*a)) >= 0;        // Nghiệm kép t0, cần t0 >= 0
        else {
            double t1 = (-b + sqrt(delta)) / (2*a);
            double t2 = (-b - sqrt(delta)) / (2*a);
            p = (t1 >= 0) || (t2 >= 0);                    // Ít nhất 1 nghiệm t >= 0
        }

    } else if (b != 0) {
        // --- PT bậc 1 theo t: bt + c = 0 → t = -c/b ---
        p = (-c / b) >= 0;                                  // Cần t >= 0

    } else if (c == 0) {
        // --- 0 = 0: mọi t đều thỏa → x = 0 là nghiệm ---
        p = true;

    } else {
        // --- Vô nghiệm (a=b=0, c≠0) ---
        p = false;
    }

    cout << p;
    return 0;
}
```

---

**Giải thích các trường hợp:**

| Điều kiện | Dạng PT (theo t) | Xử lý |
|---|---|---|
| `a ≠ 0, Δ < 0` | Bậc 2, vô nghiệm thực | `p = 0` |
| `a ≠ 0, Δ = 0` | Bậc 2, nghiệm kép `t₀ = -b/2a` | Kiểm tra `t₀ ≥ 0` |
| `a ≠ 0, Δ > 0` | Bậc 2, hai nghiệm `t₁, t₂` | Kiểm tra `t₁ ≥ 0` hoặc `t₂ ≥ 0` |
| `a = 0, b ≠ 0` | Bậc 1, `t = -c/b` | Kiểm tra `t ≥ 0` |
| `a = b = c = 0` | Vô số nghiệm | `p = 1` |
| `a = b = 0, c ≠ 0` | Vô nghiệm | `p = 0` |

> **Lưu ý then chốt:** Khác với bài TRR1_1010 kiểm tra nghiệm **dương** (`> 0`), bài này chỉ cần nghiệm **thực** nên điều kiện trên t là `t ≥ 0` (vì `t = x²` cho phép `t = 0` tương ứng `x = 0`).
