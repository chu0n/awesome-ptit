## Đề bài

> **TRR1_3025 - Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3025

**Mô tả:** Cho ba số nguyên dương `n`, `k` và `t`. Liệt kê tất cả tổ hợp gồm `k` phần tử của `n` số nguyên dương đầu tiên, trong đó phần tử thứ nhất nhận giá trị `t`.

**Giới hạn:** `3 ≤ n ≤ 30`, `k, t ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5 3 2` | `2 3 4` / `2 3 5` / `2 4 5` |

> **Giải thích Test 1:** Với n=5, có 3 tổ hợp gồm k=3 phần tử có phần tử đầu là 2.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui |
| **Mã bài** | TRR1_3025 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh tất cả tổ hợp chập k có phần tử đầu cố định bằng t |
| **Kiến thức** | Backtracking, tổ hợp có điều kiện, thứ tự từ điển |
| **Thuật toán** | Quay lui: cố định `a[1]=t`, các vị trí sau chọn từ `t+1..n` tăng dần để đảm bảo tổ hợp tăng dần |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, k, t;
int a[35];      // Tổ hợp đang xây dựng
bool found = false;

void backtrack(int pos, int start) {
    if (pos > k) {
        for (int i = 1; i <= k; i++) cout << a[i] << " \n"[i==k];
        found = true;
        return;
    }
    // Cắt nhánh: số phần tử còn lại đủ để điền không?
    for (int val = start; val <= n - (k - pos); val++) {
        a[pos] = val;
        backtrack(pos + 1, val + 1);    // Chọn từ val+1 để tổ hợp tăng dần
    }
}

int main() {
    cin >> n >> k >> t;

    // Phần tử đầu tiên bắt buộc là t, kiểm tra tính hợp lệ
    if (t > n || k > n) { cout << 0; return 0; }

    a[1] = t;
    backtrack(2, t + 1);    // Bắt đầu từ vị trí 2, giá trị từ t+1 trở đi

    if (!found) cout << 0;
    return 0;
}
```

---

**Giải thích thuật toán:**

Tổ hợp phải **tăng dần** (vì là tổ hợp, không phải chỉnh hợp), nên:
- Cố định `a[1] = t`
- Từ vị trí 2 trở đi, chỉ chọn giá trị `> a[pos-1]` (tức từ `t+1, t+2,...`)

**Cắt nhánh:** `val <= n - (k - pos)` đảm bảo còn đủ số lượng phần tử để điền các vị trí còn lại. Ví dụ n=5, k=3, pos=2: `val <= 5-(3-2) = 4` — không chọn 5 vì không còn số nào lớn hơn cho vị trí 3.

```
a[1]=2 (cố định)
  pos=2, start=3: val=3 → a[2]=3
    pos=3, start=4: val=4 → "2 3 4" ✓
                    val=5 → "2 3 5" ✓
  pos=2, start=3: val=4 → a[2]=4
    pos=3, start=5: val=5 → "2 4 5" ✓
```