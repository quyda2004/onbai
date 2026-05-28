# Tổng quan AI — Artificial Intelligence

---

## Roadmap học AI

### Bước 1 — Nền tảng AI
- **AI vs ML vs DL** — phân biệt 3 khái niệm, mối quan hệ
- **Search Algorithms** — BFS, DFS, A*, Greedy Best-First
- **Knowledge Representation** — propositional logic, first-order logic
- **Constraint Satisfaction Problems (CSP)** — backtracking, arc consistency

### Bước 2 — Probabilistic AI
- **Bayesian Networks** — conditional independence, inference
- **Markov Models** — HMM (Hidden Markov Model)
- **Naive Bayes Classifier** — ứng dụng trong NLP/spam detection

### Bước 3 — Natural Language Processing (NLP)
- **Text preprocessing** — tokenization, stemming, lemmatization, stopwords
- **Representation** — Bag of Words, TF-IDF, Word2Vec, GloVe
- **Language Models** — n-gram → Transformer → BERT, GPT
- **Tasks** — Classification, NER, Translation, Summarization, QA

### Bước 4 — Computer Vision (CV)
- **Image basics** — pixel, channel, convolution
- **CNN architecture** — AlexNet → VGG → ResNet → EfficientNet
- **Tasks** — Classification, Object Detection (YOLO), Segmentation
- **Transfer Learning** — ImageNet pretrained, fine-tuning

### Bước 5 — Generative AI & LLM
- **Transformer Architecture** — attention, positional encoding, encoder/decoder
- **Large Language Models** — GPT, BERT, LLaMA, Claude
- **Fine-tuning techniques** — full fine-tune, LoRA, PEFT, RLHF
- **Prompt Engineering** — zero-shot, few-shot, chain-of-thought, RAG

---

## AI vs ML vs DL — Phân biệt rõ ràng

```
AI (Artificial Intelligence)
└── ML (Machine Learning) — học từ data
    └── DL (Deep Learning) — neural network nhiều tầng
        └── Foundation Models / LLM — pretrained trên dữ liệu khổng lồ

AI không phải ML: Rule-based systems, Expert systems, Game AI (minimax)
ML không phải DL: Linear Regression, SVM, Random Forest, Decision Tree
DL: CNN, RNN, Transformer, GAN, Diffusion Model
```

---

## Bảng Transformer vs RNN/LSTM

| | RNN/LSTM | Transformer |
|-|----------|-------------|
| Xử lý | Sequential (tuần tự) | Parallel (song song) |
| Long-range dependency | Khó (vanishing gradient) | Tốt (self-attention) |
| Training speed | Chậm | Nhanh (GPU-friendly) |
| Memory | O(n) context | O(n²) attention |
| Ứng dụng | Time series, speech | NLP, CV, multimodal |
| Ví dụ | LSTM, GRU | BERT, GPT, T5 |

---

## Search Algorithms — So sánh

| Algorithm | Complete? | Optimal? | Time | Space | Dùng khi |
|-----------|----------|---------|------|-------|---------|
| BFS | Có | Có (cost=1) | O(b^d) | O(b^d) | Shortest path, unweighted |
| DFS | Có (finite) | Không | O(b^m) | O(b·m) | Memory giới hạn |
| UCS | Có | Có | O(b^(C*/ε)) | O(b^(C*/ε)) | Weighted shortest path |
| A* | Có | Có (admissible h) | O(b^d) | O(b^d) | Best-first với heuristic |
| Greedy BFS | Không | Không | O(b^m) | O(b^m) | Nhanh, không cần optimal |

*b=branching factor, d=depth, m=max depth, C*=optimal cost*

---

## Mã giả — Hỏi output là gì?

### Bài 1 — A* Search (trace)

```
Graph (weighted):
S → A: cost=1, h(A)=4
S → B: cost=4, h(B)=2
A → C: cost=2, h(C)=1
B → C: cost=1, h(C)=1
C → G: cost=3, h(G)=0

f(n) = g(n) + h(n)  [g=cost từ start, h=heuristic]

Step 1: Start=S, g(S)=0, h(S)=?, f(S)=?
Expand S → {A: f=1+4=5, B: f=4+2=6}
Open: [A(5), B(6)]

Step 2: Expand A (f=5 nhỏ nhất)
A → C: g(C)=1+2=3, f=3+1=4
Open: [C(4), B(6)]

Step 3: Expand C (f=4)
C → G: g(G)=3+3=6, f=6+0=6
Open: [B(6), G(6)]

Hỏi: A* chọn đường nào đến G? Chi phí bao nhiêu?
```

> **Đáp án:** Đường **S → A → C → G**, chi phí = **6**  
> **Giải thích:** A* expand B (f=6) hoặc G (f=6) tiếp theo. Khi G được expand từ C với g=6, kiểm tra B→C→G: g=4+1+3=8 > 6. Vậy đường S→A→C→G là optimal.

---

### Bài 2 — Naive Bayes Spam Detection

