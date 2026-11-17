# TRR1_4004 — Bài toán phân việc 02

> **Loại:** Tối ưu tổ hợp · **Thuật toán:** Quay lui duyệt toàn bộ hoán vị, lấy tổng lớn nhất · **Thời gian:** O(n·n!) · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_4004 - Bài toán phân việc 02**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_4004

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với năng suất `c[i][j]`. Tìm phương án phân công mỗi nhân viên một việc sao cho tổng năng suất **lớn nhất**.

**Giới hạn:** `n ≤ 10` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4
1 1 3 6
7 1 3 2
1 9 2 5
3 3 8 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>30
4 1 2 3</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Giao cho công nhân thứ 1, 2, 3 và 4 thực hiện các công việc tương ứng là 4, 1, 2 và 3 với tổng năng suất hoàn thành lớn nhất là 30.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Mỗi phương án là một hoán vị công việc `(j_1, ..., j_n)`, tổng năng suất `sum c[i][j_i]`; có `n!` phương án.
- `used[j]` chặn giao trùng việc; `assign_[i]` ghi việc đang thử.
- Không cắt nhánh: tổng hiện tại nhỏ chưa nói được tổng cuối, các việc sau có thể rất lớn.
- Cập nhật khi `curVal > bestVal`; `bestVal` khởi tạo `LLONG_MIN` để nhận cả tổng âm hoặc bằng 0.
- Lưu `bestAssign` vì `assign_` bị thay đổi khi quay lui.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <climits>
#include <iostream>
using namespace std;

int       n, c[11][11];
int       assign_[11], bestAssign[11];  // assign_[i] = công việc giao cho nhân viên i
bool      used[11];                     // used[j] = true nếu công việc j đã được giao
long long bestVal = LLONG_MIN;

void backtrack(int worker, long long curVal)
{
    if (worker > n) {
        // Cập nhật nghiệm nếu tổng năng suất lớn hơn hiện tại
        if (curVal > bestVal) {
            bestVal = curVal;
            for (int i = 1; i <= n; i++) {
                bestAssign[i] = assign_[i];
            }
        }
        return;
    }
    for (int job = 1; job <= n; job++) {
        if (used[job]) {
            continue;  // Công việc đã được giao → bỏ qua
        }
        used[job]       = true;
        assign_[worker] = job;
        backtrack(worker + 1, curVal + c[worker][job]);
        used[job] = false;  // Quay lui: thử công việc khác
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> c[i][j];
        }
    }

    backtrack(1, 0);

    cout << bestVal << '\n';
    for (int i = 1; i <= n; i++) {
        cout << bestAssign[i] << " \n"[i == n];
    }
    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `c[i][j]` | Năng suất khi nhân viên `i` làm việc `j` |
| `assign_[i]` | Việc đang chọn cho nhân viên `i` |
| `used[j]` | Việc `j` đã bị giao trong nhánh hiện tại |
| `bestVal`, `bestAssign` | Tổng lớn nhất và phương án tương ứng |

1. Đọc `n` và ma trận `c`.
2. Gọi `backtrack(1, 0)`.
3. Với mỗi việc chưa dùng: đánh dấu, gán `assign_`, gọi đệ quy với `curVal + c[worker][job]`, rồi hoàn tác.
4. Khi `worker > n`, cập nhật `bestVal`, `bestAssign` nếu tổng lớn hơn.
5. In `bestVal` và dãy `bestAssign`.

**Chỗ dễ sai**
- Quên `used[job] = false` sau đệ quy: nhánh sau thiếu việc.
- Đánh dấu `used[job]` sau lời gọi con: lời gọi con dùng lại việc.
- Cắt nhánh theo `curVal` mà không có cận trên: bỏ mất phương án tốt hơn.
- Khởi tạo `bestVal = 0`: sai nếu mọi năng suất âm.
- In `assign_` thay vì `bestAssign`: mảng đã bị thay đổi.

**Độ phức tạp:** `O(n·n!)` thời gian, `O(n²)` bộ nhớ.

## Chạy tay

Phương án `4 1 2 3` trên đề:

| Nhân viên | Việc | `c` | Tổng |
|---:|---:|---:|---:|
| 1 | 4 | 6 | 6 |
| 2 | 1 | 7 | 13 |
| 3 | 2 | 9 | 22 |
| 4 | 3 | 8 | 30 |

</details>

<details>
<summary><strong>Hướng 2 — duyệt mọi hoán vị bằng std::next_permutation</strong></summary>

```cpp
#include <algorithm>
#include <climits>
#include <iostream>
#include <numeric>
#include <vector>
using namespace std;

int main()
{
    int n;
    cin >> n;
    int productivity[11][11];
    for (int worker = 1; worker <= n; worker++) {
        for (int job = 1; job <= n; job++) {
            cin >> productivity[worker][job];
        }
    }

    vector<int> jobs(n);
    iota(jobs.begin(), jobs.end(), 1);

    long long   bestValue = LLONG_MIN;
    vector<int> bestJobs;
    do {
        long long totalValue = 0;
        for (int worker = 0; worker < n; worker++) {
            totalValue += productivity[worker + 1][jobs[worker]];
        }

        if (totalValue > bestValue) {
            bestValue = totalValue;
            bestJobs  = jobs;
        }
    } while (next_permutation(jobs.begin(), jobs.end()));

    cout << bestValue << '\n';
    for (int i = 0; i < n; i++) {
        cout << bestJobs[i] << " \n"[i == n - 1];
    }
    return 0;
}
```

Duyệt đủ `n!` hoán vị bằng thư viện, code ngắn hơn và không cần đệ quy; đổi lại luôn xét toàn bộ phương án.

</details>
