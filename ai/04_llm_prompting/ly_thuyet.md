# LLM & Prompt Engineering

---

## Giải thích cho người mới hoàn toàn

**LLM (Large Language Model)** như ChatGPT, Gemini, Claude là những "siêu trợ lý văn bản" được huấn luyện bằng cách đọc hàng nghìn tỷ từ từ internet, sách, và nhiều nguồn khác.

Cách chúng học: Đọc câu "Hôm nay trời ___ quá" → đoán từ tiếp theo là "đẹp". Lặp đi lặp lại hàng tỷ lần với vô số câu văn, chúng dần hiểu ngôn ngữ, kiến thức, và logic.

**Prompt Engineering** là nghệ thuật đặt câu hỏi/ra lệnh cho LLM một cách thông minh để nhận được câu trả lời tốt nhất — như cách bạn biết cách đặt câu hỏi khéo léo với một chuyên gia.

**RAG (Retrieval-Augmented Generation)** là kỹ thuật cho LLM "tra cứu tài liệu" trước khi trả lời — như cho phép thí sinh mang tài liệu vào phòng thi.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### LLM: Pretraining → Fine-tuning → RLHF

**1. Pretraining (Self-supervised)**:
- Objective: Next-token prediction trên massive corpus (web, books, code).
- Model học language structure, world knowledge, reasoning.
- Chi phí khổng lồ: GPT-3 ~$4.6M, GPT-4 ước tính >$100M.
- Output: Base model — hiểu ngôn ngữ nhưng chưa follow instructions tốt.

**2. Supervised Fine-Tuning (SFT)**:
- Train trên curated dataset: instruction-response pairs.
- Dạy model format output, follow instructions, avoid harmful content.
- Chỉ cần vài nghìn đến vài trăm nghìn examples.

**3. RLHF (Reinforcement Learning from Human Feedback)**:
- Human raters so sánh các responses: "Response A tốt hơn B".
- Train **Reward Model (RM)** từ preference data.
- Fine-tune LLM dùng PPO để maximize reward.
- Kết quả: Model align tốt hơn với human preferences (helpful, harmless, honest).

### Prompt Engineering Techniques

**Zero-shot**: Không có ví dụ, chỉ instruction.
```
"Phân loại sentiment của câu sau: 'Sản phẩm này tuyệt vời!'"
→ Positive
```

**Few-shot**: Cung cấp ví dụ trước khi đặt câu hỏi thực tế.
```
"Phân loại:
'Rất tệ' → Negative
'Bình thường' → Neutral
'Thích lắm' → Positive
'Không như mong đợi' → ?"
```

**Chain-of-Thought (CoT)**: Yêu cầu model giải thích từng bước.
```
"Hãy giải bước từng bước: Roger có 5 quả bóng tennis. Anh ấy mua thêm 2 hộp, mỗi hộp 3 quả. Tổng cộng bao nhiêu quả?"
→ Roger bắt đầu với 5 quả. Mua 2×3=6 quả nữa. Tổng: 5+6=11 quả.
```

**Zero-shot CoT**: Chỉ cần thêm "Let's think step by step" vào prompt — surprisingly effective.

**Self-consistency**: Sample nhiều CoT paths, majority vote → kết quả ổn định hơn.

### Fine-tuning Techniques

**Full Fine-tuning**: Cập nhật toàn bộ weights. Tốn kém nhưng hiệu quả nhất. GPT-3 175B parameters = cần nhiều A100 GPU.

**LoRA (Low-Rank Adaptation)**:
- Freeze pretrained weights W, thêm low-rank matrices: W' = W + BA (B: d×r, A: r×k, r<<d).
- Chỉ train A và B → giảm trainable parameters từ hàng tỷ xuống hàng triệu.
- Ví dụ: LLaMA 7B có 7B params, LoRA chỉ train ~4M params (0.06%).

