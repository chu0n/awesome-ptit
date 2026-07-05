## Đề bài

> **TRR1_4003 - Bài toán phân việc 01**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_4003

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với chi phí `c[i][j]`. Tìm phương án phân công mỗi nhân viên một việc sao cho tổng thời gian nhỏ nhất.

**Giới hạn:** `n ≤ 10` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `4` / ma trận 4×4 | `5` / `2 4 3 1` |

> **Giải thích Test 1:** Giao việc 2,4,1,3 cho nhân viên 1,2,3,4 — tổng thời gian = 1+2+1+... = 5.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Bài toán phân việc 01 |
| **Mã bài** | TRR1_4003 |
| **Loại bài** | Toán rời rạc — Tối ưu hóa tổ hợp |
| **Mục đích** | Tìm phân công tối ưu giảm thiểu tổng chi phí |
| **Kiến thức** | Duyệt toàn thể, hoán vị, bài toán phân việc (Assignment Problem) |
| **Thuật toán** | Backtracking duyệt toàn bộ hoán vị công việc, cắt nhánh khi chi phí tích lũy vượt nghiệm tốt nhất hiện tại |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, c[11][11];
int assign[11], bestAssign[11];  // assign[i] = công việc giao cho nhân viên i
bool used[11];                   // used[j] = true nếu công việc j đã được giao
long long bestCost;

void backtrack(int worker, long long curCost) {
    // Đã phân công xong n nhân viên → cập nhật nghiệm tối ưu
    if (worker > n) {
        if (curCost < bestCost) {
            bestCost = curCost;
            for (int i = 1; i <= n; i++) bestAssign[i] = assign[i];
        }
        return;
    }
    for (int job = 1; job <= n; job++) {
        if (used[job]) continue;            // Công việc đã được giao → bỏ qua
        long long next = curCost + c[worker][job];
        if (next >= bestCost) continue;     // Cắt nhánh: không thể cải thiện

        used[job] = true;
        assign[worker] = job;
        backtrack(worker + 1, next);
        used[job] = false;                  // Quay lui: hoàn tác để thử công việc khác
    }
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) cin >> c[i][j];

    bestCost = LLONG_MAX;   // Khởi tạo chi phí tốt nhất là vô cực
    backtrack(1, 0);

    cout << bestCost << '\n';
    for (int i = 1; i <= n; i++) cout << bestAssign[i] << " \n"[i==n];
    return 0;
}
```

---

**Giải thích thuật toán:**

Mỗi phương án phân công là một **hoán vị** của `n` công việc cho `n` nhân viên — tổng số phương án là `n! ≤ 10! = 3,628,800`, duyệt được trong 1 giây.

**Cắt nhánh:** Nếu chi phí tích lũy `curCost + c[worker][job] ≥ bestCost` thì toàn bộ nhánh con không thể cho nghiệm tốt hơn → bỏ ngay, giảm đáng kể số nhánh cần duyệt.

```
worker=1: thử job=1 (c=8), job=2 (c=1) ← chọn
  worker=2: thử job=1 (c=7), job=3 (c=9), job=4 (c=2) ← chọn
    worker=3: thử job=1 (c=1) ← chọn
      worker=4: thử job=3 (c=2) → total=1+2+1+... → cập nhật bestCost
```

> **Lưu ý:** Khởi tạo `bestCost = LLONG_MAX` thay vì một giá trị cụ thể để đảm bảo nghiệm đầu tiên hợp lệ luôn được chấp nhận. Điều kiện cắt nhánh dùng `>=` (không phải `>`) vì đề chỉ yêu cầu **một** nghiệm tối ưu.