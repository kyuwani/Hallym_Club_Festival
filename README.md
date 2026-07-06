# Hallym Club Festival

한림대학교 동아리 페스티벌 운영을 위한 모바일 관리자 웹 애플리케이션입니다. 관리자가 로그인한 뒤 보물찾기, 분실물, 공지사항 메뉴를 확인하고 관리할 수 있도록 구성되어 있습니다.

## 주요 기능

- 관리자 로그인
- 모바일 전용 관리자 화면 제공
- 보물찾기 상품 목록 확인 및 삭제 상태 표시
- 분실물 목록 확인 및 분실물 추가 화면
- 공지사항 목록 확인, 수정/삭제 메뉴, 공지사항 추가 화면
- Axios 기반 API 통신 및 access token / refresh token 처리
- 모바일 브라우저 높이 대응을 위한 `vh` 보정 유틸 적용

## 기술 스택

- React 18
- React Router DOM 6
- Axios
- Create React App
- CSS Modules 방식이 아닌 페이지별 CSS 파일 구조

## 실행 방법

### 1. 의존성 설치

```bash
npm install
```

### 2. 개발 서버 실행

```bash
npm start
```

개발 서버는 기본적으로 `http://localhost:3000`에서 실행됩니다.

### 3. 프로덕션 빌드

```bash
npm run build
```

빌드 결과물은 `build/` 디렉터리에 생성됩니다.

## 라우팅

| 경로 | 화면 | 설명 |
| --- | --- | --- |
| `/` | Login | 관리자 로그인 화면 |
| `/host` | Host | 관리자 메인 대시보드 |
| `/fotune` | Fotune | 보물찾기 관리 화면 |
| `/lost` | Lost | 분실물 목록 화면 |
| `/lostplus` | LostPlus | 분실물 추가 화면 |
| `/info` | Info | 공지사항 목록 화면 |
| `/infoplus` | InfoPlus | 공지사항 추가 화면 |

## 화면 구성

### Login

관리자 아이디와 비밀번호를 입력받아 로그인 API를 호출합니다. 로그인에 성공하면 `accessToken`과 `refreshToken`을 `localStorage`에 저장하고 `/host`로 이동합니다.

### Host

관리자 메인 화면입니다. 보물찾기, 분실물, 공지사항 메뉴로 이동할 수 있는 대시보드 형태로 구성되어 있습니다.

### Fotune

보물찾기 상품 목록을 보여주는 화면입니다. 현재 상품 데이터는 정적으로 렌더링되며, 일부 삭제 버튼은 화면 상태 변경으로 삭제 처리를 표현합니다.

### Lost / LostPlus

분실물 목록과 분실물 추가 폼을 제공합니다. 현재 분실물 목록은 정적 데이터로 구성되어 있으며, 추가 폼은 이름, 발견 위치, 이미지 파일 입력 UI를 제공합니다.

### Info / InfoPlus

공지사항 목록과 공지사항 추가 폼을 제공합니다. 공지사항 목록에서는 각 항목의 드롭다운 메뉴를 통해 수정/삭제 액션 UI를 확인할 수 있습니다.

### QR

데스크톱 해상도에서 `모바일로 접속해주세요.` 안내 문구를 표시합니다. 실제 관리자 화면은 모바일 해상도에서만 노출됩니다.

## API 구조

API 통신은 `src/apis/axiosInstance.js`와 `src/apis/axios.js`에서 관리합니다.

### 기본 API 주소

```js
https://api-hallym-club-festival.com
```

### 로그인

```http
POST /auth/login
```

요청 데이터:

```json
{
  "user_id": "관리자 아이디",
  "user_password": "관리자 비밀번호"
}
```

응답으로 받은 `accessToken`, `refreshToken`은 `localStorage`에 저장됩니다.

### 토큰 갱신

```http
POST /auth/token
```

API 응답이 `401`이고 에러 값이 `INVALID_TOKEN`이면 refresh token을 사용해 access token 재발급을 시도합니다. refresh token도 만료되었거나 유효하지 않으면 저장된 토큰을 제거하고 다시 로그인을 요구합니다.

## 디렉터리 구조

```text
src/
  apis/
    axios.js              # 로그인 API 함수
    axiosInstance.js      # Axios 인스턴스 및 토큰 인터셉터
  assets/
    images/               # 화면에서 사용하는 이미지 리소스
  components/
    Background/           # 공통 배경 컴포넌트
  pages/
    DropDownMenu/         # 공지사항 수정/삭제 드롭다운
    Fotune/               # 보물찾기 화면
    Host/                 # 관리자 메인 화면
    Info/                 # 공지사항 목록/추가 화면
    Login/                # 로그인 화면
    Lost/                 # 분실물 목록/추가 화면
    QR/                   # 데스크톱 접속 안내 화면
  styles/
    reset.css             # 전역 reset 스타일
  utils/
    setVh.js              # 모바일 viewport height 보정
  App.js                  # 라우트 정의
  index.js                # React 앱 진입점
```

## 현재 구현 상태

- 로그인 API 연동과 토큰 저장/갱신 로직은 구현되어 있습니다.
- 보물찾기, 분실물, 공지사항의 목록/추가/수정/삭제 기능은 UI 중심으로 구현되어 있습니다.
- 분실물과 공지사항 데이터는 현재 정적 데이터 또는 입력 폼 UI로 구성되어 있어, 실제 운영을 위해서는 서버 API 연동이 추가로 필요합니다.
- 로그아웃 버튼 UI는 존재하지만 토큰 제거 및 로그인 화면 이동 로직은 아직 연결되어 있지 않습니다.

## 개선 예정 항목

- 분실물 등록/조회/삭제 API 연동
- 공지사항 등록/조회/수정/삭제 API 연동
- 보물찾기 상품 조회/삭제 API 연동
- 로그아웃 기능 구현
- 로그인 상태에 따른 보호 라우팅 적용
- API 주소를 환경 변수로 분리
- 테스트 코드 추가
