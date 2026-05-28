# Linear Regression

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn muốn đoán giá nhà dựa vào diện tích. Bạn thu thập dữ liệu của 100 căn nhà: căn 50m² giá 2 tỷ, căn 100m² giá 4 tỷ, căn 75m² giá 3 tỷ...

Bạn vẽ các điểm này lên tờ giấy. Nhìn vào, bạn thấy chúng gần như nằm theo một đường thẳng. Vậy nếu ai đó hỏi "căn 80m² giá bao nhiêu?", bạn chỉ cần kẻ một đường thẳng qua đám mây điểm đó, rồi tìm điểm tương ứng với 80m² trên đường thẳng ấy.

**Linear Regression** chính là thuật toán tìm ra đường thẳng "khớp nhất" với tập dữ liệu đó — sao cho tổng khoảng cách từ các điểm thực tế đến đường thẳng là nhỏ nhất.

---

## Giải thích cho người đã biết lập trình (nâng cao)

Linear Regression học một ánh xạ tuyến tính `f: R^n → R` bằng cách tối thiểu hóa hàm mất mát MSE trên tập huấn luyện.

**Cơ chế hoạt động bên trong:**
- Model tham số hóa bằng vector `w` (weights) và scalar `b` (bias).
- Trong không gian feature n chiều, hypothesis function tạo ra một **hyperplane** chia không gian.
- Gradient Descent cập nhật `w` theo hướng ngược gradient của loss surface — loss surface của Linear Regression là **convex** (dạng chén), nên luôn hội tụ về global minimum (không bị stuck ở local minimum).
- Normal Equation tính trực tiếp `w*` bằng đại số tuyến tính, không cần iteration.

**Trade-offs quan trọng:**
- Feature scaling **bắt buộc** cho Gradient Descent (nhưng không ảnh hưởng Normal Equation về mặt kết quả, chỉ ảnh hưởng tốc độ).
- Normal Equation `O(n³)` vì cần tính nghịch đảo ma trận — không khả thi khi n > 10,000.
- Regularization (Ridge/Lasso) thêm penalty vào loss, kéo weights về 0 để tránh overfitting.

**Edge cases:**
- Multicollinearity: các features tương quan cao làm ma trận `X^T X` gần singular → Normal Equation không ổn định → dùng Ridge Regression.
- Outliers ảnh hưởng mạnh vì MSE phạt sai số bình phương → cân nhắc dùng Huber Loss.

---

## Định nghĩa chính xác

**Linear Regression** là phương pháp học có giám sát (supervised learning) dùng để dự đoán một giá trị liên tục (continuous variable). Model giả định mối quan hệ tuyến tính giữa biến đầu vào `X ∈ R^(m×n)` và biến mục tiêu `y ∈ R^m`.

---

## Công thức / Bảng kỹ thuật

### Hypothesis Function
```
ŷ = Xw + b    (dạng vector)
ŷᵢ = w₁x₁ + w₂x₂ + ... + wₙxₙ + b
```

### Cost Function — Mean Squared Error (MSE)
```
J(w, b) = (1/2m) * Σᵢ (ŷᵢ - yᵢ)²
         = (1/2m) * ||Xw - y||²
```
(Hệ số 1/2 để đạo hàm gọn hơn, không ảnh hưởng kết quả tối ưu)

### Gradient Descent Update Rule
```
w := w - α * ∂J/∂w = w - α * (1/m) * X^T(Xw - y)
b := b - α * ∂J/∂b = b - α * (1/m) * Σᵢ(ŷᵢ - yᵢ)
```
Với `α` là learning rate.

### Normal Equation (Closed-form solution)
```
w* = (X^T X)⁻¹ X^T y
```

### Regularization

**Ridge Regression (L2):**
```
J_ridge(w) = (1/2m) * ||Xw - y||² + λ||w||²
w*_ridge = (X^T X + λI)⁻¹ X^T y
```

**Lasso Regression (L1):**
```
J_lasso(w) = (1/2m) * ||Xw - y||² + λ||w||₁
```
(Không có closed-form, cần coordinate descent)

### Feature Scaling

