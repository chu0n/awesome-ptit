## Đề bài

> **TRR1_5021 - Chọn cặp số 08**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_5021

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Tìm số lượng `t` ít nhất các cặp `(x, y)` (với `x, y ≤ n`) cần chọn để **chắc chắn** có `k` cặp `(a, b)` sao cho `a*b` chia hết cho 4.

**Giới hạn:** `n, k ≤ 10⁴` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `4 2` | `10` |

> **Giải thích Test 1:** Có 8 cặp `(a,b)` mà `a*b` không chia hết cho 4. Chọn 10 cặp thì chắc chắn có ≥ 2 cặp thỏa mãn.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Chọn cặp số 08 |
| **Mã bài** | TRR1_5021 |
| **Loại bài** | Toán rời rạc — Nguyên lý Dirichlet |
| **Mục đích** | Tìm số cặp tối thiểu cần chọn để đảm bảo có k cặp thỏa mãn |
| **Kiến thức** | Nguyên lý Dirichlet, chia hết cho 4, phân tích theo dư mod 4 |
| **Thuật toán** | Đếm `bad` = số cặp `a*b` không chia hết 4 → `t = bad + k`; nếu `good < k` thì in `0` |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    long long n, k;
    cin >> n >> k;

    // Phân loại số trong [1..n] theo mod 4
    long long odd  = (n + 1) / 2;      // Số lẻ: 1, 3, 5, ...
    long long mod2 = n / 2 - n / 4;    // Số ≡ 2 (mod 4): 2, 6, 10, ...

    // Cặp BAD (a*b không chia hết 4): gồm odd×odd, odd×mod2, mod2×odd
    // = (odd + mod2)² - mod2²  vì mod2×mod2 → 2×2=4 → chia hết 4 (không tính)
    long long bad  = (odd + mod2) * (odd + mod2) - mod2 * mod2;
    long long good = n * n - bad;       // Số cặp a*b chia hết 4

    if (good < k) { cout << 0; return 0; }  // Không đủ cặp thỏa mãn

    // Tệ nhất chọn hết bad cặp không thỏa → thêm k cặp thỏa là đủ
    cout << bad + k;
    return 0;
}
```

---

**Giải thích logic:**

Phân loại theo `mod 4` — `a*b` chia hết 4 khi tích chứa đủ 2 thừa số 2:

| Loại a \ Loại b | odd | ≡2 mod4 | ≡0 mod4 |
|---|:---:|:---:|:---:|
| **odd** | ✗ BAD | ✗ BAD | ✓ |
| **≡2 mod4** | ✗ BAD | ✓ (2×2=4) | ✓ |
| **≡0 mod4** | ✓ | ✓ | ✓ |

$$\text{bad} = (\text{odd} + \text{mod2})^2 - \text{mod2}^2$$

Kiểm tra Test 1 (`n=4, k=2`): odd=2, mod2=1
- bad = 3² - 1² = **8** ✓
- good = 16 - 8 = 8 ≥ 2 ✓
- t = 8 + 2 = **10** ✓