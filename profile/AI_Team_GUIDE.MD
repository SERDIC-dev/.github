# SERDIC AI Team Development Guide

최종 업데이트: 2025.08.25.

작성자: Chansoo Park

---

SERDIC AI Team Development Guide

1. 예시이므로 아래 내용을 참조하여 프로젝트를 구성하면 됩니다.
2. 대용량 파일, 가중치, 이미지 파일들을 사용해야 하는 경우, NAS 폴더명을 명시해서 파일을 옮길 수 있도록 해야 합니다.
3. 직접 작성한 코드 외에 오픈소스 코드들도 아래 양식에 맞게 수정 할 필요는 없으나, 필요 시 수정 가능합니다.


## 📑 목차
1. [환경 설정](#환경-설정)
2. [Coding Convention and Style](#coding-convention-and-style)
3. [레포지토리 README 작성 양식](#레포지토리-readme-작성-양식)
4. [모델 구분 및 구조](#모델-구분-및-구조)

---

## ⚙️ 환경 설정

### 시스템 환경

| 항목 | 버전 | 비고 |
|------|------|------|
| 운영체제 | Ubuntu 20.04/22.04 | Windows WSL2 지원 |
| Python | 3.8+ | 3.9 권장 |
| CUDA | 11.8+ | RTX 30xx/40xx 시리즈 |
| cuDNN | 8.6+ | CUDA 버전과 호환 |
| Docker | 20.10+ | 컨테이너 환경 구축용 |

### Deep Learning Framework

| 프레임워크 | 버전 | 용도 |
|------------|------|------|
| PyTorch | 2.0+ | 메인 프레임워크 |
| TensorFlow | 2.10+ | Legacy 모델 지원 |
| OpenCV | 4.5+ | 이미지 처리 |
| ONNX | 1.14+ | 모델 최적화 |
| TensorRT | 8.5+ | 추론 가속화 |

### Conda 가상환경 설정

```bash
# 새 환경 생성
conda create -n ai_project python=3.9
conda activate ai_project

# PyTorch 설치 (CUDA 11.8)
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia

# 필수 패키지 설치
pip install opencv-python numpy pandas matplotlib seaborn
pip install ultralytics transformers accelerate
pip install tensorboard wandb

# 환경 내보내기
conda env export > environment.yml
```

### Docker 환경 설정

```dockerfile
FROM pytorch/pytorch:2.0.1-cuda11.7-cudnn8-devel

WORKDIR /workspace

# 시스템 패키지 설치
RUN apt-get update && apt-get install -y \
    libglib2.0-0 libsm6 libxext6 libxrender-dev libgomp1 \
    libgl1-mesa-glx libglib2.0-0

# Python 패키지 설치
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 환경 변수 설정
ENV PYTHONPATH=/workspace
ENV CUDA_VISIBLE_DEVICES=0
```

### 환경 변수 설정

```bash
# ~/.bashrc 또는 .env 파일에 추가
export CUDA_VISIBLE_DEVICES=0
export PYTHONPATH="${PYTHONPATH}:/path/to/project"
export WANDB_API_KEY="your_wandb_key"
export HF_HOME="/path/to/huggingface_cache"
```

---

## 📋 Coding Convention and Style

### 클래스 구조

```python
class Detector:
    """
    Description:
        AI Based Object Detection Module
        
    Attributes:
        model_path (str): 모델 파일 경로
        device (str): 추론 디바이스 (cpu/cuda)
        confidence (float): 신뢰도 임계값
        
    Methods:
        load_model: 모델 로드
        preprocess: 전처리
        inference: 추론
        postprocess: 후처리
        run: 전체 파이프라인 실행
        release: 리소스 해제
    """
    
    def __init__(self, model_path: str, device: str = "cuda", confidence: float = 0.5):
        """
        Detection 클래스 초기화
        
        Args:
            model_path (str): 모델 파일 경로
            device (str): 추론 디바이스
            confidence (float): 신뢰도 임계값
        """
        self.model_path = model_path
        self.device = device
        self.confidence = confidence
        self.model = None
        
    def load_model(self) -> None:
        """모델 로드 및 초기화"""
        try:
            # 모델 로드 로직
            self.model = torch.load(self.model_path)
            self.model.to(self.device)
            self.model.eval()
        except Exception as e:
            raise RuntimeError(f"Model loading failed: {e}")
    
    def preprocess(self, image: np.ndarray) -> torch.Tensor:
        """
        이미지 전처리
        
        Args:
            image (np.ndarray): 입력 이미지 (H, W, C)
            
        Returns:
            torch.Tensor: 전처리된 텐서 (1, C, H, W)
        """
        # 전처리 로직
        pass
    
    def inference(self, tensor: torch.Tensor) -> torch.Tensor:
        """
        모델 추론
        
        Args:
            tensor (torch.Tensor): 전처리된 입력 텐서
            
        Returns:
            torch.Tensor: 추론 결과
        """
        with torch.no_grad():
            outputs = self.model(tensor)
        return outputs
    
    def postprocess(self, outputs: torch.Tensor) -> List[Dict]:
        """
        추론 결과 후처리
        
        Args:
            outputs (torch.Tensor): 모델 출력
            
        Returns:
            List[Dict]: 검출 결과 리스트
        """
        # 후처리 로직
        pass
    
    def run(self, image: np.ndarray) -> List[Dict]:
        """
        전체 추론 파이프라인 실행
        
        Args:
            image (np.ndarray): 입력 이미지 (H, W, C)
            
        Returns:
            List[Dict]: 검출 결과
                - bbox: 바운딩 박스 좌표 [x1, y1, x2, y2]
                - confidence: 신뢰도 점수
                - class_id: 클래스 ID
                - class_name: 클래스 이름
        """
        tensor = self.preprocess(image)
        outputs = self.inference(tensor)
        results = self.postprocess(outputs)
        return results
    
    def release(self) -> None:
        """리소스 해제"""
        if self.model is not None:
            del self.model
            torch.cuda.empty_cache()

# 사용 예시
detector = Detector("models/yolo_v8n.pt", device="cuda", confidence=0.5)
detector.load_model()

# 추론 실행
image = cv2.imread("sample.jpg")
results = detector.run(image)

# 결과 출력
for result in results:
    print(f"Class: {result['class_name']}, Confidence: {result['confidence']:.2f}")
```

### 함수 및 변수 명명 규칙

```python
# 변수명: snake_case
model_path = "models/detector.pt"
batch_size = 32
learning_rate = 0.001

# 함수명: snake_case + 동사형
def load_model():
    pass

def preprocess_image():
    pass

def calculate_accuracy():
    pass

# 클래스명: PascalCase
class ObjectDetector:
    pass

class DataLoader:
    pass

# 상수: UPPER_CASE
MAX_BATCH_SIZE = 64
DEFAULT_CONFIDENCE = 0.5
MODEL_INPUT_SIZE = (640, 640)
```

### 타입 힌팅 규칙

```python
from typing import List, Dict, Tuple, Optional, Union
import numpy as np
import torch

def train_model(
    model: torch.nn.Module,
    dataloader: torch.utils.data.DataLoader,
    optimizer: torch.optim.Optimizer,
    epochs: int,
    device: str = "cuda"
) -> Tuple[float, float]:
    """
    모델 훈련 함수
    
    Args:
        model: 훈련할 모델
        dataloader: 데이터로더
        optimizer: 옵티마이저
        epochs: 훈련 에포크 수
        device: 디바이스
        
    Returns:
        Tuple[float, float]: (train_loss, train_accuracy)
    """
    pass
```

### 에러 처리 규칙

```python
import logging

# 로깅 설정
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class ModelError(Exception):
    """모델 관련 커스텀 예외"""
    pass

def load_model(model_path: str) -> torch.nn.Module:
    """안전한 모델 로드"""
    try:
        if not os.path.exists(model_path):
            raise FileNotFoundError(f"Model file not found: {model_path}")
            
        model = torch.load(model_path)
        logger.info(f"Model loaded successfully: {model_path}")
        return model
        
    except torch.serialization.UnicodeDecodeError:
        raise ModelError(f"Invalid model format: {model_path}")
    except Exception as e:
        logger.error(f"Unexpected error loading model: {e}")
        raise
```

---

## 📄 레포지토리 README 작성 양식

### 기본 구조

```markdown
# 프로젝트명

## 📋 개요
- 프로젝트 목적 및 설명
- 주요 기능

## 🔧 환경 설정
- Python 버전
- CUDA 버전  
- 필요 패키지

## 📊 모델 정보
- 베이스라인 모델
- 성능 지표
- 레퍼런스

## 🚀 사용법
- 설치 방법
- 실행 예시

## 📁 프로젝트 구조
```

### 베이스라인 모델 레퍼런스 작성법

```markdown
## 📊 베이스라인 모델

### YOLOv8 Object Detection
- **GitHub**: [ultralytics/ultralytics](https://github.com/ultralytics/ultralytics)
- **Paper**: [YOLOv8: A New Vision Transformer for Object Detection](https://arxiv.org/abs/2301.00331)
- **Model**: YOLOv8n/s/m/l/x variants
- **Performance**: mAP@0.5:0.95 = 37.3% (COCO dataset)

### SAM2 (Segment Anything Model 2)
- **GitHub**: [facebookresearch/segment-anything-2](https://github.com/facebookresearch/segment-anything-2)
- **Paper**: [SAM 2: Segment Anything in Images and Videos](https://arxiv.org/abs/2408.00714)
- **Model**: SAM2-B/L/H variants
- **Performance**: IoU@0.5 = 85.2% (SA-V dataset)

### Custom Fine-tuning
- **Base Model**: YOLOv8m
- **Dataset**: Custom industrial dataset (10K images)
- **Performance**: mAP@0.5 = 92.1% (validation set)
- **Training Details**: 100 epochs, AdamW optimizer, lr=0.001
```

---

## 🏗️ 모델 구분 및 구조

### 단순 추론 모델 (Inference Only)

```
project/
├── models/
│   ├── detector.pt          # 사전 훈련된 모델
│   └── config.yaml         # 모델 설정
├── src/
│   ├── inference.py        # 추론 모듈
│   ├── preprocess.py       # 전처리
│   └── postprocess.py      # 후처리
├── utils/
│   ├── visualize.py        # 시각화
│   └── metrics.py          # 평가 지표
├── examples/
│   └── demo.py            # 사용 예시
├── requirements.txt
├── README.md
└── run_inference.py       # 메인 실행 파일
```

#### 추론 전용 README 템플릿

```markdown
# AI Object Detection - Inference Only

## 🎯 모델 정보
- **Task**: Object Detection
- **Model**: YOLOv8m
- **Input Size**: 640x640
- **Classes**: 80 (COCO classes)

## ⚡ 빠른 시작
```bash
# 의존성 설치
pip install -r requirements.txt

# 단일 이미지 추론
python run_inference.py --image path/to/image.jpg

# 배치 추론
python run_inference.py --folder path/to/images/
```

## 📈 성능
- **mAP@0.5**: 65.2%
- **Inference Speed**: 45 FPS (RTX 3080)
- **Model Size**: 52MB


### Fine-tuning 지원 모델 (Train/Val/Eval)

```
project/
├── data/
│   ├── train/              # 훈련 데이터
│   ├── val/                # 검증 데이터
│   ├── test/               # 테스트 데이터
│   └── annotations/        # 어노테이션 파일
├── models/
│   ├── pretrained/         # 사전 훈련 모델
│   ├── checkpoints/        # 체크포인트
│   └── configs/            # 모델 설정 파일
├── src/
│   ├── train.py            # 훈련 스크립트
│   ├── validate.py         # 검증 스크립트
│   ├── inference.py        # 추론 스크립트
│   ├── dataset.py          # 데이터셋 클래스
│   └── model.py           # 모델 정의
├── utils/
│   ├── logger.py          # 로깅
│   ├── metrics.py         # 평가 지표
│   └── augmentation.py    # 데이터 증강
├── configs/
│   ├── train_config.yaml   # 훈련 설정
│   └── model_config.yaml   # 모델 설정
├── scripts/
│   ├── train.sh           # 훈련 스크립트
│   └── evaluate.sh        # 평가 스크립트
├── notebooks/
│   └── data_analysis.ipynb # 데이터 분석
├── requirements.txt
├── README.md
└── main.py                # 메인 실행 파일
```

#### Fine-tuning 지원 README 템플릿

```markdown
# AI Object Detection - Training & Inference

## 🎯 모델 정보
- **Task**: Custom Object Detection
- **Base Model**: YOLOv8m
- **Fine-tuning**: Supported
- **Classes**: Custom 5 classes

## 🔧 데이터 준비
```bash
# 데이터 구조
data/
├── train/
├── val/
└── annotations/
    ├── train.json
    └── val.json
```

## 🚀 훈련
```bash
# 기본 훈련
python src/train.py --config configs/train_config.yaml

# 멀티 GPU 훈련
python -m torch.distributed.launch --nproc_per_node=2 src/train.py
```

## 📊 평가
```bash
# 모델 평가
python src/validate.py --model models/checkpoints/best.pt --data data/val/

# 테스트 세트 추론
python src/inference.py --model models/checkpoints/best.pt --source data/test/
```

## 📈 성능
- **Training mAP@0.5**: 89.3%
- **Validation mAP@0.5**: 87.1%
- **Training Time**: 2 hours (RTX 3080)
```
