# K-뉴딜 모의면접 피드백 보드 (Firebase 버전)

면접관이 평가를 입력하면 교육생별로 취합되고, 피드백 결과지를 만들어 전달까지 관리하는 페이지입니다.
화면은 GitHub Pages에서 열리고, 입력 내용은 Firebase(Firestore)에 저장됩니다.

```
index.html       ← 페이지 (맨 위 FIREBASE_CONFIG에 내 프로젝트 값을 넣습니다)
firestore.rules  ← 보안 규칙 (로그인한 면접관만 읽고 쓰기)
```

교육생 명단 파일(`모의면접_명단_59명.json`)은 **GitHub에 올리지 마세요.** 페이지에 로그인한 뒤 한 번 불러오면 Firestore에만 저장됩니다. 그러면 저장소를 공개로 만들어도 명단이 드러나지 않습니다.

---

## 1. Firebase 프로젝트 만들기

1. https://console.firebase.google.com 에서 **프로젝트 추가**를 누릅니다. Google 애널리틱스는 꺼도 됩니다.
2. **웹 앱 등록**
   - 프로젝트 개요 옆 톱니바퀴에서 **프로젝트 설정 → 일반 → 내 앱 → 웹(`</>`)**을 누릅니다.
   - 앱 닉네임(예: `kmi-feedback`)을 넣고 **앱 등록**을 누릅니다. Firebase 호스팅은 체크하지 않습니다.
   - 화면에 나오는 `firebaseConfig` 값(apiKey, authDomain, projectId …)을 복사해 둡니다.
3. **Firestore 만들기**
   - 왼쪽 메뉴 **빌드 → Firestore Database → 데이터베이스 만들기**를 누릅니다.
   - 위치는 **asia-northeast3 (서울)**, 모드는 **프로덕션 모드**를 고릅니다.
4. **보안 규칙 넣기**
   - Firestore의 **규칙** 탭에서 기존 내용을 지웁니다.
   - `firestore.rules` 내용을 붙여넣고 **게시**를 누릅니다.
5. **로그인 방식 켜기**
   - **빌드 → Authentication → 시작하기**를 누릅니다.
   - **로그인 방법 → 이메일/비밀번호 → 사용 설정 → 저장**을 누릅니다.
6. **면접관 계정 만들기**
   - **Authentication → 사용자 → 사용자 추가**에서 면접관 이메일과 비밀번호를 넣습니다.
   - 면접관마다 하나씩 만들거나, 캠퍼스별 공용 계정을 하나씩 만들어도 됩니다.
7. **아무나 가입하지 못하게 막기 (권장)**
   - **Authentication → 설정 → 사용자 작업**에서 **생성(가입) 사용 설정**을 해제하고 저장합니다.
   - 메뉴 이름은 콘솔 버전에 따라 조금 다를 수 있습니다. 영문 화면에서는 Settings → User actions → Enable create (sign-up)입니다.

## 2. index.html에 설정값 넣기

`index.html`을 열어 `Firebase 설정` 부분을 2번에서 복사한 값으로 채웁니다.

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "내프로젝트.firebaseapp.com",
  projectId: "내프로젝트",
  storageBucket: "내프로젝트.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcd..."
};
```

이 값들은 공개되어도 괜찮습니다. 데이터는 4번의 보안 규칙이 지킵니다.

## 3. GitHub Pages로 공개

1. GitHub에서 **New repository**를 만듭니다. (예: `kmi-feedback`)
2. **Add file → Upload files**로 `index.html`, `firestore.rules`, `README.md`를 올리고 **Commit**합니다.
3. **Settings → Pages**에서 **Source: Deploy from a branch**, **Branch: main / (root)**를 고르고 **Save**합니다.
4. 1~2분 뒤 `https://<아이디>.github.io/kmi-feedback/` 주소가 생깁니다.
5. **Firebase 콘솔 → Authentication → 설정 → 승인된 도메인 → 도메인 추가**에 `<아이디>.github.io`를 넣습니다.

## 4. 명단 불러오기 (한 번만)

1. 페이지에 면접관 계정으로 로그인합니다.
2. **교육생 명단** 탭으로 갑니다.
3. **명단 파일 불러오기**에서 `모의면접_명단_59명.json`을 고릅니다.
4. "명단 59명을 불러왔습니다"가 뜨고 표에 59명이 보이면 끝입니다.

케이스 배정은 같은 탭에서 **미배정 59명에게 고르게 배정**을 누르거나, 표에서 한 명씩 고르면 됩니다.

## 5. 면접관에게 안내

- 페이지 주소와 1-6에서 만든 계정을 전달합니다.
- 오른쪽 위에 로그인한 이메일과 **로그아웃**이 보이면 정상입니다.
- 한 사람이 저장하면 다른 면접관 화면에도 바로 반영됩니다.

## 운영 메모

- **백업**: **취합 현황 → CSV로 저장**으로 내려받으세요.
- **면접이 끝난 뒤**: Authentication에서 면접관 계정을 삭제하거나 사용 중지하세요. 데이터는 Firestore 화면에서 `students`, `evals`, `finals` 컬렉션을 삭제하면 지워집니다.
- 무료(Spark) 요금제로 충분합니다. 하루 읽기 5만 회, 쓰기 2만 회까지 무료입니다.
