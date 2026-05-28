# CNN & RNN

---

## Giải thích cho người mới hoàn toàn

### CNN (Convolutional Neural Network)

Khi bạn nhìn vào một bức ảnh con mèo, não bạn không nhìn toàn bộ ảnh cùng lúc. Thay vào đó, nó xử lý từng vùng nhỏ: góc này có tai, giữa có mắt, dưới có râu... rồi ghép lại thành nhận diện "đây là mèo".

**CNN** làm điều tương tự: sử dụng những "kính lúp" nhỏ (filters) trượt qua ảnh, mỗi filter phát hiện một đặc trưng (cạnh ngang, cạnh dọc, màu sắc, kết cấu...). Nhiều tầng filter chồng nhau giúp network học từ đặc trưng đơn giản đến phức tạp.

### RNN (Recurrent Neural Network)

Hãy nghĩ đến việc đọc câu "Con mèo đang ăn...". Để đoán từ tiếp theo ("cá", "chuột"...), bạn cần nhớ ngữ cảnh trước đó — không thể chỉ nhìn vào từ cuối.

**RNN** giải quyết điều này bằng cách có "bộ nhớ" (hidden state): sau khi xử lý mỗi từ, nó truyền thông tin đó sang bước tiếp theo. Giống như bạn giữ trong đầu nội dung đã đọc khi tiếp tục đọc tiếp.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### CNN — Cơ chế toán học

**Convolution operation**: filter W (kernel) trượt qua input X với stride s:
```
(X * W)[i,j] = Σₘ Σₙ X[i*s+m, j*s+n] * W[m,n]  + b
```
**Feature map** (activation map): output sau convolution.

**Inductive biases của CNN** (giải thích tại sao hiệu quả với image):
1. **Local connectivity**: mỗi neuron chỉ kết nối với vùng nhỏ của input (receptive field) → giảm parameters.
2. **Weight sharing**: cùng một filter áp dụng cho mọi vị trí → feature detector translation invariant.
3. **Hierarchical representation**: early layers học edges, later layers học textures, objects.

**Backprop qua convolution**: gradient của loss theo filter W là convolution giữa input và gradient của output.

### CNN Architectures

- **AlexNet (2012)**: deep CNN đầu tiên thắng ImageNet — 5 conv layers + 3 FC.
- **VGG (2014)**: deep nhưng simple — chỉ dùng 3×3 conv. Học sâu hơn = tốt hơn.
- **ResNet (2015)**: **Skip connections** giải quyết vanishing gradient cho very deep networks (50, 101, 152 layers). `H(x) = F(x) + x` — network học **residual** thay vì full mapping.
- **EfficientNet (2019)**: compound scaling (depth + width + resolution) — SOTA với ít parameters.

**Transfer Learning**:
- **Feature extraction**: freeze tất cả conv layers của pretrained model, chỉ train FC head.
- **Fine-tuning**: unfreeze một số last conv layers, train với learning rate nhỏ hơn.
- Nguyên tắc: dataset nhỏ + similar domain → feature extraction; dataset lớn → fine-tune.

### RNN — Cơ chế toán học

```
hₜ = tanh(Wₕ · hₜ₋₁ + Wₓ · xₜ + b)
yₜ = Wᵧ · hₜ + bᵧ
```

**Vanishing gradient qua time**: backpropagation qua nhiều timesteps nhân gradient với `Wₕ` nhiều lần → gradient vanish (nếu |eigenvalues Wₕ| < 1) hoặc explode (nếu > 1).

### LSTM — Long Short-Term Memory

**Hai luồng thông tin**:
- **Cell state** `Cₜ`: "long-term memory" — thông tin quan trọng được giữ lâu.
- **Hidden state** `hₜ`: "short-term memory" — output tại timestep t.

**Forget gate**: quyết định thông tin nào trong cell state cần "quên".
```
fₜ = σ(Wf · [hₜ₋₁, xₜ] + bf)    (0 = quên, 1 = nhớ)
```
**Input gate**: quyết định thông tin mới nào cần thêm vào.
```
iₜ = σ(Wᵢ · [hₜ₋₁, xₜ] + bᵢ)
C̃ₜ = tanh(Wc · [hₜ₋₁, xₜ] + bc)
Cₜ = fₜ ⊙ Cₜ₋₁ + iₜ ⊙ C̃ₜ
```
**Output gate**: quyết định phần nào của cell state trở thành hidden state.
```
oₜ = σ(Wo · [hₜ₋₁, xₜ] + bo)
hₜ = oₜ ⊙ tanh(Cₜ)
```

