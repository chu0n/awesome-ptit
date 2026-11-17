# TRR1_1011 — Phương trình 03

> **Loại:** Mệnh đề logic · **Thuật toán:** Đổi biến t = x², biệt thức · **Thời gian:** O(1) · **Bộ nhớ:** O(1)

## Đề bài
> **TRR1_1011 - Phương trình 03**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_1011

**Mô tả:** Cho trước ba số thực `a, b` và `c`. Xét mệnh đề `p` = "Phương trình `ax^4 + bx^2 + c = 0` có ít nhất một nghiệm thực".

**Yêu cầu:** Xác định giá trị của `p` với ba số thực `a, b` và `c` đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa ba số thực `a, b` và `c` — thời gian chạy ≤ 1 giây, bộ nhớ 64 MB.

**Kết quả:** Ghi ra tệp Output chuẩn là giá trị của `p`.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5.0 -7.5 1.3</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Phương trình bậc bốn trùng phương 5*x^4 - 7.5*x^2 + 1.3 = 0 có 4 nghiệm thực phân biệt.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Đặt `t = x² >= 0`; phương trình thành `at² + bt + c = 0`.
- Có `x` thực khi và chỉ khi có `t >= 0`.
- `a != 0`: `Δ < 0` loại; `Δ == 0` nhận khi `t0 >= 0`; `Δ > 0` nhận khi `t1 >= 0` hoặc `t2 >= 0`.
- `a == 0`: hạ bậc `bt + c = 0`; cả ba hệ số bằng 0 thì luôn đúng.
- `t = 0` hợp lệ vì cho `x = 0`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main()
{
    double a, b, c;
    cin >> a >> b >> c;

    // Phương trình trùng phương: ax^4 + bx^2 + c = 0
    // Đặt t = x^2 (t >= 0) → bài toán quy về: at^2 + bt + c = 0 có nghiệm t >= 0 không?
    // Nếu có t >= 0 thỏa mãn thì x = ±sqrt(t) là nghiệm thực của PT gốc.

    bool p = false;

    if (a != 0) {
        // --- PT bậc 2 theo t: at^2 + bt + c = 0 ---
        double delta = b * b - 4 * a * c;

        if (delta < 0) {
            p = false;  // Vô nghiệm thực (theo t)
        } else if (delta == 0) {
            p = (-b / (2 * a)) >= 0;  // Nghiệm kép t0, cần t0 >= 0
        } else {
            double t1 = (-b + sqrt(delta)) / (2 * a);
            double t2 = (-b - sqrt(delta)) / (2 * a);
            p         = (t1 >= 0) || (t2 >= 0);  // Ít nhất 1 nghiệm t >= 0
        }
    } else if (b != 0) {
        // --- PT bậc 1 theo t: bt + c = 0 → t = -c/b ---
        p = (-c / b) >= 0;  // Cần t >= 0
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

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `a`, `b`, `c` | Hệ số phương trình trùng phương |
| `delta` | `b*b - 4*a*c` của phương trình theo `t` |
| `t1`, `t2` | Nghiệm theo `t`, cần ít nhất một nghiệm `>= 0` |
| `p` | Kết quả `1` hoặc `0` |

1. Đọc `a`, `b`, `c`.
2. Nếu `a != 0`, tính `delta` rồi rẽ ba nhánh âm, bằng 0, dương.
3. Nếu `a == 0`, xử lý `b != 0`, rồi `b == 0` với `c == 0` hoặc `c != 0`.
4. In `p`.

**Chỗ dễ sai**
- Dùng `t > 0` làm mất nghiệm `x = 0`.
- Chấp nhận `t` âm, không sinh ra `x` thực.
- Gọi `sqrt(delta)` khi `delta < 0`.
- Chỉ kiểm tra `t1`; khi `a < 0` nghiệm không âm có thể là `t2`.
- Dùng công thức bậc hai khi `a == 0`.

**Độ phức tạp:** `O(1)` thời gian, `O(1)` bộ nhớ.

## Chạy tay

| `a`, `b`, `c` | `delta` | Nghiệm `t` | `p` |
|---|---:|---|---:|
| 5.0, -7.5, 1.3 | 30.25 | 1.3 và 0.2 | 1 |
| 1, 2, 1 | 0 | -1, loại | 0 |
| 1, -2, 1 | 0 | 1, nhận | 1 |
| 1, 0, 0 | 0 | 0 | 1 |

</details>

<details>
<summary><strong>Hướng 2 — Hệ thức Viète cho t, không khai căn</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    double a, b, c;
    cin >> a >> b >> c;

    bool p = false;
    if (a != 0) {
        double delta = b * b - 4 * a * c;
        if (delta >= 0) {
            bool hasZeroRoot     = (c == 0);
            bool productNegative = (a > 0 && c < 0) || (a < 0 && c > 0);
            bool sumPositive     = (a > 0 && b < 0) || (a < 0 && b > 0);
            p                    = hasZeroRoot || productNegative || sumPositive;
        }
    } else if (b != 0) {
        bool hasZeroRoot   = (c == 0);
        bool oppositeSigns = (b > 0 && c < 0) || (b < 0 && c > 0);
        p                  = hasZeroRoot || oppositeSigns;
    } else {
        p = (c == 0);
    }

    cout << p;
    return 0;
}
```

Với `delta >= 0`, có nghiệm `t >= 0` khi `c == 0`, `P < 0`, hoặc `P > 0` và `S > 0`. Không cần `sqrt`, nhưng phải xét dấu cẩn thận.

</details>
