# iPhone 차단 앱 시장·iOS 제약 조사 (2026-11-23)

조사 범위: Life OS(iPhone 첫 플랫폼)가 "폰을 조용히 만들고 + 남은 시간을 목표로 돌려주는" 일을 하려 할 때, 서드파티 iOS 앱이 실제로 할 수 있는 것/없는 것, 그리고 기존 앱들의 가격·규모·불만.

표기: 숫자마다 출처 URL과 확인 날짜를 붙였다. 확인하지 못한 것은 **모름/못 찾음**. Reddit은 크롤러 차단으로 직접 열지 못했고, Reddit을 인용한 2차 자료(Norton, Habit Doom 등)로 대체했다. Habit Doom, Unstar, Autonomous, Habit Doom 류 블로그는 **경쟁 제품 판매사**라 판단에 가중치를 낮췄다.

---

## A-1. Screen Time API — 서드파티 앱이 할 수 있는 것 / 없는 것

세 프레임워크: **FamilyControls**(권한), **ManagedSettings**(차단 적용), **DeviceActivity**(스케줄·사용량 모니터링). iOS 15에서 부모용으로 시작, iOS 16부터 기기 소유자 본인(individual) 인증이 추가됨.
출처: Apple 문서 FamilyControls 개요 https://developer.apple.com/documentation/familycontrols (2026-11-23 확인)

### 할 수 있는 것 (공식 문서 + 개발자 포럼으로 확인)

| 기능 | 가능 여부 | 비고·출처 |
|---|---|---|
| 앱/웹도메인/카테고리 실드(차단 화면 덮기) | 가능 | `ManagedSettingsStore.shield.applications / webDomains / applicationCategories`. 카테고리는 `.all(except:)`로 "전부 차단, 예외만 허용"도 가능. https://developer.apple.com/documentation/managedsettings |
| 사용자가 앱을 고르는 피커 | 가능 | `FamilyActivityPicker`. 단, 선택 결과는 **불투명 토큰** (앱 이름·번들ID를 앱이 알 수 없음) https://developer.apple.com/documentation/familycontrols/familyactivitypicker |
| 시간대 스케줄 모니터링 | 가능 | `DeviceActivitySchedule` — `intervalStart/End`, `repeats`, `warningTime`. **최소 간격 15분, 최대 1주일**. 15분 미만은 조용히 무시됨. 출처: Apple 포럼 "Screen Time/ Schedules/ 15 minutes limitation" https://developer.apple.com/forums/thread/736496 ; https://developer.apple.com/forums/thread/729841 (2026-11-23 확인) |
| 사용량 임계치 이벤트 | 가능 | `DeviceActivityEvent(threshold:)` → `eventDidReachThreshold` 콜백. 그러나 신뢰성 문제 다수(아래 A-3) |
| 모니터 확장 콜백 | 가능 | `DeviceActivityMonitor`: `intervalDidStart / intervalDidEnd / eventDidReachThreshold / intervalWillStartWarning / intervalWillEndWarning / eventWillReachThresholdWarning`. 확장은 별도 프로세스, **메모리 상한 6MB**. https://developer.apple.com/forums/thread/735454 ; https://developer.apple.com/forums/thread/823431 |
| 실드 화면 꾸미기 | 제한적으로 가능 | `ShieldConfiguration`: backgroundBlurStyle, backgroundColor, icon(이미지 1장), title, subtitle, primaryButtonLabel/BackgroundColor, secondaryButtonLabel, secondaryButtonSubmenuItems. **이게 전부**. 커스텀 뷰·애니메이션·영상·입력창 불가. https://developer.apple.com/documentation/managedsettingsui/shieldconfiguration (2026-11-23 확인); 포럼 "Question about Shield customization limits" https://developer.apple.com/forums/thread/803432 |
| 실드 버튼 눌렀을 때 동작 | 제한적 | `ShieldActionDelegate` → `.close / .defer / .none`. 실드에서 **내 앱을 열어주는 공식 API 없음** (one sec 개발자가 private API에 의존한다고 공개 언급). https://developer.apple.com/forums/thread/819997 |
| 사용량 리포트 UI | 가능하지만 격리 | `DeviceActivityReport` 확장이 SwiftUI 뷰를 그림. 확장 안에서는 앱 이름·번들ID·사용 시간을 볼 수 있음 |
| 본인(individual) 인증 | iOS 16+ 가능 | `AuthorizationCenter.shared.requestAuthorization(for: .individual)` → 생체 인증 1회. https://developer.apple.com/documentation/familycontrols/authorizationcenter/requestauthorization(for:) |

### 할 수 없는 것 (확정)

1. **앱 이름·번들ID를 본 앱에서 읽을 수 없다.** 토큰은 불투명. 실드 확장·리포트 확장 샌드박스 안에서만 `Label(token)`으로 표시 가능. Apple 포럼 답변 "by design, you cannot access app names or bundle identifier outside of the ActivityReport or shield extension sandbox for privacy reasons". https://developer.apple.com/forums/thread/775626 (2026-11-23 확인)
2. **사용량 데이터를 리포트 확장 밖으로 꺼낼 수 없다.** Apple DTS: "Is DeviceActivityReportExtension intentionally sandboxed so Screen Time data cannot be exported to the containing app? **Yes.**" 확장은 네트워크 요청 불가, App Group UserDefaults **쓰기도 조용히 실패**(읽기는 됨). https://developer.apple.com/forums/thread/817516 ; https://developer.apple.com/forums/thread/818297 ; https://developer.apple.com/forums/thread/727958 (2026-11-23 확인)
   → 결과: **AI가 "사용 패턴을 읽고 조용히 판단"하는 구조는 iOS에서 불가능.** 앱이 알 수 있는 것은 "임계치 이벤트가 발생했다"는 콜백뿐이며, 그것도 어떤 앱인지는 토큰으로만 안다.
