# TRR1_3026 — Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui

> **Loại:** Quay lui · **Thuật toán:** Cố định `a[k] = t`, chọn `k-1` phần tử tăng dần từ `1..t-1` · **Thời gian:** O(C(t-1,k-1)·k) · **Bộ nhớ:** O(k)

## Đề bài

> **TRR1_3026 - Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3026

**Mô tả:** Cho ba số nguyên dương `n`, `k` và `t`. Liệt kê tất cả tổ hợp gồm `k` phần tử của `n` số nguyên dương đầu tiên, trong đó phần tử thứ `k` (cuối cùng) nhận giá trị `t`.

**Giới hạn:** `3 ≤ n ≤ 30`, `k, t ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5 3 4</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>1 2 4
1 3 4
2 3 4</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Với n = 5 có 3 tổ hợp gồm k = 3 phần tử thỏa mãn điều kiện với số 4 đứng ở vị trí thứ k.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Tổ hợp là dãy tăng; `a[k] = t` kéo theo mọi phần tử trước đều nhỏ hơn `t`, nên chỉ chọn trong `1..t-1`.
- Số nghiệm là `C(t-1, k-1)`; bằng 0 khi `t < k`.
- Cận trên `val <= t - (k - pos)`: phải chừa đủ `k - pos` số nằm giữa `val` và `t`.
- Vị trí cuối không cần thử: khi `pos == k`, gán thẳng `a[k] = t` rồi in.
- Đệ quy truyền `val + 1` để giữ dãy tăng; không cần mảng `used`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int  n, k, t;
int  a[35];
bool found = false;

void backtrack(int pos, int start)
{
    // Đã điền xong k-1 vị trí đầu → ghép phần tử cuối cố định t
    if (pos == k) {
        a[k] = t;
        for (int i = 1; i <= k; i++) {
            cout << a[i] << " \n"[i == k];
        }
        found = true;
        return;
    }
    // Cắt nhánh: chọn val < t và còn đủ phần tử cho các vị trí sau
    for (int val = start; val <= t - (k - pos); val++) {
        a[pos] = val;
        backtrack(pos + 1, val + 1);
    }
}

int main()
{
    cin >> n >> k >> t;

    // Kiểm tra hợp lệ: cần ít nhất k-1 số nhỏ hơn t
    if (t < k) {
        cout << 0;
        return 0;
    }

    backtrack(1, 1);  // Bắt đầu từ vị trí 1, giá trị từ 1
    if (!found) {
        cout << 0;
    }
    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `a` | Tổ hợp đang xây dựng, chỉ số 1-based |
| `pos` | Vị trí cần điền, dừng khi `pos == k` |
| `start` | Giá trị nhỏ nhất được thử tại `pos` |
| `t - (k - pos)` | Cận trên để còn đủ số nhỏ hơn `t` |
| `found` | Đã in được ít nhất một tổ hợp |

1. Đọc `n`, `k`, `t`; nếu `t < k` thì in `0`.
2. Gọi `backtrack(1, 1)`.
3. Khi `pos == k`, gán `a[k] = t`, in tổ hợp và bật `found`.
4. Ngược lại thử `val` từ `start` đến `t - (k - pos)`, gọi tiếp với `val + 1`.
5. Sau khi duyệt xong, nếu `found` vẫn `false` thì in `0`.

**Chỗ dễ sai**
- Dùng `pos > k` thay vì `pos == k`: vị trí cuối bị coi là tự do và có thể khác `t`.
- Dùng `n` làm cận trên thay vì `t`: sinh giá trị lớn hơn `t`, phá điều kiện cuối.
- Kiểm tra `t <= k` thay vì `t < k`: mất trường hợp `t = k` vẫn có nghiệm.
- Dùng `t - (k - pos) + 1`: có thể chọn `val = t`, trùng phần tử cuối.
- Quên `a[k] = t` tại lá: in ra giá trị 0 chưa gán.

**Độ phức tạp:** `O(C(t-1,k-1)·k)` thời gian, `O(k)` bộ nhớ.

## Chạy tay

`n = 5`, `k = 3`, `t = 4`, cận trên tại `pos = 1` là `4 - 2 = 2`:

| `pos` | `start` | `val` | Tổ hợp |
|---:|---:|---:|---|
| 1 | 1 | 1 | `1 _ _` |
| 2 | 2 | 2 | `1 2 4` |
| 2 | 2 | 3 | `1 3 4` |
| 1 | 1 | 2 | `2 _ _` |
| 2 | 3 | 3 | `2 3 4` |

</details>

<details>
<summary><strong>Hướng 2 — sinh tổ hợp bằng vòng lặp</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

void printCombination(const vector<int>& prefix, int last)
{
    for (int value : prefix) {
        cout << value << ' ';
    }
    cout << last << '\n';
}

int main()
{
    int n, k, t;
    cin >> n >> k >> t;

    int remaining = k - 1;
    if (t > n || k > n || t < k) {
        cout << 0;
        return 0;
    }

    vector<int> values(remaining);
    for (int i = 0; i < remaining; i++) {
        values[i] = 1 + i;
    }

    while (true) {
        printCombination(values, t);

        int index = remaining - 1;
        while (index >= 0) {
            int upper = (t - 1) - (remaining - 1 - index);
            if (values[index] < upper) {
                break;
            }
            index--;
        }

        if (index < 0) {
            break;
        }

        values[index]++;
        for (int i = index + 1; i < remaining; i++) {
            values[i] = values[i - 1] + 1;
        }
    }

    return 0;
}
```

Chỉ sinh `k-1` phần tử đầu trong `1..t-1` rồi in kèm `t`, nên không cần đệ quy; cận trên phải dùng `t-1` thay vì `n`.

</details>
