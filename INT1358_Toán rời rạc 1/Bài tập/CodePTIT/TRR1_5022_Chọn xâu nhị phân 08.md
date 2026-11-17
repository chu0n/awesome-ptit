# TRR1_5022 — Chọn xâu nhị phân 08

> **Loại:** Nguyên lý Dirichlet · **Thuật toán:** Tổ hợp, bù tập hợp · **Thời gian:** O(n²) · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_5022 - Chọn xâu nhị phân 08**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_5022

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Tìm số lượng ít nhất `t` các xâu nhị phân độ dài `n` cần chọn để **chắc chắn** tồn tại xâu có đúng `k` chữ số 1.

**Giới hạn:** `n, k ≤ 50`, `2 ≤ k ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>6</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có 5 xâu nhị phân độ dài 3 không chứa đúng 2 chữ số 1 là 000, 001, 010, 100 và 111. Do đó t = 6 thỏa mãn.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Tổng số xâu nhị phân độ dài `n` là `2^n`.
- Số xâu có đúng `k` chữ số `1` là `C(n, k)`: chọn `k` vị trí trong `n` vị trí.
- Số xâu chưa đạt là `bad = 2^n - C(n, k)`.
- Tệ nhất chọn hết `bad` xâu chưa đạt; xâu tiếp theo buộc đạt, nên đáp án là `bad + 1`.
- Đề bảo đảm `2 ≤ k ≤ n ≤ 50`, nên `C(n, k) ≥ 1`; không cần kiểm tra dữ liệu đầu vào.

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

    // choose[i][j] = số cách chọn j vị trí từ i vị trí.
    vector<vector<unsigned long long>> choose(
        n + 1,
        vector<unsigned long long>(n + 1, 0));
    for (int i = 0; i <= n; i++) {
        choose[i][0] = 1;
        for (int j = 1; j <= i; j++) {
            choose[i][j] = choose[i - 1][j - 1] + choose[i - 1][j];
        }
    }

    unsigned long long totalStrings  = 1ULL << n;  // n <= 50 nên kiểu này đủ lớn.
    unsigned long long wantedStrings = choose[n][k];
    unsigned long long otherStrings  = totalStrings - wantedStrings;

    // Chọn hết xâu chưa đạt, rồi thêm một xâu có đúng k chữ số 1.
    cout << otherStrings + 1;
    return 0;
}
```

### Giải thích code

| Biến | Ý nghĩa |
|---|---|
| `n`, `k` | Độ dài xâu và số chữ số `1` cần có |
| `choose[i][j]` | `C(i, j)` |
| `wantedStrings` | Số xâu đạt yêu cầu |
| `otherStrings` | Số xâu chưa đạt |

1. Đọc `n`, `k`.
2. Dựng bảng Pascal `(n + 1) × (n + 1)`: `choose[i][0] = 1`, `choose[i][j] = choose[i-1][j-1] + choose[i-1][j]`.
3. Lấy `wanted = choose[n][k]`.
4. Tính `total = 1ULL << n`, `bad = total - wanted`.
5. In `bad + 1`.

Bảng cần `n + 1` hàng/cột vì dùng cả chỉ số `0`. Không khởi tạo 0 sẽ đọc rác ở các ô ngoài tam giác.

`1ULL` bắt buộc: `1 << n` trên `int` sai khi `n ≥ 31`.

**Độ phức tạp:** `O(n²)` thời gian và bộ nhớ; `n ≤ 50` nên rất nhỏ.

## Chạy tay

| Input | `C(n,k)` | `total` | `bad` | Kết quả |
|---|---:|---:|---:|---:|
| `3 2` | 3 | 8 | 5 | 6 |
| `3 3` | 1 | 8 | 7 | 8 |

## Bẫy

- Viết `1 << n` thay vì `1ULL << n`.
- Cấp bảng `n × n` thay vì `(n + 1) × (n + 1)`.
- Quên `choose[i][0] = 1` hoặc quên khởi tạo bảng bằng 0.
- In `bad` thay vì `bad + 1`.

</details>

<details>
<summary><strong>Hướng 2 — tính C(n,k) bằng nhân chia</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n, k;
    cin >> n >> k;

    unsigned long long wantedStrings = 1;
    for (int i = 1; i <= k; i++) {
        wantedStrings = wantedStrings * (n - k + i) / i;
    }

    unsigned long long totalStrings = 1ULL << n;
    unsigned long long otherStrings = totalStrings - wantedStrings;

    cout << otherStrings + 1;
    return 0;
}
```

`C(n,k) = ∏(n-k+i)/i`; phép chia từng bước là chia hết. Cách này dùng `O(1)` bộ nhớ thay vì bảng Pascal.

</details>
