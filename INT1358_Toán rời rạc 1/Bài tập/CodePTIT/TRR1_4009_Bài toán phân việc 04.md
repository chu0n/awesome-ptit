# TRR1_4009 — Bài toán phân việc 04

> **Loại:** Tối ưu hóa tổ hợp · **Thuật toán:** Nhánh cận (cận trên) · **Thời gian:** O(n!·n²) xấu nhất · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_4009 - Bài toán phân việc 04**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_4009

**Mô tả:** Có `n` nhân viên và `n` công việc. Nhân viên `i` hoàn thành công việc `j` với năng suất `c[i][j]`. Tìm phương án phân công sao cho tổng năng suất **lớn nhất** bằng phương pháp nhánh cận.

**Giới hạn:** `n ≤ 15` — thời gian chạy ≤ 1 giây.

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

Mỗi phương án là một hoán vị công việc: nhân viên `1` nhận một việc, nhân viên `2` nhận việc khác, ... Tổng năng suất là:

$$\sum_{i=1}^{n} \text{productivity}[i][\text{job}_i].$$

Quay lui giao việc cho từng nhân viên. Tại nhân viên `worker`, cận trên cho phần còn lại lấy **năng suất lớn nhất trong các việc chưa dùng** của từng hàng:

$$U = \text{currentValue} + \sum_{i=\text{worker}}^{n} \max_{j:\,\text{jobUsed}[j]=0} \text{productivity}[i][j].$$

Mỗi nhân viên thật sự phải nhận một việc chưa dùng, nên giá trị thật của người đó không vượt quá max của hàng. Phép ước lượng còn cho nhiều người cùng chọn một việc, nên `U` có thể cao hơn tổng thật nhưng không bao giờ thấp hơn. Vì vậy cắt khi `U <= bestValue` là an toàn.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <climits>
#include <iostream>
using namespace std;

int       n;
int       productivity[16][16];
int       currentJob[16];
int       bestJob[16];
bool      jobUsed[16];
long long bestValue = LLONG_MIN;

// Cận trên: mỗi nhân viên còn lại tạm chọn việc có năng suất lớn nhất.
long long estimateTotalValue(int firstWorker, long long currentValue)
{
    long long estimatedValue = currentValue;

    for (int worker = firstWorker; worker <= n; worker++) {
        int largestValue = 0;
        for (int job = 1; job <= n; job++) {
            if (!jobUsed[job] && productivity[worker][job] > largestValue) {
                largestValue = productivity[worker][job];
            }
        }
        estimatedValue += largestValue;
    }
    return estimatedValue;
}

// Chọn việc cho worker; currentValue là tổng năng suất đã có.
void assignJobs(int worker, long long currentValue)
{
    if (worker > n) {
        if (currentValue > bestValue) {
            bestValue = currentValue;
            for (int i = 1; i <= n; i++) {
                bestJob[i] = currentJob[i];
            }
        }
        return;
    }

    if (estimateTotalValue(worker, currentValue) <= bestValue) {
        return;  // Ngay cả ước lượng cao nhất cũng không cải thiện được.
    }

    for (int job = 1; job <= n; job++) {
        if (jobUsed[job]) {
            continue;
        }

        currentJob[worker] = job;
        jobUsed[job]       = true;

        long long nextValue = currentValue + productivity[worker][job];
        assignJobs(worker + 1, nextValue);

        jobUsed[job] = false;  // Trả việc để thử cách phân công khác.
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> productivity[i][j];
        }
    }

    assignJobs(1, 0);

    cout << bestValue << '\n';
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
| `worker` | Nhân viên đang được giao việc |
| `currentValue` | Tổng năng suất của các nhân viên trước |
| `jobUsed[j]` | Việc `j` đã có người nhận trong nhánh hiện tại |
| `currentJob[i]` | Việc đang gán cho nhân viên `i` |
| `bestValue` | Tổng năng suất lớn nhất đã tìm được |
| `bestJob` | Bản sao phương án ứng với `bestValue` |