**QLoRA**: Quantize model xuống 4-bit (NF4 quantization) + LoRA. Cho phép fine-tune 65B model trên single A100 80GB.

**PEFT (Parameter-Efficient Fine-Tuning)**: Umbrella term cho LoRA, Adapters, Prefix Tuning, Prompt Tuning.

### RAG (Retrieval-Augmented Generation)

**Kiến trúc RAG cơ bản**:
```
User Query
    ↓
[Embedding Model] → Query Vector
    ↓
[Vector Database] → Top-K relevant chunks (cosine similarity)
    ↓
[Context Assembly] → Query + Retrieved Chunks → Prompt
    ↓
[LLM] → Generated Answer with citations
```

**Components**:
- **Document Chunking**: Chia documents thành chunks (256-512 tokens). Overlap để không mất context ở ranh giới.
- **Embedding Model**: all-MiniLM-L6, text-embedding-ada-002, e5-large để encode chunks.
- **Vector DB**: Pinecone, Weaviate, Chroma, Milvus, pgvector.
- **Retrieval**: Dense retrieval (vector similarity) hoặc Hybrid (dense + BM25 sparse).
- **Reranking**: Cross-encoder reranker (chậm hơn nhưng chính xác hơn bi-encoder).

### RAG vs Fine-tuning

| Tiêu chí | RAG | Fine-tuning |
|---------|-----|-------------|
| Knowledge update | Dễ (chỉ update vector DB) | Khó (cần retrain) |
| Domain knowledge | Tốt (nếu docs đầy đủ) | Tốt |
| Style/format | Không tốt | Tốt |
| Latency | Cao hơn | Thấp hơn |
| Cost | Thấp hơn | Cao hơn |
| Hallucination | Ít hơn | Nhiều hơn |
| Dùng khi | Knowledge cần cập nhật thường xuyên, sensitive data | Specific output format, domain-specific language |

### Hallucination

**Nguyên nhân**:
- Model học để tạo ra "plausible-sounding" text, không nhất thiết là factual.
- Training data không chứa thông tin hoặc chứa thông tin sai.
- Model không có mechanism để nói "Tôi không biết".

**Giảm thiểu**:
- RAG: Grounding responses vào retrieved documents.
- Temperature=0: Greedy decoding ít hallucinate hơn (nhưng kém creative).
- System prompt: "Chỉ trả lời dựa trên thông tin được cung cấp. Nếu không biết, nói không biết."
- Citation requirement: Yêu cầu model cite nguồn.
- Self-consistency: Multiple sampling + voting.

### Evaluation Metrics

| Metric | Dùng cho | Cách tính |
|--------|----------|-----------|
| BLEU | Machine translation | n-gram precision (predicted vs reference) |
| ROUGE | Summarization | n-gram recall (predicted vs reference) |
| BERTScore | Generation quality | Cosine similarity of BERT embeddings |
| Perplexity | Language model quality | exp(-mean log P(w_i)) |
| LLM-as-Judge | Complex tasks | Dùng GPT-4 để đánh giá response |
| Human eval | Gold standard | A/B testing với human raters |

---

## Định nghĩa chính xác

**LLM (Large Language Model)**: Mạng neural transformer quy mô lớn (hàng tỷ parameters), được pretrain trên corpus văn bản khổng lồ với objective dự đoán token tiếp theo, có khả năng thực hiện nhiều tác vụ NLP với zero/few-shot prompting.

**Prompt Engineering**: Kỹ thuật thiết kế input prompts để elicit desired behavior từ LLM mà không cần thay đổi model weights.

**RAG (Retrieval-Augmented Generation)**: Phương pháp kết hợp information retrieval với text generation, cho phép model truy cập external knowledge base khi inference thay vì chỉ dựa vào parametric knowledge.

**LoRA (Low-Rank Adaptation)**: Phương pháp PEFT decompose weight update matrix thành tích của hai ma trận low-rank, giảm đáng kể số trainable parameters trong fine-tuning.

