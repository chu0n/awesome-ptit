# TRR1_4008 — Bài toán phân việc 03

> **Loại:** Tối ưu hóa tổ hợp · **Thuật toán:** Nhánh cận · **Thời gian:** O(n!·n²) xấu nhất · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_4008 - Bài toán phân việc 03**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_4008

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với chi phí `c[i][j]`. Tìm phương án phân công sao cho tổng thời gian **nhỏ nhất** bằng phương pháp nhánh cận.

**Giới hạn:** `n ≤ 15` — thời gian chạy ≤ 1 giây.

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

- Mỗi phương án là một hoán vị công việc: người `1` nhận một việc, người `2` nhận một việc khác, ...
- Dùng quay lui: `assignJobs(worker, currentCost)` đã giao việc cho người `1..worker-1`.
- Cận dưới: mỗi người còn lại tạm lấy việc rẻ nhất trong các cột chưa dùng. Cách này cho phép hai người cùng chọn một việc, nên ước lượng luôn thấp hơn hoặc bằng chi phí thật.
- Nếu `currentCost >= bestCost`, hoặc cận dưới `>= bestCost`, bỏ nhánh vì không thể cải thiện đáp án.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <climits>
#include <iostream>
using namespace std;

int       n;
int       cost[16][16];
int       currentJob[16];  // currentJob[i] = công việc đang giao cho nhân viên i
int       bestJob[16];
bool      jobUsed[16];  // jobUsed[j] = true nếu công việc j đã được giao
long long bestCost = LLONG_MAX;

// Cận dưới: cộng chi phí rẻ nhất của từng người chưa được giao việc.
// Chỉ xét việc chưa dùng, nhưng tạm cho phép nhiều người cùng chọn một việc.
long long estimateTotalCost(int firstWorker, long long currentCost)
{
    long long estimatedCost = currentCost;

    for (int worker = firstWorker; worker <= n; worker++) {
        int cheapestCost = INT_MAX;
        for (int job = 1; job <= n; job++) {
            if (!jobUsed[job] && cost[worker][job] < cheapestCost) {
                cheapestCost = cost[worker][job];
            }
        }
        // Số người đã giao bằng số việc đã dùng, nên người còn lại luôn có việc trống.
        estimatedCost += cheapestCost;
    }

    return estimatedCost;
}