**Luồng chạy**

1. Đọc `n` và ma trận `productivity`.
2. Đặt `bestValue = LLONG_MIN`, gọi `assignJobs(1, 0)`.
3. Nếu `worker > n`: so sánh `currentValue` với kỷ lục, cập nhật `bestValue` và `bestJob` nếu lớn hơn, rồi quay lui.
4. Tính cận trên; cắt nếu `<= bestValue`.
5. Thử từng việc chưa dùng: ghi `currentJob`, đánh dấu, đệ quy với tổng mới, rồi bỏ đánh dấu.
6. In `bestValue` và `bestJob[1..n]`.

**Cận trên**

```cpp
for (int worker = firstWorker; worker <= n; worker++) {
    int largestValue = 0;
    for (int job = 1; job <= n; job++) {
        if (!jobUsed[job] && productivity[worker][job] > largestValue) {
            largestValue = productivity[worker][job];
        }
    }
    estimatedValue += largestValue;
}
```

Vòng ngoài chỉ đi từ `firstWorker`, không phải từ 1: các nhân viên trước đã có việc và đã nằm trong `currentValue`. Mỗi hàng lấy max trên các cột chưa dùng. `largestValue = 0` đúng với năng suất không âm; nếu dữ liệu âm thì cận lỏng hơn nhưng vẫn là cận trên.

**Chốt đáp án tại lá**

```cpp
if (worker > n) {
    if (currentValue > bestValue) {
        bestValue = currentValue;
        for (int i = 1; i <= n; i++) {
            bestJob[i] = currentJob[i];
        }
    }
    return;
}
```

Khi `worker > n`, mọi nhân viên đã có việc và `currentValue` là tổng đầy đủ. Phải sao chép cả `currentJob` sang `bestJob`; `currentJob` sẽ bị thay đổi khi quay lui. Dấu `>` giữ phương án đến trước khi có nhiều phương án cùng tối ưu.

**Cắt nhánh và thử việc**

```cpp
if (estimateTotalValue(worker, currentValue) <= bestValue) {
    return;  // Ngay cả ước lượng cao nhất cũng không cải thiện được.
}
```

Nếu cận trên không vượt kỷ lục, mọi phương án hoàn chỉnh trong nhánh đều `<= bestValue`; không cần đi tiếp.

```cpp
for (int job = 1; job <= n; job++) {
    if (jobUsed[job]) {
        continue;
    }

    currentJob[worker] = job;
    jobUsed[job]       = true;

    long long nextValue = currentValue + productivity[worker][job];
    assignJobs(worker + 1, nextValue);

    jobUsed[job] = false;  // Trả việc để thử cách phân công khác.
}
```

Bốn thao tác đúng thứ tự: ghi việc, đánh dấu, đệ quy, hoàn tác. `currentValue` truyền theo giá trị nên không cần trừ lại sau đệ quy; chỉ `jobUsed` là biến toàn cục phải hoàn tác.

**Vì sao cắt đúng**

Mọi phương án hoàn chỉnh trong nhánh đều gán cho mỗi nhân viên còn lại một việc chưa dùng. Giá trị thật của nhân viên `i` không vượt quá max hàng `i` trên các việc chưa dùng, nên tổng thật không vượt quá `U`. Nếu `U <= bestValue`, nhánh không thể tạo tổng lớn hơn kỷ lục. Dấu `<=` cũng bỏ phương án đồng hạng, phù hợp vì chỉ cần một phương án tối ưu.

**Độ phức tạp:** xấu nhất `O(n!·n²)` thời gian, `O(n²)` bộ nhớ.

## Chạy tay

Với ma trận mẫu:

