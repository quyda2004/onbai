# Logistic Regression

---

## Giải thích cho người mới hoàn toàn

Bạn là bác sĩ, cần dự đoán một bệnh nhân có bị tiểu đường hay không, dựa vào chỉ số đường huyết. Đây không phải dự đoán một con số (như giá nhà), mà là dự đoán **có/không**.

Nếu dùng đường thẳng (Linear Regression) để dự đoán, đôi khi nó sẽ cho ra kết quả như 1.5 hoặc -0.3, vô nghĩa với bài toán có/không. Ta cần kết quả nằm trong khoảng [0, 1] — hiểu là **xác suất**.

**Logistic Regression** giải quyết điều này bằng cách "uốn cong" đường thẳng thành một chữ S (sigmoid curve): mọi đầu vào cho ra xác suất từ 0 đến 1. Nếu xác suất > 0.5, dự đoán là "có bệnh"; ngược lại là "không".

---

## Giải thích cho người đã biết lập trình (nâng cao)

Logistic Regression là **Generalized Linear Model (GLM)** cho biến phụ thuộc nhị phân. Nó mô hình hóa **log-odds** (logit) như là hàm tuyến tính của features.

**Cơ chế toán học:**
- Output của Linear Regression `z = w^T x + b` (gọi là log-odds hoặc logit) được ánh xạ qua sigmoid: `P(y=1|x) = σ(z)`.
- Sigmoid đảm bảo output ∈ (0, 1) và là hàm monotone increasing — giúp decision boundary vẫn là hyperplane trong feature space.
- Model tối ưu hóa **Maximum Likelihood Estimation (MLE)** — tương đương tối thiểu Binary Cross-Entropy Loss.

**Tại sao không dùng MSE cho classification:**
- MSE với sigmoid tạo ra loss surface **non-convex** với nhiều local minima → Gradient Descent không đảm bảo tìm global minimum.
- Cross-entropy loss: loss surface **convex** → hội tụ đảm bảo.
- MSE phạt sai số bình phương, không phản ánh tốt xác suất — ví dụ predict 0.01 vs 0.49 khi truth là 0 thì MSE khác biệt ít nhưng semantically rất khác.

**Gradient Descent với Logistic Regression:**
- Gradient của Cross-Entropy cũng có dạng `(ŷ - y)x`, tương tự Linear Regression — gọn và hiệu quả.

**Edge cases:**
- **Perfect separation**: nếu classes linearly separable hoàn toàn, weights tiến tới ±∞ → cần regularization (luôn dùng).
- **Class imbalance**: threshold 0.5 không còn optimal → điều chỉnh threshold, dùng class_weight, hoặc xem PR-AUC.
- **Multicollinearity**: tương tự Linear Regression, ảnh hưởng đến stability của weights.

---

## Định nghĩa chính xác

**Logistic Regression** là thuật toán phân loại (classification) tuyến tính, mô hình hóa xác suất có điều kiện `P(y=1|x)` bằng hàm sigmoid áp dụng lên tổ hợp tuyến tính của features. Dù tên có "Regression", đây là thuật toán **classification**.

---

## Công thức / Bảng kỹ thuật

### Sigmoid Function
```
σ(z) = 1 / (1 + e^(-z))
σ'(z) = σ(z)(1 - σ(z))    ← đạo hàm tiện lợi
```
Properties: σ(0) = 0.5, σ(+∞) = 1, σ(-∞) = 0

### Hypothesis Function
```
z = w^T x + b              (log-odds / logit)
ŷ = P(y=1|x) = σ(z)       (xác suất)

Log-odds: log(P/(1-P)) = w^T x + b
```

### Decision Boundary
```
Predict class 1 nếu: σ(w^T x + b) ≥ threshold (thường = 0.5)
Tương đương: w^T x + b ≥ 0
```

### Binary Cross-Entropy Loss
```
J(w, b) = -(1/m) Σᵢ [yᵢ log(ŷᵢ) + (1-yᵢ) log(1-ŷᵢ)]
```

### Gradient
```
∂J/∂w = (1/m) X^T (ŷ - y)    ← cùng dạng với Linear Regression!
∂J/∂b = (1/m) Σᵢ (ŷᵢ - yᵢ)
```

