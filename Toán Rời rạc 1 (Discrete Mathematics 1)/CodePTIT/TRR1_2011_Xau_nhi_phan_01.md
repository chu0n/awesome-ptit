## Đề bài
> **TRR1_2011 - Xâu nhị phân 01**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_2011

**Mô tả:** Cho trước hai số nguyên dương `n` và `k`.

**Yêu cầu:** Tìm số lượng `t` các xâu nhị phân độ dài `n` không chứa `k` chữ số 0 liên tiếp.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa hai số nguyên dương `n, k` không vượt quá 50 và `2 ≤ k ≤ n` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

| | Input | Output |
|---|---|---|
| Test 1 | `3 2` | `5` |

> **Giải thích Test 1:** Có `t = 5` xâu nhị phân độ dài 3 thỏa mãn gồm `010, 011, 101, 110` và `111`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Xâu nhị phân 01 |
| **Mã bài** | TRR1_2011 |
| **Loại bài** | Toán rời rạc — Tổ hợp đếm |
| **Mục đích** | Đếm xâu nhị phân độ dài n không chứa k chữ số 0 liên tiếp |
| **Kiến thức** | Quy hoạch động, bài toán đếm xâu có ràng buộc |
| **Thuật toán** | DP với `dp[i][j]` = số xâu độ dài i kết thúc bằng đúng j chữ số 0 liên tiếp (j < k) |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, k;
    cin >> n >> k;

    // dp[i][j] = số xâu nhị phân độ dài i, kết thúc bằng đúng j số 0 liên tiếp
    // j chạy từ 0 đến k-1 (nếu j == k thì vi phạm → không tính)
    // Dùng unsigned long long vì n có thể đến 50, số lượng xâu rất lớn
    vector<vector<unsigned long long>> dp(n + 1, vector<unsigned long long>(k, 0));

    // Base case: xâu độ dài 1
    dp[1][0] = 1;   // Xâu "1" → kết thúc bằng 0 chữ số 0
    if (k > 1) dp[1][1] = 1;   // Xâu "0" → kết thúc bằng 1 chữ số 0 (hợp lệ vì k>=2)

    for (int i = 2; i <= n; i++) {
        // Thêm chữ số '1' vào cuối: chuỗi 0 liên tiếp bị reset về 0
        for (int j = 0; j < k; j++)
            dp[i][0] += dp[i-1][j];

        // Thêm chữ số '0' vào cuối: chuỗi 0 liên tiếp tăng thêm 1
        // dp[i][j] += dp[i-1][j-1] với j từ 1 đến k-1
        for (int j = 1; j < k; j++)
            dp[i][j] += dp[i-1][j-1];  // Kéo dài chuỗi 0 thêm 1 (chưa đạt k)
    }

    // Tổng tất cả xâu độ dài n hợp lệ (kết thúc bằng 0..k-1 số 0 liên tiếp)
    unsigned long long t = 0;
    for (int j = 0; j < k; j++) t += dp[n][j];

    cout << t;
    return 0;
}
```

---

**Giải thích logic:**

Định nghĩa trạng thái `dp[i][j]` = số xâu độ dài `i` hợp lệ, kết thúc bằng **đúng j** chữ số `0` liên tiếp (`0 ≤ j < k`):

```
Thêm '1' → dp[i][0] += dp[i-1][j]   (mọi j, reset về 0)
Thêm '0' → dp[i][j] += dp[i-1][j-1] (j từ 1..k-1, tăng chuỗi 0)
           (j = k bị bỏ qua vì vi phạm)
```

Kiểm tra với n=3, k=2:

| i | dp[i][0] | dp[i][1] | Tổng |
|:---:|:---:|:---:|:---:|
| 1 | 1 (`"1"`) | 1 (`"0"`) | 2 |
| 2 | 2 (`"11","01"`) | 1 (`"10"`) | 3 |
| 3 | 3 (`"111","011","101"`) | 2 (`"110","010"`) | **5** ✓ |
