交通路障类 道路灾后障碍物检测数据集，19250张,yolo和voc两种标注
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ea3273f5b64246268ccda7108d19ae72.png)

yolo和voc两种标注方式
图像尺寸:480*640
类别数量:4类
训练集图像数量: 17583;
验证集图像数量:1221;
测试集图像数量:446
类别名称:每一类图像数，每一类标注数
. landslide -山体滑坡: 4432， 4821
fallen tree -倒伏树: 7530， 9477
stone -石头: 2645， 21770
road collapse -道路坍塌: 3963， 5613
image num: 19250
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1bb9030897fa4905933429016137f6f0.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8993360b71fc439284f0eed873b7a5c0.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4eca012c40674d888e25c56da6198fd4.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f8c004bea5d5470dadcd30c8ce0e1583.png)
 **交通道路灾害后障碍物检测数据集** 

---
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/12ca791af09e4d439872fe961b2e2724.png)

### 📊 数据集总体概览
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/92af96ae15c6401cb2746d9c828daa0d.png)

| 项目 | 内容 |
|------|------|
| **总图像数量** | 19,250 张 |
| **图像分辨率** | 480 × 640（高 × 宽） |
| **类别数量** | 4 类 |
| **标注格式** | 同时提供 **YOLO 格式**（`.txt`）和 **PASCAL VOC 格式**（`.xml`） |
| **数据划分** | - 训练集：17,583 张<br>- 验证集：1,221 张<br>- 测试集：446 张 |

---

### 🗂️ 类别分布统计表

| 类别 ID | 类别名称（英文）      | 类别名称（中文） | 图像数量（含该类的图像数） | 标注框总数（instances） | 平均每图目标数 |
|:-------:|------------------------|------------------|----------------------------|--------------------------|----------------|
| 0       | `landslide`            | 山体滑坡         | 4,432                      | 4,821                    | ≈1.09          |
| 1       | `fallen tree`          | 倒伏树           | 7,530                      | 9,477                    | ≈1.26          |
| 2       | `stone`                | 石头             | 2,645                      | 21,770                   | ≈8.23          |
| 3       | `road collapse`        | 道路坍塌         | 3,963                      | 5,613                    | ≈1.42          |
| —       | **总计（去重图像数）** | —                | **19,250**（各图像可含多类）| **41,681**               | —              |

> 🔍 注：
> - **图像数量 ≠ 总和**（因单张图像可能包含多个类别，如“石头+倒伏树”），但所有图像合计为 19,250 张。
> - “石头”类标注数远高于图像数，表明该类别常以**密集小目标**形式出现（如碎石堆）。

---

### 📁 数据组织建议（推荐结构）

```
road_disaster_dataset/
├── images/
│   ├── train/      # 17,583 张
│   ├── val/        # 1,221 张
│   └── test/       # 446 张
├── labels_yolo/    # YOLO 格式 (.txt)
│   ├── train/
│   ├── val/
│   └── test/
├── annotations_voc/ # VOC 格式 (.xml)
│   ├── train/
│   ├── val/
│   └── test/
└── dataset.yaml     # 用于 YOLOv8 训练
```

---

### 📄 `dataset.yaml` 示例（YOLOv8 使用）

```yaml
# dataset.yaml
train: ./images/train
val: ./images/val
test: ./images/test

nc: 4
names: ['landslide', 'fallen tree', 'stone', 'road collapse']
```

---

### ✅ 应用场景

- 智能交通系统中的 **道路异常自动巡检**
- 无人机/车载摄像头 **灾后快速评估**
- 高速公路养护 **自动化障碍物识别**
- 自动驾驶车辆的 **极端场景感知模块**

---

以下是**基于 YOLOv8 构建道路障碍物检测系统**的完整指南，涵盖数据准备、模型训练、推理部署、可视化界面及工程化建议。该系统专为 **道路灾害障碍检测（山体滑坡、倒伏树、石头、道路坍塌）** 设计，支持高精度、实时检测。

---

## 🧩 一、项目目标

- 检测 4 类道路障碍：`landslide`、`fallen tree`、`stone`、`road collapse`
- 支持图像、视频、摄像头、无人机/车载设备输入
- 提供训练 + 推理 + 可视化一体化解决方案
- 兼容 YOLO 格式（已提供 19,250 张标注数据）

---

## 📁 二、项目结构

```bash
road_obstacle_detection/
├── data/
│   ├── images/
│   │   ├── train/      # 17,583
│   │   ├── val/        # 1,221
│   │   └── test/       # 446
│   ├── labels/         # YOLO格式 .txt
│   │   ├── train/
│   │   ├── val/
│   │   └── test/
│   └── dataset.yaml    # 数据配置
├── weights/
│   └── yolov8s.pt      # 官方预训练权重（自动下载）
├── outputs/            # 推理结果保存目录
├── models/             # 训练输出模型（best.pt）
├── train.py            # 训练脚本
├── detect.py           # 推理核心
├── GUI.py              # PySide6 可视化界面（可选）
├── utils/
│   ├── convert_voc_to_yolo.py  # VOC转YOLO工具（如需）
│   └── visualize_labels.py     # 标注可视化
├── requirements.txt
└── README.md
```

---

## 📦 三、环境依赖（`requirements.txt`）

```txt
ultralytics==8.2.0
opencv-python==4.8.0
numpy==1.24.3
matplotlib==3.6.3
PySide6==6.6.0          # 如需GUI
scikit-image
tqdm
```

安装：
```bash
pip install -r requirements.txt
```

---

