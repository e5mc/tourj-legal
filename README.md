# TourJ Legal Pages

App Store 제출에 필요한 법적 문서 묶음입니다. GitHub Pages에 그대로 올려서 사용하세요.

## 포함된 파일

```
legal/
├── index.html         ← 메인 랜딩 (모든 문서로의 링크)
├── privacy-ko.html    ← 개인정보처리방침 (한국어)
├── privacy-en.html    ← Privacy Policy (English)
├── terms-ko.html      ← 이용약관 (한국어)
├── terms-en.html      ← Terms of Service (English)
├── licenses.html      ← 오픈소스 라이선스 고지
├── assets/style.css   ← 공통 스타일 (다크모드 포함)
└── README.md          ← 이 파일
```

## ⚠️ 게시 전에 반드시 확인하세요

각 HTML 파일을 열어 다음을 본인 정보로 바꿔주세요. 현재는 **자리표시자**가 들어가 있습니다.

| 자리표시자 | 어디 있나 | 바꿔야 할 값 |
|---|---|---|
| `엄민규(EOM MINKYU)` | 모든 파일 | 본인 성함 (지금: 엄민규/EOM MINKYU). 가능하면 한국어 이름과 영문 이름을 각 언어판에 맞게. |
| `dainomk556@gmail.com` | 모든 파일 | 개인정보 문의용 이메일. 별도 운영 이메일이 있으면 그것으로. |
| `2026년 5월 23일 / May 23, 2026` | 모든 파일의 시행일 | 실제 앱스토어 출시일 또는 약관 시행일 |

특히 **개인정보 보호책임자 연락처**는 한국 개인정보보호법상 실명·연락 가능한 이메일이 필수입니다.

## GitHub Pages 배포 방법 (5분)

### 방법 A — 별도 리포지토리 (권장)

법적 문서는 본 앱 소스와 분리하여 별도 리포에 두는 게 깔끔합니다.

1. GitHub에서 새 public 리포지토리 생성 (예: `tourj-legal`)
2. 이 `legal/` 폴더의 **내용물**(`index.html`, `assets/` 등)을 **리포 루트**에 푸시
   ```bash
   cd legal
   git init
   git add .
   git commit -m "Add legal pages"
   git branch -M main
   git remote add origin https://github.com/<your-username>/tourj-legal.git
   git push -u origin main
   ```
3. 리포 → Settings → Pages → Source = `Deploy from a branch` / Branch = `main` / `/ (root)` → Save
4. 1~2분 후 `https://<your-username>.github.io/tourj-legal/`에서 접속 가능

### 방법 B — 기존 앱 리포에 `docs/` 폴더로 배포

소스코드 리포가 public이라면 같은 리포 안의 `docs/` 폴더로 배포할 수도 있습니다.

1. 이 `legal/` 폴더를 `docs/`로 이름 바꿔 리포 루트에 추가
2. Settings → Pages → Branch = `main` / `/docs` → Save
3. `https://<your-username>.github.io/<repo-name>/`에서 접속

### 방법 C — 커스텀 도메인

본인 도메인(`tourj.app` 등)을 가지고 있다면:

1. 위 A/B 방법 중 하나로 배포 완료 후
2. Settings → Pages → Custom domain에 `legal.tourj.app` 등 입력
3. DNS에 CNAME 레코드 추가 (도메인 등록기관에서)
4. 신뢰도 가장 높음. App Store 리뷰어가 자체 도메인을 더 좋아함

## App Store Connect에 등록

배포 URL을 다음 두 곳에 입력합니다.

1. **App Store Connect → App → App Information → Privacy Policy URL**  
   → `https://.../privacy-ko.html` 또는 `index.html`
2. **App Store Connect → App → App Information → Marketing URL** (선택)
3. **App Store Connect → App → General → Localizable Information → Privacy Policy URL**  
   각 언어별로 한국어판/영어판 URL을 각각 입력하면 더 좋음

## 앱 내 링크 추가도 잊지 마세요

심사 통과 후에도 사용자가 앱 안에서 두 문서에 도달할 수 있어야 합니다. 추천 위치:

- `OnboardingFlowView.swift`의 "이용약관 · 개인정보 처리방침에 동의" 텍스트를 **탭 가능한 링크**로 변경
- `ProfileView.swift`의 `appInfoSection`을 본문에 추가하고, 그 안에 두 링크 + Open Source Licenses 링크 노출

각 링크는 `Link(destination: URL(string: "https://.../privacy-ko.html")!)` 또는 `SafariView`로 띄우면 됩니다.

## 변경 시 동기화 체크리스트

- [ ] `PrivacyInfo.xcprivacy`의 수집 항목과 `privacy-*.html`의 항목이 정확히 일치하는가
- [ ] App Store Connect의 "App Privacy" 응답과 `privacy-*.html`이 일치하는가
- [ ] 새 SDK를 추가했다면 `licenses.html`에 반영했는가
- [ ] 약관 변경 시 시행일자 갱신했는가 (7일 전 공지 의무)

## 라이선스

이 문서들은 본 앱(TourJ)의 운영에 사용할 목적으로 작성되었습니다.
다른 앱에 그대로 사용할 경우 본인 앱의 실제 데이터 수집 행위에 맞게
반드시 항목을 수정하세요. 잘못된 개인정보처리방침은 법적 책임의 원인이 됩니다.
