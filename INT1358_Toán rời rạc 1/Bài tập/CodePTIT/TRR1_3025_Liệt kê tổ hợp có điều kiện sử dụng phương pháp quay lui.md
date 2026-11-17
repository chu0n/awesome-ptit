# TRR1_3025 — Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui

> **Loại:** Quay lui · **Thuật toán:** Cố định `a[1] = t`, chọn `k-1` phần tử tăng dần từ `t+1..n` · **Thời gian:** O(C(n-t,k-1)·k) · **Bộ nhớ:** O(k)

## Đề bài

> **TRR1_3025 - Liệt kê tổ hợp có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3025

**Mô tả:** Cho ba số nguyên dương `n`, `k` và `t`. Liệt kê tất cả tổ hợp gồm `k` phần tử của `n` số nguyên dương đầu tiên, trong đó phần tử thứ nhất nhận giá trị `t`.

**Giới hạn:** `3 ≤ n ≤ 30`, `k, t ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5 3 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>2 3 4
2 3 5
2 4 5</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Với n = 5 có 3 tổ hợp gồm k = 3 phần tử thỏa mãn điều kiện với số 2 đứng ở vị trí thứ nhất.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Tổ hợp là dãy tăng `a[1] < a[2] < ... < a[k]`; điều kiện tăng đã thay cho mảng `used`.
- Cố định `a[1] = t`, bài toán còn lại là chọn `k-1` phần tử từ `t+1..n`.
- Số nghiệm là `C(n-t, k-1)`; bằng 0 khi không đủ chỗ sau `t`.
- Cận trên `val <= n - (k - pos)`: sau khi chọn `val`, phải còn đủ `k - pos` số lớn hơn.
- Đệ quy truyền `val + 1` để giữ dãy tăng và không sinh trùng.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int  n, k, t;
int  a[35];  // Tổ hợp đang xây dựng
bool found = false;

void backtrack(int pos, int start)
{
    if (pos > k) {
        for (int i = 1; i <= k; i++) {
            cout << a[i] << " \n"[i == k];
        }
        found = true;
        return;
    }
    // Cắt nhánh: số phần tử còn lại đủ để điền không?
    for (int val = start; val <= n - (k - pos); val++) {
        a[pos] = val;
        backtrack(pos + 1, val + 1);  // Chọn từ val+1 để tổ hợp tăng dần
    }
}

int main()
{
    cin >> n >> k >> t;

    // Phần tử đầu tiên bắt buộc là t, kiểm tra tính hợp lệ
    if (t > n || k > n) {
        cout << 0;
        return 0;
    }

    a[1] = t;
    backtrack(2, t + 1);  // Bắt đầu từ vị trí 2, giá trị từ t+1 trở đi

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
| `pos` | Vị trí cần điền |
| `start` | Giá trị nhỏ nhất được thử tại `pos` |
| `n - (k - pos)` | Cận trên để còn đủ số cho các vị trí sau |
| `found` | Đã in được ít nhất một tổ hợp |

1. Đọc `n`, `k`, `t`; nếu `t > n` hoặc `k > n` thì in `0`.
2. Gán `a[1] = t`, gọi `backtrack(2, t + 1)`.
3. Nếu `pos > k`, in tổ hợp và bật `found`.
4. Ngược lại thử `val` từ `start` đến `n - (k - pos)`, gọi tiếp với `val + 1`.
5. Sau khi duyệt xong, nếu `found` vẫn `false` thì in `0`.

**Chỗ dễ sai**
- Gọi `backtrack(2, t)` thay vì `t + 1`: cho phép chọn lại `t`, phá điều kiện tăng.
- Dùng `val < n - (k - pos)` thay vì `<=`: mất các tổ hợp có phần tử cuối bằng `n`.
- Truyền `val` thay vì `val + 1`: sinh dãy có phần tử lặp.
- Dừng ở `pos >= k` thay vì `pos > k`: in thiếu phần tử cuối.
- Quên `found = true`: in thêm `0` sau các nghiệm.

**Độ phức tạp:** `O(C(n-t,k-1)·k)` thời gian, `O(k)` bộ nhớ.

## Chạy tay

`n = 5`, `k = 3`, `t = 2`, cận trên tại `pos = 2` là `5 - 1 = 4`:

| `pos` | `start` | `val` | Tổ hợp |
|---:|---:|---:|---|
| 2 | 3 | 3 | `2 3 _` |
| 3 | 4 | 4 | `2 3 4` |
| 3 | 4 | 5 | `2 3 5` |
| 2 | 3 | 4 | `2 4 _` |
| 3 | 5 | 5 | `2 4 5` |

</details>

<details>
<summary><strong>Hướng 2 — sinh tổ hợp bằng vòng lặp</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

void printCombination(int first, const vector<int>& rest)
{
    cout << first;
    for (int value : rest) {
        cout << ' ' << value;
    }
    cout << '\n';
}

int main()
{
    int n, k, t;
    cin >> n >> k >> t;

    int remaining = k - 1;
    if (t > n || k > n || t > n - remaining) {
        cout << 0;
        return 0;
    }

    vector<int> values(remaining);
    for (int i = 0; i < remaining; i++) {
        values[i] = t + 1 + i;
    }

    while (true) {
        printCombination(t, values);

        int index = remaining - 1;
        while (index >= 0) {
            int upper = n - (remaining - 1 - index);
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

Bắt đầu từ dãy nhỏ nhất `t+1..t+k-1`, tăng vị trí phải nhất còn tăng được rồi điền đuôi nhỏ nhất; không dùng đệ quy và chỉ giữ `k-1` phần tử.

</details>