// Đã giao việc cho người 1..worker-1; bây giờ chọn việc cho người worker.
void assignJobs(int worker, long long currentCost)
{
    if (currentCost >= bestCost) {
        return;  // Chi phí không âm, giao thêm việc cũng không thể rẻ hơn.
    }

    if (worker > n) {
        bestCost = currentCost;
        for (int i = 1; i <= n; i++) {
            bestJob[i] = currentJob[i];
        }
        return;
    }

    if (estimateTotalCost(worker, currentCost) >= bestCost) {
        return;  // Ngay cả ước lượng rẻ nhất cũng không cải thiện được đáp án.
    }

    for (int job = 1; job <= n; job++) {
        if (jobUsed[job]) {
            continue;
        }

        currentJob[worker] = job;
        jobUsed[job]       = true;

        long long nextCost = currentCost + cost[worker][job];
        assignJobs(worker + 1, nextCost);

        jobUsed[job] = false;  // Quay lui: trả công việc để thử phương án khác.
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> cost[i][j];
        }
    }

    assignJobs(1, 0);

    cout << bestCost << '\n';
    for (int i = 1; i <= n; i++) {
        cout << bestJob[i] << " \n"[i == n];
    }
    return 0;
}
```

### Giải thích code

**Trạng thái chính**

| Biến | Ý nghĩa |
|---|---|
| `worker` | Người đang được giao việc |
| `currentCost` | Tổng chi phí của những người trước |
| `jobUsed[j]` | Việc `j` đã bị người khác nhận |
| `currentJob[i]` | Việc đang gán cho người `i` |
| `bestCost`, `bestJob` | Đáp án tốt nhất đã tìm được |

**Luồng chạy**

1. Nếu `currentCost >= bestCost`, dừng nhánh.
2. Nếu đã giao đủ `n` người, cập nhật `bestCost` và sao chép `currentJob` sang `bestJob`.
3. Tính cận dưới; nếu cận `>= bestCost`, dừng nhánh.
4. Thử từng việc chưa dùng: gán, đánh dấu, gọi đệ quy, rồi bỏ đánh dấu.

**Cận dưới**

Với mỗi người `i` từ `worker` đến `n`, lấy giá nhỏ nhất trong các cột chưa dùng:

$$\text{estimated} = \text{currentCost} + \sum_{i=\text{worker}}^{n} \min_{j:\,\text{jobUsed}[j]=0} cost[i][j].$$

Ước lượng này lạc quan vì nhiều người có thể cùng lấy một việc. Chi phí thật không thể nhỏ hơn nó, nên cắt nhánh vẫn an toàn.

**Vì sao dùng `>=`?** Bài chỉ cần một phương án tối ưu. Phương án bằng kỷ lục không cải thiện đáp án, nên cắt được.

**Độ phức tạp:** xấu nhất `O(n!·n²)` thời gian, `O(n²)` bộ nhớ. Cận dưới chỉ giảm nhánh thực tế, không đổi độ phức tạp xấu nhất.

## Chạy tay

Ví dụ phụ 2 người:

| Người | Việc 1 | Việc 2 |
|---|---:|---:|
| 1 | 4 | 1 |
| 2 | 2 | 3 |

| Bước | Lựa chọn | `currentCost` | Việc dùng | Kết quả |
|---|---|---:|---|---|
| 1 | Người 1 việc 1 | 4 | `{1}` | Đi tiếp |
| 2 | Người 2 việc 2 | 7 | `{1,2}` | Lá, `bestCost = 7` |
| 3 | Quay lại, người 1 việc 2 | 1 | `{2}` | Đi tiếp |
| 4 | Người 2 việc 1 | 3 | `{1,2}` | Lá, `bestCost = 3` |

Kết quả: `3` và dãy `2 1`.

## Bẫy và câu hỏi

- Quên `jobUsed[j] = false` sau đệ quy: nhánh sau mất việc.
- Đánh dấu trước khi gán hoặc gán sau khi gọi đệ quy: lời gọi con thấy trạng thái sai.
- Khởi tạo `bestCost = 0` khi tìm min: không phương án dương nào cập nhật được.
- In `currentJob` thay vì `bestJob`: đó là nhánh cuối, không chắc tối ưu.

**Câu hỏi dễ gặp**
- Vì sao cận dưới đúng dù cho phép dùng lại việc? Vì mỗi người vẫn phải chọn một việc; chi phí thật không nhỏ hơn giá rẻ nhất của hàng đó.
- Vì sao không dùng `>` ở điều kiện cắt? `>=` bỏ được nhánh chỉ bằng kỷ lục; bài chỉ cần một nghiệm.
- Nếu cần liệt kê mọi phương án tối ưu? Đổi điều kiện cắt và lưu danh sách thay vì một `bestJob`.

</details>

<details>
<summary><strong>Hướng 2 — DP bitmask</strong></summary>

```cpp
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n;
    cin >> n;
    int cost[16][16];
    for (int worker = 1; worker <= n; worker++) {
        for (int job = 1; job <= n; job++) {
            cin >> cost[worker][job];
        }
    }

    int         maskCount = 1 << n;
    vector<int> workers(maskCount, 0);
    for (int mask = 1; mask < maskCount; mask++) {
        workers[mask] = workers[mask >> 1] + (mask & 1);
    }

    const long long   INF = LLONG_MAX / 4;
    vector<long long> dp(maskCount, INF);
    vector<int>       previousMask(maskCount, -1);
    vector<int>       chosenJob(maskCount, 0);
    dp[0] = 0;

    for (int mask = 0; mask < maskCount; mask++) {
        if (dp[mask] == INF) {
            continue;
        }
        int worker = workers[mask];
        if (worker == n) {
            continue;
        }

        for (int job = 1; job <= n; job++) {
            int bit = 1 << (job - 1);
            if ((mask & bit) != 0) {
                continue;
            }

            int       nextMask = mask | bit;
            long long nextCost = dp[mask] + cost[worker + 1][job];
            if (nextCost < dp[nextMask]) {
                dp[nextMask]           = nextCost;
                previousMask[nextMask] = mask;
                chosenJob[nextMask]    = job;
            }
        }
    }

    int         fullMask = maskCount - 1;
    vector<int> bestJob(n + 1, 0);
    for (int mask = fullMask; mask != 0; mask = previousMask[mask]) {
        int worker      = workers[mask];
        bestJob[worker] = chosenJob[mask];
    }

    cout << dp[fullMask] << '\n';
    for (int worker = 1; worker <= n; worker++) {
        cout << bestJob[worker] << " \n"[worker == n];
    }
    return 0;
}
```

`mask` là tập công việc đã giao; `workers[mask]` là số người đã nhận việc. `dp[mask]` là chi phí nhỏ nhất để đạt trạng thái đó. Mỗi lần thêm một việc chưa có trong `mask`, gán cho người `workers[mask] + 1`. Mảng `previousMask` và `chosenJob` dùng để truy ngược phương án.

</details>