3. **홈 화면 교체 불가.** 런처 개념 없음. 가능한 것은 위젯·Focus 커스텀 페이지뿐(A-4).
4. **다른 앱의 알림 제어 불가.** Focus 필터(`SetFocusFilterIntent`)는 *내 앱*의 동작만 바꿈. https://developer.apple.com/documentation/appintents/focus
5. **실드 50개 상한.** `shield.applications`+`webDomains` 합계 50개 초과 시 아무것도 차단되지 않고 getter가 nil 반환 (iOS 16·17에서 보고, Apple 답변 없음). 카테고리 토큰으로 우회. https://developer.apple.com/forums/thread/733361 (2026-11-23 확인)
6. **현재 어떤 앱이 실드 중인지 읽는 공개 프로퍼티 없음.** (WWDC26 이후에도 변동 없음, Habit Doom 블로그 — 경쟁사 — https://habitdoom.com/blog/wwdc-2026-screen-time-wishlist)
7. **토큰 → 앱 실행 API 없음.** 차단 해제 후 "원래 열려던 앱으로 돌려보내기"를 공식적으로 할 수 없음(FB15500695). https://developer.apple.com/forums/thread/819997
8. **Downtime·Always Allowed 같은 Apple 자체 Screen Time 설정을 앱이 읽거나 바꿀 수 없음.** https://developer.apple.com/forums/thread/731934
9. visionOS 호환 앱에서는 인증이 항상 실패. (Apple 문서)

---

## A-2. Family Controls (Distribution) 자격 — 승인 현실

- **절차**: Account Holder가 https://developer.apple.com/contact/request/family-controls-distribution 에서 요청. **본 앱 + 각 확장(DeviceActivityMonitor, DeviceActivityReport, ShieldConfiguration, ShieldAction)마다 따로** 요청해야 함. 승인되면 managed capability로 계정에 붙음. 출처: Apple "Requesting the Family Controls entitlement" https://developer.apple.com/documentation/familycontrols/requesting-the-family-controls-entitlement (2026-11-23 확인)
- **개발(Development)은 요청 불필요**: Xcode에서 capability만 켜면 실기기 테스트 가능. 배포(TestFlight/App Store)에만 Distribution 승인 필요. TestFlight에서 확장에 Distribution 자격이 없으면 콜백이 무시된다는 보고 있음. https://developer.apple.com/forums/tags/family-controls
- **소요 기간 (포럼 보고, 공식 수치 없음)**:
  - 약 1일 만에 4개 모두 승인 (2025-12, Apple DTS가 "가장 빠른 사례"로 인정) https://developer.apple.com/forums/thread/809190
  - 2주+ 무응답 (2026-02 제출) https://developer.apple.com/forums/thread/818452 ; https://developer.apple.com/forums/thread/824665
  - 2026-03-09 제출 후 응답 없음 https://developer.apple.com/forums/thread/818553
  - "며칠~한 달 이상"이 포럼의 일반 범위. 거의 두 달 무응답 사례도 있음 https://developer.apple.com/forums/thread/812332
  - DTS 조언: 응답 없으면 Team ID + bundle ID 넣어 code-level support 티켓
- **거절 사유 (드물게 공개됨)**:
  - iOS 16 초기: "Family Sharing을 사용하지 않아서" 거절된 사례 (현재는 individual 인증이 공식이라 이 기준은 사라진 것으로 보임. **확정 못 함**) https://developer.apple.com/forums/thread/718396
  - 본 앱만 승인되고 확장은 "Submitted" 상태로 방치 → 심사 자동 리젝 ("uses Screen Time APIs but has not been submitted with the Family Controls entitlement") https://developer.apple.com/forums/thread/779449 ; https://developer.apple.com/forums/thread/774455
  - 앱을 다른 개발자 계정으로 이전하면 승인이 소멸 → 재신청
  - 메일로는 승인됐는데 포털은 "Submitted" → 제출 막힘 https://developer.apple.com/forums/thread/820971
- **individual vs child**: 자격(entitlement) 자체는 동일 `com.apple.developer.family-controls` 하나. 차이는 런타임 인증 흐름뿐 — child는 Family Sharing 그룹 부모 승인 + 앱 삭제·iCloud 로그아웃 방지가 시스템이 강제됨; individual은 기기 소유자 생체 인증 1회이고 **보호 장치 없음**(A-3 참조). https://developer.apple.com/documentation/familycontrols (2026-11-23 확인)
- 1인 창업자 관점 결론: 신청은 공짜지만 **1일~2개월 블랙박스**. 사이클 계획에서 "신청 → 승인 대기 중 개발" 순서로 두어야 하고, 확장 4종을 처음부터 전부 신청해야 재신청 지연을 피함.

---

## A-3. 알려진 한계·버그 (개발자 불만 모음) + WWDC25/26 변화

### 사용자가 쉽게 끄는 문제 (가장 큰 구조적 약점)
- **설정 → 앱 → "Screen Time 접근" 토글 끄기 + Face ID** 로 어떤 서드파티 차단도 즉시 해제. 2023-03 스레드가 2026-05까지 이어지며 iOS 17, 18.5, 26.3, 26.6에서 "여전함" 확인. Apple 답변 없음. https://developer.apple.com/forums/thread/727291 (2026-11-23 확인)
- **iOS 26.4에서 Screen Time 암호(passcode)로 보호하는 옵션 추가** — "Lock Screen Time Settings"를 켜면 접근 토글 해제에 Screen Time 암호 요구. 단 Content & Privacy Restrictions가 켜져 있어야 하고, 26.4 초기엔 Face ID로 우회되는 버그 보고(26.5 b2에서 수정됐다는 답글, 26.6 beta에서 재발 보고). https://developer.apple.com/forums/thread/821959 ; https://techlockdown.com/articles/ios26-update-screen-time-protected-app-permissions ; Freedom 지원문서 https://support.freedom.to/en/articles/15949415 (2026-11-23 확인)
- **앱 삭제로 해제**: individual 인증은 앱 삭제를 막지 않음. 역으로 **앱을 지워도 실드가 남는** 버그도 있음(재부팅이 유일한 해법). https://developer.apple.com/forums/thread/744157 ; https://developer.apple.com/forums/thread/738262

### DeviceActivity 신뢰성
- `intervalDidStart`가 아예 호출 안 됨 (iOS 26.3.1) https://developer.apple.com/forums/thread/819224
- `intervalDidEnd` 미호출 https://developer.apple.com/forums/thread/774285
- 임계치가 **생성 직후 즉시 도달**로 잘못 발화 (iOS 26.0–26.4, 26.5 b1에서 수정됐다고 Apple이 레이더 갱신; 그러나 26.5 이후·iOS 27에서도 재발 보고) https://developer.apple.com/forums/thread/819997 ; https://developer.apple.com/forums/thread/814559
- 확장 6MB 메모리 상한에 걸려 죽으면 콜백이 사라짐 https://developer.apple.com/forums/thread/827783
- 45분 넘는 스케줄은 콜백이 지연/누락된다는 경험담 (Habit Doom, 경쟁사) https://habitdoom.com/blog/wwdc-2026-screen-time-wishlist
- 실드 확장에 **랜덤한 새 토큰**이 들어오는 현상 https://developer.apple.com/forums/thread/819997
- iOS 26 업그레이드 시 **권한이 풀려 재인증 필요** https://developer.apple.com/forums/thread/794128
- one sec 개발자(Frederik Riedel) 2026-03 공개 글: "수십만 실사용자에 영향", 수년간 수십 건 Feedback에 실질 답변 없음, iOS 26 beta 1(2025-06)에 보고한 회귀가 10개월 미해결. https://developer.apple.com/forums/thread/819997 (2026-11-23 확인)

### WWDC 2025 (iOS 26) — Screen Time API 변화
- Apple 뉴스룸(2025-06-11): 아동 계정 설정 간소화, 연령대 공유(Declared Age Range API), 13–17세 기본 보호, Communication Limits 확장, PermissionKit. **FamilyControls/DeviceActivity/ManagedSettings 자체의 신규 API는 없음** ("기존 도구"로만 언급). https://www.apple.com/newsroom/2025/06/apple-expands-tools-to-help-parents-protect-kids-and-teens-online (2026-11-23 확인)
- 사용자 측: App Limits를 0분으로 설정해 **완전 차단** 가능(이전 최소 1분), Downtime 중 인앱 브라우저도 차단, 26.4에서 Screen Time 암호로 서드파티 권한 보호. https://techlockdown.com/blog/ios-26-screen-time-changes
- 블로그 일각의 "Screen Time API 3.0" 명칭은 **확인 불가**.

### WWDC 2026 (iOS 27) — 2026-06-08 발표
- 아동용: Time Allowances(카테고리별 일일 예산, 평일/주말 구분), Screen Time Schedules, Ask to Browse(Safari 허용목록), 대시보드 개편, 연락처 승인. 개발자: PermissionKit, Declared Age Range API, SensitiveContentAnalysis. **성인용 변화·서드파티 차단 API 변화 없음.** https://apple.com/newsroom/2026/06/apple-previews-new-child-safety-features (2026-11-23 확인)
- 서드파티 개발자 시각(Habit Doom, 경쟁사): iOS 27 beta에서도 DeviceActivity 스로틀링·콜백 누락 동일, 실드 상태 읽기 불가 동일, 리포트 데이터 격리 동일. https://habitdoom.com/blog/wwdc-2026-screen-time-wishlist
- 출시 예정: iOS 27은 2026 가을(iPhone 18과 함께). 2026-11-23 현재 일반 공개 여부는 이 조사로 확정하지 않음.

---

## A-4. iOS에서 가능한 "홈 화면 경험"

| 수단 | 서드파티 앱이 할 수 있는 것 | 한계 |
|---|---|---|
| **Focus 모드 + 커스텀 홈 페이지** | 없음(사용자가 직접 설정). 사용자는 Focus별로 보여줄 홈 페이지를 고르고 나머지를 숨길 수 있음 | **앱이 Focus를 켜거나 설정을 만들 수 없음.** 공개 API 없음(포럼 질문에 답 없음). 앱이 할 수 있는 건 Focus 상태 읽기(INFocusStatus, 권한 필요)와 Focus 필터(내 앱 동작만). Focus 전용 새 페이지를 만들 수도 없고 기존 페이지만 선택. https://developer.apple.com/forums/thread/693444 ; https://beebom.com/create-use-custom-home-screen-focus-mode ; https://developer.apple.com/documentation/appintents/focus (2026-11-23 확인) |
| **Shortcuts 자동화** | 사용자가 "앱 열릴 때 / 시간 / 위치 / Focus 켜질 때" 트리거로 개인 자동화를 만들고 "확인 없이 실행" 설정 가능. 자동화 안에서 **Set Focus** 액션으로 Focus 켜기 가능 | 앱이 자동화를 **설치해줄 수 없음** — 사용자가 Shortcuts 앱에서 직접 만들어야. 기기별이며 iCloud 동기화 안 됨. 외부(서버)에서 Focus 설정 불가. 앱은 App Intents로 "액션"을 제공할 뿐. https://support.apple.com/guide/shortcuts/apde31e9638b/ios ; https://support.apple.com/guide/shortcuts/apdfbdbd7123/ (2026-11-23 확인) |
| **App Intents** | Siri·Shortcuts·위젯·Control Center에서 내 앱 액션 노출(iOS 16+) | 다른 앱을 제어하진 못함 |
| **WidgetKit 위젯** | 홈·잠금화면·StandBy 위젯. iOS 17+ **인터랙티브 위젯**(Button/Toggle → App Intent 실행, 앱 안 열고도 동작). 잠금화면 accessory 위젯도 상호작용 가능 | 텍스트 입력·내비게이션 불가, 잠긴 상태에선 버튼 비활성, 타임라인 기반이라 실시간 아님. https://developer.apple.com/documentation/widgetkit/adding-interactivity-to-widgets-and-live-activities (2026-11-23 확인) |
| **Controls (iOS 18+)** | Control Center·잠금화면·Action 버튼에 내 앱 버튼/토글 배치 (`ControlWidgetButton/Toggle`) | 사용자가 갤러리에서 직접 추가해야 함. https://developer.apple.com/documentation/widgetkit/creating-controls-to-perform-actions-across-the-system |
| **Live Activities** | 잠금화면·Dynamic Island에 진행 중 세션 표시(ActivityKit, iOS 16.1+), 버튼 포함 가능 | 8시간 상한, 푸시 갱신 필요 |
| **StandBy (iOS 17+)** | 충전 중 가로 모드에 위젯 표시 | 위젯과 동일 제약 |
| **iOS 26 Liquid Glass·아이콘** | 사용자가 Light/Dark/Tinted/**Clear** 아이콘 스타일 선택, 위젯도 Clear 적용 | 앱이 홈 전체 스타일을 바꿀 수 없음. https://www.macrumors.com/guide/ios-26-home-screen/ (2026-11-23 확인) |

**결론**: "조용한 폰"의 시각적 구현은 (1) 사용자가 1회 설정하는 Focus 커스텀 페이지 + (2) 우리 앱의 위젯/Live Activity 조합이 상한선. **앱이 자동으로 Focus를 구성해주는 것은 불가.** 온보딩에서 Shortcuts 자동화 생성을 "안내"할 수는 있지만 사용자가 손으로 만들어야 한다.

---

## A-5. Apple 자체 Screen Time — 경쟁 상대

- **기능 (iOS 18 기준)**: Downtime(허용 앱·전화만), App Limits(앱·카테고리별 일일 한도, 모든 Screen Time 기기에 적용), Always Allowed, Communication Limits(Downtime 중 연락 가능 대상), Screen Distance, Content & Privacy, Screen Time 암호. https://support.apple.com/108806 (2026-11-23 확인)
- **iOS 26 추가**: App Limits 0분 = 완전 차단(이전 최소 1분, "1분 더" 우회 악명), Downtime 중 인앱 브라우저 차단, Managed Screen Time(원격 아동 관리), 13–17세 기본 보호, 26.4 서드파티 권한 암호 보호. https://techlockdown.com/blog/ios-26-screen-time-changes
- **iOS 27 예고**: Time Allowances·Schedules·Ask to Browse — **모두 아동 계정용**. 성인 본인용 개선 없음. https://apple.com/newsroom/2026/06/apple-previews-new-child-safety-features
- **Apple 자체의 신뢰성 문제**: iOS 26에서 "1분 설정이 모든 앱에 적용", App Limits 미작동, 26.2 휴식 후 제한 풀림 등 커뮤니티 보고 다수. https://discussions.apple.com/thread/256137598 ; https://discussions.apple.com/thread/256216039 ; 9to5Mac "Screen Time is broken" (2024-02) https://9to5mac.com/2024/02/01/screen-time-is-broken-2/
- **서드파티가 Apple보다 할 수 있는 것**: 커스텀 실드 문구/버튼, 사용자가 만든 "의도" 흐름(실드 2차 버튼 → 내 앱), 임계치 기반 개입, 친구/책임 파트너, 물리 키(NFC) 결합, 더 좋은 통계 UI. **할 수 없는 것**: Apple보다 강한 강제력(결국 같은 토글로 풀림), 알림 제어, 홈 교체.

---

## B. iOS 차단·스크린타임 앱 비교

| 앱 | 가격 (US App Store, 2026-11) | 투자·규모 | 평점 | 주요 불만 | 남은 시간을 "돌려주기"? |
|---|---|---|---|---|---|
| **Opal** | Pro $99.99/년, $19.99/월, 주간 $4.99–9.99, 학생 할인. 무료: 규칙 1개. Deep Focus(취소 불가)는 Pro 전용 [1] | 시드 $4.3M (2021-01, Speedinvest·ISAI·Adjacent·Harry Stebbings 등) [2]; 이후 $10M 추가 조달 + "attention OS" 발표, 일 100만 사용자·누적 1,000만 설치 주장 (RuntimeWire 기사, **날짜 미확인**, 1차 출처는 창업자 X 글) [3]; 50만 다운로드(2023-02) [4]; "5억 시간 절약"(2026-06, 자체 추정 — 온보딩 자기신고 기준선 대비, 활성 사용자만 합산, 회사도 "측정이 아닌 추정"이라 명시) [5]; 1인당 하루 평균 1h23m 절약 주장 [5] | 4.7 / 92K [1] | 가격이 카테고리 최고(Unstar); "Screen Time 설정에서 지우면 즉시 풀림"; 세션 중 Opal 자체를 일시정지 가능; 재구독 $132 청구 불만(커뮤니티); 업데이트 후 크래시·블록 안 풀림("Reload Opal" 버튼 추가); 리디자인 후 "직관적이지 않다"(유료 사용자) [6][7][8]; Reddit: "Opal을 쓰려고 Opal을 차단하기 시작했다", "무의식적으로 앱에서 해제한다" [9] | **부분적**. Focus Session·Rule·Opal Score·주간 리포트·10일 코스. "의도(intention)" 기능은 **못 찾음**(Common Sense Media의 옛 VPN 시절 문구만). 차단 해제 후 할 일을 제안하지 않음 |
| **one sec** | Pro $19.99/년(지역별 €14.99 등 불일치), Family $29.99/년, Lifetime $99.99(과거 $49.99). 무료: 앱 1개 [10] | 부트스트랩(외부 투자 없음), 팀 18명 (RevenueCat 인터뷰 2026-04-15) [11]; 2022 기준 25만+ 다운로드 [12]; PNAS 논문(2023, 공동저자=창업자): 개입 후 36%가 열기 포기, 열기 시도 37%↓ (보도는 57%) [13] | 4.8 / ~21–23K [10] | 숨쉬기 일시정지는 "기다리면 됨"(마찰형); 효과가 시간 지나며 둔화(WhistleOut); iOS 26에서 재개입 안 됨·Screen Time 재인증 필요·스크린샷 후 재트리거 등 API 글리치(공식 지원문서) [14]; Face ID로 앱 숨기면 블록 멈춤(리뷰) | **가장 멀리 감**. 개입 후 **"왜 열려고 하나" 의도 선택(intention picker) + 의도 추적**, 감정 추적, "healthy alternatives"(대안 행동 제안), Lengo·Structured 연동, Phone-Free Morning. 그러나 "목표"로 연결되진 않고 앱 단위 순간 개입에 머묾 [15] |
| **Jomo** | Plus $29.99/년($2.49/월 환산, 3일 체험), $5.99/월, Lifetime $99.99(또는 $84.99), 학생 $14.99/년, Family $59.99/년 [16] | 100% 자기자금, 프랑스 2–3인 팀, 2022-10 출시; "30만 명이 신뢰"(헬프센터) vs "수천 활성 사용자"(웹) — 불일치 [17] | 4.8 / 2.6K (US) [16] | UI "clunky·confusing", 앱 자체가 "복잡하다"고 릴리즈노트에서 인정; Strict Mode에서도 제한 목록에서 앱 제거 가능; "의식적 사용" 프롬프트에 아무 글자나 쳐도 통과; 휴식 시작해도 앱이 안 열리는 경우 "거의 절반"(justuseapp 리뷰) [18] | **중간**. Locks(일일 열기 횟수 + 운동으로 해제), **Routines(침대 정리 등 습관 완료 → 스크린타임 획득)**, 오늘 사용량 vs 목표, 친구와 함께. Reddit: "제한 앱 열면 다른 할 일을 상기시킨다" [9] |
| **ScreenZen** | **무료, 구독 없음**, 팁 $5/$10/$20/$40 [19] | 개발자 Scott 1인; 블로그 글(날짜 미상): DAU 약 75K, 월 수백 달러 수익, 광고 거부·팁 모델 선택 [20]; 웹: 월 50만+ 사용자, "100만+" [21]; Play 1M+ 다운로드; Adapty 추정 월 $30K(검증 불가) | 4.9 / 55K [19] | 마찰형이라 "기다리면 열림"; 웹사이트 선택이 불편; 가장 엄격한 모드에서 "unbrick 깜빡" 시나리오 문제; Trustpilot 프라이버시 불만 1건 | **부분적**. 열기 전 대기(점점 길어짐), 체크인 프롬프트("무엇을 찾나"), 열기 전 **필수 과제·대안 활동 체크리스트**(사용자 리뷰 언급), 일일 목표·스트릭. 목표 시스템은 없음 |
| **Clearspace** | Premium $6.99/월, $44.99–59.99/년, Family $79.99 [22] | YC W23, 프리시드 약 $500K (YC·Team Ignite·Roadrunner, 2023-04) [23]; 창업자 Royce Branning·Oliver Hill | 4.7 / 9K [22] | 안 연 날에도 차단됨, 한 번에 한 앱만, 차단 시도 중 리뷰 요청 팝업, 삭제·재설치로 우회, 푸시업 카메라 인식 불편(iPad) [22][24]; justuseapp 37% 부정 | **중간**. "의도와 사용을 정렬" 슬로건, 열기 전 심호흡 + 진행/포기 선택, **운동(푸시업)으로 스크린타임 획득**, 책임 파트너. 목표 연결은 없음 |
| **Brick** (NFC 기기) | 앱 무료, 기기 $59(출시 $49, 세일 $65 표기도), Brick Night $129(2026-10-22 출시) [25] | 부트스트랩(2024 BizTimes: 완전 자기자금), 창업자 TJ Driver·Zach Nasgowitz; 2023-09 출시, 지하실에서 7만 개 생산; Entrepreneur(2026-05-19): **활성 사용자 50만+, 180개국, 매출 "multiple eight figures"**(2차 출처, 감사 안 됨) [26]; Google Play·공식 "5.5만 개 5점 리뷰" | 4.9 / 61K [27] | **스케줄이 제때 brick/unbrick 안 됨**("scheduling portion rarely works", 개발자 "신뢰성 개선 중"); 앱 삭제로 우회(Strict Mode 켜면 불가); 긴급 unbrick 5회 소진 후 고객지원 요청; 기기 분실 시 공장초기화 위험(Norton); "명예 시스템"; 글리치·동기화 불안(Reddit 인용) [27][28][29] | **없음**. 순수 차단. "할 일"은 사용자가 알아서 |
| **Unpluq** (NFC 태그) | Premium $59.99/년, $6.99/월; 과거 태그 번들 $44.95(6개월)/$59.95(1년)/$199.95(평생) [30] | TU Delft 학생 창업(Tim Smits·Jorn Rigter), Kickstarter 2020(263명)·Indiegogo 2022·Wefunder 지분 크라우드펀딩; VC 라운드 **못 찾음**; 2024 "소프트웨어 피벗" 기사(접근 불가) [31] | 4.8 / 932 [30] | 앱 추가 화면 프리징 수개월 미해결("돈 내고 못 쓴다", 2025-11); "Apple Screen Time 대비 비쌈"; "앱 기능 유지 위한 필수 업데이트" 반복 | **없음**. 사용자 평균 1h22–26m 절약 주장만 |
| **BePresent** | $59.99/년(체험 1–4주), $3.99/월, Pro $24.99–29.99/년, Family $99.99 [32] | 프리시드 $750K (Half Court Ventures + 엔젤), 시드 텀시트 수령(날짜 미상); 창업자 Jack·Charles Winston(형제), 법인 Screen Detox Inc [33] | 4.8 / 72K [32] | 체험 후 $70 자동청구·환불 거부("biggest scam"); 광고 있음; 가족 응급 시 "give up" 프롬프트가 막음; 삭제 후에도 차단 남음(Trustpilot) [32] | **게임화**. 그룹 세션, 일일 목표에 **돈 걸기**, 리더보드, 포인트 → 명상·운동 파트너 쿠폰. 개인 목표와의 연결은 없음 |
| **Roots** | Plus $29.99–59.99/년, $9.99/월, 주 $7.99 [34] | 개발사 MWM(Get Roots, Inc.); 투자 **못 찾음** | 4.7 / 3.5K [34] | 제한이 일찍 걸림("$30 내고 폰이 벽돌", 개발자 버그 인정·환불), downtime 차단/해제·앱 피커·추적 버그 | **부분적**. 일일 목표·스트릭·Monk Mode, 해제 전 "속도방지턱"(숨쉬기·명상·자연소리 60초·푸시업). 대안 행동 제안은 없음 |
| **Refocus** | Pro $7.99–69.99 등 지역·티어 혼재, Family $79.99–99.99 [35] | Labi LLC; "매일 4.3만 명 사용" 주장; 투자 못 찾음 | 모름 | 모름 | 모름 |
| **Freedom** | $39.99/년(7일 체험), $8.99–12.99/월, Forever $199 [36] | 2025 PNAS Nexus 연구(Castelo 외)에 사용; 투자 모름 | 4.5 / 6.2K [36] | 보호가 "경고 없이 멈춤"; 한 사이트 막으려다 인터넷 전체 차단; iOS 26.2+ Apple 측 버그; 2024-01 "Screen Time 토글 하나로 풀려 Freedom이 무의미" (개발자 포럼) [36][37] | **없음**. 세션 기반 차단 |
| **Forest** | 2025-12 Pro 일회구매($3.99) 종료 → 무료+Plus 구독(가격 지역별, 미공개) [38] | 2014년부터 6,000만 명 사용, 100만+ 평점 | 4.8 (US 수치 미확인) | 구독 전환 불만(2차 출처) | **간접**. 타이머 동안 나무 키우기, 집중 시간의 시각화. 차단 자체는 약함 |

### 주석(출처·날짜)
[1] Opal App Store https://apps.apple.com/us/app/opal-screen-time-control/id1497465230 (2026-11-23)
[2] TechCrunch 2021-01-26 https://techcrunch.com/2021/01/26/opal-raises-4-3-million-for-its-digital-wellbeing-assistant-for-iphone/ ; Speedinvest https://www.speedinvest.com/blog/taking-back-control-of-your-phone-why-we-invested-in-opal ; CB Insights 총 $6.91M https://www.cbinsights.com/company/opal-3/
[3] RuntimeWire https://runtimewire.com/article/opal-raises-10m-unveils-new-opal-attention-os-kenneth-schlenker (본문 접근 불가, 검색 요약만; 날짜 미확인)
[4] Opal 블로그 2023-02-15 https://opalapp.com/blog/opal-reaches-500k-downloads
[5] Opal 블로그 2026-06-15 https://opalapp.com/blog/opal-saved-humans-over-500-million-hours ; 2022 임팩트 서베이(응답 2,000+) https://www.opal.so/blog/the-opal-impact-survey
[6] Unstar(경쟁사) https://unstar.app/hi/blog/opal-forest-freedom-one-sec-jomo-screen-time-apps-ranked-2026
[7] Opal 커뮤니티 https://community.opal.so/t/132-re-subscription-it-s-time-to-say-goodbye/2898 ; https://community.opal.so/t/doesn-t-work-anymore/2487 ; 릴리즈노트 https://community.opalapp.com/t/opal-ios-release-notes-wednesday-july-1st/10897
[8] Habit Doom(경쟁사) https://habitdoom.com/blog/opal-vs-screenzen-jomo-one-sec
[9] Reddit 인용 — Habit Doom(경쟁사)이 r/getdisciplined 스레드에서 발췌 https://habitdoom.com/blog/screen-time-reward-apps-reddit (원 스레드: https://www.reddit.com/r/getdisciplined/comments/1o9nqby/best_free_screen_time_limiting_app/ 등; 직접 열지 못함)
[10] App Pricing Lab https://apppricinglab.com/app/apple/1532875441 ; AppFollow https://apps.appfollow.io/ios/one-sec-screen-time-focus/1532875441?country=us ; 한 리뷰가 지적한 가격 불일치 https://makeheadway.com/blog/one-sec-app-review/
[11] RevenueCat 2026-04-15 https://www.revenuecat.com/blog/growth/frederik-riedel-expected-12-his-app-cut-screen-time-by-57
[12] TIME 2022 https://time.com/6240981/social-media-addiction-app
[13] PNAS 2023 https://pmc.ncbi.nlm.nih.gov/articles/PMC9974409 ; 장기 연구(1,039명) https://www.medien.ifi.lmu.de/pubdb/publications/pub/haliburton2024chi/haliburton2024chi.pdf
[14] one sec 지원문서 https://tutorials.one-sec.app/en/articles/3036354 ; https://tutorials.one-sec.app/en/articles/5709826
[15] Fortune 2023-03-06 https://fortune.com/well/2023/03/06/one-sec-app-can-help-cut-your-social-media-use ; one sec FAQ https://one-sec.app/faq ; Pro 포함 내역 https://tutorials.one-sec.app/whats-included-in-one-sec-pro
[16] Jomo App Store https://apps.apple.com/us/app/jomo-screen-time-blocker/id1609960918 (2026-11-23) ; 가격 페이지 https://jomo.so/pricing
[17] Jomo 헬프센터 https://help.jomo.so/en/article/1fq5ura/ ; Strict Mode https://help.jomo.so/en/article/what-is-strict-mode-14vrs43/
[18] justuseapp 2026-11-18 https://justuseapp.com/en/app/1609960918/jomo-screen-time-blocker/reviews
[19] ScreenZen App Store https://apps.apple.com/us/app/screenzen-screen-time-control/id1541027222 (2026-11-23)
[20] ScreenZen 블로그 "Navigating the Path from Free to Sustainable"(날짜 미상, 직접 접근 404) — 검색 스니펫 기준 https://www.screenzen.co/blog
[21] ScreenZen 홈 https://www.screenzen.co/ (2026-11-23) ; Adapty 추정 https://adapty.io/paywall-library/screenzen/
[22] Clearspace App Store https://apps.apple.com/us/app/clearspace-reduce-screen-time/id1572515807 (2026-11-23)
[23] VCBacked https://www.vcbacked.co/company/clearspace ; YC https://ycombinator.com/companies/clearspace ; YC 런치 "earn your screen time with exercise" https://www.ycombinator.com/launches/KxS-clearspace-earn-your-screen-time-with-exercise
[24] justuseapp 2026-01 https://justuseapp.com/en/app/1572515807/clearspace-reduce-screen-time/reviews
[25] TechCrunch 2026-06-24 https://techcrunch.com/?p=3135283 ; BizTimes 2024 https://biztimes.com/jump-start-germantown-startup-brick-aims-to-temporarily-block-distractions-on-smartphones/ ; Fast Company(Brick Night) https://www.fastcompany.com/91628593/brick-night-bedtime-screentime
[26] Entrepreneur 2026-05-19 https://www.entrepreneur.com/business-news/these-founders-built-a-device-that-solves-phone-addiction (본문 접근 불가, 검색 요약)
[27] Brick App Store https://apps.apple.com/us/app/brick-screen-time-app-blocker/id6448794069 (2026-11-23)
[28] Norton(Reddit 인용) https://us.norton.com/blog/privacy-tips/is-brick-app-secure ; Tom's Guide https://www.tomsguide.com/phones/brick-review ; NBC https://www.nbcnews.com/select/shopping/brick-phone-app-blocker-review-rcna259740
[29] Protect Young Eyes https://www.protectyoungeyes.com/devices/brick ; Man of Many https://manofmany.com/tech/brick-app-blocker-review-analysis
[30] Unpluq App Store https://apps.apple.com/us/app/unpluq-reduce-screen-time/id1673743726 (2026-11-23) ; 2023 리뷰 https://nicklafferty.com/reviews/unpluq/
[31] IO+ 2022-05-26 https://ioplus.nl/archive/en/unpluq-stops-you-endlessly-scrolling-on-your-phone/ ; YES!Delft https://yesdelft.com/startups/unpluq/ ; Calm Tech Institute 2024-09 https://www.calmtech.institute/post/a-physical-key-to-digital-balance-unpluq-s-innovative-approach-to-screen-time ; MT/Sprout(403) https://mtsprout.nl/groei/unpluq-wil-meer-schermtijd-besparen-met-pivot-naar-software
[32] BePresent App Store https://apps.apple.com/us/app/bepresent-screen-time-control/id1644737181 (2026-11-23)
[33] Bullet Pitch https://bulletpitch.beehiiv.com/p/bepresent (날짜 미상)
[34] Roots App Store https://apps.apple.com/us/app/roots-screen-time-control/id6446800962 (2026-11-23)
[35] Refocus App Store https://apps.apple.com/us/app/refocus-app-blocker-control/id1645639057
[36] Freedom App Store https://apps.apple.com/us/app/freedom-control-screen-time/id1269788228 (2026-11-23)
[37] Apple 포럼 727291 (dtb69, 2024-01) https://developer.apple.com/forums/thread/727291
[38] Forest FAQ https://forestapp.cc/llms-full.txt ; ClickUp 리뷰 https://clickup.com/learn/topic/productivity/tools/forest/

### 카테고리 전체에 공통된 불만 (여러 출처 교차)
1. **"결국 내가 끈다"** — 설정 토글·앱 삭제·날짜 변경·일시정지. 마찰형(one sec, ScreenZen, Clearspace)은 "기다리면 열림", 강제형(Opal Deep Focus, Jomo Strict, Brick Strict)은 "응급 상황에 못 열어 화남". 두 불만이 양쪽에서 동시에 나옴.
2. **구독 가격·자동청구** — Opal $99.99/년이 가장 많이 욕먹음; BePresent $70 자동청구; Opal $132 재구독. 반대편에 ScreenZen(무료)이 "Reddit이 추천하는 앱"으로 자주 등장.
3. **iOS 업데이트마다 깨짐** — iOS 26에서 one sec·Freedom·Opal·Unpluq 모두 "Apple 쪽 버그" 공지. 스케줄 미발화(Brick, Roots), 권한 풀림(권한 재인증).
4. **UI 복잡함** — Jomo가 대표. 규칙 엔진이 커질수록 사용자가 못 따라감.
5. **"줄였는데 할 게 없다"** — Reddit r/getdisciplined "I reduced my screen time by 8 hours but I am not doing anything" (Habit Doom 인용). 이 빈 공간을 채우는 앱이 없다는 게 기회.

---

## B-6. "남은 시간을 무엇에 쓸지"까지 가는 앱이 있는가

결론: **없다.** 가장 멀리 간 것도 "열기 전 순간"에 머문다.

| 앱 | 어디까지 가나 | 안 가는 곳 |
|---|---|---|
| **one sec** | 차단 직후 **"왜 열려고 하나"를 고르게 하고(intention picker) 그 선택을 추적**, 감정 기록, "healthy alternatives"로 다른 행동 제안, Structured(일정 앱)·Lengo(어학) 연동 | 의도가 사용자의 *장기 목표*와 연결되지 않음. 대안은 일반 목록. AI 없음 |
| **Jomo** | **Routines: 실제 습관(침대 정리 등)을 완료해야 스크린타임 획득**, 오늘 사용량 vs 목표, 차단 화면에 팁 | 습관은 "화폐"일 뿐 목표 체계 아님. 어떤 습관을 할지 앱이 제안하지 않음 |
| **Clearspace** | 심호흡 + 진행/포기, **운동으로 스크린타임 획득**, 책임 파트너 | 운동 외 활동 없음. 한 번에 한 앱 |
| **ScreenZen** | 대기 + 체크인 질문("무엇을 찾나") + 열기 전 **필수 과제/대안 체크리스트**(사용자 설정) | 사용자가 직접 써넣어야. 목표·AI 없음 |
| **Opal** | Focus Session을 "일·공부·수면"으로 이름 붙임, Opal Score, 10일 코스 | "의도" 기능 못 찾음. 세션이 끝난 뒤 무엇을 했는지 묻지 않음 |
| **BePresent** | 일일 목표에 돈 걸기, 포인트 → 명상·운동 쿠폰 | 외부 보상으로 치환. 개인 목표 없음 |
| **Roots** | 해제 전 속도방지턱(숨쉬기·푸시업), 일일 목표·Monk Mode | 대안 행동 없음 |
| **Brick / Unpluq** | 물리 마찰만 | 전무 |
| **Forest** | 집중 시간을 나무로 시각화 | 그 시간에 무엇을 했는지는 무관 |

**Life OS에 주는 시사점**
- "차단 → 의도 선택"까지는 one sec가 이미 했고 PNAS 논문까지 있다. 차별점은 그 다음: **의도/목표가 실드 화면·위젯·Live Activity를 통해 "지금 할 일"로 되돌아오는 루프**. iOS 제약상 이 루프를 구현할 수 있는 접점은 (a) ShieldConfiguration의 title/subtitle/2차 버튼 문구(앱이 동적으로 바꿀 수 있음), (b) 인터랙티브 위젯(App Intent로 "시작" 버튼), (c) Live Activity(세션 중 잠금화면 상주), (d) 푸시/로컬 알림 — 네 가지뿐이다.
- "AI가 조용히 백그라운드에서"는 **사용 데이터를 못 읽는다**는 벽에 부딪힌다. AI의 입력은 Screen Time이 아니라 사용자가 앱에 준 목표·캘린더·위치·시간대·"지금 열려던 앱 카테고리(토큰)"·임계치 이벤트 발생 여부로 제한된다. 이 범위 안에서 설계해야 한다.
- 강제력 경쟁은 Apple의 토글 하나에 무너진다(2023→2026 미해결). **강제력이 아니라 "돌아올 이유"로 경쟁**하는 포지션이 iOS 제약과도 맞고, 시장의 빈 자리(B 공통 불만 5)와도 맞는다.

---

## 출처 목록 (URL + 확인일 2026-11-23)

Apple 공식
- FamilyControls 개요 — https://developer.apple.com/documentation/familycontrols
- Family Controls entitlement 요청 — https://developer.apple.com/documentation/familycontrols/requesting-the-family-controls-entitlement
- requestAuthorization(for:) — https://developer.apple.com/documentation/familycontrols/authorizationcenter/requestauthorization(for:)
- DeviceActivitySchedule — https://developer.apple.com/documentation/deviceactivity/deviceactivityschedule
- DeviceActivityReportExtension — https://developer.apple.com/documentation/deviceactivity/deviceactivityreportextension
- ShieldConfiguration — https://developer.apple.com/documentation/managedsettingsui/shieldconfiguration
- App Intents Focus — https://developer.apple.com/documentation/appintents/focus
- WidgetKit 인터랙티브 위젯 — https://developer.apple.com/documentation/widgetkit/adding-interactivity-to-widgets-and-live-activities
- WidgetKit Controls — https://developer.apple.com/documentation/widgetkit/creating-controls-to-perform-actions-across-the-system
- Shortcuts 트리거 — https://support.apple.com/guide/shortcuts/apde31e9638b/ios ; 자동화 — https://support.apple.com/guide/shortcuts/apdfbdbd7123/
- Screen Time 사용법 — https://support.apple.com/108806
- 뉴스룸 2025-06-11 — https://www.apple.com/newsroom/2025/06/apple-expands-tools-to-help-parents-protect-kids-and-teens-online
- 뉴스룸 2026-06-08 — https://apple.com/newsroom/2026/06/apple-previews-new-child-safety-features

Apple 개발자 포럼 (개발자 보고, 비공식)
- 자격 승인 기간 — https://developer.apple.com/forums/thread/809190 ; /812332 ; /818452 ; /818553 ; /824665 ; /820971 ; /779449 ; /774455 ; /718396
- 토큰 불투명 — https://developer.apple.com/forums/thread/775626
- 리포트 샌드박스 — https://developer.apple.com/forums/thread/817516 ; /818297 ; /727958 ; /818174
- 15분 최소 — https://developer.apple.com/forums/thread/736496 ; /729841
- 50토큰 상한 — https://developer.apple.com/forums/thread/733361
- 6MB 메모리 — https://developer.apple.com/forums/thread/735454 ; /823431 ; /827783
- 콜백 미발화 — https://developer.apple.com/forums/thread/819224 ; /774285 ; /814559 ; /745035
- 토글로 해제(2023–2026) — https://developer.apple.com/forums/thread/727291
- iOS 26.4 Screen Time 암호 — https://developer.apple.com/forums/thread/821959 ; /758333
- iOS 26 권한 풀림 — https://developer.apple.com/forums/thread/794128
- one sec 개발자 종합 보고 2026-03 — https://developer.apple.com/forums/thread/819997
- Focus 프로그래밍 제어 불가 — https://developer.apple.com/forums/thread/693444
- 실드 커스텀 한계 — https://developer.apple.com/forums/thread/803432
- Always Allowed 접근 불가 — https://developer.apple.com/forums/thread/731934
- 삭제 후 실드 잔존 — https://developer.apple.com/forums/thread/744157 ; /738262

언론·블로그·기타
- TechCrunch Opal 2021 — https://techcrunch.com/2021/01/26/opal-raises-4-3-million-for-its-digital-wellbeing-assistant-for-iphone/
- TechCrunch Opal 2022-09-15 — https://techcrunch.com/2022/09/15/opal-revamps-its-screen-time-app-to-help-anyone-not-just-parents-with-kids-focus-and-avoid-distractions/
- TechCrunch Brick 2026-06-24 — https://techcrunch.com/?p=3135283
- Entrepreneur Brick 2026-05-19 — https://www.entrepreneur.com/business-news/these-founders-built-a-device-that-solves-phone-addiction
- Fortune Gen Z 아날로그 2026-02-13 — https://fortune.com/2026/02/13/analog-gen-z-phone-addiction-bloom-brick-app-blockers-dumb-phones-social-media/
- RevenueCat one sec 2026-04-15 — https://www.revenuecat.com/blog/growth/frederik-riedel-expected-12-his-app-cut-screen-time-by-57
- PNAS one sec 2023 — https://pmc.ncbi.nlm.nih.gov/articles/PMC9974409
- Fortune one sec 2023-03-06 — https://fortune.com/well/2023/03/06/one-sec-app-can-help-cut-your-social-media-use
- Tech Lockdown iOS 26 — https://techlockdown.com/blog/ios-26-screen-time-changes ; 26.4 — https://techlockdown.com/articles/ios26-update-screen-time-protected-app-permissions
- Habit Doom (경쟁사) WWDC26 — https://habitdoom.com/blog/wwdc-2026-screen-time-wishlist ; Reddit 인용 — https://habitdoom.com/blog/screen-time-reward-apps-reddit
- Unstar (경쟁사) 비교 — https://unstar.app/hi/blog/opal-forest-freedom-one-sec-jomo-screen-time-apps-ranked-2026
- Norton Brick — https://us.norton.com/blog/privacy-tips/is-brick-app-secure
- MacRumors iOS 26 홈 — https://www.macrumors.com/guide/ios-26-home-screen/
- Beebom Focus 커스텀 페이지 — https://beebom.com/create-use-custom-home-screen-focus-mode
- 9to5Mac "Screen Time is broken" 2024-02-01 — https://9to5mac.com/2024/02/01/screen-time-is-broken-2/
- App Store 페이지: Opal id1497465230 / one sec id1532875441 / Jomo id1609960918 / ScreenZen id1541027222 / Clearspace id1572515807 / Brick id6448794069 / Unpluq id1673743726 / BePresent id1644737181 / Roots id6446800962 / Freedom id1269788228 / Refocus id1645639057

### 못 찾은 것 / 확인 못 한 것
- Opal $10M 라운드의 날짜·투자자 (기사 본문 접근 불가)
- Reddit 원문 (크롤러 차단) — 2차 인용만
- ScreenZen 블로그 글 날짜, 현재 수익
- Unpluq·Roots·Refocus·Freedom 투자 내역
- Brick 정확한 판매 수량 (7만 개 "생산"만 확인)
- Family Controls 자격 심사의 공식 기준·거절 사유 (Apple 미공개)
- one sec·Forest US App Store 정확한 평점 수(지역별 상이)
- iOS 27 일반 공개 여부(2026-11-23 기준)
