## Đề bài

> **TRR1_4009 - Bài toán phân việc 04**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_4009

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với năng suất `c[i][j]`. Tìm phương án phân công sao cho tổng năng suất **lớn nhất** bằng phương pháp nhánh cận.

**Giới hạn:** `n ≤ 15` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `4` / ma trận 4×4 | `30` / `4 1 2 3` |

> **Giải thích Test 1:** Giao việc 4,1,2,3 cho nhân viên 1,2,3,4 → tổng = 6+7+9+8 = 30.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Bài toán phân việc 04 |
| **Mã bài** | TRR1_4009 |
| **Loại bài** | Toán rời rạc — Tối ưu hóa tổ hợp |
| **Mục đích** | Tìm phân công tối ưu tối đa hóa tổng năng suất bằng nhánh cận |
| **Kiến thức** | Branch and Bound, bài toán phân việc, cận trên |
| **Thuật toán** | Backtracking + cận trên: với mỗi nhân viên chưa phân công, cộng thêm năng suất lớn nhất trong hàng tương ứng — nếu cận trên ≤ bestVal thì cắt nhánh |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, c[16][16];
int assign_[16], bestAssign[16];    // assign_[i] = công việc giao cho nhân viên i
bool usedJob[16];                   // usedJob[j] = true nếu công việc j đã được giao
long long bestVal;

// Cận trên: với mỗi nhân viên chưa phân công (worker+1..n),
// cộng thêm năng suất LỚN NHẤT có thể trong hàng (job chưa dùng)
long long upperBound(int worker, long long curVal) {
    long long bound = curVal;
    for (int i = worker + 1; i <= n; i++) {
        long long maxVal = 0;
        for (int j = 1; j <= n; j++)
            if (!usedJob[j]) maxVal = max(maxVal, (long long)c[i][j]);
        bound += maxVal;
    }
    return bound;
}

void backtrack(int worker, long long curVal) {
    if (worker > n) {
        if (curVal > bestVal) {
            bestVal = curVal;
            for (int i = 1; i <= n; i++) bestAssign[i] = assign_[i];
        }
        return;
    }
    for (int job = 1; job <= n; job++) {
        if (usedJob[job]) continue;
        long long next = curVal + c[worker][job];

        usedJob[job] = true;
        assign_[worker] = job;
        // Cắt nhánh: nếu cận trên không vượt được bestVal thì bỏ
        if (upperBound(worker, next) > bestVal)
            backtrack(worker + 1, next);
        usedJob[job] = false;                   // Quay lui
    }
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) cin >> c[i][j];

    bestVal = 0;
    backtrack(1, 0);

    cout << bestVal << '\n';
    for (int i = 1; i <= n; i++) cout << bestAssign[i] << " \n"[i==n];
    return 0;
}
```

---

**Giải thích thuật toán:**

**Cận trên** tại trạng thái đã phân công xong `worker` nhân viên đầu:

$$\text{bound} = \text{curVal} + \sum_{i=\text{worker}+1}^{n} \max_{j \notin \text{used}} c[i][j]$$

Đây là ước lượng **lạc quan** — mỗi nhân viên còn lại lấy năng suất lớn nhất có thể. Nếu ngay cả ước lượng lạc quan này cũng ≤ `bestVal` thì nhánh chắc chắn không cải thiện được → cắt.

```
worker=1, thử job=4: next=6
  bound = 6 + max(c[2][j≠4]) + max(c[3][j≠4]) + max(c[4][j≠4])
        = 6 + 7 + 9 + 8 = 30 > 0 → đi tiếp
  worker=2, thử job=1: next=6+7=13
    bound = 13 + max(c[3][j≠1,4]) + max(c[4][j≠1,4])
          = 13 + 9 + 8 = 30 → tiếp → bestVal=30 ✓
```

> **So sánh TRR1_4008 vs TRR1_4009:**
>
> | | TRR1_4008 (min) | TRR1_4009 (max) |
> |---|---|---|
> | Cận | Cận **dưới** (min mỗi hàng) | Cận **trên** (max mỗi hàng) |
> | Cắt khi | `lowerBound ≥ bestCost` | `upperBound ≤ bestVal` |
> | Khởi tạo | `bestCost = LLONG_MAX` | `bestVal = 0` |