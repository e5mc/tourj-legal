# 법적 문서 — 다음 작업 단계

법적 문서 HTML 7개는 작성 완료 상태입니다.
운영자/회사 표기는 이미 다음과 같이 들어가 있습니다:
- 회사/브랜드: **umpire**
- 운영자(개인사업자 미등록 상태): **엄민규(EOM MINKYU)**
- 문의 이메일: **dainomk556@gmail.com**

실제 App Store 제출에 사용하려면 아래 단계를 순서대로 진행하세요.

---

## ✅ 완료된 것

- [x] `index.html` — 랜딩
- [x] `privacy-ko.html` / `privacy-en.html` — 개인정보처리방침
- [x] `terms-ko.html` / `terms-en.html` — 이용약관
- [x] `licenses.html` — 오픈소스 SDK 라이선스 고지
- [x] `assets/style.css` — 다크모드/반응형 스타일
- [x] 운영자/회사명/저작권 표기 일괄 갱신 완료

---

## 1단계: 정보 최종 확인 (1분)

| 항목 | 현재 값 | 바꿔야 한다면 |
|---|---|---|
| 회사/브랜드 | `umpire` | 추후 사업자 등록 시 정식 상호로 |
| 운영자 실명 | `엄민규(EOM MINKYU)` | 그대로 OK |
| 문의 이메일 | `dainomk556@gmail.com` | 별도 운영 메일이 있으면 그것으로 |
| 시행일 | `2026년 5월 23일` / `May 23, 2026` | 실제 출시일로 |

시행일만 출시일에 맞춰 일괄 치환하면 됩니다:

```bash
cd legal
find . -name "*.html" -exec sed -i.bak \
  -e 's/2026년 5월 23일/2026년 7월 1일/g' \
  -e 's/May 23, 2026/July 1, 2026/g' \
  {} \;
find . -name "*.bak" -delete
```

---

## 2단계: GitHub Pages 배포 (10분)

### 옵션 A — 별도 리포지토리 (권장)

```bash
cd legal
git init
git add .
git commit -m "Initial legal pages for umpire/TourJ"
git branch -M main

# GitHub에서 https://github.com/new 로 새 public 리포 생성 (예: umpire-legal)
git remote add origin https://github.com/<your-username>/umpire-legal.git
git push -u origin main
```

GitHub 웹 → **Settings → Pages** → Source = `Deploy from a branch` → Branch = `main` / `/ (root)` → Save

1~2분 후 접속 URL:
```
https://<your-username>.github.io/umpire-legal/
https://<your-username>.github.io/umpire-legal/privacy-ko.html
https://<your-username>.github.io/umpire-legal/privacy-en.html
https://<your-username>.github.io/umpire-legal/terms-ko.html
https://<your-username>.github.io/umpire-legal/terms-en.html
```

### 옵션 B — 앱 리포 안의 `docs/` 폴더

```bash
cd /Users/eomjaejun/Desktop/TourJ    # 폴더 mv 완료 후
cp -r legal docs
git add docs
git commit -m "Add legal pages for App Store submission"
git push
```

Settings → Pages → Branch = `main` / `/docs` → Save

### 옵션 C — 커스텀 도메인

도메인을 소유하면(예: `umpire.io`):
1. 옵션 A/B로 배포 완료
2. Settings → Pages → Custom domain → `legal.umpire.io` 입력
3. DNS에 CNAME 추가 → `<your-username>.github.io`

리뷰어가 자체 도메인을 더 신뢰합니다.

---

## 3단계: App Store Connect 등록 + 앱 내 노출

### App Store Connect 등록 위치

1. **App → App Information → General Information**
   - **Privacy Policy URL**: 배포 URL (예: `https://....github.io/umpire-legal/privacy-ko.html`)
2. **App → App Information → Localizable Information**
   - 한국어/영어 각 로컬에 해당 언어판 URL을 따로 등록 (권장)

### 앱 내 링크 추가 — `IN_APP_LINKS_PATCH.md` 참고

리뷰어가 **앱 내에서 두 문서로 도달하는 경로**를 직접 확인합니다.
도달 불가능하면 Guideline 5.1.1로 리젝됩니다.

---

## 변경 동기화 체크리스트

문서를 업데이트할 때마다 확인:

- [ ] `TourJ/PrivacyInfo.xcprivacy`의 `NSPrivacyCollectedDataTypes` 항목과 `privacy-*.html`의 1조 표가 일치하는가
- [ ] App Store Connect의 "App Privacy" 응답과 `privacy-*.html`이 일치하는가
- [ ] 새 SDK를 추가했다면 `licenses.html`에 반영했는가
- [ ] 약관 변경 시 시행일자 갱신했는가 (7일 전 공지 의무 — 한국어판 14조, 영어판 §13)
- [ ] 한국어판/영어판이 같은 사실관계를 말하고 있는가
- [ ] 사업자 등록 완료 시 모든 문서에 사업자등록번호 + 통신판매업 신고번호 추가

---

## 자주 묻는 질문

**Q. 영어판도 꼭 필요한가요?**
A. 한국 단독 출시면 한국어판만으로도 통과 가능. 다만 App Review는 영문으로 진행되므로 영문판이 있으면 심사관 이해도/통과율이 높아집니다. 글로벌 출시면 필수.

**Q. 사업자 등록 전인데 운영자 표기는 어떻게?**
A. 현재 문서들은 "umpire(상호 미등록 개인사업자, 운영자: 엄민규(EOM MINKYU))" 형태로 작성되어 있어 법적으로 안전합니다. 사업자 등록 완료 시 "상호 미등록" 문구를 제거하고 사업자등록번호를 추가하면 됩니다.

**Q. PDF로 올려도 되나요?**
A. ❌ Apple은 HTML 페이지를 요구합니다. PDF 링크만 등록하면 리젝됩니다.

**Q. Notion이나 구글 docs 공유 링크는?**
A. 기술적으로 가능하지만 권장하지 않습니다. 로그인 게이트가 걸려있거나 페이지가 사라지면 즉시 리젝. 직접 호스팅하는 정적 HTML이 가장 안전.
