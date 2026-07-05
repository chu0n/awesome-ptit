## Đề bài
> **TRR1_2014 - Xâu nhị phân 04**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_2014

**Mô tả:** Cho trước hai số nguyên dương `n` và `k`.

**Yêu cầu:** Tìm số lượng `t` các xâu nhị phân độ dài `n` có chứa `k` chữ số 1 liên tiếp.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa hai số nguyên dương `n, k` không vượt quá 50 và `2 ≤ k ≤ n` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

| | Input | Output |
|---|---|---|
| Test 1 | `3 2` | `3` |

> **Giải thích Test 1:** Có `t = 3` xâu nhị phân độ dài 3 thỏa mãn gồm `011, 110` và `111`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Xâu nhị phân 04 |
| **Mã bài** | TRR1_2014 |
| **Loại bài** | Toán rời rạc — Tổ hợp đếm |
| **Mục đích** | Đếm xâu nhị phân độ dài n **có chứa** ít nhất k chữ số 1 liên tiếp |
| **Kiến thức** | Quy hoạch động, bài toán đếm xâu có ràng buộc, bù tập hợp |
| **Thuật toán** | Bù: `t = 2ⁿ - (số xâu KHÔNG chứa k chữ số 1 liên tiếp)` — tái dụng tư tưởng DP từ TRR1_2011 |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, k;
    cin >> n >> k;

    // dp[i][j] = số xâu độ dài i hợp lệ, kết thúc bằng đúng j chữ số 1 liên tiếp
    // "Hợp lệ" ở đây nghĩa là KHÔNG chứa k chữ số 1 liên tiếp → j chạy 0..k-1
    vector<vector<unsigned long long>> dp(n + 1, vector<unsigned long long>(k, 0));

    // Base case: xâu độ dài 1
    dp[1][0] = 1;           // Xâu "0" → kết thúc bằng 0 chữ số 1
    if (k > 1) dp[1][1] = 1; // Xâu "1" → kết thúc bằng 1 chữ số 1 (hợp lệ vì k>=2)

    for (int i = 2; i <= n; i++) {
        // Thêm '0': chuỗi 1 liên tiếp reset về 0
        for (int j = 0; j < k; j++)
            dp[i][0] += dp[i-1][j];

        // Thêm '1': kéo dài chuỗi 1 thêm 1 (chỉ hợp lệ khi j < k)
        for (int j = 1; j < k; j++)
            dp[i][j] += dp[i-1][j-1];
    }

    // Tổng xâu độ dài n KHÔNG chứa k chữ số 1 liên tiếp
    unsigned long long notContain = 0;
    for (int j = 0; j < k; j++) notContain += dp[n][j];

    // Bù: tổng xâu độ dài n CÓ chứa k chữ số 1 liên tiếp
    unsigned long long total = 1ULL << n;   // 2^n
    cout << total - notContain;

    return 0;
}
```

---

**Giải thích logic:**

Thay vì đếm trực tiếp (phức tạp vì xâu có thể chứa nhiều đoạn k số 1), ta dùng **phép bù**:

$$t = 2^n - \text{(số xâu không chứa k số 1 liên tiếp)}$$

Phần *"không chứa"* dùng đúng cấu trúc DP của TRR1_2011 nhưng đổi vai trò `0 ↔ 1`:

```
Thêm '0' → dp[i][0] += dp[i-1][j]   (mọi j, reset chuỗi 1 về 0)
Thêm '1' → dp[i][j] += dp[i-1][j-1] (j từ 1..k-1, tăng chuỗi 1)
           (j = k bị bỏ qua vì vi phạm)
```

Kiểm tra với n=3, k=2:

| i | dp[i][0] | dp[i][1] | notContain |
|:---:|:---:|:---:|:---:|
| 1 | 1 (`"0"`) | 1 (`"1"`) | 2 |
| 2 | 2 (`"00","10"`) | 1 (`"01"`) | 3 |
| 3 | 3 (`"000","100","010"`) | 2 (`"001","101"`) | 5 |

$$t = 2^3 - 5 = 8 - 5 = 3 \checkmark$$
