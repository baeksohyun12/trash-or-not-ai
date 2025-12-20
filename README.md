# ♻️ AI를 이용한 분리수거 도우미 "버릴까말까"

> **제주대학교 기초웹개발론 5조**<br>
> **팀원**: 백소현 양문준 고지운 강영빈

## 프로젝트 링크 및 서버 정보

프로젝트와 관련된 핵심 리소스 및 문서 링크입니다.

| 구분 | 링크 또는 상태 | 비고 |
| :--- | :--- | :--- |
| **Notion** | [trash-or-not-ai](https://www.notion.so/2a68d2b0e964801ebdc4deaba36f461c?source=copy_link) | **버릴까말까** 프로젝트와 관련된 모든 공유 문서 존재 <br> - 이제까지 진행한 회의록과 피드백을 정리한 문서 허브 <br> - 기능정의서와 API 명세서 <br> - BE, FE GitHub 주소, Figma 링크 <br> - 작업 트래커 등 |
| **API Docs** | [Swagger UI 바로가기](https://waste-api-6xd9.onrender.com/docs) | 실시간 API 테스트 가능 |
| **AI Status** | 정상 가동 중 | MobileNetV3 Large 기반 |


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

