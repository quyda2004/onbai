# NLP Basics — Xử lý Ngôn ngữ Tự nhiên

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng máy tính giống như một người nước ngoài không biết tiếng Việt. Khi bạn nói "Hôm nay trời đẹp quá!", máy tính chỉ thấy một chuỗi ký tự vô nghĩa.

**NLP (Natural Language Processing)** là tập hợp các kỹ thuật dạy máy tính "hiểu" và xử lý ngôn ngữ của con người. Quá trình này gồm nhiều bước:

1. **Tách từ (Tokenization)**: Chia câu thành các từ riêng biệt — như cắt một sợi dây thành nhiều đoạn nhỏ.
2. **Chuẩn hóa (Normalization)**: "Chạy", "chạy", "CHẠY" đều là một từ — đưa về dạng chuẩn.
3. **Loại bỏ từ vô nghĩa (Stopwords)**: Từ như "và", "là", "của" không mang nhiều thông tin — bỏ bớt đi.
4. **Biểu diễn số (Vectorization)**: Chuyển từ ngữ thành con số để máy tính tính toán được.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Text Preprocessing Pipeline

```
Raw Text → Tokenization → Lowercasing → Stopword Removal → Stemming/Lemmatization → Feature Extraction
```

**Tokenization**: Không chỉ đơn giản là `split()`. Word tokenizer phải xử lý "don't" → ["do", "n't"], "New York" → ["New York"]. Subword tokenization (BPE, WordPiece) dùng trong LLM để handle OOV words.

**Stemming vs Lemmatization**:
- **Stemming**: Cắt suffix bằng rules cứng. Nhanh nhưng thô. "running" → "run", "studies" → "studi" (sai ngữ pháp). Dùng Porter/Snowball stemmer.
- **Lemmatization**: Dùng từ điển + POS tag để tìm base form chính xác. "better" → "good", "ran" → "run". Chậm hơn nhưng chính xác hơn (spaCy, NLTK WordNet).

### Bag of Words (BoW)
Biểu diễn document bằng frequency vector. Vocabulary size = số từ unique trong corpus.

- **Ưu điểm**: Đơn giản, dễ implement, hiệu quả cho text classification cơ bản.
- **Nhược điểm**: Mất thông tin thứ tự ("dog bites man" vs "man bites dog"), sparse matrix (hàng triệu chiều), từ phổ biến ("the", "a") dominate.

### TF-IDF (Term Frequency — Inverse Document Frequency)
**TF(t, d)** = (số lần t xuất hiện trong d) / (tổng số từ trong d)
**IDF(t)** = log(N / df(t)) — N là tổng số document, df(t) là số document chứa t
**TF-IDF(t, d)** = TF(t, d) × IDF(t)

Ý tưởng: Từ xuất hiện nhiều trong một document nhưng ít trong corpus → quan trọng với document đó. Từ xuất hiện ở mọi document (stopwords) → IDF thấp → bị giảm trọng số tự động.

### Word Embeddings

**Word2Vec** — Neural network dự đoán từ từ context (hoặc ngược lại):
- **CBOW (Continuous Bag of Words)**: Dự đoán từ trung tâm từ context xung quanh. Input: context words → Output: center word.
- **Skip-gram**: Dự đoán context từ từ trung tâm. Input: center word → Output: context words. Hiệu quả hơn với rare words.
- Cả hai dùng negative sampling hoặc hierarchical softmax để tránh tính softmax full vocabulary.
- Kết quả: vector 100-300 chiều, king - man + woman ≈ queen (arithmetic của meanings).

**GloVe (Global Vectors)**: Dùng co-occurrence matrix toàn corpus. Tối ưu để F(w_i, w_j) ≈ log P(j|i). Nhanh hơn Word2Vec khi đã có co-occurrence matrix.

**Sentence Embeddings**: Trung bình word vectors (đơn giản), hoặc dùng BERT [CLS] token, sentence-transformers (SBERT) cho semantic similarity.

### N-gram Language Model
P(w_n | w_1, ..., w_{n-1}) ≈ P(w_n | w_{n-2}, w_{n-1}) — bigram/trigram assumption.
**Vấn đề**: Data sparsity (nhiều n-gram không xuất hiện trong training), không capture long-range dependencies.

---

## Định nghĩa chính xác

**NLP (Natural Language Processing)**: Nhánh của AI nghiên cứu các phương pháp cho phép máy tính phân tích, hiểu, và sinh ra ngôn ngữ tự nhiên của con người.

