# TRR1_4003 — Bài toán phân việc 01

> **Loại:** Tối ưu tổ hợp · **Thuật toán:** Quay lui duyệt hoán vị, cắt nhánh theo chi phí tích lũy · **Thời gian:** O(n·n!) xấu nhất · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_4003 - Bài toán phân việc 01**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_4003

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với chi phí `c[i][j]`. Tìm phương án phân công mỗi nhân viên một việc sao cho tổng thời gian nhỏ nhất.

**Giới hạn:** `n ≤ 10` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4
8 1 8 9
7 4 9 2
1 9 5 8
7 8 2 9</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5
2 4 3 1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Giao cho công nhân thứ 1, 2, 3 và 4 thực hiện các công việc tương ứng là 2, 4, 1 và 3 với tổng thời gian hoàn thành nhỏ nhất là 5.</td>
    </tr>
  </tbody>
</table>

> **Mẫu trên trang đề có mâu thuẫn:** Bảng được chép theo ảnh trang đề. Với ma trận này, phân công `2 4 1 3` có chi phí `1 + 2 + 1 + 2 = 6` và là tối ưu. Dãy `2 4 3 1` trong Output có chi phí 15, không phải 5. Giữ nguyên mẫu công bố; code bên dưới tính theo ma trận và cho kết quả tối ưu 6.

## Ý tưởng

- Mỗi phương án là một hoán vị công việc `(j_1, ..., j_n)`, chi phí `sum c[i][j_i]`; có `n!` phương án.
- `assign[i]` ghi việc đang thử cho nhân viên `i`, `used[j]` chặn giao trùng việc.
- Cắt nhánh khi `curCost + c[worker][job] >= bestCost`: chi phí không âm nên nhánh không thể tốt hơn.
- Cập nhật `bestCost` và sao chép `assign` sang `bestAssign` khi đã giao đủ `n` việc.
- `bestCost` khởi tạo `LLONG_MAX` để nhận nghiệm hợp lệ đầu tiên.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <climits>
#include <iostream>
using namespace std;

int       n, c[11][11];
int       assign[11], bestAssign[11];  // assign[i] = công việc giao cho nhân viên i
bool      used[11];                    // used[j] = true nếu công việc j đã được giao
long long bestCost = LLONG_MAX;

void backtrack(int worker, long long curCost)
{
    // Đã phân công xong n nhân viên → cập nhật nghiệm tối ưu
    if (worker > n) {
        if (curCost < bestCost) {
            bestCost = curCost;
            for (int i = 1; i <= n; i++) {
                bestAssign[i] = assign[i];
            }
        }
        return;
    }
    for (int job = 1; job <= n; job++) {
        if (used[job]) {
            continue;  // Công việc đã được giao → bỏ qua
        }
        long long next = curCost + c[worker][job];
        if (next >= bestCost) {
            continue;  // Cắt nhánh: không thể cải thiện
        }

        used[job]      = true;
        assign[worker] = job;
        backtrack(worker + 1, next);
        used[job] = false;  // Quay lui: hoàn tác để thử công việc khác
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

    cout << bestCost << '\n';
    for (int i = 1; i <= n; i++) {
        cout << bestAssign[i] << " \n"[i == n];
    }
    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `c[i][j]` | Chi phí giao việc `j` cho nhân viên `i` |
| `assign[i]` | Việc đang chọn cho nhân viên `i` |
| `used[j]` | Việc `j` đã bị giao trong nhánh hiện tại |
| `bestCost`, `bestAssign` | Chi phí nhỏ nhất và phương án tương ứng |

1. Đọc `n` và ma trận `c`.
2. Gọi `backtrack(1, 0)`.
3. Với mỗi việc chưa dùng, tính `next = curCost + c[worker][job]`.
4. Bỏ nhánh nếu `next >= bestCost`; ngược lại đánh dấu, gán `assign`, gọi đệ quy rồi hoàn tác.
5. Khi `worker > n`, cập nhật `bestCost`, `bestAssign`; in kết quả sau khi duyệt.

**Chỗ dễ sai**
- Quên `used[job] = false` sau đệ quy: các nhánh sau thiếu việc.
- Đánh dấu `used[job]` sau lời gọi con: lời gọi con dùng lại việc.
- Khởi tạo `bestCost = 0` khi tìm min: không nghiệm dương nào cập nhật được.
- In `assign` thay vì `bestAssign`: mảng đã bị thay đổi sau khi quay lui.
- Tin Output mẫu 5 thay vì cộng ma trận: code đúng theo ma trận cho 6.

**Độ phức tạp:** `O(n·n!)` thời gian xấu nhất, `O(n²)` bộ nhớ.

## Chạy tay

Phương án `2 4 1 3` trên ma trận của đề:

| Nhân viên | Việc | `c` | Tổng |
|---:|---:|---:|---:|
| 1 | 2 | 1 | 1 |
| 2 | 4 | 2 | 3 |
| 3 | 1 | 1 | 4 |
| 4 | 3 | 2 | 6 |

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
    int cost[11][11];
    for (int worker = 1; worker <= n; worker++) {
        for (int job = 1; job <= n; job++) {
            cin >> cost[worker][job];
        }
    }

    vector<int> jobs(n);
    iota(jobs.begin(), jobs.end(), 1);

    long long   bestCost = LLONG_MAX;
    vector<int> bestJobs;
    do {
        long long totalCost = 0;
        for (int worker = 0; worker < n; worker++) {
            totalCost += cost[worker + 1][jobs[worker]];
        }

        if (totalCost < bestCost) {
            bestCost = totalCost;
            bestJobs = jobs;
        }
    } while (next_permutation(jobs.begin(), jobs.end()));

    cout << bestCost << '\n';
    for (int i = 0; i < n; i++) {
        cout << bestJobs[i] << " \n"[i == n - 1];
    }
    return 0;
}
```

Sinh đủ `n!` hoán vị bằng thư viện nên code ngắn hơn, nhưng không cắt nhánh; với `n ≤ 10` vẫn đủ nhanh.

</details>
