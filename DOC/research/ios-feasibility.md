# Life OS — iOS / watchOS 기술 타당성 조사

> 작성 2026-11-23, Claude 조사. 확인 필요 항목은 ⚠️
>
> 조사 범위: Swift 네이티브 iPhone + Apple Watch 앱이 "폰을 조용하게 만들고(앱 가리기·제한, 첫 화면 바꾸기) AI가 뒤에서 조용히 일하는" 제품을 만들 때, 서드파티 앱에 허용되는 것과 불가능한 것.
> 출처는 각 문장 끝 `[번호]`로 표기, 4장에 URL·날짜 정리. 웹 문서 접근일은 모두 2026-11-23.

---

## 0. 전제 확인 — "iOS 26이 최신"이 아니다

- **iOS 27 / watchOS 27 / Xcode 27이 2026-09-14에 정식 출시**됐다. Xcode 27(27A266a)은 iOS 27·watchOS 27 SDK를 포함한다 [E1][E2].
- Xcode 27.1은 Swift 6.4 + iOS 27.1·watchOS 27 SDK를 포함하며 **macOS Tahoe 26.6 이상** 필요, 기기 디버깅은 iOS 17+/watchOS 10+ [E3].
- 2027년 4월부터 App Store Connect 업로드는 iOS 27/watchOS 27 SDK로 빌드해야 한다 [E4].
- 즉 이 문서의 "현재"는 **iOS 27.x / watchOS 27.x / Xcode 27.1**이다. iOS 26.4(2026년 봄)에 Screen Time API에 중요한 변화가 있었으므로(§A-4, §A-6) 최소 지원 버전은 iOS 26.4 이상을 권한다.

---

## 1. 한눈에 보기

| 영역 | 항목 | 판정 | 핵심 한 줄 |
|---|---|---|---|
| A | 개인(본인) 기기 Screen Time 권한 (FamilyControls `.individual`) | 가능 | iOS 16+. 사용자는 시스템 알림 → Face ID/Touch ID 한 번. 이후 재요청 시 프롬프트 없음 [A1][A2] |
| A | 앱·카테고리·웹도메인 차단(쉴드) | 제한 있음 | 앱 토큰 50개, 웹도메인 토큰 50개 한도. 선택은 반드시 `FamilyActivityPicker`로 사용자가 직접 [A5][A6] |
| A | 쉴드 화면 커스터마이즈 | 제한 있음 | 블러·배경색·아이콘·제목·부제·버튼 1~2개(+서브메뉴 3개). 임의 UI/애니메이션 불가 [A7][A8] |
| A | 사용량 데이터를 메인 앱·서버로 가져오기 | **불가(한국)** | Report 확장은 네트워크·외부 전달 차단 샌드박스. iOS 26.4의 `approvedWithDataAccess`/`activityData(...)`는 **EU 거주 고객만** [A10][A11][A12][A13] |
| A | 전화·설정 등 시스템 앱 차단 | 제한 있음 ⚠️ | 자기 앱은 차단 불가, "항상 허용" 앱은 카테고리 쉴드 무시. 전화/설정 개별 확인 미확인 [A15][A16] |
| A | 사용자 우회(권한 해제) | 제한 있음 | 설정에서 Face ID로 끌 수 있음. iOS 26.4부터 Screen Time 암호로 잠글 수 있으나 우회 버그가 있었고 iOS 27 베타에서 수정 보고 [A17][A18][A19][A20] |
| A | Family Controls 배포 권한 승인 | 제한 있음 | 신청 필수, 확장마다 별도 신청. 기간 미공개 — 수일~2개월+ 보고 [A21][A22][A23] |
| A | 앱 심사 리스크 | 제한 있음 | `blockedApplications`로 "앱 숨기기" 홍보 → 2.5.1 리젝 사례. "Screen Time API 수익화 금지" 조항 존재 [A24][A25][A26] |
| B | 홈/잠금화면 위젯·StandBy·인터랙티브 위젯 | 가능 | WidgetKit. iOS 27: App Intents로 위젯 커스터마이즈·동적 스타일 [B1] |
| B | Live Activity | 가능 | 최대 8h(잠금화면 12h), 4KB, 네트워크 불가, 페어링된 Watch Smart Stack에 자동 표시 [B2] |
| B | 집중 모드(Focus) 켜기/끄기 | **불가** | 공개 API 없음. Focus Filter로 "반응"만 가능. 켜기는 사용자가 만든 단축어 자동화에 의존 [B3][B4][B5] |
| B | Control Center 컨트롤·Action 버튼 | 가능 | iOS 18 ControlWidget. 토글/액션/딥링크 [B6] |
| B | 앱 숨기기/잠그기(iOS 18 기능) 제어 | 불가 | 시스템 전용, 공개 API 없음. API로 숨기려면 ManagedSettings `blockedApplications`뿐이며 심사 리스크 [B7][A24] |
| B | 홈 화면 대체(런처) | **불가** | SpringBoard 대체 불가. 위젯·잠금화면·Focus별 홈 페이지(사용자 설정)로 근접만 가능 [B8][B9] |
| C | watchOS에서 FamilyControls/ManagedSettings/DeviceActivity | **불가** | 세 프레임워크 모두 가용 플랫폼이 iOS/iPadOS(일부 Catalyst)만 [A3][A4][A14] |
| C | 워치에서 폰 차단 트리거 | 제한 있음 | WCSession(폰 근처), 워치 Control Center 컨트롤(액션이 iPhone에서 실행), 서버→사일런트 푸시(2~3회/h, 보장 없음) [C1][C2][C3] |
| C | 폰 두고 워치만으로 생활 | 제한 있음 | 셀룰러 모델이면 통화·메시지·Siri 가능. 서드파티 푸시는 iPhone이 켜져 네트워크에 있어야 함 [C4] |
| C | 컴플리케이션·Smart Stack 위젯 | 가능 | WidgetKit watchOS 10+, 관련성(RelevantContext) watchOS 11+, 컨트롤 watchOS 26+ [C5][C6] |
| C | HealthKit(HR·HRV·수면 단계·서있기·운동) | 가능 | 전부 읽기 가능. 백그라운드 전달은 대부분 시간당 1회 상한, 컴플리케이션 있으면 ~4회/h 예산 공유 [C7][C8][C9] |
| C | 워치 백그라운드 실행 | 제한 있음 | 앱 리프레시 ≈4회/h(활성 페이스에 컴플리케이션 있을 때), HealthKit과 예산 공유 [C8][C9] |
| C | Double Tap / Action 버튼 | 가능 | Double Tap API watchOS 11+, Action 버튼은 Ultra 전용(컨트롤로 연결) [C6][C10] |
| C | 워치에서 Foundation Models | 제한 있음 | watchOS 27: 온디바이스 AFM 없음 → PCC(네트워크 필요)만. 오프라인 AI는 Core AI/Core ML [D4][C11] |
| D | Foundation Models(온디바이스 LLM) | 가능 | iOS 26+, iPhone 15 Pro/16/17 이상. 한국어 지원. 비용 0, 컨텍스트 4K(Core)/8K(Core Advanced), 추론 없음 [D1][D2][D3][D4] |
| D | Private Cloud Compute 모델 | 제한 있음 | iOS 27+, 32K, 일일 쿼터, 관리형 권한 필요. 소규모 사업자(<200만 DL)는 API 비용 0 [D5][D6] |
| D | 백그라운드 실행 | 제한 있음 | AppRefresh ≤30초·시점은 시스템 재량, Processing은 "유휴+충전 시 수 분", 사일런트 푸시 ≤2~3회/h [D7][D8][D9] |
| D | "앱 사이에서 조용히 일하는 에이전트" | 제한 있음 | 상시 실행 불가. 야간 BGProcessingTask + 앱 실행 시 + 위젯 타임라인 + DeviceActivity 콜백 조합으로 "스냅샷형" 에이전트만 가능 [D7][D10] |
| E | M1 Max MacBook | 가능 | Xcode 27은 Apple silicon 전용, macOS 26.6+ 요구 → 충족 [E1][E3] |
| E | 개발자 프로그램 | 가능 | US$99/년. TestFlight 내부 100/외부 10,000. Family Controls는 배포 권한 승인 전엔 TestFlight도 불가 [E5][E6] |
| E | 시뮬레이터 | 제한 있음 | watchOS 시뮬레이터는 있으나 FamilyControls 개인 인증은 시뮬레이터에서 실패 보고, HealthKit 백그라운드는 시뮬레이터 미지원 → 실기기 필수 [E7][C9] |
| F | 오픈소스 선행 사례 | 제한 있음 | 완성형 레포 없음. 소규모 예제(krypted 35★, RN 래퍼) + Apple WWDC 샘플 [F1][F2][F3] |

