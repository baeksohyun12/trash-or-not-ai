## 📄 [BE] API 명세서 (API Specification)

### 1. AI 이미지 분석 API
사용자가 업로드한 쓰레기 이미지를 분석하여 분리배출 방법과 카테고리를 반환합니다.

* **Endpoint:** `POST /api/predict`
* **Content-Type:** `multipart/form-data`
* **제한 사항:**
    * 최대 파일 크기: 10MB
    * 허용 확장자: `.jpg`, `.jpeg`, `.png`, `.webp`

#### **Response Body (Success)**

| 필드명 | 타입 | 설명 |
| :--- | :--- | :--- |
| **category** | String | 아이콘 표시용 카테고리 (Can, Plastic, Glass, Paper, Styrofoam, Vinyl, Food, General) |
| **is_dirty** | Boolean | 오염 여부 (현재 기본값 false 반환) |
| **message** | String | 상세 분리배출 가이드 메시지 (예: "내용물을 비우고 헹군 뒤...") |
| **confidence** | Float | AI 예측 신뢰도 (0.0 ~ 1.0) |

> **Note:** AI 모델이 분류하는 10종의 데이터 중 `Scrap`은 `Can`으로, `PET`는 `Plastic` 카테고리로 매핑되어 반환됩니다.

---

### 2. 에러 응답 코드 (Error Codes)

| 상태 코드 | 에러 메시지 (detail) | 발생 원인 |
| :--- | :--- | :--- |
| **400** | 지원하지 않는 파일 형식입니다. | 허용되지 않은 확장자 파일 업로드 시 |
| **413** | 파일 크기가 너무 큼 | 10MB 초과 파일 업로드 시 |
| **500** | 이미지 분석에 실패했습니다. | AI 모델 로드 실패 또는 서버 내부 추론 오류 시 |

---

### 3. 기타 API 정보
* **서버 상태 확인:** `GET /`
* **Swagger URL:** `https://waste-api-6xd9.onrender.com/docs`
