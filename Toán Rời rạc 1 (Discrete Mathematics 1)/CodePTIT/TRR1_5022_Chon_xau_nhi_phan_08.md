## Đề bài

> **TRR1_5022 - Chọn xâu nhị phân 08**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_5022

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Tìm số lượng ít nhất `t` các xâu nhị phân độ dài `n` cần chọn để **chắc chắn** tồn tại xâu có đúng `k` chữ số 1.

**Giới hạn:** `n, k ≤ 50`, `2 ≤ k ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `3 2` | `6` |

> **Giải thích Test 1:** Có 5 xâu độ dài 3 **không** có đúng 2 chữ số 1: `000, 001, 010, 100, 111`. Chọn 6 xâu thì chắc chắn có xâu đúng 2 chữ số 1.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Chọn xâu nhị phân 08 |
| **Mã bài** | TRR1_5022 |
| **Loại bài** | Toán rời rạc — Nguyên lý Dirichlet |
| **Mục đích** | Tìm số xâu tối thiểu cần chọn để chắc chắn có xâu đúng k chữ số 1 |
| **Kiến thức** | Nguyên lý Dirichlet, tổ hợp, số tổ hợp C(n,k) |
| **Thuật toán** | Đếm `bad` = số xâu độ dài n **không** có đúng k chữ số 1 = `2ⁿ - C(n,k)`; nếu `C(n,k)=0` thì in `0`; ngược lại `t = bad + 1` |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, k;
    cin >> n >> k;

    // Tính C(n,k) bằng tam giác Pascal (tránh tràn số khi n=50)
    vector<vector<unsigned long long>> C(n+1, vector<unsigned long long>(n+1, 0));
    for (int i = 0; i <= n; i++) {
        C[i][0] = 1;
        for (int j = 1; j <= i; j++) C[i][j] = C[i-1][j-1] + C[i-1][j];
    }

    unsigned long long total = 1ULL << n;   // Tổng xâu nhị phân độ dài n = 2^n
    unsigned long long good  = C[n][k];     // Số xâu có đúng k chữ số 1

    if (good == 0) { cout << 0; return 0; } // Không tồn tại xâu thỏa mãn

    // Tệ nhất chọn hết bad xâu không thỏa → thêm 1 chắc chắn có xâu đúng k số 1
    unsigned long long bad = total - good;
    cout << bad + 1;
    return 0;
}
```

---

**Giải thích logic:**

Số xâu nhị phân độ dài `n` có đúng `k` chữ số 1 = $\binom{n}{k}$ (chọn `k` vị trí đặt số 1).

$$\text{bad} = 2^n - \binom{n}{k} \quad \Rightarrow \quad t = \text{bad} + 1$$

Kiểm tra Test 1 (`n=3, k=2`):
- good = C(3,2) = 3 → bad = 8-3 = **5** ✓
- t = 5+1 = **6** ✓