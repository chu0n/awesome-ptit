# TRR1_3024 — Liệt kê hoán vị có điều kiện sử dụng phương pháp quay lui

> **Loại:** Quay lui · **Thuật toán:** Chọn giá trị cho từng vị trí, kiểm tra ràng buộc trước khi duyệt · **Thời gian:** O(n·n!) xấu nhất · **Bộ nhớ:** O(n)

> [!CAUTION]
> **CẢNH BÁO: Bài này không AC được trên CodePTIT hiện tại vì test case của CodePTIT bị sai.**
> **Bấm “Hiện code AC” bên dưới để xem code AC được.**
>
> <details>
> <summary><strong>Hiện code AC</strong></summary>
>
> ```cpp
> #include <bits/stdc++.h>
> using namespace std;
> #define ll   long long
> #define endl '\n'
> #define boost                         \
>     ios_base::sync_with_stdio(false); \
>     cin.tie(0);                       \
>     cout.tie(0)
> const int MOD = 1e9 + 7;
> 
> ll   arr[1000005];
> bool visited[1000005];
> ll   n, k, u, v, x, y, l, r;
> bool flag = false;
> 
> void backtrack(int pos)
> {
>     if (pos > n) {
>         if (!flag) {
>             for (int i = 1; i <= n; ++i) {
>                 cout << arr[i] << ' ';
>             }
>             cout << endl;
>             return;
>         }
>     }
>     for (int i = 1; i <= n; ++i) {
>         if (!visited[i]) {
>             flag       = true;
>             arr[pos]   = i;
>             visited[i] = true;
>             backtrack(pos + 1);
>             visited[i] = false;
>         }
>     }
> }
> 
> int32_t main()
> {
>     boost;
>     cin >> n >> k;
>     for (int i = 1; i <= k; ++i) {
>         cin >> u >> v;
>         arr[u]     = v;
>         visited[v] = true;
>     }
>     backtrack(k + 1);
>     if (flag)
>         cout << 0 << endl;
>     return 0;
> }
> ```
>
> </details>

## Đề bài

> **TRR1_3024 - Liệt kê hoán vị có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_3024

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Liệt kê tất cả hoán vị của `n` số nguyên dương đầu tiên, trong đó `k` vị trí đã được cho giá trị trước.

**Giới hạn:** `3 ≤ n ≤ 20`, `k ≤ n` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>3 1
1 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>2 1 3
2 3 1</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Với n = 3 có 2 hoán vị thỏa mãn điều kiện có số 2 đứng ở vị trí thứ nhất.</td>
    </tr>
  </tbody>
</table>

> **Ghi chú:** Bộ chấm CodePTIT không khớp với đề bài; xem cảnh báo ở trên. Lời giải dưới đây xử lý đúng các ràng buộc của đề.

## Ý tưởng

- Mỗi nghiệm là một hoán vị `a[1..n]`; ràng buộc "mỗi giá trị dùng một lần" do `used` quản lý.
- Vị trí cố định chỉ có đúng một lựa chọn; vị trí tự do thử mọi giá trị chưa dùng, tăng dần để giữ thứ tự từ điển.
- Nếu không xung đột, số nghiệm là `(n-m)!` với `m` là số vị trí cố định.
- Hai loại xung đột: một vị trí bị gán hai giá trị, một giá trị bị ép vào hai vị trí. Kiểm tra trước khi ghi.
- Nhánh cố định luôn `return` sau khi thử, kể cả khi giá trị bắt buộc đã bị dùng.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int  n, k;
int  a[25];           // Hoán vị đang xây dựng, a[i] = giá trị tại vị trí i
bool used[25];        // used[v] = true nếu giá trị v đã được dùng
int  fixedPos[25];    // fixedPos[i] = v nếu vị trí i bắt buộc là v, 0 nếu tự do
int  whereValue[25];  // whereValue[v] = i nếu giá trị v bị ép vào vị trí i, 0 nếu chưa
bool found = false;   // Đánh dấu có tìm được hoán vị nào không

void backtrack(int pos)
{
    // Đã điền xong tất cả n vị trí → in hoán vị
    if (pos > n) {
        for (int i = 1; i <= n; i++) {
            cout << a[i] << " \n"[i == n];
        }
        found = true;
        return;
    }

    if (fixedPos[pos] != 0) {
        // Vị trí pos có ràng buộc → chỉ được điền đúng giá trị fixedPos[pos]
        int val = fixedPos[pos];
        if (!used[val]) {  // Giá trị đó chưa bị dùng ở vị trí khác
            a[pos]    = val;
            used[val] = true;
            backtrack(pos + 1);
            used[val] = false;  // Quay lui: hoàn tác để tiếp tục
        }
        return;  // Dù thế nào cũng không thử giá trị khác
    }

    // Vị trí pos tự do → thử lần lượt tất cả giá trị 1..n chưa được dùng
    for (int val = 1; val <= n; val++) {
        if (!used[val]) {
            a[pos]    = val;
            used[val] = true;
            backtrack(pos + 1);
            used[val] = false;  // Quay lui: hoàn tác để thử giá trị tiếp theo
        }
    }
}

