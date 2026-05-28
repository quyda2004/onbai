# Tổng quan ML — Machine Learning

---

## Roadmap học Machine Learning

### Bước 1 — Nền tảng toán
- **Linear Algebra** — vector, matrix, dot product, eigenvalue
- **Calculus** — gradient, partial derivative, chain rule
- **Probability & Statistics** — distribution, Bayes theorem, MLE
- **Optimization** — Gradient Descent, SGD, Adam

### Bước 2 — Classical ML
- **Supervised Learning** — Regression (Linear, Logistic), Classification (SVM, KNN, Decision Tree, Random Forest)
- **Unsupervised Learning** — Clustering (K-Means, DBSCAN), Dimensionality Reduction (PCA)
- **Evaluation** — Accuracy, Precision, Recall, F1, ROC-AUC, RMSE

### Bước 3 — Neural Networks
- **Perceptron** → MLP (Multi-Layer Perceptron)
- **Backpropagation** — chain rule, weight update
- **Activation functions** — Sigmoid, ReLU, Tanh, Softmax
- **Loss functions** — MSE, Cross-Entropy, Hinge Loss

### Bước 4 — Deep Learning
- **CNN** — convolution, pooling, cho image
- **RNN/LSTM/GRU** — sequence, time series
- **Transformer / Attention** — self-attention, positional encoding
- **Transfer Learning** — fine-tuning pretrained models

### Bước 5 — MLOps
- **Train/Val/Test split** — data leakage, stratification
- **Regularization** — L1/L2, Dropout, Early stopping
- **Hyperparameter tuning** — Grid search, Random search, Bayesian
- **Feature Engineering** — scaling, encoding, selection

---

## Supervised vs Unsupervised vs Reinforcement

| | Supervised | Unsupervised | Reinforcement |
|-|-----------|-------------|---------------|
| Có label? | Có | Không | Reward signal |
| Mục tiêu | Predict output | Tìm structure | Maximize reward |
| Ví dụ | Spam detection | Customer segmentation | Game AI |
| Algorithm | Linear Reg, SVM | K-Means, PCA | Q-Learning, PPO |

---

## Bias-Variance Tradeoff

```
Error = Bias² + Variance + Irreducible Noise

High Bias   → Underfitting → model quá đơn giản
High Variance → Overfitting → model quá phức tạp

Cách fix:
Underfitting: tăng model complexity, thêm features, giảm regularization
Overfitting:  thêm data, tăng regularization, dropout, early stopping
```

---

## Bảng Evaluation Metrics

| Metric | Công thức | Khi dùng |
|--------|-----------|---------|
| Accuracy | (TP+TN)/(Total) | Balanced dataset |
| Precision | TP/(TP+FP) | Quan trọng khi FP costly (spam filter) |
| Recall | TP/(TP+FN) | Quan trọng khi FN costly (cancer detection) |
| F1-Score | 2·P·R/(P+R) | Imbalanced dataset |
| ROC-AUC | Area under ROC curve | So sánh models |
| RMSE | √(Σ(y-ŷ)²/n) | Regression |
| MAE | Σ\|y-ŷ\|/n | Regression (robust với outlier) |

---

## Mã giả — Hỏi output là gì?

### Bài 1 — Gradient Descent (trace từng bước)

```python
# y = x² + 2 → dy/dx = 2x
# Gradient Descent tìm minimum

x = 10.0       # khởi tạo
lr = 0.1       # learning rate

for i in range(4):
    grad = 2 * x
    x = x - lr * grad
    print(f"Step {i+1}: x={x:.4f}, y={x**2+2:.4f}")
```

> **Output là gì?**
> ```
> Step 1: x=8.0000, y=66.0000
> Step 2: x=6.4000, y=42.9600
> Step 3: x=5.1200, y=28.2934
> Step 4: x=4.0960, y=18.7779
> ```
> **Giải thích:** Mỗi step: grad=2x, x mới = x - 0.1·(2x) = x(1-0.2) = 0.8x. x hội tụ về 0 (minimum) nhưng chậm vì lr nhỏ.

---

### Bài 2 — Confusion Matrix

```
Kết quả classifier trên 100 mẫu:
- 45 Positive thật, model đoán đúng 40 (TP=40)
- 45 Positive thật, model đoán sai thành Negative (FN=5)
- 55 Negative thật, model đoán đúng 45 (TN=45)
- 55 Negative thật, model đoán sai thành Positive (FP=10)

Hỏi: Tính Accuracy, Precision, Recall, F1?
```