**TF-IDF**: Phương pháp đánh trọng số đặc trưng văn bản, phản ánh tầm quan trọng của một từ trong một document tương đối so với corpus. Công thức: TF-IDF(t, d, D) = TF(t, d) × log(|D| / |{d ∈ D: t ∈ d}|).

**Word Embedding**: Biểu diễn từ dưới dạng vector số thực dense trong không gian chiều thấp (thường 100–300 chiều), trong đó các từ có nghĩa tương tự có vector gần nhau.

---

## Bảng / Sơ đồ kỹ thuật

### So sánh các phương pháp biểu diễn văn bản

| Phương pháp | Chiều | Dense/Sparse | Thứ tự từ | Ngữ nghĩa | Tốc độ | Dùng cho |
|-------------|-------|--------------|-----------|-----------|--------|----------|
| BoW | |V| (>10k) | Sparse | Không | Không | Nhanh | Text classification cơ bản |
| TF-IDF | |V| (>10k) | Sparse | Không | Có (idf) | Nhanh | Information retrieval, search |
| Word2Vec | 100-300 | Dense | Không | Có (local) | Nhanh | NER, analogy tasks |
| GloVe | 50-300 | Dense | Không | Có (global) | Trung bình | Pre-trained embeddings |
| BERT | 768 | Dense | Có (attention) | Contextual | Chậm | Mọi NLP tasks |

### NLP Pipeline chuẩn

```
Raw Text
   |
   v
[Tokenization] → "Hello world!" → ["Hello", "world", "!"]
   |
   v
[Lowercasing]  → ["hello", "world", "!"]
   |
   v
[Stopword removal] → ["hello", "world"]
   |
   v
[Stemming/Lemmatization] → ["hello", "world"]
   |
   v
[Vectorization] → TF-IDF / Word2Vec / BERT
   |
   v
[Model] → Classification / NER / Sentiment / etc.
```

### Công thức TF-IDF chi tiết

```
TF(t, d)   = count(t in d) / len(d)
IDF(t)     = log( (1 + N) / (1 + df(t)) ) + 1   ← sklearn smooth variant
TF-IDF     = TF × IDF
```

---

## Code mẫu

