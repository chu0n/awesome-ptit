# TRR1_4007 — Bài toán người du lịch 02

> **Loại:** Tối ưu hóa tổ hợp · **Thuật toán:** Nhánh cận (TSP) · **Thời gian:** O(n!·n²) xấu nhất · **Bộ nhớ:** O(n²)

## Đề bài

> **TRR1_4007 - Bài toán người du lịch 02**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_4007

**Mô tả:** Xuất phát tại thành phố 1, đi qua tất cả `n` thành phố mỗi nơi đúng một lần rồi quay về. Chi phí từ `i` đến `j` là `c[i][j]`. Tìm hành trình có tổng chi phí nhỏ nhất.

**Giới hạn:** `n ≤ 30`, `c[i][j] ≤ 10⁶` — thời gian chạy ≤ 1 giây.

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
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>5
1 2 4 3</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Người du lịch tham quan các thành phố theo thứ tự 1, 2, 4, 3 và quay về 1 với tổng chi phí nhỏ nhất là 5.</td>
    </tr>
  </tbody>
</table>

> **Mẫu trên trang đề có mâu thuẫn:** Bảng được chép theo ảnh trang đề. Hành trình `1 → 2 → 4 → 3 → 1` có chi phí `1 + 2 + 2 + 1 = 6`, không phải 5. Duyệt mọi hành trình cũng cho giá trị tối ưu 6. Giữ nguyên mẫu công bố; code bên dưới tính theo ma trận.

## Ý tưởng

Mỗi hành trình là một hoán vị `(1, p₂, ..., pₙ)` và phải đóng vòng:

$$\text{cost} = c[1][p_2] + c[p_2][p_3] + \cdots + c[p_n][1].$$

Quay lui chọn thành phố tiếp theo, giữ `visited` để không lặp. Tại trạng thái đang đứng ở `u = path[step]`, cận dưới cộng ba phần:

- chi phí đã đi `currentCost`;
- cạnh ra rẻ nhất của `u`;
- cạnh ra rẻ nhất của từng thành phố chưa thăm.

Mỗi thành phố còn lại chắc chắn phải có một cạnh ra, nên cạnh thật của nó không rẻ hơn cạnh rẻ nhất. Các cạnh trong cận được chọn độc lập, có thể trùng đích hoặc cùng quay về 1, nên cận luôn thấp hơn hoặc bằng chi phí thật. Vì vậy cắt khi `bound >= bestCost` không bỏ sót hành trình tốt hơn.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <climits>
#include <iostream>
using namespace std;

int       n;
int       cost[31][31];
int       path[31] = {0, 1};
int       bestPath[31];
bool      visited[31] = {false, true};
long long bestCost    = LLONG_MAX;

// Tìm cạnh rẻ nhất từ một thành phố chưa thăm.
int cheapestEdge(int from)
{
    int cheapest = INT_MAX;
    for (int to = 1; to <= n; to++) {
        bool canGo = from != to && (!visited[to] || to == 1);
        if (canGo && cost[from][to] < cheapest) {
            cheapest = cost[from][to];
        }
    }
    return cheapest;
}

// Cận dưới = chi phí đã đi + cạnh ra rẻ nhất của thành phố hiện tại
// và của mỗi thành phố chưa thăm.
long long estimateTotalCost(int step, long long currentCost)
{
    long long estimatedCost = currentCost + cheapestEdge(path[step]);
    for (int city = 1; city <= n; city++) {
        if (!visited[city]) {
            estimatedCost += cheapestEdge(city);
        }
    }
    return estimatedCost;
}

