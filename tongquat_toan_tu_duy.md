# Tổng quan Toán Tư Duy — Logical & Mathematical Reasoning

---

## Roadmap học Toán Tư Duy

### Bước 1 — Logic cơ bản
- **Mệnh đề** — true/false, phủ định (¬), conjunction (∧), disjunction (∨)
- **Implication** — P→Q, contrapositive (¬Q→¬P), converse (Q→P)
- **Biconditional** — P↔Q (khi và chỉ khi)
- **Tautology vs Contradiction**

### Bước 2 — Kỹ thuật suy luận
- **Modus Ponens**: P→Q, P → Q
- **Modus Tollens**: P→Q, ¬Q → ¬P
- **Proof by Contradiction** — giả sử ngược lại dẫn đến mâu thuẫn
- **Proof by Induction** — base case + inductive step

### Bước 3 — Tư duy toán học
- **Quy nạp toán học** (Mathematical Induction)
- **Đếm có nguyên tắc** — phép cộng, phép nhân, pigeonhole
- **Suy luận có/không** — bao hàm-loại trừ
- **Bài toán tối ưu đơn giản** — min/max, greedy logic

### Bước 4 — Dạng bài tư duy lập trình
- **Bit manipulation** — XOR tricks, popcount, bit masking
- **Số học** — GCD/LCM, modular arithmetic, prime sieve
- **Bài toán logic** — loại trừ, suy luận từng bước, invariant

---

## Bảng Toán logic — Bảng chân trị

| P | Q | P∧Q | P∨Q | P→Q | P↔Q | ¬P |
|---|---|-----|-----|-----|-----|-----|
| T | T |  T  |  T  |  T  |  T  |  F  |
| T | F |  F  |  T  |  F  |  F  |  F  |
| F | T |  F  |  T  |  T  |  F  |  T  |
| F | F |  F  |  F  |  T  |  T  |  T  |

**Lưu ý quan trọng:** P→Q chỉ FALSE khi P=True mà Q=False ("nếu trời mưa thì đường ướt" — sai chỉ khi trời mưa mà đường không ướt).

---

## Quy nạp toán học — Template

```
Muốn chứng minh: P(n) đúng với mọi n ≥ 1

Bước 1 (Base case): Chứng minh P(1) đúng.
Bước 2 (Inductive step): Giả sử P(k) đúng (Inductive Hypothesis),
                          chứng minh P(k+1) cũng đúng.
Kết luận: P(n) đúng với mọi n ≥ 1.

Ví dụ: Chứng minh 1+2+...+n = n(n+1)/2
- Base: n=1: LHS=1, RHS=1·2/2=1 ✓
- Step: Giả sử đúng với k, cần chứng minh với k+1:
  1+2+...+k+(k+1) = k(k+1)/2 + (k+1) = (k+1)(k+2)/2 ✓
```

---

## Bit Manipulation — Tricks quan trọng

| Phép tính | Code | Kết quả |
|-----------|------|---------|
| Check bit thứ i | `n & (1 << i)` | 0 nếu bit=0 |
| Set bit thứ i | `n \| (1 << i)` | bật bit i lên |
| Clear bit thứ i | `n & ~(1 << i)` | tắt bit i |
| Toggle bit thứ i | `n ^ (1 << i)` | đảo bit i |
| Check số chẵn/lẻ | `n & 1` | 0=chẵn, 1=lẻ |
| Xóa bit thấp nhất | `n & (n-1)` | xóa rightmost 1-bit |
| Đếm số bit 1 | `bin(n).count('1')` | popcount |
| A XOR A | `a ^ a` | luôn = 0 |
| A XOR 0 | `a ^ 0` | luôn = a |

---

## Mã giả — Hỏi output là gì?

### Bài 1 — Logic Suy luận

```
Cho các mệnh đề:
1. Nếu trời mưa thì tôi ở nhà. (R → H)
2. Nếu tôi ở nhà thì tôi xem phim. (H → M)
3. Hôm nay trời mưa. (R = True)

Hỏi: Tôi có xem phim không?
```

> **Đáp án: Có**  
> **Giải thích:** R=True → (R→H) → H=True → (H→M) → M=True. Đây là **Modus Ponens** áp dụng 2 lần / Law of Syllogism: (R→H) ∧ (H→M) → (R→M).

---

### Bài 2 — Bit XOR trick

```python
def find_single(nums):
    result = 0
    for n in nums:
        result ^= n
    return result

print(find_single([4, 1, 2, 1, 2]))
print(find_single([2, 2, 3, 2]))

# XOR trace cho [4,1,2,1,2]:
# 0^4=4, 4^1=5, 5^2=7, 7^1=6, 6^2=4
```

