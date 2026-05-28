# CLAUDE.md — Hướng dẫn tự động tạo cấu trúc học tập DSA / OOP / ...

## Mục tiêu
Khi người dùng yêu cầu thêm một chủ đề mới (ví dụ: `oop`, `dsa`, `system-design`, ...), Claude **tự động tạo toàn bộ cấu trúc thư mục và file** theo đúng chuẩn dưới đây — không hỏi lại, không bỏ sót bước nào.

---

## Quy tắc kích hoạt

Bất cứ khi nào người dùng nói:
- "thêm [chủ đề]" / "tạo [chủ đề]" / "add [chủ đề]"
- "học [chủ đề]" / "làm [chủ đề]"
- hoặc đề cập đến một chủ đề mới chưa có thư mục

→ **Lập tức tạo cấu trúc thư mục** theo template bên dưới.

---

## Cấu trúc thư mục chuẩn

```
<tên_chủ_đề>/
├── <sub_topic_1>/
│   ├── ly_thuyet.md
│   └── trac_nghiem.md
├── <sub_topic_2>/
│   ├── ly_thuyet.md
│   └── trac_nghiem.md
└── ...
```

### Mapping chủ đề → sub-topics

| Chủ đề | Sub-topics |
|--------|-----------|
| `dsa` | `01_bigo`, `02_array_string`, `03_hashmap_hashset`, `04_two_pointers`, `05_sliding_window`, `06_prefix_sum`, `07_recursion`, `08_stack_queue`, `09_linked_list`, `10_tree_bst`, `11_heap`, `12_graph`, `13_trie`, `14_binary_search`, `15_sorting`, `16_dfs_bfs`, `17_topological_sort`, `18_greedy`, `19_dynamic_programming`, `20_backtracking` |
| `oop` | `01_class_object`, `02_encapsulation`, `03_inheritance`, `04_polymorphism`, `05_abstraction`, `06_interface_abstract`, `07_solid_principles`, `08_design_patterns` |
| `system-design` | `01_scalability`, `02_load_balancing`, `03_caching`, `04_database`, `05_message_queue`, `06_api_design`, `07_microservices`, `08_consistency_availability` |
| `os` | `01_process_thread`, `02_memory_management`, `03_scheduling`, `04_deadlock`, `05_file_system`, `06_synchronization` |
| `network` | `01_osi_model`, `02_tcp_ip`, `03_http_https`, `04_dns`, `05_socket`, `06_security` |

> Nếu chủ đề chưa có trong bảng, Claude tự suy luận sub-topics hợp lý và tạo ít nhất 5 sub-topics.

---

## Chuẩn nội dung file `ly_thuyet.md`

Mỗi file lý thuyết **bắt buộc** có đủ các phần sau, theo đúng thứ tự:

```markdown
# [Tên chủ đề]

---

## Giải thích cho người mới hoàn toàn

> Dùng ngôn ngữ đời thường, ví dụ thực tế từ cuộc sống (không dùng thuật ngữ kỹ thuật).
> Mục tiêu: người chưa biết lập trình đọc xong phải hiểu được ý tưởng cốt lõi.

---

## Giải thích cho người đã biết lập trình (nâng cao)

> Đi sâu vào cơ chế hoạt động bên trong, trade-off, edge case, và ứng dụng thực tế trong
> phỏng vấn / production code. Dùng đúng thuật ngữ chuyên ngành.

---

## Định nghĩa chính xác

> Định nghĩa học thuật hoặc chuẩn công nghiệp.

---

## Độ phức tạp (Time & Space Complexity) ← BẮT BUỘC ĐẦY ĐỦ

Phải cover **tất cả** các case sau cho mỗi thao tác:

| Thao tác | Best Case | Average Case | Worst Case | Space Complexity | Ghi chú / Điều kiện |
|----------|-----------|--------------|------------|------------------|---------------------|
| ...      | O(?)      | O(?)         | O(?)       | O(?)             | ...                 |

**Quy tắc bắt buộc:**
- Không được gộp best/average/worst nếu chúng khác nhau
- Space Complexity phải tính cả **auxiliary space** (không gian bổ sung) lẫn **total space**
- Ghi rõ điều kiện để đạt best case (ví dụ: "mảng đã sắp xếp", "hash không có collision")
- Với đệ quy: ghi rõ call stack space O(h) hay O(n)
- Với các thuật toán có randomization (QuickSort): ghi rõ expected vs worst case

**Ví dụ chuẩn:**

| Thao tác      | Best   | Average  | Worst  | Auxiliary Space | Ghi chú |
|---------------|--------|----------|--------|-----------------|---------|
| Search        | O(1)   | O(log n) | O(n)   | O(1)            | Best khi phần tử ở giữa; Worst khi BST suy biến thành linked list |
| Insert        | O(log n)| O(log n)| O(n)   | O(log n)        | Space do call stack đệ quy |

---

## Pseudocode / Code mẫu

\`\`\`python
# Code minh họa rõ ràng, có comment giải thích từng bước quan trọng
\`\`\`

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- ...

**Không dùng khi:**
- ...

---

## So sánh với các cấu trúc/thuật toán liên quan

| | [Chủ đề này] | [So sánh với] |
|-|-------------|--------------|
| ... | ... | ... |

---

## Lỗi thường gặp (Common Pitfalls)

- ...

---

## Câu hỏi phỏng vấn hay gặp

- ...
```