**Standardization (Z-score):**
```
x' = (x - μ) / σ
```

**Min-Max Normalization:**
```
x' = (x - x_min) / (x_max - x_min)
```

### Evaluation Metrics

| Metric | Công thức | Ý nghĩa |
|--------|-----------|---------|
| MSE | (1/m)Σ(ŷᵢ-yᵢ)² | Trung bình bình phương sai số |
| RMSE | √MSE | Cùng đơn vị với y, dễ diễn giải |
| MAE | (1/m)Σ\|ŷᵢ-yᵢ\| | Ít nhạy với outlier hơn MSE |
| R² | 1 - SS_res/SS_tot | Tỉ lệ variance được giải thích (0→1, cao hơn tốt hơn) |
| Adjusted R² | 1 - (1-R²)(m-1)/(m-n-1) | R² điều chỉnh theo số features |

### Gradient Descent vs Normal Equation

| Tiêu chí | Gradient Descent | Normal Equation |
|----------|-----------------|-----------------|
| Cần chọn α | Có | Không |
| Số iterations | Nhiều | Không cần |
| Độ phức tạp mỗi bước | O(mn) | O(n³) |
| Phù hợp n lớn | Tốt | Kém (n > 10,000) |
| Phù hợp m lớn | Tốt | Tốt |
| Non-invertible matrix | Không ảnh hưởng | Cần xử lý thêm |

### Assumptions của Linear Regression (LINE)

| Assumption | Tên | Vi phạm → hậu quả |
|------------|-----|-------------------|
| **L**inearity | Quan hệ tuyến tính | Bias, predictions sai |
| **I**ndependence | Residuals độc lập | SE không chính xác (time series) |
| **N**ormality | Residuals phân phối chuẩn | Inference không valid |
| **E**qual variance | Homoscedasticity | Kém hiệu quả, CI không chính xác |

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: Linear Regression từ scratch với NumPy
# ============================================================
import numpy as np
import matplotlib.pyplot as plt

class LinearRegressionScratch:
    def __init__(self, learning_rate=0.01, n_iterations=1000):
        self.lr = learning_rate
        self.n_iter = n_iterations
        self.w = None
        self.b = None
        self.loss_history = []

    def fit(self, X, y):
        m, n = X.shape
        self.w = np.zeros(n)
        self.b = 0.0

        for i in range(self.n_iter):
            # Forward pass
            y_pred = X @ self.w + self.b

            # Tính gradient
            error = y_pred - y
            dw = (1/m) * X.T @ error
            db = (1/m) * np.sum(error)

            # Cập nhật parameters
            self.w -= self.lr * dw
            self.b -= self.lr * db

            # Lưu loss để monitor
            loss = (1/(2*m)) * np.sum(error**2)
            self.loss_history.append(loss)

        return self

    def predict(self, X):
        return X @ self.w + self.b

    def score(self, X, y):
        y_pred = self.predict(X)
        ss_res = np.sum((y - y_pred)**2)
        ss_tot = np.sum((y - np.mean(y))**2)
        return 1 - ss_res/ss_tot  # R²


# ============================================================
# PHẦN 2: sklearn LinearRegression
# ============================================================
from sklearn.linear_model import LinearRegression, Ridge, Lasso
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score
from sklearn.datasets import fetch_california_housing

# Load dataset
data = fetch_california_housing()
X, y = data.data, data.target

# Split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Feature scaling — QUAN TRỌNG cho Gradient Descent
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)  # Chỉ transform, KHÔNG fit lại

# Train model
model = LinearRegression()
model.fit(X_train_scaled, y_train)

# Evaluate
y_pred = model.predict(X_test_scaled)
print(f"RMSE: {np.sqrt(mean_squared_error(y_test, y_pred)):.4f}")
print(f"MAE:  {mean_absolute_error(y_test, y_pred):.4f}")
print(f"R²:   {r2_score(y_test, y_pred):.4f}")

