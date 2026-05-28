# Tổng quan Toán Rời Rạc — Discrete Mathematics

---

## Roadmap học Toán Rời Rạc

### Bước 1 — Logic & Tập hợp
- **Mệnh đề logic** — bảng chân trị, tautology, equivalence
- **Quantifiers** — ∀ (for all), ∃ (there exists)
- **Tập hợp** — union, intersection, complement, power set
- **Hàm & Quan hệ** — injective, surjective, bijective

### Bước 2 — Lý thuyết số
- **Divisibility** — chia hết, GCD, LCM, Bézout's Identity
- **Modular Arithmetic** — congruence, Fermat's Little Theorem
- **Number Theory** — prime factorization, Euler's totient
- **Chinese Remainder Theorem** (CRT)

### Bước 3 — Lý thuyết đồ thị
- **Graph basics** — directed/undirected, degree, path, cycle
- **Special graphs** — tree, bipartite, complete graph (Kₙ)
- **Graph coloring** — chromatic number, 4-color theorem
- **Euler path/circuit** — điều kiện tồn tại
- **Hamiltonian path/circuit** — NP-complete problem

### Bước 4 — Quan hệ & Thứ tự
- **Equivalence relation** — reflexive, symmetric, transitive
- **Partial order** — poset, Hasse diagram
- **Lattice** — join, meet

### Bước 5 — Lý thuyết tự động & Ngôn ngữ
- **Finite Automata** — DFA, NFA
- **Regular expressions** — pattern matching
- **Context-free grammar** — parsing
- **Turing Machine** — computability

---

## Tập hợp — Bảng phép toán

| Phép toán | Ký hiệu | Định nghĩa | Ví dụ |
|-----------|---------|------------|-------|
| Union | A∪B | {x: x∈A hoặc x∈B} | {1,2}∪{2,3}={1,2,3} |
| Intersection | A∩B | {x: x∈A và x∈B} | {1,2}∩{2,3}={2} |
| Complement | Aᶜ hay Ā | {x∈U: x∉A} | U={1,2,3}, A={1} → Aᶜ={2,3} |
| Difference | A-B | {x: x∈A và x∉B} | {1,2,3}-{2,3}={1} |
| Cartesian | A×B | {(a,b): a∈A, b∈B} | {1,2}×{a}={(1,a),(2,a)} |
| Power Set | P(A) hay 2^A | Tất cả subsets | P({1,2})={∅,{1},{2},{1,2}} |

**De Morgan's Laws:**
- (A∪B)ᶜ = Aᶜ∩Bᶜ
- (A∩B)ᶜ = Aᶜ∪Bᶜ

---

## Lý thuyết đồ thị — Tổng hợp

### Phân loại đồ thị

| Loại | Mô tả | Ví dụ |
|------|-------|-------|
| Simple graph | Không self-loop, không multi-edge | Mạng xã hội |
| Directed (Digraph) | Cạnh có hướng | Web links |
| Weighted | Cạnh có trọng số | Bản đồ GPS |
| Complete (Kₙ) | Mọi cặp đỉnh đều có cạnh | K₄: 6 cạnh |
| Bipartite | Chia 2 nhóm, cạnh chỉ giữa 2 nhóm | Matching problem |
| Tree | Connected + acyclic | File system |
| DAG | Directed Acyclic Graph | Dependency graph |

### Công thức đồ thị quan trọng

| Công thức | Giải thích |
|-----------|------------|
| Σ deg(v) = 2\|E\| | Tổng degree = 2 lần số cạnh (Handshaking Lemma) |
| Số đỉnh degree lẻ luôn chẵn | Hệ quả Handshaking Lemma |
| Tree: \|E\| = \|V\| - 1 | Cây n đỉnh có n-1 cạnh |
| Euler circuit: mọi vertex degree chẵn | Điều kiện Euler circuit |
| Euler path: đúng 0 hoặc 2 vertex degree lẻ | Điều kiện Euler path |
| Complete Kₙ: n(n-1)/2 cạnh | Mọi cặp kết nối |
| Planar: \|E\| ≤ 3\|V\| - 6 | Euler's formula cho planar graph |

---

## Modular Arithmetic — Tính chất