---

## 2. 상세

### A. Screen Time API (FamilyControls · ManagedSettings · DeviceActivity)

#### A-1. 인증(FamilyControls) — 가능
- `AuthorizationCenter.shared.requestAuthorization(for: .individual)`은 **iOS 16.0+** [A1]. Apple 문서: "If you're authenticating an `individual` who hasn't previously authorized your app, the system displays an alert. If the `individual` chooses to continue, the device asks to authorize that `individual` using Face ID or Touch ID." 승인 후 재호출은 프롬프트 없이 상태만 갱신 [A2].
- 매 실행 시 요청 권장: "Always request authorization when your app first launches." 상태는 "a parent or guardian changing the status in Settings" 같은 외부 이벤트로 바뀔 수 있음 [A2].
- `AuthorizationStatus`: `.notDetermined / .denied / .approved / .approvedWithDataAccess`. 마지막 항목은 "approved ... with access to non-tokenized family activity data" [A9].
- 가용 플랫폼: `AuthorizationCenter`는 iOS/iPadOS 15.0+만 표기. **watchOS 없음** [A3]. visionOS 호환 앱에서는 "authorization attempts always fail" [A1].
- ⚠️ 시뮬레이터: `.individual` 인증이 시뮬레이터에서 실패한다는 포럼 보고가 있다(공식 문서 미확인) [E7].
- 백그라운드에서 권한 철회를 감지할 공식 방법 없음. iOS 26.2 기준 `$authorizationStatus` 퍼블리셔가 디버거 없이는 발행 안 된다는 보고, Apple 답변 없음(2026-03) [A20].

#### A-2. 차단(ManagedSettings) — 제한 있음
- `ManagedSettingsStore`로 쉴드·앱 차단·웹 콘텐츠 필터·미디어·App Store·암호 설정 등 제한 가능 [A4].
- `shield.applications`: "Your app can shield up to 50 application tokens at once." 사용자가 쉴드된 앱을 열면 ShieldConfiguration 확장 → 버튼 누르면 ShieldAction 확장 호출 [A5]. `shield.webDomains`도 50 토큰 [A6]. 카테고리는 `ActivityCategoryPolicy` (`.all(except:)` / `.specific`) [A5].
- `application.blockedApplications`: 문서 문구 "The system hides blocked applications and prevents the user from launching them." (앱이 홈 화면에서 사라짐) [A24]. iOS 27에서 차단 해제 후 원래 위치로 돌아오지 않는 회귀 보고 ⚠️ [A27].
- 토큰은 불투명(`Token`: "doesn't reveal its identity"). `ApplicationToken`에서 번들 ID를 얻을 수 없음(Apple 직원 답변) [A4][A12]. 토큰은 **picker에서 사용자가 고를 때만** 생성 → 코드로 "인스타그램 차단" 같은 지정 불가 [A16].
- 웹: `webDomains` 토큰도 picker 유래. 비-Safari 브라우저 도메인은 picker에 잘 안 뜸. `webContent.blockedByFilter = .specific(...)`가 iOS 26에서 Safari에 안 먹고 `.all(except:)`는 먹는다는 보고 ⚠️ [A28].

#### A-3. 쉴드 화면 커스터마이즈(ManagedSettingsUI) — 제한 있음
- `ShieldConfiguration` 속성: `backgroundBlurStyle, backgroundColor, icon, title, subtitle, primaryButtonLabel, primaryButtonBackgroundColor, secondaryButtonLabel, secondaryButtonSubmenuItems([String])`. "The system provides a default appearance for any properties you set to `nil`." [A7]
- `ShieldAction`: `primaryButtonPressed, secondaryButtonPressed, first/second/thirdSecondarySubmenuItemPressed` [A8]. 응답은 `.close / .defer / .none` ⚠️(ShieldActionResponse 문서 직접 미열람) [A29].
- 글자수 제한·여러 줄·동영상·개인화 질문은 포럼에서 답변 없음 [A30]. **임의 뷰는 불가** — Life OS가 "쉴드 대신 할 일"을 보여주려면 제목/부제 텍스트 + 버튼 2개 안에서 해결해야 한다.

#### A-4. 모니터링(DeviceActivity) — 제한 있음
- `DeviceActivitySchedule`(캘린더 기반 반복), `DeviceActivityEvent`(앱/카테고리/도메인별 임계치), `DeviceActivityMonitor` 확장이 `intervalDidStart/End`, `eventDidReachThreshold`, 경고 콜백을 받고 그 안에서 `ManagedSettingsStore`를 바꿀 수 있다 [A14][A31]. 가용: iOS/iPadOS/macCatalyst(DeviceActivityReport는 16.0+) [A10].
- Monitor 확장 메모리 ≈ **6 MB** 상한(EXC_RESOURCE 보고, 공식 문서 없음) ⚠️ [A32]. LLM 호출 같은 무거운 일은 못 한다.
- 신뢰성: 임계치 이벤트가 즉시 발화/미발화(2023~iOS 26.x까지 지속 보고, Radar 첨부) [A33][A34], `intervalDidStart`가 "most of the time not at all" 보고 [A35]. 실무에서는 폴백 로직 필수.

#### A-5. 사용량 데이터 접근 — 불가(한국), 제한 있음(EU)
- `DeviceActivityReport` 문서: "your extension runs in a sandbox. This sandbox prevents your extension from making network requests or moving sensitive content outside the extension's address space." [A10]
- Apple DTS(Quinn, 2026-03): 의도적인가? → "Yes." "The system runs such appexen in a read-only sandbox, so that the appex can't export that sensitive data to other code with a less restrictive sandbox." [A11] App Group UserDefaults 쓰기가 샌드박스 오류로 거부된다 [A13].
- Apple DTS(2026-06): "The restriction preventing third-party apps from accessing or exfiltrating detailed Screen Time data is not a missing feature; it is a fundamental, deliberate architectural decision." 부모 기기에서 자녀 상세 데이터도 "It is not possible for third-party apps to get access to privacy information." [A13]
- **iOS 26.4 신규(EU 전용)**: `com.apple.developer.family-controls.app-and-website-usage` 권한 + `approvedWithDataAccess` 상태면 `FamilyActivityData`(설치 앱 번들 ID·방문 도메인·카테고리 이름)와 `DeviceActivityData.activityData(filteredBy:using:)`("Use this method to export family activity data, for use in another app or platform")를 메인 앱에서 쓸 수 있다. 단 "Customer installations of your app can only use the method on devices located in the EU that are signed in with an Apple Account with an EU country or region. Otherwise, it throws an error." 개발·테스트는 어느 지역에서나 가능 [A12][A36][A37].
- 부작용: 이 capability를 켜면 iOS 26.4+ 사용자에게 "전체 접근 or 거부"만 보이고 '제한만 허용' 옵션이 사라진다는 보고(답변 없음) [A38]. 비-EU 스토어 빌드는 `.approved`에 머문다(커뮤니티 답변) [A39]. 카자흐스탄 개발자의 "EU 밖 공식 경로?" 질문은 2026-10 기준 무응답 [A40].
- **결론(한국 사용자)**: 사용량 숫자는 Report 확장 안의 SwiftUI 뷰로 **보여줄 수만** 있고, 앱 로직·AI·서버는 **DeviceActivity 임계치 이벤트(불투명 토큰 기준 "넘었다/안 넘었다")**만 신호로 쓸 수 있다.

