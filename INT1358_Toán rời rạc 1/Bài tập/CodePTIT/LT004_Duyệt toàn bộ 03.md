# LT004 — Duyệt toàn bộ 03

> **Loại:** Tối ưu hóa tổ hợp · **Thuật toán:** Quay lui cắt nhánh (TSP) · **Thời gian:** O(n!) xấu nhất · **Bộ nhớ:** O(n·K), K là số nghiệm tối ưu

## Đề bài
> **LT004 - Duyệt toàn bộ 03**
> 🔗 https://code.ptit.edu.vn/submit/LT004

**Mô tả:** Một người du lịch muốn đi tham quan `n` thành phố đánh số từ 1 đến `n`. Xuất phát tại thành phố thứ 1, người du lịch muốn qua tất cả các thành phố còn lại mỗi thành phố đúng một lần rồi trở lại thành phố xuất phát. Biết rằng, chi phí đi lại từ thành phố `i` đến thành phố `j` (1 ≤ i, j ≤ n) là `c_ij`.

**Yêu cầu:** Sử dụng thuật toán duyệt toàn bộ, tìm một hành trình cho người đi du lịch sao cho tổng chi phí là nhỏ nhất.

**Giới hạn:** 
- Dòng đầu chứa số nguyên dương `n`, với `n ≤ 30`.
- Trong `n` dòng tiếp theo, dòng thứ `i` (1 ≤ i ≤ n) chứa `n` số nguyên dương `c_ij` (1 ≤ j ≤ n), mỗi số ≤ `10⁶` và `c_ii = 0`.
- Thời gian chạy ≤ 2 giây, bộ nhớ 64 MB.

**Output:**
- Nếu bài toán có một nghiệm tối ưu:
  - Dòng đầu ghi ra số là tổng chi phí nhỏ nhất (`f*`) tìm được.
  - Dòng sau ghi ra `n` số nguyên dương là thứ tự các thành phố xuất hiện trong hành trình du lịch tương ứng tìm được.
- Nếu bài toán có nhiều hơn một nghiệm tối ưu:
  - Dòng đầu tiên ghi ra hai giá trị: giá trị tổng chi phí nhỏ nhất (`f*`) và số phương án tối ưu của bài toán, mỗi giá trị cách nhau một khoảng trắng.
  - Các dòng tiếp theo ghi các phương án tối ưu, mỗi phương án trên một dòng.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4
0 1 8 9
7 0 9 2
1 9 0 8
7 8 2 0</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>6
1 2 4 3</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có một hành trình tối ưu của người du lịch, tham quan các thành phố theo thứ tự 1,2,4,3, sau đó quay về thành phố 1 với tổng chi phí nhỏ nhất là 6.</td>
    </tr>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4
0 1 1 5
1 0 5 1
1 5 0 1
5 1 1 0</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4 2
1 2 4 3
1 3 4 2</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có hai hành trình tối ưu đều có giá trị là 4:<ul><li>Hành trình tham quan các thành phố theo thứ tự 1,2,4,3 và quay về 1.</li><li>Hành trình tham quan các thành phố theo thứ tự 1,3,4,2 và quay về 1.</li></ul></td>
    </tr>
  </tbody>
</table>

## Ý tưởng

- Hành trình là hoán vị của `2..n` đứng sau thành phố 1; chi phí `c[1][p2] + ... + c[pn][1]`.
- Có `(n-1)!` hành trình; quay lui dựng từng vị trí `path[step]`.
- Mọi cạnh giữa hai thành phố khác nhau đều dương, nên `nextCost >= bestCost` cắt được cả nhánh.
- Lưu mọi hành trình đạt `bestCost`; duyệt `city` tăng cho thứ tự từ điển.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

int  n;
int  c[31][31];                    // Ma trận chi phí
int  path[31]    = {1};            // Hành trình hiện tại, path[0] = 1
bool visited[31] = {false, true};  // Đánh dấu thành phố đã thăm

int                 bestCost = INT_MAX;  // Chi phí tối ưu
vector<vector<int>> bestPaths;           // Lưu tất cả hành trình tối ưu

