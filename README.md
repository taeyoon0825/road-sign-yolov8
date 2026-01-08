# YOLOv8 Road Sign Detection (Kaggle)

## 1) 과제 개요
Kaggle **Road Sign Detection** 데이터셋을 활용해 **YOLOv8 기반 객체 탐지(Object Detection)** 모델을 학습하고, 학습된 모델로 도로 표지판 탐지 결과를 확인했습니다.

- 목표
  - YOLO 형식 데이터셋 구조 이해 (`images/labels`, `train/val`)
  - YOLOv8 모델 학습(train)
  - 학습 모델로 객체 탐지(predict) 및 결과 저장
  - Colab 실습 내용을 GitHub로 정리/공유

---

## 2) 데이터셋 정보
- Dataset: Kaggle `andrewmvd/road-sign-detection`
- 규모: **877 images / 4 classes**
- Annotation: **PASCAL VOC (XML)**

### Classes (4)
- `trafficlight`
- `stop`
- `speedlimit`
- `crosswalk`

---

## 3) 데이터셋 준비 (YOLO 형식 변환)
원본 데이터 구조(수동 정리):
data/
├─ images/ (877 images)
└─ annotations/ (877 xml)

markdown
코드 복사

### YOLO 학습용 구조로 변환
- VOC(XML) → YOLO(txt) 변환
- Split: `train/val = 8:2` (random seed = 42)

변환 후 구조:
road_sign_yolo/
├─ images/
│ ├─ train/
│ └─ val/
└─ labels/
├─ train/
└─ val/

makefile
코드 복사

### data.yaml
```yaml
path: /content/road_sign_yolo
train: images/train
val: images/val

nc: 4
names: [trafficlight, stop, speedlimit, crosswalk]
4) YOLOv8 학습 (Train)
Environment
Google Colab GPU: Tesla T4

Python: 3.12

PyTorch: 2.9.0 + CUDA

Model / Hyperparams
Model: yolov8n.pt

imgsz=640

epochs=50

batch=16

Train Command
bash
코드 복사
pip -q install ultralytics
yolo detect train data=/content/data.yaml model=yolov8n.pt epochs=50 imgsz=640 batch=16 project=/content/runs name=roadsign
학습 결과 저장 위치:

swift
코드 복사
/content/runs/detect/roadsign/
 ├─ results.png
 └─ weights/
    ├─ best.pt
    └─ last.pt
5) 객체 탐지 테스트 (Predict)
학습된 best.pt를 사용해 validation 이미지에 대해 탐지를 수행했습니다.

Predict Command
bash
코드 복사
yolo detect predict model=/content/runs/detect/roadsign/weights/best.pt source=/content/road_sign_yolo/images/val save=True conf=0.25 project=/content/runs name=pred_roadsign
결과 저장 위치:

swift
코드 복사
/content/runs/pred_roadsign/
실행 로그 예시(요약)
speedlimit, trafficlight, stop, crosswalk 객체가 정상적으로 탐지됨

일부 이미지는 (no detections)가 발생할 수 있으며 이는 confidence threshold, 표지판 크기/가림(occlusion), 조명 등의 영향을 받을 수 있음

6) 실행 방법 요약
Kaggle 데이터셋 다운로드

data/images, data/annotations 구조로 정리

VOC(XML) → YOLO(txt) 변환 및 train/val 분할

data.yaml 작성

YOLOv8 학습(train)

학습 모델로 예측(predict) 및 결과 저장

7) 레포 구조 (예시)
kotlin
코드 복사
.
├─ RoadSign_YOLOv8.ipynb
├─ data.yaml
└─ README.md
(선택) 학습/예측 결과(runs/)는 용량이 커질 수 있어 필요한 파일만 포함하거나 Git LFS를 사용하는 것을 권장합니다.

makefile
코드 복사
::contentReference[oaicite:0]{index=0}