#### A-6. 차단 불가 영역과 우회
- 자기 앱: "apps authorized in FamilyControls are exempt from being blocked or shielded" [A15].
- "항상 허용"에 있는 앱은 카테고리 쉴드로 안 막힘, 직접 선택 쉴드는 가능. 메시지 앱이 대표 사례 [A16]. ⚠️ 전화·설정 앱의 쉴드 가능 여부는 공식 문서·답변을 찾지 못함(iOS Screen Time 자체도 전화를 항상 허용에서 못 빼는 구조). 제품 설계는 "전화·설정은 못 막는다"로 두는 게 안전.
- 우회: 설정 > 앱 > [앱] 또는 설정 > 스크린 타임에서 접근을 끄면 즉시 모든 제한 해제. 2022~2025 내내 Screen Time 암호가 아니라 Face ID/기기 암호만 요구됐다 [A17][A41].
- **iOS 26.4 변화**: Screen Time 암호로 서드파티 앱의 Screen Time 권한 해제·앱 삭제를 잠글 수 있게 됨("Lock Screen Time Settings", 콘텐츠 및 개인정보 보호 제한 켜기). one sec 블로그 2026-03-24, 가이드 2026-04-20 [A18][A19]. 그러나 **앱별 설정 페이지에서는 여전히 Face ID로 끌 수 있는 버그**(FB18804632 등)가 보고되어 Apple이 2026-05 인지, iOS 27 공개 베타(2026-08)에서 "Enter Screen Time Passcode" 프롬프트 확인 보고, 2026-09에도 iOS 26.5/26.6.2에서 재현 보고 [A20b]. ⚠️ iOS 27 정식에서 완전 수정 여부는 직접 확인 필요.
- 앱 삭제: 개인 인증은 삭제를 막지 못함(자녀 인증만 "prevents the child user from deleting the app") [A1]. iOS 26.4 잠금 설정 시 삭제도 암호 필요 [A18].

#### A-7. 배포 권한(Family Controls Distribution) 신청
- 공식: "Before submitting your app to the App Store, you must request permission" → https://developer.apple.com/contact/request/family-controls-distribution (또는 Certificates, Identifiers & Profiles > Capability Requests). **확장(Monitor/Report/ShieldConfiguration/ShieldAction)마다 같은 요청을 따로 제출** [A21][A22].
- 개발용 권한만으로는 TestFlight·App Store 배포 프로파일에 못 넣는다(DTS 답변) [E6].
- 기간: Apple은 타임라인을 제시하지 않음("Apple doesn't provide timelines for this") [A22]. 포럼 보고: 수일~1개월, 2개월+도 있음. 2026-04~05에도 13일 무응답 등 [A23]. 확장 요청이 더 느린 경향 [A23].
- 리젝 사례는 드물고 대부분 "무응답". 신청서에는 용도를 구체적으로(개인 생산성·집중) 쓰고 Team ID·번들 ID를 준비 [A22].

#### A-8. 앱 심사 리스크
- **2.5.1(공개 API 의도된 용도)**: `blockedApplications`로 앱을 "숨긴다"고 홍보한 앱이 "your app uses ScreenTime API to hide apps" 사유로 리젹(2025-03). 문서 문구를 인용해 항소해도 유지 [A24]. 2026년에도 유사 사례, 자동 사전검사의 "Family Controls entitlement 미포함" 오탐 보고 [A25].
- **수익화 조항**: 심사지침에 "Apple services and technologies, such as Apple Music access, iCloud storage, or Screen Time APIs" 수익화 금지 문구가 있다(지침 최종 수정 2026-10-16). ⚠️ 조항 번호(3.2.2(ix) vs 4.10)는 재확인 필요 [A26]. Opal·Jomo 등 구독 앱은 존재하므로 "API 접근 자체를 파는 것"이 금지, 부가가치는 허용으로 해석되나 심사자 재량 리스크.
- 2.5.9(표준 스위치·네이티브 동작 변경 금지), 4.2(최소 기능) 일반 리스크 [A26]. 5.5(MDM)는 MDM 미사용 시 무관 [A26].
- 심사가 보는 것: 앱 설명에 Screen Time 사용을 명시("indicate that integration in their app description") [A26].

### B. iOS "첫 화면" 경험

- **위젯**: 홈·잠금화면·StandBy·인터랙티브(App Intents 버튼/토글). iOS 27: "widgets can now be customized through App Intents and dynamic styling" [B1]. 홈 화면 전체 크기 위젯(TechCrunch 2026-06-09) ⚠️ 공식 문서 미확인 [B10].
- **Live Activity**: Dynamic Island·잠금화면·StandBy·CarPlay·페어링 Watch. 8시간 상한(잠금화면 최대 12시간), 데이터 4 KB, 자체 샌드박스로 네트워크 불가, 업데이트는 앱 또는 ActivityKit 푸시. `LiveActivityIntent`로 컨트롤에서 백그라운드 시작 가능 [B2]. Watch 알림은 `AlertConfiguration`의 title/body 사용 [B2].
- **Focus**: 켜고 끄는 공개 API 없음(2021~2023 포럼 질문 무응답) [B3]. 앱이 할 수 있는 것은 (1) `SetFocusFilterIntent`로 Focus 변화에 **반응** [B4], (2) `INFocusStatus` 읽기(커뮤니케이션 알림 entitlement 전제) [B5], (3) 사용자가 단축어 앱에서 "Focus 설정" 액션과 우리 App Intent를 묶은 **자동화**를 직접 만들기. iOS 27: 단축어에서 활성 Focus 확인, "Otherwise if", Sleep Focus 트리거 추가(Engadget 2026-09-18) [B11]. ⚠️ `shortcuts://run-shortcut` URL로 단축어를 실행시키는 우회는 Shortcuts 앱이 전면에 뜬다(문서 미인용).
- **Controls / Action 버튼**: iOS 18 ControlWidget — "Controls can toggle a setting, execute an action, or deep link". Control Center·잠금화면·Action 버튼(iPhone 15 Pro+) [B6]. Life OS의 "지금 집중 시작" 토글에 적합.
- **앱 숨기기/잠그기(iOS 18)**: 시스템 기능. 설정·시계·계산기·지도는 잠금 대상 아님. 개발자 API 없음(포럼 질문 무응답) [B7]. 서드파티가 "숨기기"를 하려면 ManagedSettings `blockedApplications`뿐 → §A-8 리스크.
- **iOS 27 홈 화면**: 독립 Siri 앱 아이콘 추가, App Intents 엔티티/인텐트 스키마로 Siri가 앱 콘텐츠·액션에 접근 [B1][B12]. 홈 화면 커스터마이즈 변화 루머(MacRumors 2026-04-20) ⚠️ [B13]. Screen Time/Focus 관련 개발자 변화는 iOS 27 What's New에 **없음** [B1].
- **홈 화면 대체**: SpringBoard는 서드파티가 대체할 수 없다 [B8]. 런처 업체 문서: "iOS doesn't allow third-party launchers any chance to take over the home screen" [B9]. 가능한 최대치: (a) 잠금화면 위젯 + Live Activity로 "폰을 들면 보이는 것"을 장악, (b) 사용자가 Focus 모드에 **특정 홈 페이지만 표시**(iOS 16 기능)로 묶어 두고 우리 앱이 Focus Filter로 내용을 바꿈, (c) 홈 1페이지를 우리 큰 위젯만으로 구성하도록 온보딩 안내. 앱 아이콘 배치·페이지 전환·독(Dock)은 **불가**.

