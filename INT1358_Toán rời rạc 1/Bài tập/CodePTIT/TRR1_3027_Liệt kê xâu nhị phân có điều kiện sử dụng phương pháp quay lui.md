# TRR1_3027 — Liệt kê xâu nhị phân có điều kiện sử dụng phương pháp quay lui

> **Loại:** Quay lui · **Thuật toán:** Điền từng vị trí, vị trí khóa giữ nguyên giá trị · **Thời gian:** O(S·n) với S = 2^d · **Bộ nhớ:** O(n)

## Đề bài

> **TRR1_3027 - Liệt kê xâu nhị phân có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3027

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Liệt kê tất cả xâu nhị phân độ dài `n` theo thứ tự từ điển, trong đó `k` vị trí đã được cho trước giá trị.

**Giới hạn:** `3 ≤ n ≤ 100`, `k ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 1
2 1</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>0 1 0
0 1 1
1 1 0
1 1 1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Với n = 3 có 4 xâu nhị phân sắp xếp theo thứ tự từ điển thỏa mãn điều kiện có số 1 đứng ở vị trí thứ hai.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Gọi `d` là số vị trí tự do; mỗi vị trí tự do có 2 lựa chọn nên có `S = 2^d` xâu.
- Vị trí bị khóa giữ nguyên giá trị đọc từ input, không thử gì thêm.
- Vị trí tự do thử `0` rồi `1`, nên thứ tự từ điển có sẵn, không cần sắp xếp.
- `visited` ở đây nghĩa là "vị trí bị khóa", không phải "bit đã dùng", nên không hoàn tác.
- Nếu một vị trí xuất hiện nhiều lần, giá trị sau ghi đè giá trị trước.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int  n, k;
int  arr[105];      // Xâu nhị phân đang xây dựng
bool visited[105];  // visited[i] = true nếu vị trí i đã bị ràng buộc
bool flag;          // Đánh dấu đã tìm được ít nhất 1 xâu

void backtrack(int pos)
{
    if (pos > n) {
        // In xâu, cách nhau bởi dấu cách
        for (int i = 1; i <= n; i++) {
            cout << arr[i] << " \n"[i == n];
        }
        flag = true;
        return;
    }
    if (visited[pos]) {
        // Vị trí cố định → giữ nguyên giá trị đã gán, đi thẳng xuống
        backtrack(pos + 1);
        return;
    }
    // Vị trí tự do → thử 0 trước rồi 1, đảm bảo thứ tự từ điển tăng dần
    for (int i = 0; i <= 1; i++) {
        arr[pos] = i;
        backtrack(pos + 1);
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> k;
    for (int i = 1; i <= k; i++) {
        int u, v;
        cin >> u >> v;
        arr[u]     = v;     // Gán giá trị ràng buộc (đè nếu trùng vị trí)
        visited[u] = true;  // Đánh dấu vị trí không được thay đổi khi quay lui
    }
    backtrack(1);
    if (!flag) {
        cout << 0 << '\n';  // Không tìm được xâu nào thỏa mãn
    }
    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `arr[i]` | Bit đang đặt tại vị trí `i`, 1-based |
| `visited[i]` | Vị trí `i` bị khóa bởi input |
| `flag` | Đã in được ít nhất một xâu |
| `pos > n` | Đã đủ `n` bit, in xâu |

1. Đọc `n`, `k` và `k` cặp `(u, v)`.
2. Gán `arr[u] = v`, bật `visited[u]`.
3. Gọi `backtrack(1)`.
4. Vị trí khóa đi thẳng sang vị trí sau; vị trí tự do thử `0` rồi `1`.
5. In xâu khi đủ `n` bit; nếu không in được xâu nào thì in `0`.

**Chỗ dễ sai**
- Thử `1` trước `0`: đủ xâu nhưng sai thứ tự từ điển.
- Quên `return` ở vị trí khóa: chương trình thử thêm bit tại cùng vị trí và phá ràng buộc.
- Coi `visited` là "bit 1 đã dùng" rồi hoàn tác: làm mất ràng buộc.
- Ghi đè `arr[pos]` của vị trí khóa bằng vòng `0/1`.
- In `n` bit nhưng bắt đầu từ chỉ số 0: thừa giá trị rỗng.

**Độ phức tạp:** `O(S·n)` thời gian với `S = 2^d`, `O(n)` bộ nhớ.

## Chạy tay

`n = 3`, khóa `arr[2] = 1`:

| `pos` | Loại | `arr` | Hành động |
|---:|---|---|---|
| 1 | Tự do | `0 1 _` | thử 0 |
| 3 | Tự do | `0 1 0` | in `0 1 0` |
| 3 | Tự do | `0 1 1` | in `0 1 1` |
| 1 | Tự do | `1 1 _` | thử 1 |
| 3 | Tự do | `1 1 0` | in `1 1 0` |
| 3 | Tự do | `1 1 1` | in `1 1 1` |

</details>

<details>
<summary><strong>Hướng 2 — bộ đếm bit tự do</strong></summary>

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, k;
    cin >> n >> k;

    vector<int>  bits(n + 1, 0);
    vector<bool> fixedPosition(n + 1, false);

    for (int i = 0; i < k; i++) {
        int position, value;
        cin >> position >> value;
        bits[position]          = value;
        fixedPosition[position] = true;
    }

    vector<int> freePositions;
    for (int position = 1; position <= n; position++) {
        if (!fixedPosition[position]) {
            freePositions.push_back(position);
        }
    }

    while (true) {
        for (int position = 1; position <= n; position++) {
            cout << bits[position] << " \n"[position == n];
        }

        int index = static_cast<int>(freePositions.size()) - 1;
        while (index >= 0 && bits[freePositions[index]] == 1) {
            bits[freePositions[index]] = 0;
            index--;
        }

        if (index < 0) {
            break;
        }
        bits[freePositions[index]] = 1;
    }

    return 0;
}
```

Coi các vị trí tự do là một số nhị phân và cộng 1 sau mỗi lần in; bit khóa không bao giờ bị sửa. Không đệ quy, nhưng phải giữ `freePositions` tăng dần.

</details>
