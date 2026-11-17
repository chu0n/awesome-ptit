# TRR1_5021 — Chọn cặp số 08

> **Loại:** Nguyên lý Dirichlet · **Thuật toán:** Đếm theo nhóm mod 4 · **Thời gian:** O(1) · **Bộ nhớ:** O(1)

## Đề bài

> **TRR1_5021 - Chọn cặp số 08**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_5021

**Mô tả:** Cho hai số nguyên dương `n` và `k`. Tìm số lượng `t` ít nhất các cặp `(x, y)` (với `x, y ≤ n`) cần chọn để **chắc chắn** có `k` cặp `(a, b)` sao cho `a*b` chia hết cho 4.

**Giới hạn:** `n, k ≤ 10⁴` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>4 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>10</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Có 8 cặp số (a, b) mà a*b không chia hết cho 4: (1,1), (1,2), (1,3), (2,1), (2,3), (3,1), (3,2), (3,3). Do đó chỉ cần chọn t = 10 cặp số (a,b) thì chắc chắn có k = 2 cặp số thỏa mãn a*b chia hết cho 4.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

Xét `v₂(x)` là số mũ của 2 trong `x`. Tích `a × b` chia hết cho 4 khi và chỉ khi `v₂(a) + v₂(b) >= 2`. Chia các số `1..n` thành ba nhóm:

- lẻ: `v₂ = 0`, số lượng `O = (n + 1) / 2`;
- chẵn nhưng không chia hết cho 4: `v₂ = 1`, số lượng `T = n/2 - n/4`;
- chia hết cho 4: `v₂ >= 2`, số lượng `F = n/4`.

Cặp chưa đạt chỉ có ba dạng: lẻ–lẻ, lẻ–`T`, và `T`–lẻ. Vì cặp có thứ tự:

$$B = O^2 + 2OT.$$

Tổng số cặp là `n²`, nên số cặp tốt là `G = n² - B`. Tình huống xấu nhất chọn hết `B` cặp xấu trước, rồi cần thêm `k` cặp tốt. Nếu `G < k`, không thể bảo đảm, in `0`; ngược lại đáp án là `B + k`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long n, k;
    cin >> n >> k;

    long long oddCount            = (n + 1) / 2;
    long long evenCount           = n / 2;
    long long multipleOf4Count    = n / 4;
    long long onlyOneFactor2Count = evenCount - multipleOf4Count;

    // Các cặp chưa đạt: lẻ–lẻ, lẻ–(dư 2), (dư 2)–lẻ.
    long long badPairCount  = oddCount * oddCount
                            + 2 * oddCount * onlyOneFactor2Count;
    long long goodPairCount = n * n - badPairCount;

    if (goodPairCount < k) {
        cout << 0;
    } else {
        // Chọn hết cặp xấu trước, sau đó cần thêm k cặp tốt.
        cout << badPairCount + k;
    }
    return 0;
}
```

### Giải thích code

**Trạng thái chính**

| Biến | Ý nghĩa |
|---|---|
| `n`, `k` | Giới hạn `x, y ≤ n` và số cặp tốt cần có |
| `oddCount` | Số lẻ trong `[1, n]` |
| `evenCount` | Số chẵn trong `[1, n]` |
| `multipleOf4Count` | Số bội của 4 trong `[1, n]` |
| `onlyOneFactor2Count` | Số chẵn nhưng không chia hết cho 4 |
| `badPairCount` | Số cặp `(a, b)` có `a*b` không chia hết cho 4 |
| `goodPairCount` | Số cặp `(a, b)` có `a*b` chia hết cho 4 |

**Luồng chạy**

1. Đọc `n`, `k`.
2. Đếm `oddCount`, `evenCount`, `multipleOf4Count`; suy ra `onlyOneFactor2Count`.
3. Tính `badPairCount = oddCount² + 2·oddCount·onlyOneFactor2Count`.
4. Tính `goodPairCount = n² - badPairCount`.
5. Nếu `goodPairCount < k`, in `0`; ngược lại in `badPairCount + k`.

**Phân nhóm số**

```cpp
long long oddCount            = (n + 1) / 2;
long long evenCount           = n / 2;
long long multipleOf4Count    = n / 4;
long long onlyOneFactor2Count = evenCount - multipleOf4Count;
```

`(n + 1) / 2` đếm số lẻ, `n / 2` đếm số chẵn, `n / 4` đếm bội của 4. Hiệu `evenCount - multipleOf4Count` là các số chẵn còn lại, đúng nhóm `v₂ = 1`.

**Đếm cặp xấu và cặp tốt**

```cpp
// Các cặp chưa đạt: lẻ–lẻ, lẻ–(dư 2), (dư 2)–lẻ.
long long badPairCount  = oddCount * oddCount
                        + 2 * oddCount * onlyOneFactor2Count;
