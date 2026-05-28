# SVM & KNN

---

## Giải thích cho người mới hoàn toàn

### SVM (Support Vector Machine)

Tưởng tượng bạn có hai nhóm điểm: nhóm đỏ và nhóm xanh trên mặt giấy. Bạn cần vẽ một đường thẳng để phân chia chúng. Có vô số đường thẳng có thể phân chia được — nhưng đường nào "an toàn nhất"?

SVM chọn đường thẳng có **khoảng cách xa nhất** đến các điểm gần nhất của mỗi nhóm. Giống như kẻ một con đường hai làn giữa hai đám cây — bạn muốn con đường càng rộng càng tốt để xe không vô tình đâm vào cây.

Các điểm nằm ở rìa (gần đường nhất) được gọi là **support vectors** — chúng "đỡ" cho hyperplane.

### KNN (K-Nearest Neighbors)

Hãy nghĩ đến câu tục ngữ "Gần mực thì đen, gần đèn thì rạng". Khi cần phân loại một điểm dữ liệu mới, KNN tìm K điểm gần nhất trong tập huấn luyện, rồi cho "bầu phiếu": class nào được K hàng xóm đó bầu nhiều nhất thì thắng.

Nếu K=1: nhìn vào 1 người hàng xóm gần nhất.
Nếu K=5: nhìn vào 5 người hàng xóm và bầu chọn đa số.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### SVM — Cơ chế sâu

**Hard Margin SVM** (linearly separable):
- Tìm hyperplane `w^T x + b = 0` sao cho margin `2/||w||` là lớn nhất.
- Tương đương minimize `||w||²/2` subject to `yᵢ(w^T xᵢ + b) ≥ 1`.
- Đây là **convex quadratic programming** problem → global optimum.

**Soft Margin SVM** (không linearly separable hoàn toàn):
- Cho phép một số điểm vi phạm margin với slack variable `ξᵢ ≥ 0`.
- Minimize `||w||²/2 + C * Σᵢ ξᵢ`.
- `C` lớn: phạt vi phạm nặng → margin hẹp hơn, fit training data hơn.
- `C` nhỏ: chấp nhận nhiều vi phạm → margin rộng hơn, generalize tốt hơn.

**Kernel Trick**:
- Nhiều dữ liệu không linearly separable trong không gian gốc nhưng **linearly separable trong không gian chiều cao hơn**.
- Thay vì tính `φ(xᵢ)^T φ(xⱼ)` (tốn kém), dùng **kernel function** `K(xᵢ, xⱼ) = φ(xᵢ)^T φ(xⱼ)`.
- Computation chỉ phụ thuộc vào dot products → **kernel trick** tránh tính `φ(x)` explicit.

**Dual formulation**: SVM có thể giải bằng Lagrangian duality → prediction chỉ cần inner products với support vectors.

### KNN — Cơ chế sâu

**Lazy learning**: KNN không "train" theo nghĩa truyền thống — toàn bộ training data được lưu và dùng tại inference time.

**Curse of Dimensionality**:
- Trong không gian cao chiều, tất cả các điểm đều "xa nhau tương đương" → khoảng cách Euclidean không còn phân biệt tốt.
- Volume của hypersphere giảm relative to hypercube khi d tăng → K hàng xóm "gần nhất" thực ra rất xa.
- Rule of thumb: với KNN, n_features nên ≤ log(n_samples).

**K selection**:
- K nhỏ (K=1): high variance, low bias → overfit.
- K lớn: low variance, high bias → underfit.
- Dùng **cross-validation** hoặc **elbow method** (plot error vs K).

---

## Định nghĩa chính xác

**SVM**: thuật toán học có giám sát tìm hyperplane tối ưu phân chia các classes với maximum margin, sử dụng kernel functions để xử lý non-linear separation.

**KNN**: thuật toán instance-based learning (lazy learning) phân loại/dự đoán dựa trên K điểm gần nhất trong training set theo một distance metric cho trước.

---

## Công thức / Bảng kỹ thuật

### SVM — Hard Margin
```
Primal: minimize ½||w||²
        s.t.    yᵢ(w^T xᵢ + b) ≥ 1  ∀i

Margin = 2 / ||w||
Support vectors: yᵢ(w^T xᵢ + b) = 1
```

### SVM — Soft Margin (C-SVM)
```
Primal: minimize ½||w||² + C * Σᵢ ξᵢ
        s.t.    yᵢ(w^T xᵢ + b) ≥ 1 - ξᵢ
                ξᵢ ≥ 0

C lớn → ít vi phạm, margin hẹp → dễ overfit
C nhỏ → nhiều vi phạm, margin rộng → regularized hơn
```

### Kernel Functions

| Kernel | Công thức K(xᵢ, xⱼ) | Hyperparameter | Dùng khi |
|--------|---------------------|----------------|---------|
| Linear | xᵢ^T xⱼ | Không có | Data linearly separable; n_features lớn |
| RBF (Gaussian) | exp(-γ\|xᵢ-xⱼ\|²) | γ (gamma) | Mặc định; non-linear; không biết cấu trúc |
| Polynomial | (γ xᵢ^T xⱼ + r)^d | d, γ, r | Quan hệ polynomial rõ ràng |
| Sigmoid | tanh(γ xᵢ^T xⱼ + r) | γ, r | Ít dùng; đôi khi cho NLP |

