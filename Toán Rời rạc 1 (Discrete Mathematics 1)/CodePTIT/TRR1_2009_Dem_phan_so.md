## Đề bài
> **TRR1_2009 - Đếm phân số**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_2009

**Mô tả:** Cho trước số nguyên dương `n` không vượt quá `10^4`.

**Yêu cầu:** Tìm số lượng `t` các phân số khác nhau có tử số và mẫu số là các số nguyên `a, b`, trong đó `0 ≤ a ≤ b`, `1 ≤ b ≤ n`.

**Giới hạn:** Vào từ tệp Input chuẩn chứa số nguyên dương `n` không vượt quá `10^4` — thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

| | Input | Output |
|---|---|---|
| Test 1 | `4` | `7` |

> **Giải thích Test 1:** Có `t = 7` phân số khác nhau thỏa mãn: `0/1, 1/1, 1/2, 1/3, 2/3, 1/4` và `3/4`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Đếm phân số |
| **Mã bài** | TRR1_2009 |
| **Loại bài** | Toán rời rạc — Tổ hợp đếm |
| **Mục đích** | Đếm số phân số tối giản phân biệt trong miền cho trước |
| **Kiến thức** | Hàm Euler phi (φ), phân số tối giản, UCLN |
| **Thuật toán** | Với mỗi mẫu số b từ 1→n, đếm tử số a (0 ≤ a ≤ b) sao cho gcd(a,b)=1 hoặc a=0; dùng sàng Euler phi để tính nhanh |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;

    // Sàng Euler phi: phi[i] = số lượng số trong [1,i] nguyên tố cùng nhau với i
    vector<int> phi(n + 1);
    iota(phi.begin(), phi.end(), 0);        // Khởi tạo phi[i] = i

    for (int i = 2; i <= n; i++) {
        if (phi[i] == i) {                  // i là số nguyên tố (chưa bị sàng)
            for (int j = i; j <= n; j += i)
                phi[j] -= phi[j] / i;       // Nhân thừa số (1 - 1/i) vào phi[j]
        }
    }

    // Với mỗi mẫu b:
    // - Phân số 0/b (a=0): luôn hợp lệ, nhưng 0/1 = 0/2 = ... → chỉ đếm 1 lần (b=1)
    // - Phân số a/b với 1 <= a <= b, gcd(a,b) = 1: có phi[b] giá trị
    //   nhưng chỉ lấy a <= b nên với a=b thì gcd(b,b)=b=1 chỉ khi b=1 (đã tính)
    //   → thực ra phi[b] đếm đúng số a trong [1,b-1] với gcd(a,b)=1, cộng thêm a=b/b=1 khi b=1

    // Tóm lại: t = 1 (phân số 0) + sum(phi[b]) với b từ 1 đến n
    // Vì: phi[b] = số a trong [1,b] với gcd(a,b)=1, bao gồm a=b chỉ khi b=1
    // Các phân số a/b với gcd(a,b)=1 và 1<=a<=b đều phân biệt nhau

    long long t = 1;                        // +1 cho phân số 0 (tức 0/1)
    for (int b = 1; b <= n; b++)
        t += phi[b];                        // Đếm a/b với gcd(a,b)=1, 1<=a<=b

    cout << t;
    return 0;
}
```

---

**Giải thích logic:**

Một phân số `a/b` (với `0 ≤ a ≤ b`, `1 ≤ b ≤ n`) là **duy nhất** khi nó ở dạng **tối giản**, tức `gcd(a,b) = 1`.

- **Phân số bằng 0:** chỉ có `0/1` duy nhất → cộng `1`
- **Phân số dương:** với mỗi mẫu `b`, số tử `a ∈ [1, b]` thỏa `gcd(a,b) = 1` chính là `φ(b)`

$$t = 1 + \sum_{b=1}^{n} \varphi(b)$$

Kiểm tra với n=4:

| b | φ(b) | Các phân số |
|:---:|:---:|---|
| 1 | 1 | 1/1 |
| 2 | 1 | 1/2 |
| 3 | 2 | 1/3, 2/3 |
| 4 | 2 | 1/4, 3/4 |

$$t = 1 + 1 + 1 + 2 + 2 = 7 \checkmark$$