**Cell state như "highway"**: gradient có thể flow qua nhiều timesteps mà không vanish.

---

## Định nghĩa chính xác

**CNN**: kiến trúc Neural Network dùng convolution operation để khai thác spatial locality và translation invariance trong dữ liệu có cấu trúc không gian (images, audio).

**RNN**: kiến trúc Neural Network có kết nối vòng lặp (recurrent connections) cho phép xử lý sequential data bằng cách duy trì hidden state qua các timesteps.

**LSTM**: dạng RNN đặc biệt với gating mechanisms (forget/input/output gates) và cell state riêng biệt, giải quyết vanishing gradient cho long-range dependencies.

---

## Công thức / Bảng kỹ thuật

### CNN — Output Dimensions

```
Output height = (H - F + 2P) / S + 1
Output width  = (W - F + 2P) / S + 1

H, W = input height/width
F    = filter size
P    = padding
S    = stride
```

### Padding Types

| Type | Mô tả | Output size vs Input |
|------|-------|---------------------|
| 'valid' | Không padding | Nhỏ hơn |
| 'same' | Padding để giữ size | Bằng (khi S=1) |

### Pooling Operations

| Type | Công thức | Tác dụng |
|------|-----------|---------|
| Max Pooling | max trong window | Giữ đặc trưng mạnh nhất, giảm noise |
| Average Pooling | mean trong window | Smooth, dùng trong global pooling |
| Global Average Pooling | mean toàn feature map | Thay FC layer, giảm parameters |

### Skip Connection (ResNet)
```
H(x) = F(x) + x         (identity shortcut)
H(x) = F(x) + Wx         (projection shortcut, khi dimensions khác)
```

### LSTM Gates Summary

| Gate | Activation | Vai trò |
|------|-----------|---------|
| Forget gate fₜ | Sigmoid | Bao nhiêu cell state cũ cần giữ |
| Input gate iₜ | Sigmoid | Bao nhiêu new info cần thêm |
| Candidate C̃ₜ | Tanh | New info candidate |
| Output gate oₜ | Sigmoid | Bao nhiêu cell state expose thành hidden |
| Cell state Cₜ | - | Long-term memory (linear, gradient highway) |
| Hidden state hₜ | Tanh(Cₜ) filtered | Short-term memory / output |

### GRU vs LSTM

| Tiêu chí | GRU | LSTM |
|----------|-----|------|
| Gates | 2 (reset, update) | 3 (forget, input, output) |
| States | 1 (hidden) | 2 (hidden + cell) |
| Parameters | Ít hơn ~25% | Nhiều hơn |
| Performance | Tương đương | Đôi khi tốt hơn sequences rất dài |
| Training speed | Nhanh hơn | Chậm hơn |
| Phù hợp | Smaller datasets, faster training | Long sequences, larger data |

### Famous CNN Architectures

| Model | Năm | Top-1 Acc (ImageNet) | Params | Đặc điểm |
|-------|-----|---------------------|--------|----------|
| AlexNet | 2012 | 63.3% | 60M | First deep CNN thắng ImageNet |
| VGG-16 | 2014 | 74.4% | 138M | Đơn giản, deep, 3×3 conv |
| ResNet-50 | 2015 | 76.1% | 25M | Skip connections, rất deep |
| EfficientNet-B0 | 2019 | 77.1% | 5.3M | Compound scaling, efficient |
| Vision Transformer (ViT) | 2020 | 81.8% | 86M | Transformer cho image |

---

## Code mẫu

