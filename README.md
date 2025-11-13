# Nail Detection 專案（使用 YOLOv8）

本專案使用 YOLOv8 建立指甲（nail）與手指（finger）偵測模型。  
資料集共約 5000 張圖片，涵蓋不同膚色、光線、角度與美甲樣式，以提升模型泛化能力。

GitHub 僅提供程式碼、設定檔與訓練結果，不包含完整資料集。

---

# 資料集說明

總圖片數：4986 張  
Train：3960 張  
Val：996 張  
標註格式：YOLO txt（class x_center y_center width height）

## 類別（Classes）
0 → nail  
1 → finger

## data.yaml 內容
train: dataset/images/train  
val: dataset/images/val  
nc: 2  
names: ['nail', 'finger']

---

# 訓練設定（Training Configuration）

模型：YOLOv8s（small）

## 訓練指令（100 epochs）

yolo detect train  
model=yolov8s.pt  
data=dataset/data.yaml  
epochs=100  
imgsz=640  
batch=8  
device=0  
name=nail_v3_s  
optimizer=AdamW  
lr0=0.005  
lrf=0.01  
weight_decay=0.0005  
mosaic=1.0  
mixup=0.1  
copy_paste=0.1  
close_mosaic=15  
hsv_h=0.015  
hsv_s=0.7  
hsv_v=0.4  
fliplr=0.5  
degrees=5  
translate=0.05  
scale=0.2  
shear=2.0

## 主要設定說明
- AdamW：更穩定的優化器  
- mosaic / mixup / copy_paste：強化資料增強  
- hsv：模擬不同光線  
- degrees / scale / shear：增加姿勢多樣性  
- close_mosaic=15：後半程提高框的穩定度  
- batch=8：適合 3060 Ti 8GB VRAM

---

# 訓練結果（100 Epochs）

以下圖片放在：
runs/detect/nail_v4_s/results.png

---

# 訓練結果分析

## 訓練 Loss（train）
- box / cls / dfl loss 持續下降  
- 訓練過程穩定、成功收斂  

## 驗證 Loss（val）
- 前期下降  
- 後期小幅上升 → 輕微過擬合  
- 屬正常現象  

## Precision 與 Recall
Precision：約 0.72 – 0.74  
Recall：約 0.76 – 0.80  

模型能正確偵測大部分指甲與手指，漏抓率低。

## mAP 指標
mAP50：約 0.75 – 0.78  
mAP50-95：約 0.28 – 0.30  

偵測能力佳，但框的精細度仍有提升空間（YOLOv8s 正常極限）。

---

# 性能總結

Precision：0.72–0.74  
Recall：0.76–0.80  
mAP50：0.76  
mAP50-95：0.29  

模型已達研究與實際應用可用水準。

---

# 未來改善方向

- 影像解析度提升（imgsz 800）  
- 使用更大模型（YOLOv8m 或 YOLOv8l）  
- 加入困難案例圖片（反光、背光、模糊、極端角度）  
- 改善標註精準度  
- 引入 Test-Time Augmentation（TTA）  