### C. Apple Watch

#### C-1. 앱 모델과 "폰 없이" 시나리오
- 컴패니언/독립(watchOS 6+) 앱 모두 가능. 셀룰러 모델은 iPhone 없이 통화·메시지·Siri 가능 [C4].
- **핵심 제한**: "To receive SMS, MMS, or push notifications from third-party apps on your cellular Apple Watch, your paired iPhone must be powered on and connected to Wi-Fi or cellular, but it doesn't need to be nearby." "Some apps might not update without a connection to your iPhone." [C4] 즉 **폰을 집에 켜 두고** 나가면 서드파티 푸시는 받지만, 폰을 꺼두면 못 받는다.
- watchOS 27 호환: SE 3, Series 9~12, Ultra 2~4. "watchOS 27 requires iPhone 11 or later or iPhone SE (2nd generation or later) with iOS 27." [C12]. 워치 Siri AI는 Apple Intelligence 지원 iPhone이 필요 [C13].
- watchOS 27 홈 화면은 "Dynamic App Grid"(Siri 제안 앱 자동 강조, Siri 앱이 중앙) — 서드파티가 바꿀 수 없음 [C13].

#### C-2. 워치에서 폰 차단을 트리거하기
- FamilyControls·ManagedSettings·DeviceActivity는 **watchOS 미지원**(가용 플랫폼 목록에 없음) [A3][A4][A10][A14]. 차단 상태 변경은 **반드시 iPhone 측 코드**가 실행돼야 한다.
- 경로 1 — **Watch Connectivity**: `sendMessage`는 `isReachable == true`일 때만. `transferUserInfo`/`updateApplicationContext`는 큐에 들어가 "transfers continue when the current app is suspended or terminated", 단 "background transfers are not delivered immediately" [C1]. 폰과 워치가 BT/같은 Wi‑Fi로 연결돼 있어야 하고, 폰 앱이 백그라운드에서 깨어나 `ManagedSettingsStore`를 쓸 수 있는지 ⚠️ 실기기 검증 필요(DeviceActivityMonitor 확장이 백그라운드에서 스토어를 쓰는 것은 공식 패턴이라 가능성은 높음) [A31].
- 경로 2 — **워치 Control Center 컨트롤(watchOS 26+)**: iPhone의 컨트롤이 워치에 자동 등장하고 "When you tap on one of these controls, the action is performed on the companion iPhone." 앱을 전면으로 띄우는 컨트롤은 워치에 안 뜸. Smart Stack·Action 버튼(Ultra)에도 배치 가능 [C2][C6]. → "집중 시작" 토글의 가장 자연스러운 구현. ⚠️ 폰이 비연결 상태일 때의 동작은 문서 미확인.
- 경로 3 — **서버 경유 사일런트 푸시**: 워치(셀룰러/Wi‑Fi)→서버→iPhone APNs `content-available`. 그러나 "the system doesn't guarantee their delivery ... don't try to send more than two or three per hour", 30초 실행 [C3][D9]. 긴급 차단용으로는 부적합, "오늘 계획 갱신" 정도.
- 경로 4 — **iPhone 측 자동화**: 사용자가 단축어 "집을 떠날 때(위치)" 자동화에 우리 App Intent를 연결. 워치가 필요 없고 가장 확실 ⚠️(자동화 트리거별 확인 프롬프트 유무는 미검증).

#### C-3. 워치 표면(컴플리케이션·Smart Stack·알림)
- WidgetKit 기반 컴플리케이션/Smart Stack 위젯(watchOS 10+). watchOS 11: `RelevantContext`(시간·위치·수면·운동)로 자동 등장 [C5]. watchOS 26: Smart Stack이 Controls·Widgets·Live Activities 지원, 새 관련성 위젯 [C6]. watchOS 27: Smart Stack 단일 탭 선택, 관련 위젯 더 많이 표시 [C12].
- iPhone Live Activity는 워치 Smart Stack에 자동 표시("even if you don't have a watchOS app yet", `.small` 보조 패밀리 선언) [C5][B2].
- 알림: 워치 로컬 알림·미러링 가능. "조용한 화면"으로서 워치는 **보여주기**는 되지만 폰의 앱을 **막지는** 못한다.
- Double Tap: watchOS 11 API로 앱이 기본 동작 지정 [C10]. Action 버튼: Ultra 전용, 컨트롤/운동 앱으로 연결 [C6].

#### C-4. HealthKit과 백그라운드
- 읽을 수 있는 것: 심박, HRV(`heartRateVariabilitySDNN`), 수면 단계(`sleepAnalysis`: core/deep/REM/awake, watchOS 9+), 서있기(`appleStandTime`, `appleStandHour`), 운동, 활동 링 [C7].
- 백그라운드 전달: iOS 15/watchOS 8+ `com.apple.developer.healthkit.background-delivery` 권한 필수. "In watchOS, most data types have an hourly maximum frequency." "Your app can receive four updates (or background app refresh tasks) an hour, as long as it has a complication on the active watch face." 시뮬레이터 미지원 [C9]. DTS: 빈도는 보장되지 않음 [C14].
- 워치 앱 리프레시: "approximately four tasks per hour for each app with a complication on the active watch face" [C8]. watchOS 26에서 리프레시가 안 온다는 보고 ⚠️ [C15].
- 함의: 워치는 **수면·HRV·활동**을 하루 몇 번 모아 iPhone 플래너에 넘기는 "컨텍스트 센서" 역할이 현실적. 실시간 반응은 운동 세션 중에만.

#### C-5. 워치 AI
- Apple 호환표: Apple Watch(SE 3, S9+, Ultra 2+)는 AFM Core/Core Advanced **❌**, AFM Cloud(PCC) ✅. "A compatible Apple Watch must be paired with an Apple Intelligence-enabled iPhone to run AFM Cloud." [D4]
- Apple 엔지니어(2026-06): watchOS 27 Foundation Models는 "does not work offline. It requires a network connection." "does not require a nearby iPhone." Core AI는 "does work offline because the models are running on device" [C11].

### D. 조용한 백그라운드 AI

#### D-1. Foundation Models 프레임워크
- iOS 26+(온디바이스), iOS 27+(PCC·`LanguageModel` 프로토콜·이미지 입력·Dynamic Profiles) [D1][D6].
- 모델: AFM Core 3B(4~8K 컨텍스트), AFM Core Advanced 20B 희소(8K), AFM Cloud(32K). 기기: iPhone 15 Pro·16 전체·17 → Core; iPhone Air·17 Pro → iOS 27에서 Core Advanced; iPhone 18 Pro·Duo → Core Advanced만 [D4]. 앱은 모델을 선택할 수 없고 `variant`/`contextSize`로 감지 [D4].
- Apple 엔지니어(2026-06): "The on-device model has a 4K context window." 토큰은 `usage`·Instruments로 측정, Dynamic Instructions로 압축. 백그라운드: "we might limit access to the model on background tasks ... excessive requests to the model made in the background might be throttled or canceled." [D2] 2026-02 업데이트: `tokenCount(for:)`, `contextSize`, Playground에서 "4,096 tokens" 추정 표시 [D3].
- 용도: 요약·엔티티 추출·분류·텍스트/이미지 이해·창작, 가이드 생성(`@Generable`)·도구 호출(`Tool`). "When you need more reasoning capabilities and context size, use Private Cloud Compute" [D1]. 온디바이스는 "Reasoning: Not supported", "Works offline ✅", "Usage limits: Unlimited" [D5].
- **한국어**: Apple Intelligence 지원 언어 목록에 한국어 포함 [D11]. 한국어는 iOS 18.4(2025-03-31)에서 추가 [D12]. 가용 판단은 시스템 언어가 아니라 **Siri/Apple Intelligence 언어**(Apple 답변 2025-11), `supportsLocale()`로 앱별 확인 [D13][D14]. 가드레일은 지원 언어에만 동작 [D14]. iOS 27부터 Apple Intelligence는 기본 켜짐·끌 수 없음 [D4].
- 비용: 온디바이스 0. PCC는 "daily request limit", iCloud+로 증량, `com.apple.developer.private-cloud-compute` 관리형 권한 필요, 네트워크 필수 [D5]. Small Business Program + 첫 다운로드 200만 미만이면 "at no cloud API cost" [D6]. `LanguageModel` 프로토콜로 Claude/Gemini 등 외부 모델도 같은 API로 연결 가능(비용은 해당 제공자) [D1][D6].
- Core AI(iOS 27 신규): 커스텀 온디바이스 모델 실행 프레임워크, 워치 포함 오프라인 [D6][C11]. Core ML은 계속 사용 가능.

