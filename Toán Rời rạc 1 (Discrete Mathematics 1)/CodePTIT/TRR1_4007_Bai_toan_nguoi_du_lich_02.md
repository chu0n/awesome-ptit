## Đề bài

> **TRR1_4007 - Bài toán người du lịch 02**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_4007

**Mô tả:** Xuất phát tại thành phố 1, đi qua tất cả `n` thành phố mỗi nơi đúng một lần rồi quay về. Chi phí từ `i` đến `j` là `c[i][j]`. Tìm hành trình có tổng chi phí nhỏ nhất.

**Giới hạn:** `n ≤ 30`, `c[i][j] ≤ 10⁶` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `4` / ma trận 4×4 | `5` / `1 2 4 3` |

> **Giải thích Test 1:** Hành trình `1→2→4→3→1` có tổng chi phí = 1+2+2+... = 5 *(lưu ý: đáp án mẫu trước TRR1_4007 ghi 6, bài này ghi 5 — kiểm tra lại ma trận)*.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Bài toán người du lịch 02 |
| **Mã bài** | TRR1_4007 |
| **Loại bài** | Toán rời rạc — Tối ưu hóa tổ hợp |
| **Mục đích** | Tìm hành trình TSP chi phí nhỏ nhất bằng phương pháp nhánh cận |
| **Kiến thức** | Branch and Bound, TSP, cận dưới |
| **Thuật toán** | Backtracking + cận dưới: tại mỗi bước cộng thêm min chi phí hàng của các thành phố chưa thăm để ước lượng chi phí còn lại, cắt nhánh nếu vượt `bestCost` |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, c[31][31];
int path[31], bestPath[31];
bool visited[31];
long long bestCost;

// Tính cận dưới: với mỗi thành phố chưa thăm (+ thành phố đích 1),
// cộng thêm cạnh ra nhỏ nhất của nó → ước lượng lạc quan chi phí còn lại
long long lowerBound(int step, long long curCost) {
    long long bound = curCost;
    for (int i = 1; i <= n; i++) {
        if (visited[i]) continue;   // Chỉ xét thành phố chưa thăm
        long long minEdge = LLONG_MAX;
        for (int j = 1; j <= n; j++) {
            // Cạnh ra từ i đến j hợp lệ: j chưa thăm hoặc j=1 (quay về)
            if (i != j && (!visited[j] || j == 1))
                minEdge = min(minEdge, (long long)c[i][j]);
        }
        if (minEdge != LLONG_MAX) bound += minEdge;
    }
    return bound;
}

void backtrack(int step, long long curCost) {
    if (step == n) {
        // Đã thăm hết n thành phố → cộng chi phí quay về thành phố 1
        long long total = curCost + c[path[n]][1];
        if (total < bestCost) {
            bestCost = total;
            for (int i = 1; i <= n; i++) bestPath[i] = path[i];
        }
        return;
    }
    for (int city = 2; city <= n; city++) {
        if (visited[city]) continue;
        long long next = curCost + c[path[step]][city];
        if (next >= bestCost) continue;     // Cắt nhánh đơn giản

        // Kiểm tra cận dưới trước khi đi sâu
        visited[city] = true;
        path[step + 1] = city;
        if (lowerBound(step + 1, next) < bestCost)
            backtrack(step + 1, next);
        visited[city] = false;
    }
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) cin >> c[i][j];

    // Khởi tạo: xuất phát từ thành phố 1
    bestCost = LLONG_MAX;
    fill(visited + 1, visited + n + 1, false);
    visited[1] = true;
    path[1] = 1;

    backtrack(1, 0);

    cout << bestCost << '\n';
    for (int i = 1; i <= n; i++) cout << bestPath[i] << " \n"[i==n];
    return 0;
}
```

---

**Giải thích thuật toán:**

**Nhánh cận (Branch & Bound)** = Backtracking + cận dưới để cắt nhánh sớm hơn:

**Cận dưới** tại mỗi trạng thái: với mỗi thành phố chưa thăm, lấy **cạnh ra nhỏ nhất** có thể → tổng là ước lượng **lạc quan** (thấp hơn hoặc bằng chi phí thực). Nếu cận dưới đã ≥ `bestCost` thì chắc chắn không cải thiện được → cắt.

```
Hành trình 1→2→4→3→1:
  1→2: c[1][2]=1, bound còn lại ≥ min(2→?)+ min(4→?)+ min(3→?)
  ...cắt các nhánh có bound ≥ bestCost
→ bestCost=5, bestPath=[1,2,4,3] ✓
```

> **So sánh với LT004 (duyệt toàn thể):** Cùng bài TSP nhưng LT004 chỉ cắt nhánh đơn giản (`curCost ≥ bestCost`), còn TRR1_4007 dùng **cận dưới** chặt hơn — quan trọng khi `n` lớn đến 30.