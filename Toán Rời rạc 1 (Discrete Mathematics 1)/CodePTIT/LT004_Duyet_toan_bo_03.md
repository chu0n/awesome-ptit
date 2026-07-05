## Đề bài
> **LT004 - Duyệt toàn bộ 03**
> 🔗 https://code.ptit.edu.vn/student/question/LT004

**Mô tả:** Một người du lịch muốn đi tham quan `n` thành phố đánh số từ 1 đến `n`. Xuất phát tại thành phố thứ 1, người du lịch muốn qua tất cả các thành phố còn lại mỗi thành phố đúng một lần rồi trở lại thành phố xuất phát. Biết rằng, chi phí đi lại từ thành phố `i` đến thành phố `j` (1 ≤ i, j ≤ n) là `c_ij`.

**Yêu cầu:** Sử dụng thuật toán duyệt toàn bộ, tìm một hành trình cho người đi du lịch sao cho tổng chi phí là nhỏ nhất.

**Giới hạn:** 
- Dòng đầu chứa số nguyên dương `n`, với `n ≤ 30`.
- Trong `n` dòng tiếp theo, dòng thứ `i` (1 ≤ i ≤ n) chứa `n` số nguyên dương `c_ij` (1 ≤ j ≤ n), mỗi số ≤ `10⁶` và `c_ii = 0`.
- Thời gian chạy ≤ 2 giây, bộ nhớ 65536 MB.

**Output:**
- Nếu bài toán có một nghiệm tối ưu:
  - Dòng đầu ghi ra số là tổng chi phí nhỏ nhất (`f*`) tìm được.
  - Dòng sau ghi ra `n` số nguyên dương là thứ tự các thành phố xuất hiện trong hành trình du lịch tương ứng tìm được.
- Nếu bài toán có nhiều hơn một nghiệm tối ưu:
  - Dòng đầu tiên ghi ra hai giá trị: giá trị tổng chi phí nhỏ nhất (`f*`) và số phương án tối ưu của bài toán, mỗi giá trị cách nhau một khoảng trắng.
  - Các dòng tiếp theo ghi các phương án tối ưu, mỗi phương án trên một dòng.

| | Input | Output |
|---|---|---|
| Test 1 | `4`<br>`0 1 8 9`<br>`7 0 9 2`<br>`1 9 0 8`<br>`7 8 2 0` | `6`<br>`1 2 4 3` |
| Test 2 | `4`<br>`0 1 1 5`<br>`1 0 5 1`<br>`1 5 0 1`<br>`5 1 1 0` | `4 2`<br>`1 2 4 3`<br>`1 3 4 2` |

> **Giải thích Test 1:** Có một hành trình tối ưu của người du lịch, tham quan các thành phố theo thứ tự 1,2,4,3, sau đó quay về thành phố 1 với tổng chi phí nhỏ nhất là 6.
> **Giải thích Test 2:** Có hai hành trình tối ưu đều có giá trị là 4: Hành trình tham quan các thành phố theo thứ tự 1,2,4,3 và quay về 1; Hành trình tham quan các thành phố theo thứ tự 1,3,4,2 và quay về 1.

---

## Overview

| Trường | Nội dung |
|---|---|
| **Tên bài** | Duyệt toàn bộ 03 |
| **Mã bài** | LT004 |
| **Loại bài** | Tối ưu hóa tổ hợp |
| **Mục đích** | Tìm hành trình du lịch (TSP) với chi phí nhỏ nhất, xuất phát và kết thúc tại thành phố 1 |
| **Kiến thức** | Hoán vị, bài toán người du lịch (TSP), duyệt toàn bộ |
| **Thuật toán** | Backtracking / Brute-force permutation — duyệt tất cả hoán vị của các thành phố 2..n, tính chi phí từng hành trình, lưu các hành trình đạt chi phí tối thiểu |

---

```cpp
#include <bits/stdc++.h>
using namespace std;

int n;
int c[31][31];              // Ma trận chi phí
int path[31];               // Hành trình hiện tại
bool visited[31];           // Đánh dấu thành phố đã thăm

int bestCost;               // Chi phí tối ưu
vector<vector<int>> bestPaths; // Lưu tất cả hành trình tối ưu

// Backtracking duyệt toàn bộ hành trình
// step: số thành phố đã chọn (kể cả thành phố 1 xuất phát)
// curCost: chi phí tích lũy đến bước hiện tại
void backtrack(int step, int curCost) {
    // Đã chọn đủ n thành phố → cộng chi phí về thành phố 1
    if (step == n) {
        int total = curCost + c[path[n-1]][1];
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
            int nextCost = curCost + c[path[step-1]][city];
            if (nextCost >= bestCost) continue;

            visited[city] = true;
            path[step] = city;
            backtrack(step + 1, nextCost);
            visited[city] = false;
        }
    }
}

int main() {
    ios::sync_with_stdio(false); cin.tie(NULL);

    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) cin >> c[i][j];

    // Khởi tạo: xuất phát từ thành phố 1
    bestCost = INT_MAX;
    memset(visited, false, sizeof(visited));
    visited[1] = true;
    path[0] = 1;

    backtrack(1, 0);

    // Xuất kết quả
    if (bestPaths.size() == 1) {
        // Chỉ một nghiệm tối ưu
        cout << bestCost << "\n";
        for (int i = 0; i < n; i++) cout << bestPaths[0][i] << " \n"[i==n-1];
    } else {
        // Nhiều nghiệm tối ưu
        cout << bestCost << " " << bestPaths.size() << "\n";
        for (auto& p : bestPaths) {
            for (int i = 0; i < n; i++) cout << p[i] << " \n"[i==n-1];
        }
    }

    return 0;
}
```

---

**Giải thích logic chính:**

- **Backtracking** duyệt tất cả hoán vị của thành phố `2..n`, xây dựng hành trình từng bước.
- **Cắt nhánh (pruning):** nếu chi phí tích lũy `nextCost >= bestCost` hiện tại thì bỏ ngay nhánh đó — giúp giảm đáng kể số trường hợp cần xét so với brute-force thuần túy.
- Khi duyệt xong `n` thành phố, cộng thêm chi phí quay về thành phố 1 để ra `total`.
- Lưu **tất cả** hành trình đạt `bestCost` vào `bestPaths` để xử lý trường hợp nhiều nghiệm tối ưu.
