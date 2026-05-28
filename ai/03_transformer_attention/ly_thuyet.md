# Transformer & Attention Mechanism

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn đang dịch câu: "Con mèo ngồi trên chiếc ghế vì **nó** mệt."

Để hiểu "nó" là ai, bạn phải nhìn lại cả câu và nhận ra "nó" đề cập đến "con mèo", không phải "chiếc ghế". Bộ não bạn tự động **chú ý** (attention) đến từ "con mèo" nhiều hơn khi xử lý từ "nó".

**Attention Mechanism** dạy máy tính làm điều tương tự: khi xử lý một từ, hãy "nhìn" vào tất cả các từ khác và quyết định từ nào quan trọng nhất.

**Transformer** là kiến trúc mạng neural sử dụng attention mechanism thay thế hoàn toàn RNN/LSTM. Đây là nền tảng của mọi LLM hiện đại: GPT, BERT, ChatGPT, Gemini, v.v.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Attention Mechanism: Query, Key, Value

Cơ chế attention được lấy cảm hứng từ information retrieval:
- **Query (Q)**: "Tôi đang tìm gì?" — vector biểu diễn từ hiện tại cần xử lý.
- **Key (K)**: "Tôi có thể cung cấp gì?" — vector nhãn của mỗi từ trong sequence.
- **Value (V)**: "Nội dung thực sự của tôi" — vector nội dung để aggregate.

Quá trình: Q dot product với tất cả K → softmax → weights → weighted sum of V.

### Scaled Dot-Product Attention

```
Attention(Q, K, V) = softmax(QK^T / √d_k) · V
```

- **QK^T**: Tính similarity giữa query và tất cả keys → score matrix (n×n).
- **/ √d_k**: Scale để tránh vanishing gradient khi d_k lớn (dot product có thể rất lớn → softmax saturate).
- **softmax(...)**: Normalize thành probability distribution (attention weights).
- **· V**: Weighted sum — từ nào được "chú ý" nhiều hơn sẽ contribute nhiều hơn vào output.

### Multi-Head Attention
Thay vì 1 attention, dùng h heads song song:

```
MultiHead(Q, K, V) = Concat(head_1, ..., head_h) · W^O
head_i = Attention(Q·W_i^Q, K·W_i^K, V·W_i^V)
```

**Tại sao cần nhiều head?** Mỗi head học một loại relationship khác nhau:
- Head 1: Coreference ("it" → "cat")
- Head 2: Syntactic dependency (subject-verb)
- Head 3: Semantic similarity
- v.v.

### Positional Encoding
Transformer không có built-in thứ tự (khác RNN). Cần thêm vị trí thông qua positional encoding:

```
PE(pos, 2i)   = sin(pos / 10000^(2i/d_model))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d_model))
```

- Dùng sin/cos với tần số khác nhau để encode position.
- **Absolute PE** (original Transformer): Cộng vào embedding.
- **Relative PE** (Transformer-XL, T5): Encode khoảng cách tương đối giữa tokens.
- **RoPE** (GPT-NeoX, LLaMA): Rotary PE — hiện tại phổ biến nhất.
- **ALiBi**: Không dùng PE, thêm linear bias vào attention scores — tốt cho generalization với longer sequences.

### Transformer Architecture

**Encoder** (BERT-style):
```
Input Embedding + PE
→ [Self-Attention → Add&Norm → FFN → Add&Norm] × N layers
→ Contextual representations
```

**Decoder** (GPT-style):
```
Output Embedding + PE
→ [Masked Self-Attention → Add&Norm → (Cross-Attention → Add&Norm) → FFN → Add&Norm] × N
→ Linear → Softmax → Next token
```

**Masked Self-Attention**: Trong decoder, chỉ attend đến các token trước đó (causal mask) để tránh "nhìn trộm" future tokens trong autoregressive generation.

**Cross-Attention**: Decoder queries attend đến encoder keys/values — cầu nối giữa source và target sequence (dùng trong seq2seq như T5, mT5).

### BERT vs GPT

| | BERT | GPT |
|-|------|-----|
| Architecture | Encoder-only | Decoder-only |
| Training objective | Masked LM + NSP | Autoregressive LM |
| Directionality | Bidirectional | Unidirectional (left-to-right) |
| Good for | Understanding tasks (classification, NER, QA) | Generation tasks |
| Examples | BERT, RoBERTa, ALBERT | GPT-2/3/4, LLaMA, Mistral |

