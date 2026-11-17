# TRR1_3016 — Liệt kê một phần xâu nhị phân sinh ngược

> **Loại:** Sinh xâu nhị phân · **Thuật toán:** Trừ 1 nhị phân, in `0` khi gặp toàn bit `0` · **Thời gian:** O(t·n) · **Bộ nhớ:** O(n)

## Đề bài

> **TRR1_3016 - Liệt kê một phần xâu nhị phân sử dụng phương pháp sinh ngược**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3016

**Mô tả:** Cho hai số nguyên dương `n`, `t` và một xâu nhị phân độ dài `n`.
Tìm `t` xâu nhị phân liền kề **tiếp theo** theo thứ tự **ngược** với thứ tự từ điển.

**Giới hạn:** `3 ≤ n, t ≤ 100` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5 2
1 0 1 0 1</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>1 0 1 0 0
1 0 0 1 1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Hai xâu nhị phân liên tiếp tiếp theo của xâu (1,0,1,0,1) theo thứ tự ngược với thứ tự từ điển là (1,0,1,0,0) và (1,0,0,1,1).</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Thứ tự ngược đi từ xâu lớn xuống xâu nhỏ, nên mỗi bước là trừ 1 trong biểu diễn nhị phân.
- Trừ 1: tìm bit `1` ngoài cùng bên phải, đổi thành `0`, đổi mọi bit phía sau thành `1`.
- Code gộp tìm và mượn: gặp bit `0` thì gán `1` ngay rồi lùi; gặp bit `1` thì dừng.
- Toàn bit `0` là xâu nhỏ nhất: vòng lặp đã biến nó thành toàn `1`, `id = -1`; in `0` rồi `continue`, lượt sau trừ tiếp từ `1...1`.
- Nhờ `continue`, chương trình luôn in đúng `t` dòng.

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

    int n, t;
    cin >> n >> t;
    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    for (int step = 0; step < t; step++) {
        // Duyệt từ phải: vừa tìm vừa reset bit 0 → 1 (phần "mượn" khi trừ 1)
        int id = n - 1;
        while (id >= 0 && a[id] == 0) {
            a[id] = 1;
            id--;
        }

        // Toàn bit 0 → không có xâu kế tiếp → in 0, tiếp tục (xâu thành 1...1 cho lần sau)
        if (id < 0) {
            cout << 0 << '\n';
            continue;
        }

        a[id] = 0;  // Lật bit 1 → 0 tại vị trí tìm được
        for (int i = 0; i < n; i++) {
            cout << a[i] << " \n"[i == n - 1];
        }
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `a` | Xâu hiện tại, chỉ số 0-based |
| `id` | Vị trí bit `1` ngoài cùng bên phải |
| `id < 0` | Xâu trước khi trừ là toàn `0`; in `0` rồi `continue` |
| `step` | Số lượt đã chạy, luôn đúng `t` lượt |

1. Đọc `n`, `t`, rồi `n` bit.
2. Đặt `id = n - 1`; trong khi `a[id] == 0`, gán `a[id] = 1` và lùi `id`.
3. Nếu `id < 0`, in `0`, `continue` sang lượt sau (lúc này `a` toàn `1`).
4. Ngược lại gán `a[id] = 0`.
5. In `n` bit, mỗi bit kèm một dấu cách, rồi xuống dòng.

**Chỗ dễ sai**
- Viết `a[id] == 0 && id >= 0`: truy cập `a[-1]`.
- Đổi bit `1` thành `0` ngay trong vòng `while` rồi vẫn lùi: mượn sai vị trí.
- Dùng `break` hoặc `return` khi `id < 0`: thiếu các dòng còn lại.
- Quên `continue` sau khi in `0`: in thêm xâu toàn `1` trên cùng lượt.
- Đổi hành vi thành dừng hẳn ở xâu `0`: khác code hiện tại, các lượt sau sẽ lặp lại `0`.

**Độ phức tạp:** `O(t·n)` thời gian, `O(n)` bộ nhớ.

## Chạy tay

`n = 5`, `t = 2`, xâu `1 0 1 0 1`:

| Lượt | Diễn biến | Xâu sau | Dòng in |
|---|---|---|---|
| 0 | `id = 4`, `a[4] = 1` nên dừng ngay | `1 0 1 0 0` | `1 0 1 0 0 ` |
| 1 | `a[4]`, `a[3]` về `1`, `id = 2`; lật `a[2] = 0` | `1 0 0 1 1` | `1 0 0 1 1 ` |

Với xâu `0 0 0`, lượt đầu in `0` và để lại `1 1 1`; lượt sau trừ tiếp thành `1 1 0`.

</details>

<details>
<summary><strong>Hướng 2 — trừ 1 nhị phân bằng đệ quy</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

bool subtractOne(vector<int>& bits, int position)
{
    if (position < 0) {
        return false;
    }
    if (bits[position] == 1) {
        bits[position] = 0;
        for (int i = position + 1; i < static_cast<int>(bits.size()); i++) {
            bits[i] = 1;
        }
        return true;
    }
    return subtractOne(bits, position - 1);
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
        if (!subtractOne(bits, n - 1)) {
            fill(bits.begin(), bits.end(), 1);
            cout << "0\n";
            continue;
        }

        for (int i = 0; i < n; i++) {
            cout << bits[i] << " \n"[i == n - 1];
        }
    }
    return 0;
}
```

Đệ quy đi sang trái khi gặp `0`; gặp `1` thì đặt `0`, đuôi thành `1` và dừng. Nhánh thất bại phải `fill` lại toàn `1` để giữ đúng hành vi `continue` của bản chính; code dùng `fill` nên cần `<algorithm>` nếu máy chấm không tự include.

</details>