### Categorical Cross-Entropy (Multiclass)
```
J = -(1/m) Σᵢ Σₖ yᵢₖ log(ŷᵢₖ)
```

### Softmax (Multiclass Logistic Regression)
```
P(y=k|x) = e^(wₖ^T x) / Σⱼ e^(wⱼ^T x)
```

### Regularization trong sklearn
Parameter `C = 1/λ` (ngược với convention thông thường):

| C nhỏ | Regularization mạnh | Weights nhỏ → underfitting |
|-------|--------------------|-----------------------------|
| C lớn | Regularization yếu | Weights tự do → overfitting |

### Multiclass Strategies

| Strategy | Mô tả | Số models | Vấn đề |
|----------|-------|-----------|--------|
| One-vs-Rest (OvR) | K models, mỗi class vs rest | K | Imbalanced sub-problems |
| One-vs-One (OvO) | K(K-1)/2 pairs | K(K-1)/2 | Tốn nhiều models |
| Softmax (Multinomial) | 1 model, K outputs | 1 | Cần nhiều data hơn |

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: Sigmoid và Binary Cross-Entropy từ scratch
# ============================================================
import numpy as np
import matplotlib.pyplot as plt
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import (classification_report, confusion_matrix,
                              roc_curve, auc, RocCurveDisplay)

def sigmoid(z):
    """Ổn định số: clip để tránh overflow"""
    z = np.clip(z, -500, 500)
    return 1.0 / (1.0 + np.exp(-z))

class LogisticRegressionScratch:
    def __init__(self, lr=0.1, n_iter=1000, C=1.0):
        self.lr = lr
        self.n_iter = n_iter
        self.C = C  # Regularization strength (như sklearn)
        self.w = None
        self.b = None

    def fit(self, X, y):
        m, n = X.shape
        self.w = np.zeros(n)
        self.b = 0.0

        for _ in range(self.n_iter):
            # Forward pass
            z = X @ self.w + self.b
            y_pred = sigmoid(z)

            # Gradient của cross-entropy loss
            error = y_pred - y
            dw = (1/m) * X.T @ error + (1/self.C) * self.w  # L2 regularization
            db = (1/m) * np.sum(error)

            self.w -= self.lr * dw
            self.b -= self.lr * db

        return self

    def predict_proba(self, X):
        return sigmoid(X @ self.w + self.b)

    def predict(self, X, threshold=0.5):
        return (self.predict_proba(X) >= threshold).astype(int)

# ============================================================
# PHẦN 2: sklearn + ROC Curve
# ============================================================
data = load_breast_cancer()
X, y = data.data, data.target

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y  # stratify để giữ tỉ lệ class
)

# Feature scaling bắt buộc
scaler = StandardScaler()
X_train_s = scaler.fit_transform(X_train)
X_test_s  = scaler.transform(X_test)

# sklearn LogisticRegression
# solver='lbfgs' cho nhỏ, 'saga' cho lớn/sparse
# max_iter cần tăng nếu gặp ConvergenceWarning
model = LogisticRegression(C=1.0, solver='lbfgs', max_iter=1000, random_state=42)
model.fit(X_train_s, y_train)

y_pred = model.predict(X_test_s)
y_proba = model.predict_proba(X_test_s)[:, 1]  # Xác suất class positive

print(classification_report(y_test, y_pred, target_names=data.target_names))

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)
print(f"Confusion Matrix:\n{cm}")
# [[TN  FP]
#  [FN  TP]]

# ROC Curve
fpr, tpr, thresholds = roc_curve(y_test, y_proba)
roc_auc = auc(fpr, tpr)
print(f"ROC-AUC: {roc_auc:.4f}")

# ============================================================
# PHẦN 3: Multiclass Logistic Regression
# ============================================================
from sklearn.datasets import load_iris

iris = load_iris()
X_iris, y_iris = iris.data, iris.target

X_tr, X_te, y_tr, y_te = train_test_split(X_iris, y_iris, test_size=0.2, random_state=42)
sc = StandardScaler()
X_tr_s = sc.fit_transform(X_tr)
X_te_s  = sc.transform(X_te)

