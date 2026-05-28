# Neural Network & Backpropagation

---

## Giải thích cho người mới hoàn toàn

Não người có hàng tỷ tế bào thần kinh (neurons). Mỗi neuron nhận tín hiệu từ nhiều neuron khác, nếu tổng tín hiệu đủ mạnh thì nó "bắn" tín hiệu tiếp cho các neuron tiếp theo.

**Neural Network** mô phỏng điều này bằng máy tính: mỗi "neuron nhân tạo" nhận nhiều số, cộng lại (có trọng số), rồi quyết định "bắn" hay không qua một hàm kích hoạt.

Quá trình học giống như chỉnh volume của nhiều nút vặn: ban đầu các trọng số ngẫu nhiên, network đưa ra dự đoán sai, sau đó tính toán "lỗi" rồi điều chỉnh ngược từ output về input (gọi là **backpropagation**) — làm đi làm lại hàng nghìn lần cho đến khi network dự đoán đúng.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Perceptron → MLP

**Single Perceptron**: `output = activation(w^T x + b)` — chỉ học được linearly separable.

**Multi-Layer Perceptron (MLP)**: nhiều lớp (layers) với non-linear activation functions giữa các lớp → học được **bất kỳ hàm liên tục nào** (Universal Approximation Theorem).

### Forward Pass
Cho layer l:
```
Z[l] = W[l] · A[l-1] + b[l]    (linear combination)
A[l] = g[l](Z[l])               (activation function)
```

### Backpropagation — Chain Rule

Dùng chain rule để tính gradient của loss L theo mọi parameters:
```
∂L/∂W[l] = ∂L/∂Z[l] · ∂Z[l]/∂W[l]
           = δ[l] · A[l-1]^T

δ[l] = ∂L/∂Z[l] = (W[l+1]^T · δ[l+1]) * g'[l](Z[l])    (hidden layers)
δ[L] = ∂L/∂Z[L] = A[L] - y                                (output layer, CE loss)
```

**Gradient flow** đi ngược từ output về input, nhân dần các Jacobian matrices.

### Vanishing Gradient Problem
- Với Sigmoid/Tanh: `σ'(z) ≤ 0.25` — gradient shrink theo cấp số nhân qua mỗi lớp.
- Deep networks (nhiều layers): gradient gần như = 0 ở early layers → không learn được.
- **Giải pháp**: ReLU, BatchNorm, Residual connections (ResNet), weight initialization tốt (He init).

### Exploding Gradient Problem
- Ngược lại: gradient lớn dần theo cấp số nhân → weights update quá lớn → NaN.
- **Giải pháp**: Gradient Clipping (`torch.nn.utils.clip_grad_norm_`), careful init.

---

## Định nghĩa chính xác

**Neural Network (MLP)**: mô hình học máy gồm các lớp linear transformations xen kẽ với các non-linear activation functions, có khả năng xấp xỉ bất kỳ hàm số liên tục nào (Universal Approximation Theorem).

**Backpropagation**: thuật toán tính gradient hiệu quả của loss function theo tất cả parameters bằng chain rule của calculus, propagating errors từ output về input.

---

## Công thức / Bảng kỹ thuật

### Activation Functions

| Hàm | Công thức | Đạo hàm | Ưu điểm | Nhược điểm |
|-----|-----------|---------|---------|-----------|
| Sigmoid | 1/(1+e^-z) | σ(1-σ) | Output [0,1] | Vanishing gradient |
| Tanh | (e^z-e^-z)/(e^z+e^-z) | 1-tanh² | Zero-centered | Vanishing gradient |
| ReLU | max(0,z) | 1 if z>0 else 0 | Gradient không vanish | Dying ReLU (z<0 → 0) |
| Leaky ReLU | max(αz,z), α≈0.01 | 1 if z>0 else α | Fix dying ReLU | Thêm hyperparameter |
| ELU | z if z>0 else α(e^z-1) | 1 if z>0 else output+α | Smooth, zero-mean | Chậm hơn |
| Softmax | e^zᵢ/Σe^zⱼ | Jacobian | Xác suất multiclass | Chỉ dùng output layer |

**Khi nào dùng activation nào:**
- Hidden layers: ReLU mặc định, Leaky ReLU nếu dying ReLU
- Output layer (binary): Sigmoid
- Output layer (multiclass): Softmax
- Output layer (regression): không dùng activation (linear)

### Loss Functions

| Task | Loss Function | Công thức |
|------|--------------|-----------|
| Regression | MSE | (1/m)Σ(ŷ-y)² |
| Binary Classification | Binary Cross-Entropy | -(1/m)Σ[y log ŷ + (1-y)log(1-ŷ)] |
| Multiclass | Categorical Cross-Entropy | -(1/m)ΣΣ yₖ log ŷₖ |

### Optimizers So Sánh

