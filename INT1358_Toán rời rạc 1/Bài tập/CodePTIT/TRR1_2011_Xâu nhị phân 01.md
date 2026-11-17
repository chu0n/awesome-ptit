# TRR1_2011 — Xâu nhị phân 01

> **Loại:** Tổ hợp đếm · **Thuật toán:** Quy hoạch động theo đuôi số 0 · **Thời gian:** O(nk) · **Bộ nhớ:** O(nk)

## Đề bài
> **TRR1_2011 - Xâu nhị phân 01**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_2011

**Mô tả:** Cho trước hai số nguyên dương `n` và `k`.

**Yêu cầu:** Tìm số lượng `t` các xâu nhị phân độ dài `n` không chứa `k` chữ số 0 liên tiếp.

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
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có t = 5 xâu nhị phân độ dài 3 thỏa mãn gồm 010, 011, 101, 110 và 111.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- `dp[i][j]` là số xâu dài `i`, kết thúc đúng `j` số 0, với `0 <= j < k`.
- Thêm `1`: mọi đuôi reset, `dp[i][0] = Σdp[i-1][j]`.
- Thêm `0`: `dp[i][j] = dp[i-1][j-1]` với `1 <= j < k`.
- Đáp án là `Σdp[n][j]`.
- Không lưu `j = k` vì trạng thái đó đã vi phạm.

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

    // dp[i][j] = số xâu nhị phân độ dài i, kết thúc bằng đúng j số 0 liên tiếp
    // j chạy từ 0 đến k-1 (nếu j == k thì vi phạm → không tính)
    // Dùng unsigned long long vì n có thể đến 50, số lượng xâu rất lớn
    vector<vector<unsigned long long>> dp(n + 1, vector<unsigned long long>(k, 0));

    // Base case: xâu độ dài 1
    dp[1][0] = 1;  // Xâu "1" → kết thúc bằng 0 chữ số 0
    dp[1][1] = 1;  // k >= 2 nên trạng thái này luôn hợp lệ

    for (int i = 2; i <= n; i++) {
        // Thêm chữ số '1' vào cuối: chuỗi 0 liên tiếp bị reset về 0
        for (int j = 0; j < k; j++) {
            dp[i][0] += dp[i - 1][j];
        }

        // Thêm chữ số '0' vào cuối: chuỗi 0 liên tiếp tăng thêm 1
        // dp[i][j] += dp[i-1][j-1] với j từ 1 đến k-1
        for (int j = 1; j < k; j++) {
            dp[i][j] += dp[i - 1][j - 1];  // Kéo dài chuỗi 0 thêm 1 (chưa đạt k)
        }
    }

    // Tổng tất cả xâu độ dài n hợp lệ (kết thúc bằng 0..k-1 số 0 liên tiếp)
    unsigned long long t = 0;
    for (int j = 0; j < k; j++) {
        t += dp[n][j];
    }

    cout << t;
    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `dp[i][j]` | Số xâu dài `i`, kết thúc đúng `j` số 0 |
| `i` | Độ dài xâu |
| `j` | Độ dài đuôi số 0, `0 <= j < k` |
| `t` | Tổng hàng `dp[n]` |

1. Đọc `n`, `k`; cấp bảng `(n+1) x k` toàn 0.
2. Cơ sở: `dp[1][0] = 1` cho xâu `1`; vì `k ≥ 2`, `dp[1][1] = 1` cho xâu `0`.
3. Với `i = 2..n`, cộng toàn hàng trước vào `dp[i][0]`.
4. Với `j = 1..k-1`, gán `dp[i][j] += dp[i-1][j-1]`.
5. Cộng `dp[n][0..k-1]` rồi in.

**Chỗ dễ sai**
- Dùng `int` làm tràn khi `n = 50`.
- Cấp `k+1` cột và cộng cả trạng thái `j = k`.
- Chỉ lấy `dp[n][0]` làm mất xâu kết thúc bằng 0.
- Dùng `=` thay vì `+=` khi tính cột 0.

**Độ phức tạp:** `O(nk)` thời gian, `O(nk)` bộ nhớ.

## Chạy tay

Với `n = 3`, `k = 2`:

| `i` | `dp[i][0]` | `dp[i][1]` | Tổng |
|---:|---:|---:|---:|
| 1 | 1 | 1 | 2 |
| 2 | 2 | 1 | 3 |
| 3 | 3 | 2 | 5 |

</details>

<details>
<summary><strong>Hướng 2 — Quy hoạch động một hàng</strong></summary>

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
        for (int zeros = 0; zeros < k; zeros++) {
            total += dp[zeros];
        }
        nextRow[0] = total;

        for (int zeros = 1; zeros < k; zeros++) {
            nextRow[zeros] = dp[zeros - 1];
        }

        dp.swap(nextRow);
    }

    unsigned long long answer = 0;
    for (int zeros = 0; zeros < k; zeros++) {
        answer += dp[zeros];
    }

    cout << answer;
    return 0;
}
```

Chỉ giữ hàng hiện tại và hàng kế tiếp rồi hoán đổi. Cùng công thức, giảm bộ nhớ từ `O(nk)` xuống `O(k)`.

</details>
