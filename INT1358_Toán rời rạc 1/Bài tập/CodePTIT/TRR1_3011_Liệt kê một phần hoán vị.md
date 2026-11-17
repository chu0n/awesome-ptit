# TRR1_3011 — Liệt kê một phần hoán vị

> **Loại:** Sinh hoán vị · **Thuật toán:** Sinh hoán vị kế tiếp, lặp `t` lần · **Thời gian:** O(t·n) · **Bộ nhớ:** O(n)

## Đề bài

> **TRR1_3011 - Liệt kê một phần hoán vị**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3011

**Mô tả:** Cho hai số nguyên dương `n`, `t` và một hoán vị của `n` số nguyên dương đầu tiên.
Tìm `t` hoán vị liền kề **tiếp theo** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n, t ≤ 100` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5 2
3 1 2 5 4</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 1 4 2 5
3 1 4 5 2</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Hai hoán vị kế tiếp của hoán vị (3, 1, 2, 5, 4) là (3,1,4,2,5) và (3,1,4,5,2).</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Giữ tiền tố dài nhất có thể: tìm `i` ngoài cùng bên phải với `a[i] < a[i+1]`.
- Đoạn sau `i` khi đó đang giảm; chọn `j` ngoài cùng bên phải có `a[j] > a[i]`, tức giá trị nhỏ nhất lớn hơn `a[i]`.
- Đổi chỗ `a[i]`, `a[j]` rồi đảo đoạn sau `i`: đuôi từ giảm thành tăng, là cách xếp nhỏ nhất.
- Không có `i` nghĩa là hoán vị đang là lớn nhất; các dòng còn lại in `0`.
- Lặp đúng `t` lần, mỗi lần in một hoán vị.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

// Sinh hoán vị kế tiếp theo thứ tự từ điển
// Trả về false nếu đang ở hoán vị lớn nhất (không có hoán vị tiếp theo)
bool nextPermutation(vector<int>& a)
{
    int n = a.size();

    // B1: Tìm i lớn nhất sao cho a[i] < a[i+1] (từ phải sang trái)
    int i = n - 2;
    while (i >= 0 && a[i] >= a[i + 1]) {
        i--;
    }
    if (i < 0) {
        return false;  // Hoán vị lớn nhất, không có kế tiếp
    }

    // B2: Tìm j lớn nhất sao cho a[j] > a[i] (bên phải vị trí i)
    int j = n - 1;
    while (a[j] <= a[i]) {
        j--;
    }

    swap(a[i], a[j]);                     // B3: Hoán đổi a[i] và a[j]
    reverse(a.begin() + i + 1, a.end());  // B4: Đảo ngược đuôi a[i+1..n-1]
    return true;
}

int main()
{
    int n, t;
    cin >> n >> t;
    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    for (int step = 0; step < t; step++) {
        // Nếu không còn hoán vị tiếp theo → in 0 cho tất cả dòng còn lại
        if (!nextPermutation(a)) {
            for (int remain = step; remain < t; remain++) {
                cout << "0\n";
            }
            return 0;
        }
        for (int x = 0; x < n; x++) {
            cout << a[x] << " \n"[x == n - 1];
        }
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `i` | Vị trí bản lề, nơi đầu tiên còn tăng được từ phải |
| `j` | Vị trí đổi chỗ, giữ giá trị nhỏ nhất lớn hơn `a[i]` |
| `step` | Số hoán vị đã sinh |
| `remain` | Số dòng còn thiếu khi hết hoán vị |
| `reverse(a.begin() + i + 1, ...)` | Đưa đuôi từ giảm về tăng |

1. Đọc `n`, `t` và hoán vị ban đầu.
2. Tìm `i` từ `n - 2` về 0 sao cho `a[i] < a[i+1]`; nếu không có, in `0` cho `t - step` dòng còn lại.
3. Tìm `j` từ cuối về sao cho `a[j] > a[i]`.
4. Đổi chỗ `a[i]`, `a[j]` rồi đảo đoạn `i+1..n-1`.
5. In hoán vị mới và lặp.

**Chỗ dễ sai**
- Dùng `a[i] <= a[i+1]` khi tìm `i`: đó là logic của hoán vị trước.
- Quét `j` từ trái sang phải: chọn giá trị lớn nhất, không phải nhỏ nhất lớn hơn.
- Đảo từ `i` hoặc đảo cả mảng: phá tiền tố vừa giữ.
- Đảo trước rồi mới đổi chỗ: mất cấu trúc đuôi giảm.
- Hết hoán vị nhưng chỉ in một dòng `0`: thiếu dòng.

**Độ phức tạp:** `O(t·n)` thời gian, `O(n)` bộ nhớ.

## Chạy tay

| Lần | `i` | `j` | Sau `swap` | Sau `reverse` |
|---|---:|---:|---|---|
| 1 | 2 | 4 | `3 1 4 5 2` | `3 1 4 2 5` |
| 2 | 3 | 4 | `3 1 4 5 2` | `3 1 4 5 2` |

</details>

<details>
<summary><strong>Hướng 2 — dùng std::next_permutation</strong></summary>

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, t;
    cin >> n >> t;
    vector<int> permutation(n);
    for (int i = 0; i < n; i++) {
        cin >> permutation[i];
    }

    for (int step = 0; step < t; step++) {
        if (!next_permutation(permutation.begin(), permutation.end())) {
            for (int remain = step; remain < t; remain++) {
                cout << "0\n";
            }
            return 0;
        }

        for (int i = 0; i < n; i++) {
            cout << permutation[i] << " \n"[i == n - 1];
        }
    }
    return 0;
}
```

Thư viện làm đúng bốn bước trên, code ngắn hơn nhưng vẫn phải kiểm tra giá trị trả về để biết khi nào in `0`.

</details>
