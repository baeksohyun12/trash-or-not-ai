# AI를 이용한 분리수거 도우미 "버릴까말까"
제주대학교 기초웹개발론 5조 백소현 양문준 고지운 강영빈

## Notion
https://www.notion.so/2a68d2b0e964801ebdc4deaba36f461c?source=copy_link
- 이제까지 진행한 회의록과 피드백을 정리한 문서 허브
- 기능정의서와 API 명세서
- BE, FE GitHub 주소, Figma 링크
- 작업 트래커 등

**버릴까말까** 프로젝트와 관련된 모든 공유 문서가 있는 Notion 링크입니다.


🛠️ 버릴까 말까 - Backend API & AI System🚀 백엔드 API 서버 정보API 문서(Swagger): http://[서버-IP]:8000/docsAI 모델 가동 상태: 정상 (MobileNetV3 Large 기반)프로젝트 소개 (Backend)"버릴까 말까" 서비스의 핵심 두뇌 역할을 담당합니다. 업로드된 이미지를 실시간으로 분석하는 고성능 AI 추론 엔진을 탑재하고 있으며, 대용량 위치 데이터를 효율적으로 처리하여 사용자에게 가장 가까운 분리수거 정보를 제공합니다.주요 역할AI 기반 객체 분류: MobileNetV3 모델을 활용한 10종의 쓰레기 이미지 판별RESTful API 설계: 프론트엔드와 유연한 연동을 위한 FastAPI 기반 API 구축데이터 정합성 및 예외 처리: 이미지 규격 검증 및 서버 사이드 보안 로직 적용기술 스택 (Tech Stack)Backend & Server<img src="https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white"> <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white"> <img src="https://img.shields.io/badge/Uvicorn-4051B5?style=for-the-badge&logo=uvicorn&logoColor=white">AI Engine<img src="https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white"> <img src="https://img.shields.io/badge/Torchvision-000000?style=for-the-badge&logo=pytorch&logoColor=white"> <img src="https://img.shields.io/badge/Pillow-000000?style=for-the-badge">DevOps & Tools<img src="https://img.shields.io/badge/github-181717?style=for-the-badge&logo=github&logoColor=white"> <img src="https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white">핵심 백엔드 아키텍처1. 실시간 AI 추론 파이프라인 (MobileNetV3)사용자가 업로드한 고해상도 이미지를 서버 메모리에서 즉시 처리하여 분석 결과를 반환합니다.전처리 시스템: Resize(256) 및 CenterCrop(224)을 적용하여 스마트폰 사진의 비율 왜곡을 방지하고 분석 정확도를 극대화했습니다.추론 최적화: eval() 모드와 torch.no_grad()를 사용하여 불필요한 연산을 제거하고 응답 속도를 향상시켰습니다.2. 강력한 이미지 유효성 검증악의적인 요청이나 대용량 파일로부터 서버를 보호하기 위해 다중 보안 계층을 구축했습니다.MIME Type 검사: ALLOWED_EXTENSIONS를 정의하여 JPG, PNG, WEBP 등 허용된 이미지 포맷만 통과시킵니다.Payload 제한: MAX_FILE_SIZE (10MB) 설정을 통해 서버 부하를 방지하고 안정적인 서비스를 보장합니다.API 명세 (API Specification)기능MethodPath설명이미지 분석POST/api/predictAI를 통한 쓰레기 분류 및 배출 팁 반환서버 상태GET/API 서버 가동 여부 확인[AI 분석 상세 응답 구조]AI 분석 시 프론트엔드 아이콘 매핑을 고려하여 Scrap → Can, PET → Plastic으로 데이터 변환 후 전달합니다.Response 예시:JSON{
  "category": "Can",
  "is_dirty": false,
  "message": "\n내용물을 비우고 헹군 뒤 찌그러뜨려 배출해주세요.",
  "confidence": 0.98
}
프로젝트 구조 (Backend)Plaintextapp/
├── core/
│   └── ai_model.py      # AI 모델 로드 및 추론 로직
├── schemas/
│   └── waste.py         # Pydantic 데이터 모델 (Response/Request)
├── model_data/
│   ├── best_waste_model.pth  # 학습된 AI 가중치 파일
│   └── classes.txt           # 분류 클래스 정보
└── main.py              # FastAPI 앱 설정 및 라우팅
설치 및 실행 방법환경 설정Python 3.9+ 환경이 필요합니다.필수 라이브러리 설치:Bashpip install fastapi uvicorn torch torchvision pillow python-multipart
서버 실행Bashuvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
도움이 되셨나요? 위 내용은 프론트엔드 문서의 구성과 디자인을 완벽하게 따라가면서도 백엔드만의 전문적인 로직(전처리, 데이터 모델링, 보안 검증)을 강조하고 있습니다.추가로 수정이 필요한 부분이나 강조하고 싶은 백엔드 기능이 있다면 말씀해 주세요!
