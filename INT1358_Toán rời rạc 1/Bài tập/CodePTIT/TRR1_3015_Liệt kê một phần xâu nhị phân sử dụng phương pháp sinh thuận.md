# TRR1_3015 — Liệt kê một phần xâu nhị phân sinh thuận

> **Loại:** Sinh xâu nhị phân · **Thuật toán:** Cộng 1 nhị phân trên từng bit · **Thời gian:** O(t·n) · **Bộ nhớ:** O(n)

## Đề bài

> **TRR1_3015 - Liệt kê một phần xâu nhị phân sử dụng phương pháp sinh thuận**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3015

**Mô tả:** Cho hai số nguyên dương `n`, `t` và một xâu nhị phân độ dài `n`.
Tìm `t` xâu nhị phân liền kề **tiếp theo** theo thứ tự từ điển.

**Giới hạn:** `3 ≤ n, t ≤ 100` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5 2
1 0 0 0 1</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>1 0 0 1 0
1 0 0 1 1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Hai xâu nhị phân liên tiếp tiếp theo của xâu (1,0,0,0,1) theo thứ tự từ điển là (1,0,0,1,0) và (1,0,0,1,1).</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Với cùng độ dài, thứ tự từ điển của xâu nhị phân trùng thứ tự giá trị nhị phân; xâu kế tiếp là giá trị cộng 1.
- Cộng 1 tương đương: tìm bit `0` ngoài cùng bên phải, lật thành `1`, đặt mọi bit sau về `0`.
- Toàn bit `1` là xâu lớn nhất, không có xâu kế tiếp; in `0` cho các dòng còn lại.
- Không dùng số nguyên vì `n` tới 100, vượt mọi kiểu số có sẵn.
- Mỗi vòng cập nhật trước rồi mới in, nên xâu ban đầu không bị in.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Sinh xâu nhị phân kế tiếp (cộng 1 nhị phân từ phải sang trái)
// Trả về false nếu xâu đang là 1...1 (lớn nhất, không có kế tiếp)
bool nextBinary(vector<int>& a)
{
    int n = a.size();

    // Tìm bit 0 ngoài cùng bên phải để lật thành 1
    int i = n - 1;
    while (i >= 0 && a[i] == 1) {
        i--;  // Bỏ qua các bit 1 liên tiếp từ phải
    }
    if (i < 0) {
        return false;  // Toàn bit 1 → xâu lớn nhất, không có kế tiếp
    }

    a[i] = 1;  // Lật bit 0 thành 1 (vị trí nhớ dừng)
    for (int j = i + 1; j < n; j++) {
        a[j] = 0;  // Reset toàn bộ bit sau về 0
    }

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
        // Nếu không còn xâu tiếp theo → in 0 cho tất cả dòng còn lại
        if (!nextBinary(a)) {
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
| `a` | Xâu nhị phân hiện tại, chỉ số 0-based |
| `i` | Vị trí bit `0` ngoài cùng bên phải |
| `i < 0` | Xâu toàn bit `1`, không có xâu kế tiếp |
| `step`, `remain` | Đếm dòng đã in và số dòng còn thiếu |

1. Đọc `n`, `t`, rồi `n` bit.
2. Quét `i` từ cuối về, bỏ qua các bit `1`.
3. Nếu `i < 0`, in `0` cho `t - step` dòng còn lại và kết thúc.
4. Gán `a[i] = 1`, đặt `a[j] = 0` cho mọi `j > i`.
5. In xâu mới và lặp.

**Chỗ dễ sai**
- Quên reset đuôi: `1 0 0 1` phải thành `1 0 1 0`, không phải `1 0 1 1`.
- Viết `while (a[i] == 1 && i >= 0)`: truy cập `a[-1]` khi xâu toàn `1`.
- In trước khi gọi `nextBinary`: in cả xâu ban đầu và thiếu xâu cuối.
- Khi tràn chỉ in một dòng `0`: thiếu dòng.
- Cho `remain` bắt đầu từ `step + 1`: tổng số dòng ít hơn `t`.

**Độ phức tạp:** `O(t·n)` thời gian, `O(n)` bộ nhớ.

## Chạy tay

`n = 5`, `t = 2`, xâu `1 0 0 0 1`:

| Lượt | `i` | Sau khi lật và reset | Dòng in |
|---|---:|---|---|
| 0 | 3 | `1 0 0 1 0` | `1 0 0 1 0` |
| 1 | 4 | `1 0 0 1 1` | `1 0 0 1 1` |

</details>

<details>
<summary><strong>Hướng 2 — cộng 1 nhị phân bằng đệ quy</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

bool addOne(vector<int>& bits, int position)
{
    if (position < 0) {
        return false;
    }
    if (bits[position] == 0) {
        bits[position] = 1;
        for (int i = position + 1; i < static_cast<int>(bits.size()); i++) {
            bits[i] = 0;
        }
        return true;
    }
    return addOne(bits, position - 1);
}

int main()
{
    int n, t;
    cin >> n >> t;
    vector<int> bits(n);
    for (int i = 0; i < n; i++) {
        cin >> bits[i];
    }

    for (int step = 0; step < t; step++) {
        if (!addOne(bits, n - 1)) {
            for (int remain = step; remain < t; remain++) {
                cout << "0\n";
            }
            return 0;
        }

        for (int i = 0; i < n; i++) {
            cout << bits[i] << " \n"[i == n - 1];
        }
    }
    return 0;
}
```

Đệ quy mô phỏng trực tiếp chuỗi nhớ: gặp `0` thì lật, reset đuôi và dừng; gặp `1` thì gọi sang trái. Cùng chi phí `O(t·n)`, nhưng tốn thêm `O(n)` ngăn xếp đệ quy.

</details>
