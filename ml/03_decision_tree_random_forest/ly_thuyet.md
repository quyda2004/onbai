# Decision Tree & Random Forest

---

## Giải thích cho người mới hoàn toàn

Hãy nghĩ đến trò chơi "20 câu hỏi": bạn cần đoán một con vật, và chỉ được hỏi các câu hỏi có/không. "Nó có 4 chân không?" → Có. "Nó ăn thịt không?" → Không. "Nó sống dưới nước không?" → Không. "Nó là bò không?" → Đúng!

**Decision Tree** hoạt động y hệt: nó học một chuỗi câu hỏi có/không (điều kiện) từ dữ liệu, sắp xếp thành hình cây. Mỗi nhánh là một câu hỏi, mỗi lá là câu trả lời cuối cùng (nhãn phân loại hoặc giá trị dự đoán).

**Random Forest** là "hội đồng" của nhiều Decision Tree: mỗi cây được xây trên một tập dữ liệu khác nhau (lấy mẫu ngẫu nhiên) và chỉ nhìn vào một số features ngẫu nhiên. Kết quả cuối là đa số phiếu (classification) hoặc trung bình (regression) từ tất cả cây. Như thể hỏi ý kiến 100 chuyên gia thay vì 1 người — ít bị lệch hơn nhiều.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Decision Tree

**Splitting criteria**: tại mỗi node, thuật toán tìm feature và threshold tối ưu để **giảm impurity** nhất.

- **Gini Impurity** (mặc định trong sklearn): đo xác suất phân loại sai ngẫu nhiên.
- **Information Gain (Entropy)**: đo lượng thông tin thu được sau khi biết giá trị feature.

Thuật toán phổ biến: **CART** (sklearn), **ID3**, **C4.5**.

**Overfitting**: Decision Tree có thể fit hoàn hảo training data (mỗi leaf chứa 1 sample) → variance cao. Giải pháp: **pruning**.

**Pre-pruning** (early stopping): dừng sớm khi:
- `max_depth` đạt giới hạn
- `min_samples_split` / `min_samples_leaf` không đủ
- `max_leaf_nodes` đạt giới hạn

**Post-pruning** (cost-complexity pruning): build cây đầy đủ rồi cắt bớt node dựa trên validation performance.

### Random Forest

**Bagging (Bootstrap Aggregating)**:
- Tạo m cây, mỗi cây train trên bootstrap sample (sampling with replacement, ~63.2% unique samples).
- **Out-of-Bag (OOB) samples** (~36.8% còn lại) dùng để validate mà không cần validation set riêng.

**Feature Randomness**:
- Mỗi split chỉ xem xét `max_features` features ngẫu nhiên (thường `sqrt(n_features)` cho classification, `n_features/3` cho regression).
- Giảm correlation giữa các cây → ensemble hiệu quả hơn.

**Bias-Variance Decomposition**:
- Mỗi cây sâu: low bias, high variance.
- Averaging nhiều cây: variance giảm đáng kể, bias gần như không đổi.
- Kết quả: low bias, lower variance → tốt hơn single tree.

**Feature Importance**:
- Tính bằng tổng decrease in impurity do feature đó gây ra, trung bình trên tất cả cây.
- **Cảnh báo**: biased toward high-cardinality features → dùng `permutation_importance` cho kết quả đáng tin hơn.

---

## Định nghĩa chính xác

**Decision Tree**: thuật toán học có giám sát sử dụng cấu trúc cây để học các quy tắc quyết định (decision rules) từ features, chia không gian feature thành các vùng axis-aligned (hyperrectangles).

**Random Forest**: phương pháp ensemble kết hợp nhiều Decision Tree độc lập sử dụng Bagging và feature randomness, giảm variance so với single tree.

---

## Công thức / Bảng kỹ thuật

### Gini Impurity
```
Gini(S) = 1 - Σₖ pₖ²

Với pₖ = tỉ lệ class k trong node S
Gini = 0: node thuần (pure) — tất cả cùng class
Gini = 0.5: worst case (binary, 50-50)
```

### Entropy và Information Gain
```
Entropy(S) = -Σₖ pₖ log₂(pₖ)    (0 log 0 = 0 by convention)

Information Gain(S, A) = Entropy(S) - Σᵥ (|Sᵥ|/|S|) * Entropy(Sᵥ)
```
Với A là feature, v là các giá trị của A.

### Gini vs Entropy

| Tiêu chí | Gini Impurity | Entropy |
|----------|---------------|---------|
| Tính toán | Nhanh hơn (không có log) | Chậm hơn |
| Range | [0, 0.5] (binary) | [0, 1] (binary) |
| Bias | Hơi thiên về balanced splits | Thiên về features nhiều values |
| Kết quả thực tế | Gần như tương đương | Gần như tương đương |

### Splitting tại một node
```
Gain = Impurity(parent) - weighted_avg_impurity(children)
Best split = argmax over (feature, threshold) of Gain
```