---

## Chuẩn nội dung file `trac_nghiem.md`

```markdown
# Trắc nghiệm — [Tên chủ đề]

> **Tổng số câu:** ≥ 20  
> **Mức độ:** Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)  
> Mỗi câu có **4 đáp án** (A/B/C/D), ghi rõ đáp án đúng và **giải thích tại sao**.

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** [Nội dung câu hỏi]

- A. ...
- B. ...
- C. ...
- D. ...

> **Đáp án: X**  
> **Giải thích:** [Giải thích ngắn gọn, chính xác tại sao đáp án đó đúng và các đáp án kia sai]

---

## Phần 2 — Trung bình (câu 7–14)

[Tương tự, câu hỏi về ứng dụng, chọn CTDL phù hợp, tính complexity]

---

## Phần 3 — Nâng cao (câu 15–20+)

[Câu hỏi về edge case, so sánh, tối ưu, phân tích code]

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | X      | 11  | X      |
| ... | ...    | ... | ...    |
```

---

## Quy tắc chất lượng nội dung

1. **Lý thuyết phải đủ sâu** — không được viết chung chung kiểu "Array là mảng các phần tử". Phải có cơ chế bộ nhớ, trade-off, ví dụ code thực tế.
2. **Hai tầng giải thích bắt buộc** — mỗi file phải có phần "người mới" VÀ phần "nâng cao/chuyên ngành".
3. **Trắc nghiệm phải có giải thích** — không được chỉ ghi đáp án, phải giải thích tại sao đúng/sai.
4. **Ít nhất 20 câu trắc nghiệm** — phân bổ đủ 3 mức độ.
5. **Code mẫu phải chạy được** — không viết pseudocode mơ hồ, ưu tiên Python hoặc Java.
6. **Bảng complexity bắt buộc** — phải có cho mọi CTDL và thuật toán.

---

## Ví dụ — Khi người dùng nói "thêm oop"

Claude sẽ tạo ngay:
```
oop/
├── 01_class_object/
│   ├── ly_thuyet.md
│   └── trac_nghiem.md
├── 02_encapsulation/
│   ├── ly_thuyet.md
│   └── trac_nghiem.md
...
└── 08_design_patterns/
    ├── ly_thuyet.md
    └── trac_nghiem.md
```

---

## Ghi chú thêm

- Tên thư mục dùng **snake_case**, có số thứ tự `01_`, `02_`, ... để dễ sắp xếp.
- Ngôn ngữ mặc định: **Tiếng Việt** cho giải thích, **Tiếng Anh** cho thuật ngữ kỹ thuật và code.
- Nếu người dùng không nói rõ ngôn ngữ code → dùng **Python**.
- Luôn tạo **toàn bộ** các sub-topic một lúc, không tạo từng cái một trừ khi được yêu cầu.