#### D-2. 백그라운드 실행 제약
- `BGAppRefreshTask`: "The system decides the best time to launch your background task, and provides your app up to 30 seconds of background runtime." [D7]
- `BGProcessingTask`: "processing tasks can run for minutes, the system can interrupt" "Processing tasks run only when the device is idle. The system terminates any background processing tasks running when the user starts using the device." 야간 충전 중 ML 학습 등 용도 [D8]. iOS 26/Xcode 26.1.1에서 예약 시각에 안 뜬다는 보고 ⚠️ [D15].
- `BGContinuedProcessingTask`(iOS 26): 전면에서 시작한 **사용자 가시** 작업을 백그라운드에서 이어감. 백그라운드에서 CPU/ML 4~5배 느림, 예측 불가 만료 보고 [D10].
- 사일런트 푸시: 우선순위 5, 30초, "don't try to send more than two or three per hour", 시스템이 보류·병합·폐기 가능 [D9][C3].
- 추가 웨이크업 소스: DeviceActivityMonitor 콜백(6 MB, LLM 불가) [A32], HealthKit 백그라운드 전달(iOS는 타입별 시간당 1회 등) [C9], WCSession 수신 [C1].

#### D-3. "조용한 에이전트"가 실제로 할 수 있는 것
- **가능**: (1) 야간 충전 중 `BGProcessingTask`에서 온디바이스 LLM으로 내일 계획·메시지 생성(수 분, 중단 대비 체크포인트) [D8]; (2) 앱 실행·위젯 타임라인 갱신 시점의 짧은 추론(30초 이내) [D7]; (3) DeviceActivity 임계치 콜백 → 쉴드 적용 + 미리 생성해 둔 문구를 쉴드 부제에 표시 [A31]; (4) 사용자가 쉴드 버튼을 눌렀을 때 ShieldAction 확장에서 즉시 반응(메모리 한도 유의).
- **불가**: 상시 실행 루프, 사용량 숫자 기반 실시간 개입(데이터 접근 불가), 사용자가 안 여는 앱에서 "하루 종일 관찰".
- 설계 원칙: AI는 **배치형**(밤에 생각하고 낮에 보여줌), 낮의 개입은 **사전 계산된 콘텐츠 + 시스템 트리거**로.

### E. 개발 환경

