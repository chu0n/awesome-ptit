# TRR1_3008 — Liệt kê tổ hợp trước 01

> **Loại:** Sinh tổ hợp · **Thuật toán:** Giảm phần tử bản lề, điền đuôi lớn nhất · **Thời gian:** O(k) · **Bộ nhớ:** O(k)

> [!CAUTION]
> **CẢNH BÁO: Bài này không AC được trên CodePTIT hiện tại vì test case của CodePTIT bị sai.**
> **Bấm “Hiện code AC” bên dưới để xem code AC được.**
>
> <details>
> <summary><strong>Hiện code AC</strong></summary>
>
> ```cpp
> #include <bits/stdc++.h>
> using namespace std;
> 
> int main()
> {
>     int n, k;
>     cin >> n >> k;
>     vector<int> a(k);
>     for (int i = 0; i < k; i++) {
>         cin >> a[i];
>     }
> 
>     int i = k - 1;
>     while (i >= 0 && a[i] == i + 1) {
>         i--;
>     }
> 
>     if (i < 0) {
>         cout << 0;
>         return 0;
>     }
> 
>     a[i]--;
>     for (int j = i + 1; j < k; j++) {
>         a[j] = a[j - 1] + 1;
>     }
> 
>     for (int x = 0; x < k; x++) {
>         cout << a[x] << " \n"[x == k - 1];
>     }
> 
>     return 0;
> }
> ```
>
> </details>

## Đề bài

> **TRR1_3008 - Liệt kê tổ hợp trước 01**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3008

**Mô tả:** Cho hai số nguyên dương `n`, `k` và một tổ hợp chập `k` của `n` số nguyên dương đầu tiên.
Tìm tổ hợp liền kề **trước** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n ≤ 100`, `k ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5 3
1 2 5</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>1 2 4</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Tổ hợp trước của tổ hợp (1,2,5) là (1,2,4).</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Tổ hợp lưu thành dãy tăng `a[0] < a[1] < ... < a[k-1]`; thứ tự từ điển trên dãy trùng thứ tự trên tổ hợp.
- Giá trị lớn nhất tại vị trí `j` là `n - k + j + 1`, vì phía sau còn `k - 1 - j` chỗ.
- Giá trị nhỏ nhất tại `j` là `1` nếu `j = 0`, ngược lại là `a[j-1] + 1`.
- Tìm `i` ngoài cùng bên phải còn giảm được (`a[i]` lớn hơn giới hạn dưới), giảm `a[i]` đi 1, rồi điền đuôi bằng giá trị lớn nhất. Đây là tổ hợp lớn nhất nhỏ hơn bản cũ, tức liền kề trước.
- Không có `i` nghĩa là đang ở `1 2 ... k`, tổ hợp nhỏ nhất; in `0`.

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
    vector<int> a(k);
    for (int i = 0; i < k; i++) {
        cin >> a[i];
    }

    // Thuật toán tìm tổ hợp liền kề TRƯỚC theo thứ tự từ điển:
    // Tổ hợp được lưu tăng dần: a[0] < a[1] < ... < a[k-1]
    //
    // B1: Tìm i ngoài cùng bên phải sao cho a[i] có thể giảm
    //     Điều kiện giảm được: a[i] > a[i-1]+1 (hoặc a[0] > 1)
    //     Phải giữ a[i] lớn hơn phần tử ngay trước sau khi giảm
    //     Nếu a[i] = a[i-1]+1 → chuyển sang vị trí bên trái
    //
    // B2: Nếu không tìm được i → đang ở tổ hợp nhỏ nhất → in 0
    //
    // B3: Giảm a[i] đi 1, sau đó điền a[i+1..k-1] bằng các số lớn nhất
    //     a[j] = n-k+j+1 để tạo tổ hợp lớn nhất có thể với tiền tố đó

    int i = k - 1;
    while (i >= 0) {
        int smallest = 1;
        if (i > 0) {
            smallest = a[i - 1] + 1;
        }
        if (a[i] > smallest) {
            break;
        }
        i--;  // B1: tìm i từ phải
    }

    if (i < 0) {
        cout << 0;
        return 0;
    }  // B2: tổ hợp nhỏ nhất

    a[i]--;  // B3: giảm a[i] đi 1
    for (int j = i + 1; j < k; j++) {
        a[j] = n - k + j + 1;  // Điền phần sau bằng các giá trị lớn nhất
    }

    for (int x = 0; x < k; x++) {
        cout << a[x] << " \n"[x == k - 1];
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `a` | Tổ hợp hiện tại, tăng dần, chỉ số 0-based |
| `i` | Vị trí bản lề cần giảm |
| `smallest` | Giá trị nhỏ nhất hợp lệ tại `i` |
| `n - k + j + 1` | Giá trị lớn nhất hợp lệ tại vị trí `j` |
| `i < 0` | Không còn tổ hợp trước, in `0` |

1. Đọc `n`, `k` và `k` phần tử của tổ hợp.
2. Quét `i` từ `k - 1` về 0, dừng ở vị trí đầu tiên có `a[i] > smallest`.
3. Nếu `i < 0`, in `0` và kết thúc.
4. Giảm `a[i]` một đơn vị, gán `a[j] = n - k + j + 1` cho mọi `j > i`.
5. In tổ hợp mới, cách nhau một dấu cách.

**Chỗ dễ sai**
- Quét `i` từ trái sang phải: đổi vị trí không ngoài cùng bên phải, tổ hợp mới nhỏ hơn quá mức.
- Chỉ so `a[i] > 1`: với `i > 0` phải so với `a[i-1] + 1`, nếu không sẽ tạo giá trị trùng.
- Điền đuôi bằng giá trị nhỏ nhất: được tổ hợp nhỏ nhất trong nhóm, không liền kề.
- Quên `return` sau `cout << 0`, chương trình chạy tiếp với `i = -1`.
- Dùng `n - k + j + 2`: phần tử cuối vượt quá `n`.

**Độ phức tạp:** `O(k)` thời gian, `O(k)` bộ nhớ.

## Chạy tay

`n = 5`, `k = 3`, tổ hợp `1 3 4` — trường hợp phải lùi `i` và điền lại đuôi:

| Bước | `i` | Mảng |
|---|---:|---|
| Xét `i = 2`: `4 = a[1] + 1` nên lùi | 1 | `1 3 4` |
| Xét `i = 1`: `3 > a[0] + 1` nên dừng | 1 | `1 3 4` |
| Giảm `a[1]` | 1 | `1 2 4` |
| Điền đuôi `a[2] = 5` | 1 | `1 2 5` |

</details>

<details>
<summary><strong>Hướng 2 — xâu bit và next_permutation</strong></summary>

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, k;
    cin >> n >> k;

    vector<int> bits(n, 0);
    for (int i = 0; i < k; i++) {
        int value;
        cin >> value;
        bits[value - 1] = 1;
    }

    if (!next_permutation(bits.begin(), bits.end())) {
        cout << 0;
        return 0;
    }

    bool first = true;
    for (int i = 0; i < n; i++) {
        if (bits[i] == 1) {
            if (!first) {
                cout << ' ';
            }
            cout << i + 1;
            first = false;
        }
    }
    cout << '\n';
    return 0;
}
```

Biểu diễn tổ hợp bằng `n` bit; vì thứ tự bit đảo chiều so với thứ tự tổ hợp, tổ hợp trước ứng với xâu bit kế tiếp. Cách này ngắn hơn nhưng tốn `O(n)` bộ nhớ và cần hiểu ánh xạ.

</details>
