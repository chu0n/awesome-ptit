# TRR1_2005 — Đếm số hình vuông

> **Loại:** Tổ hợp đếm · **Thuật toán:** Tham lam, cộng dần i² · **Thời gian:** O(n^(1/3)) · **Bộ nhớ:** O(1)

## Đề bài
> **TRR1_2005 - Đếm số hình vuông**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_2005

**Mô tả:** Cho trước `n` ô vuông đơn vị với cạnh là 1.

**Yêu cầu:** Tìm số lượng `t` lớn nhất các hình vuông có kích thước cạnh là các số nguyên khác nhau có thể tạo được từ `n` ô vuông đã cho.

**Giới hạn:** Vào từ tệp Input chuẩn gồm một dòng chứa số nguyên dương `n` không vượt quá `2^18` — thời gian chạy ≤ 1 giây, bộ nhớ 64 MB.

**Kết quả:** Ghi ra tệp Output chuẩn giá trị `t` tìm được.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>10</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>2</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Giá trị lớn nhất t = 2 vì có thể tạo được hai hình vuông cạnh 1 và cạnh 2.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Hình cạnh `i` tốn `i²` ô; các cạnh phải đôi một khác nhau.
- Với `t` hình, chi phí nhỏ nhất là `S_t = 1² + 2² + ... + t²`.
- Tạo được `t` hình khi và chỉ khi `S_t <= n`.
- `S_t` tăng nên chọn cạnh `1, 2, 3, ...` đến khi vượt `n` là tối ưu.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long n;
    cin >> n;

    // Tham lam: lấy hình vuông cạnh 1, 2, 3,... theo thứ tự tăng dần
    // Mỗi hình vuông cạnh i tốn i*i ô vuông đơn vị
    // Dừng lại khi không đủ ô để tạo hình vuông tiếp theo
    long long t = 0, used = 0;
    for (long long i = 1; used + i * i <= n; i++) {
        used += i * i;  // Dùng i² ô cho hình vuông cạnh i
        t++;            // Tăng số lượng hình vuông
    }

    cout << t;
    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `n` | Số ô vuông đơn vị |
| `i` | Cạnh đang thử, tăng `1, 2, 3, ...` |
| `used` | Tổng ô đã dùng |
| `t` | Số hình đã chọn, cũng là đáp án |

1. Đọc `n`.
2. Khởi tạo `t = 0`, `used = 0`.
3. Với `i = 1, 2, ...`, dừng khi `used + i*i > n`.
4. Nếu còn đủ ô, cộng `i*i` vào `used` và tăng `t`.
5. In `t`.

**Chỗ dễ sai**
- Cộng `i` thay vì `i*i`.
- Dùng `<` thay vì `<=` làm mất trường hợp vừa đủ.
- Bắt đầu `i` từ 0.
- Quên cập nhật `used` nên chọn quá nhiều hình.

**Độ phức tạp:** `O(n^(1/3))` thời gian, `O(1)` bộ nhớ.

## Chạy tay

Với `n = 10`:

| `i` | `i²` | `used` trước | Điều kiện | `used` sau | `t` |
|---:|---:|---:|---|---:|---:|
| 1 | 1 | 0 | `1 <= 10` | 1 | 1 |
| 2 | 4 | 1 | `5 <= 10` | 5 | 2 |
| 3 | 9 | 5 | `14 <= 10` sai | 5 | 2 |

</details>

<details>
<summary><strong>Hướng 2 — Công thức tổng bình phương và tìm kiếm nhị phân</strong></summary>

```cpp
#include <iostream>
using namespace std;

long long sumSquares(long long count)
{
    return count * (count + 1) * (2 * count + 1) / 6;
}

int main()
{
    long long n;
    cin >> n;

    long long low  = 0;
    long long high = n;
    while (low < high) {
        long long middle = (low + high + 1) / 2;
        if (sumSquares(middle) <= n) {
            low = middle;
        } else {
            high = middle - 1;
        }
    }

    cout << low;
    return 0;
}
```

Tìm `t` lớn nhất với `t(t+1)(2t+1)/6 <= n`. Nhanh hơn về lý thuyết nhưng cần xử lý `long long` và biên tìm kiếm.

</details>