### Out-of-Bag Error
```
OOB Error = fraction of OOB samples misclassified
(tính trên mỗi sample bằng các cây KHÔNG dùng sample đó để train)
```

### Hyperparameters quan trọng

| Hyperparameter | Tác động | Default sklearn |
|----------------|----------|-----------------|
| `n_estimators` | Nhiều cây hơn → variance thấp hơn, tốn TG hơn | 100 |
| `max_depth` | Giới hạn độ sâu → giảm overfitting | None (full) |
| `max_features` | Features mỗi split → giảm correlation giữa cây | `sqrt(n)` (clf) |
| `min_samples_split` | Số mẫu tối thiểu để split | 2 |
| `min_samples_leaf` | Số mẫu tối thiểu tại leaf | 1 |
| `bootstrap` | Dùng bootstrap sampling | True |
| `oob_score` | Tính OOB score | False |

### Complexity

| Operation | Decision Tree | Random Forest |
|-----------|---------------|---------------|
| Training | O(mn log n) | O(T * m√n log n) |
| Prediction | O(depth) | O(T * depth) |
| Space | O(nodes) | O(T * nodes) |

Với m = n_samples, n = n_features, T = n_estimators.

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: Decision Tree từ scratch (ý tưởng core)
# ============================================================
import numpy as np
from collections import Counter

def gini_impurity(y):
    """Tính Gini impurity của một node"""
    m = len(y)
    if m == 0:
        return 0
    counts = Counter(y)
    return 1 - sum((c/m)**2 for c in counts.values())

def best_split(X, y):
    """Tìm feature và threshold tốt nhất để split"""
    best_gain = -1
    best_feature, best_threshold = None, None
    parent_gini = gini_impurity(y)

    for feature in range(X.shape[1]):
        thresholds = np.unique(X[:, feature])
        for threshold in thresholds:
            left_mask = X[:, feature] <= threshold
            right_mask = ~left_mask
            if left_mask.sum() == 0 or right_mask.sum() == 0:
                continue

            n = len(y)
            gain = parent_gini - (
                (left_mask.sum()/n) * gini_impurity(y[left_mask]) +
                (right_mask.sum()/n) * gini_impurity(y[right_mask])
            )

            if gain > best_gain:
                best_gain = gain
                best_feature = feature
                best_threshold = threshold

    return best_feature, best_threshold, best_gain

# ============================================================
# PHẦN 2: sklearn Decision Tree
# ============================================================
from sklearn.tree import DecisionTreeClassifier, export_text, plot_tree
from sklearn.ensemble import RandomForestClassifier
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.metrics import classification_report
from sklearn.inspection import permutation_importance
import matplotlib.pyplot as plt

data = load_breast_cancer()
X, y = data.data, data.target
feature_names = data.feature_names

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# ---- Decision Tree ----
# max_depth=None → full tree → overfit
dt_full = DecisionTreeClassifier(random_state=42)
dt_full.fit(X_train, y_train)

# Pre-pruning để tránh overfitting
dt_pruned = DecisionTreeClassifier(
    max_depth=5,
    min_samples_split=10,
    min_samples_leaf=5,
    criterion='gini',
    random_state=42
)
dt_pruned.fit(X_train, y_train)

print(f"Full Tree   — Train: {dt_full.score(X_train, y_train):.4f}, Test: {dt_full.score(X_test, y_test):.4f}")
print(f"Pruned Tree — Train: {dt_pruned.score(X_train, y_train):.4f}, Test: {dt_pruned.score(X_test, y_test):.4f}")

# Visualize tree (chỉ hiệu quả với shallow tree)
print(export_text(dt_pruned, feature_names=list(feature_names)))

# ---- Random Forest ----
rf = RandomForestClassifier(
    n_estimators=200,
    max_depth=None,        # Mỗi cây grow full → low bias
    max_features='sqrt',   # sqrt(n_features) mỗi split
    bootstrap=True,
    oob_score=True,        # Validate với OOB samples
    n_jobs=-1,             # Dùng tất cả CPU cores
    random_state=42
)
rf.fit(X_train, y_train)

print(f"\nRandom Forest:")
print(f"  Train Accuracy: {rf.score(X_train, y_train):.4f}")
print(f"  Test  Accuracy: {rf.score(X_test, y_test):.4f}")
print(f"  OOB   Score:    {rf.oob_score_:.4f}")  # Ước tính test performance không cần val set
print(classification_report(y_test, rf.predict(X_test)))

# ---- Feature Importance ----
# 1. Impurity-based (nhanh nhưng biased)
importances = rf.feature_importances_
top_idx = np.argsort(importances)[::-1][:10]
print("\nTop 10 Features (impurity-based):")
for i in top_idx:
    print(f"  {feature_names[i]:40s}: {importances[i]:.4f}")

