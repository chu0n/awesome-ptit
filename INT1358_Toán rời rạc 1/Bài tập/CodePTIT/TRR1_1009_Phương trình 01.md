# TRR1_1009 — Phương trình 01

> **Loại:** Mệnh đề logic · **Thuật toán:** Biện luận phương trình bậc nhất · **Thời gian:** O(1) · **Bộ nhớ:** O(1)

## Đề bài
> **TRR1_1009 - Phương trình 01**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_1009

**Mô tả:** Cho trước hai số thực `a` và `b`. Xét mệnh đề `p` = "Phương trình `ax + b = 0` có ít nhất một nghiệm thực âm".

**Yêu cầu:** Xác định giá trị của `p` với hai số thực `a` và `b` đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa hai số thực `a` và `b` — thời gian chạy ≤ 1 giây, bộ nhớ 64 MB.

**Kết quả:** Ghi ra tệp Output chuẩn là giá trị của `p`.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5.0 7.5</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Phương trình 5.0*x + 7.5 = 0 có 1 nghiệm thực âm.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Mệnh đề `p`: tồn tại `x < 0` để `ax + b = 0`.
- `a != 0`: nghiệm duy nhất `x = -b/a`, đúng khi `x < 0`.
- `a == 0, b == 0`: mọi `x` là nghiệm, nên có nghiệm âm.
- `a == 0, b != 0`: vô nghiệm.
- Nghiệm `0` không phải nghiệm âm.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    double a, b;
    cin >> a >> b;

    // Phương trình ax + b = 0
    // TH1: a != 0 → nghiệm duy nhất x = -b/a, kiểm tra x < 0
    // TH2: a == 0, b == 0 → vô số nghiệm (gồm cả nghiệm âm) → p = true
    // TH3: a == 0, b != 0 → vô nghiệm → p = false

    bool p;
    if (a != 0) {
        p = (-b / a) < 0;  // Nghiệm duy nhất, kiểm tra âm
    } else if (b == 0) {
        p = true;  // Mọi x đều là nghiệm, có nghiệm âm
    } else {
        p = false;  // Vô nghiệm
    }

    cout << p;
    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `a`, `b` | Hai hệ số thực đọc từ input |
| `p` | Giá trị mệnh đề, in ra `0` hoặc `1` |
| `a != 0` | Nhánh nghiệm duy nhất `x = -b/a` |
| `a == 0` | Nhánh hằng: vô số nghiệm hoặc vô nghiệm |

1. Đọc `a`, `b`.
2. Nếu `a != 0`, gán `p = (-b / a) < 0`.
3. Nếu `a == 0`, gán `p = (b == 0)`.
4. In `p`.

**Chỗ dễ sai**
- Dùng `<= 0` sẽ nhận cả nghiệm `0`.
- Xử lý `0 = 0` thành vô nghiệm.
- Chia `-b/a` trước khi kiểm tra `a != 0`.
- Dùng `int` cho hệ số thực làm mất phần thập phân.

**Độ phức tạp:** `O(1)` thời gian, `O(1)` bộ nhớ.

## Chạy tay

| `a` | `b` | Nghiệm | `p` |
|---:|---:|---|---:|
| 5.0 | 7.5 | `-1.5` | 1 |
| 2 | 0 | `0`, không âm | 0 |
| 0 | 0 | mọi `x` | 1 |
| 0 | 3 | vô nghiệm | 0 |

</details>

<details>
<summary><strong>Hướng 2 — Biện luận theo dấu, không chia</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    double a, b;
    cin >> a >> b;

    bool p;
    if (a != 0) {
        bool bothPositive = a > 0 && b > 0;
        bool bothNegative = a < 0 && b < 0;
        p                 = bothPositive || bothNegative;
    } else {
        p = (b == 0);
    }

    cout << p;
    return 0;
}
```

Nghiệm âm khi `a`, `b` cùng dấu và khác 0; tránh được phép chia số thực. Với `a == 0`, chỉ cần kiểm tra `b == 0`.

</details>