```
a ≡ b (mod m)  ↔  m | (a-b)  ↔  a và b cùng remainder khi chia m

Tính chất:
(a + b) mod m = ((a mod m) + (b mod m)) mod m
(a × b) mod m = ((a mod m) × (b mod m)) mod m
(a - b) mod m = ((a mod m) - (b mod m) + m) mod m

Fermat's Little Theorem (p là số nguyên tố, gcd(a,p)=1):
a^(p-1) ≡ 1 (mod p)
→ a^p ≡ a (mod p)

Ứng dụng: tính a^b mod p hiệu quả khi b lớn
a^(1000000007-1) ≡ 1 (mod 1000000007)
```

---

## Hàm — Phân loại

```
f: A → B

Injective (one-to-one / đơn ánh):
  a₁ ≠ a₂ → f(a₁) ≠ f(a₂)
  Không có 2 phần tử A map vào cùng 1 phần tử B
  Điều kiện: |A| ≤ |B|

Surjective (onto / toàn ánh):
  Mọi b∈B đều có a∈A sao cho f(a)=b
  Mọi phần tử B đều được "chạm đến"
  Điều kiện: |A| ≥ |B|

Bijective (song ánh):
  Vừa injective vừa surjective
  Điều kiện: |A| = |B|
  → Có inverse function f⁻¹
```

---

## Mã giả — Hỏi output là gì?

### Bài 1 — GCD và Euclidean Algorithm

```python
def gcd(a, b):
    print(f"gcd({a}, {b})")
    if b == 0:
        return a
    return gcd(b, a % b)

result = gcd(48, 18)
print(f"Result: {result}")
```

> **Output là gì?**
> ```
> gcd(48, 18)
> gcd(18, 12)
> gcd(12, 6)
> gcd(6, 0)
> Result: 6
> ```
> **Giải thích:** Euclidean: gcd(a,b) = gcd(b, a%b). 48%18=12, 18%12=6, 12%6=0 → GCD=6. Thời gian O(log(min(a,b))).

---

### Bài 2 — Modular Arithmetic

```python
MOD = 1000000007

a = 999999999
b = 999999998

print((a + b) % MOD)
print((a * b) % MOD)

# Hỏi: Tính 2^100 mod 7 bằng Fermat's Little Theorem
# 7 là số nguyên tố → 2^6 ≡ 1 (mod 7)
# 100 = 6*16 + 4
# 2^100 = (2^6)^16 * 2^4 ≡ 1^16 * 16 ≡ 16 ≡ ? (mod 7)
```

> **Output là gì?**
> ```
> 999999999  (vì (a+b) mod MOD = 1999999997 mod 1000000007 = 999999990... cần tính chính xác)
> ```
> Tính chuẩn: (999999999 + 999999998) = 1999999997, 1999999997 % 1000000007 = **999999990**  
> 2^100 mod 7: 100 = 6×16+4 → 2^4 mod 7 = 16 mod 7 = **2**

---

### Bài 3 — Euler Path

```
Đồ thị G với các cạnh:
A-B, A-C, B-C, B-D, C-D

Degree của mỗi đỉnh:
A: 2 (kết nối B, C)
B: 3 (kết nối A, C, D)
C: 3 (kết nối A, B, D)
D: 2 (kết nối B, C)

Hỏi:
a) Đồ thị có Euler Circuit không?
b) Đồ thị có Euler Path không?
c) Nếu có, chỉ ra một đường đi Euler Path.
```

> **Đáp án:**
> a) **Không có Euler Circuit** — B và C có degree lẻ (3), cần mọi đỉnh degree chẵn.
> b) **Có Euler Path** — đúng 2 đỉnh (B và C) có degree lẻ → có Euler Path từ B đến C (hoặc C đến B).
> c) Một Euler Path: **B → A → C → B → D → C** (đi qua mọi cạnh đúng 1 lần)

---

### Bài 4 — Graph Coloring

```
Đồ thị:
  A - B
  |   |
  C - D - E

Hỏi: Cần ít nhất bao nhiêu màu để tô sao cho không có 2 đỉnh kề nhau cùng màu?
(Chromatic number χ(G))
```