long long goodPairCount = n * n - badPairCount;
```

`oddCount²` là các cặp lẻ–lẻ. `2·oddCount·onlyOneFactor2Count` gộp hai chiều lẻ–`T` và `T`–lẻ. Không có số hạng `T²` vì `1 + 1 = 2`, tích của hai số nhóm `T` đã chia hết cho 4. Mọi cặp liên quan đến bội của 4 đều tốt.

**Kiểm tra và in**

```cpp
if (goodPairCount < k) {
    cout << 0;
} else {
    // Chọn hết cặp xấu trước, sau đó cần thêm k cặp tốt.
    cout << badPairCount + k;
}
```

Điều kiện phải là `goodPairCount < k`, không phải `badPairCount < k`. Khi `G = k`, đáp án là `B + k = n²`: phải chọn toàn bộ các cặp.

**Vì sao đúng**

`a*b` chia hết cho 4 khi tổng số mũ của 2 trong `a` và `b` ít nhất là 2. Các cặp có tổng nhỏ hơn 2 chỉ là `O–O`, `O–T`, `T–O`; cặp `T–T` có tổng `1 + 1 = 2` nên tốt. Đếm đúng các cặp xấu cho `B`. Với `G >= k`, mọi cách chọn `B + k` cặp đều phải vượt qua toàn bộ `B` cặp xấu, nên có ít nhất `k` cặp tốt; `B + k - 1` cặp thì vẫn có thể toàn cặp xấu hoặc thiếu một cặp tốt. Vậy `B + k` là tối thiểu.

**Độ phức tạp:** `O(1)` thời gian, `O(1)` bộ nhớ.

## Chạy tay

| `n` | `k` | `O` | `T` | `F` | `B` | `G` | `t` |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 4 | 2 | 2 | 1 | 1 | 8 | 8 | 10 |
| 2 | 1 | 1 | 1 | 0 | 3 | 1 | 4 |
| 1 | 1 | 1 | 0 | 0 | 1 | 0 | 0 |

Với `n = 4`, `k = 2`: tám cặp xấu, tám cặp tốt, đáp án `8 + 2 = 10`. Với `n = 1`, không có cặp tốt nên in `0`.

## Bẫy và câu hỏi

- Chỉ chia số thành chẵn và lẻ: phải tách riêng nhóm chẵn nhưng không chia hết cho 4.
- Tính cặp `T–T` là cặp xấu: `1 + 1 = 2`, tích của hai số này chia hết cho 4.
- Quên hệ số 2 cho hai chiều lẻ–`T`: `(1,2)` và `(2,1)` là hai cặp khác nhau.
- Dùng `n(n-1)/2` hoặc `n(n+1)/2` làm tổng số cặp: tổng đúng là `n²`.
- Kiểm tra `badPairCount < k` thay vì `goodPairCount < k`, hoặc in `badPairCount` thay vì `badPairCount + k`.

**Câu hỏi dễ gặp**

- Vì sao cặp `(2,2)` vẫn tốt? `2 × 2 = 4`, tổng số mũ của 2 là `1 + 1 = 2`.
- Vì sao có hệ số 2? Cặp có thứ tự, nên chiều lẻ–`T` và `T`–lẻ là hai tập khác nhau.
- Nếu `G = k` thì sao? Đáp án là `B + k = n²`, phải chọn toàn bộ.
- Vì sao `G < k` thì in `0`? Vì ngay cả khi chọn hết `n²` cặp cũng không đủ `k` cặp tốt.
- Vì sao không cần duyệt từng cặp? Chỉ cần đếm theo ba nhóm dư; tính chất chia hết của tích chỉ phụ thuộc nhóm của `a` và `b`.

</details>

<details>
<summary><strong>Hướng 2 — Quét từng a và cộng số b phù hợp</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long n, k;
    cin >> n >> k;

    long long goodPairCount = 0;
    for (long long a = 1; a <= n; a++) {
        if (a % 4 == 0) {
            goodPairCount += n;
        } else if (a % 2 == 0) {
            goodPairCount += n / 2;
        } else {
            goodPairCount += n / 4;
        }
    }

    long long badPairCount = n * n - goodPairCount;
    if (goodPairCount < k) {
        cout << 0;
    } else {
        cout << badPairCount + k;
    }
    return 0;
}
```

Với mỗi `a`, đếm trực tiếp số `b` làm `a*b` chia hết cho 4: `n` nếu `a` chia hết cho 4, `n/2` nếu `a` chỉ có một thừa số 2, và `n/4` nếu `a` lẻ. Cộng dồn được `goodPairCount`, suy ra `badPairCount = n² - goodPairCount`. Cách này dễ nối với định nghĩa nhưng chạy `O(n)` thay vì `O(1)`; với `n ≤ 10⁴` vẫn nhanh.

</details>