# Regularization comparison
for name, reg_model in [
    ("No Reg", LinearRegression()),
    ("Ridge (L2)", Ridge(alpha=1.0)),
    ("Lasso (L1)", Lasso(alpha=0.1)),
]:
    reg_model.fit(X_train_scaled, y_train)
    y_pred = reg_model.predict(X_test_scaled)
    r2 = r2_score(y_test, y_pred)
    print(f"{name:15s} R²={r2:.4f}")

# ============================================================
# PHẦN 3: Normal Equation từ scratch
# ============================================================
def normal_equation(X, y):
    """w* = (X^T X)^-1 X^T y"""
    X_b = np.c_[np.ones((len(X), 1)), X]  # Thêm bias term
    w_star = np.linalg.pinv(X_b.T @ X_b) @ X_b.T @ y  # pinv để tránh singular
    return w_star[0], w_star[1:]  # (bias, weights)
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- Quan hệ giữa features và target thực sự gần tuyến tính.
- Cần model **diễn giải được** (interpretable) — coefficients cho biết tác động của từng feature.
- Dataset nhỏ đến trung bình, cần training nhanh.
- Làm baseline trước khi thử các model phức tạp hơn.
- Output cần là giá trị liên tục (giá nhà, nhiệt độ, doanh thu...).

**Không dùng khi:**
- Quan hệ phi tuyến rõ ràng (dùng Polynomial Regression, Tree-based models, hoặc Neural Network).
- Dữ liệu có nhiều outliers (cân nhắc Huber Regression hoặc thêm robust preprocessing).
- Bài toán classification (dùng Logistic Regression hoặc models khác).
- Features có multicollinearity cao mà không dùng regularization.
- n_features > m_samples (high-dimensional, underdetermined) mà không có regularization.

---

## So sánh với các thuật toán liên quan

| Tiêu chí | Linear Regression | Ridge (L2) | Lasso (L1) | Polynomial Regression |
|----------|-------------------|------------|------------|----------------------|
| Regularization | Không | L2 | L1 | Không |
| Feature selection | Không | Không (shrink) | Có (zero out) | Không |
| Xử lý multicollinearity | Kém | Tốt | Trung bình | Kém |
| Diễn giải | Dễ | Trung bình | Dễ (sparse) | Khó |
| Overfitting | Dễ bị | Ít hơn | Ít hơn | Rất dễ bị |
| Quan hệ phi tuyến | Không | Không | Không | Có (degree nhỏ) |

---

## Lỗi thường gặp (Common Pitfalls)

- **Data leakage khi scaling**: fit scaler trên cả train+test thay vì chỉ fit trên train → test set bị "nhìn" trước.
- **Quên feature scaling**: Gradient Descent hội tụ rất chậm hoặc không hội tụ khi features có range khác nhau nhiều.
- **Dùng R² một mình**: R² cao không có nghĩa model tốt — cần kiểm tra residuals và plot actual vs predicted.
- **Không kiểm tra assumptions**: dùng Linear Regression cho dữ liệu có quan hệ phi tuyến rõ ràng.
- **Overfitting với nhiều features**: thêm quá nhiều features mà không regularize, model fit noise.
- **Nhầm Adjusted R²**: khi so sánh models với số features khác nhau, phải dùng Adjusted R², không dùng R² thuần.
- **Không handle outliers**: một vài outliers cực đoan có thể kéo lệch đường hồi quy đáng kể.

---

## Câu hỏi phỏng vấn hay gặp

- **Gradient Descent và Normal Equation khác gì nhau? Khi nào dùng cái nào?**
- **Tại sao phải feature scaling trước Gradient Descent?**
- **R² là gì? R² = 0.85 có nghĩa gì?**
- **Ridge và Lasso khác gì nhau? Cái nào làm feature selection?**
- **Assumptions của Linear Regression là gì? Nếu vi phạm thì sao?**
- **Tại sao loss surface của Linear Regression là convex?**
- **Multicollinearity ảnh hưởng thế nào? Giải quyết ra sao?**
- **Underfitting và overfitting xảy ra như thế nào trong Linear Regression? Cách fix?**
- **Nếu m < n (ít samples hơn features), điều gì xảy ra?**
- **Tại sao dùng MSE làm loss thay vì MAE?** (MSE differentiable everywhere, convex, nhạy với outliers)
