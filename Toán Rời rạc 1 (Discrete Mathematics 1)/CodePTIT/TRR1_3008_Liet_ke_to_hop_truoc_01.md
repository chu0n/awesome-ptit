## Đề bài

> **TRR1_3008 - Liệt kê tổ hợp trước 01**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3008

**Mô tả:** Cho hai số nguyên dương `n`, `k` và một tổ hợp chập `k` của `n` số nguyên dương đầu tiên.
Tìm tổ hợp liền kề **trước** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n ≤ 100`, `k ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `5 3` / `1 2 5` | `1 2 4` |

> **Giải thích Test 1:** Tổ hợp liền kề trước của `(1,2,5)` là `(1,2,4)`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê tổ hợp trước 01 |
| **Mã bài** | TRR1_3008 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Tìm tổ hợp chập k liền kề trước theo thứ tự từ điển |
| **Kiến thức** | Thứ tự từ điển trên tổ hợp, thuật toán sinh tổ hợp |
| **Thuật toán** | Tìm vị trí `i` ngoài cùng bên phải có thể giảm (tức `a[i] > a[i-1]+1` hoặc `i=0` và `a[0]>1`), giảm `a[i]` đi 1, điền các vị trí sau bằng `a[i]+1, a[i]+2,...` |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, k;
    cin >> n >> k;
    vector<int> a(k);
    for (int i = 0; i < k; i++) cin >> a[i];

    // Thuật toán tìm tổ hợp liền kề TRƯỚC theo thứ tự từ điển:
    // Tổ hợp được lưu tăng dần: a[0] < a[1] < ... < a[k-1]
    //
    // B1: Tìm i ngoài cùng bên phải sao cho a[i] có thể giảm
    //     Điều kiện giảm được: a[i] > (i+1)
    //     Vì tổ hợp tăng dần, a[i] nhỏ nhất có thể là i+1 (1-indexed)
    //     Nếu a[i] = i+1 → không thể giảm thêm
    //
    // B2: Nếu không tìm được i → đang ở tổ hợp nhỏ nhất → in 0
    //
    // B3: Giảm a[i] đi 1, sau đó điền a[i+1..k-1] liên tiếp tăng dần
    //     từ a[i]+1 để tạo tổ hợp lớn nhất có thể với tiền tố đó

    int i = k - 1;
    while (i >= 0 && a[i] == i + 1) i--;   // B1: tìm i từ phải

    if (i < 0) { cout << 0; return 0; }    // B2: tổ hợp nhỏ nhất

    a[i]--;                                 // B3: giảm a[i] đi 1
    for (int j = i + 1; j < k; j++)
        a[j] = a[j-1] + 1;                 // Điền phần sau tăng liên tiếp

    for (int x = 0; x < k; x++)
        cout << a[x] << " \n"[x == k-1];

    return 0;
}
```

---

**Giải thích logic (minh họa Test 1):**

Tổ hợp: `1 2 5`, n=5, k=3

| Bước | Thao tác | Kết quả |
|---|---|---|
| B1 | `a[2]=5, i+1=3` → `5≠3` → dừng, `i=2` | vị trí `i=2` (giá trị `5`) |
| B3a | `a[2]-- → 4` | `1 2 4` |
| B3b | Không có `j > 2` trong k=3 | `1 2 4` ✓ |

Thêm ví dụ minh họa trường hợp phải điền lại đuôi — tổ hợp `1 3 4`, n=5, k=3:

| Bước | Thao tác | Kết quả |
|---|---|---|
| B1 | `a[2]=4=3+1` → tiếp; `a[1]=3≠2+1=2` → dừng, `i=1` | vị trí `i=1` |
| B3a | `a[1]-- → 2` | `1 2 4` |
| B3b | `a[2] = a[1]+1 = 3` | `1 2 3` ✓ |

> **Điểm mấu chốt:** Sau khi giảm `a[i]`, các vị trí sau phải điền **tăng liên tiếp** từ `a[i]+1` — đây là tổ hợp **lớn nhất có thể** với tiền tố `a[0..i]`, đảm bảo đúng thứ tự từ điển.