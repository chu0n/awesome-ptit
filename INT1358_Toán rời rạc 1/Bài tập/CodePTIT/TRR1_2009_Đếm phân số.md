# TRR1_2009 — Đếm phân số

> **Loại:** Tổ hợp đếm · **Thuật toán:** Sàng Euler phi · **Thời gian:** O(n log log n) · **Bộ nhớ:** O(n)

## Đề bài
> **TRR1_2009 - Đếm phân số**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_2009

**Mô tả:** Cho trước số nguyên dương `n` không vượt quá `10^4`.

**Yêu cầu:** Tìm số lượng `t` các phân số khác nhau có tử số và mẫu số là các số nguyên `a, b`, trong đó `0 ≤ a ≤ b`, `1 ≤ b ≤ n`.

**Giới hạn:** Vào từ tệp Input chuẩn chứa số nguyên dương `n` không vượt quá `10^4` — thời gian chạy ≤ 1 giây, bộ nhớ 64 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>7</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có t = 7 phân số khác nhau thỏa mãn: 0/1, 1/1, 1/2, 1/3, 2/3, 1/4 và 3/4.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Mỗi giá trị hữu tỉ có duy nhất dạng tối giản `a/b` với `gcd(a,b) = 1`.
- Giá trị 0 chỉ có `0/1`, cộng đúng một lần.
- Với mẫu `b`, số tử tối giản `1 <= a <= b` là `φ(b)`.
- `t = 1 + Σφ(b)` với `b = 1..n`.
- Sàng φ: với mỗi số nguyên tố `i`, `phi[j] -= phi[j]/i` cho mọi bội `j`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
#include <numeric>
#include <vector>
using namespace std;

int main()
{
    int n;
    cin >> n;

    // Sàng Euler phi: phi[i] = số lượng số trong [1,i] nguyên tố cùng nhau với i
    vector<int> phi(n + 1);
    iota(phi.begin(), phi.end(), 0);  // Khởi tạo phi[i] = i

    for (int i = 2; i <= n; i++) {
        if (phi[i] == i) {  // i là số nguyên tố (chưa bị sàng)
            for (int j = i; j <= n; j += i) {
                phi[j] -= phi[j] / i;  // Nhân thừa số (1 - 1/i) vào phi[j]
            }
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

    long long t = 1;  // +1 cho phân số 0 (tức 0/1)
    for (int b = 1; b <= n; b++) {
        t += phi[b];  // Đếm a/b với gcd(a,b)=1, 1<=a<=b
    }

    cout << t;
    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `phi[b]` | Số `a` trong `1..b` nguyên tố cùng nhau với `b` |
| `i`, `j` | Số nguyên tố sàng và bội của nó |
| `t` | `1 + Σphi[b]` |
| `n` | Mẫu số tối đa |

1. Đọc `n`; khởi tạo `phi[i] = i`.
2. Với `i = 2..n`, nếu `phi[i] == i` thì `i` nguyên tố; cập nhật mọi bội `j`.
3. Khởi tạo `t = 1` cho phân số `0/1`.
4. Cộng `phi[b]` với `b = 1..n` rồi in `t`.

**Chỗ dễ sai**
- Đếm mọi cặp `(a,b)` mà không khử trùng.
- Quên cộng 1 cho phân số 0.
- Khởi tạo `phi[1] = 0` làm mất phân số `1/1`.
- Bắt đầu sàng từ `i = 1`.
- Bắt đầu vòng trong từ `2i` làm `phi[i]` sai.

**Độ phức tạp:** `O(n log log n)` thời gian, `O(n)` bộ nhớ.

## Chạy tay

Với `n = 4`:

| `b` | `phi[b]` | Phân số mới | `t` |
|---:|---:|---|---:|
| khởi tạo | — | `0/1` | 1 |
| 1 | 1 | `1/1` | 2 |
| 2 | 1 | `1/2` | 3 |
| 3 | 2 | `1/3`, `2/3` | 5 |
| 4 | 2 | `1/4`, `3/4` | 7 |

</details>

<details>
<summary><strong>Hướng 2 — Phân tích từng mẫu để tính φ</strong></summary>

```cpp
#include <iostream>
using namespace std;

long long eulerPhi(int value)
{
    long long result    = value;
    int       remaining = value;

    for (int prime = 2; prime * prime <= remaining; prime++) {
        if (remaining % prime == 0) {
            while (remaining % prime == 0) {
                remaining /= prime;
            }
            result -= result / prime;
        }
    }

    if (remaining > 1) {
        result -= result / remaining;
    }
    return result;
}

int main()
{
    int n;
    cin >> n;

    long long fractions = 1;
    for (int denominator = 1; denominator <= n; denominator++) {
        fractions += eulerPhi(denominator);
    }

    cout << fractions;
    return 0;
}
```

Tính `φ(b)` bằng cách phân tích thừa số nguyên tố trong `O(√b)`, không cần mảng sàng. Với `n <= 10^4`, tổng `O(n√n)` vẫn đủ nhanh.

</details>
