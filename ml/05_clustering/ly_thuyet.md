# Clustering

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng bạn có 1000 tờ báo và cần sắp xếp chúng thành các chủ đề mà không có ai chỉ cho bạn trước. Bạn đọc qua, rồi tự nhiên nhận ra "những tờ này nói về thể thao, những tờ kia về kinh tế, còn nhóm này về chính trị".

Đó chính là **Clustering** — học không giám sát (unsupervised learning): tìm nhóm tự nhiên trong dữ liệu mà không có nhãn cho trước.

**K-Means** giống như: "Tôi muốn chia thành K nhóm. Hãy tìm K trung tâm sao cho mỗi điểm dữ liệu ở gần trung tâm của nhóm mình nhất."

**DBSCAN** giống như: "Chỗ nào đông người (dày đặc) thì là một nhóm. Chỗ vắng người thì không thuộc nhóm nào — gọi là noise."

---

## Giải thích cho người đã biết lập trình (nâng cao)

### K-Means

**Algorithm (Lloyd's algorithm):**
1. **Init**: chọn K centroids (ngẫu nhiên hoặc K-Means++).
2. **Assign**: mỗi điểm gán vào cluster có centroid gần nhất (Euclidean distance).
3. **Update**: centroid mới = mean của tất cả điểm trong cluster.
4. Lặp 2-3 cho đến convergence (centroids không đổi hoặc thay đổi < tolerance).

**Convergence**: K-Means luôn hội tụ (vì inertia giảm đơn điệu mỗi bước), nhưng có thể hội tụ về **local minimum** (phụ thuộc init).

**K-Means++** (init thông minh):
- Chọn centroid 1 ngẫu nhiên.
- Chọn centroid tiếp theo với xác suất tỉ lệ thuận với `D(x)²` (bình phương khoảng cách đến centroid gần nhất).
- Giảm đáng kể khả năng hội tụ về local minimum.

**Limitations:**
- Assumes **spherical, equally-sized clusters** (vì dùng Euclidean mean).
- Sensitive với outliers (một outlier kéo centroid lệch).
- Phải biết K trước.
- Không xử lý tốt clusters có mật độ khác nhau.

### DBSCAN

**Core concepts:**
- **Core point**: điểm có ≥ `min_samples` điểm (bao gồm chính nó) trong vòng tròn bán kính `eps`.
- **Border point**: không phải core nhưng nằm trong eps-neighborhood của một core point.
- **Noise point**: không phải core, không phải border → outlier.

**Cluster expansion**: bắt đầu từ core point, expand theo density-reachability.

**Advantages over K-Means:**
- Tự động xác định số clusters.
- Phát hiện arbitrary-shaped clusters (không chỉ spherical).
- Robust với outliers (chúng là noise, không ảnh hưởng cluster).

**Disadvantages:**
- Nhạy với `eps` và `min_samples`.
- Khó handle clusters có mật độ rất khác nhau (dùng HDBSCAN).
- Không scale tốt với high-dimensional data (curse of dimensionality ảnh hưởng density).

### Hierarchical Clustering

**Agglomerative (bottom-up):**
1. Mỗi điểm là một cluster.
2. Merge hai cluster gần nhau nhất.
3. Lặp đến khi còn 1 cluster.

**Divisive (top-down):** ngược lại, ít dùng hơn.

**Linkage criteria (cách đo khoảng cách giữa clusters):**
- **Single linkage**: min distance giữa 2 điểm bất kỳ → sensitive to outliers, tạo chain.
- **Complete linkage**: max distance → tạo compact clusters.
- **Average linkage**: average distance → cân bằng.
- **Ward linkage**: minimize variance → compact, spherical clusters (thường tốt nhất).

---

## Định nghĩa chính xác

**Clustering**: bài toán học không giám sát, phân chia tập dữ liệu thành các nhóm (clusters) sao cho các điểm trong cùng cluster tương tự nhau hơn các điểm khác cluster theo một distance/similarity metric cho trước.

---

## Công thức / Bảng kỹ thuật

### K-Means Objective (Inertia / WCSS)
```
Inertia = Σₖ Σ_{x∈Cₖ} ||x - μₖ||²

μₖ = (1/|Cₖ|) * Σ_{x∈Cₖ} x    (centroid)
```
Mục tiêu: minimize inertia.

### Elbow Method
```
Vẽ đồ thị: K (trục x) vs Inertia (trục y)
"Elbow point" = điểm gập — thêm K không giảm inertia nhiều nữa
```

### Silhouette Score
```
Cho điểm i:
  a(i) = mean distance đến các điểm trong cùng cluster (cohesion)
  b(i) = min mean distance đến điểm trong cluster khác gần nhất (separation)

s(i) = (b(i) - a(i)) / max(a(i), b(i))

Silhouette Score = mean s(i) over all points
Range: [-1, 1]
  +1: điểm nằm sâu trong cluster đúng
   0: điểm nằm trên ranh giới
  -1: điểm có thể thuộc cluster khác
```

### Davies-Bouldin Index
```
DB = (1/K) * Σₖ max_{j≠k} [(σₖ + σⱼ) / d(μₖ, μⱼ)]

σₖ = mean distance của points trong cluster k đến centroid k
d(μₖ, μⱼ) = distance giữa hai centroids
Nhỏ hơn tốt hơn (0 là tốt nhất)
```

### Calinski-Harabasz Index
```
CH = [SS_between / (K-1)] / [SS_within / (n-K)]

SS_between = variance giữa clusters
SS_within  = variance trong clusters
Lớn hơn tốt hơn
```

### DBSCAN Parameters

| Parameter | Ý nghĩa | Tác động |
|-----------|---------|---------|
| `eps` | Bán kính neighborhood | Nhỏ → nhiều noise, nhiều clusters nhỏ |
| `min_samples` | Số điểm tối thiểu cho core point | Lớn → strict, nhiều noise hơn |

### Complexity

| Thuật toán | Training | Prediction | Space |
|------------|----------|------------|-------|
| K-Means | O(K·m·n·iter) | O(K·n) | O(K·n) |
| DBSCAN | O(m·log m) với indexing | Cần refit | O(m) |
| Hierarchical | O(m²·log m) | N/A (dendrogram) | O(m²) |

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: K-Means + Elbow Method + Silhouette
# ============================================================
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans, DBSCAN, AgglomerativeClustering
from sklearn.datasets import make_blobs, make_moons
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import silhouette_score, davies_bouldin_score, calinski_harabasz_score

# Dataset với clusters rõ ràng
X, y_true = make_blobs(n_samples=500, centers=4, cluster_std=0.8, random_state=42)
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# --- Elbow Method ---
inertias = []
K_range = range(1, 11)
for k in K_range:
    km = KMeans(n_clusters=k, init='k-means++', n_init=10, random_state=42)
    km.fit(X_scaled)
    inertias.append(km.inertia_)

# Silhouette để chọn K tốt hơn
sil_scores = []
for k in range(2, 11):
    km = KMeans(n_clusters=k, init='k-means++', n_init=10, random_state=42)
    labels = km.fit_predict(X_scaled)
    sil_scores.append(silhouette_score(X_scaled, labels))

best_k = np.argmax(sil_scores) + 2  # +2 vì range bắt đầu từ 2
print(f"Best K (silhouette): {best_k}, score: {max(sil_scores):.4f}")

# K-Means với best K
km_best = KMeans(n_clusters=best_k, init='k-means++', n_init=10, random_state=42)
labels_km = km_best.fit_predict(X_scaled)

print(f"\nK-Means Evaluation:")
print(f"  Inertia:           {km_best.inertia_:.4f}")
print(f"  Silhouette Score:  {silhouette_score(X_scaled, labels_km):.4f}")
print(f"  Davies-Bouldin:    {davies_bouldin_score(X_scaled, labels_km):.4f}")
print(f"  Calinski-Harabasz: {calinski_harabasz_score(X_scaled, labels_km):.4f}")

# ============================================================
# PHẦN 2: DBSCAN — tốt với non-spherical clusters
# ============================================================

# Make moons: K-Means thất bại, DBSCAN thành công
X_moon, _ = make_moons(n_samples=500, noise=0.05, random_state=42)
X_moon_s = StandardScaler().fit_transform(X_moon)

# K-Means
km_moon = KMeans(n_clusters=2, random_state=42)
labels_km_moon = km_moon.fit_predict(X_moon_s)
print(f"\nMoons - K-Means Silhouette:  {silhouette_score(X_moon_s, labels_km_moon):.4f}")

# DBSCAN
# Chọn eps: vẽ k-distance plot (khoảng cách đến k-th neighbor, k = min_samples-1)
from sklearn.neighbors import NearestNeighbors
k = 4  # min_samples = 5 → dùng k=4
nbrs = NearestNeighbors(n_neighbors=k).fit(X_moon_s)
distances, _ = nbrs.kneighbors(X_moon_s)
kth_distances = np.sort(distances[:, -1])[::-1]  # k-th distance sorted

# Chọn eps tại "elbow" của k-distance plot (~0.2 cho dataset này)
dbscan = DBSCAN(eps=0.2, min_samples=5)
labels_db = dbscan.fit_predict(X_moon_s)

n_clusters = len(set(labels_db)) - (1 if -1 in labels_db else 0)
n_noise = list(labels_db).count(-1)
print(f"Moons - DBSCAN: {n_clusters} clusters, {n_noise} noise points")
if n_clusters > 1:
    # Chỉ tính silhouette trên non-noise points
    mask = labels_db != -1
    print(f"Moons - DBSCAN Silhouette: {silhouette_score(X_moon_s[mask], labels_db[mask]):.4f}")

# ============================================================
# PHẦN 3: Hierarchical Clustering
# ============================================================
X_hier, _ = make_blobs(n_samples=200, centers=3, random_state=42)
X_hier_s = StandardScaler().fit_transform(X_hier)

# Agglomerative clustering
for linkage in ['ward', 'complete', 'average', 'single']:
    agg = AgglomerativeClustering(n_clusters=3, linkage=linkage)
    labels_agg = agg.fit_predict(X_hier_s)
    sil = silhouette_score(X_hier_s, labels_agg)
    print(f"Hierarchical ({linkage:8s}): Silhouette = {sil:.4f}")

# Dendrogram visualization
from scipy.cluster.hierarchy import dendrogram, linkage as scipy_linkage
Z = scipy_linkage(X_hier_s[:50], method='ward')  # Chỉ lấy 50 points để visualization rõ
# plt.figure(figsize=(12,5))
# dendrogram(Z, truncate_mode='lastp', p=12)  # Hiển thị 12 last merges
# plt.title('Dendrogram (Ward linkage)')
# plt.show()
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**K-Means — Dùng khi:**
- Biết (hoặc có thể ước tính) số clusters K.
- Clusters có hình dạng gần spherical và kích thước tương đương.
- Dataset lớn (scale tốt).
- Cần nhanh: customer segmentation, document clustering, image compression.

**K-Means — Không dùng khi:**
- Clusters phi cầu (crescent, ring, irregular shape) → DBSCAN.
- Dữ liệu có nhiều outliers → DBSCAN hoặc K-Medoids.
- Không biết K và không thể estimate.

**DBSCAN — Dùng khi:**
- Không biết số clusters trước.
- Cần phát hiện outliers/anomalies.
- Clusters có hình dạng tùy ý (arbitrary shape).
- Dữ liệu có density tương đối đồng đều.

**DBSCAN — Không dùng khi:**
- Clusters có density rất khác nhau → HDBSCAN.
- High-dimensional data → density estimation kém (curse of dimensionality).
- Cần assign nhãn cho điểm mới (DBSCAN không có predict cho new data trong sklearn).

**Hierarchical — Dùng khi:**
- Cần khám phá cấu trúc phân cấp (dendrogram).
- Dataset nhỏ (< 10,000 samples, vì `O(m²)`).
- Không biết K và muốn xem toàn bộ cấu trúc clusters.

---

## So sánh K-Means vs DBSCAN vs Hierarchical

| Tiêu chí | K-Means | DBSCAN | Hierarchical |
|----------|---------|--------|--------------|
| Cần biết K | Có | Không | Không (dendrogram) |
| Hình dạng cluster | Spherical | Arbitrary | Arbitrary |
| Outlier handling | Kém | Tốt (noise label) | Trung bình |
| Scalability | Tốt O(Kmn) | Trung bình | Kém O(m²) |
| Deterministic | Không (init) | Có | Có |
| Parameters | K | eps, min_samples | n_clusters, linkage |
| Density varying | Kém | Kém | Trung bình |
| Interpretability | Tốt (centroids) | Trung bình | Tốt (dendrogram) |

---

## Lỗi thường gặp (Common Pitfalls)

- **Không scale features**: K-Means dùng Euclidean distance → features với range lớn dominate → luôn StandardScaler trước.
- **Dùng Elbow method một mình**: elbow không phải lúc nào cũng rõ ràng → kết hợp Silhouette score.
- **Chỉ chạy K-Means một lần**: local minima → dùng `n_init=10` hoặc nhiều hơn.
- **DBSCAN không tune eps**: dùng k-distance plot để chọn eps một cách có căn cứ, không đoán.
- **Dùng inertia để so sánh K khác nhau trực tiếp**: inertia luôn giảm khi K tăng → không phải metric tốt để so sánh, dùng Silhouette.
- **Hierarchical cho dataset lớn**: `O(m²)` space → cực kỳ chậm và tốn RAM với m > 10,000.
- **Đánh giá clustering bằng accuracy với true labels**: chỉ valid khi có ground truth; thực tế clustering là unsupervised, dùng internal metrics.

---

## Câu hỏi phỏng vấn hay gặp

- **K-Means và K-Medoids khác gì nhau? Khi nào dùng K-Medoids?**
- **K-Means++ cải thiện K-Means thế nào?**
- **Silhouette score là gì? Giá trị nào là tốt?**
- **DBSCAN phân biệt core/border/noise point thế nào?**
- **Khi nào DBSCAN tốt hơn K-Means? Khi nào ngược lại?**
- **Elbow method hoạt động thế nào? Tại sao không phải lúc nào cũng rõ ràng?**
- **Hierarchical clustering: agglomerative vs divisive, Ward linkage là gì?**
- **Curse of dimensionality ảnh hưởng clustering thế nào?**
- **Làm sao evaluate chất lượng clustering khi không có ground truth labels?**
- **K-Means có hội tụ không? Hội tụ về đâu?** (luôn hội tụ, nhưng local minimum)
