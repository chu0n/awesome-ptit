## Đề bài
> **TRR1_1018 - Tập hợp 02**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_1018

**Mô tả:** Xét tập hợp `S` gồm `n` số nguyên dương đầu tiên. Mỗi tập con `A` của `S` có thể được biểu diễn dưới dạng xâu nhị phân `X` độ dài `n`, trong đó, `X[i] = 1` nếu số `i` (1 ≤ i ≤ n) là phần tử của tập `A` và `X[i] = 0` trong trường hợp ngược lại. Cho trước hai tập con `A` và `B` của tập `S`.

**Yêu cầu:** Xác định các phần tử của tập hợp là giao của hai tập `A` và `B`.

**Giới hạn:** 
- Dòng đầu chứa số nguyên dương `n` không vượt quá 1000.
- Trong hai dòng tiếp theo, mỗi dòng chứa một xâu nhị phân độ dài `n` biểu diễn tập con `A` và `B`.
- Thời gian chạy ≤ 1 giây, bộ nhớ 65536 MB.

**Kết quả:** Ghi ra tệp Output chuẩn gồm 2 dòng:
- Dòng đầu ghi số `k` là số lượng phần tử của tập hợp là giao của hai tập `A` và `B`;
- Trong trường hợp `k > 0`, dòng thứ 2 ghi `k` số là các phần tử của tập hợp là giao của hai tập `A` và `B` theo thứ tự tăng.

| | Input | Output |
|---|---|---|
| Test 1 | `5`<br>`0 1 1 0 1`<br>`1 1 0 1 1` | `2`<br>`2 5` |

> **Giải thích Test 1:** Tập `A = {2,3,5}`, `B = {1,2,4,5}` nên giao của hai tập `A` và `B` là `{2, 5}`.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Tập hợp 02 |
| **Mã bài** | TRR1_1018 |
| **Loại bài** | Toán rời rạc — Lý thuyết tập hợp |
| **Mục đích** | Tính giao (intersection) của hai tập hợp biểu diễn bằng xâu nhị phân |
| **Kiến thức** | Tập hợp, phép giao, biểu diễn tập hợp bằng bit |
| **Thuật toán** | Duyệt từng vị trí, lấy `AND` bit của A[i] và B[i] → vị trí nào có giá trị `1` thì thuộc giao |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;

    vector<int> a(n), b(n);
    for (int i = 0; i < n; i++) cin >> a[i];
    for (int i = 0; i < n; i++) cin >> b[i];

    // Tính giao A ∩ B: vị trí i thuộc giao nếu a[i]=1 VÀ b[i]=1
    vector<int> result;
    for (int i = 0; i < n; i++)
        if (a[i] & b[i]) result.push_back(i + 1);  // i+1 vì phần tử đánh số từ 1

    // Xuất kết quả
    cout << result.size() << "\n";
    for (int i = 0; i < (int)result.size(); i++)
        cout << result[i] << " \n"[i == (int)result.size()-1];

    return 0;
}
```

---

**Giải thích logic:**

| Bit A[i] | Bit B[i] | A[i] & B[i] | Thuộc A∩B? |
|:---:|:---:|:---:|:---:|
| 0 | 0 | 0 | ✗ |
| 0 | 1 | 0 | ✗ |
| 1 | 0 | 0 | ✗ |
| 1 | 1 | 1 | ✓ |

> **So sánh với TRR1_1017:** Bài này chỉ khác một ký tự — dùng `&` (AND) thay vì `|` (OR). Phép hợp lấy những gì có ở **ít nhất một** tập, phép giao chỉ lấy những gì có ở **cả hai** tập.