| Optimizer | Update Rule (tóm tắt) | Ưu điểm | Nhược điểm |
|-----------|----------------------|---------|-----------|
| SGD | w -= α∇J | Đơn giản | Chậm, oscillate |
| Momentum | v = βv + ∇J; w -= αv | Ổn định hơn | Thêm β |
| RMSprop | Sᵢ = βSᵢ + (1-β)(∇J)²; w -= α∇J/√S | Adaptive LR | Không có momentum |
| Adam | Kết hợp Momentum + RMSprop | Tốt nhất mặc định | Tốn bộ nhớ hơn |

### Adam Update Rule (chi tiết)
```
mₜ = β₁mₜ₋₁ + (1-β₁)gₜ          (first moment, momentum)
vₜ = β₂vₜ₋₁ + (1-β₂)gₜ²         (second moment, RMSprop)
m̂ₜ = mₜ/(1-β₁ᵗ)                  (bias correction)
v̂ₜ = vₜ/(1-β₂ᵗ)                  (bias correction)
wₜ = wₜ₋₁ - α * m̂ₜ/(√v̂ₜ + ε)

Defaults: α=0.001, β₁=0.9, β₂=0.999, ε=1e-8
```

### Weight Initialization

| Init | Công thức | Dùng với |
|------|-----------|---------|
| Random Normal | N(0, 0.01) | Kém, chỉ dùng tham khảo |
| Xavier/Glorot | N(0, 2/(fan_in+fan_out)) | Sigmoid, Tanh |
| He/Kaiming | N(0, 2/fan_in) | ReLU, Leaky ReLU |

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: MLP từ scratch với NumPy (2-layer)
# ============================================================
import numpy as np

def sigmoid(z):
    return 1 / (1 + np.exp(-np.clip(z, -500, 500)))

def relu(z):
    return np.maximum(0, z)

def relu_backward(dA, Z):
    return dA * (Z > 0)

class MLPScratch:
    """2-layer MLP: Input → Hidden (ReLU) → Output (Sigmoid)"""

    def __init__(self, n_input, n_hidden, n_output, lr=0.01):
        self.lr = lr
        # He initialization cho ReLU
        self.W1 = np.random.randn(n_hidden, n_input) * np.sqrt(2/n_input)
        self.b1 = np.zeros((n_hidden, 1))
        # Xavier initialization cho Sigmoid
        self.W2 = np.random.randn(n_output, n_hidden) * np.sqrt(1/n_hidden)
        self.b2 = np.zeros((n_output, 1))

    def forward(self, X):
        # X shape: (n_features, m_samples)
        self.Z1 = self.W1 @ X + self.b1          # (n_hidden, m)
        self.A1 = relu(self.Z1)                   # (n_hidden, m)
        self.Z2 = self.W2 @ self.A1 + self.b2     # (n_output, m)
        self.A2 = sigmoid(self.Z2)                # (n_output, m)
        return self.A2

    def backward(self, X, y):
        m = X.shape[1]

        # Output layer gradient (Binary Cross-Entropy + Sigmoid)
        dZ2 = self.A2 - y                         # (n_output, m)
        dW2 = (1/m) * dZ2 @ self.A1.T             # (n_output, n_hidden)
        db2 = (1/m) * np.sum(dZ2, axis=1, keepdims=True)

        # Hidden layer gradient (chain rule)
        dA1 = self.W2.T @ dZ2                     # (n_hidden, m)
        dZ1 = relu_backward(dA1, self.Z1)         # element-wise
        dW1 = (1/m) * dZ1 @ X.T                   # (n_hidden, n_input)
        db1 = (1/m) * np.sum(dZ1, axis=1, keepdims=True)

        # Gradient descent update
        self.W1 -= self.lr * dW1
        self.b1 -= self.lr * db1
        self.W2 -= self.lr * dW2
        self.b2 -= self.lr * db2

    def compute_loss(self, y, y_hat):
        m = y.shape[1]
        eps = 1e-8
        return -(1/m) * np.sum(y * np.log(y_hat + eps) + (1-y) * np.log(1-y_hat + eps))

    def fit(self, X, y, n_epochs=1000):
        X_T = X.T  # (n_features, m)
        y_T = y.reshape(1, -1)  # (1, m)
        losses = []
        for epoch in range(n_epochs):
            y_hat = self.forward(X_T)
            loss = self.compute_loss(y_T, y_hat)
            self.backward(X_T, y_T)
            if epoch % 100 == 0:
                losses.append(loss)
        return losses

    def predict(self, X, threshold=0.5):
        y_hat = self.forward(X.T)
        return (y_hat >= threshold).astype(int).flatten()

# ============================================================
# PHẦN 2: PyTorch MLP
# ============================================================
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import TensorDataset, DataLoader
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

