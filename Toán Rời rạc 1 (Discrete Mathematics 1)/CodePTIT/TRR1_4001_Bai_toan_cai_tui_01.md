## Đề bài

> **TRR1_4001 - Bài toán cái túi 01**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_4001

**Mô tả:** Cho cái túi sức chứa `b` và `n` đồ vật, mỗi đồ vật có trọng lượng `a[i]` và giá trị `c[i]`. Tìm cách chọn đồ vật sao cho tổng giá trị lớn nhất mà không vượt quá sức chứa.

**Giới hạn:** `n ≤ 20`, `b ≤ 10⁷`, `a[i], c[i] ≤ 10⁶` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `3 8` / `5 8` / `7 12` / `3 7` | `15` / `1 0 1` |

> **Giải thích Test 1:** Chọn đồ vật 1 (w=5,c=8) và 3 (w=3,c=7), tổng giá trị = 15, tổng trọng lượng = 8 ≤ b.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Bài toán cái túi 01 |
| **Mã bài** | TRR1_4001 |
| **Loại bài** | Toán rời rạc — Tối ưu hóa tổ hợp |
| **Mục đích** | Tìm tập đồ vật có tổng giá trị lớn nhất không vượt sức chứa |
| **Kiến thức** | Duyệt toàn thể, bitmask, bài toán knapsack 0/1 |
| **Thuật toán** | Duyệt tất cả `2ⁿ` tập con bằng bitmask: với mỗi mask kiểm tra tổng trọng lượng ≤ b, cập nhật nghiệm tối ưu |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);

    int n; long long b;
    cin >> n >> b;

    vector<long long> a(n), c(n);  // Trọng lượng và giá trị từng đồ vật
    for (int i = 0; i < n; i++) cin >> a[i] >> c[i];

    long long bestVal = 0;  // Giá trị tối ưu tìm được
    int bestMask = 0;       // Bitmask tương ứng với tập chọn tối ưu

    // Duyệt toàn bộ 2^n tập con qua bitmask
    for (int mask = 0; mask < (1 << n); mask++) {
        long long totalW = 0, totalC = 0;
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) {  // Bit i = 1 → đồ vật i được chọn
                totalW += a[i];
                totalC += c[i];
            }
        }
        // Cập nhật nếu hợp lệ và tốt hơn nghiệm hiện tại
        if (totalW <= b && totalC > bestVal) {
            bestVal = totalC;
            bestMask = mask;
        }
    }

    // In tổng giá trị tối ưu
    cout << bestVal << '\n';
    // In vector chọn: x[i]=1 nếu đồ vật i được chọn, x[i]=0 nếu không
    for (int i = 0; i < n; i++) cout << ((bestMask >> i) & 1) << " \n"[i==n-1];

    return 0;
}
```

---

**Giải thích thuật toán:**

Với `n ≤ 20`, tổng số tập con là `2²⁰ ≈ 10⁶` — hoàn toàn duyệt được trong 1 giây.

**Bitmask** biểu diễn một tập con: bit thứ `i` của `mask` bằng `1` nghĩa là đồ vật `i` được chọn.

```
n=3, b=8:
mask=001 (bin) → chọn {1}:     w=5, c=8  ≤ 8 → bestVal=8
mask=011 (bin) → chọn {1,2}:   w=12      > 8 → bỏ
mask=101 (bin) → chọn {1,3}:   w=8, c=15 ≤ 8 → bestVal=15 ✓
mask=111 (bin) → chọn {1,2,3}: w=15      > 8 → bỏ
...
→ bestMask = 101 → in "1 0 1" ✓
```

> **Lưu ý:** Dùng `long long` cho `b`, `totalW`, `totalC` vì `n=20` đồ vật mỗi cái `≤ 10⁶` → tổng có thể đạt `2×10⁷` vượt `int`.