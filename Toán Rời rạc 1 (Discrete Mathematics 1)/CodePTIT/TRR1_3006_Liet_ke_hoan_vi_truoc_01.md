## Đề bài

> **TRR1_3006 - Liệt kê hoán vị trước 01**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3006

**Mô tả:** Cho số nguyên dương `n` và một hoán vị của `n` số nguyên dương đầu tiên.
Tìm hoán vị liền kề **trước** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n ≤ 100` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5` / `3 1 2 5 4` | `3 1 2 4 5` |

> **Giải thích Test 1:** Hoán vị liền kề trước của `(3,1,2,5,4)` là `(3,1,2,4,5)`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê hoán vị trước 01 |
| **Mã bài** | TRR1_3006 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Tìm hoán vị liền kề trước theo thứ tự từ điển |
| **Kiến thức** | Thứ tự từ điển trên hoán vị, thuật toán sinh hoán vị |
| **Thuật toán** | Đảo ngược thuật toán `next_permutation`: tìm vị trí `i` giảm từ phải, hoán đổi với phần tử lớn hơn nhỏ nhất bên phải, đảo ngược đuôi |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    vector<int> a(n);
    for (int i = 0; i < n; i++) cin >> a[i];

    // Thuật toán tìm hoán vị liền kề TRƯỚC (prev_permutation):
    // B1: Tìm i lớn nhất sao cho a[i] > a[i+1] (từ phải sang trái)
    //     → Đây là vị trí đầu tiên bị "tăng" khi đọc từ phải
    // B2: Nếu không tìm được i → đang ở hoán vị nhỏ nhất → in 0
    // B3: Tìm j lớn nhất sao cho a[j] < a[i] (bên phải vị trí i)
    //     → Phần tử nhỏ hơn a[i] lớn nhất ở bên phải
    // B4: Hoán đổi a[i] và a[j]
    // B5: Đảo ngược đoạn a[i+1..n-1] (từ tăng dần → giảm dần để lấy max)

    int i = n - 2;
    while (i >= 0 && a[i] <= a[i+1]) i--;  // B1: tìm i từ phải

    if (i < 0) { cout << 0; return 0; }    // B2: hoán vị nhỏ nhất

    int j = n - 1;
    while (a[j] >= a[i]) j--;              // B3: tìm j thỏa a[j] < a[i]

    swap(a[i], a[j]);                      // B4: hoán đổi
    reverse(a.begin() + i + 1, a.end());   // B5: đảo ngược đuôi (tăng → giảm)

    for (int x = 0; x < n; x++)
        cout << a[x] << " \n"[x == n-1];

    return 0;
}
```

---

**Giải thích logic (minh họa Test 1):**

Hoán vị: `3 1 2 5 4`

| Bước | Thao tác | Kết quả |
|---|---|---|
| B1 | Tìm i: `a[2]=2 < a[3]=5` → dừng, `i=2` | vị trí `i=2` (giá trị `2`) |
| B3 | Tìm j từ phải: `a[4]=4 > a[2]=2` → `j=4`... `a[3]=5 > 2` → chọn `j=4` | vị trí `j=4` (giá trị `4`) |
| B4 | Swap `a[2]↔a[4]` | `3 1 4 5 2` |
| B5 | Reverse `a[3..4]` | `3 1 4 2 5`... |

> Thực ra với test này: swap `2↔4` → `3 1 4 5 2`, reverse `[5,2]` → `3 1 4 2 5`?

Thử lại đúng hơn — `i=3` vì `a[3]=5 > a[4]=4`:

| Bước | Thao tác | Kết quả |
|---|---|---|
| B1 | `a[3]=5 > a[4]=4` → `i=3` | vị trí `i=3` |
| B3 | `a[4]=4 < a[3]=5` → `j=4` | vị trí `j=4` |
| B4 | Swap `a[3]↔a[4]` | `3 1 2 4 5` |
| B5 | Reverse `a[4..4]` (1 phần tử) | `3 1 2 4 5` ✓ |