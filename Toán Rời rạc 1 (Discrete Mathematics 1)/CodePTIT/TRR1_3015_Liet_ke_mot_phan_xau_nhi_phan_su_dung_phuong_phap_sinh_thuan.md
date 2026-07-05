## Đề bài

> **TRR1_3015 - Liệt kê một phần xâu nhị phân sử dụng phương pháp sinh thuận**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3015

**Mô tả:** Cho hai số nguyên dương `n`, `t` và một xâu nhị phân độ dài `n`.
Tìm `t` xâu nhị phân liền kề **tiếp theo** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n, t ≤ 100` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5 2` / `1 0 0 0 1` | `1 0 0 1 0` / `1 0 0 1 1` |

> **Giải thích Test 1:** Hai xâu kế tiếp của `(1,0,0,0,1)` là `(1,0,0,1,0)` và `(1,0,0,1,1)`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê một phần xâu nhị phân sinh thuận |
| **Mã bài** | TRR1_3015 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh t xâu nhị phân liên tiếp kế tiếp theo thứ tự từ điển |
| **Kiến thức** | Thứ tự từ điển trên xâu nhị phân, cộng 1 nhị phân |
| **Thuật toán** | Cộng 1 vào xâu nhị phân (từ phải sang trái, xử lý nhớ): tìm bit `0` ngoài cùng bên phải → đặt thành `1`, các bit sau đặt về `0`; nếu toàn `1` → không có xâu tiếp theo → in `0` |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

// Sinh xâu nhị phân kế tiếp (cộng 1 nhị phân từ phải sang trái)
// Trả về false nếu xâu đang là 1...1 (lớn nhất, không có kế tiếp)
bool nextBinary(vector<int>& a) {
    int n = a.size();

    // Tìm bit 0 ngoài cùng bên phải để lật thành 1
    int i = n - 1;
    while (i >= 0 && a[i] == 1) i--;   // Bỏ qua các bit 1 liên tiếp từ phải
    if (i < 0) return false;            // Toàn bit 1 → xâu lớn nhất, không có kế tiếp

    a[i] = 1;                           // Lật bit 0 thành 1 (vị trí nhớ dừng)
    for (int j = i + 1; j < n; j++)
        a[j] = 0;                       // Reset toàn bộ bit sau về 0

    return true;
}

int main() {
    int n, t;
    cin >> n >> t;
    vector<int> a(n);
    for (int i = 0; i < n; i++) cin >> a[i];

    for (int step = 0; step < t; step++) {
        // Nếu không còn xâu tiếp theo → in 0 cho tất cả dòng còn lại
        if (!nextBinary(a)) {
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

Xâu ban đầu: `1 0 0 0 1`

**Lần 1 — next:**

| Bước | Thao tác | Kết quả |
|---|---|---|
| Tìm i | `a[4]=1` → bỏ qua; `a[3]=0` → dừng, `i=3` | vị trí `i=3` |
| Lật bit | `a[3]: 0 → 1` | `1 0 0 1 1` |
| Reset sau | `a[4]: 1 → 0` | `1 0 0 1 0` ✓ |

**Lần 2 — next** từ `1 0 0 1 0`:

| Bước | Thao tác | Kết quả |
|---|---|---|
| Tìm i | `a[4]=0` → dừng, `i=4` | vị trí `i=4` |
| Lật bit | `a[4]: 0 → 1` | `1 0 0 1 1` |
| Reset sau | Không có `j > 4` | `1 0 0 1 1` ✓ |

> **Bản chất:** Đây chính là phép **cộng 1 số nhị phân** — tìm bit `0` ngoài cùng bên phải, lật thành `1`, reset toàn bộ bit phía sau về `0`. Tương tự cách TRR1_3011 xử lý hoán vị nhưng đơn giản hơn nhiều vì chỉ có 2 ký tự `{0,1}`.