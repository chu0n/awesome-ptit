## Đề bài

> **TRR1_2040 - Xâu nhị phân 14**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_2040

**Mô tả:** Cho trước hai số nguyên dương `n` và `s`.
Tìm số lượng `t` các xâu nhị phân độ dài `n` có tổng các chữ số bằng `s`.

**Giới hạn:** `n, s ≤ 50`, `s ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `3 1` | `3` |

> **Giải thích Test 1:** Ba xâu thỏa mãn: `001`, `010`, `100`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Xâu nhị phân 14 |
| **Mã bài** | TRR1_2040 |
| **Loại bài** | Toán rời rạc — Tổ hợp đếm |
| **Mục đích** | Đếm xâu nhị phân độ dài n có đúng s chữ số 1 |
| **Kiến thức** | Tổ hợp chập, tam giác Pascal, số tổ hợp C(n,s) |
| **Thuật toán** | Tổ hợp — chọn s vị trí đặt chữ số `1` trong n vị trí → `C(n, s)` |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, s;
    cin >> n >> s;

    // Xâu nhị phân độ dài n có tổng chữ số = s
    // ↔ Chọn đúng s vị trí trong n vị trí để đặt chữ số '1'
    // → Số lượng = C(n, s) (tổ hợp chập s của n)

    // Tính C(n,s) bằng tam giác Pascal để tránh tràn số
    // C[i][j] = số cách chọn j phần tử từ i phần tử
    vector<vector<unsigned long long>> C(n + 1, vector<unsigned long long>(n + 1, 0));

    for (int i = 0; i <= n; i++) {
        C[i][0] = 1;                            // C(i,0) = 1
        for (int j = 1; j <= i; j++)
            C[i][j] = C[i-1][j-1] + C[i-1][j]; // Công thức Pascal
    }

    cout << C[n][s];
    return 0;
}
```

---

**Giải thích logic:**

Một xâu nhị phân độ dài `n` có tổng chữ số bằng `s` chính là xâu có **đúng s chữ số `1`** ở s vị trí bất kỳ trong n vị trí:

$$t = \binom{n}{s} = \frac{n!}{s!(n-s)!}$$

Kiểm tra với n=3, s=1:

$$t = \binom{3}{1} = 3 \checkmark \quad \text{(001, 010, 100)}$$

> **Lưu ý:** Dùng **tam giác Pascal** thay vì tính giai thừa trực tiếp để tránh tràn số khi `n` lớn đến 50.