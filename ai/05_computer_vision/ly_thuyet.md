# Computer Vision

---

## Giải thích cho người mới hoàn toàn

Khi bạn nhìn vào bức ảnh một con mèo, não bạn ngay lập tức nhận ra: đây là con mèo, không phải chó, không phải người. Điều này có vẻ đơn giản với người, nhưng với máy tính — đó là một thách thức khổng lồ.

**Computer Vision** dạy máy tính "nhìn" và hiểu hình ảnh:
- **Phân loại (Classification)**: Bức ảnh này là mèo hay chó?
- **Phát hiện (Detection)**: Con mèo đang ở VỊ TRÍ NÀO trong ảnh? (vẽ bounding box)
- **Phân vùng (Segmentation)**: Đâu là từng pixel thuộc về con mèo?

Cách máy tính "nhìn": Ảnh = ma trận số. Ảnh màu RGB 1920×1080 = 3 × 1920 × 1080 = 6,220,800 số. Mạng neural CNN học cách trích xuất đặc trưng từ những con số đó.

---

## Giải thích cho người đã biết lập trình (nâng cao)

### Image Representation

**Tensor shape**: Trong PyTorch dùng (C, H, W) — Channel, Height, Width. Trong TensorFlow: (H, W, C).
- **Grayscale**: (1, H, W), giá trị 0-255 (uint8) hoặc 0.0-1.0 (float32 normalized).
- **RGB**: (3, H, W) — 3 channels: Red, Green, Blue.
- **Batch**: (N, C, H, W) — N images cùng lúc.

**Normalization**: `img = (img - mean) / std` per channel. ImageNet mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225].

### Convolution Operation

**Filter/Kernel**: Ma trận nhỏ (3×3, 5×5, 7×7) được học trong training.

```
Output feature map size:
H_out = floor((H_in + 2*padding - kernel_size) / stride) + 1
W_out = floor((W_in + 2*padding - kernel_size) / stride) + 1
```

**Tại sao CNN hoạt động?**
- **Local connectivity**: Mỗi neuron chỉ kết nối với vùng nhỏ (receptive field) — phù hợp với local patterns.
- **Weight sharing**: Cùng filter áp dụng toàn ảnh → số params giảm drastically.
- **Translation equivariance**: Con mèo ở góc trái hay góc phải đều detect được.
- **Hierarchical features**: Layer đầu học edges/colors, layer sau học shapes/objects.

**Depthwise Separable Convolution** (MobileNet): Tách convolution thành depthwise (per-channel spatial) + pointwise (1×1, cross-channel mixing) → giảm FLOPs ~8-9×.

### Object Detection Architectures

**Two-stage detectors (Faster R-CNN)**:
1. **Region Proposal Network (RPN)**: Đề xuất ~300 regions of interest (RoI) có thể chứa object.
2. **RoI Pooling**: Normalize RoI về fixed size.
3. **Detection Head**: Classify + refine bounding box cho mỗi RoI.
- Slower but more accurate, tốt cho small objects.

**One-stage detectors (YOLO — You Only Look Once)**:
- Chia ảnh thành S×S grid. Mỗi cell dự đoán B bounding boxes + confidence + class probabilities.
- Single forward pass → rất nhanh (real-time).
- YOLO v8 là state-of-the-art hiện tại.

**YOLO output**: (S, S, B*(5 + C)) — 5 = (x, y, w, h, confidence), C = số class.

### Segmentation

- **Semantic segmentation**: Gán class label cho mỗi pixel. Mọi pixel "mèo" đều cùng label. (FCN, DeepLab, SegFormer).
- **Instance segmentation**: Phân biệt từng instance. "Mèo 1" và "Mèo 2" có mask riêng. (Mask R-CNN).
- **Panoptic segmentation**: Kết hợp cả hai.

### Transfer Learning với ImageNet

**ImageNet**: 1.2M ảnh, 1000 classes — benchmark chuẩn của CV.

**Pretrained models**: VGG16, ResNet50, EfficientNet, ViT — đã học visual features từ ImageNet.

**Strategies**:
1. **Feature extraction**: Freeze toàn bộ convolution layers, chỉ train classification head mới. Dùng khi dataset nhỏ + similar to ImageNet.
2. **Fine-tuning**: Unfreeze một số top layers + train với learning rate nhỏ. Dùng khi dataset đủ lớn hoặc domain khác ImageNet.
3. **Full training**: Chỉ dùng ImageNet weights làm init. Cần dataset lớn.

**Rule of thumb**: Dataset nhỏ + similar domain → freeze nhiều. Dataset lớn + different domain → unfreeze nhiều.

### Feature Pyramid Network (FPN)