# multi_class='multinomial' dùng Softmax thay vì OvR
clf = LogisticRegression(
    multi_class='multinomial',
    solver='lbfgs',
    C=1.0,
    max_iter=1000
)
clf.fit(X_tr_s, y_tr)
print(f"Multiclass Accuracy: {clf.score(X_te_s, y_te):.4f}")

# So sánh C parameter (regularization strength)
for c_val in [0.01, 0.1, 1.0, 10.0, 100.0]:
    m = LogisticRegression(C=c_val, max_iter=1000)
    m.fit(X_train_s, y_train)
    acc = m.score(X_test_s, y_test)
    print(f"C={c_val:6.2f} → Accuracy={acc:.4f}, |w|={np.linalg.norm(m.coef_):.3f}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Bài toán binary hoặc multiclass classification.
- Cần **xác suất** đầu ra, không chỉ nhãn (output calibration tốt).
- Cần model **diễn giải được**: weights phản ánh tác động của từng feature đến log-odds.
- Dataset linearly separable hoặc gần linearly separable.
- Làm **baseline** trước khi thử model phức tạp hơn.
- Phân loại spam, chẩn đoán y tế, credit scoring.

**Không dùng khi:**
- Ranh giới quyết định phi tuyến phức tạp → dùng SVM với kernel, Random Forest, hoặc Neural Network.
- Dataset rất lớn với features sparse → Naive Bayes hoặc Linear SVM thường nhanh hơn.
- Quan hệ giữa features và target rất phức tạp → Deep Learning.
- Class imbalance nặng mà không điều chỉnh threshold hoặc class_weight.

---

## So sánh với các thuật toán liên quan

| Tiêu chí | Logistic Regression | Linear SVM | Naive Bayes | Decision Tree |
|----------|--------------------|-----------:|-------------|---------------|
| Output là xác suất | Có (calibrated) | Không trực tiếp | Có (nhưng kém calibrated) | Không trực tiếp |
| Decision boundary | Linear | Linear | Linear (GNB) | Non-linear |
| Diễn giải | Tốt | Trung bình | Tốt | Rất tốt |
| Training speed | Nhanh | Nhanh | Rất nhanh | Nhanh |
| Feature independence assumption | Không | Không | Có | Không |
| Regularization built-in | C parameter | C parameter | Laplace smoothing | max_depth... |
| Large feature space | Tốt | Tốt (hinge loss) | Tốt (Bernoulli) | Kém |

---

## Lỗi thường gặp (Common Pitfalls)

- **Quên feature scaling**: Logistic Regression cũng dùng Gradient Descent (hoặc LBFGS) → scaling quan trọng.
- **ConvergenceWarning**: tăng `max_iter` hoặc scale features.
- **Dùng accuracy với imbalanced data**: 95% accuracy khi 95% là class 0 không có nghĩa gì → dùng F1, PR-AUC.
- **Perfect separation**: model diverges (weights → ∞) mà không có regularization → luôn dùng `C` hợp lý.
- **Nhầm C với λ**: trong sklearn, `C = 1/λ`, C nhỏ → regularization mạnh (ngược lại với thông thường).
- **Multiclass với multi_class='auto'**: sklearn tự chọn strategy, nên explicit chỉ định `'multinomial'` cho Softmax.
- **Threshold cứng 0.5**: với imbalanced dataset, tối ưu threshold bằng F1 hoặc theo business requirement.

---

## Câu hỏi phỏng vấn hay gặp

- **Tại sao Logistic Regression không dùng MSE loss?** (non-convex, gradient vanishing)
- **Sigmoid function có những properties quan trọng nào?**
- **Sự khác biệt giữa OvR và Softmax cho multiclass?**
- **Tại sao cần regularization trong Logistic Regression?** (tránh perfect separation, overfitting)
- **ROC-AUC là gì? Khi nào dùng PR-AUC thay vì ROC-AUC?** (imbalanced classes)
- **Log-odds là gì? Tại sao model tuyến tính trên log-odds?**
- **Khi nào Logistic Regression tốt hơn Decision Tree và ngược lại?**
- **Giải thích decision boundary của Logistic Regression.**
- **Class imbalance ảnh hưởng Logistic Regression thế nào? Cách xử lý?**
- **So sánh Logistic Regression với Linear SVM.**