```python
# ===== Phần 1: TF-IDF với scikit-learn =====
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

# Corpus mẫu
corpus = [
    "machine learning is a subset of artificial intelligence",
    "deep learning uses neural networks with many layers",
    "natural language processing deals with text data",
    "computer vision processes image and video data",
    "reinforcement learning trains agents through rewards",
]

# Khởi tạo TF-IDF vectorizer
tfidf = TfidfVectorizer(
    max_features=50,       # Chỉ lấy 50 từ quan trọng nhất
    stop_words='english',  # Loại stopwords tiếng Anh
    ngram_range=(1, 2),    # Dùng unigram + bigram
)

# Fit và transform corpus
tfidf_matrix = tfidf.fit_transform(corpus)
print(f"Shape of TF-IDF matrix: {tfidf_matrix.shape}")
print(f"Vocabulary size: {len(tfidf.vocabulary_)}")

# Semantic search: tìm document gần nhất với query
query = "what is deep learning?"
query_vec = tfidf.transform([query])
similarities = cosine_similarity(query_vec, tfidf_matrix)[0]

print("\n=== Search Results ===")
for idx in np.argsort(similarities)[::-1]:
    print(f"Score {similarities[idx]:.3f}: {corpus[idx]}")


# ===== Phần 2: Word2Vec với gensim =====
from gensim.models import Word2Vec
from gensim.utils import simple_preprocess

# Tokenize corpus (lowercase + remove punctuation)
tokenized_corpus = [simple_preprocess(doc) for doc in corpus]
print("\nTokenized:", tokenized_corpus[0])

# Train Word2Vec model
model = Word2Vec(
    sentences=tokenized_corpus,
    vector_size=50,    # Kích thước vector (thường 100-300 cho production)
    window=5,          # Context window size
    min_count=1,       # Bỏ qua từ xuất hiện < 1 lần
    workers=4,         # Số CPU threads
    sg=1,              # sg=1: Skip-gram, sg=0: CBOW
    epochs=100,        # Số vòng training
)

# Lấy vector của một từ
word = "learning"
if word in model.wv:
    vec = model.wv[word]
    print(f"\nVector of '{word}' (first 5 dims): {vec[:5]}")

# Tìm từ tương tự
similar_words = model.wv.most_similar("learning", topn=3)
print(f"\nWords similar to 'learning': {similar_words}")


# ===== Phần 3: Named Entity Recognition (NER) với spaCy =====
# pip install spacy && python -m spacy download en_core_web_sm

try:
    import spacy
    nlp = spacy.load("en_core_web_sm")
    
    text = "Apple CEO Tim Cook visited the OpenAI office in San Francisco last Monday."
    doc = nlp(text)
    
    print("\n=== Named Entities ===")
    for ent in doc.ents:
        print(f"  {ent.text:<20} → {ent.label_} ({spacy.explain(ent.label_)})")
    
    print("\n=== POS Tags ===")
    for token in doc:
        if not token.is_stop and not token.is_punct:
            print(f"  {token.text:<15} POS: {token.pos_:<8} Lemma: {token.lemma_}")

except ImportError:
    print("Install spaCy: pip install spacy && python -m spacy download en_core_web_sm")


# ===== Phần 4: N-gram Language Model đơn giản =====
from collections import defaultdict, Counter

def build_bigram_model(corpus_texts):
    """Xây dựng bigram language model."""
    bigram_counts = defaultdict(Counter)
    
    for text in corpus_texts:
        tokens = ["<START>"] + text.lower().split() + ["<END>"]
        for i in range(len(tokens) - 1):
            bigram_counts[tokens[i]][tokens[i+1]] += 1
    
    # Normalize thành probability
    bigram_probs = {}
    for word, next_words in bigram_counts.items():
        total = sum(next_words.values())
        bigram_probs[word] = {w: c/total for w, c in next_words.items()}
    
    return bigram_probs

model_lm = build_bigram_model(corpus)
print("\n=== Bigram LM: P(word | 'learning') ===")
if "learning" in model_lm:
    for w, p in sorted(model_lm["learning"].items(), key=lambda x: -x[1]):
        print(f"  P({w} | learning) = {p:.2f}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- TF-IDF: Search engine, keyword extraction, text similarity trong corpus nhỏ-trung bình.
- Word2Vec/GloVe: Cần pre-trained embeddings nhẹ, inference nhanh, không cần contextual understanding.
- N-gram: Language model đơn giản, spell checking, predictive text với resource ít.
- NLTK/spaCy NER: Production-ready NER pipeline không cần train custom model.

**Không dùng khi:**
- TF-IDF: Cần hiểu ngữ nghĩa sâu ("good" và "excellent" vẫn là 2 từ khác nhau trong BoW).
- Word2Vec: Từ đa nghĩa ("bank" — ngân hàng hay bờ sông?) vì chỉ có 1 vector cho mỗi từ.
- N-gram: Long-range dependencies (dùng RNN/Transformer thay thế).
- Khi bài toán cần contextual embeddings → dùng BERT/RoBERTa.

---

## So sánh với các khái niệm liên quan

| | Word2Vec | GloVe | FastText | BERT |
|-|----------|-------|---------|------|
| Training | Local window | Global co-occurrence | Subword + window | Masked LM |
| OOV handling | Không | Không | Có (subword) | Có (WordPiece) |
| Context-aware | Không | Không | Không | Có |
| Speed inference | Rất nhanh | Rất nhanh | Nhanh | Chậm |
| Tốt nhất cho | Word analogy | Semantic similarity | Morphology-rich languages | All NLP tasks |

---

## Lỗi thường gặp (Common Pitfalls)

- **Data leakage trong TF-IDF**: Fit TfidfVectorizer trên cả train+test set → IDF bị ảnh hưởng bởi test data. Luôn fit trên train, transform trên test.
- **Dùng stemming cho lemmatization**: Stemming "studies" → "studi" không phải base form đúng. Dùng lemmatizer khi cần từ có nghĩa.
- **Bỏ qua preprocessing**: "Machine", "machine", "MACHINE" là 3 từ khác nhau trong BoW nếu không lowercase.
- **Word2Vec với corpus nhỏ**: Cần ít nhất hàng triệu từ để embeddings có chất lượng. Với corpus nhỏ, dùng pre-trained embeddings (GloVe, fastText).
- **Cosine similarity vs Euclidean**: Với text vectors, luôn dùng cosine similarity vì độ dài vector phụ thuộc độ dài document.

---

## Câu hỏi phỏng vấn hay gặp

- TF-IDF khác BoW như thế nào? Tại sao TF-IDF thường tốt hơn?
- Skip-gram vs CBOW: cái nào tốt hơn với rare words và tại sao?
- Word2Vec không handle được từ đa nghĩa — giải pháp là gì?
- Stemming vs Lemmatization: trade-off và khi nào dùng cái nào?
- Giải thích IDF: tại sao từ xuất hiện nhiều trong corpus lại có IDF thấp?
- Sentence embeddings: tại sao không thể chỉ trung bình word vectors?
- Thiết kế text classification pipeline từ đầu đến cuối.