Vấn đề: Objects có nhiều kích thước khác nhau. Shallow layers giữ spatial detail nhưng semantic yếu; deep layers ngược lại.

FPN tạo **feature pyramid** bằng cách kết nối top-down pathway với lateral connections, tạo multi-scale feature maps với semantic strength ở mọi scale — giúp detect small objects tốt hơn.

---

## Định nghĩa chính xác

**Convolution**: Phép toán cross-correlation giữa input feature map và learnable kernel, tính tổng có trọng số của vùng cục bộ để tạo ra output feature map.

**Transfer Learning**: Sử dụng weights từ model được pretrain trên dataset lớn (ImageNet) làm khởi điểm cho task mới, thay vì random initialization.

**Object Detection**: Bài toán xác định vị trí (bounding boxes) và nhận dạng (class labels) của tất cả objects trong ảnh.

**Image Augmentation**: Kỹ thuật tăng cường tập dữ liệu bằng cách áp dụng transformations lên training images để tăng diversity và giảm overfitting.

---

## Bảng / Sơ đồ kỹ thuật

### Convolution: Ví dụ với 3×3 filter

```
Input (5×5):          Filter (3×3):        Output (3×3):
┌───────────────┐     ┌───────────┐         ┌───────────┐
│ 1  2  3  4  5 │     │  1  0 -1  │         │  ?  ?  ?  │
│ 6  7  8  9 10 │  *  │  2  0 -2  │    =    │  ?  ?  ?  │
│11 12 13 14 15 │     │  1  0 -1  │         │  ?  ?  ?  │
│16 17 18 19 20 │     └───────────┘         └───────────┘
│21 22 23 24 25 │
└───────────────┘
Output[0,0] = 1*1 + 2*0 + 3*(-1) + 6*2 + 7*0 + 8*(-2) + 11*1 + 12*0 + 13*(-1)
            = 1 + 0 - 3 + 12 + 0 - 16 + 11 + 0 - 13 = -8
stride=1, padding=0: output = (5-3)/1+1 = 3
```

### CNN Feature Hierarchy

```
Input Image
    ↓ Conv1 (3×3, 64 filters)
Edges & Color blobs
    ↓ Conv2 (3×3, 128 filters)
Textures & Corners
    ↓ Conv3 (3×3, 256 filters)
Parts (eyes, wheels, handles)
    ↓ Conv4 (3×3, 512 filters)
Object parts composition
    ↓ Global Average Pooling
Semantic feature vector
    ↓ Fully Connected
Class probabilities
```

### So sánh Object Detection Models

| Model | Type | Speed (FPS) | mAP (COCO) | Params | Use case |
|-------|------|-------------|------------|--------|----------|
| Faster R-CNN | 2-stage | ~5 | 37.9 | 28M | High accuracy |
| YOLOv5s | 1-stage | 140 | 37.4 | 7M | Real-time, mobile |
| YOLOv8n | 1-stage | 380 | 37.3 | 3.2M | Edge/embedded |
| YOLOv8x | 1-stage | 50 | 53.9 | 68M | Best accuracy 1-stage |
| DETR | Transformer | 28 | 42.0 | 41M | No NMS needed |
| ViT-Det | Transformer | ~15 | 51.6 | 100M+ | SOTA |

### Image Augmentation Types

| Augmentation | Mô tả | Khi nào dùng |
|-------------|-------|-------------|
| Horizontal Flip | Lật ngang | Hầu hết tasks (trừ text recognition) |
| Random Crop | Cắt ngẫu nhiên | Classification, detection |
| Color Jitter | Thay đổi brightness/contrast/saturation | Outdoor images |
| Gaussian Blur | Làm mờ nhẹ | Medical images, satellite |
| CutOut | Xóa ngẫu nhiên patch | Object classification |
| MixUp | Blend 2 images với alpha | Classification |
| CutMix | Cắt patch từ ảnh khác ghép vào | Detection, classification |
| Mosaic (YOLO) | Ghép 4 ảnh thành 1 | Small object detection |

---

## Code mẫu

