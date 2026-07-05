## Đề bài
> **TRR1_2002 - Đếm số 02**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_2002

**Mô tả:** Cho trước bốn số nguyên dương `a, b, k` và `m`.

**Yêu cầu:** Tìm số lượng `t` các số nguyên dương trong phạm vi từ `a` đến `b` là bội của `k` hoặc `m`.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa bốn số nguyên dương `a, b, k` và `m`, mỗi số không vượt quá `2^18` và `a ≤ b` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

| | Input | Output |
|---|---|---|
| Test 1 | `1 10 2 3` | `7` |

> **Giải thích Test 1:** Có `t = 7` số trong phạm vi từ `a = 1` đến `b = 10` là bội của `k = 2` hoặc `m = 3` gồm `2, 3, 4, 6, 8, 9` và `10`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Đếm số 02 |
| **Mã bài** | TRR1_2002 |
| **Loại bài** | Toán rời rạc — Tổ hợp đếm |
| **Mục đích** | Đếm số nguyên trong đoạn [a,b] là bội của k hoặc m |
| **Kiến thức** | Nguyên lý bao hàm - loại trừ (Inclusion-Exclusion), UCLN/BCNN |
| **Thuật toán** | `\|A∪B\| = \|A\| + \|B\| - \|A∩B\|` — đếm bội của k, bội của m, trừ đi bội chung của lcm(k,m) |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

// Đếm số bội của x trong đoạn [a, b]
long long countMultiples(long long a, long long b, long long x) {
    return b/x - (a-1)/x;
}

int main() {
    long long a, b, k, m;
    cin >> a >> b >> k >> m;

    long long lcm_km = k / __gcd(k, m) * m;    // lcm(k,m) để tránh tràn số

    // Bao hàm - loại trừ: |bội(k) ∪ bội(m)| = |bội(k)| + |bội(m)| - |bội(lcm(k,m))|
    long long t = countMultiples(a, b, k)
                + countMultiples(a, b, m)
                - countMultiples(a, b, lcm_km);

    cout << t;
    return 0;
}
```

---

**Giải thích logic:**

Gọi A = tập bội của k trong [a,b], B = tập bội của m trong [a,b]:

$$|A \cup B| = |A| + |B| - |A \cap B|$$

Trong đó $A \cap B$ chính là tập bội của $\text{lcm}(k, m)$ trong [a,b].

Hàm `countMultiples(a, b, x)` đếm bội của x trong [a,b] bằng công thức:

$$\left\lfloor \frac{b}{x} \right\rfloor - \left\lfloor \frac{a-1}{x} \right\rfloor$$

| Thành phần | Ví dụ (a=1, b=10, k=2, m=3) | Giá trị |
|---|---|---|
| Bội của k=2 | {2,4,6,8,10} | 5 |
| Bội của m=3 | {3,6,9} | 3 |
| Bội của lcm(2,3)=6 | {6} | 1 |
| **Kết quả** | 5 + 3 - 1 | **7** ✓ |
