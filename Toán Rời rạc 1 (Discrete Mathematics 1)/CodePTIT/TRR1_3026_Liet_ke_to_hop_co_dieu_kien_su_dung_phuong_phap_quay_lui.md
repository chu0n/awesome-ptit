## Đề bài

> **TRR1_3026 - Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3026

**Mô tả:** Cho ba số nguyên dương `n`, `k` và `t`. Liệt kê tất cả tổ hợp gồm `k` phần tử của `n` số nguyên dương đầu tiên, trong đó phần tử thứ `k` (cuối cùng) nhận giá trị `t`.

**Giới hạn:** `3 ≤ n ≤ 30`, `k, t ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5 3 4` | `1 2 4` / `1 3 4` / `2 3 4` |

> **Giải thích Test 1:** Với n=5, có 3 tổ hợp gồm k=3 phần tử có phần tử cuối là 4.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui |
| **Mã bài** | TRR1_3026 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh tất cả tổ hợp chập k có phần tử cuối cố định bằng t |
| **Kiến thức** | Backtracking, tổ hợp có điều kiện, thứ tự từ điển |
| **Thuật toán** | Quay lui: cố định `a[k]=t`, các vị trí `1..k-1` chọn từ `1..t-1` tăng dần |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, k, t;
int a[35];
bool found = false;

void backtrack(int pos, int start) {
    // Đã điền xong k-1 vị trí đầu → ghép phần tử cuối cố định t
    if (pos == k) {
        a[k] = t;
        for (int i = 1; i <= k; i++) cout << a[i] << " \n"[i==k];
        found = true;
        return;
    }
    // Cắt nhánh: chọn val < t và còn đủ phần tử cho các vị trí sau
    for (int val = start; val <= t - (k - pos); val++) {
        a[pos] = val;
        backtrack(pos + 1, val + 1);
    }
}

int main() {
    cin >> n >> k >> t;

    // Kiểm tra hợp lệ: cần ít nhất k-1 số nhỏ hơn t
    if (t < k) { cout << 0; return 0; }

    backtrack(1, 1);    // Bắt đầu từ vị trí 1, giá trị từ 1
    if (!found) cout << 0;
    return 0;
}
```

---

**Giải thích thuật toán:**

Tổ hợp tăng dần, phần tử cuối `a[k] = t` cố định → các vị trí `1..k-1` chỉ được chọn từ `1..t-1`:

**Cắt nhánh:** `val <= t - (k - pos)` đảm bảo còn đủ số lượng phần tử nhỏ hơn `t` để điền các vị trí còn lại. Ví dụ n=5, k=3, t=4, pos=1: `val <= 4-(3-1) = 2` — không chọn 3 vì vị trí 2 cần một số trong `(3, 4)` mà `4` đã bị chiếm.

```
a[3]=4 (cố định)
  pos=1, start=1: val=1 → a[1]=1
    pos=2, start=2: val=2 → "1 2 4" ✓
                    val=3 → "1 3 4" ✓
  pos=1, start=1: val=2 → a[1]=2
    pos=2, start=3: val=3 → "2 3 4" ✓
```

> **So sánh với TRR1_3025:**
>
> | | TRR1_3025 | TRR1_3026 |
> |---|---|---|
> | Cố định | Phần tử **đầu** = t | Phần tử **cuối** = t |
> | Các vị trí còn lại | Chọn từ `t+1..n` | Chọn từ `1..t-1` |
> | Kiểm tra hợp lệ | `t <= n` | `t >= k` |