// path[step] là thành phố hiện tại; currentCost là chi phí đã đi.
void travel(int step, long long currentCost)
{
    if (currentCost >= bestCost) {
        return;
    }

    if (step == n) {
        // Đã thăm đủ n thành phố, cộng cạnh quay về thành phố 1.
        long long totalCost = currentCost + cost[path[n]][1];
        if (totalCost < bestCost) {
            bestCost = totalCost;
            for (int i = 1; i <= n; i++) {
                bestPath[i] = path[i];
            }
        }
        return;
    }

    if (estimateTotalCost(step, currentCost) >= bestCost) {
        return;  // Ngay cả ước lượng rẻ nhất cũng không cải thiện được.
    }

    for (int city = 2; city <= n; city++) {
        if (visited[city]) {
            continue;
        }

        path[step + 1] = city;
        visited[city]  = true;

        long long nextCost = currentCost + cost[path[step]][city];
        travel(step + 1, nextCost);

        visited[city] = false;  // Trả thành phố để thử hành trình khác.
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> cost[i][j];
        }
    }

    travel(1, 0);

    cout << bestCost << '\n';
    for (int i = 1; i <= n; i++) {
        cout << bestPath[i] << " \n"[i == n];
    }
    return 0;
}
```

### Giải thích code

**Trạng thái chính**

| Biến | Ý nghĩa |
|---|---|
| `step` | Số thành phố đã đi, thành phố hiện tại là `path[step]` |
| `path[s]` | Thành phố ở vị trí `s` của hành trình đang thử |
| `visited[i]` | Thành phố `i` đã có trong nhánh hiện tại |
| `currentCost` | Chi phí các cạnh đã đi, chưa gồm cạnh quay về 1 |
| `bestCost` | Chi phí hành trình hoàn chỉnh tốt nhất đã biết |
| `bestPath` | Bản sao hành trình ứng với `bestCost` |

`travel(step, currentCost)` nghĩa là: đã đi qua `step` thành phố, đang đứng ở `path[step]`, đã trả `currentCost`.

**Luồng chạy**

1. Đọc `n` và ma trận `cost`.
2. Đặt `bestCost = LLONG_MAX`, `path[1] = 1`, `visited[1] = true`.
3. Gọi `travel(1, 0)`.
4. Tại mỗi nút: cắt nếu `currentCost >= bestCost`; nếu `step == n` thì cộng cạnh quay về 1, cập nhật kỷ lục rồi quay lui.
5. Tính cận dưới; cắt nếu cận `>= bestCost`.
6. Thử từng thành phố `2..n` chưa thăm: ghi vào `path`, đánh dấu, đệ quy, rồi bỏ đánh dấu.
7. In `bestCost` và `bestPath[1..n]`.

**Hàm `cheapestEdge`**

```cpp
int cheapestEdge(int from)
{
    int cheapest = INT_MAX;
    for (int to = 1; to <= n; to++) {
        bool canGo = from != to && (!visited[to] || to == 1);
        if (canGo && cost[from][to] < cheapest) {
            cheapest = cost[from][to];
        }
    }
    return cheapest;
}
```

Hàm chỉ lấy cạnh rời `from` đến thành phố chưa thăm hoặc đến thành phố 1. `from != to` loại cạnh tự đi. Khi `step < n` luôn còn ít nhất một thành phố chưa thăm, nên `cheapest` không giữ `INT_MAX`. Việc cho phép đi về 1 khi vẫn còn thành phố khác là cố ý: nó làm cận nhỏ hơn, không làm cận vượt chi phí thật.

**Cận dưới**

```cpp
long long estimateTotalCost(int step, long long currentCost)
{
    long long estimatedCost = currentCost + cheapestEdge(path[step]);
    for (int city = 1; city <= n; city++) {
        if (!visited[city]) {
            estimatedCost += cheapestEdge(city);
        }
    }
    return estimatedCost;
}
```

Cận cộng cạnh ra của thành phố hiện tại và của từng thành phố chưa thăm. Các thành phố đã thăm, trừ thành phố hiện tại, đã trả cạnh ra của chúng trong `currentCost`.

**Chốt đáp án tại lá**

```cpp
if (step == n) {
    // Đã thăm đủ n thành phố, cộng cạnh quay về thành phố 1.
    long long totalCost = currentCost + cost[path[n]][1];
    if (totalCost < bestCost) {
        bestCost = totalCost;
        for (int i = 1; i <= n; i++) {
            bestPath[i] = path[i];
        }
    }
    return;
}
```

Khi `step == n`, `path[1..n]` đã đủ và phân biệt. Phải cộng `cost[path[n]][1]` mới có chi phí chu trình. Chỉ cập nhật khi nhỏ hơn thật sự; hành trình bằng kỷ lục không cần lưu.

**Thử thành phố và hoàn tác**

```cpp
for (int city = 2; city <= n; city++) {
    if (visited[city]) {
        continue;
    }

    path[step + 1] = city;
    visited[city]  = true;

    long long nextCost = currentCost + cost[path[step]][city];
    travel(step + 1, nextCost);

    visited[city] = false;  // Trả thành phố để thử hành trình khác.
}
```

Thành phố 1 cố định ở đầu nên vòng lặp chỉ thử `2..n`. `visited[city] = false` sau đệ quy là bắt buộc: nhánh khác phải dùng lại được thành phố này.

**Vì sao cắt đúng**

Với mọi cách hoàn thành hành trình, mỗi thành phố còn lại đóng góp đúng một cạnh ra. Cạnh thật của thành phố `v` không rẻ hơn `cheapestEdge(v)`, nên tổng thật không nhỏ hơn cận. Nếu cận đã `>= bestCost` thì mọi hành trình trong nhánh đều không thể tốt hơn kỷ lục. Dấu `>=` bỏ cả nhánh đồng hạng, phù hợp vì đề chỉ cần một hành trình tối ưu.

**Độ phức tạp:** xấu nhất `O(n!·n²)` thời gian, `O(n²)` bộ nhớ; cận chỉ giảm số nhánh thực tế.

## Chạy tay

Ví dụ 3 thành phố với các cạnh `1→2 = 2`, `1→3 = 5`, `2→1 = 7`, `2→3 = 3`, `3→1 = 4`, `3→2 = 2`:

| Hành trình / nhánh | Phép tính | Chi phí | Kết quả |
|---|---|---:|---|
| `1 → 2 → 3 → 1` | `2 + 3 + 4` | 9 | `bestCost = 9`, lưu `1 2 3` |
| `1 → 3 → 2 → 1` | `5 + 2 + 7` | 14 | Không tốt hơn |
| Cận tại `1 → 3` | `5 + 2 + 7` | 14 | `14 >= 9`, cắt nhánh |

Đáp án cuối: `9` và hành trình `1 2 3`.

## Bẫy và câu hỏi

- Quên cộng cạnh `path[n] → 1`: kết quả thiếu cạnh đóng vòng.
- Khởi tạo `bestCost = 0` khi tìm min: không hành trình dương nào cập nhật được.
- Đánh dấu `visited` sau khi gọi đệ quy, hoặc quên `visited[city] = false`: nhánh con thấy sai trạng thái.
- In `path` thay vì `bestPath`: `path` là nhánh vừa duyệt xong, không chắc tối ưu.
- Sửa code cho ra `5` theo Output mẫu. Ma trận đề cho hành trình `1 → 2 → 4 → 3 → 1` chi phí `1 + 2 + 2 + 1 = 6`; code tính đúng theo ma trận.

**Câu hỏi dễ gặp**

- Vì sao cận cho phép nhiều cạnh cùng đi vào một thành phố mà vẫn đúng? Vì phép nới lỏng chỉ làm cận nhỏ hơn; cận vẫn không vượt chi phí thật.
- Vì sao `cheapestEdge` cho phép quay về 1 khi chưa thăm đủ? Đó là một cạnh ra hợp lệ trong chu trình cuối; cho phép sớm chỉ làm cận lạc quan hơn.
- Vì sao kiểm tra lá trước khi tính cận? Để cộng kịp cạnh đóng vòng và cập nhật đáp án.
- `n = 1` thì sao? `travel(1,0)` gặp `step == n`, cộng `cost[1][1]`, in chi phí và `1`.
- Nhánh cận có bảo đảm `n = 30` chạy dưới 1 giây? Không. Cận chỉ tỉa thực tế; độ phức tạp xấu nhất vẫn giai thừa.

</details>

<details>
<summary><strong>Hướng 2 — Cận dưới bằng cây khung nhỏ nhất</strong></summary>

```cpp
#include <climits>
#include <iostream>
using namespace std;

