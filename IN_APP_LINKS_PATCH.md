# 앱 내 법적 문서 링크 추가 — SwiftUI 패치

App Store 리뷰어가 **앱 내에서** 개인정보처리방침/이용약관에 도달할 수 있는지 직접 확인합니다.
도달 불가능하면 Guideline 5.1.1 위반으로 리젝됩니다.

이 문서는 그대로 복사-붙여넣기 할 수 있는 SwiftUI 패치 코드입니다.
배포 URL이 정해진 후 `LEGAL_BASE_URL` 한 곳만 바꾸면 모든 링크가 동작합니다.

> **참고**: 아래 경로들은 폴더 리네임 완료(`TravelPlanner/` → `TourJ/`) 이후 기준입니다.

---

## 0. 공통 — `LegalURLs.swift` 신규 파일 생성

`TourJ/LegalURLs.swift` 위치에 새 파일 생성:

```swift
import Foundation

/// 외부 호스팅된 약관/정책 페이지 URL 모음.
/// 호스팅 위치가 바뀌면 여기 한 곳만 수정하면 된다.
enum LegalURLs {
    /// 배포 후 실제 URL로 교체
    /// 예: "https://your-username.github.io/umpire-legal"
    static let baseURL = "https://CHANGE-ME.github.io/umpire-legal"

    static var privacyPolicyKO: URL { URL(string: "\(baseURL)/privacy-ko.html")! }
    static var privacyPolicyEN: URL { URL(string: "\(baseURL)/privacy-en.html")! }
    static var termsOfServiceKO: URL { URL(string: "\(baseURL)/terms-ko.html")! }
    static var termsOfServiceEN: URL { URL(string: "\(baseURL)/terms-en.html")! }
    static var openSourceLicenses: URL { URL(string: "\(baseURL)/licenses.html")! }

    /// 현재 시스템 언어에 따라 자동 선택
    static var privacyPolicy: URL {
        Locale.current.language.languageCode?.identifier == "ko" ? privacyPolicyKO : privacyPolicyEN
    }

    static var termsOfService: URL {
        Locale.current.language.languageCode?.identifier == "ko" ? termsOfServiceKO : termsOfServiceEN
    }
}
```

---

## 1. `OnboardingFlowView.swift` 패치 (line 265~273)

### 현재 코드

```swift
private var termsNotice: some View {
    Text("계속 진행 시 이용약관 · 개인정보 처리방침에\n동의하게 됩니다")
        .font(.system(size: 10))
        .foregroundColor(AppColors.Designer.inkSecondary)
        .multilineTextAlignment(.center)
        .lineSpacing(2)
        .padding(.horizontal, 24)
        .padding(.bottom, 22)
}
```

### 수정 후

```swift
private var termsNotice: some View {
    var attributed: AttributedString {
        var s = AttributedString("계속 진행 시 이용약관 · 개인정보 처리방침에\n동의하게 됩니다")

        if let range = s.range(of: "이용약관") {
            s[range].link = LegalURLs.termsOfService
            s[range].underlineStyle = .single
        }
        if let range = s.range(of: "개인정보 처리방침") {
            s[range].link = LegalURLs.privacyPolicy
            s[range].underlineStyle = .single
        }
        return s
    }

    return Text(attributed)
        .font(.system(size: 10))
        .foregroundColor(AppColors.Designer.inkSecondary)
        .tint(AppColors.Designer.sky500)
        .multilineTextAlignment(.center)
        .lineSpacing(2)
        .padding(.horizontal, 24)
        .padding(.bottom, 22)
}
```

---

## 2. `ProfileView.swift` 패치 — "정보" 섹션 추가

### Step 2-1: body 안에 `appInfoSection` 추가

```swift
VStack(spacing: 16) {
    statCards
    friendPreviewSection
    syncSection
    accountSection
    appInfoSection   // ← 추가
}
```