```python
import numpy as np
from PIL import Image
import torchvision.transforms as T
import torchvision.transforms.functional as TF

# ===== Phần 1: Image Processing với PIL và numpy =====

def demonstrate_image_representation():
    """Minh họa cách máy tính biểu diễn ảnh."""
    # Tạo ảnh RGB 64x64 giả lập
    img_array = np.random.randint(0, 256, (64, 64, 3), dtype=np.uint8)
    img = Image.fromarray(img_array)
    
    print(f"Image size (PIL): {img.size}")       # (width, height)
    print(f"Image mode: {img.mode}")              # RGB
    
    # Chuyển sang numpy
    arr = np.array(img)
    print(f"Array shape (H, W, C): {arr.shape}") # (64, 64, 3)
    print(f"Dtype: {arr.dtype}")                  # uint8
    print(f"Value range: [{arr.min()}, {arr.max()}]")
    
    # Normalize về [0, 1]
    arr_float = arr.astype(np.float32) / 255.0
    
    # Chuyển sang PyTorch format (C, H, W)
    arr_chw = arr_float.transpose(2, 0, 1)
    print(f"PyTorch format (C, H, W): {arr_chw.shape}")  # (3, 64, 64)
    
    return arr_chw


# ===== Phần 2: Convolution từ scratch =====

def conv2d_naive(input_map: np.ndarray, kernel: np.ndarray,
                 stride: int = 1, padding: int = 0) -> np.ndarray:
    """
    2D Convolution (cross-correlation) từ scratch.
    
    Args:
        input_map: (H, W) 2D feature map
        kernel: (kH, kW) convolution filter
        stride: bước nhảy
        padding: zero-padding kích thước
    
    Returns:
        output: feature map sau convolution
    """
    H, W = input_map.shape
    kH, kW = kernel.shape
    
    # Zero padding
    if padding > 0:
        input_map = np.pad(input_map, padding, mode='constant', constant_values=0)
        H, W = input_map.shape
    
    # Tính output size
    H_out = (H - kH) // stride + 1
    W_out = (W - kW) // stride + 1
    
    output = np.zeros((H_out, W_out))
    
    # Slide kernel over input
    for i in range(H_out):
        for j in range(W_out):
            # Extract patch tương ứng với vị trí kernel
            patch = input_map[i*stride : i*stride+kH,
                              j*stride : j*stride+kW]
            # Element-wise multiply + sum = convolution
            output[i, j] = np.sum(patch * kernel)
    
    return output


# Demo convolution
input_map = np.array([
    [1, 2, 3, 4, 5],
    [6, 7, 8, 9, 10],
    [11, 12, 13, 14, 15],
    [16, 17, 18, 19, 20],
    [21, 22, 23, 24, 25],
], dtype=np.float32)

# Sobel filter — detect vertical edges
sobel_vertical = np.array([
    [1, 0, -1],
    [2, 0, -2],
    [1, 0, -1],
], dtype=np.float32)

edge_map = conv2d_naive(input_map, sobel_vertical)
print("Edge detection output:")
print(edge_map)
print(f"Output size: {edge_map.shape}")  # (3, 3)


# ===== Phần 3: torchvision Transforms Pipeline =====

def build_transform_pipeline(mode: str = 'train'):
    """
    Xây dựng augmentation pipeline cho training và validation.
    
    Args:
        mode: 'train' hoặc 'val'
    """
    if mode == 'train':
        transform = T.Compose([
            # 1. Random crop từ larger image
            T.RandomResizedCrop(224, scale=(0.8, 1.0)),
            
            # 2. Horizontal flip 50% xác suất
            T.RandomHorizontalFlip(p=0.5),
            
            # 3. Color jitter — thay đổi brightness, contrast, saturation, hue
            T.ColorJitter(
                brightness=0.2,   # ±20% brightness
                contrast=0.2,
                saturation=0.2,
                hue=0.1,
            ),
            
            # 4. Random rotation ±15 độ
            T.RandomRotation(degrees=15),
            
            # 5. Chuyển sang tensor [0, 1]
            T.ToTensor(),
            
            # 6. Normalize với ImageNet stats
            T.Normalize(
                mean=[0.485, 0.456, 0.406],
                std=[0.229, 0.224, 0.225]
            ),
        ])
    else:  # validation/test
        transform = T.Compose([
            # Resize về lớn hơn một chút
            T.Resize(256),
            # Center crop chính xác
            T.CenterCrop(224),
            T.ToTensor(),
            T.Normalize(mean=[0.485, 0.456, 0.406],
                       std=[0.229, 0.224, 0.225]),
        ])
    
    return transform


# ===== Phần 4: Transfer Learning với torchvision =====

def build_transfer_learning_model(num_classes: int, freeze_backbone: bool = True):
    """
    Xây dựng model transfer learning từ ResNet50 pretrained.
    
    Args:
        num_classes: Số class trong bài toán của bạn
        freeze_backbone: True = chỉ train classifier head
    """
    try:
        import torch
        import torch.nn as nn
        from torchvision.models import resnet50, ResNet50_Weights
        
        # Load pretrained model (ImageNet weights)
        model = resnet50(weights=ResNet50_Weights.IMAGENET1K_V2)
        
        if freeze_backbone:
            # Freeze toàn bộ backbone
            for param in model.parameters():
                param.requires_grad = False
            print("Backbone frozen — chỉ train classification head")
        else:
            # Fine-tune toàn bộ, nhưng dùng lr nhỏ hơn cho backbone
            print("Full fine-tuning mode")
        
        # Thay thế classification head
        # ResNet50 fc layer: Linear(2048, 1000)
        in_features = model.fc.in_features  # 2048
        model.fc = nn.Sequential(
            nn.Dropout(0.5),
            nn.Linear(in_features, 512),
            nn.ReLU(),
            nn.Dropout(0.3),
            nn.Linear(512, num_classes),
        )
        
        # Count trainable parameters
        trainable = sum(p.numel() for p in model.parameters() if p.requires_grad)
        total = sum(p.numel() for p in model.parameters())
        print(f"Trainable params: {trainable:,} / {total:,} ({100*trainable/total:.1f}%)")
        
        return model
        
    except ImportError:
        print("Install PyTorch: pip install torch torchvision")


# ===== Main Demo =====
if __name__ == "__main__":
    print("=== Image Representation ===")
    img_tensor = demonstrate_image_representation()
    
    print("\n=== Convolution Demo ===")
    # Đã chạy ở trên
    
    print("\n=== Transform Pipeline ===")
    train_transform = build_transform_pipeline('train')
    val_transform = build_transform_pipeline('val')
    print(f"Train transforms: {len(train_transform.transforms)} steps")
    print(f"Val transforms: {len(val_transform.transforms)} steps")
    
    print("\n=== Transfer Learning Model ===")
    model = build_transfer_learning_model(num_classes=10, freeze_backbone=True)
```