### RBF Kernel — γ parameter
```
K(xᵢ, xⱼ) = exp(-γ||xᵢ - xⱼ||²)

γ nhỏ → mỗi điểm ảnh hưởng rộng → smoother boundary → underfit
γ lớn → mỗi điểm ảnh hưởng hẹp → complex boundary → overfit
```

### KNN — Distance Metrics

| Metric | Công thức | Dùng khi |
|--------|-----------|---------|
| Euclidean (L2) | √(Σ(xᵢ-yᵢ)²) | Không gian liên tục, đồng nhất |
| Manhattan (L1) | Σ\|xᵢ-yᵢ\| | High-dimensional, sparse data |
| Cosine similarity | (x·y)/(||x||||y||) | Text/NLP, độ tương tự hướng |
| Minkowski | (Σ\|xᵢ-yᵢ\|^p)^(1/p) | Tổng quát (p=1:Manhattan, p=2:Euclidean) |
| Hamming | tỉ lệ features khác nhau | Categorical data |

### Complexity

| | SVM (train) | SVM (predict) | KNN (train) | KNN (predict) |
|--|------------|---------------|-------------|----------------|
| Time | O(n²·m) ~ O(n³·m) | O(n_sv · n) | O(1) | O(m · n) |
| Space | O(n_sv) | O(n_sv) | O(m · n) | O(m · n) |

Với m = n_samples, n = n_features, n_sv = n_support_vectors.

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: SVM với RBF kernel
# ============================================================
import numpy as np
import matplotlib.pyplot as plt
from sklearn.svm import SVC, SVR
from sklearn.neighbors import KNeighborsClassifier
from sklearn.datasets import load_breast_cancer, make_moons
from sklearn.model_selection import train_test_split, cross_val_score, GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import classification_report
from sklearn.pipeline import Pipeline

# Dataset
data = load_breast_cancer()
X, y = data.data, data.target

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# Feature scaling BẮT BUỘC cho SVM
scaler = StandardScaler()
X_train_s = scaler.fit_transform(X_train)
X_test_s  = scaler.transform(X_test)

# SVM với RBF kernel
svm = SVC(kernel='rbf', C=1.0, gamma='scale', probability=True, random_state=42)
svm.fit(X_train_s, y_train)

print(f"SVM RBF — Test Accuracy: {svm.score(X_test_s, y_test):.4f}")
print(f"Số Support Vectors: {svm.n_support_}")  # Per class
print(classification_report(y_test, svm.predict(X_test_s)))

# GridSearch cho C và gamma
param_grid = {
    'C':     [0.1, 1, 10, 100],
    'gamma': ['scale', 'auto', 0.01, 0.1],
}
grid = GridSearchCV(SVC(kernel='rbf', random_state=42),
                    param_grid, cv=5, scoring='accuracy', n_jobs=-1)
grid.fit(X_train_s, y_train)
print(f"\nBest params: {grid.best_params_}")
print(f"Best CV Acc: {grid.best_score_:.4f}")

# So sánh kernels
for kernel in ['linear', 'rbf', 'poly']:
    pipe = Pipeline([
        ('scaler', StandardScaler()),
        ('svm', SVC(kernel=kernel, random_state=42))
    ])
    scores = cross_val_score(pipe, X, y, cv=5, scoring='accuracy')
    print(f"Kernel {kernel:8s}: {scores.mean():.4f} ± {scores.std():.4f}")

# ============================================================
# PHẦN 2: KNN với cross-validation để chọn K
# ============================================================

# Feature scaling BẮT BUỘC cho KNN
# (KNN nhạy với scale vì dùng distance)

k_values = range(1, 31)
cv_scores = []

for k in k_values:
    knn = KNeighborsClassifier(n_neighbors=k, metric='euclidean', n_jobs=-1)
    scores = cross_val_score(knn, X_train_s, y_train, cv=5, scoring='accuracy')
    cv_scores.append(scores.mean())

# Best K
best_k = k_values[np.argmax(cv_scores)]
print(f"\nBest K: {best_k}, CV Accuracy: {max(cv_scores):.4f}")

# Train với best K
knn_best = KNeighborsClassifier(n_neighbors=best_k, metric='euclidean', n_jobs=-1)
knn_best.fit(X_train_s, y_train)
print(f"KNN (K={best_k}) Test Accuracy: {knn_best.score(X_test_s, y_test):.4f}")

# ============================================================
# PHẦN 3: Minh họa SVM với non-linear data (make_moons)
# ============================================================
X_moon, y_moon = make_moons(n_samples=500, noise=0.2, random_state=42)
X_m_train, X_m_test, y_m_train, y_m_test = train_test_split(
    X_moon, y_moon, test_size=0.2, random_state=42
)

# Linear SVM: không tốt với dữ liệu non-linear
svm_linear = SVC(kernel='linear', C=1.0)
svm_linear.fit(X_m_train, y_m_train)
print(f"\nMoons - Linear SVM:  {svm_linear.score(X_m_test, y_m_test):.4f}")