> **Output là gì?**
> ```
> 4
> 3
> ```
> **Giải thích:** XOR có tính chất: `a^a=0` và `a^0=a`. Các số xuất hiện 2 lần sẽ triệt tiêu nhau. Số còn lại là số xuất hiện 1 lần. Đây là LeetCode 136 — Single Number.

---

### Bài 3 — Proof by Contradiction

```
Chứng minh: √2 là số vô tỉ

Giả sử ngược lại: √2 = p/q (p, q nguyên, tối giản — không có ước chung)
→ 2 = p²/q²
→ p² = 2q²
→ p² chia hết cho 2 → p chia hết cho 2
→ p = 2k
→ (2k)² = 2q² → 4k² = 2q² → q² = 2k²
→ q² chia hết cho 2 → q chia hết cho 2
→ p và q đều chia hết cho 2 → MÂU THUẪN (p/q không tối giản)
→ Giả sử sai → √2 là số vô tỉ ■

Hỏi: Bước nào là bước "mâu thuẫn" trong proof này?
```

> **Đáp án:** Bước "p và q đều chia hết cho 2" mâu thuẫn với giả thiết "p/q tối giản (không có ước chung)".

---

### Bài 4 — Modular Arithmetic

```python
# Tính (a^b) % m hiệu quả — Fast Exponentiation
def power_mod(a, b, m):
    result = 1
    a = a % m
    while b > 0:
        if b % 2 == 1:      # b lẻ
            result = (result * a) % m
        b = b // 2
        a = (a * a) % m
    return result

print(power_mod(2, 10, 1000))
print(power_mod(3, 4, 5))
print(power_mod(2, 0, 7))
```

> **Output là gì?**
> ```
> 24
> 1
> 1
> ```
> **Giải thích:**
> - 2^10 = 1024, 1024 % 1000 = **24**
> - 3^4 = 81, 81 % 5 = **1**
> - 2^0 = 1, 1 % 7 = **1**

---

### Bài 5 — Pigeonhole Principle

```
Bài toán:
Trong một lớp có 367 học sinh.
Hỏi: Có ít nhất 2 học sinh nào sinh cùng ngày trong năm không?
Tại sao chắc chắn?
```

> **Đáp án: Chắc chắn có**  
> **Giải thích (Pigeonhole Principle):** Một năm có tối đa 366 ngày (năm nhuận). Có 367 học sinh → 367 "con chim" phải vào 366 "cái lồng" → ít nhất 1 lồng chứa ≥ 2 con chim → ít nhất 2 học sinh sinh cùng ngày.

---

### Bài 6 — Mathematical Induction

```
Chứng minh: 2^0 + 2^1 + 2^2 + ... + 2^n = 2^(n+1) - 1

Kiểm tra với n=0, n=1, n=2, n=3:
n=0: 1 = 2^1 - 1 = 1 ✓
n=1: 1+2 = 3 = 2^2 - 1 = 3 ✓
n=2: 1+2+4 = 7 = 2^3 - 1 = 7 ✓
n=3: 1+2+4+8 = 15 = 2^4 - 1 = 15 ✓

Hỏi: Tính tổng 2^0 + 2^1 + ... + 2^9 bằng công thức?
```

> **Đáp án:** 2^(9+1) - 1 = 2^10 - 1 = 1024 - 1 = **1023**

---

## Các tính chất số học quan trọng

```python
import math

# GCD và LCM
gcd = math.gcd(48, 18)       # 6
lcm = 48 * 18 // gcd          # 144

# Kiểm tra số nguyên tố
def is_prime(n):
    if n < 2: return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0: return False
    return True

# Sieve of Eratosthenes
def sieve(n):
    is_p = [True] * (n+1)
    is_p[0] = is_p[1] = False
    for i in range(2, int(n**0.5)+1):
        if is_p[i]:
            for j in range(i*i, n+1, i):
                is_p[j] = False
    return [i for i in range(n+1) if is_p[i]]

print(sieve(20))  # [2, 3, 5, 7, 11, 13, 17, 19]
```

---

## Câu hỏi tự test nhanh

1. P→Q tương đương với gì? → ¬Q→¬P (contrapositive)
2. Modus Tollens: P→Q, ¬Q → ? → ¬P
3. `n & (n-1)` làm gì? → Xóa bit 1 thấp nhất của n
4. Pigeonhole Principle phát biểu thế nào? → n+1 vật vào n hộp → ít nhất 1 hộp chứa ≥ 2 vật
5. Induction cần 2 bước gì? → Base case + Inductive step