data = load_breast_cancer()
X, y = data.data, data.target.reshape(-1, 1).astype(np.float32)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
scaler = StandardScaler()
X_train_s = scaler.fit_transform(X_train).astype(np.float32)
X_test_s  = scaler.transform(X_test).astype(np.float32)

# PyTorch Dataset
train_ds = TensorDataset(torch.tensor(X_train_s), torch.tensor(y_train))
train_dl = DataLoader(train_ds, batch_size=32, shuffle=True)

class MLP(nn.Module):
    def __init__(self, n_input, hidden_sizes, n_output):
        super().__init__()
        layers = []
        prev_size = n_input
        for h in hidden_sizes:
            layers.append(nn.Linear(prev_size, h))
            layers.append(nn.BatchNorm1d(h))   # BatchNorm giúp ổn định training
            layers.append(nn.ReLU())
            layers.append(nn.Dropout(0.3))     # Dropout để regularize
            prev_size = h
        layers.append(nn.Linear(prev_size, n_output))
        layers.append(nn.Sigmoid())
        self.network = nn.Sequential(*layers)

    def forward(self, x):
        return self.network(x)

model = MLP(n_input=30, hidden_sizes=[64, 32], n_output=1)
criterion = nn.BCELoss()
optimizer = optim.Adam(model.parameters(), lr=0.001, weight_decay=1e-4)

# Training loop
for epoch in range(100):
    model.train()
    for X_batch, y_batch in train_dl:
        optimizer.zero_grad()       # Reset gradients
        y_pred = model(X_batch)
        loss = criterion(y_pred, y_batch)
        loss.backward()             # Backpropagation
        # Gradient clipping để tránh exploding gradient
        torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
        optimizer.step()            # Update weights

    if (epoch+1) % 20 == 0:
        model.eval()
        with torch.no_grad():
            y_test_pred = model(torch.tensor(X_test_s))
            acc = ((y_test_pred >= 0.5).float() == torch.tensor(y_test)).float().mean()
            print(f"Epoch {epoch+1}: Test Accuracy = {acc:.4f}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Dữ liệu phức tạp với quan hệ phi tuyến cao (image, audio, text).
- Dataset lớn (nhiều data → deep learning học được nhiều hơn).
- Có GPU để training.
- Không cần diễn giải model.
- Transfer learning: tận dụng pretrained weights.

**Không dùng khi:**
- Dataset nhỏ (< vài nghìn samples) → overfitting, dùng traditional ML.
- Cần model diễn giải được (medical, finance, legal).
- Không có đủ compute resources.
- Tabular data với features được feature-engineered → XGBoost thường tốt hơn.
- Cần training nhanh và deploy đơn giản.

---

## So sánh Optimizers

| Optimizer | Tốc độ | Stability | Memory | Phù hợp |
|-----------|--------|-----------|--------|---------|
| SGD | Chậm | Oscillate | Thấp | Với momentum + LR schedule |
| SGD + Momentum | Nhanh hơn | Tốt | Trung bình | Image classification |
| RMSprop | Khá | Tốt | Trung bình | RNN |
| Adam | Nhanh | Tốt | Cao | Mặc định cho hầu hết |
| AdamW | Nhanh | Tốt | Cao | NLP, weight decay tốt hơn |

---

## Lỗi thường gặp (Common Pitfalls)

- **Quên `optimizer.zero_grad()`**: gradients tích lũy → update sai.
- **Không dùng BatchNorm/Dropout**: deep networks bị exploding/vanishing gradient và overfitting.
- **Learning rate sai**: quá lớn → diverge; quá nhỏ → không học. Dùng LR finder hoặc LR scheduler.
- **Dying ReLU**: tất cả neurons ra 0 → không học nữa. Dùng Leaky ReLU hoặc He init.
- **Không normalize input**: features có scale khác nhau → gradient descent bất ổn.
- **Quên `model.eval()` khi inference**: BatchNorm và Dropout vẫn active nếu không switch mode.
- **NaN loss**: exploding gradient → dùng gradient clipping, kiểm tra learning rate.
- **Overfitting nhanh**: thêm Dropout, L2 regularization (`weight_decay`), data augmentation.

---

## Câu hỏi phỏng vấn hay gặp

- **Backpropagation hoạt động thế nào? Explain chain rule trong context này.**
- **Vanishing gradient là gì? Tại sao ReLU giải quyết được?**
- **Tại sao cần non-linear activation functions?** (không có → toàn mạng là linear)
- **Batch Normalization hoạt động thế nào? Tại sao giúp training?**
- **Adam optimizer kết hợp gì? β₁, β₂, ε là gì?**
- **Dropout là gì? Tại sao nó regularize model?**
- **Tại sao dùng He initialization với ReLU?**
- **Universal Approximation Theorem nói gì?**
- **SGD vs Batch GD vs Mini-batch GD: trade-offs?**
- **Exploding gradient: cách detect và fix?**