```python
# ============================================================
# PHẦN 1: CNN với PyTorch cho Image Classification
# ============================================================
import torch
import torch.nn as nn
import torch.optim as optim
from torchvision import datasets, transforms, models
from torch.utils.data import DataLoader

# Transform với Data Augmentation
train_transform = transforms.Compose([
    transforms.RandomHorizontalFlip(),
    transforms.RandomRotation(10),
    transforms.ColorJitter(brightness=0.2, contrast=0.2),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],   # ImageNet stats
                         std=[0.229, 0.224, 0.225])
])

val_transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                         std=[0.229, 0.224, 0.225])
])

# Simple CNN từ scratch
class SimpleCNN(nn.Module):
    def __init__(self, n_classes=10):
        super().__init__()
        # Block 1: Conv → BatchNorm → ReLU → MaxPool
        self.conv1 = nn.Sequential(
            nn.Conv2d(in_channels=3, out_channels=32, kernel_size=3, padding=1),
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2)  # Output: 32 x H/2 x W/2
        )
        # Block 2
        self.conv2 = nn.Sequential(
            nn.Conv2d(32, 64, 3, padding=1),
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.MaxPool2d(2, 2)  # Output: 64 x H/4 x W/4
        )
        # Block 3
        self.conv3 = nn.Sequential(
            nn.Conv2d(64, 128, 3, padding=1),
            nn.BatchNorm2d(128),
            nn.ReLU(),
            nn.AdaptiveAvgPool2d(4)  # Output: 128 x 4 x 4 bất kể input size
        )
        # Classifier
        self.classifier = nn.Sequential(
            nn.Flatten(),
            nn.Linear(128 * 4 * 4, 512),
            nn.ReLU(),
            nn.Dropout(0.5),
            nn.Linear(512, n_classes)
        )

    def forward(self, x):
        x = self.conv1(x)
        x = self.conv2(x)
        x = self.conv3(x)
        x = self.classifier(x)
        return x


# Transfer Learning với ResNet50
def create_transfer_model(n_classes, mode='fine_tune'):
    # Load pretrained ResNet50
    model = models.resnet50(pretrained=True)

    if mode == 'feature_extraction':
        # Freeze tất cả layers
        for param in model.parameters():
            param.requires_grad = False
    elif mode == 'fine_tune':
        # Chỉ freeze early layers (conv1 và layer1)
        for name, param in model.named_parameters():
            if 'conv1' in name or 'layer1' in name:
                param.requires_grad = False

    # Thay FC head cho task mới
    in_features = model.fc.in_features  # 2048 cho ResNet50
    model.fc = nn.Sequential(
        nn.Dropout(0.4),
        nn.Linear(in_features, n_classes)
    )
    return model


# Training loop chuẩn
def train_epoch(model, loader, criterion, optimizer, device):
    model.train()
    total_loss, correct = 0, 0
    for X_batch, y_batch in loader:
        X_batch, y_batch = X_batch.to(device), y_batch.to(device)
        optimizer.zero_grad()
        outputs = model(X_batch)
        loss = criterion(outputs, y_batch)
        loss.backward()
        optimizer.step()
        total_loss += loss.item()
        correct += (outputs.argmax(1) == y_batch).sum().item()
    return total_loss/len(loader), correct/len(loader.dataset)


# ============================================================
# PHẦN 2: LSTM cho Sequence Classification
# ============================================================
class LSTMClassifier(nn.Module):
    def __init__(self, input_size, hidden_size, n_layers, n_classes, dropout=0.3):
        super().__init__()
        self.lstm = nn.LSTM(
            input_size=input_size,
            hidden_size=hidden_size,
            num_layers=n_layers,
            batch_first=True,         # Input shape: (batch, seq, features)
            dropout=dropout if n_layers > 1 else 0,
            bidirectional=True        # BiLSTM: đọc cả chiều xuôi và ngược
        )
        self.dropout = nn.Dropout(dropout)
        # bidirectional=True → hidden_size * 2
        self.fc = nn.Linear(hidden_size * 2, n_classes)

    def forward(self, x):
        # x: (batch, seq_len, input_size)
        lstm_out, (hn, cn) = self.lstm(x)
        # Dùng hidden state của timestep cuối (từ cả 2 directions)
        # hn shape: (n_layers * 2, batch, hidden_size)
        # Lấy layer cuối, cả 2 directions
        hn_last = torch.cat([hn[-2], hn[-1]], dim=1)  # (batch, hidden_size*2)
        out = self.dropout(hn_last)
        return self.fc(out)


# GRU (đơn giản hơn LSTM, thường hiệu quả tương đương)
class GRUClassifier(nn.Module):
    def __init__(self, input_size, hidden_size, n_layers, n_classes):
        super().__init__()
        self.gru = nn.GRU(input_size, hidden_size, n_layers, batch_first=True)
        self.fc = nn.Linear(hidden_size, n_classes)

    def forward(self, x):
        _, hn = self.gru(x)         # hn: (n_layers, batch, hidden_size)
        return self.fc(hn[-1])      # Dùng hidden state của layer cuối


# ============================================================
# PHẦN 3: Minh họa Convolution Operation
# ============================================================
import numpy as np

def conv2d_naive(X, W, stride=1, padding=0):
    """Convolution 2D từ scratch (for illustration)"""
    if padding > 0:
        X = np.pad(X, padding, mode='constant')
    H, W_in = X.shape
    Fh, Fw = W.shape
    H_out = (H - Fh) // stride + 1
    W_out = (W_in - Fw) // stride + 1
    output = np.zeros((H_out, W_out))
    for i in range(H_out):
        for j in range(W_out):
            region = X[i*stride:i*stride+Fh, j*stride:j*stride+Fw]
            output[i, j] = np.sum(region * W)
    return output

# Ví dụ: edge detection filter
X = np.array([[1,2,3,4,5],
              [6,7,8,9,10],
              [1,2,3,4,5],
              [6,7,8,9,10],
              [1,2,3,4,5]], dtype=float)

# Sobel filter (phát hiện cạnh ngang)
sobel_h = np.array([[-1,-2,-1], [0,0,0], [1,2,1]])
feature_map = conv2d_naive(X, sobel_h, stride=1, padding=0)
print("Output feature map shape:", feature_map.shape)
# (3, 3) vì (5-3)/1 + 1 = 3
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**CNN — Dùng khi:**
- Image classification, object detection, image segmentation.
- Dữ liệu có cấu trúc không gian (spatial structure): images, video frames.
- Audio processing (spectrogram as 2D input).
- Dữ liệu có translation invariance (đặc trưng xuất hiện ở nhiều vị trí).

**CNN — Không dùng khi:**
- Dữ liệu tabular không có spatial structure.
- Cần positional encoding quan trọng (dùng Transformer).
- Dataset rất nhỏ (vài trăm images → dùng transfer learning hoặc traditional CV).

**RNN/LSTM — Dùng khi:**
- Sequential data: text, time series, speech, music.
- Cần nhớ long-range dependencies (LSTM/GRU).
- Language modeling, machine translation, sentiment analysis.

**RNN/LSTM — Không dùng khi:**
- Sequences rất dài (> vài nghìn tokens) → Transformer hiệu quả hơn.
- Cần parallelization khi training (RNN sequential → chậm trên GPU).
- Hiện tại: LSTM đang được Transformer thay thế trong NLP.

---

## So sánh CNN vs RNN vs Transformer

| Tiêu chí | CNN | RNN/LSTM | Transformer |
|----------|-----|----------|-------------|
| Dữ liệu phù hợp | Spatial (image) | Sequential (text, TS) | Cả hai |
| Parallelization | Cao | Thấp (sequential) | Cao |
| Long-range dep. | Hạn chế | LSTM: khá tốt | Rất tốt (attention) |
| Memory usage | Trung bình | Tỉ lệ với seq_len | Quadratic với seq_len |
| Translation inv. | Có | Không | Không (cần pos. enc.) |
| State-of-the-art | Image tasks | Một số TS tasks | NLP, multimodal |

---

## Lỗi thường gặp (Common Pitfalls)

**CNN:**
- **Sai output dimension**: không tính đúng `(H-F+2P)/S+1` → dimension mismatch.
- **Không dùng BatchNorm**: training unstable, slow convergence.
- **Quên data augmentation**: overfitting với dataset ảnh nhỏ.
- **Fine-tune learning rate quá lớn**: phá vỡ pretrained weights.
- **Dùng FC layers thay vì GAP**: nhiều parameters, dễ overfit.

**RNN/LSTM:**
- **Không dùng `batch_first=True`**: nhầm lẫn input shape (seq, batch, features) vs (batch, seq, features).
- **Vanishing gradient với plain RNN**: luôn dùng LSTM hoặc GRU cho sequences > 10.
- **Không pack padded sequences**: padding tokens ảnh hưởng hidden state (`pack_padded_sequence`).
- **Nhầm hn và output của LSTM**: `output` chứa hidden state mọi timestep, `hn` chỉ timestep cuối.

---

## Câu hỏi phỏng vấn hay gặp

- **Convolution khác với Fully Connected layer thế nào? Tại sao CNN efficient hơn?**
- **Weight sharing trong CNN mang lại lợi ích gì?** (translation invariance, ít params)
- **Max pooling vs Average pooling: khi nào dùng cái nào?**
- **Skip connection trong ResNet giải quyết vấn đề gì?**
- **Transfer learning: feature extraction vs fine-tuning: khi nào dùng cái nào?**
- **Vanishing gradient trong RNN xảy ra thế nào?**
- **LSTM giải quyết vanishing gradient bằng cách nào?** (cell state, gating)
- **Forget gate trong LSTM làm gì?**
- **GRU vs LSTM: khi nào chọn cái nào?**
- **Tại sao Transformer đang thay thế RNN trong NLP?** (parallelization, attention)
