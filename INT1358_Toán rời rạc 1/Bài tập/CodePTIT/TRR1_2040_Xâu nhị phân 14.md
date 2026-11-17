# TRR1_2040 — Xâu nhị phân 14

> **Loại:** Tổ hợp đếm · **Thuật toán:** Tam giác Pascal · **Thời gian:** O(n²) · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_2040 - Xâu nhị phân 14**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_2040

**Mô tả:** Cho trước hai số nguyên dương `n` và `s`.
Tìm số lượng `t` các xâu nhị phân độ dài `n` có tổng các chữ số bằng `s`.

**Giới hạn:** `n, s ≤ 50`, `s ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 1</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có t = 3 xâu nhị phân độ dài 3 thỏa mãn gồm 001, 010 và 100.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Tổng chữ số bằng số lượng bit 1 trong xâu.
- Chọn `s` trong `n` vị trí để đặt số 1: có `C(n,s)` cách.
- Pascal: `C[i][0] = 1`, `C[i][j] = C[i-1][j-1] + C[i-1][j]`.
- Dùng `unsigned long long`; `C(50,25)` vẫn nằm trong 64 bit.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, s;
    cin >> n >> s;

    // Xâu nhị phân độ dài n có tổng chữ số = s
    // ↔ Chọn đúng s vị trí trong n vị trí để đặt chữ số '1'
    // → Số lượng = C(n, s) (tổ hợp chập s của n)

    // Tính C(n,s) bằng tam giác Pascal để tránh tràn số
    // C[i][j] = số cách chọn j phần tử từ i phần tử
    vector<vector<unsigned long long>> C(n + 1, vector<unsigned long long>(n + 1, 0));

    for (int i = 0; i <= n; i++) {
        C[i][0] = 1;  // C(i,0) = 1
        for (int j = 1; j <= i; j++) {
            C[i][j] = C[i - 1][j - 1] + C[i - 1][j];  // Công thức Pascal
        }
    }

    cout << C[n][s];
    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `C[i][j]` | Tổ hợp chập `j` của `i` |
| `i` | Số phần tử, cũng là hàng Pascal |
| `j` | Số vị trí được chọn, cũng là cột |
| `C[n][s]` | Đáp án |

1. Đọc `n`, `s`; cấp bảng `(n+1) x (n+1)` toàn 0.
2. Với `i = 0..n`, gán `C[i][0] = 1`.
3. Với `j = 1..i`, tính `C[i][j] = C[i-1][j-1] + C[i-1][j]`.
4. In `C[n][s]`.

**Chỗ dễ sai**
- Tính giai thừa trực tiếp; `50!` vượt xa 64 bit.
- Dùng `int` cho `C`.
- Quên `C[i][0] = 1`.
- In `C[s][n]` thay vì `C[n][s]`.

**Độ phức tạp:** `O(n²)` thời gian, `O(n²)` bộ nhớ.

## Chạy tay

| `i` | `C[i][0]` | `C[i][1]` | `C[i][2]` | `C[i][3]` |
|---:|---:|---:|---:|---:|
| 0 | 1 | | | |
| 1 | 1 | 1 | | |
| 2 | 1 | 2 | 1 | |
| 3 | 1 | 3 | 3 | 1 |

</details>

<details>
<summary><strong>Hướng 2 — Tính trực tiếp tổ hợp bằng nhân chia liên tiếp</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n, s;
    cin >> n >> s;

    unsigned long long result = 1;
    for (int i = 1; i <= s; i++) {
        result = result * (n - s + i) / i;
    }

    cout << result;
    return 0;
}
```

Dùng `C(n,s) = Π(n-s+i)/i` với `i = 1..s`. Chỉ `O(s)` thời gian và `O(1)` bộ nhớ, nhưng phải nhân trước rồi chia.

</details>
