## Đề bài
> **TRR1_1010 - Phương trình 02**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_1010

**Mô tả:** Cho trước ba số thực `a, b` và `c`. Xét mệnh đề `p` = "Phương trình `ax^2 + bx + c = 0` có ít nhất một nghiệm thực dương".

**Yêu cầu:** Xác định giá trị của `p` với ba số thực `a, b` và `c` đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa ba số thực `a, b` và `c` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn là giá trị của `p`.

| | Input | Output |
|---|---|---|
| Test 1 | `5.0 7.5 1.3` | `0` |

> **Giải thích Test 1:** Phương trình bậc hai `5.0*x^2 + 7.5*x + 1.3 = 0` có 2 nghiệm thực âm.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Phương trình 02 |
| **Mã bài** | TRR1_1010 |
| **Loại bài** | Toán rời rạc — Mệnh đề logic |
| **Mục đích** | Xác định giá trị chân lý của mệnh đề p |
| **Kiến thức** | Mệnh đề logic, phương trình bậc hai, phương trình bậc nhất |
| **Thuật toán** | Phân tích toàn bộ các trường hợp nghiệm của `ax² + bx + c = 0`, kiểm tra có tồn tại nghiệm dương không |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    double a, b, c;
    cin >> a >> b >> c;

    bool p = false;

    if (a != 0) {
        // --- Phương trình bậc 2: ax^2 + bx + c = 0 ---
        double delta = b*b - 4*a*c;

        if (delta < 0) p = false;                        // Vô nghiệm thực
        else if (delta == 0) p = (-b / (2*a)) > 0;      // Nghiệm kép, kiểm tra dương
        else {
            // Hai nghiệm phân biệt x1, x2
            double x1 = (-b + sqrt(delta)) / (2*a);
            double x2 = (-b - sqrt(delta)) / (2*a);
            p = (x1 > 0) || (x2 > 0);                   // Có ít nhất 1 nghiệm dương
        }
    } else if (b != 0) {
        // --- Phương trình bậc 1: bx + c = 0 ---
        p = (-c / b) > 0;                                // Nghiệm duy nhất, kiểm tra dương

    } else if (c == 0) {
        // --- 0 = 0: vô số nghiệm (kể cả nghiệm dương) ---
        p = true;

    } else {
        // --- Vô nghiệm (c != 0, a = b = 0) ---
        p = false;
    }

    cout << p;
    return 0;
}
```

---

**Giải thích các trường hợp:**

| Điều kiện | Dạng PT | Xử lý |
|---|---|---|
| `a ≠ 0, Δ < 0` | Bậc 2, vô nghiệm thực | `p = 0` |
| `a ≠ 0, Δ = 0` | Bậc 2, nghiệm kép `x = -b/2a` | Kiểm tra `> 0` |
| `a ≠ 0, Δ > 0` | Bậc 2, hai nghiệm phân biệt | Kiểm tra `x1 > 0` hoặc `x2 > 0` |
| `a = 0, b ≠ 0` | Bậc 1, nghiệm `x = -c/b` | Kiểm tra `> 0` |
| `a = b = c = 0` | Vô số nghiệm | `p = 1` |
| `a = b = 0, c ≠ 0` | Vô nghiệm | `p = 0` |