int       n;
int       cost[31][31];
int       path[31] = {0, 1};
int       bestPath[31];
bool      visited[31] = {false, true};
long long bestCost    = LLONG_MAX;

long long minimumSpanningTreeCost(int step)
{
    int vertices[31];
    int count = 0;

    vertices[count++] = path[step];
    if (path[step] != 1) {
        vertices[count++] = 1;
    }
    for (int city = 1; city <= n; city++) {
        if (!visited[city] && city != 1) {
            vertices[count++] = city;
        }
    }

    long long distance[31];
    bool      inTree[31] = {};
    for (int i = 0; i < count; i++) {
        distance[i] = LLONG_MAX;
    }
    distance[0] = 0;

    long long total = 0;
    for (int added = 0; added < count; added++) {
        int chosen = -1;
        for (int i = 0; i < count; i++) {
            if (!inTree[i] && (chosen == -1 || distance[i] < distance[chosen])) {
                chosen = i;
            }
        }

        inTree[chosen]  = true;
        total          += distance[chosen];

        for (int i = 0; i < count; i++) {
            if (!inTree[i]) {
                long long edgeCost    = cost[vertices[chosen]][vertices[i]];
                long long reverseCost = cost[vertices[i]][vertices[chosen]];
                if (reverseCost < edgeCost) {
                    edgeCost = reverseCost;
                }
                if (edgeCost < distance[i]) {
                    distance[i] = edgeCost;
                }
            }
        }
    }
    return total;
}

long long estimateTotalCost(int step, long long currentCost)
{
    return currentCost + minimumSpanningTreeCost(step);
}

void travel(int step, long long currentCost)
{
    if (currentCost >= bestCost) {
        return;
    }

    if (step == n) {
        long long totalCost = currentCost + cost[path[n]][1];
        if (totalCost < bestCost) {
            bestCost = totalCost;
            for (int i = 1; i <= n; i++) {
                bestPath[i] = path[i];
            }
        }
        return;
    }

    if (estimateTotalCost(step, currentCost) >= bestCost) {
        return;
    }

    for (int city = 2; city <= n; city++) {
        if (visited[city]) {
            continue;
        }

        path[step + 1] = city;
        visited[city]  = true;

        travel(step + 1, currentCost + cost[path[step]][city]);

        visited[city] = false;
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> cost[i][j];
        }
    }

    travel(1, 0);

    cout << bestCost << '\n';
    for (int i = 1; i <= n; i++) {
        cout << bestPath[i] << " \n"[i == n];
    }
    return 0;
}
```

Thay tổng cạnh ra rẻ nhất bằng chi phí cây khung nhỏ nhất của tập `{path[step], 1, các thành phố chưa thăm}`. Mọi cách hoàn thành hành trình tạo một cây khung trên tập này, nên MST là cận dưới hợp lệ và thường chặt hơn. Vì ma trận có hướng, mỗi cạnh lấy `min(c[u][v], c[v][u])`. Cái giá là mỗi nút chạy Prim `O(n²)`, nên thời gian xấu nhất vẫn `O(n!·n²)`.

</details>
