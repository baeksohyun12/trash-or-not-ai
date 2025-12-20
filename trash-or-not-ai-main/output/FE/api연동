# API 연동 설명서

## 목차
1. [개요](#개요)
2. [API 연동 구조](#api-연동-구조)
3. [주요 API 연동 상세](#주요-api-연동-상세)
4. [에러 처리 및 예외 상황](#에러-처리-및-예외-상황)
5. [성능 최적화 기법](#성능-최적화-기법)

---

## 개요

본 프로젝트는 **"버릴까 말까"** - AI 탐지 기반 쓰레기 분리수거 시스템으로, 프론트엔드에서 다양한 API를 연동하여 사용자에게 분리수거 가이드를 제공합니다.

### 배포 정보
- **프론트엔드 배포 주소**: [https://0bini.github.io/my-recycling-project/main.html](https://0bini.github.io/my-recycling-project/main.html)

### 연동된 API 목록
1. **AI 이미지 분석 API** (백엔드 서버)
2. **클린하우스 데이터 API** (로컬 CSV 파일)
3. **카카오맵 API** (지도 서비스)
4. **Geolocation API** (브라우저 내장)

---

## API 연동 구조

### 전체 아키텍처

```
프론트엔드 (JavaScript)
├── js/ai/api.js          → AI 백엔드 API 통신
├── js/map/api.js         → 클린하우스 데이터 로드
├── js/map/map.js         → 카카오맵 API 연동
└── js/main.js            → 통합 제어 및 UI 렌더링
```

### 데이터 흐름도

```
사용자 이미지 업로드
    ↓
[클라이언트 검증] (파일 크기, 확장자)
    ↓
FormData 생성 및 전송
    ↓
AI 백엔드 API (/api/predict)
    ↓
응답 데이터 변환 및 UI 렌더링
```

---

## 주요 API 연동 상세

### 1. AI 이미지 분석 API

#### 1.1 기본 정보
- **서버 주소**: `https://waste-api-6xd9.onrender.com`
- **엔드포인트**: `/api/predict`
- **메서드**: `POST`
- **Content-Type**: `multipart/form-data`
- **파일 위치**: `js/ai/api.js`

#### 1.2 요청 구조

```javascript
// FormData 생성
const formData = new FormData();
formData.append('file', imageFile);

// Fetch API를 사용한 비동기 요청
const response = await fetch(`${API_BASE_URL}/api/predict`, {
    method: 'POST',
    body: formData,
});
```

**요청 파라미터:**
- `file` (File 객체): 업로드할 이미지 파일
  - 지원 형식: `.jpg`, `.jpeg`, `.png`, `.webp`
  - 최대 크기: 10MB

#### 1.3 응답 구조

**성공 응답 (200 OK):**
```json
{
    "category": "Plastic",
    "is_dirty": false,
    "message": "플라스틱은 재활용 가능합니다.",
    "confidence": 0.95
}
```

**응답 필드 설명:**
- `category`: 아이콘 표시용 카테고리 
  - 가능한 값: `Can`, `Plastic`, `Glass`, `Paper`, `Styrofoam`, `Vinyl`, `Food`, `General`
  - **Note**: AI 모델이 분류하는 10종의 데이터 중 `Scrap`은 `Can`으로, `PET`는 `Plastic` 카테고리로 매핑되어 반환됩니다.
- `is_dirty`: 오염 여부 (현재 기본값 false 반환)
- `message` : 상세 분리배출 가이드 메시지 (예: "내용물을 비우고 헹군 뒤...")
- `confidence` : AI 예측 신뢰도 (0.0 ~ 1.0)

#### 1.4 데이터 변환 로직

백엔드에서 받은 영문 카테고리를 한글로 변환하는 매핑 테이블:

```javascript
const categoryMap = {
    'Plastic': '플라스틱',
    'Can': '캔/고철류',
    'Glass': '병류',
    'Paper': '종이류',
    'Vinyl': '비닐류',
    'Styrofoam': '스티로폼',
    'General': '일반 쓰레기',
    'Food': '음식물',
};
```

**프론트엔드 반환 형식:**
```javascript
{
    type: "플라스틱",           // 한글 변환된 카테고리
    tip: "분리수거해주세요.",    // 안내 메시지
    is_dirty: false,            // 오염 여부
    confidence: 0.95,           // 신뢰도
    raw_category: "Plastic"      // 원본 카테고리 (디버깅용)
}
```

#### 1.5 구현 코드 위치

**파일**: `js/ai/api.js`

**주요 함수:**
- `analyzeImage(imageFile)`: 이미지 분석 요청
- `testConnection()`: 백엔드 서버 연결 테스트

---

### 2. 클린하우스 데이터 API

#### 2.1 기본 정보
- **데이터 소스**: 로컬 CSV 파일
- **파일 경로**: `data/jeju_cleanhouse.csv`
- **로드 방식**: Fetch API
- **파일 위치**: `js/map/api.js`

#### 2.2 데이터 구조

**CSV 파일 형식:**
```csv
읍면동 명,도로명 주소,단지 명,위도 좌표,경도 좌표,...
```

**파싱 후 JavaScript 객체:**
```javascript
{
    id: "csv-1",
    name: "클린하우스",
    address: "제주 제주시 아봉로 32",
    lat: 33.4996,
    lng: 126.5312,
    operatingHours: "15:00 - 04:00",
    distance: 500  // 사용자 위치 기준 거리 (미터)
}
```

#### 2.3 주요 함수

**`getNearbyCleanHouses(lat, lng, radius)`**
- **목적**: 사용자 위치 기준 반경 내 클린하우스 검색
- **파라미터**:
  - `lat` : 위도
  - `lng` : 경도
  - `radius` : 반경 (미터, 기본값: 5000m)
- **반환값**: Promise<Array> - 거리순으로 정렬된 클린하우스 배열 (최대 5개)

**`loadCsvCleanHouses()`**
- **목적**: CSV 파일을 비동기로 로드하고 파싱
- **반환값**: Promise<Array> - 모든 클린하우스 데이터 배열

#### 2.4 거리 계산 알고리즘

Haversine 공식을 사용하여 두 좌표 간 거리를 계산합니다:

```javascript
function calculateDistance(lat1, lng1, lat2, lng2) {
    const R = 6371000; // 지구 반지름 (미터)
    const dLat = (lat2 - lat1) * Math.PI / 180;
    const dLng = (lng2 - lng1) * Math.PI / 180;
    const a = 
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
        Math.sin(dLng / 2) * Math.sin(dLng / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    return R * c;
}
```

---

### 3. 카카오맵 API

#### 3.1 기본 정보
- **API 키**: `ff6a702c9f9b214063e669d5297d8046`
- **SDK URL**: `https://dapi.kakao.com/v2/maps/sdk.js`
- **라이브러리**: `services` 
- **파일 위치**: `main.html` (스크립트 로드), `js/map/map.js` (구현)

#### 3.2 지도 초기화

```javascript
// 카카오맵 SDK 로드 확인
if (typeof kakao === 'undefined' || !kakao.maps) {
    // 재시도 로직
}

// 지도 생성
const mapOption = {
    center: new kakao.maps.LatLng(lat, lng),
    level: 5  // 확대 레벨
};
const map = new kakao.maps.Map(mapContainer, mapOption);
```

#### 3.3 마커 표시

**현재 위치 마커:**
```javascript
const currentMarkerImage = new kakao.maps.MarkerImage(
    'https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/marker_red.png',
    new kakao.maps.Size(24, 35)
);

const currentMarker = new kakao.maps.Marker({
    position: new kakao.maps.LatLng(lat, lng),
    map: map,
    image: currentMarkerImage,
    title: '현재 위치'
});
```

**클린하우스 마커:**
```javascript
const marker = new kakao.maps.Marker({
    position: new kakao.maps.LatLng(house.lat, house.lng),
    map: map,
    title: house.name
});

// 마커 클릭 이벤트
kakao.maps.event.addListener(marker, 'click', () => {
    updateCleanHouseInfo(house);
});
```

---

### 4. Geolocation API

#### 4.1 기본 정보
- **API 타입**: 브라우저 내장 Web API
- **파일 위치**: `js/map/map.js`

#### 4.2 위치 정보 획득

**다중 샘플링 기법:**
정확도 향상을 위해 3회 측정 후 가장 정확한 좌표를 선택합니다.

```javascript
function getUserLocation() {
    let positions = [];
    let attempts = 0;
    const maxAttempts = 3;
    
    const getPosition = () => {
        navigator.geolocation.getCurrentPosition(
            (position) => {
                positions.push({
                    lat: position.coords.latitude,
                    lng: position.coords.longitude,
                    accuracy: position.coords.accuracy
                });
                
                attempts++;
                if (attempts < maxAttempts) {
                    setTimeout(getPosition, 1000); // 1초 후 재측정
                } else {
                    // 가장 정확한 위치 선택 (accuracy가 낮을수록 정확함)
                    const bestPosition = positions.reduce((best, current) => 
                        current.accuracy < best.accuracy ? current : best
                    );
                }
            },
            (error) => {
                // 에러 처리: 기본 좌표 사용
            },
            {
                enableHighAccuracy: true,  // GPS 사용
                timeout: 15000,             // 15초 타임아웃
                maximumAge: 0               // 캐시 사용 안 함
            }
        );
    };
}
```

**옵션 설명:**
- `enableHighAccuracy: true`: GPS 사용 (더 정확하지만 느림)
- `timeout: 15000`: 15초 내 응답 없으면 타임아웃
- `maximumAge: 0`: 캐시된 위치 정보 사용 안 함

---

## 에러 처리 및 예외 상황

### 1. AI API 에러 처리

#### 1.1 HTTP 에러 응답 코드

백엔드 API에서 반환하는 에러 코드 및 처리 방법:

| 상태 코드 | 에러 메시지 (detail) | 발생 원인 | 프론트엔드 처리 |
| :--- | :--- | :--- | :--- |
| **400** | 지원하지 않는 파일 형식입니다. | 허용되지 않은 확장자 파일 업로드 시 | 클라이언트 측에서 이미 검증하지만, 서버 측에서도 재검증하여 반환 |
| **413** | 파일 크기가 너무 큽니다. | 10MB 초과 파일 업로드 시 | 클라이언트 측에서 이미 검증하지만, 서버 측에서도 재검증하여 반환 |
| **500** | 이미지 분석에 실패했습니다. | AI 모델 로드 실패 또는 서버 내부 추론 오류 시 | Fail-over 로직으로 Mock 데이터 사용 |

#### 1.2 HTTP 에러 응답 처리
```javascript
if (!response.ok) {
    // 상태 코드에 따른 에러 메시지 처리
    if (response.status === 400) {
        throw new Error('지원하지 않는 파일 형식입니다.');
    } else if (response.status === 413) {
        throw new Error('파일 크기가 너무 큽니다.');
    } else if (response.status === 500) {
        throw new Error('이미지 분석에 실패했습니다.');
    } else {
        throw new Error(`API 요청 실패: ${response.status} ${response.statusText}`);
    }
}
```

#### 1.3 네트워크 에러 처리
```javascript
catch (error) {
    console.error('❌ AI 분석 실패:', error);
    return {
        type: "오류",
        tip: `서버 연결에 실패했습니다. 백엔드 서버(${API_BASE_URL})가 실행 중인지 확인해주세요.`,
        error: error.message
    };
}
```

#### 1.4 Fail-over 로직
서버 연결 실패 시 Mock 데이터로 자동 전환:

```javascript
const analysisRequest = analyzeImage(currentImageFile)
    .catch(error => {
        console.error('❌ 분석 중 오류(서버):', error);
        return mockAiAnalysis(currentImageSrc);  // Mock 데이터 사용
    });
```

### 2. 클린하우스 데이터 에러 처리

#### 2.1 CSV 로드 실패
```javascript
try {
    const csvData = await loadCsvCleanHouses();
    // 처리 로직
} catch (error) {
    console.error('❌ 클린하우스 데이터 로드 실패:', error.message);
    return [];  // 빈 배열 반환
}
```

#### 2.2 데이터 파싱 에러
```javascript
if (lines.length < 2) {
    throw new Error('CSV has no data');
}

// 좌표 유효성 검사
if (Number.isNaN(lat) || Number.isNaN(lng)) {
    continue;  // 해당 행 건너뛰기
}
```

### 3. 카카오맵 API 에러 처리

#### 3.1 SDK 로드 실패
```javascript
if (typeof kakao === 'undefined' || !kakao.maps) {
    kakaoLoadRetryCount++;
    
    if (kakaoLoadRetryCount > 5) {
        console.error('❌ Kakao Map API 로드 실패 (5회 재시도 초과)');
        showMapError();  // 에러 메시지 표시
        return;
    }
    
    setTimeout(loadKakaoMap, 1000);  // 1초 후 재시도
}
```

#### 3.2 API 제한 초과
```javascript
function showApiLimitError() {
    // 사용자에게 에러 메시지 표시
    const errorDiv = document.createElement('div');
    errorDiv.innerHTML = `
        <div>⚠️ 카카오맵 API 요청 제한 초과</div>
        <div>잠시 후 페이지를 새로고침해주세요.</div>
        <button onclick="location.reload()">지금 새로고침</button>
    `;
    document.body.appendChild(errorDiv);
}
```

### 4. Geolocation API 에러 처리

#### 4.1 위치 정보 획득 실패
```javascript
(error) => {
    console.error('❌ 위치 가져오기 실패:', error.message);
    console.warn('⚠️ 제주시 기본 좌표 사용');
    currentPosition = { lat: 33.4996, lng: 126.5312 };  // 기본 좌표
    loadKakaoMap();
}
```

#### 4.2 브라우저 미지원
```javascript
if (!navigator.geolocation) {
    console.warn('⚠️ Geolocation 미지원 - 제주시 기본 좌표 사용');
    loadKakaoMap();
}
```

---

## 성능 최적화 기법

### 1. 데이터 캐싱 시스템

#### 1.1 TTL 기반 캐싱
클린하우스 데이터를 메모리에 캐싱하여 5분간 재사용:

```javascript
const CACHE_TTL = 5 * 60 * 1000; // 5분

export async function getNearbyCleanHouses(lat, lng, radius = 5000) {
    const now = Date.now();
    
    // 캐시가 유효하면 캐시 사용
    if (cachedResults && (now - cachedTimestamp) < CACHE_TTL) {
        console.log('🗂️ 캐시된 클린하우스 결과 사용');
        return takeNearest(cachedResults, lat, lng, radius);
    }
    
    // 캐시 만료 시 새로 로드
    const csvData = await loadCsvCleanHouses();
    cachedResults = csvData;
    cachedTimestamp = Date.now();
    return takeNearest(csvData, lat, lng, radius);
}
```

**효과:**
- 불필요한 네트워크 요청 차단
- 지도 로딩 속도 개선
- 서버 트래픽 절감

### 2. 클라이언트 측 검증

#### 2.1 파일 크기 검사
서버로 전송하기 전 클라이언트에서 파일 크기를 검증:

```javascript
const MAX_SIZE = 10 * 1024 * 1024; // 10MB
if (file.size > MAX_SIZE) {
    showAlert(`파일 크기가 너무 큽니다!\n(10MB 이하만 가능합니다)`);
    return;
}
```

#### 2.2 확장자 검사
지원하지 않는 파일 형식은 업로드 전에 차단:

```javascript
const ALLOWED_TYPES = ["image/jpeg", "image/png", "image/jpg", "image/webp"];
if (!ALLOWED_TYPES.includes(file.type)) {
    showAlert('지원하지 않는 파일 형식입니다.\nJPG, PNG, WEBP 파일만 업로드해주세요.');
    return;
}
```

**효과:**
- 불필요한 서버 트래픽 방지
- 사용자 즉시 피드백 제공
- 서버 부하 감소

### 3. 비동기 처리 최적화

#### 3.1 Promise.all 활용
로딩 시간과 API 응답을 병렬 처리:

```javascript
const minLoadingTime = new Promise(resolve => setTimeout(resolve, 2500));
const analysisRequest = analyzeImage(currentImageFile);

// 두 작업이 모두 완료될 때까지 대기
const [_, result] = await Promise.all([minLoadingTime, analysisRequest]);
```

**효과:**
- 최소 로딩 시간 보장 (UX 개선)
- API 응답이 빠르면 즉시 결과 표시
- 사용자 경험 향상

### 4. 데이터 제한 및 필터링

#### 4.1 결과 개수 제한
클린하우스 검색 결과를 최대 5개로 제한:

```javascript
function takeNearest(data, lat, lng, radius) {
    return data
        .map((item) => {
            const distance = calculateDistance(lat, lng, item.lat, item.lng);
            return { ...item, distance: Math.round(distance) };
        })
        .filter((item) => item.distance <= radius)
        .sort((a, b) => a.distance - b.distance)
        .slice(0, 5);  // 최대 5개만 반환
}
```

**효과:**
- 렌더링 성능 향상
- 메모리 사용량 감소
- 사용자에게 가장 관련성 높은 정보만 제공

---

## API 연동 흐름도

### 전체 프로세스

```
1. 사용자 이미지 업로드
   ↓
2. 클라이언트 검증 (크기, 확장자)
   ↓
3. FormData 생성 및 AI API 요청
   ↓
4. 로딩 화면 표시 (최소 2.5초)
   ↓
5. 백엔드 응답 수신
   ↓
6. 데이터 변환 (영문 → 한글)
   ↓
7. 결과 화면 렌더링
   ↓
8. (선택) 지도 화면으로 이동하여 클린하우스 확인
```

### 지도 화면 프로세스

```
1. 페이지 로드
   ↓
2. Geolocation API로 사용자 위치 획득 (3회 샘플링)
   ↓
3. 가장 정확한 좌표 선택
   ↓
4. 카카오맵 초기화
   ↓
5. 클린하우스 데이터 로드 (캐시 확인)
   ↓
6. 거리 계산 및 정렬
   ↓
7. 지도에 마커 표시 (최대 5개)
   ↓
8. 가장 가까운 클린하우스 정보 표시
```

---

## 주요 코드 파일 위치

| 기능 | 파일 경로 | 주요 함수 |
|------|----------|----------|
| AI 이미지 분석 | `js/ai/api.js` | `analyzeImage()`, `testConnection()` |
| 클린하우스 데이터 | `js/map/api.js` | `getNearbyCleanHouses()`, `loadCsvCleanHouses()` |
| 지도 렌더링 | `js/map/map.js` | `initMap()`, `loadKakaoMap()`, `displayCleanHouses()` |
| 통합 제어 | `js/main.js` | `startAnalysis()`, `renderResultState()` |

---

## 참고 사항

### 백엔드 API 명세
- **서버 상태 확인**: `GET /`
- **Swagger URL**: `https://waste-api-6xd9.onrender.com/docs` 
- **API 명세서**: 본 문서의 "주요 API 연동 상세" 섹션 참조

### 환경 변수
현재 백엔드 서버 주소는 `js/ai/api.js` 파일의 상수로 관리됩니다:
```javascript
const API_BASE_URL = 'https://waste-api-6xd9.onrender.com';
```

### 배포 정보
- **프론트엔드 배포 주소**: [https://0bini.github.io/my-recycling-project/main.html](https://0bini.github.io/my-recycling-project/main.html)

---