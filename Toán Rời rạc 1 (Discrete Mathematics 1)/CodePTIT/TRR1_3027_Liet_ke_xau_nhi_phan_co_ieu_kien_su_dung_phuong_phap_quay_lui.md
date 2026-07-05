## Đề bài

> **TRR1_3027 - Liệt kê xâu nhị phân có điều kiện sử dụng phương pháp quay lui**
> 🔗 https://code.ptit.edu.vn/student/question/TRR1_3027

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Liệt kê tất cả xâu nhị phân độ dài `n` theo thứ tự từ điển, trong đó `k` vị trí đã được cho trước giá trị.

**Giới hạn:** `3 ≤ n ≤ 100`, `k ≤ n` — thời gian chạy ≤ 1 giây.

| | Input | Output |
|---|---|---|
| Test 1 | `3 1` / `2 1` | `0 1 0` / `0 1 1` / `1 1 0` / `1 1 1` |

> **Giải thích Test 1:** Với n=3, có 4 xâu nhị phân thỏa mãn điều kiện vị trí 2 là số 1.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Liệt kê xâu nhị phân có điều kiện sử dụng phương pháp quay lui |
| **Mã bài** | TRR1_3027 |
| **Loại bài** | Toán rời rạc — Liệt kê tổ hợp |
| **Mục đích** | Sinh tất cả xâu nhị phân thỏa mãn các ràng buộc vị trí cho trước |
| **Kiến thức** | Backtracking, xâu nhị phân có điều kiện |
| **Thuật toán** | Quay lui: nếu vị trí đã `visited` thì giữ nguyên giá trị và đi tiếp, nếu tự do thì thử `0` rồi `1` để đảm bảo thứ tự từ điển |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, k;
int arr[105];           // Xâu nhị phân đang xây dựng
bool visited[105];      // visited[i] = true nếu vị trí i đã bị ràng buộc
bool flag;              // Đánh dấu đã tìm được ít nhất 1 xâu

void backtrack(int pos) {
    if (pos > n) {
        // In xâu với trailing space (hệ thống chấp nhận)
        for (int i = 1; i <= n; i++) cout << arr[i] << ' ';
        cout << '\n';
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

int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= k; i++) {
        int u, v; cin >> u >> v;
        arr[u] = v;         // Gán giá trị ràng buộc (đè nếu trùng vị trí)
        visited[u] = true;  // Đánh dấu vị trí không được thay đổi khi quay lui
    }
    backtrack(1);
    if (!flag) cout << 0 << '\n';   // Không tìm được xâu nào thỏa mãn
    return 0;
}
```

---

**Giải thích thuật toán:**

```
visited[2]=true, arr[2]=1

pos=1 [tự do]: i=0 → arr[1]=0
  pos=2 [visited]: giữ arr[2]=1, đi tiếp
    pos=3 [tự do]: i=0 → "0 1 0" ✓
                   i=1 → "0 1 1" ✓
pos=1 [tự do]: i=1 → arr[1]=1
  pos=2 [visited]: giữ arr[2]=1, đi tiếp
    pos=3 [tự do]: i=0 → "1 1 0" ✓
                   i=1 → "1 1 1" ✓
```

> **Bài học:** Không cần kiểm tra mâu thuẫn phức tạp — nếu input có 2 ràng buộc trùng vị trí, giá trị sau **đè lên** giá trị trước, backtrack vẫn chạy đúng vì chỉ một giá trị duy nhất được lưu tại mỗi vị trí.