### Complexity và Vấn đề Long Sequences

**Self-attention**: O(n²·d) time, O(n²) space — n là sequence length.
- Với n=512 (BERT): manageable.
- Với n=100k (long documents): không feasible.

**Giải pháp**:
- **Sparse Attention** (Longformer): Attend đến local window + global tokens.
- **Linear Attention** (Performer): Approximate softmax attention với O(n) complexity.
- **Flash Attention**: Không giảm complexity nhưng tối ưu IO-bound operations trên GPU.
- **Sliding Window** (Mistral): Local attention với mỗi token chỉ attend đến k neighbors.

---

## Định nghĩa chính xác

**Transformer**: Kiến trúc mạng neural dựa hoàn toàn vào attention mechanism, không dùng convolution hay recurrence, được giới thiệu trong bài báo "Attention Is All You Need" (Vaswani et al., 2017).

**Self-Attention**: Cơ chế attention trong đó query, key, value đều xuất phát từ cùng một sequence, cho phép mỗi position attend đến tất cả các position khác.

**Multi-Head Attention**: Chạy h attention functions song song trên các projected subspaces khác nhau, concatenate kết quả, cho phép model capture nhiều loại relationship đồng thời.

**Positional Encoding**: Vector được cộng vào input embeddings để inject thông tin về vị trí của token trong sequence, vì self-attention bản chất là permutation-invariant.

---

## Bảng / Sơ đồ kỹ thuật

### Kiến trúc Transformer đầy đủ

```
                    [Encoder]                    [Decoder]
                        |                            |
Input Tokens        [Embed+PE]              Output Tokens [Embed+PE]
                        |                            |
              ┌─────────────────┐          ┌──────────────────────┐
              │  Self-Attention │          │  Masked Self-Attention│
              │   (Bidirect.)   │          │   (Causal/Left-only)  │
              │   Add & Norm    │          │     Add & Norm        │
              │                 │          │                       │
              │  Feed Forward   │    ──→   │  Cross-Attention      │
              │   (2 layers)    │    K,V   │  (Query=Decoder,      │
              │   Add & Norm    │          │   Key/Value=Encoder)  │
              └─────────────────┘          │     Add & Norm        │
                     × N                   │                       │
                        |                  │  Feed Forward         │
               Contextual repr.            │   Add & Norm          │
                                           └──────────────────────┘
                                                   × N
                                                     |
                                              Linear + Softmax
                                                     |
                                               Next Token
```

### So sánh Encoder-only vs Decoder-only vs Encoder-Decoder

| | Encoder-only | Decoder-only | Encoder-Decoder |
|-|--------------|--------------|-----------------|
| Ví dụ | BERT, RoBERTa | GPT, LLaMA, Mistral | T5, BART, mT5 |
| Attention | Bidirectional | Causal (left-to-right) | Both |
| Dùng cho | Classification, NER, QA | Text generation, chat | Translation, summarization |
| Training | Masked LM | Next token prediction | Seq2Seq objectives |

### Complexity của các thao tác Transformer

| Thao tác | Time Complexity | Space Complexity | Ghi chú |
|----------|-----------------|------------------|---------|
| Self-Attention | O(n²·d) | O(n²) | n = seq len, d = dim |
| FFN | O(n·d·d_ff) | O(n·d_ff) | d_ff thường = 4d |
| Total per layer | O(n²·d + n·d²) | O(n²+n·d) | |
| Flash Attention | O(n²·d) | O(n) | IO-aware, no full n² materialization |

---

## Code mẫu