---

## Bảng / Sơ đồ kỹ thuật

### LLM Training Pipeline

```
Stage 1: PRETRAINING
────────────────────
Massive corpus (Common Crawl, books, GitHub, Wikipedia)
         ↓
   Base Model (next-token prediction)
   - Hiểu ngôn ngữ sâu sắc
   - Chứa world knowledge
   - Chưa follow instructions tốt

Stage 2: SUPERVISED FINE-TUNING (SFT)
──────────────────────────────────────
Curated instruction-response dataset (vài chục nghìn pairs)
         ↓
   SFT Model
   - Follow instructions
   - Better format

Stage 3: RLHF
─────────────
Human preferences (A better than B)
         ↓ Train Reward Model
Reward Model → PPO Fine-tuning
         ↓
   RLHF Model (ChatGPT, Claude, Gemini)
   - Aligned with human values
   - Helpful, Harmless, Honest
```

### LoRA: Cơ chế hoạt động

```
Original weight matrix W: (d_out × d_in)
                              ↓
         Frozen (không update trong training)

LoRA addition:
  ΔW = B × A
  B: (d_out × r)   ← Khởi tạo random
  A: (r × d_in)    ← Khởi tạo zero (để ΔW=0 lúc đầu)
  r << d (rank, thường r=4, 8, 16, 32)

Forward: h = (W + ΔW)x = Wx + BAx
                         ↑ Chỉ BA được update trong training

Merged at inference: W_merged = W + BA (zero overhead)
```

### RAG Pipeline chi tiết

```
INDEXING (offline, một lần)
──────────────────────────
Documents → Chunking (256-512 tokens, overlap 50)
                ↓
          Embedding Model → Dense Vectors
                ↓
          Vector Database (Chroma/Pinecone/etc.)

RETRIEVAL (online, mỗi query)
─────────────────────────────
User Query → Embedding → Query Vector
                ↓
          ANN Search (HNSW/IVF)
                ↓
          Top-K chunks (K=3~10)
                ↓ (optional)
          Reranker (Cross-encoder)
                ↓
          Final context

GENERATION
──────────
System prompt + Context + User query → LLM → Answer
```

---

## Code mẫu

