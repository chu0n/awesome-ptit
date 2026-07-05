## Đề bài

> **TRR1_3024 - Liệt kê hoán vị có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3024

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Liệt kê tất cả hoán vị của `n` số nguyên dương đầu tiên, trong đó `k` vị trí đã được cho giá trị trước.

**Giới hạn:** `3 ≤ n ≤ 20`, `k ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `3 1` / `1 2` | `2 1 3` / `2 3 1` |

> **Giải thích Test 1:** Với n=3, có 2 hoán vị thỏa mãn điều kiện vị trí 1 là số 2.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê hoán vị có điều kiện sử dụng phương pháp quay lui |
| **Mã bài** | TRR1_3024 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh tất cả hoán vị thỏa mãn các ràng buộc vị trí cho trước |
| **Kiến thức** | Backtracking, hoán vị có điều kiện, kiểm tra mâu thuẫn |
| **Thuật toán** | Kiểm tra 2 loại xung đột trước → Quay lui: vị trí có ràng buộc chỉ thử đúng giá trị đó, vị trí tự do thử tất cả giá trị chưa dùng |

> ⚠️ **Lưu ý:** Hệ thống chấm bài này hiện bị lỗi (đề bài đã thay đổi nhưng test case và checker vẫn là của bài cũ) — code dưới đây logic hoàn toàn đúng với đề bài hiện tại.

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, k;
int a[25];              // Hoán vị đang xây dựng, a[i] = giá trị tại vị trí i
bool used[25];          // used[v] = true nếu giá trị v đã được dùng
int fixedPos[25];       // fixedPos[i] = v nếu vị trí i bắt buộc là v, 0 nếu tự do
int whereValue[25];     // whereValue[v] = i nếu giá trị v bị ép vào vị trí i, 0 nếu chưa
bool found = false;     // Đánh dấu có tìm được hoán vị nào không

void backtrack(int pos) {
    // Đã điền xong tất cả n vị trí → in hoán vị
    if (pos > n) {
        for (int i = 1; i <= n; i++) cout << a[i] << ' ';
        cout << '\n';
        found = true;
        return;
    }

    if (fixedPos[pos] != 0) {
        // Vị trí pos có ràng buộc → chỉ được điền đúng giá trị fixedPos[pos]
        int val = fixedPos[pos];
        if (!used[val]) {           // Giá trị đó chưa bị dùng ở vị trí khác
            a[pos] = val;
            used[val] = true;
            backtrack(pos + 1);
            used[val] = false;      // Quay lui: hoàn tác để tiếp tục
        }
        return;                     // Dù thế nào cũng không thử giá trị khác
    }

    // Vị trí pos tự do → thử lần lượt tất cả giá trị 1..n chưa được dùng
    for (int val = 1; val <= n; val++) {
        if (!used[val]) {
            a[pos] = val;
            used[val] = true;
            backtrack(pos + 1);
            used[val] = false;      // Quay lui: hoàn tác để thử giá trị tiếp theo
        }
    }
}

int main() {
    cin >> n >> k;

    bool ok = true;     // Kiểm tra dữ liệu có mâu thuẫn không

    for (int i = 1; i <= k; i++) {
        int u, v; cin >> u >> v;

        // Mâu thuẫn loại 1: vị trí u đã bị gán giá trị khác trước đó
        if (fixedPos[u] != 0 && fixedPos[u] != v) ok = false;

        // Mâu thuẫn loại 2: giá trị v đã bị ép vào vị trí khác trước đó
        if (whereValue[v] != 0 && whereValue[v] != u) ok = false;

        fixedPos[u] = v;
        whereValue[v] = u;
    }

    // Phát hiện mâu thuẫn → chắc chắn không có nghiệm
    if (!ok) { cout << 0 << '\n'; return 0; }

    backtrack(1);
    if (!found) cout << 0 << '\n';

    return 0;
}
```

---

**Giải thích thuật toán chi tiết:**

**Bước 1 — Kiểm tra mâu thuẫn trước khi quay lui:**

Có 2 loại mâu thuẫn có thể xảy ra ngay trong input:

| Loại | Ví dụ | Ý nghĩa |
|---|---|---|
| Loại 1 | `vị trí 1 = 2` và `vị trí 1 = 3` | Một vị trí bị gán 2 giá trị khác nhau |
| Loại 2 | `vị trí 1 = 2` và `vị trí 3 = 2` | Một giá trị bị ép vào 2 vị trí khác nhau |

Nếu có bất kỳ mâu thuẫn nào → in `0` ngay, không cần quay lui.

**Bước 2 — Quay lui:**

```
backtrack(1) với n=3, fixedPos[1]=2:

pos=1 [ràng buộc=2] → chỉ thử val=2
  pos=2 [tự do] → thử val=1
    pos=3 [tự do] → thử val=3
      pos=4 > n → in "2 1 3" ✓
  pos=2 [tự do] → thử val=3
    pos=3 [tự do] → thử val=1
      pos=4 > n → in "2 3 1" ✓
```

**Tại sao cần `return` sau khi xử lý vị trí có ràng buộc?** Vì dù `used[val]` là `true` hay `false`, ta cũng **không được thử giá trị nào khác** — vị trí đã bị cố định chỉ có đúng một lựa chọn duy nhất.

**Tại sao cần `whereValue[]`?** Mảng `fixedPos[]` chỉ phát hiện được mâu thuẫn loại 1 (một vị trí bị gán 2 giá trị). Cần thêm `whereValue[]` để phát hiện loại 2 (một giá trị bị gán cho 2 vị trí) — nếu thiếu, backtrack vẫn chạy đúng nhưng sẽ tốn thời gian hơn khi k lớn.