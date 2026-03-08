# Firebase 설정 가이드

> 이 가이드를 따라 약 10분 안에 Firebase를 설정할 수 있습니다.

---

## Step 1. Firebase 프로젝트 생성

1. [Firebase 콘솔](https://console.firebase.google.com) 접속 (Google 계정 로그인)
2. **"프로젝트 추가"** 클릭
3. 프로젝트 이름 입력 (예: `choidongjin-site`)
4. Google Analytics 사용 여부 선택 후 **"프로젝트 만들기"**

---

## Step 2. 웹 앱 등록 및 설정값 복사

1. 프로젝트 홈 → **`</>`** (웹 아이콘) 클릭
2. 앱 닉네임 입력 (예: `homepage`) → **"앱 등록"**
3. 아래와 같은 설정값이 표시됩니다:

```javascript
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "choidongjin-site.firebaseapp.com",
  projectId: "choidongjin-site",
  storageBucket: "choidongjin-site.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123",
  measurementId: "G-XXXXXXX"
};
```

4. 위 값을 **`firebase-config.js`** 파일에 복사하여 채워 넣으세요.
5. `ADMIN_EMAIL`에 본인 Gmail 주소를 입력하세요.

---

## Step 3. Google 로그인 활성화

1. Firebase 콘솔 → 좌측 메뉴 **"Authentication"** → **"시작하기"**
2. **"Sign-in method"** 탭 → **"Google"** 클릭
3. **"사용 설정"** 토글 ON → 프로젝트 지원 이메일 선택 → **"저장"**

---

## Step 4. Firestore 데이터베이스 생성

1. Firebase 콘솔 → 좌측 메뉴 **"Firestore Database"** → **"데이터베이스 만들기"**
2. **"테스트 모드로 시작"** 선택 → 위치 선택 (`asia-northeast3` = 서울 권장)
3. **"완료"** 클릭

### Firestore 보안 규칙 설정 (중요!)

**"규칙"** 탭에서 아래 내용으로 교체하세요:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // 문의 폼: 누구나 쓰기 가능, 관리자만 읽기/수정/삭제
    match /contacts/{id} {
      allow create: if true;
      allow read, update, delete: if request.auth != null
        && request.auth.token.email == "여기에_본인Gmail_입력@gmail.com";
    }

    // 방문자 통계: 누구나 쓰기 가능, 관리자만 읽기
    match /visits/{id} {
      allow create: if true;
      allow read, delete: if request.auth != null
        && request.auth.token.email == "여기에_본인Gmail_입력@gmail.com";
    }

    // 강의/저서: 관리자만 모든 권한
    match /lectures/{id} {
      allow read, write: if request.auth != null
        && request.auth.token.email == "여기에_본인Gmail_입력@gmail.com";
    }
    match /books/{id} {
      allow read, write: if request.auth != null
        && request.auth.token.email == "여기에_본인Gmail_입력@gmail.com";
    }
  }
}
```

> `여기에_본인Gmail_입력@gmail.com` 부분을 실제 Gmail 주소로 교체하세요.

---

## Step 5. 로컬에서 테스트 (중요!)

`file://` 프로토콜은 Firebase 인증에서 오류가 발생할 수 있습니다.
**로컬 서버로 실행**하세요:

### 방법 A — Node.js (권장)
```bash
# Node.js 설치 필요 (https://nodejs.org)
npx serve C:/Claude/test
# 브라우저에서 http://localhost:3000/drchoidjintro.html 접속
```

### 방법 B — Python
```bash
cd C:/Claude/test
python -m http.server 8080
# 브라우저에서 http://localhost:8080/drchoidjintro.html 접속
```

### 방법 C — VS Code Live Server 확장
VS Code → 확장 → "Live Server" 설치 → `drchoidjintro.html` 우클릭 → "Open with Live Server"

---

## 파일 구조

```
C:\Claude\test\
├── drchoidjintro.html       ← 메인 홈페이지 (Firebase 연동 완료)
├── admin.html               ← 관리자 대시보드 (구글 로그인)
├── firebase-config.js       ← ⚠️ Firebase 설정값 입력 필요
├── Firebase_설정_가이드.md  ← 현재 문서
├── index.html               ← 기본 소개 페이지
├── 개발기획서.md
└── 최동진_박사_소개페이지_기획서.md
```

---

## Firestore 컬렉션 구조

| 컬렉션 | 용도 | 저장 시점 |
|--------|------|-----------|
| `contacts` | 문의 폼 제출 내역 | 방문자가 폼 제출 시 |
| `visits` | 방문자 통계 | 페이지 로드 시 자동 |
| `lectures` | 강의 이력 | 관리자가 admin.html에서 추가 |
| `books` | 저서 정보 | 관리자가 admin.html에서 추가 |

---

## 관리자 페이지 접속

```
http://localhost:3000/admin.html
```

- Google 계정으로 로그인 (firebase-config.js의 ADMIN_EMAIL과 일치해야 함)
- 문의 내역 확인 및 관리
- 방문자 통계 조회
- 강의 이력 / 저서 추가·삭제

---

*가이드 작성일: 2026년 3월 8일*