## 📄 四、数据配置文件 `dataset.yaml`

```yaml
# data/dataset.yaml
train: ../data/images/train
val: ../data/images/val
test: ../data/images/test

nc: 4
names: ['landslide', 'fallen tree', 'stone', 'road collapse']
```

> ✅ 确保路径正确，类别顺序与标注 `.txt` 中 class_id 一致（0~3）

---

## 🚀 五、模型训练（`train.py`）

```python
# train.py
from ultralytics import YOLO

def train_road_obstacle_detector():
    # 使用预训练模型（yolov8s 在精度和速度间平衡较好）
    model = YOLO("yolov8s.pt")  # 首次运行会自动下载

    # 开始训练
    results = model.train(
        data="data/dataset.yaml",
        imgsz=640,                # 输入尺寸（原始图 480x640，可保持或缩放）
        epochs=100,
        batch=32,                 # 根据 GPU 显存调整（如 16/32/64）
        device=0,                 # GPU ID；无 GPU 则设为 "cpu"
        name="road_obstacle_yolov8s",
        project="models",
        patience=30,              # 早停
        save=True,
        save_period=10,
        augment=True,             # 启用增强（对遮挡/光照鲁棒性关键）
        hsv_h=0.015,
        hsv_s=0.7,
        hsv_v=0.4,
        degrees=10.0,
        translate=0.1,
        scale=0.5,
        fliplr=0.5,
        mosaic=1.0,               # Mosaic 增强（提升小目标检测）
        mixup=0.1,
    )

    print(f"✅ 训练完成！最佳模型: {results.save_dir}/weights/best.pt")

if __name__ == "__main__":
    train_road_obstacle_detector()
```

> 💡 **训练建议**：
> - “石头”类目标密集且小 → 启用 `mosaic` 和 `scale` 增强
> - 若 GPU 显存不足，降低 `batch` 或使用 `yolov8n`
> - 使用 `yolov8m` 可进一步提升 mAP（适合服务器部署）

---

## 🔍 六、推理检测（`detect.py`）

```python
# detect.py
import cv2
import os
from ultralytics import YOLO

class RoadObstacleDetector:
    def __init__(self, model_path="models/road_obstacle_yolov8s/weights/best.pt"):
        self.model = YOLO(model_path)
        self.class_names = ['Landslide', 'Fallen Tree', 'Stone', 'Road Collapse']

    def detect_image(self, image_path, conf=0.3, save_dir="outputs"):
        results = self.model(image_path, conf=conf)
        annotated_img = results[0].plot()

        os.makedirs(save_dir, exist_ok=True)
        output_path = os.path.join(save_dir, os.path.basename(image_path))
        cv2.imwrite(output_path, annotated_img)
        print(f"✅ 图像检测完成 → {output_path}")
        return annotated_img

    def detect_video(self, video_path, conf=0.3, save_dir="outputs"):
        cap = cv2.VideoCapture(video_path)
        fps = int(cap.get(cv2.CAP_PROP_FPS))
        w = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
        h = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))

        os.makedirs(save_dir, exist_ok=True)
        out_path = os.path.join(save_dir, "detected_" + os.path.basename(video_path))
        out = cv2.VideoWriter(out_path, cv2.VideoWriter_fourcc(*'mp4v'), fps, (w, h))

        while True:
            ret, frame = cap.read()
            if not ret:
                break
            results = self.model(frame, conf=conf)
            annotated = results[0].plot()
            out.write(annotated)

        cap.release()
        out.release()
        print(f"✅ 视频检测完成 → {out_path}")

    def detect_camera(self, cam_id=0, conf=0.3):
        cap = cv2.VideoCapture(cam_id)
        while True:
            ret, frame = cap.read()
            if not ret:
                break
            results = self.model(frame, conf=conf)
            annotated = results[0].plot()
            cv2.imshow("Road Obstacle Detection", annotated)
            if cv2.waitKey(1) & 0xFF == ord('q'):
                break
        cap.release()
        cv2.destroyAllWindows()
```

---

## 🖥️ 七、可视化界面（可选，`GUI.py`）

> 可复用前文“行人跌倒检测”的 PySide6 界面，仅需替换：
> - 模型路径
> - 类别名称
> - 默认置信度（建议 0.3，因“石头”目标小）

---

## ▶️ 八、使用示例

### 1. 训练模型
```bash
python train.py
```

### 2. 检测单张图像
```python
from detect import RoadObstacleDetector
detector = RoadObstacleDetector()
detector.detect_image("test_images/road1.jpg")
```

### 3. 检测视频
```python
detector.detect_video("road_disaster.mp4")
```

### 4. 实时摄像头检测
```python
detector.detect_camera()
```

---

## 🛠️ 九、工程化部署建议

| 场景 | 方案 |
|------|------|
| **边缘设备**（Jetson） | 导出为 TensorRT 引擎：<br>`yolo export model=best.pt format=engine` |
| **Web API 服务** | 使用 FastAPI 封装推理接口 |
| **车载/无人机端** | 集成到 ROS 或 DJI SDK，实时回传告警 |
| **批量处理** | 编写脚本遍历 `test/` 目录生成检测报告 |

---


---

## 📎 附：评估模型性能（可选）

```python
# eval.py
from ultralytics import YOLO
model = YOLO("models/road_obstacle_yolov8s/weights/best.pt")
metrics = model.val(data="data/dataset.yaml")
print(metrics.box.map)  # mAP@0.5:0.95
```

---



 **智慧交通、应急救灾、自动驾驶感知** 等关键场景。