> **Đáp án:**
> - Accuracy = (40+45)/100 = **85%**
> - Precision = 40/(40+10) = **80%**
> - Recall = 40/(40+5) = **88.9%**
> - F1 = 2·(0.8·0.889)/(0.8+0.889) = **84.2%**
>
> **Giải thích:** Recall cao → model ít bỏ sót Positive. Precision thấp hơn → có nhiều false alarm. Phù hợp với cancer detection (FN costly hơn FP).

---

### Bài 3 — K-Means Clustering (trace)

```
Points: A(1,1), B(2,1), C(5,4), D(6,5), E(1.5,1.5)
K=2, khởi tạo centroids: C1=(1,1), C2=(6,5)

Iteration 1:
- Assign mỗi point vào centroid gần nhất (Euclidean distance)
- A(1,1): dist(C1)=0, dist(C2)=√50 → Cluster 1
- B(2,1): dist(C1)=1, dist(C2)=√41 → Cluster 1
- C(5,4): dist(C1)=√25=5, dist(C2)=√2 → Cluster 2
- D(6,5): dist(C1)=√50, dist(C2)=0 → Cluster 2
- E(1.5,1.5): dist(C1)=√0.5, dist(C2)=≈6.7 → Cluster 1

Update centroids:
C1 = mean(A,B,E) = ((1+2+1.5)/3, (1+1+1.5)/3) = ?
C2 = mean(C,D) = ((5+6)/2, (4+5)/2) = ?

Hỏi: Centroid mới sau iteration 1?
```

> **Đáp án:**
> - C1 = (4.5/3, 3.5/3) = **(1.5, 1.17)**
> - C2 = (11/2, 9/2) = **(5.5, 4.5)**
>
> **Giải thích:** K-Means lặp assign → update cho đến khi centroids không thay đổi (converge).

---

### Bài 4 — Sigmoid & Backprop

```python
import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

def sigmoid_derivative(x):
    s = sigmoid(x)
    return s * (1 - s)

# Forward pass
x = 2.0
y_true = 1.0
y_pred = sigmoid(x)
loss = -y_true * math.log(y_pred) - (1-y_true) * math.log(1-y_pred)

print(f"y_pred = {y_pred:.4f}")
print(f"loss = {loss:.4f}")
print(f"sigmoid'(x) = {sigmoid_derivative(x):.4f}")
```

> **Output là gì?**
> ```
> y_pred = 0.8808
> loss = 0.1269
> sigmoid'(x) = 0.1050
> ```
> **Giải thích:** sigmoid(2) ≈ 0.88. Cross-entropy loss với y_true=1: -log(0.88) ≈ 0.127. Gradient sigmoid'(2) = 0.88 × (1-0.88) = 0.105 — nhỏ → vanishing gradient problem với sigmoid.

---

### Bài 5 — Overfitting vs Underfitting (nhận dạng)

```
Model A: Train accuracy=99%, Val accuracy=60%
Model B: Train accuracy=70%, Val accuracy=68%
Model C: Train accuracy=90%, Val accuracy=88%

Hỏi: Model nào overfitting, underfitting, good fit?
```

> **Đáp án:**
> - Model A: **Overfitting** — khoảng cách train/val lớn (99% vs 60%)
> - Model B: **Underfitting** — cả train accuracy cũng thấp (70%)
> - Model C: **Good fit** — train cao, val gần với train, khoảng cách nhỏ

---

## Bảng Algorithm so sánh nhanh

| Algorithm | Type | Params quan trọng | Khi dùng |
|-----------|------|--------------------|---------|
| Linear Regression | Supervised/Reg | Learning rate | Relationship tuyến tính |
| Logistic Regression | Supervised/Clf | Threshold | Binary classification |
| Decision Tree | Supervised/Clf | max_depth | Interpretable model |
| Random Forest | Supervised/Clf | n_estimators | Robust, ít tune |
| SVM | Supervised/Clf | C, kernel | High-dim, small data |
| KNN | Supervised/Clf | K | Simple baseline |
| K-Means | Unsupervised | K | Clustering |
| Neural Network | Both | layers, lr | Complex patterns |

---

## Câu hỏi tự test nhanh

1. Precision vs Recall — khi nào ưu tiên cái nào? → Recall khi FN costly (bệnh), Precision khi FP costly (spam)
2. Tại sao cần validation set ngoài test set? → Tune hyperparameter trên val, test chỉ đánh giá cuối
3. L1 vs L2 regularization khác nhau gì? → L1 tạo sparse model (feature selection), L2 shrink weights đều
4. Tại sao normalize data trước khi train? → Gradient descent hội tụ nhanh hơn, tránh feature dominance
5. Tại sao ReLU phổ biến hơn Sigmoid? → Không có vanishing gradient vùng dương, tính toán nhanh