### Step 2-2: 파일 하단(`accountSection` 정의 다음)에 신규 섹션 정의 추가

```swift
private var appInfoSection: some View {
    VStack(spacing: 0) {
        sectionHeader("정보")

        VStack(spacing: 0) {
            settingsRow(
                icon: "doc.text",
                title: "이용약관",
                color: AppColors.skyDark
            ) {
                UIApplication.shared.open(LegalURLs.termsOfService)
            }
            Divider().padding(.leading, 48)

            settingsRow(
                icon: "hand.raised",
                title: "개인정보처리방침",
                color: AppColors.skyDark
            ) {
                UIApplication.shared.open(LegalURLs.privacyPolicy)
            }
            Divider().padding(.leading, 48)

            settingsRow(
                icon: "shippingbox",
                title: "오픈소스 라이선스",
                color: AppColors.skyDark
            ) {
                UIApplication.shared.open(LegalURLs.openSourceLicenses)
            }
            Divider().padding(.leading, 48)

            // 앱 버전 표시
            HStack {
                Image(systemName: "info.circle")
                    .font(.system(size: 16))
                    .foregroundColor(.secondary)
                    .frame(width: 28)
                Text("앱 버전")
                    .font(.system(size: 15, weight: .medium))
                    .foregroundColor(.primary)
                Spacer()
                Text(appVersionString)
                    .font(.system(size: 14))
                    .foregroundColor(.secondary)
            }
            .padding(.horizontal, 16)
            .padding(.vertical, 13)
        }
        .background(Color(.systemBackground))
        .cornerRadius(AppDesign.cornerButton)
        .cardShadow()
    }
}

private var appVersionString: String {
    let version = Bundle.main.infoDictionary?["CFBundleShortVersionString"] as? String ?? "1.0"
    let build = Bundle.main.infoDictionary?["CFBundleVersion"] as? String ?? "1"
    return "\(version) (\(build))"
}
```

---

## 3. (선택) 인앱 Safari로 띄우기

외부 Safari 대신 인앱 Safari로 띄우려면:

```swift
import SafariServices
import SwiftUI

struct SafariView: UIViewControllerRepresentable {
    let url: URL
    func makeUIViewController(context: Context) -> SFSafariViewController {
        SFSafariViewController(url: url)
    }
    func updateUIViewController(_ uiViewController: SFSafariViewController, context: Context) {}
}
```

ProfileView에서:

```swift
@State private var presentedLegalURL: URL?

settingsRow(...) {
    presentedLegalURL = LegalURLs.termsOfService
}

.sheet(item: Binding(
    get: { presentedLegalURL.map { IdentifiableURL(url: $0) } },
    set: { presentedLegalURL = $0?.url }
)) { item in
    SafariView(url: item.url)
}

private struct IdentifiableURL: Identifiable {
    let url: URL
    var id: String { url.absoluteString }
}
```

---

## 4. 적용 후 확인 체크리스트

- [ ] `LegalURLs.swift`의 `baseURL`을 실제 배포 URL로 교체했는가
- [ ] 온보딩 화면에서 "이용약관"/"개인정보 처리방침" 단어를 탭했을 때 페이지가 열리는가
- [ ] 프로필 → 정보 섹션에서 3개 링크가 모두 동작하는가
- [ ] Sign in with Apple과 Google 로그인 버튼이 동등 노출되어 있는가 (이미 수정 완료)
- [ ] 다크모드에서 링크 색상이 잘 보이는가
- [ ] 한국어/영어 시스템 언어에 따라 자동으로 해당 언어판 페이지가 열리는가

---

## 5. 빌드 확인

```bash
cd /Users/eomjaejun/Desktop/TourJ
xcodebuild -project TourJ.xcodeproj \
           -scheme TourJ \
           -destination 'platform=iOS Simulator,name=iPhone 15' \
           build
```

`scripts/lint.sh`도 통과:

```bash
./scripts/lint.sh
```