> **Đáp án: 2 màu**  
> **Giải thích:** Đây là đồ thị **bipartite** (chia thành {A,D} và {B,C,E} — không có cạnh trong mỗi nhóm). Bipartite graph luôn có χ = 2 (trừ đồ thị rỗng χ=1).  
> Tô màu: A=đỏ, D=đỏ, B=xanh, C=xanh, E=xanh. Kiểm tra: A-B✓, A-C✓, B-D✓, C-D✓, D-E✓.

---

### Bài 5 — Equivalence Relation

```python
# Quan hệ R trên Z: aRb khi và chỉ khi (a-b) chia hết cho 3

def R(a, b):
    return (a - b) % 3 == 0

# Kiểm tra tính chất
pairs = [(1, 4), (2, 5), (0, 9), (1, 2), (3, 7)]
for a, b in pairs:
    print(f"R({a},{b}) = {R(a,b)}")

# Các lớp tương đương với Z:
print("\nClasses:")
print("[0] =", [x for x in range(-6, 7) if R(x, 0)])
print("[1] =", [x for x in range(-6, 7) if R(x, 1)])
print("[2] =", [x for x in range(-6, 7) if R(x, 2)])
```

> **Output là gì?**
> ```
> R(1,4) = True      (1-4=-3, chia hết 3)
> R(2,5) = True      (2-5=-3)
> R(0,9) = True      (0-9=-9)
> R(1,2) = False     (1-2=-1, không chia hết 3)
> R(3,7) = False     (3-7=-4)
>
> Classes:
> [0] = [-6, -3, 0, 3, 6]
> [1] = [-5, -2, 1, 4]
> [2] = [-4, -1, 2, 5]
> ```
> **Giải thích:** Đây là quan hệ tương đương (reflexive: aRa vì 0%3=0; symmetric: nếu 3|(a-b) thì 3|(b-a); transitive). Tạo ra 3 lớp tương đương = Z/3Z.

---

### Bài 6 — DFA (Deterministic Finite Automaton)

```
DFA nhận string nhị phân. Chấp nhận nếu số lượng '1' chia hết cho 3.

States: q0 (start/accept), q1, q2
Transitions:
  q0 --0--> q0, q0 --1--> q1
  q1 --0--> q1, q1 --1--> q2
  q2 --0--> q2, q2 --1--> q0

Accept states: {q0}

Trace các input:
a) "101"
b) "111"
c) "110"
d) "000"
```

> **Output là gì?** (ACCEPT/REJECT)
> ```
> a) "101": q0→q1(1)→q1(0)→q2(1) → ở q2 → REJECT  (hai số 1)
> b) "111": q0→q1(1)→q2(1)→q0(1) → ở q0 → ACCEPT  (ba số 1)
> c) "110": q0→q1(1)→q2(1)→q2(0) → ở q2 → REJECT  (hai số 1)
> d) "000": q0→q0(0)→q0(0)→q0(0) → ở q0 → ACCEPT  (không số 1, 0%3=0)
> ```

---

## Bảng tổng hợp các cấu trúc rời rạc

| Cấu trúc | Tập nền | Quan hệ | Tính chất | Ví dụ |
|---------|---------|---------|-----------|-------|
| Equivalence | Tập S | ~ | Reflexive, Symmetric, Transitive | mod 3 |
| Partial Order | Tập S | ≤ | Reflexive, Antisymmetric, Transitive | Số nguyên (≤) |
| Total Order | Tập S | ≤ | Partial order + Totality | Số thực |
| Lattice | Poset | join∨, meet∧ | Mọi cặp có sup và inf | Subsets với ⊆ |
| Group | (G, *) | * | Closure, Assoc, Identity, Inverse | (Z, +) |

---

## Câu hỏi tự test nhanh

1. Handshaking Lemma nói gì? → Tổng tất cả degree = 2|E|
2. Điều kiện Euler Circuit? → Tất cả đỉnh degree chẵn + đồ thị liên thông
3. Bipartite graph có chu trình lẻ không? → Không (không có odd cycle)
4. |P(A)| với |A|=4? → 2^4 = 16 subsets
5. Fermat's Little Theorem: a^p ≡ ? (mod p) với p nguyên tố, gcd(a,p)=1? → a^p ≡ a (mod p), hay a^(p-1) ≡ 1
6. Bijection cần gì? → |A|=|B|, injective và surjective
7. DFA vs NFA: cái nào mạnh hơn? → Như nhau (NFA có thể convert sang DFA tương đương)