- Xcode 27(2026-09-14), 27.1: macOS Tahoe 26.6+, Swift 6.4, iOS 27.1/watchOS 27 SDK, 기기 디버깅 iOS 17+/watchOS 10+ [E1][E3]. Apple silicon 전용(macOS 26이 Intel 마지막) → **M1 Max 적합** [E2]. ⚠️ macOS 27(Golden Gate) M1 지원 여부는 미확인이나 Xcode 27은 26.6으로 충분.
- Xcode 27은 "Device Hub"가 Simulator를 대체(뉴스룸 2026-06-08) [E8]; 27.1 릴리스 노트에는 "Simulator" 항목이 여전히 있음 [E3]. watchOS 시뮬레이터/가상 기기는 제공되지만 **FamilyControls·HealthKit 백그라운드는 실기기 필수** [E7][C9].
- Apple Developer Program US$99/년, TestFlight 내부 100·외부 10,000, watchOS 베타 SDK 포함 [E5]. 무료 계정은 TestFlight 불가 [E5].
- Family Controls 배포 권한 신청: https://developer.apple.com/contact/request/family-controls-distribution 또는 Capability Requests 탭. 앱 + 각 확장 별도 신청. 승인 시 "Family Controls (Distribution)"가 Assigned 표시 [A21][A22]. 승인 전엔 TestFlight도 불가 [E6].
- PCC 사용 시 별도 관리형 권한 신청(https://developer.apple.com/private-cloud-compute/) [D5].
- 2027-04부터 iOS 27/watchOS 27 SDK 빌드 필수 [E4].

### F. 선행 사례

- **Opal**: 원래 VPN 방식 → iOS 16에서 ManagedSettings로 전환(TechCrunch 2022-09-15) [F4]. 공개 로드맵에 "keep hardening background blocking so every Rule applies on time ... without opening Opal", "apps getting stuck blocked" 해결 과제 [F5]. 커뮤니티: 2022-12 "Currently, it requires the device passcode"로 우회 가능, Opal 측 "74 open tickets with them about the API"(Apple) [F6]. iOS 26.4 이후 "Screen Time Passcode will be required to delete Opal, or to disable Opal's access" 가이드 제공, 구버전은 단축어 자동화로 설정 앱 열기 차단 [F7].
- **one sec**: "DELAYS access to distracting apps instead of blocking", 재개입 타이머, Focus 연동, Screen Time API 연결 [F8]. 2026-03-24 블로그: iOS 26.4 Screen Time Lock으로 "lock the screen time permission for one sec" + 삭제 방지 [A18]. 단축어 자동화 우회는 별도 "Shortcuts App Lock" 필요 [A18].
- **Jomo**: Screen Time API 기반, 과차단 시 "settings > screen time > always allowed" 안내 [F9].
- **Brick**: NFC 태그 + 앱, "temporarily removes distracting apps (and their notifications)" — ManagedSettings 사용은 추정 ⚠️ [F10].
- **ScreenZen**: 기본 30초 대기 후 해제(경쟁사 블로그 평가, 편향 주의) [F11].
- 사용자 불만 공통: "default app time limits are too easy to bypass", 기기 간 합산 불가, 버그·지연 [F5][F6].
- 오픈소스: `krypted/DeviceActivityExample`(35★, Monitor 확장 자리표시자 수준) [F1]; `kingstinct/react-native-device-activity`(RN 래퍼) [F2]; `dpowers/ScreenPact`(최소 예제) [F3]; Apple WWDC21 "Homework"·WWDC22 "Worklog" 샘플 [F12]; `apple/foundation-models-utilities`, `apple/coreai-models` [D6]. 완성형 Swift 오픈소스 차단 앱은 없음.

---

## 3. Life OS에 뜻하는 것

### 3-1. 첫 iPhone + Watch 제품이 현실적으로 될 수 있는 모습
1. **온보딩**(iPhone): Family Controls 승인(Face ID 1회) → `FamilyActivityPicker`로 "조용히 할 앱" 선택 → iOS 26.4+면 "Screen Time 설정 잠금"을 안내(암호는 가족/친구가 설정). 홈 화면 1페이지를 Life OS 큰 위젯만으로 구성하고, Focus 모드에 그 페이지만 연결하도록 **사용자 손으로** 설정 안내(앱이 대신 못 함).
2. **조용한 폰**: `DeviceActivitySchedule`로 시간대 쉴드 + 임계치 이벤트. 쉴드 화면은 아이콘·제목·부제·버튼 2개로 "지금 대신 할 1가지"를 표시(부제에 밤에 생성해 둔 문장). `blockedApplications`(숨기기)는 **쓰지 않거나 "숨기기"로 홍보하지 않음**.
3. **첫 화면**: 잠금화면 위젯 + 홈 위젯 + 집중 세션 중 Live Activity(워치 Smart Stack 자동 표시). Control Center/Action 버튼 컨트롤로 세션 시작/종료.
4. **워치**: 컴플리케이션·Smart Stack 위젯에 "지금 할 일 1개"; 워치 Control Center의 컨트롤(액션은 iPhone 실행)로 세션 토글; HealthKit(수면·HRV·서있기)을 하루 몇 번 모아 플래너 입력. 워치는 **표시·입력 장치**, 차단은 **iPhone이 실행**.
5. **AI**: 온디바이스 Foundation Models(한국어, iPhone 15 Pro+), 야간 `BGProcessingTask`로 내일 계획·쉴드 문구·위젯 문구 일괄 생성 → 낮에는 시스템 트리거가 미리 만든 콘텐츠를 꺼냄. 사용량 "숫자"는 Report 확장 안에서만 보여주고, 로직은 임계치 이벤트와 세션 로그(자체 기록)로.
6. **채팅 UI 없음**은 제약과 잘 맞는다: 쉴드·위젯·Live Activity·컴플리케이션이 곧 UI.

### 3-2. 최대 리스크 3가지
1. **승인·심사 게이트**: Family Controls 배포 권한은 기간이 없고(수일~2개월+), 앱+확장 4종 모두 별도 승인. 승인 전엔 TestFlight도 못 돈다. 심사에서는 "앱 숨기기" 표현과 Screen Time API 수익화 조항이 지뢰. → 신청을 **첫 주에** 내고, 승인 대기 중 Screen Time 없이 동작하는 위젯·워치·AI 부분을 먼저 만든다. 유료화는 "AI 플래너"에 두고 차단 기능을 단독 판매처럼 쓰지 않는다.
2. **데이터 봉인(비-EU)**: 사용량 데이터는 한국에서 메인 앱·서버에 못 들어온다. "AI가 사용 패턴을 보고 시간을 되돌린다"는 서술은 **임계치 이벤트 + 자체 세션 로그 + HealthKit 컨텍스트**로 다시 써야 한다. DeviceActivity 콜백 자체도 불안정하다는 보고가 iOS 26.x까지 이어진다.
3. **우회 가능성과 워치의 무력함**: 사용자는 설정에서 끌 수 있고(iOS 26.4 잠금은 버그 이력), 개인 인증은 앱 삭제를 막지 못한다. 워치는 어떤 차단도 직접 못 하며, 폰이 꺼져 있으면 서드파티 푸시도 못 받는다. "폰을 집에 두고 워치로 산다"는 폰을 **켜 둔 채** 두는 시나리오로 한정해야 하고, 핵심 가치는 강제보다 **마찰 + 대안 제시**에 둬야 한다.

부가 리스크: Foundation Models 기기 조건(iPhone 15 Pro 이상)이 한국 사용자층을 줄인다 → 비호환 기기는 규칙 기반 폴백 또는 PCC(iOS 27, 쿼터) 설계가 필요하다 [D4][D5].

---

## 4. 출처

접근일은 모두 2026-11-23. 괄호 안은 문서/게시물 자체의 날짜.

**A. Screen Time API**
- [A1] Apple, Family Controls 프레임워크 문서 — https://developer.apple.com/documentation/familycontrols
- [A2] Apple, AuthorizationCenter — https://developer.apple.com/documentation/familycontrols/authorizationcenter ; requestAuthorization(for:) iOS 16.0+ — https://developer.apple.com/documentation/familycontrols/authorizationcenter/requestauthorization(for:)
- [A3] 위 AuthorizationCenter 페이지 가용성 메타데이터(iOS/iPadOS 15.0+만)
- [A4] Apple, Managed Settings 프레임워크(iOS/iPadOS 15+, macCatalyst) — https://developer.apple.com/documentation/managedsettings
- [A5] Apple, ShieldSettings.applications("up to 50 application tokens") — https://developer.apple.com/documentation/managedsettings/shieldsettings/applications-swift.property ; ShieldSettings — https://developer.apple.com/documentation/managedsettings/shieldsettings
- [A6] Apple, ShieldSettings.webDomains(50 토큰) — https://developer.apple.com/documentation/managedsettings/shieldsettings/webdomains-swift.property
- [A7] Apple, ShieldConfiguration — https://developer.apple.com/documentation/managedsettingsui/shieldconfiguration
- [A8] Apple, ShieldAction — https://developer.apple.com/documentation/managedsettings/shieldaction
- [A9] Apple, AuthorizationStatus — https://developer.apple.com/documentation/familycontrols/authorizationstatus
- [A10] Apple, DeviceActivityReport(샌드박스 문구, iOS 16+) — https://developer.apple.com/documentation/deviceactivity/deviceactivityreport
- [A11] Apple Developer Forums, "Is Screen Time trapped inside DeviceActivityReport on purpose?" DTS 답변(2026-03) — https://developer.apple.com/forums/thread/817516
- [A12] Apple Developer Forums, "Token Selection from DeviceActivityReport"(번들 ID 불가) — https://developer.apple.com/forums/thread/729818 ; "Access Screen Time total usage from main app…" DTS(2026-03) — https://developer.apple.com/forums/thread/818174
- [A13] Apple Developer Forums, 자녀 기기 상세 데이터 요청에 대한 DTS 답변(2026-06) — https://developer.apple.com/forums/thread/834794 ; App Group 쓰기 거부 — https://developer.apple.com/forums/thread/818297
- [A14] Apple, Device Activity 프레임워크 — https://developer.apple.com/documentation/deviceactivity ; macCatalyst 외 미지원 포럼(2022) — https://developer.apple.com/forums/thread/721295
- [A15] Apple Developer Forums, 자기 앱 쉴드 불가 — https://developer.apple.com/forums/thread/783310
- [A16] Apple Developer Forums, "ManagedSettings can't shield Messages and other system apps"(2023-05) — https://developer.apple.com/forums/thread/729891
- [A17] Apple Developer Forums, "Changing Screen Time Passcode does not protect apps…"(2022-09~2025-05) — https://developer.apple.com/forums/thread/714651
- [A18] one sec 블로그, "Making one sec truly bullet-proof with iOS 26.4"(2026-03-24) — https://one-sec.app/blog/lock-screen-time-permission/
- [A19] Tech Lockdown, iOS 26.4 Screen Time-protected app permissions(2026-04-20 업데이트) — https://techlockdown.com/articles/ios26-update-screen-time-protected-app-permissions
- [A20] Apple Developer Forums, "No way to detect revocation while app is backgrounded"(2026-03, iOS 26.2) — https://developer.apple.com/forums/thread/820796
- [A20b] Apple Developer Forums, "iOS 26.4 asks for Face ID instead of Screen Time passcode…"(2026-04~09, Apple 인지 2026-05) — https://developer.apple.com/forums/thread/821959
- [A21] Apple, Requesting the Family Controls entitlement — https://developer.apple.com/documentation/familycontrols/requesting-the-family-controls-entitlement ; 신청 폼 — https://developer.apple.com/contact/request/family-controls-distribution
- [A22] Apple Developer Forums, "Family Controls Request Form"(DTS: 확장별 신청, 타임라인 없음) — https://developer.apple.com/forums/thread/735888
- [A23] Apple Developer Forums 대기 보고: https://developer.apple.com/forums/thread/809190 , https://developer.apple.com/forums/thread/826427 (2026-05, 13일 무응답), https://developer.apple.com/forums/thread/812332 , https://developer.apple.com/forums/thread/820811 , https://developer.apple.com/forums/thread/744094 (4주+), https://developer.apple.com/forums/thread/809208 (확장 2주+)
- [A24] Apple Developer Forums, "Using ManagedSettings.ApplicationSettings.blockedApplications to 'hide' apps is causing App Review rejection"(2025-03) — https://developer.apple.com/forums/thread/776058
- [A25] Apple Developer Forums 2026 리젹/오탐: https://developer.apple.com/forums/thread/838802 , https://developer.apple.com/forums/thread/822078 , https://developer.apple.com/forums/thread/858509 , https://developer.apple.com/forums/thread/749466
- [A26] Apple, App Review Guidelines(최종 수정 2026-10-16; 2.5.1, 2.5.9, 4.2, 5.5, Screen Time API 수익화 문구) — https://developer.apple.com/app-store/review/guidelines/
- [A27] Apple Developer Forums, Family Controls 태그(iOS 27 blockedApplications 위치 회귀 보고) — https://origin-devforums.apple.com/forums/tags/family-controls
- [A28] Apple Developer Forums, "blockedByFilter = .specific(domains) does not work on Safari"(iOS 26) — https://developer.apple.com/forums/thread/805209 ; WebDomainToken 출처 — https://developer.apple.com/forums/thread/735923
- [A29] Apple, ShieldActionResponse(⚠️ 직접 미열람) — https://developer.apple.com/documentation/managedsettings/shieldactionresponse
- [A30] Apple Developer Forums, 쉴드 커스터마이즈 한계 질문(무응답) — https://developer.apple.com/forums/thread/803432
- [A31] Apple, DeviceActivityMonitor — https://developer.apple.com/documentation/deviceactivity/deviceactivitymonitor
- [A32] Apple Developer Forums, "Memory limit for Device Activity Monitor Extension"(6 MB) — https://developer.apple.com/forums/thread/735454
- [A33] Apple Developer Forums, "eventDidReachThreshold triggers immediately" — https://developer.apple.com/forums/thread/728277
- [A34] Apple Developer Forums, iOS 26 조기 임계치 콜백 워크어라운드 질문 — https://developer.apple.com/forums/thread/840907
- [A35] Apple Developer Forums, "Device Activity Monitor Extension behaves randomly" — https://developer.apple.com/forums/thread/751597
- [A36] Apple, FamilyActivityData(iOS 26.4+, EU 전용) — https://developer.apple.com/documentation/familycontrols/familyactivitydata
- [A37] Apple, DeviceActivityData.activityData(filteredBy:using:)(iOS 26.4+, EU 전용) — https://developer.apple.com/documentation/deviceactivity/deviceactivitydata/activitydata(filteredby:using:) ; 권한 — https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.family-controls.app-and-website-usage
- [A38] Apple Developer Forums, "iOS 26.4: No way for users to only approve Screen Time without data access?"(2026-03) — https://developer.apple.com/forums/thread/820283
- [A39] Apple Developer Forums, App Store 빌드가 .approved에 머무름(2026-09) — https://developer.apple.com/forums/thread/844541
- [A40] Apple Developer Forums, "Is there any official way for a Screen Time app outside the EU…"(2026-10, 무응답) — https://developer.apple.com/forums/thread/849296
- [A41] Apple Developer Forums, "Screen time API can be disabled easily" — https://developer.apple.com/forums/thread/727291

**B. 첫 화면**
- [B1] Apple, What's new in iOS 27(WWDC26) — https://developer.apple.com/ios/whats-new/
- [B2] Apple, Displaying live data with Live Activities — https://developer.apple.com/documentation/activitykit/displaying-live-data-with-live-activities
- [B3] Apple Developer Forums, "iOS api for enable disable focus mode programmatically" — https://developer.apple.com/forums/thread/693444 ; macOS 동일 질문 — https://developer.apple.com/forums/thread/729475
- [B4] Apple, App Intents > Focus(SetFocusFilterIntent) — https://developer.apple.com/documentation/appintents/focus
- [B5] Apple, INFocusStatus — https://developer.apple.com/documentation/intents/infocusstatus ; 포럼 — https://developer.apple.com/forums/thread/682143
- [B6] Apple WWDC24, "Extend your app's controls across the system"(10157) — https://developer.apple.com/videos/play/wwdc2024/10157/ ; 9to5Mac(2024-06-10) — https://9to5mac.com/2024/06/10/developers-toggles-control-center-ios-18
- [B7] Apple Developer Forums, "Can locked/hidden apps in iOS 18 be tracked by developers?" — https://developer.apple.com/forums/thread/764880 ; MacRumors 잠금 제외 앱 — https://www.macrumors.com/how-to/ios-18-how-to-lock-and-hide-iphone-apps/
- [B8] 9to5Mac, 홈 화면 대체 불가 보도 — https://9to5mac.com/?p=349588 ; Wikipedia SpringBoard — https://en.wikipedia.org/wiki/SpringBoard
- [B9] Smart Launcher 문서(iPhone) — https://docs.smartlauncher.net/device-specific-issues/apple-iphone
- [B10] TechCrunch, iOS 27 features(2026-06-09) — https://techcrunch.com/2026/06/09/ios-27-features-we-didnt-see-on-stage/
- [B11] Engadget, "How Focus mode has changed in iOS 27"(2026-09-18) — https://www.engadget.com/2257610/how-focus-mode-changed-ios-27/
- [B12] 9to5Mac, iOS 27 Siri 앱(2026-09-21) — https://9to5mac.com/2026/09/21/ios-27-adds-an-all-new-app-to-your-iphones-home-screen/ ; Apple 지원, New in iOS 27 — https://support.apple.com/en-us/149045
- [B13] MacRumors, iOS 27 홈 화면 커스터마이즈 루머(2026-04-20) — https://www.macrumors.com/2026/04/20/ios-27-customize-your-iphone-home-screen/

**C. Apple Watch**
- [C1] Apple, WCSession — https://developer.apple.com/documentation/watchconnectivity/wcsession
- [C2] Apple WWDC25, "What's new in watchOS 26"(334) — https://developer.apple.com/videos/play/wwdc2025/334 ; MacRumors(2025-06-15) — https://www.macrumors.com/2025/06/15/watchos-26-custom-controls/
- [C3] Apple, Pushing background updates to your App — https://developer.apple.com/documentation/usernotifications/pushing-background-updates-to-your-app
- [C4] Apple 지원, "Use your Apple Watch without your iPhone nearby" — https://support.apple.com/en-om/108300 ; 셀룰러 설정 — https://support.apple.com/en-euro/119601
- [C5] Apple WWDC24, "What's new in watchOS 11"(10205) — https://developer.apple.com/videos/play/wwdc2024/10205/
- [C6] Apple, What's new in watchOS 26(Control Widget API) — https://developer.apple.com/watchos/whats-new (현재는 watchOS 27 페이지로 갱신됨; WWDC25 334 참조)
- [C7] Apple, HealthKit data types — https://developer.apple.com/documentation/healthkit/data-types ; HKCategoryValueSleepAnalysis — https://developer.apple.com/documentation/healthkit/hkcategoryvaluesleepanalysis ; WWDC22 10005 — https://developer.apple.com/wwdc22/10005
- [C8] Apple, WKApplicationRefreshBackgroundTask(≈4회/h) — https://developer.apple.com/documentation/watchkit/wkapplicationrefreshbackgroundtask
- [C9] Apple, enableBackgroundDelivery(for:frequency:withCompletion:) — https://developer.apple.com/documentation/healthkit/hkhealthstore/enablebackgrounddelivery(for:frequency:withcompletion:)
- [C10] Apple, Enabling the double-tap gesture on Apple Watch(watchOS 11) — https://developer.apple.com/documentation/watchos-apps ; WWDC24 10205 [C5]
- [C11] Apple Developer Forums, "Foundation Models + Core AI on watchOS"(Apple 엔지니어, 2026-06) — https://developer.apple.com/forums/thread/831742
- [C12] Apple, watchOS 27 제품 페이지(호환 기기·iPhone 11+/iOS 27) — https://www.apple.com/watchos/ ; Apple 지원, What's new in watchOS 27 — https://support.apple.com/en-us/guide/watch/apdb93ea3872/watchos ; About watchOS 27 Updates(2026-11-09) — https://support.apple.com/en-us/149077
- [C13] 위 Apple 지원 "What's new in watchOS 27"(Dynamic app grid, Siri AI 요구사항)
- [C14] Apple Developer Forums, "Clarification on HealthKit Observer Delivery Frequency…"(DTS: 보장 없음) — https://developer.apple.com/forums/thread/823699
- [C15] Apple Developer Forums, "Has Background Refresh Stopped Working on watchOS 26?" — https://developer.apple.com/forums/thread/797459

**D. AI·백그라운드**
- [D1] Apple, Foundation Models 프레임워크 — https://developer.apple.com/documentation/foundationmodels
- [D2] Apple Developer Forums, "On-device model capabilities, limits, and versioning"(Apple Frameworks Engineer, 2026-06) — https://developer.apple.com/forums/thread/833642
- [D3] Apple, Foundation Models updates(2026-02/03/06) — https://developer.apple.com/documentation/Updates/FoundationModels
- [D4] Apple, Detecting available Apple Foundation Models(모델·기기 표) — https://developer.apple.com/documentation/foundationmodels/detecting-available-apple-foundation-models
- [D5] Apple, Adding server-side intelligence with Private Cloud Compute — https://developer.apple.com/documentation/foundationmodels/adding-server-side-intelligence-with-private-cloud-compute ; PCC 접근 — https://developer.apple.com/private-cloud-compute/
- [D6] Apple Newsroom(2026-06-08), 새 인텔리전스 프레임워크 — https://www.apple.com/newsroom/2026/06/apple-aids-app-development-with-new-intelligence-frameworks-and-advanced-tools/ ; What's new in iOS 27 [B1]
- [D7] Apple, Choosing Background Strategies for Your App — https://developer.apple.com/documentation/backgroundtasks/choosing-background-strategies-for-your-app
- [D8] Apple, BGProcessingTask — https://developer.apple.com/documentation/backgroundtasks/bgprocessingtask
- [D9] [C3]와 동일 + [D7]
- [D10] Apple, BGContinuedProcessingTask — https://developer.apple.com/documentation/backgroundtasks/bgcontinuedprocessingtask ; 포럼 — https://developer.apple.com/forums/thread/807957 , https://developer.apple.com/forums/thread/805554
- [D11] Apple 지원, How to get Apple Intelligence(지원 언어·기기) — https://support.apple.com/en-us/121115
- [D12] Apple Newsroom(KR, 2025-03) — https://www.apple.com/kr/newsroom/2025/03/apple-intelligence-features-are-now-available-in-korean/
- [D13] Apple Developer Forums, "Foundation Models unavailable … device language restriction"(Apple 답변 2025-11) — https://developer.apple.com/forums/thread/805378
- [D14] Apple, Supporting languages and locales with Foundation Models — https://developer.apple.com/documentation/foundationmodels/supporting-languages-and-locales-with-foundation-models
- [D15] Apple Developer Forums, "BGProcessingTask Not Triggering … Xcode 26.1.1" — https://developer.apple.com/forums/thread/812932

**E. 개발 환경**
- [E1] Apple Developer News, Xcode 27 릴리스(2026-09-14) — https://developer.apple.com/news/releases/?id=09142026h
- [E2] Apple Developer News, App Store submissions open(iOS 27…, Intel 종료 안내) — https://developer.apple.com/news/?id=k1mtkt1k ; Blake Crosley, "Xcode 27 Is Out" — https://blakecrosley.com/blog/xcode-27-release
- [E3] Apple, Xcode 27.1 Release Notes — https://developer.apple.com/documentation/xcode-release-notes/xcode-27_1-release-notes
- [E4] MacTech(2026-09-10) — https://www.mactech.com/2026/09/10/app-store-submissions-now-open-for-the-latest-os-releases/amp/ ; 9to5Mac(2026-09-10) — https://9to5mac.com/2026/09/10/apple-now-accepting-app-store-submissions-built-for-ios-27-macos-27-more/
- [E5] Apple Developer Program, What's included — https://developer.apple.com/programs/whats-included/
- [E6] Apple Developer Forums, Family Controls 개발 권한으로 TestFlight 불가(DTS) — https://developer.apple.com/forums/thread/735888 ; https://developer.apple.com/forums/thread/784112
- [E7] Apple Developer Forums, Family Controls 태그(시뮬레이터 개인 인증 실패 보고) — https://developer.apple.com/forums/tags/family-controls?page=2 ⚠️
- [E8] Apple Newsroom(2026-06-08) [D6], "The all-new Device Hub replaces Simulator"

**F. 선행 사례**
- [F1] GitHub, krypted/DeviceActivityExample — https://github.com/krypted/DeviceActivityExample
- [F2] kingstinct/react-native-device-activity — https://context7.com/kingstinct/react-native-device-activity
- [F3] Apple Developer Forums, dpowers/ScreenPact 언급 — https://developer.apple.com/forums/thread/695651
- [F4] TechCrunch(2022-09-15), Opal의 ManagedSettings 전환 — https://techcrunch.com/2022/09/15/opal-revamps-its-screen-time-app-to-help-anyone-not-just-parents-with-kids-focus-and-avoid-distractions/
- [F5] Opal App Store 페이지·리뷰 — https://apps.apple.com/us/app/1497465230 ; Opal 도움말 — https://help.opalapp.com/v3/how-is-opal-different-from-screen-time-settings-on-ios
- [F6] Opal 커뮤니티, "How do we prevent turning off 'Screen Time Access'?"(2022-12~2023-02, 제목 "[SOLVED on iOS 26.4]") — https://community.opalapp.com/t/how-do-we-prevent-turning-off-screen-time-access-solved/488
- [F7] Opal 도움말, "How To Lock Opal's Screen Time Access" — https://help.opalapp.com/article/how-to-lock-opals-screen-time-access ; 부모 통제 — https://help.opalapp.com/article/how-to-use-parental-controls
- [F8] one sec App Store(KR) — https://apps.apple.com/KR/app/id1532875441 ; 9to5Mac Indie Spotlight(2024-12-21) — https://9to5mac.com/2024/12/21/indie-app-spotlight-one-sec/
- [F9] Jomo App Store — https://apps.apple.com/PH/app/id1609960918
- [F10] WhistleOut, Brick 리뷰 — https://www.whistleout.com/CellPhones/Guides/brick-for-phone-limits-app-review ; Brick App Store — https://apps.apple.com/app/id6448794069
- [F11] Habit Doom 블로그(경쟁사, 편향) — https://habitdoom.com/blog/doomscrolling-apps-no-bypass-2026
- [F12] Apple WWDC21 "Meet the Screen Time API"(10123) — https://developer.apple.com/wwdc21/10123 ; WWDC22 "What's new in Screen Time API"(110336) — https://developer.apple.com/videos/play/wwdc2022/110336/ ; levelup.gitconnected 글 — https://levelup.gitconnected.com/swiftui-monitor-restrict-device-activity-our-own-device-or-family-devices-a6d2f9fe254a
