## Đề bài

> **TRR1_3011 - Liệt kê một phần hoán vị**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3011

**Mô tả:** Cho hai số nguyên dương `n`, `t` và một hoán vị của `n` số nguyên dương đầu tiên.
Tìm `t` hoán vị liền kề **tiếp theo** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n, t ≤ 100` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5 2` / `3 1 2 5 4` | `3 1 4 2 5` / `3 1 4 5 2` |

> **Giải thích Test 1:** Hai hoán vị kế tiếp của `(3,1,2,5,4)` là `(3,1,4,2,5)` và `(3,1,4,5,2)`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê một phần hoán vị |
| **Mã bài** | TRR1_3011 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh t hoán vị liên tiếp kế tiếp theo thứ tự từ điển |
| **Kiến thức** | Thứ tự từ điển trên hoán vị, thuật toán sinh hoán vị kế tiếp |
| **Thuật toán** | Lặp t lần `next_permutation`: tìm i giảm từ phải → tìm j nhỏ nhất > a[i] ở bên phải → swap → reverse đuôi; nếu hết hoán vị → in `0` cho các dòng còn lại |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

// Sinh hoán vị kế tiếp theo thứ tự từ điển
// Trả về false nếu đang ở hoán vị lớn nhất (không có hoán vị tiếp theo)
bool nextPermutation(vector<int>& a) {
    int n = a.size();

    // B1: Tìm i lớn nhất sao cho a[i] < a[i+1] (từ phải sang trái)
    int i = n - 2;
    while (i >= 0 && a[i] >= a[i+1]) i--;
    if (i < 0) return false;                // Hoán vị lớn nhất, không có kế tiếp

    // B2: Tìm j lớn nhất sao cho a[j] > a[i] (bên phải vị trí i)
    int j = n - 1;
    while (a[j] <= a[i]) j--;

    swap(a[i], a[j]);                       // B3: Hoán đổi a[i] và a[j]
    reverse(a.begin() + i + 1, a.end());    // B4: Đảo ngược đuôi a[i+1..n-1]
    return true;
}

int main() {
    int n, t;
    cin >> n >> t;
    vector<int> a(n);
    for (int i = 0; i < n; i++) cin >> a[i];

    for (int step = 0; step < t; step++) {
        // Nếu không còn hoán vị tiếp theo → in 0 cho tất cả dòng còn lại
        if (!nextPermutation(a)) {
            for (int remain = step; remain < t; remain++) cout << "0\n";
            return 0;
        }
        for (int x = 0; x < n; x++)
            cout << a[x] << " \n"[x == n-1];
    }

    return 0;
}
```

---

**Giải thích logic (minh họa Test 1):**

Hoán vị ban đầu: `3 1 2 5 4`

**Lần 1 — next:**

| Bước | Thao tác | Kết quả |
|---|---|---|
| B1 | `a[2]=2 < a[3]=5` → `i=2` | vị trí `i=2` |
| B2 | Từ phải: `a[4]=4 > 2` → `j=4` | vị trí `j=4` |
| B3 | Swap `a[2]↔a[4]` | `3 1 4 5 2` |
| B4 | Reverse `a[3..4]=[5,2]` → `[2,5]` | `3 1 4 2 5` ✓ |

**Lần 2 — next** từ `3 1 4 2 5`:

| Bước | Thao tác | Kết quả |
|---|---|---|
| B1 | `a[3]=2 < a[4]=5` → `i=3` | vị trí `i=3` |
| B2 | `a[4]=5 > 2` → `j=4` | vị trí `j=4` |
| B3 | Swap `a[3]↔a[4]` | `3 1 4 2 5`→ `3 1 4 5 2` |
| B4 | Reverse `a[4..4]` (1 phần tử) | `3 1 4 5 2` ✓ |

> **Điểm quan trọng:** Khi hết hoán vị giữa chừng, **toàn bộ các dòng còn lại** đều in `0` — đảm bảo output luôn đủ `t` dòng.