```
Training data:
- "buy now cheap" → SPAM
- "free money win" → SPAM
- "meeting tomorrow lunch" → HAM
- "project update schedule" → HAM

P(SPAM) = 2/4 = 0.5, P(HAM) = 0.5

P("free"|SPAM) = 1/3, P("free"|HAM) = 0/3 ≈ 0.01 (Laplace smoothing)
P("update"|SPAM) ≈ 0.01, P("update"|HAM) = 1/3

Test: "free update"
P(SPAM|"free update") ∝ 0.5 × 1/3 × 0.01 = ?
P(HAM|"free update") ∝ 0.5 × 0.01 × 1/3 = ?

Hỏi: Email "free update" được phân loại là gì?
```

> **Đáp án:** Cả hai xác suất bằng nhau → **cần thêm data**  
> Nhưng nếu P("free"|SPAM)=0.5 và P("free"|HAM)=0.01:  
> P(SPAM) ∝ 0.5 × 0.5 × 0.01 = **0.0025**  
> P(HAM) ∝ 0.5 × 0.01 × 0.33 = **0.00165**  
> → **SPAM** (xác suất cao hơn)

---

### Bài 3 — Transformer Self-Attention (khái niệm)

```
Input: ["I", "love", "AI"]
Mỗi word có vector Q, K, V

Attention(Q,K,V) = softmax(QK^T / √d_k) · V

Với "love" làm query:
- Similarity với "I": score=3
- Similarity với "love": score=5
- Similarity với "AI": score=4

Sau softmax (scale √d_k=2):
scores = [3/2, 5/2, 4/2] = [1.5, 2.5, 2.0]
softmax([1.5, 2.5, 2.0]) ≈ ?

Hỏi: Word nào "love" chú ý nhất khi tính output vector?
```

> **Đáp án:** softmax([1.5, 2.5, 2.0]) ≈ [0.164, 0.592, 0.244]  
> → **"love" chú ý bản thân nhiều nhất** (weight 0.592)  
> **Giải thích:** Self-attention học cách các word liên quan nhau trong câu. Output của "love" là weighted sum của tất cả V vectors, với "love" tự trọng số nhiều nhất.

---

### Bài 4 — TF-IDF

```
Corpus:
Doc1: "the cat sat on the mat"
Doc2: "the cat in the hat"
Doc3: "the dog sat on the log"

TF("cat", Doc1) = 1/6 ≈ 0.167
TF("cat", Doc2) = 1/5 = 0.2

IDF("cat") = log(3/2) ≈ 0.405  [xuất hiện trong 2/3 docs]
IDF("sat") = log(3/2) ≈ 0.405  [xuất hiện trong 2/3 docs]
IDF("the") = log(3/3) = 0       [xuất hiện trong tất cả docs]

Hỏi:
a) TF-IDF("the", Doc1) = ?
b) TF-IDF("cat", Doc1) = ?
c) Word nào có TF-IDF = 0 trong mọi document?
```

> **Đáp án:**
> a) TF-IDF("the") = (2/6) × 0 = **0** — "the" quá phổ biến, không mang thông tin
> b) TF-IDF("cat", Doc1) = 0.167 × 0.405 ≈ **0.068**
> c) **"the"** — xuất hiện trong tất cả docs → IDF=0 → TF-IDF=0

---

### Bài 5 — RAG vs Fine-tuning

```
Tình huống:
- Công ty có 10,000 trang tài liệu nội bộ (cập nhật hàng tuần)
- Cần chatbot trả lời câu hỏi về tài liệu này
- Budget giới hạn

Hỏi: Nên dùng RAG hay Fine-tuning? Tại sao?
```

> **Đáp án: RAG (Retrieval-Augmented Generation)**  
> **Lý do:**
> - Tài liệu cập nhật hàng tuần → Fine-tune lại mỗi tuần rất tốn kém
> - RAG: chỉ cần update vector database khi tài liệu mới
> - Fine-tune phù hợp khi cần thay đổi behavior/style của model, không phải knowledge
> - RAG tốt hơn cho "factual knowledge" từ tài liệu cụ thể

---

## Bảng AI Tasks & Models

| Task | Input | Output | Model phổ biến |
|------|-------|--------|----------------|
| Text Classification | Text | Label | BERT, Logistic Reg |
| Text Generation | Prompt | Text | GPT-4, LLaMA |
| Translation | Text | Text | T5, mBART |
| Image Classification | Image | Label | ResNet, EfficientNet |
| Object Detection | Image | Boxes + Labels | YOLO, DETR |
| Speech-to-Text | Audio | Text | Whisper |
| Text-to-Image | Text | Image | Stable Diffusion, DALL-E |
| Recommendation | User/Item history | Items | Collaborative Filtering, NCF |

---

## Câu hỏi tự test nhanh

1. BERT vs GPT: hướng attention khác nhau thế nào? → BERT: bidirectional; GPT: unidirectional (left-to-right)
2. Zero-shot vs Few-shot prompting? → Zero-shot: không có ví dụ; Few-shot: có 1-5 ví dụ trong prompt
3. Tại sao Transformer thay thế RNN? → Parallel training, xử lý long-range dependency tốt hơn
4. Hallucination trong LLM là gì? → Model tạo ra thông tin sai nhưng nghe có vẻ đúng
5. RAG giải quyết vấn đề gì của LLM? → Knowledge cutoff và factual grounding với nguồn cụ thể