```python
# ===== Phần 1: Prompt Engineering Patterns =====

from openai import OpenAI
import json

client = OpenAI()  # Cần OPENAI_API_KEY env var


def zero_shot(question: str) -> str:
    """Zero-shot prompting — không có ví dụ."""
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "user", "content": question}
        ],
        temperature=0,
    )
    return response.choices[0].message.content


def few_shot_sentiment(text: str) -> str:
    """Few-shot prompting cho sentiment analysis."""
    system_prompt = """Bạn là model phân tích sentiment. Phân loại thành: Positive, Negative, hoặc Neutral.
Chỉ trả về một trong ba từ đó, không giải thích thêm."""
    
    examples = [
        {"role": "user", "content": "Sản phẩm tuyệt vời, giao hàng nhanh!"},
        {"role": "assistant", "content": "Positive"},
        {"role": "user", "content": "Hàng bình thường, không có gì đặc biệt."},
        {"role": "assistant", "content": "Neutral"},
        {"role": "user", "content": "Chất lượng kém, không đáng mua."},
        {"role": "assistant", "content": "Negative"},
    ]
    
    messages = [{"role": "system", "content": system_prompt}]
    messages.extend(examples)
    messages.append({"role": "user", "content": text})
    
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages,
        temperature=0,
    )
    return response.choices[0].message.content


def chain_of_thought(problem: str) -> str:
    """Chain-of-Thought: Yêu cầu model suy luận từng bước."""
    prompt = f"""Hãy giải bài toán sau từng bước một, giải thích rõ ràng:

Bài toán: {problem}

Hãy:
1. Xác định các thông tin đã cho
2. Xác định yêu cầu
3. Giải từng bước
4. Kết luận

Giải:"""
    
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": prompt}],
        temperature=0,
    )
    return response.choices[0].message.content


# ===== Phần 2: RAG đơn giản với Chroma + sentence-transformers =====
# pip install chromadb sentence-transformers

def build_rag_demo():
    """Demo RAG pipeline đơn giản."""
    try:
        import chromadb
        from sentence_transformers import SentenceTransformer
        
        # Embedding model nhẹ, chạy local
        embedder = SentenceTransformer('all-MiniLM-L6-v2')
        
        # Khởi tạo vector database in-memory
        client_db = chromadb.Client()
        collection = client_db.create_collection("knowledge_base")
        
        # ===== INDEXING =====
        documents = [
            "Python là ngôn ngữ lập trình thông dịch, hướng đối tượng, được tạo bởi Guido van Rossum năm 1991.",
            "Machine learning là nhánh của AI cho phép máy tính học từ dữ liệu mà không cần lập trình tường minh.",
            "Neural network được lấy cảm hứng từ cấu trúc não người, gồm các layers of neurons kết nối với nhau.",
            "Transformer là kiến trúc sử dụng self-attention mechanism, giới thiệu trong paper 'Attention Is All You Need' năm 2017.",
            "RAG kết hợp retrieval với generation để giúp LLM truy cập thông tin cập nhật mà không cần retrain.",
        ]
        
        # Encode documents thành vectors
        embeddings = embedder.encode(documents).tolist()
        
        # Lưu vào vector DB
        collection.add(
            documents=documents,
            embeddings=embeddings,
            ids=[f"doc_{i}" for i in range(len(documents))],
        )
        print(f"Indexed {len(documents)} documents")
        
        # ===== RETRIEVAL =====
        def retrieve(query: str, top_k: int = 2):
            """Tìm các documents liên quan nhất với query."""
            query_embedding = embedder.encode([query]).tolist()
            results = collection.query(
                query_embeddings=query_embedding,
                n_results=top_k,
            )
            return results["documents"][0]  # List of relevant chunks
        
        # ===== GENERATION =====
        def rag_answer(query: str) -> str:
            """Trả lời câu hỏi dựa trên retrieved context."""
            context_chunks = retrieve(query)
            context = "\n".join([f"- {chunk}" for chunk in context_chunks])
            
            prompt = f"""Dựa vào thông tin sau, hãy trả lời câu hỏi.
Nếu thông tin không đủ, hãy nói "Tôi không có đủ thông tin".

Thông tin:
{context}

Câu hỏi: {query}
Trả lời:"""
            
            print(f"\nQuery: {query}")
            print(f"Retrieved context:\n{context}")
            print(f"Prompt sent to LLM:\n{prompt}\n")
            
            # Trong demo này giả lập LLM response
            return f"[LLM Response based on context above]"
        
        # Test
        rag_answer("Transformer được giới thiệu khi nào?")
        rag_answer("RAG là gì?")
        
    except ImportError:
        print("Install: pip install chromadb sentence-transformers")


# ===== Phần 3: Đánh giá BLEU và ROUGE =====
def compute_rouge_1(prediction: str, reference: str) -> float:
    """
    Tính ROUGE-1 F1 score đơn giản (không dùng thư viện).
    ROUGE-1: overlap của unigrams giữa prediction và reference.
    """
    pred_tokens = set(prediction.lower().split())
    ref_tokens = set(reference.lower().split())
    
    if not pred_tokens or not ref_tokens:
        return 0.0
    
    overlap = pred_tokens & ref_tokens
    precision = len(overlap) / len(pred_tokens)
    recall = len(overlap) / len(ref_tokens)
    
    if precision + recall == 0:
        return 0.0
    
    f1 = 2 * precision * recall / (precision + recall)
    return f1


# Demo
if __name__ == "__main__":
    # Sentiment analysis
    texts = [
        "Ứng dụng này thật tuyệt vời!",
        "Dịch vụ khách hàng rất tệ.",
        "Sản phẩm bình thường, không đặc biệt.",
    ]
    
    print("=== Few-shot Sentiment Analysis ===")
    # for text in texts:
    #     result = few_shot_sentiment(text)
    #     print(f"'{text}' → {result}")
    
    # ROUGE demo
    print("\n=== ROUGE-1 Demo ===")
    pred = "The cat sat on the mat near the door"
    ref = "The cat sat on the mat"
    score = compute_rouge_1(pred, ref)
    print(f"Prediction: '{pred}'")
    print(f"Reference:  '{ref}'")
    print(f"ROUGE-1 F1: {score:.3f}")
    
    # Build RAG demo
    print("\n=== RAG Demo ===")
    build_rag_demo()
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- **Zero/Few-shot**: Bài toán không có training data, hoặc cần quick prototype.
- **CoT**: Bài toán reasoning phức tạp, math, code generation.
- **RAG**: Cần knowledge cập nhật thường xuyên (news, docs nội bộ), domain-specific knowledge.
- **Fine-tuning (LoRA)**: Cần specific output format, domain jargon, hoặc style nhất quán.
- **RLHF**: Production systems cần align với safety guidelines.

**Không dùng khi:**
- **LLM** cho structured prediction với training data đủ lớn → fine-tune BERT hoặc dùng traditional ML.
- **RAG** khi latency rất quan trọng → fine-tuning bake knowledge vào model.
- **Fine-tuning** khi data ít (<1000 examples) → few-shot prompting hiệu quả hơn.
- **CoT** với gpt-3.5 cho toán học phức tạp → dùng GPT-4 hoặc specialized model.

---

## So sánh với các khái niệm liên quan

| | RAG | Fine-tuning | Pretraining | In-context Learning |
|-|-----|-------------|-------------|---------------------|
| Data needed | Documents | Labeled examples | Massive corpus | Examples in prompt |
| Update knowledge | Dễ | Khó (retrain) | Rất khó | Không lưu lại |
| Cost | Thấp | Trung bình | Rất cao | Miễn phí |
| Hallucination | Ít | Có thể | N/A | Phụ thuộc model |
| Latency | Cao hơn | Thấp | N/A | Thấp |

---

## Lỗi thường gặp (Common Pitfalls)

- **Prompt injection**: User input override system instructions. Luôn sanitize/separate user input.
- **Hallucination không kiểm soát**: Dùng temperature cao cho factual tasks → sai nhiều. Dùng temperature=0 cho factual.
- **RAG chunking kém**: Chunk quá nhỏ mất context, quá lớn noise nhiều. Cần thử nghiệm chunk size.
- **Fine-tuning trên noisy data**: Garbage in, garbage out. Model học cả mistakes trong training data.
- **LoRA rank quá nhỏ**: r=1 không đủ capacity, r=64 gần như full fine-tune. Thường r=8 hoặc r=16 là sweet spot.
- **Context window overflow**: Nhét quá nhiều context → model bị lost in the middle (middle tokens bị ignore).
- **Evaluation gaming**: BLEU/ROUGE cao không = output tốt. Cần human eval cho production.

---

## Câu hỏi phỏng vấn hay gặp

- RLHF là gì? Tại sao cần RLHF sau SFT?
- Zero-shot vs Few-shot vs Chain-of-Thought: khi nào dùng cái nào?
- LoRA hoạt động như thế nào? Tại sao hiệu quả hơn full fine-tuning?
- RAG vs Fine-tuning: khi nào dùng RAG, khi nào dùng fine-tuning?
- Hallucination trong LLM là gì và làm thế nào để giảm thiểu?
- Context window là gì? Khi nào gặp vấn đề với long context?
- BLEU score có phải metric tốt nhất để đánh giá LLM không? Tại sao?
- Giải thích quá trình chunking trong RAG và trade-off của chunk size.