# RBF SVM: tốt hơn với dữ liệu non-linear
svm_rbf = SVC(kernel='rbf', C=10.0, gamma=1.0)
svm_rbf.fit(X_m_train, y_m_train)
print(f"Moons - RBF SVM:     {svm_rbf.score(X_m_test, y_m_test):.4f}")

# KNN cũng xử lý non-linear tốt
knn_moon = KNeighborsClassifier(n_neighbors=5)
knn_moon.fit(X_m_train, y_m_train)
print(f"Moons - KNN (K=5):   {knn_moon.score(X_m_test, y_m_test):.4f}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**SVM — Dùng khi:**
- Dataset nhỏ đến trung bình (< 100,000 samples) với features nhiều chiều.
- High-dimensional data (text classification, genomics) → Linear SVM rất hiệu quả.
- Cần **maximum margin** classifier với lý thuyết mạnh.
- Dữ liệu có ranh giới phân chia rõ ràng (với slack).

**SVM — Không dùng khi:**
- Dataset rất lớn: training `O(n²~n³)` không khả thi.
- Cần xác suất calibrated tốt (SVM probability bằng Platt scaling, không tự nhiên).
- Nhiều classes (K-class): cần K*(K-1)/2 models với OvO.
- Cần interpret model.

**KNN — Dùng khi:**
- Dataset nhỏ (< 10,000 samples), cần triển khai nhanh.
- Không giả định về phân phối dữ liệu (non-parametric).
- Recommendation systems (item similarity).
- Anomaly detection (KNN distance-based).

**KNN — Không dùng khi:**
- Dataset lớn: prediction `O(m*n)` per query → quá chậm (dùng Approximate NN như FAISS).
- High-dimensional data: curse of dimensionality.
- Cần retrain thường xuyên (KNN không có training nhưng cần lưu toàn bộ data).
- Dữ liệu có irrelevant features nhiều.

---

## So sánh: SVM vs KNN vs Logistic Regression

| Tiêu chí | SVM | KNN | Logistic Regression |
|----------|-----|-----|---------------------|
| Training time | O(n²m) chậm | O(1) instant | O(mn) nhanh |
| Prediction time | O(n_sv * n) | O(mn) chậm | O(n) rất nhanh |
| Memory | O(n_sv) nhỏ | O(mn) lớn | O(n) nhỏ |
| Non-linear | Có (kernel) | Tự nhiên | Không |
| Kernel/distance tuning | Quan trọng | Quan trọng | Không cần |
| Feature scaling | BẮT BUỘC | BẮT BUỘC | Nên làm |
| Interpretability | Thấp | Thấp | Cao |
| Probabilistic output | Không tự nhiên | Có (vote ratio) | Có |
| Noise robustness | Tốt (soft margin) | Kém (K=1) | Khá |
| High-dimensional | Tốt (linear kernel) | Kém (curse of dim) | Tốt |

---

## Lỗi thường gặp (Common Pitfalls)

**SVM:**
- **Quên feature scaling**: SVM rất nhạy với scale, performance giảm mạnh nếu features có range khác nhau.
- **Không tune C và gamma**: dùng default thường không optimal — GridSearchCV là bắt buộc.
- **Dùng SVM cho dataset lớn**: training time `O(n²~n³)` không khả thi với triệu samples → dùng LinearSVC (`O(nm)`) hoặc SGDClassifier.
- **Nhầm C parameter**: C lớn → fit chặt hơn (ít regularization), ngược với Ridge/Lasso.
- **probability=True làm chậm training**: Platt scaling cần thêm cross-validation.

**KNN:**
- **Quên feature scaling**: features với range lớn sẽ dominate distance → luôn scale trước.
- **Dùng K=1**: quá sensitive với noise và outliers.
- **Không tune K**: chỉ dùng K=5 mặc định mà không cross-validate.
- **High-dimensional features**: dùng PCA hoặc feature selection trước khi KNN.
- **Dataset lớn với brute force**: sklearn sẽ dùng KD-tree hoặc Ball-tree tự động, nhưng rất dim cao vẫn chậm.

---

## Câu hỏi phỏng vấn hay gặp

- **Support vectors là gì? Tại sao chúng quan trọng?**
- **Hard margin vs Soft margin SVM: khi nào dùng cái nào?**
- **Kernel trick hoạt động thế nào? Tại sao không cần tính φ(x) explicit?**
- **RBF kernel có γ: γ lớn/nhỏ ảnh hưởng gì đến decision boundary?**
- **SVM là convex optimization → ý nghĩa gì?** (global optimum guaranteed)
- **Curse of dimensionality ảnh hưởng KNN thế nào?**
- **Tại sao KNN được gọi là lazy learner?**
- **Elbow method để chọn K trong KNN là gì?**
- **KNN có thể dùng cho regression không? Như thế nào?**
- **So sánh SVM và Logistic Regression cho binary classification.**
- **Khi nào nên dùng Linear kernel thay vì RBF?** (n_features >> n_samples, linearly separable)
