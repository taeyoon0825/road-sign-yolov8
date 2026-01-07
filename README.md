# Road Sign Detection with YOLOv8 (Kaggle)
1. Overview

Kaggle Road Sign Detection 데이터셋을 사용해 YOLOv8 기반 도로 표지판(Object Detection) 모델을 학습하고, 학습된 모델로 이미지 객체 탐지(predict) 결과를 확인했습니다.

Classes (4): trafficlight, stop, speedlimit, crosswalk

2. Dataset

Source: Kaggle andrewmvd/road-sign-detection

Total: 877 images / 4 classes

Annotation format: PASCAL VOC (XML)

Dataset 구조(원본):

data/
 ├─ images/        (877 images)
 └─ annotations/   (877 xml)

3. YOLO Format Conversion (images/labels)

VOC(XML) 어노테이션을 YOLO 라벨(txt) 형식으로 변환했고, 학습을 위해 다음 구조로 정리했습니다.

Split: train/val = 8:2 (random seed = 42)

변환 후 구조:

road_sign_yolo/
 ├─ images/
 │   ├─ train/
 │   └─ val/
 └─ labels/
     ├─ train/
     └─ val/


data.yaml:

path: /content/road_sign_yolo
train: images/train
val: images/val

nc: 4
names: [trafficlight, stop, speedlimit, crosswalk]

4. Training (YOLOv8)

Environment: Google Colab (GPU: Tesla T4)

Framework: Ultralytics YOLOv8

Model: yolov8n.pt

Img size: 640

Epochs: 50

Batch: 16

학습 명령:

yolo detect train data=/content/data.yaml model=yolov8n.pt epochs=50 imgsz=640 batch=16 project=/content/runs name=roadsign


학습 로그 및 지표(loss/metrics)는 runs/detect/roadsign/ 폴더에 저장됩니다.

5. Inference (Predict)

학습된 best weight로 validation 이미지에 대해 객체 탐지를 수행했습니다.

명령:

yolo detect predict model=/content/runs/detect/roadsign/weights/best.pt source=/content/road_sign_yolo/images/val save=True conf=0.25 project=/content/runs name=pred_roadsign


결과 저장 위치:

runs/pred_roadsign/


예측 로그 예시:

road107.png → speedlimit 1개 탐지

road272.png → trafficlight, stop, crosswalk 다중 탐지

일부 이미지는 (no detections) 발생 (conf threshold/학습량/표지판 크기 등에 영향)

6. Result Samples

아래는 예측 결과 샘플입니다. (박스/클래스 라벨 표시)


예시:

Trafficlight 탐지

Stop sign 탐지

Speedlimit 탐지

Crosswalk 탐지

(여기에 이미지 링크/캡처 첨부)

7. How to Run

Kaggle 데이터셋 다운로드

data/images, data/annotations 구성

VOC→YOLO 변환 후 road_sign_yolo 생성

data.yaml 작성

학습(train) 실행

예측(predict) 실행 및 결과 확인

📌 Repository Structure (추천)
.
├─ RoadSign_YOLOv8.ipynb
├─ data.yaml
├─ runs/
│  ├─ detect/roadsign/ (학습 결과)
│  └─ pred_roadsign/   (예측 결과 중 일부만)
└─ README.md
