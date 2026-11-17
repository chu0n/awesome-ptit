# TRR1_2014 — Xâu nhị phân 04

> **Loại:** Tổ hợp đếm · **Thuật toán:** Quy hoạch động phần bù · **Thời gian:** O(nk) · **Bộ nhớ:** O(nk)

## Đề bài
> **TRR1_2014 - Xâu nhị phân 04**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_2014

**Mô tả:** Cho trước hai số nguyên dương `n` và `k`.

**Yêu cầu:** Tìm số lượng `t` các xâu nhị phân độ dài `n` có chứa `k` chữ số 1 liên tiếp.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa hai số nguyên dương `n, k` không vượt quá 50 và `2 ≤ k ≤ n` — thời gian chạy ≤ 1 giây, bộ nhớ 64 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có t = 3 xâu nhị phân độ dài 3 thỏa mãn gồm 011, 110 và 111.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Đếm trực tiếp dễ trùng khi xâu có nhiều đoạn `k` số 1.
- Dùng bù: `t = 2^n -` số xâu không chứa `k` số 1 liên tiếp.
- `dp[i][j]` là số xâu an toàn dài `i`, kết thúc đúng `j` số 1.
- Thêm `0` reset đuôi về 0; thêm `1` tăng `j`, chỉ giữ `j < k`.
- `2^n` tính bằng nhân đôi, không dùng `pow`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, k;
    cin >> n >> k;

    // dp[i][j] = số xâu độ dài i hợp lệ, kết thúc bằng đúng j chữ số 1 liên tiếp
    // "Hợp lệ" ở đây nghĩa là KHÔNG chứa k chữ số 1 liên tiếp → j chạy 0..k-1
    vector<vector<unsigned long long>> dp(n + 1, vector<unsigned long long>(k, 0));

    // Base case: xâu độ dài 1
    dp[1][0] = 1;  // Xâu "0" → kết thúc bằng 0 chữ số 1
    dp[1][1] = 1;  // k >= 2 nên trạng thái này luôn hợp lệ

    for (int i = 2; i <= n; i++) {
        // Thêm '0': chuỗi 1 liên tiếp reset về 0
        for (int j = 0; j < k; j++) {
            dp[i][0] += dp[i - 1][j];
        }

        // Thêm '1': kéo dài chuỗi 1 thêm 1 (chỉ hợp lệ khi j < k)
        for (int j = 1; j < k; j++) {
            dp[i][j] += dp[i - 1][j - 1];
        }
    }

    // Tổng xâu độ dài n KHÔNG chứa k chữ số 1 liên tiếp
    unsigned long long notContain = 0;
    for (int j = 0; j < k; j++) {
        notContain += dp[n][j];
    }

    // Bù: tổng xâu độ dài n CÓ chứa k chữ số 1 liên tiếp
    unsigned long long total = 1;  // 2^n
    for (int i = 0; i < n; i++) {
        total *= 2;
    }
    cout << total - notContain;

    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `dp[i][j]` | Số xâu an toàn dài `i`, đuôi đúng `j` số 1 |
| `notContain` | Tổng hàng `dp[n]` |
| `total` | `2^n` |
| `total - notContain` | Số xâu chứa `k` số 1 liên tiếp |

1. Đọc `n`, `k`; cấp bảng `(n+1) x k` toàn 0.
2. Cơ sở: `dp[1][0] = 1` cho xâu `0`; vì `k ≥ 2`, `dp[1][1] = 1` cho xâu `1`.
3. Với `i = 2..n`, thêm `0` dồn toàn hàng trước vào cột 0.
4. Thêm `1` chuyển `dp[i-1][j-1]` sang `dp[i][j]` với `j = 1..k-1`.
5. Tính `2^n`, trừ tổng hàng `dp[n]` rồi in.

**Chỗ dễ sai**
- Hiểu `dp[i][j]` là đã chứa `k` số 1; thực tế đây là phần bù.
- Cấp `k+1` cột và tính cả trạng thái vi phạm.
- Tính `2^n` bằng `pow` gây sai số.
- Dùng `int` cho `total` hoặc `notContain`.

**Độ phức tạp:** `O(nk)` thời gian, `O(nk)` bộ nhớ.

## Chạy tay

Với `n = 3`, `k = 2`:

| `i` | `dp[i][0]` | `dp[i][1]` | Tổng an toàn |
|---:|---:|---:|---:|
| 1 | 1 | 1 | 2 |
| 2 | 2 | 1 | 3 |
| 3 | 3 | 2 | 5 |
| Kết quả | | | `8 - 5 = 3` |

</details>

<details>
<summary><strong>Hướng 2 — Quy hoạch động một hàng và phần bù</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, k;
    cin >> n >> k;

    vector<unsigned long long> dp(k, 0);
    vector<unsigned long long> nextRow(k, 0);

    dp[0] = 1;
    dp[1] = 1;

    for (int length = 2; length <= n; length++) {
        fill(nextRow.begin(), nextRow.end(), 0);

        unsigned long long total = 0;
        for (int ones = 0; ones < k; ones++) {
            total += dp[ones];
        }
        nextRow[0] = total;

        for (int ones = 1; ones < k; ones++) {
            nextRow[ones] = dp[ones - 1];
        }

        dp.swap(nextRow);
    }

    unsigned long long safeStrings = 0;
    for (int ones = 0; ones < k; ones++) {
        safeStrings += dp[ones];
    }

    unsigned long long totalStrings = 1ULL << n;
    cout << totalStrings - safeStrings;
    return 0;
}
```

Giữ một hàng trạng thái an toàn rồi hoán đổi với hàng mới. Cùng kết quả, bộ nhớ `O(k)` thay vì `O(nk)`.

</details>