# 2. Permutation importance (chậm hơn nhưng đáng tin hơn)
perm_imp = permutation_importance(rf, X_test, y_test, n_repeats=10, random_state=42)
perm_top = np.argsort(perm_imp.importances_mean)[::-1][:10]
print("\nTop 10 Features (permutation importance):")
for i in perm_top:
    print(f"  {feature_names[i]:40s}: {perm_imp.importances_mean[i]:.4f} ± {perm_imp.importances_std[i]:.4f}")

# ---- Cross-validation để chọn hyperparameters ----
from sklearn.model_selection import GridSearchCV

param_grid = {
    'n_estimators': [100, 200],
    'max_depth': [None, 5, 10],
    'max_features': ['sqrt', 'log2'],
}
grid = GridSearchCV(RandomForestClassifier(random_state=42),
                    param_grid, cv=5, scoring='accuracy', n_jobs=-1)
grid.fit(X_train, y_train)
print(f"\nBest params: {grid.best_params_}")
print(f"Best CV score: {grid.best_score_:.4f}")
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Decision Tree — Dùng khi:**
- Cần model dễ giải thích cho stakeholder không kỹ thuật.
- Dữ liệu có mixed types (numerical + categorical).
- Không cần feature scaling.
- Dataset nhỏ, cần training nhanh.

**Decision Tree — Không dùng khi:**
- Cần accuracy cao (thường bị overfitting).
- Dataset có noise nhiều.
- Quan hệ tuyến tính → Logistic/Linear Regression đơn giản hơn.

**Random Forest — Dùng khi:**
- Cần accuracy cao mà vẫn muốn interpretability (feature importance).
- Dataset trung bình đến lớn.
- Dữ liệu có missing values (có thể handle tốt).
- Muốn OOB score thay vì validation set riêng.
- Bài toán tabular data — Random Forest thường là model mạnh nhất cho tabular.

**Random Forest — Không dùng khi:**
- Cần model rất nhanh ở inference time với ít bộ nhớ.
- Dữ liệu sequence/image/text → Deep Learning phù hợp hơn.
- Dataset rất nhỏ (< vài trăm samples) → overfitting risky.
- Cần diễn giải chi tiết từng quyết định (black box hơn single tree).

---

## So sánh với các thuật toán liên quan

| Tiêu chí | Decision Tree | Random Forest | Gradient Boosting (XGBoost) | Logistic Regression |
|----------|---------------|---------------|------------------------------|---------------------|
| Accuracy | Trung bình | Cao | Rất cao | Trung bình |
| Overfitting | Dễ bị | Ít hơn | Ít nếu tuned | Ít (với regularization) |
| Training speed | Nhanh | Trung bình | Chậm hơn RF | Rất nhanh |
| Interpretability | Rất cao | Trung bình (FI) | Thấp | Cao |
| Feature scaling cần | Không | Không | Không | Có |
| Hyperparameter tuning | Ít | Trung bình | Nhiều | Ít |
| Parallel training | Không | Có (cây độc lập) | Không (sequential) | Không |
| Tabular data | Tốt | Rất tốt | Tốt nhất | Khá |

---

## Lỗi thường gặp (Common Pitfalls)

- **Không pruning Decision Tree**: train accuracy 100% nhưng test accuracy thấp → phải giới hạn `max_depth` hoặc `min_samples_leaf`.
- **Feature importance bị biased**: impurity-based importance ưu tiên features có nhiều giá trị unique → dùng `permutation_importance`.
- **Quá ít n_estimators**: Random Forest với 10 cây có variance cao → cần ít nhất 100-200, check OOB score để xác định.
- **Không dùng `stratify` khi split**: imbalanced classes bị phân phối không đều giữa train/test.
- **Nhầm Random Forest với Bagging**: RF thêm feature randomness, không chỉ bootstrap — đây là điểm khác biệt quan trọng.
- **Không check overfitting**: train score = 1.0 không phải lúc nào cũng OK, cần so sánh với test/CV score.
- **Dùng RF cho text/image raw**: cần feature engineering trước, không dùng raw pixel/token.

---

## Câu hỏi phỏng vấn hay gặp

- **Gini Impurity và Information Gain khác gì nhau? Khi nào chọn cái nào?**
- **Decision Tree overfit như thế nào? Cách prevent?**
- **Random Forest giảm variance thế nào? Tại sao không giảm bias?**
- **OOB error là gì? Nó ước tính được gì?**
- **Feature importance trong RF tính như thế nào? Hạn chế?**
- **Tại sao RF lại chọn subset of features tại mỗi split?** (giảm correlation giữa cây)
- **Bagging vs Boosting: điểm khác biệt cốt lõi là gì?**
- **Khi nào Random Forest tốt hơn Gradient Boosting và ngược lại?**
- **Decision Tree có cần feature scaling không? Tại sao?** (Không, vì splits dựa trên rank/threshold)
- **Pre-pruning vs Post-pruning: trade-offs?**