---

## Khi nào dùng / Khi nào KHÔNG dùng

**Dùng khi:**
- **CNN**: Image classification, detection, segmentation — bất kỳ task nào với spatial data.
- **Transfer Learning**: Dataset nhỏ (<50k ảnh) — gần như luôn tốt hơn train from scratch.
- **YOLO**: Real-time detection, video processing, edge devices.
- **Faster R-CNN**: Khi accuracy quan trọng hơn speed, small objects, crowded scenes.
- **FPN**: Khi cần detect objects ở nhiều scales khác nhau.

**Không dùng khi:**
- CNN với pure text data — dùng Transformer.
- Heavy augmentation với medical images nếu thay đổi semantic (flip có thể flip left/right organ).
- Pre-trained ImageNet weights cho radically different domains (satellite, medical X-ray) mà không fine-tune — feature distributions quá khác.
- YOLOv1 cho small, densely packed objects — dùng YOLO v7/v8 hoặc Faster R-CNN.

---

## So sánh với các khái niệm liên quan

| | CNN | ViT (Vision Transformer) | Hybrid (ConvNext) |
|-|-----|--------------------------|-------------------|
| Inductive bias | Translation equivariance | None (position encoding) | Hybrid |
| Data efficiency | Cao (ít data hơn) | Thấp (cần nhiều data) | Trung bình |
| Global context | Chậm (stacking layers) | Nhanh (self-attention) | Tốt |
| Speed | Nhanh | Chậm hơn | Tương đương CNN |
| SOTA (2024) | Không | Có (ViT-G) | Cạnh tranh |

---

## Lỗi thường gặp (Common Pitfalls)

- **Data leakage trong augmentation**: Fit normalization stats trên toàn bộ dataset kể cả test set. Luôn tính mean/std chỉ từ training set.
- **Không resize về cùng kích thước**: Batch processing yêu cầu cùng kích thước. Thiếu resize sẽ lỗi.
- **Channel order nhầm lẫn**: PIL = (H, W, C) RGB; OpenCV = (H, W, C) BGR; PyTorch = (C, H, W). Nhầm BGR/RGB là lỗi rất phổ biến.
- **Normalize sau ToTensor và trước model**: Nếu không normalize → model predict không ổn định vì distribution shift so với pretraining.
- **Augment validation set**: Chỉ augment training set. Val/test set chỉ resize + normalize.
- **Class imbalance**: 1000 ảnh mèo, 10 ảnh chó → model chỉ đoán mèo. Dùng weighted loss hoặc oversampling.

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích convolution operation: stride, padding, output size formula.
- Tại sao CNN dùng weight sharing? Lợi ích là gì so với fully connected layer?
- Depthwise Separable Convolution là gì? Tại sao MobileNet dùng nó?
- One-stage vs Two-stage detection: trade-off?
- Khi nào freeze backbone, khi nào fine-tune toàn bộ trong transfer learning?
- Semantic segmentation vs Instance segmentation: sự khác biệt?
- Batch Normalization hoạt động như thế nào trong CNN?
- Giải thích FPN: tại sao cần multi-scale features?