int main()
{
    cin >> n >> k;

    bool ok = true;  // Kiểm tra dữ liệu có mâu thuẫn không

    for (int i = 1; i <= k; i++) {
        int u, v;
        cin >> u >> v;

        // Mâu thuẫn loại 1: vị trí u đã bị gán giá trị khác trước đó
        if (fixedPos[u] != 0 && fixedPos[u] != v) {
            ok = false;
        }

        // Mâu thuẫn loại 2: giá trị v đã bị ép vào vị trí khác trước đó
        if (whereValue[v] != 0 && whereValue[v] != u) {
            ok = false;
        }

        fixedPos[u]   = v;
        whereValue[v] = u;
    }

    // Phát hiện mâu thuẫn → chắc chắn không có nghiệm
    if (!ok) {
        cout << 0 << '\n';
        return 0;
    }

    backtrack(1);
    if (!found) {
        cout << 0 << '\n';
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / nhánh | Vai trò |
|---|---|
| `fixedPos[i]` | Giá trị bắt buộc tại vị trí `i`, `0` nếu tự do |
| `whereValue[v]` | Vị trí bị ép của giá trị `v`, `0` nếu chưa |
| `used[v]` | Giá trị `v` đã dùng trong nhánh hiện tại |
| `found` | Đã in được ít nhất một hoán vị |

1. Đọc `n`, `k` và `k` cặp `(u, v)`.
2. Kiểm tra xung đột theo vị trí và theo giá trị, rồi ghi `fixedPos`, `whereValue`.
3. Nếu có xung đột, in `0` và kết thúc.
4. Gọi `backtrack(1)`; vị trí cố định đi thẳng một nhánh, vị trí tự do thử `1..n`.
5. In `n` giá trị khi `pos > n`; sau khi duyệt xong mà `found` vẫn `false` thì in `0`.

**Chỗ dễ sai**
- Bỏ `return` ở nhánh cố định: chương trình rơi xuống vòng tự do và phá ràng buộc.
- Đặt `return` vào trong `if (!used[val])`: trường hợp giá trị bị dùng lại rơi xuống vòng tự do.
- Gán `fixedPos[u] = v` trước khi kiểm tra: mất giá trị cũ, không phát hiện xung đột.
- Chỉ kiểm tra `fixedPos`, quên `whereValue`: bỏ sót một giá trị bị ép vào hai vị trí.
- Quên `used[val] = false` sau đệ quy: các nhánh sau thiếu giá trị.

**Độ phức tạp:** `O(n·n!)` thời gian xấu nhất, `O(n)` bộ nhớ.

## Chạy tay

`n = 3`, ràng buộc `fixedPos[1] = 2`:

| `pos` | Loại | Giá trị | Kết quả |
|---:|---|---:|---|
| 1 | Cố định | 2 | `2 _ _` |
| 2 | Tự do | 1 | `2 1 _` |
| 3 | Tự do | 3 | in `2 1 3` |
| 2 | Tự do | 3 | `2 3 _` |
| 3 | Tự do | 1 | in `2 3 1` |

</details>

<details>
<summary><strong>Hướng 2 — cố định rồi hoán vị giá trị tự do</strong></summary>

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int n, k;
    cin >> n >> k;

    vector<int> fixedPosition(n + 1, 0);
    vector<int> positionOfValue(n + 1, 0);
    bool        possible = true;

    for (int i = 0; i < k; i++) {
        int position, value;
        cin >> position >> value;

        if (fixedPosition[position] != 0 && fixedPosition[position] != value) {
            possible = false;
        }
        if (positionOfValue[value] != 0 && positionOfValue[value] != position) {
            possible = false;
        }

        fixedPosition[position] = value;
        positionOfValue[value]  = position;
    }

    if (!possible) {
        cout << 0 << '\n';
        return 0;
    }

    vector<bool> valueIsFixed(n + 1, false);
    for (int value = 1; value <= n; value++) {
        if (positionOfValue[value] != 0) {
            valueIsFixed[value] = true;
        }
    }

    vector<int> freePositions;
    vector<int> freeValues;
    for (int position = 1; position <= n; position++) {
        if (fixedPosition[position] == 0) {
            freePositions.push_back(position);
        }
    }
    for (int value = 1; value <= n; value++) {
        if (!valueIsFixed[value]) {
            freeValues.push_back(value);
        }
    }

    vector<int> permutation(n + 1, 0);
    for (int position = 1; position <= n; position++) {
        if (fixedPosition[position] != 0) {
            permutation[position] = fixedPosition[position];
        }
    }

    do {
        for (int i = 0; i < static_cast<int>(freePositions.size()); i++) {
            permutation[freePositions[i]] = freeValues[i];
        }

        for (int position = 1; position <= n; position++) {
            cout << permutation[position] << " \n"[position == n];
        }
    } while (next_permutation(freeValues.begin(), freeValues.end()));

    return 0;
}
```

Gán cứng các vị trí đã cố định rồi chỉ hoán vị tập giá trị tự do, nên không cần đệ quy và không sinh nhánh chết. Vẫn kiểm tra hai loại xung đột như bản chính.

</details>
