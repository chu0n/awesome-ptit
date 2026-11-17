# TRR1_3006 — Liệt kê hoán vị trước 01

> **Loại:** Liệt kê tổ hợp · **Thuật toán:** Sinh hoán vị liền trước · **Thời gian:** O(n) · **Bộ nhớ:** O(n)

## Đề bài

> **TRR1_3006 - Liệt kê hoán vị trước 01**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3006

**Mô tả:** Cho số nguyên dương `n` và một hoán vị của `n` số nguyên dương đầu tiên.
Tìm hoán vị liền kề **trước** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n ≤ 100` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5
3 1 2 5 4</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 1 2 4 5</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Hoán vị liền kề trước của hoán vị (3, 1, 2, 5, 4) là (3, 1, 2, 4, 5).</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Tìm `i` phải nhất có `a[i] > a[i+1]`; đoạn sau `i` đang tăng.
- Không có `i`: dãy là hoán vị nhỏ nhất, in `0`.
- Chọn `j` phải nhất có `a[j] < a[i]`; đó là giá trị lớn nhất nhỏ hơn `a[i]`.
- Đổi chỗ rồi đảo đuôi: đuôi tăng thành giảm, tức cách xếp lớn nhất.
- Kết quả là hoán vị nhỏ hơn gần nhất.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n;
    cin >> n;
    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // Thuật toán tìm hoán vị liền kề TRƯỚC (prev_permutation):
    // B1: Tìm i lớn nhất sao cho a[i] > a[i+1] (từ phải sang trái)
    //     → Đây là vị trí đầu tiên bị "tăng" khi đọc từ phải
    // B2: Nếu không tìm được i → đang ở hoán vị nhỏ nhất → in 0
    // B3: Tìm j lớn nhất sao cho a[j] < a[i] (bên phải vị trí i)
    //     → Phần tử nhỏ hơn a[i] lớn nhất ở bên phải
    // B4: Hoán đổi a[i] và a[j]
    // B5: Đảo ngược đoạn a[i+1..n-1] (từ tăng dần → giảm dần để lấy max)

    int i = n - 2;
    while (i >= 0 && a[i] <= a[i + 1]) {
        i--;  // B1: tìm i từ phải
    }

    if (i < 0) {
        cout << 0;
        return 0;
    }  // B2: hoán vị nhỏ nhất

    int j = n - 1;
    while (a[j] >= a[i]) {
        j--;  // B3: tìm j thỏa a[j] < a[i]
    }

    swap(a[i], a[j]);                     // B4: hoán đổi
    reverse(a.begin() + i + 1, a.end());  // B5: đảo ngược đuôi (tăng → giảm)

    for (int x = 0; x < n; x++) {
        cout << a[x] << " \n"[x == n - 1];
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `a` | Hoán vị hiện tại |
| `i` | Vị trí giảm ngoài cùng bên phải |
| `j` | Vị trí chứa giá trị lớn nhất nhỏ hơn `a[i]` |
| `reverse` | Đảo đoạn `a[i+1..n-1]` |

1. Đọc `n` và hoán vị `a`.
2. Lùi `i` từ `n-2` khi `a[i] <= a[i+1]`; nếu `i < 0`, in `0` và dừng.
3. Lùi `j` từ `n-1` đến khi `a[j] < a[i]`.
4. Đổi chỗ `a[i]`, `a[j]` rồi đảo đoạn sau `i`.
5. In hoán vị mới.

**Chỗ dễ sai**
- Cho vòng tìm `i` lùi khi `a[i] >= a[i+1]`; đó là logic của hoán vị kế tiếp.
- Tìm `i` từ trái sang phải làm mất tính liền kề.
- Chọn `j` nhỏ nhất thay vì lớn nhất nhỏ hơn `a[i]`.
- Quên `return` sau khi in `0`.
- Đảo từ `i` hoặc đảo trước khi đổi chỗ.

**Độ phức tạp:** `O(n)` thời gian, `O(n)` bộ nhớ.

## Chạy tay

Với hoán vị `3 1 2 5 4`:

| Bước | Thao tác | `i` | `j` | `a` |
|---|---|---:|---:|---|
| Ban đầu | đọc dữ liệu | | | `3 1 2 5 4` |
| Tìm `i` | `5 > 4` | 3 | | `3 1 2 5 4` |
| Tìm `j` | `4 < 5` | 3 | 4 | `3 1 2 5 4` |
| Đổi chỗ | `swap(a[3], a[4])` | 3 | 4 | `3 1 2 4 5` |
| Đảo đuôi | đoạn một phần tử | 3 | 4 | `3 1 2 4 5` |

</details>

<details>
<summary><strong>Hướng 2 — Dùng std::prev_permutation</strong></summary>

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n;
    cin >> n;
    vector<int> permutation(n);
    for (int i = 0; i < n; i++) {
        cin >> permutation[i];
    }

    if (!prev_permutation(permutation.begin(), permutation.end())) {
        cout << 0;
        return 0;
    }

    for (int i = 0; i < n; i++) {
        cout << permutation[i] << " \n"[i == n - 1];
    }
    return 0;
}
```

Thư viện đã cài đúng thuật toán, chỉ cần kiểm tra giá trị trả về. Ngắn hơn nhưng che mất ba bước tìm `i`, tìm `j`, đảo đuôi.

</details>
