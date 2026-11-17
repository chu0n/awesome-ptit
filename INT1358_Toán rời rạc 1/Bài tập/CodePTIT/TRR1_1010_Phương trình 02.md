# TRR1_1010 — Phương trình 02

> **Loại:** Mệnh đề logic · **Thuật toán:** Biện luận nghiệm · **Thời gian:** O(1) · **Bộ nhớ:** O(1)

## Đề bài
> **TRR1_1010 - Phương trình 02**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_1010

**Mô tả:** Cho trước ba số thực `a, b` và `c`. Xét mệnh đề `p` = "Phương trình `ax^2 + bx + c = 0` có ít nhất một nghiệm thực dương".

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
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5.0 7.5 1.3</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>0</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Phương trình bậc hai 5.0*x^2 + 7.5*x + 1.3 = 0 có 2 nghiệm thực âm.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Nếu `a != 0`, đây là phương trình bậc hai. Tính `delta = b*b - 4*a*c`.
  - `delta < 0`: vô nghiệm thực, trả `false`.
  - `delta == 0`: nghiệm kép `x = -b/(2a)`, trả `true` khi `x > 0`.
  - `delta > 0`: tính `x1, x2`, trả `true` khi có ít nhất một nghiệm `> 0`.
- Nếu `a == 0`, phương trình thành `bx + c = 0`:
  - `b != 0`: nghiệm `x = -c/b`, trả `true` khi `x > 0`.
  - `b == 0, c == 0`: mọi `x` đều là nghiệm, trả `true`.
  - `b == 0, c != 0`: vô nghiệm, trả `false`.

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

    bool p = false;

    if (a != 0) {
        // --- Phương trình bậc 2: ax^2 + bx + c = 0 ---
        double delta = b * b - 4 * a * c;

        if (delta < 0) {
            p = false;  // Vô nghiệm thực
        } else if (delta == 0) {
            p = (-b / (2 * a)) > 0;  // Nghiệm kép, kiểm tra dương
        } else {
            // Hai nghiệm phân biệt x1, x2
            double x1 = (-b + sqrt(delta)) / (2 * a);
            double x2 = (-b - sqrt(delta)) / (2 * a);
            p         = (x1 > 0) || (x2 > 0);  // Có ít nhất 1 nghiệm dương
        }
    } else if (b != 0) {
        // --- Phương trình bậc 1: bx + c = 0 ---
        p = (-c / b) > 0;  // Nghiệm duy nhất, kiểm tra dương
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

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `p` | Kết quả cuối cùng, in ra `0` hoặc `1` |
| `delta` | Phân biệt số nghiệm của phương trình bậc hai |
| `x1`, `x2` | Hai nghiệm khi `delta > 0` |
| `a == 0` | Hạ bậc về phương trình bậc nhất hoặc hằng |

1. Đọc `a, b, c`.
2. Nếu `a != 0`, tính `delta` rồi rẽ ba nhánh âm / bằng 0 / dương.
3. Nếu `a == 0`, xử lý `b != 0`, rồi `b == 0` với `c == 0` hoặc `c != 0`.
4. In `p`.

**Chỗ dễ sai**
- Nghiệm bằng `0` không phải nghiệm dương.
- Chỉ được chia cho `2*a` khi `a != 0`.
- Chỉ được gọi `sqrt(delta)` khi `delta >= 0`.
- Quên nhánh `a == 0`: code vẫn chạy nhưng chia cho 0 hoặc xét sai bậc.

**Độ phức tạp:** `O(1)` thời gian, `O(1)` bộ nhớ.

</details>

<details>
<summary><strong>Hướng 2 — xét dấu tổng và tích nghiệm</strong></summary>

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
            bool productNegative = (a > 0 && c < 0) || (a < 0 && c > 0);
            bool sumPositive     = (a > 0 && b < 0) || (a < 0 && b > 0);
            p                    = productNegative || sumPositive;
        }
    } else if (b != 0) {
        bool oppositeSigns = (b > 0 && c < 0) || (b < 0 && c > 0);
        p                  = oppositeSigns;
    } else {
        p = (c == 0);
    }

    cout << p;
    return 0;
}
```

Cách này dùng hệ thức Viète, không cần `sqrt`. Với `delta >= 0`, đặt `P = c/a` và `S = -b/a`:

- `P < 0`: hai nghiệm trái dấu, chắc chắn có một nghiệm dương.
- `P >= 0`: có nghiệm dương khi `S > 0`.

Với `a == 0`, phương trình bậc nhất có nghiệm dương khi `b` và `c` trái dấu.

</details>