```python
import numpy as np

def softmax(x, axis=-1):
    """Numerically stable softmax."""
    e_x = np.exp(x - np.max(x, axis=axis, keepdims=True))
    return e_x / e_x.sum(axis=axis, keepdims=True)


def scaled_dot_product_attention(Q, K, V, mask=None):
    """
    Scaled Dot-Product Attention từ scratch.
    
    Args:
        Q: Query matrix, shape (batch, heads, seq_len, d_k)
        K: Key matrix, same shape as Q
        V: Value matrix, shape (batch, heads, seq_len, d_v)
        mask: Optional causal mask (batch, 1, seq_len, seq_len)
    
    Returns:
        output: (batch, heads, seq_len, d_v)
        attention_weights: (batch, heads, seq_len, seq_len)
    """
    d_k = Q.shape[-1]  # Dimension của key/query
    
    # Step 1: Tính attention scores = Q·K^T / √d_k
    # (batch, heads, seq_len_q, seq_len_k)
    scores = np.matmul(Q, K.transpose(0, 1, 3, 2)) / np.sqrt(d_k)
    
    # Step 2: Apply mask (causal mask cho decoder)
    if mask is not None:
        # Điền -inf vào vị trí bị mask → softmax sẽ cho weight ≈ 0
        scores = scores + (mask * -1e9)
    
    # Step 3: Softmax để có attention weights
    attention_weights = softmax(scores, axis=-1)
    
    # Step 4: Weighted sum of values
    output = np.matmul(attention_weights, V)
    
    return output, attention_weights


def create_causal_mask(seq_len):
    """
    Tạo causal mask cho decoder: token i chỉ attend đến j <= i.
    Ma trận upper triangular (trên đường chéo) = 1 → bị mask.
    """
    mask = np.triu(np.ones((seq_len, seq_len)), k=1)  # k=1: above diagonal
    return mask[np.newaxis, np.newaxis, :, :]  # (1, 1, seq_len, seq_len)


class MultiHeadAttention:
    """Multi-Head Attention bằng numpy — educational implementation."""
    
    def __init__(self, d_model: int, num_heads: int):
        assert d_model % num_heads == 0, "d_model phải chia hết cho num_heads"
        
        self.d_model = d_model
        self.num_heads = num_heads
        self.d_k = d_model // num_heads  # Chiều của mỗi head
        
        # Weight matrices (randomly initialized cho demo)
        # Trong thực tế, đây là learnable parameters
        self.W_Q = np.random.randn(d_model, d_model) * 0.1
        self.W_K = np.random.randn(d_model, d_model) * 0.1
        self.W_V = np.random.randn(d_model, d_model) * 0.1
        self.W_O = np.random.randn(d_model, d_model) * 0.1
    
    def split_heads(self, x, batch_size):
        """
        Reshape để tách thành num_heads heads.
        (batch, seq_len, d_model) → (batch, num_heads, seq_len, d_k)
        """
        x = x.reshape(batch_size, -1, self.num_heads, self.d_k)
        return x.transpose(0, 2, 1, 3)
    
    def forward(self, Q_input, K_input, V_input, mask=None):
        """
        Forward pass của Multi-Head Attention.
        
        Args:
            Q_input, K_input, V_input: (batch, seq_len, d_model)
        """
        batch_size = Q_input.shape[0]
        
        # Linear projections
        Q = np.matmul(Q_input, self.W_Q)  # (batch, seq, d_model)
        K = np.matmul(K_input, self.W_K)
        V = np.matmul(V_input, self.W_V)
        
        # Split thành multiple heads
        Q = self.split_heads(Q, batch_size)  # (batch, heads, seq, d_k)
        K = self.split_heads(K, batch_size)
        V = self.split_heads(V, batch_size)
        
        # Scaled dot-product attention cho tất cả heads song song
        attn_output, attn_weights = scaled_dot_product_attention(Q, K, V, mask)
        
        # Concatenate heads: (batch, heads, seq, d_k) → (batch, seq, d_model)
        attn_output = attn_output.transpose(0, 2, 1, 3)
        attn_output = attn_output.reshape(batch_size, -1, self.d_model)
        
        # Final linear projection
        output = np.matmul(attn_output, self.W_O)
        
        return output, attn_weights


def positional_encoding(max_len: int, d_model: int):
    """
    Sinusoidal Positional Encoding.
    Mỗi dimension dùng sin/cos với tần số khác nhau.
    """
    PE = np.zeros((max_len, d_model))
    positions = np.arange(max_len)[:, np.newaxis]  # (max_len, 1)
    
    # Tần số cho mỗi dimension: 1/10000^(2i/d_model)
    div_term = np.exp(
        np.arange(0, d_model, 2) * (-np.log(10000.0) / d_model)
    )
    
    # Even dimensions → sin, odd dimensions → cos
    PE[:, 0::2] = np.sin(positions * div_term)
    PE[:, 1::2] = np.cos(positions * div_term)
    
    return PE


# ===== Demo =====
if __name__ == "__main__":
    np.random.seed(42)
    
    # Hyperparameters
    batch_size = 2
    seq_len = 8
    d_model = 64
    num_heads = 4
    
    # Giả lập input embeddings + positional encoding
    embeddings = np.random.randn(batch_size, seq_len, d_model)
    PE = positional_encoding(seq_len, d_model)
    x = embeddings + PE[np.newaxis, :, :]  # Broadcast over batch
    
    print(f"Input shape: {x.shape}")  # (2, 8, 64)
    
    # Self-Attention (Q=K=V=x cho encoder)
    mha = MultiHeadAttention(d_model=d_model, num_heads=num_heads)
    output, weights = mha.forward(x, x, x)
    
    print(f"Output shape: {output.shape}")        # (2, 8, 64)
    print(f"Attention weights shape: {weights.shape}")  # (2, 4, 8, 8)
    
    # Demo causal mask (cho decoder)
    causal_mask = create_causal_mask(seq_len)
    output_masked, weights_masked = mha.forward(x, x, x, mask=causal_mask)
    
    print(f"\nWith causal mask:")
    print(f"Attention weights[0, 0] (head 0, sample 0):")
    print(np.round(weights_masked[0, 0], 2))
    # Token 0 chỉ attend đến token 0 (diagonal),
    # Token 1 attend đến token 0 và 1, v.v.
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Bài toán NLP yêu cầu hiểu ngữ nghĩa sâu và long-range dependencies.
- Text generation, machine translation, summarization.
- Fine-tune BERT/GPT cho downstream tasks thay vì train từ đầu.
- Multi-modal tasks: Vision Transformer (ViT) cho images.

**Không dùng khi:**
- Resource bị hạn chế nghiêm trọng (mobile/edge) — dùng distilled models (DistilBERT, MobileBERT).
- Sequence rất dài (>100k tokens) với standard Transformer — cần Longformer, BigBird, hoặc state space models (Mamba).
- Bài toán đơn giản không cần contextual understanding — TF-IDF + logistic regression đủ dùng.
- Real-time streaming với latency constraint cực thấp.

---

## So sánh với các khái niệm liên quan

| | RNN/LSTM | Transformer | SSM (Mamba) |
|-|---------|-------------|-------------|
| Xử lý thứ tự | Sequential | Parallel | Sequential (but efficient) |
| Long-range dep. | Yếu (vanishing gradient) | Mạnh (direct attention) | Mạnh (selective state) |
| Complexity | O(n·d²) time, O(n) space | O(n²·d) time, O(n²) space | O(n·d) time, O(d) space |
| Training | Khó parallel hóa | Dễ parallel | Dễ parallel |
| Best for | Streaming, short seq | NLP tasks, LLMs | Very long sequences |

---

## Lỗi thường gặp (Common Pitfalls)

- **Quên scale QK^T bởi √d_k**: Với d_k lớn, dot product có variance lớn → softmax bị push vào vùng gradient nhỏ (vanishing gradient). Scaling là bắt buộc.
- **Causal mask trong training vs inference**: Encoder không cần mask; Decoder luôn cần causal mask trong training. Khi inference autoregressive, mask thay đổi theo từng bước.
- **Positional encoding và sequence length**: Pre-trained models có max sequence length (BERT: 512). Input dài hơn cần truncate hoặc sliding window.
- **Nhầm Encoder vs Decoder use cases**: BERT (encoder) không sinh văn bản tốt; GPT (decoder) không tốt cho classification nếu không fine-tune đúng cách.
- **Attention không phải là interpretability**: Attention weights không nhất thiết giải thích tại sao model đưa ra decision (attention không phải explanation).

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích công thức Attention(Q,K,V) = softmax(QK^T/√d_k)V — tại sao cần √d_k?
- Multi-head attention giải quyết vấn đề gì mà single-head không làm được?
- Tại sao Transformer cần Positional Encoding trong khi RNN thì không?
- BERT dùng bidirectional attention — tại sao điều này quan trọng cho NLU?
- GPT dùng causal (unidirectional) attention — tại sao phù hợp với generation?
- Self-attention có complexity O(n²) — giải pháp nào cho long sequences?
- Encoder-only vs Decoder-only vs Encoder-Decoder: dùng cái nào cho bài toán gì?
- Layer Normalization được đặt ở đâu trong Transformer và tại sao (Pre-LN vs Post-LN)?
