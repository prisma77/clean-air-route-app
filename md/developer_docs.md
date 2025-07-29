# 미세먼지 회피 경로 추천 앱 개발자 문서

## 프로젝트 개요

미세먼지 회피 경로 추천 앱은 서울시 열린데이터광장, 에어코리아, 카카오맵 API를 활용하여 미세먼지 농도가 낮은 최적의 경로를 추천하는 모바일 앱입니다. 특히 한강 산책로를 중심으로 깨끗한 공기를 마시며 이동할 수 있는 경로를 안내합니다.

## 기술 스택

### 백엔드
- Node.js
- Express
- MySQL
- Axios (API 요청)
- dotenv (환경 변수 관리)
- cors (CORS 처리)

### 프론트엔드
- React Native
- Expo
- React Navigation
- React Native Paper (UI 컴포넌트)
- React Native Maps (지도 컴포넌트)
- Axios (API 요청)

## 프로젝트 구조

```
CleanAirRoute/
├── backend/                # 백엔드 서버
│   ├── src/
│   │   ├── app.js          # 앱 진입점
│   │   ├── config/         # 설정 파일
│   │   ├── controllers/    # 컨트롤러
│   │   ├── models/         # 데이터 모델
│   │   ├── routes/         # API 라우트
│   │   ├── services/       # 비즈니스 로직
│   │   └── utils/          # 유틸리티 함수
│   ├── .env                # 환경 변수
│   └── package.json        # 패키지 정보
├── frontend/               # 프론트엔드 앱
│   ├── src/
│   │   ├── App.js          # 앱 진입점
│   │   ├── api/            # API 서비스
│   │   ├── components/     # 공통 컴포넌트
│   │   ├── navigation/     # 네비게이션
│   │   ├── screens/        # 화면 컴포넌트
│   │   ├── styles/         # 스타일
│   │   └── utils/          # 유틸리티 함수
│   ├── assets/             # 이미지, 폰트 등
│   └── package.json        # 패키지 정보
└── docs/                   # 문서
    ├── api_info.md         # API 정보
    ├── architecture.md     # 아키텍처 설계
    ├── user_manual.md      # 사용자 매뉴얼
    └── developer_docs.md   # 개발자 문서
```

## API 키 설정

프로젝트에서 사용하는 API 키는 다음과 같습니다:

1. 서울시 열린데이터광장 API 키
2. 기상청(에어코리아) API 키
3. 네이버맵 API 키
   - JavaScript 키
   - REST API 키

이 API 키들은 백엔드의 `.env` 파일에 저장되어 있습니다.

## 백엔드 API 엔드포인트

### 대기질 정보 API

- `GET /api/air-quality`: 서울시 전체 대기질 정보 조회
- `GET /api/air-quality/district/:district`: 특정 지역 대기질 정보 조회
- `GET /api/air-quality/forecast`: 대기질 예보 정보 조회

### 경로 추천 API

- `POST /api/routes/recommend`: 미세먼지 회피 경로 추천
  - 요청 본문: `{ origin, destination, options }`
  - 옵션: `{ prioritizeWalkingPaths: boolean, maxPM10: number }`

### 한강 산책로 API

- `GET /api/walking-paths/hangang`: 한강 산책로 정보 조회

### 장소 검색 API

- `GET /api/places/search?keyword=:keyword`: 장소 검색

## 경로 추천 알고리즘

미세먼지 회피 경로 추천 알고리즘은 다음과 같은 요소를 고려합니다:

1. 출발지와 목적지 사이의 가능한 경로 탐색 (카카오맵 API 활용)
2. 각 경로 주변의 미세먼지 농도 계산 (서울시 대기오염 정보 API 활용)
3. 한강 산책로 포함 여부 및 비율 계산
4. 경로 점수 계산: `score = (1 - pm10Weight) + (walkingPathRatio * walkingPathWeight)`
5. 점수가 높은 순으로 경로 정렬 및 추천

## 데이터베이스 스키마

### 대기질 정보 테이블
```sql
CREATE TABLE air_quality (
  id INT AUTO_INCREMENT PRIMARY KEY,
  district VARCHAR(50) NOT NULL,
  pm10 FLOAT NOT NULL,
  pm25 FLOAT NOT NULL,
  o3 FLOAT,
  no2 FLOAT,
  co FLOAT,
  so2 FLOAT,
  measured_at DATETIME NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 한강 산책로 테이블
```sql
CREATE TABLE walking_paths (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  location VARCHAR(100) NOT NULL,
  start_point VARCHAR(100) NOT NULL,
  end_point VARCHAR(100) NOT NULL,
  length FLOAT NOT NULL,
  duration INT NOT NULL,
  description TEXT,
  coordinates JSON NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 배포 가이드

### 백엔드 배포
1. Node.js와 MySQL이 설치된 서버 준비
2. 프로젝트 클론: `git clone https://github.com/prisma7/clean-air-route-app.git`
3. 백엔드 디렉토리로 이동: `cd clean-air-route-app/backend`
4. 의존성 설치: `npm install`
5. 환경 변수 설정: `.env` 파일 생성 및 API 키 설정
6. 데이터베이스 초기화: `node src/config/dbInit.js`
7. 서버 실행: `npm start`

### 프론트엔드 배포
1. Expo CLI 설치: `npm install -g expo-cli`
2. 프론트엔드 디렉토리로 이동: `cd clean-air-route-app/frontend`
3. 의존성 설치: `npm install`
4. 앱 실행: `expo start`
5. 빌드:
   - Android: `expo build:android`
   - iOS: `expo build:ios`

## 문제 해결

### 일반적인 문제
- API 키 오류: `.env` 파일의 API 키가 올바른지 확인
- CORS 오류: 백엔드의 CORS 설정 확인
- 데이터베이스 연결 오류: MySQL 서버 실행 여부 및 연결 정보 확인

### API 관련 문제
- 서울시 API 오류: 일일 요청 한도 확인
- 에어코리아 API 오류: URL 인코딩 확인
- 카카오맵 API 오류: 도메인 등록 여부 확인

## 향후 개발 계획

1. 실시간 대기질 알림 기능 강화
2. 사용자 피드백 시스템 개선
3. 경로 추천 알고리즘 정확도 향상
4. 다른 도시로 서비스 확장
5. 웨어러블 기기 연동 기능 추가

---

© 2025 CleanAir Team. All rights reserved.
