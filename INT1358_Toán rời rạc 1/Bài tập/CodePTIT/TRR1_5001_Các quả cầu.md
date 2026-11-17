# TRR1_5001 — Các quả cầu

> **Loại:** Nguyên lý Dirichlet · **Thuật toán:** Công thức đếm · **Thời gian:** O(1) · **Bộ nhớ:** O(1)

## Đề bài

> **TRR1_5001 - Các quả cầu**
> 🔗 https://code.ptit.edu.vn/submit/TRR1_5001

**Mô tả:** Hộp có `h` quả cầu với `m` màu và `n` kích thước khác nhau. Tìm số quả cầu ít nhất cần lấy để **chắc chắn** có `t` quả giống nhau cả màu lẫn kích thước.

**Giới hạn:** `h, m, n, t ≤ 10⁴`, `t ≥ 2` — thời gian chạy ≤ 1 giây.

**Ví dụ:**

<table style="border-collapse: collapse; width: 100%; margin: 0 0 16px;" border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Input</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left; width: 1%; white-space: nowrap;">Output</th><th align="left" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; text-align: left;">Giải thích</th></tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>10 3 2 2</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>7</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Chỉ cần lấy ra ít nhất 7 quả cầu từ hộp thì trong chúng có ít nhất hai quả cầu giống nhau cả về kích thước lẫn màu sắc.</td>
    </tr>
    <tr>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>10 2 3 3</code></pre></td>
      <td valign="top" width="1%" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;"><pre style="margin: 0; white-space: pre; font-size: 0.9em; line-height: 1.5;"><code>0</code></pre></td>
      <td valign="top" style="border: 1px solid rgba(127, 127, 127, 0.45); padding: 6px 10px; vertical-align: top;">Số quả cầu ít nhất cần lấy ra từ hộp thỏa mãn điều kiện là 13. Số quả cầu trong hộp chỉ có 10.</td>
    </tr>
  </tbody>
</table>

## Ý tưởng

Một quả cầu được xác định bởi cặp `(màu, kích thước)`, nên có `K = m × n` loại. Muốn chắc chắn có `t` quả cùng loại, xét tình huống xấu nhất: mỗi loại chỉ có `t - 1` quả.

$$W = (t - 1) \times K.$$

`W` là số quả nhiều nhất mà vẫn chưa có loại nào đạt `t`. Quả tiếp theo buộc một loại đạt `t`, nên ngưỡng là:

$$T = W + 1 = (t - 1)mn + 1.$$

Nếu `T > h`, hộp không đủ quả để đạt ngưỡng, in `0`. Ngược lại in `T`. Khi `T = h`, vẫn phải lấy toàn bộ hộp và đáp án là `T`, không phải `0`.

<details>
<summary><strong>Hướng 1 — Lời giải chính</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long ballCount, colorCount, sizeCount, requiredCount;
    cin >> ballCount >> colorCount >> sizeCount >> requiredCount;

    long long typeCount = colorCount * sizeCount;

    // Xấu nhất: mỗi loại chỉ có requiredCount - 1 quả được lấy ra.
    long long worstCase     = (requiredCount - 1) * typeCount;
    long long minimumToDraw = worstCase + 1;

    if (minimumToDraw > ballCount) {
        cout << 0;
    } else {
        cout << minimumToDraw;
    }
    return 0;
}
```

### Giải thích code

**Trạng thái chính**

| Biến | Ý nghĩa |
|---|---|
| `ballCount` | Số quả cầu trong hộp `h` |
| `colorCount` | Số màu `m` |
| `sizeCount` | Số kích thước `n` |
| `requiredCount` | Số quả cùng loại cần có `t` |
| `typeCount` | Số loại quả cầu `m × n` |
| `worstCase` | Số quả tối đa vẫn chưa đạt yêu cầu |
| `minimumToDraw` | Ngưỡng cần lấy |

**Luồng chạy**

1. Đọc `h`, `m`, `n`, `t`.
2. Tính `typeCount = m × n`.
3. Tính `worstCase = (t - 1) × typeCount`.
4. Tính `minimumToDraw = worstCase + 1`.
5. Nếu `minimumToDraw > h`, in `0`; ngược lại in `minimumToDraw`.

**Đếm loại và ngưỡng**

```cpp
long long typeCount = colorCount * sizeCount;

