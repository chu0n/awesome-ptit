## Đề bài

> **TRR1_4004 - Bài toán phân việc 02**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_4004

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với năng suất `c[i][j]`. Tìm phương án phân công mỗi nhân viên một việc sao cho tổng năng suất **lớn nhất**.

**Giới hạn:** `n ≤ 10` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `4` / ma trận 4×4 | `30` / `4 1 2 3` |

> **Giải thích Test 1:** Giao việc 4,1,2,3 cho nhân viên 1,2,3,4 → tổng năng suất = 6+7+9+8 = 30.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Bài toán phân việc 02 |
| **Mã bài** | TRR1_4004 |
| **Loại bài** | Toán rời rạc — Tối ưu hóa tổ hợp |
| **Mục đích** | Tìm phân công tối ưu tối đa hóa tổng năng suất |
| **Kiến thức** | Duyệt toàn thể, hoán vị, bài toán phân việc |
| **Thuật toán** | Backtracking duyệt toàn bộ hoán vị công việc, cắt nhánh khi năng suất tích lũy không thể vượt nghiệm tốt nhất |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, c[11][11];
int assign_[11], bestAssign[11];  // assign_[i] = công việc giao cho nhân viên i
bool used[11];                    // used[j] = true nếu công việc j đã được giao
long long bestVal;

void backtrack(int worker, long long curVal) {
    if (worker > n) {
        // Cập nhật nghiệm nếu tổng năng suất lớn hơn hiện tại
        if (curVal > bestVal) {
            bestVal = curVal;
            for (int i = 1; i <= n; i++) bestAssign[i] = assign_[i];
        }
        return;
    }
    for (int job = 1; job <= n; job++) {
        if (used[job]) continue;            // Công việc đã được giao → bỏ qua
        used[job] = true;
        assign_[worker] = job;
        backtrack(worker + 1, curVal + c[worker][job]);
        used[job] = false;                  // Quay lui: thử công việc khác
    }
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) cin >> c[i][j];

    bestVal = 0;    // Tìm max nên khởi tạo bằng 0
    backtrack(1, 0);

    cout << bestVal << '\n';
    for (int i = 1; i <= n; i++) cout << bestAssign[i] << " \n"[i==n];
    return 0;
}
```

---

**Giải thích thuật toán:**

Bài này **đối xứng hoàn toàn** với TRR1_4003 — chỉ đổi **tìm min → tìm max**:

| | TRR1_4003 (min) | TRR1_4004 (max) |
|---|---|---|
| Khởi tạo | `bestCost = LLONG_MAX` | `bestVal = 0` |
| Cập nhật | `curCost < bestCost` | `curVal > bestVal` |
| Cắt nhánh | `next > bestCost` | Không cắt được dễ dàng |

> **Tại sao không cắt nhánh cho bài max?** Với bài min, chi phí chỉ tăng nên dễ cắt. Với bài max, năng suất cũng chỉ tăng — nhưng ta không biết trước tổng tối đa có thể đạt được từ các nhân viên còn lại, nên **không cắt nhánh an toàn** mà vẫn đảm bảo đúng. Với `n ≤ 10`, `10! ≈ 3.6×10⁶` vẫn chạy trong 1 giây mà không cần cắt.