// Backtracking duyệt toàn bộ hành trình
// step: số thành phố đã chọn (kể cả thành phố 1 xuất phát)
// curCost: chi phí tích lũy đến bước hiện tại
void backtrack(int step, int curCost)
{
    // Đã chọn đủ n thành phố → cộng chi phí về thành phố 1
    if (step == n) {
        int total = curCost + c[path[n - 1]][1];
        if (total < bestCost) {
            bestCost = total;
            bestPaths.clear();
            bestPaths.push_back(vector<int>(path, path + n));
        } else if (total == bestCost) {
            bestPaths.push_back(vector<int>(path, path + n));
        }
        return;
    }

    // Thử chọn thành phố tiếp theo
    for (int city = 2; city <= n; city++) {
        if (!visited[city]) {
            // Cắt nhánh: nếu chi phí hiện tại đã >= bestCost thì bỏ
            int nextCost = curCost + c[path[step - 1]][city];
            if (nextCost >= bestCost) {
                continue;
            }

            visited[city] = true;
            path[step]    = city;
            backtrack(step + 1, nextCost);
            visited[city] = false;
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> c[i][j];
        }
    }

    backtrack(1, 0);

    // Xuất kết quả
    if (bestPaths.size() == 1) {
        // Chỉ một nghiệm tối ưu
        cout << bestCost << "\n";
        for (int i = 0; i < n; i++) {
            cout << bestPaths[0][i] << " \n"[i == n - 1];
        }
    } else {
        // Nhiều nghiệm tối ưu
        cout << bestCost << " " << bestPaths.size() << "\n";
        for (auto& p : bestPaths) {
            for (int i = 0; i < n; i++) {
                cout << p[i] << " \n"[i == n - 1];
            }
        }
    }

    return 0;
}
```

### Giải thích nhanh

| Biến / thành phần | Vai trò |
|---|---|
| `c[i][j]` | Chi phí có hướng từ `i` đến `j` |
| `path`, `visited` | Hành trình đang dựng và thành phố đã dùng |
| `bestCost` | Tổng chi phí nhỏ nhất đã gặp |
| `bestPaths` | Mọi hành trình đạt `bestCost` |

1. Đọc `n` và ma trận `c`.
2. Đặt `path[0] = 1`, `visited[1] = true`, gọi `backtrack(1, 0)`.
3. Tại lá `step == n`, cộng `c[path[n-1]][1]` rồi cập nhật `bestCost` và `bestPaths`.
4. Thử mọi `city` chưa thăm; cắt nếu `nextCost >= bestCost`; đệ quy rồi bỏ đánh dấu.
5. In một nghiệm, hoặc `bestCost` kèm số nghiệm và từng hành trình.

**Chỗ dễ sai**
- `path[step-1]` là thành phố vừa đứng; `path[step]` chưa được ghi.
- Quên `visited[city] = false` sau đệ quy làm mất nhiều hành trình.
- Chỉ `clear()` khi `total < bestCost`; bằng nhau phải thêm vào.
- Thiếu `return` ở lá thì hàm chạy tiếp vòng lặp với `step == n`.
- Dùng sai chiều `c[city][path[step-1]]` hoặc `c[1][path[n-1]]`.

**Độ phức tạp:** `O(n!)` thời gian xấu nhất; `O(n·K)` bộ nhớ với `K` hành trình tối ưu, `K` tối đa `(n-1)!`.

## Chạy tay

Với ví dụ 1:

| Bước | Hành trình | Chi phí | `bestCost` |
|---|---:|---:|---:|
| 1 | `1 2 3 4` | 25 | 25 |
| 2 | `1 2 4 3` | 6 | 6 |
| 3 | nhánh `1 3 ...` | `c[1][3] = 8 >= 6`, cắt | 6 |

</details>

<details>
<summary><strong>Hướng 2 — Duyệt hoán vị bằng std::next_permutation</strong></summary>

```cpp
#include <algorithm>
#include <climits>
#include <iostream>
#include <numeric>
#include <vector>
using namespace std;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n;
    int cost[31][31];
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> cost[i][j];
        }
    }

    vector<int> middle(n - 1);
    iota(middle.begin(), middle.end(), 2);

    long long           bestCost = LLONG_MAX;
    vector<vector<int>> bestPaths;

    do {
        int         current   = 1;
        long long   totalCost = 0;
        vector<int> path;
        path.push_back(1);

        for (int city : middle) {
            totalCost += cost[current][city];
            current    = city;
            path.push_back(city);
        }
        totalCost += cost[current][1];

        if (totalCost < bestCost) {
            bestCost = totalCost;
            bestPaths.clear();
            bestPaths.push_back(path);
        } else if (totalCost == bestCost) {
            bestPaths.push_back(path);
        }
    } while (next_permutation(middle.begin(), middle.end()));

    if (bestPaths.size() == 1) {
        cout << bestCost << '\n';
        for (int i = 0; i < n; i++) {
            cout << bestPaths[0][i] << " \n"[i == n - 1];
        }
    } else {
        cout << bestCost << ' ' << bestPaths.size() << '\n';
        for (const vector<int>& path : bestPaths) {
            for (int i = 0; i < n; i++) {
                cout << path[i] << " \n"[i == n - 1];
            }
        }
    }
    return 0;
}
```

Cố định thành phố 1, sinh mọi hoán vị `2..n` rồi tính chi phí. Code ngắn hơn nhưng không cắt nhánh, nên chỉ hợp dữ liệu nhỏ.

</details>
