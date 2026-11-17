# TRR1_4001 — Bài toán cái túi 01

> **Loại:** Tối ưu tổ hợp · **Thuật toán:** Duyệt toàn bộ `2^n` tập con bằng bitmask · **Thời gian:** O(n·2^n) · **Bộ nhớ:** O(n)

## Đề bài

> **TRR1_4001 - Bài toán cái túi 01**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_4001

**Mô tả:** Cho cái túi sức chứa `b` và `n` đồ vật, mỗi đồ vật có trọng lượng `a[i]` và giá trị `c[i]`. Tìm cách chọn đồ vật sao cho tổng giá trị lớn nhất mà không vượt quá sức chứa.

**Giới hạn:** `n ≤ 20`, `b ≤ 10⁷`, `a[i], c[i] ≤ 10⁶` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 8
5 8
7 12
3 7</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>15
1 0 1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Đồ vật thứ 1 và thứ 3 được chọn với tổng giá trị sử dụng lớn nhất là 15.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Mỗi đồ vật có hai trạng thái lấy hoặc không, nên mỗi tập con ứng với một `mask` từ `0` đến `2^n - 1`.
- Bit `i` của `mask` bằng 1 nghĩa là chọn đồ vật `i`; kiểm tra bằng `mask & (1 << i)`.
- Tính `totalW`, `totalC` cho từng `mask`; chỉ nhận khi `totalW <= b`.
- Cập nhật khi `totalC > bestVal`, nên tập đồng hạng gặp trước được giữ.
- Lưu thêm `bestMask` để tái tạo dãy chọn `0/1`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int       n;
    long long b;
    cin >> n >> b;

    vector<long long> a(n), c(n);  // Trọng lượng và giá trị từng đồ vật
    for (int i = 0; i < n; i++) {
        cin >> a[i] >> c[i];
    }

    long long bestVal  = 0;  // Giá trị tối ưu tìm được
    int       bestMask = 0;  // Bitmask tương ứng với tập chọn tối ưu

    // Duyệt toàn bộ 2^n tập con qua bitmask
    int totalSubsets = 1 << n;
    for (int mask = 0; mask < totalSubsets; mask++) {
        long long totalW = 0, totalC = 0;
        for (int i = 0; i < n; i++) {
            if ((mask & (1 << i)) != 0) {  // Bit i = 1 → đồ vật i được chọn
                totalW += a[i];
                totalC += c[i];
            }
        }
        // Cập nhật nếu hợp lệ và tốt hơn nghiệm hiện tại
        if (totalW <= b && totalC > bestVal) {
            bestVal  = totalC;
            bestMask = mask;
        }
    }

    // In tổng giá trị tối ưu
    cout << bestVal << '\n';
    // In vector chọn: x[i]=1 nếu đồ vật i được chọn, x[i]=0 nếu không
    for (int i = 0; i < n; i++) {
        bool selected = (bestMask & (1 << i)) != 0;
        cout << selected << " \n"[i == n - 1];
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `mask` | Tập con đang xét, bit `i` bật nghĩa là chọn đồ vật `i` |
| `totalW`, `totalC` | Tổng trọng lượng và giá trị của `mask` |
| `bestVal` | Giá trị lớn nhất của một tập hợp lệ |
| `bestMask` | Tập tạo ra `bestVal`, dùng để in dãy chọn |

1. Đọc `n`, `b` và `n` cặp `(a[i], c[i])`.
2. Với mỗi `mask` từ `0` đến `2^n - 1`, cộng trọng lượng và giá trị các bit được bật.
3. Nếu `totalW <= b` và `totalC > bestVal`, lưu `bestVal`, `bestMask`.
4. In `bestVal`.
5. In bit `0..n-1` của `bestMask`, cách nhau một dấu cách.

**Chỗ dễ sai**
- Không đặt `totalW = totalC = 0` trong mỗi vòng `mask`: tổng bị cộng dồn.
- Kiểm tra bit bằng `mask & i` thay vì `mask & (1 << i)`.
- Cập nhật đáp án trước khi kiểm tra sức chứa.
- Chỉ lưu `bestVal`, quên `bestMask`.
- In bit từ cao xuống thấp: đảo thứ tự đồ vật so với input.

**Độ phức tạp:** `O(n·2^n)` thời gian, `O(n)` bộ nhớ.

## Chạy tay

`n = 3`, `b = 8`, các cặp `(5,8)`, `(7,12)`, `(3,7)`:

| `mask` | Tập chọn | `totalW` | `totalC` | `bestVal` |
|---:|---|---:|---:|---:|
| `001` | `{1}` | 5 | 8 | 8 |
| `010` | `{2}` | 7 | 12 | 12 |
| `011` | `{1,2}` | 12 | 20 | 12 |
| `101` | `{1,3}` | 8 | 15 | 15 |

</details>

<details>
<summary><strong>Hướng 2 — quay lui chọn hoặc không chọn</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

int               n;
long long         capacity;
vector<long long> weight;
vector<long long> value;

long long bestValue = 0;
int       bestMask  = 0;

void search(int index, long long totalWeight, long long totalValue, int mask)
{
    if (totalWeight > capacity) {
        return;
    }

    if (index == n) {
        if (totalValue > bestValue || (totalValue == bestValue && mask < bestMask)) {
            bestValue = totalValue;
            bestMask  = mask;
        }
        return;
    }

    search(index + 1, totalWeight, totalValue, mask);
    search(index + 1,
           totalWeight + weight[index],
           totalValue + value[index],
           mask | (1 << index));
}

int main()
{
    cin >> n >> capacity;
    weight.resize(n);
    value.resize(n);
    for (int i = 0; i < n; i++) {
        cin >> weight[i] >> value[i];
    }

    search(0, 0, 0, 0);

    cout << bestValue << '\n';
    for (int i = 0; i < n; i++) {
        cout << ((bestMask >> i) & 1) << " \n"[i == n - 1];
    }
    return 0;
}
```

Mỗi đồ vật tạo hai nhánh lấy/không lấy, cắt ngay khi vượt sức chứa nên nhiều nhánh không cần đi tiếp. Cùng độ phức tạp xấu nhất `O(2^n)`, nhưng tốn thêm `O(n)` ngăn xếp đệ quy.

</details>
