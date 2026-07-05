## Đề bài

> **TRR1_3016 - Liệt kê một phần xâu nhị phân sử dụng phương pháp sinh ngược**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3016

**Mô tả:** Cho hai số nguyên dương `n`, `t` và một xâu nhị phân độ dài `n`.
Tìm `t` xâu nhị phân liền kề **tiếp theo** theo thứ tự **ngược** với thứ tự từ điển.

**Giới hạn:** `3 ≤ n, t ≤ 100` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5 2` / `1 0 1 0 1` | `1 0 1 0 0` / `1 0 0 1 1` |

> **Giải thích Test 1:** Hai xâu kế tiếp (thứ tự ngược) của `(1,0,1,0,1)` là `(1,0,1,0,0)` và `(1,0,0,1,1)`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê một phần xâu nhị phân sinh ngược |
| **Mã bài** | TRR1_3016 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh t xâu nhị phân liên tiếp kế tiếp theo thứ tự **ngược** từ điển |
| **Kiến thức** | Thứ tự từ điển ngược trên xâu nhị phân, trừ 1 nhị phân |
| **Thuật toán** | Trừ 1 nhị phân: duyệt từ phải, reset bit `0→1` ngay trong vòng lặp cho đến khi gặp bit `1` → lật thành `0`; nếu `id < 0` → in `0` rồi `continue` (không dừng hẳn) |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);

    int n, t;
    cin >> n >> t;
    vector<int> a(n);
    for (int i = 0; i < n; i++) cin >> a[i];

    while (t--) {
        // Duyệt từ phải: vừa tìm vừa reset bit 0 → 1 (phần "mượn" khi trừ 1)
        int id = n - 1;
        while (id >= 0 && a[id] == 0) a[id--] = 1;

        // Toàn bit 0 → không có xâu kế tiếp → in 0, tiếp tục (xâu thành 1...1 cho lần sau)
        if (id < 0) { cout << 0 << '\n'; continue; }

        a[id] = 0;  // Lật bit 1 → 0 tại vị trí tìm được
        for (int i = 0; i < n; i++) cout << a[i] << ' ';
        cout << '\n';
    }

    return 0;
}
```

---

**Giải thích logic (minh họa Test 1):**

Xâu ban đầu: `1 0 1 0 1`

**Lần 1:**

| Bước | Thao tác | Kết quả |
|---|---|---|
| Duyệt từ phải | `a[4]=1` → dừng ngay, `id=4` | không reset bit nào |
| Lật bit | `a[4]: 1 → 0` | `1 0 1 0 0` ✓ |

**Lần 2** từ `1 0 1 0 0`:

| Bước | Thao tác | Kết quả |
|---|---|---|
| Duyệt từ phải | `a[4]=0 → 1, id=3`; `a[3]=0 → 1, id=2`; `a[2]=1` → dừng | `1 0 1 1 1` tạm thời |
| Lật bit | `a[2]: 1 → 0` | `1 0 0 1 1` ✓ |

> **Bài học quan trọng:** Khi `id < 0` (toàn `0`), dùng `continue` thay vì dừng hẳn — xâu lúc này đã được reset thành `1...1` nên các lần kế tiếp vẫn có thể tiếp tục sinh bình thường.