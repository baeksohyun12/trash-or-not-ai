# ♻️ AI를 이용한 분리수거 도우미 "버릴까말까"

> **제주대학교 기초웹개발론 5조**<br>
> **팀원**: 백소현(PM, UI/UX) 양문준(BE) 고지운(FE) 강영빈(FE)

## 목차
1. [프로젝트 링크 및 서버 정보](#프로젝트-링크-및-서버-정보)
2. [프로젝트 소개(Backend)](#프로젝트-소개-backend)
3. [핵심 백엔드 아키텍처](#핵심-백엔드-아키텍처)
4. [핵심 프론트엔드 아키텍처](#핵심-프론트엔드-아키텍처)
5. [프로젝트 구조(Frontend) 및 설치 및 실행 방법](#프로젝트-구조frontend)
6. [프로젝트 구조(Backend) 및 설치 및 실행 방법](#프로젝트-구조-backend)
7. [환경정보](#환경-정보)

## 프로젝트 링크 및 서버 정보

프로젝트와 관련된 핵심 리소스 및 문서 링크입니다.

| 구분 | 링크 또는 상태 | 비고 |
| :--- | :--- | :--- |
| **Notion** | [trash-or-not-ai](https://www.notion.so/2a68d2b0e964801ebdc4deaba36f461c?source=copy_link) | **버릴까말까** 프로젝트와 관련된 모든 공유 문서 존재 <br> - 이제까지 진행한 회의록과 피드백을 정리한 문서 허브 <br> - 기능정의서와 API 명세서 <br> - BE, FE GitHub 주소, Figma 링크 <br> - 작업 트래커 등 |
| **API Docs** | [Swagger UI 바로가기](https://waste-api-6xd9.onrender.com/docs) | 실시간 API 테스트 가능 |
| **AI Status** | 정상 가동 중 | MobileNetV3 Large 기반 |
| **프론트 배포 URL** | [웹사이트 접속](https://0bini.github.io/my-recycling-project/main.html) | **GitHub Pages 배포** <br> - 실제 사용자가 이용하는 서비스 메인 화면 <br> - 모바일/PC 환경 접속 가능 |
| **백엔드 배포 URL** | [서버 호스트](https://waste-api-6xd9.onrender.com) | **Render 클라우드 배포** <br> - API 서버의 기본 호스트 주소 <br> - 서버 가동 상태 확인용 |
|**Figma**|[trash-or-not-Figma](https://www.figma.com/files/team/1568607282944043296/project/493662203/Team-project?fuid=1433665484561556956)|- 브레인스토밍 <br>- 와이어프레임<br>- 디자인, 프로토타입<br>- PT 자료, 일정표<br>- 디자인 시스템, 라이선스 정리

## 프로젝트 소개 (Backend)

 **버릴까 말까** 서비스의 핵심 두뇌 역할을 담당합니다. 업로드된 이미지를 실시간으로 분석하는 고성능 AI 추론 엔진을 탑재하고 있으며, 대용량 위치 데이터를 효율적으로 처리하여 사용자에게 가장 가까운 분리수거 정보를 제공합니다.

* 주요 역할
AI 기반 객체 분류: MobileNetV3 모델을 활용한 10종의 쓰레기 이미지 판별

* RESTful API 설계: 프론트엔드와 유연한 연동을 위한 FastAPI 기반 API 구축

* 데이터 정합성 및 예외 처리: 이미지 규격 검증 및 서버 사이드 보안 로직 적용

### 기술 스택 (Tech Stack)
**Backend & Server** <br><br>
<img src="https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white"> <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white"> <img src="https://img.shields.io/badge/Uvicorn-4051B5?style=for-the-badge&logo=uvicorn&logoColor=white">

**AI Engine** <br><br>
<img src="https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white"> <img src="https://img.shields.io/badge/Torchvision-000000?style=for-the-badge&logo=pytorch&logoColor=white"> <img src="https://img.shields.io/badge/Pillow-000000?style=for-the-badge">

**DevOps & Tools** <br><br>
<img src="https://img.shields.io/badge/github-181717?style=for-the-badge&logo=github&logoColor=white"> <img src="https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white">

## 핵심 백엔드 아키텍처

### 1. 실시간 AI 추론 파이프라인 (MobileNetV3)

사용자가 업로드한 고해상도 이미지를 서버 메모리에서 즉시 처리하여 분석 결과를 반환합니다.

* **전처리 시스템**: Resize(256) 및 CenterCrop(224)을 적용하여 스마트폰 사진의 비율 왜곡을 방지하고 분석 정확도를 극대화했습니다.

* **추론 최적화**: eval() 모드와 torch.no_grad()를 사용하여 불필요한 연산을 제거하고 응답 속도를 향상시켰습니다.

### 2. 강력한 이미지 유효성 검증

악의적인 요청이나 대용량 파일로부터 서버를 보호하기 위해 다중 보안 계층을 구축했습니다.

* **MIME Type 검사**: ALLOWED_EXTENSIONS를 정의하여 JPG, PNG, WEBP 등 허용된 이미지 포맷만 통과시킵니다.

* **Payload 제한**: MAX_FILE_SIZE (10MB) 설정을 통해 서버 부하를 방지하고 안정적인 서비스를 보장합니다.

* API 명세 (API Specification)기능MethodPath설명이미지 분석POST/api/predictAI를 통한 쓰레기 분류 및 배출 팁 반환서버 상태GET/API 서버 가동 여부 확인

## AI 분석 상세 응답 구조
AI 분석 시 프론트엔드 아이콘 매핑을 고려하여 Scrap → Can, PET → Plastic으로 데이터 변환 후 전달합니다.

Response 예시:

```json
{
  "category": "Can",
  "is_dirty": false,
  "message": "\n내용물을 비우고 헹군 뒤 찌그러뜨려 배출해주세요.",
  "confidence": 0.98
}
```

## 핵심 프론트엔드 아키텍처
### 1. 인터랙티브 UI 및 물리 엔진 (Three.js & Matter.js)
시각적 몰입감과 게임 요소를 결합한 사용자 경험 중심의 엔진 구조입니다.
* **3D 로딩 파이프라인**: Three.js 기반의 지구 자전 애니메이션을 통해 AI 분석 대기 시간의 이탈률을 최소화했습니다.
* **물리 기반 병합 알고리즘**: Matter.js를 활용하여 쓰레기 아이콘 간의 충돌 감지 및 상위 단계 오브젝트로의 실시간 변환을 구현했습니다.

### 2. 위치 데이터 최적화 및 스마트 캐싱 (TTL & Multi-sampling)
정확한 위치 서비스 제공과 네트워크 비용 절감을 위한 최적화 레이어입니다.
* **고정밀 위치 보정**: 단발성 측정이 아닌 Geolocation API 3회 샘플링을 통해 도심 오차 범위를 혁신적으로 줄였습니다.
* **TTL 기반 CSV 캐싱**: 5,000여 개의 클린하우스 데이터 로드 시 TTL(Time-To-Live) 메모리 캐싱을 적용해 지도 로딩 속도를 개선했습니다.

### 3. 안정적인 API 통신 및 데이터 무결성 (Fail-over)
백엔드 연동 시 발생할 수 있는 장애 상황에 대비한 방어적 설계입니다.
* **Fail-over 메커니즘**: 백엔드 서버 장애 시 즉시 Mock Data로 전환되는 무중단 서비스 아키텍처를 구축했습니다.
* **선제적 페이로드 검증**: 전송 전 단계에서 MIME Type 및 10MB 크기 제한을 수행하여 불필요한 서버 트래픽을 차단했습니다.


## 페이지별 스크린 샷
| 1. 시작 페이지 (Start) | 2. 사진 업로드 (Upload) | 3. AI 분석 중 (Loading) | 4. 분석 결과 (Result) |
| :---: | :---: | :---: | :---: |
| <img src="output\FE\img\1. 메인화면.png" width="150" /> | <img src="output\FE\img\2. 이미지업로드.png" width="150" /> | <img src="output\FE\img\3. 로딩화면.png" width="150" /> | <img src="output\FE\img\4. 이미지분류결과.png" width="150" /> |



| 5. 미니 게임 | 6. 미니 게임 결과 | 7. 요일별 배출 모달 | 
| :---: | :---: | :---: |
| <img src="output\FE\img\5. 게임화면.png" width="150" /> | <img src="output\FE\img\6. 게임결과.png" width="150" /> | <img src="output\FE\img\7. 요일별배출모달.png" width="150" /> |


## 프로젝트 구조(Frontend)

```text
my-recycling-project 
├── css/                        
│ ├── ai.css                    # AI 가이드 화면 전용 스타일 
│ └── main.css                  # 메인 화면 및 공통 스타일 
├── data/                       
│ └── jeju_cleanhouse.csv       # 제주도 클린하우스(쓰레기 배출 장소) 위치 데이터 
├── img/                        # 이미지 리소스
├── js/                         
│ ├── ai/                       # AI 이미지 분석 기능 
│ │ └── api.js                  # AI 모델 API 통신 및 데이터 처리 
│ ├── map/                      # 지도 서비스 기능 
│ │ ├── api.js                  # 지도 API 호출 및 설정 
│ │ └── map.js                  # 지도 렌더링 및 마커 표시 로직 
│ ├── game.js                   # 미니게임 핵심 로직
│ └── main.js                   # 웹사이트 메인 기능 및 이벤트 제어 
├── main.html                   # 웹 애플리케이션 진입점 (실행 파일) 
└── README.md                   # 프로젝트 설명서
```

## 설치 및 실행 방법
이 프로젝트는 AI 이미지 분석과 제주 클린하우스 데이터 로딩을 위해 로컬 서버 환경이 필요합니다. <br>
단순히 HTML 파일을 더블 클릭하여 실행할 경우 브라우저 보안 정책으로 인해 기능이 정상 작돟하지 않습니다.

**VS Code(Visual Studio Code)** 환경에서의 실행을 권장합니다.

### 프로젝트 다운로드 
터미널(Git bash 등)을 열고 아래 명령어를 입력하여 프로젝트를 내려받습니다.
```bash
git clone [https://github.com/0bini/my-recycling-project.git](https://0bini.github.io/my-recycling-project/main.html)
```

##  프로젝트 구조 (Backend)

```
app/                        # 백엔드 애플리케이션 메인 패키지
├── core/                   # 핵심 비즈니스 로직 및 AI 엔진
│   ├── __init__.py
│   └── ai_model.py         # MobileNetV3 기반 AI 모델 추론 클래스
├── schemas/                # 데이터 모델 및 유효성 검사 정의
│   ├── __init__.py
│   └── waste.py            # AI 분석 응답용 Pydantic 모델
├── __init__.py             # 패키지 초기화 파일
├── main.py                 # FastAPI 앱 객체 생성 및 라우팅
├── model_data/                 # AI 모델 실행에 필요한 리소스 파일
│   ├── best_waste_model.pth    # 학습 완료된 AI 가중치(Weights) 파일
│   └── classes.txt             # AI가 분류하는 10종의 클래스 정보
└── requirements.txt            # 프로젝트 실행을 위한 라이브러리 목록
```

## 설치 및 실행 방법

* 환경 설정: **Python 3.9+** 환경이 필요합니다.

* 필수 라이브러리 설치: **Bash**

  ```bash
  pip install fastapi uvicorn torch torchvision pillow python-multipart
  ```
* 서버 실행

  ```bash
  uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
  ```

## 환경 정보

### 🛠 Backend Environment & Requirements
백엔드 서버 가동 및 AI 모델 추론을 위해 아래의 환경 설정이 필요합니다.
* 환경 설정: **Python 3.9+** 환경이 필요합니다.
  
* 서버 실행

  ```bash
  uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
  ```

1. 소프트웨어 사양 (Software Requirements)
Language: Python 3.9 이상 (MobileNetV3 및 PyTorch 호환성 권장 버전)

* Framework: FastAPI

* ASGI Server: Uvicorn

* AI Engine: PyTorch, Torchvision

2. 필수 라이브러리 (Dependencies)
프로젝트 루트의 requirements.txt를 통해 아래 라이브러리들이 설치되어야 합니다.

  ```bash
  pip install fastapi uvicorn torch torchvision pillow python-multipart
  ```

3. 모델 데이터 경로 설정 (Path Configuration)
AI 모델이 정상적으로 로드되려면 프로젝트 구조 내에 아래 파일들이 지정된 위치에 있어야 합니다.

* 가중치 파일: model_data/best_waste_model.pth

* 클래스 정의: model_data/classes.txt

Note: ai_model.py 내부에서 os.path를 통해 자동 경로 탐색을 수행하지만, 실행 시 해당 폴더가 누락되지 않도록 주의해야 합니다.

4. 하드웨어 설정 (Hardware Setting)
Device: GPU(CUDA) 사용이 가능할 경우 자동으로 GPU를 활용하며, 불가할 경우 CPU로 추론을 수행합니다.

5. 주요 환경 변수 및 제약 조건
CORS: 현재 모든 Origin(*)에 대해 허용 설정되어 있습니다.

Max Upload Size: 최대 이미지 업로드 용량은 10MB로 제한되어 있습니다.

---

### 🛠 Frontend Environment & Requirements

* 배포 주소: https://0bini.github.io/my-recycling-project/main.html

* 주요 기술 스택: JavaScript (ES6+), HTML5, CSS3

* 사용 라이브러리 및 엔진:

  * Three.js: 3D 지구 로딩 애니메이션 구현

  * Matter.js: 미니게임 물리 엔진 및 충돌 로직 구현

* 연동 API:

  * Kakao Map API: 클린하우스 위치 표시 및 지도 서비스

  * Geolocation API: 사용자 실시간 위치 측정 및 다중 샘플링 보정

* 최적화 기법: TTL 기반 데이터 캐싱, Client-side 파일 유효성 검사, Fail-over 시스템

* 권장 실행 환경: WebGL 및 위치 정보 권한을 지원하는 최신 브라우저 (Chrome 권장)