// Xấu nhất: mỗi loại chỉ có requiredCount - 1 quả được lấy ra.
long long worstCase     = (requiredCount - 1) * typeCount;
long long minimumToDraw = worstCase + 1;
```

Phải nhân `m × n` vì mỗi màu kết hợp được với mọi kích thước. `t - 1` là số quả tối đa mỗi loại được phép có mà vẫn chưa đạt. Cộng thêm một quả là ngưỡng buộc một loại đạt `t`.

**So với sức chứa của hộp**

```cpp
if (minimumToDraw > ballCount) {
    cout << 0;
} else {
    cout << minimumToDraw;
}
```

Dùng `>` chứ không phải `>=`. Khi `minimumToDraw == ballCount`, lấy hết hộp vẫn đạt ngưỡng, nên đáp án là `minimumToDraw`. Chỉ khi ngưỡng vượt quá số quả mới không thể bảo đảm.

**Vì sao đúng**

Nếu một tập đã lấy chưa có `t` quả cùng loại, mỗi loại góp tối đa `t - 1` quả, nên tổng tối đa là `W`. Vì vậy `W + 1` quả chắc chắn có một loại đạt `t`. Khi `W <= h`, tình huống xấu nhất có thể có `W` quả với mỗi loại đúng `t - 1` quả, nên `W` chưa đủ. Do đó `W + 1` là ngưỡng nhỏ nhất.

**Độ phức tạp:** `O(1)` thời gian, `O(1)` bộ nhớ.

## Chạy tay

| `h` | `m` | `n` | `t` | `K = mn` | `W = (t-1)K` | `T = W+1` | Kết quả |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 10 | 3 | 2 | 2 | 6 | 6 | 7 | 7 |
| 10 | 2 | 3 | 3 | 6 | 12 | 13 | 0 |
| 7 | 3 | 2 | 2 | 6 | 6 | 7 | 7 |

Dòng cuối là biên `T = h`: vẫn lấy toàn bộ 7 quả và in `7`.

## Bẫy và câu hỏi

- Dùng `int` cho `worstCase` hoặc `minimumToDraw`: `(t - 1)mn` có thể tới `10¹²`, vượt giới hạn `int`.
- Quên cộng 1: `W` là số quả **chưa** đủ, không phải đáp án.
- Dùng `m + n` thay vì `m × n`: số loại là số cặp màu–kích thước.
- So sánh `minimumToDraw >= ballCount` rồi in `0`: khi bằng nhau vẫn đủ quả để lấy.
- Hiểu `0` là “cần lấy 0 quả”. Đề dùng `0` cho “không thể bảo đảm”.

**Câu hỏi dễ gặp**

- Vì sao số loại là `m × n`? Mỗi màu ghép được với từng kích thước, tạo thành một cặp riêng.
- Vì sao lấy `t - 1` quả mỗi loại? Đó là số tối đa còn tránh được việc có đủ `t` quả cùng loại.
- Nếu `T = h` thì sao? Vẫn in `T`; phải lấy toàn bộ hộp mới chắc chắn.
- `t = 1` thì công thức ra sao? `W = 0`, `T = 1`; một quả đã đủ, ngoài giới hạn đề nhưng công thức vẫn nhất quán.
- Vì sao `T > h` thì in `0`? Vì `h < T`, lấy hết hộp vẫn có thể chưa có loại nào đạt `t`.

</details>

<details>
<summary><strong>Hướng 2 — Dò ngưỡng bằng vòng lặp</strong></summary>

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long ballCount, colorCount, sizeCount, requiredCount;
    cin >> ballCount >> colorCount >> sizeCount >> requiredCount;

    long long typeCount            = colorCount * sizeCount;
    long long maximumWithoutTarget = (requiredCount - 1) * typeCount;

    for (long long draw = 1; draw <= ballCount; draw++) {
        if (draw > maximumWithoutTarget) {
            cout << draw;
            return 0;
        }
    }

    cout << 0;
    return 0;
}
```

Cách này vẫn tính `W = (t - 1)mn`, nhưng dò `draw` từ `1` đến `h` và in `draw` tại giá trị đầu tiên vượt `W`. Nếu không có giá trị nào vượt, in `0`. Cùng kết quả với công thức trực tiếp, đổi lại thời gian `O(h)` thay vì `O(1)`; với `h ≤ 10⁴` vẫn nhanh.

</details>