| Bước | Phương án đang thử | Phép tính | Tổng | Kỷ lục |
|---|---|---|---:|---:|
| 1 | `1 2 3 4` | `1+1+2+2` | 6 | 6 |
| 2 | `1 2 4 3` | `1+1+5+8` | 15 | 15 |
| 3 | `1 4 2 3` | `1+2+9+8` | 20 | 20 |
| 4 | `2 1 4 3` | `7+1+5+8` | 21 | 21 |
| 5 | `4 1 2 3` | `6+7+9+8` | 30 | 30 |

Đáp án cuối: `30` và dãy `4 1 2 3`.

## Bẫy và câu hỏi

- Khởi tạo `bestValue = 0`: nếu mọi tổng đều `<= 0`, không lá nào cập nhật và `bestJob` có thể chưa được gán.
- Dùng `estimateTotalValue(...) >= bestValue` để cắt: với bài tìm max, cận lớn hơn kỷ lục là bình thường, cắt như vậy sẽ mất nghiệm.
- Quên `jobUsed[job] = false` sau đệ quy: các nhánh sau mất việc.
- Dùng `int` cho `bestValue`: tổng `n` năng suất có thể tràn.
- In `currentJob` thay vì `bestJob`: đó là nhánh cuối, không chắc tối ưu.

**Câu hỏi dễ gặp**

- Vì sao cận trên được phép cho nhiều nhân viên chọn cùng một việc? Vì nới lỏng như vậy chỉ làm cận cao hơn, không bao giờ thấp hơn tổng thật.
- Vì sao dùng `<=` ở điều kiện cắt? Phương án bằng kỷ lục không cải thiện đáp án; chỉ cần một nghiệm.
- Vì sao kiểm tra lá trước khi tính cận? Để lá luôn có cơ hội cập nhật kỷ lục.
- Vì sao `largestValue` khởi tạo bằng 0? Với năng suất không âm, 0 không lớn hơn giá trị thật nào; nếu dữ liệu âm thì cận lỏng nhưng vẫn đúng.
- Có cách nào ổn định hơn khi `n = 15`? Dùng DP bitmask `O(n·2^n)`, đổi bộ nhớ `O(2^n)` lấy thời gian ổn định.

</details>

<details>
<summary><strong>Hướng 2 — Quy hoạch động bitmask</strong></summary>

```cpp
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n;
    cin >> n;
    int productivity[16][16];
    for (int worker = 1; worker <= n; worker++) {
        for (int job = 1; job <= n; job++) {
            cin >> productivity[worker][job];
        }
    }

    int         maskCount = 1 << n;
    vector<int> workers(maskCount, 0);
    for (int mask = 1; mask < maskCount; mask++) {
        workers[mask] = workers[mask >> 1] + (mask & 1);
    }

    const long long   NEGATIVE_INFINITY = LLONG_MIN / 4;
    vector<long long> dp(maskCount, NEGATIVE_INFINITY);
    vector<int>       previousMask(maskCount, -1);
    vector<int>       chosenJob(maskCount, 0);
    dp[0] = 0;

    for (int mask = 0; mask < maskCount; mask++) {
        if (dp[mask] == NEGATIVE_INFINITY) {
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

            int       nextMask  = mask | bit;
            long long nextValue = dp[mask] + productivity[worker + 1][job];
            if (nextValue > dp[nextMask]) {
                dp[nextMask]           = nextValue;
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

`mask` là tập công việc đã giao; `workers[mask]` là số nhân viên đã nhận việc, nên nhân viên tiếp theo là `workers[mask] + 1`. `dp[mask]` là tổng năng suất lớn nhất đạt được với tập việc đó. Mỗi lần thêm một việc chưa có trong `mask` cho nhân viên `workers[mask] + 1`; `previousMask` và `chosenJob` dùng để truy ngược. Cách này chạy `O(n·2^n)` thời gian và `O(2^n)` bộ nhớ, ổn định hơn nhánh cận nhưng tốn bộ nhớ hơn.

</details>
