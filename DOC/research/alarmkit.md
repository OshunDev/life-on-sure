# AlarmKit(iOS 26) — 서드파티 알람이 어디까지 되는가

> 작성 2026-11-23, Claude 조사. 모든 웹 출처 확인일 2026-11-23. 출처는 문장 끝 `[번호]`, 7장에 URL.
> 질문: 아침 장면(scenes.md 1-2) "알람이 울리면 끄는 화면이 아니라 전날 밤 정해둔 '오늘의 첫 한 가지'가 보이고, 끄기 = 시작 버튼"이 서드파티 앱으로 되는가.
> 읽은 것: Apple 공식 문서(.md 렌더링), WWDC25 세션 230 대본, Apple DTS가 쓴 AlarmKit FAQ, 개발자 포럼 버그 스레드 10여 개, 실제 앱 사례(Alarmy·ByeBed·AutoSleep·VariAlarm·KeyAlarm 등), HealthKit 수면 스레드. 레딧·MacStories·Alarmy 고객센터 원문은 크롤러 차단(403)으로 못 읽음.

---

## 0. 한 줄 결론

**부분 가능.** 알람 화면에 우리가 쓴 제목("오늘의 첫 한 가지: 물 한 잔")이 보이고, 버튼 하나를 "시작"으로 만들어 우리 앱을 열 수 있다. 그러나 (1) 알람 화면은 시스템 템플릿이라 커스텀 뷰는 못 띄우고, (2) 시스템 "중단" 버튼과 하드웨어 버튼(전원·볼륨)으로 언제든 그냥 끌 수 있어 "끄기 = 시작"은 **강제가 아니라 기본 경로**로만 만들 수 있으며, (3) iOS 26.0~27.2에 걸친 미발화·지연 버그가 Apple 답변 없이 남아 있다. 그래도 AlarmKit 이전의 우회법(백그라운드 무음 오디오·크리티컬 알림)보다 모든 면에서 낫고, 심사 위험도 없다. **AlarmKit을 주 경로로, 시스템 시계 알람을 사용자 백업으로** 가는 것이 답이다.

---

## 1. 가능 · 불가능 표

판정: **가능** / **제한** / **불가** / ⚠️ 미확인

### 1-1. 울리는가

| 항목 | 판정 | 근거 |
|---|---|---|
| 무음 스위치·집중 모드를 뚫고 울림 | **가능** | WWDC25 대본: "the alert breaks through the silent mode and the current focus" [A2]. Apple 샘플 문서: "It overrides both a device's focus and silent mode" [A3]. Apple DTS FAQ Q5: "AlarmKit alarms can break through all focus modes" [B1] |
| 앱 종료·강제 종료·재부팅 후에도 울림 | **가능** | DTS FAQ Q2: "all alarms are expected to persist regardless of app or device state changes, once they are successfully scheduled" [B1]. 알람 UI·소리는 시스템 프로세스가 그림, 앱을 깨우지 않음 [A5] |
| 앱이 "숨김" 또는 Face ID 잠금 상태 | **불가** | DTS FAQ Q3: "Hidden or passcode required apps do not work with AlarmKit… will silently fail" [B1] |
| 다른 앱 알람과 겹칠 때 | **제한** | DTS FAQ Q6: 어느 알람이 이길지 "non-deterministic" [B1] |
| 다른 앱이 가로 모드로 떠 있을 때 | **제한** ⚠️ | 알림 UI가 안 뜨는 버그 보고(iOS 26 베타~26.2, Apple 미답변). 개발자 우회: preAlert 1초 [C4] |
| 지원 플랫폼 | iOS·iPadOS·Mac Catalyst 26.0+ | 문서 메타데이터에 watchOS·visionOS 없음 [A1][A4] |
| Apple Watch | **표시만** | "The system forwards the alert presentation to a paired watch (if any)" [A3]. 워치용 API는 없음(워치 단독 알람 불가) |
| iOS 27 | **가능하나 새 버그** | iOS 27 SDK에서 AlarmKit API 변경 없음(서드파티 요약) [D7]. 단 iOS 27에서 상태 전달 지연 버그 신규 보고(§1-5) |

### 1-2. 알람 UI를 어디까지 바꿀 수 있나

| 항목 | 판정 | 근거 |
|---|---|---|
| 제목 | **가능** | `AlarmPresentation.Alert(title:…)` [A3]. 밤에 정한 문장을 그대로 넣을 수 있음 |
| 중단(Stop) 버튼의 글자·글자색·SF 심볼 | **가능** | `AlarmButton(text:textColor:systemImageName:)` [A2][A3] |
| 중단 버튼을 **없애거나 조건부로** 만들기 | **불가** | "AlarmKit always draws a Stop button… a third-party app cannot remove it, rename it, or make it conditional"(오픈소스 미션 알람 README; 글자 변경은 가능하므로 "rename" 부분은 과장) [D3]. 하드웨어 버튼으로도 중단됨(§1-4) |
| 두 번째 버튼 | **가능** | `secondaryButton` + `secondaryButtonBehavior`: `.countdown`(스누즈·반복) 또는 `.custom`(App Intent 실행, "Open" 액션으로 표시) [A3] |
| 두 번째 버튼이 우리 앱을 연다 | **가능** | 대본: "execute an app intent to open my app when tapped… sets the openAppWhenRun flag to true" [A2]. 인텐트 타입은 `LiveActivityIntent` [A4] |
| 중단 버튼에도 인텐트 | **가능** | "You can provide a custom app intent for the stop button or for the secondary button" [A2]. `stopIntent`의 perform이 실행됨(DTS FAQ Q4) [B1] |
| 색 | **제한** | `tintColor` 하나. 두 번째 버튼 채우기색, 잠금화면 제목·카운트다운 틴트에 쓰임 [A2][A4] |
| 알람 화면 전체 커스텀 뷰(이미지·레이아웃) | **불가** | 알람(alerting) 전체 화면은 시스템 프로세스가 템플릿으로 그림. KeyAlarm 개발자: 로컬라이즈 키가 그대로 노출됐을 정도로 앱 밖에서 렌더됨 [D4]. 대본: "displays the alert UI, customized with your configuration"(설정값만) [A2] |
| Live Activity(잠금화면·Dynamic Island) 커스텀 | **가능** | 카운트다운·일시정지 상태는 위젯 확장에서 SwiftUI로 직접 그림. 알람 중(alerting) 상태의 Live Activity에도 버튼을 넣을 수 있음(KeyAlarm은 여기 Stop/Snooze를 넣음) [A2][D4] |
| 커스텀 메타데이터 | **가능** | `AlarmMetadata` 구조체 → Live Activity 컨텍스트에 전달 [A4] |
| 알람 중 **우리 화면**을 띄우기 | **불가** | 울리는 동안 보이는 것은 시스템 알람 화면 + Live Activity뿐. 우리 앱은 사용자가 버튼을 **누른 뒤** 열림. 로그상 데몬이 알람을 먼저 끄고 인텐트 perform이 나중에 실행됨(비공식 관찰) [D5] |
| 소리 | **제한** | 기본음 또는 `AlertSound.named(파일)`. 파일은 앱 번들 또는 `Library/Sounds`에 있어야 함 [A2][A6]. 스트리밍·Apple Music 불가, "limited to 30 seconds of playback and then it loops again"(VariAlarm 개발자) [D2]. iOS 26.0에서 커스텀 음이 기본음으로 대체되는 버그, 26.1에서 수정 [C5] |

### 1-3. 스케줄·권한

| 항목 | 판정 | 근거 |
|---|---|---|
| 일회성(고정 날짜) | **가능** | `Alarm.Schedule.fixed(Date)`(시간대 변경에 안 따라감) [A2] |
| 반복 | **제한** | `.relative(time, repeats: .never / .weekly([요일]))`. 주 단위 요일 반복만 [A3]. 격주·특정 날짜 반복은 앱이 직접 여러 개 예약 |
| 스누즈 | **가능** | `countdownDuration.postAlert` + 두 번째 버튼 `.countdown`. 카운트다운을 쓰면 **위젯 확장 필수**("Otherwise, the system may unexpectedly dismiss alarms and fail to alert") [A3] |
| 알람 수정 | **제한** | update API 없음. cancel 후 재예약 [D1] |
| 알람 개수 | **제한** | "There is no set number as a limit", 넘으면 `maximumLimitReached` 오류 [B1]. 50분 간격으로 촘촘히 깔면 미발화 보고(§1-5) |
| 권한 | **가능** | Info.plist `NSAlarmKitUsageDescription` + `requestAuthorization()`. 없으면 첫 알람 예약 때 자동 요청. 앱별 1회 동의, 설정 > 알림 > [앱] > 알람에서 해제 가능 [A3][D1]. **별도 entitlement 없음** — `com.apple.developer.alarmkit`은 LLM이 지어낸 것이라고 Apple 엔지니어가 명시 [C6] |
| 심사 | **가능** | 공개 API. 특별 신청 없음. Family Controls와 달리 TestFlight 제약 없음 |

### 1-4. 끄기 전에 "행동"을 강제할 수 있나

| 항목 | 판정 | 근거 |
|---|---|---|
| 수학문제·걷기·사진을 풀어야 꺼지게 | **불가** | 중단 버튼을 못 없애고, DTS FAQ Q7: "Any physical button that causes a reaction from AlarmKit will stop the currently alerting alarm", Q8: 물리 버튼은 "dismiss all currently alerting alarms" [B1]. 전원 버튼 = 스누즈가 아니라 **해제** [D2] |
| 우회 패턴 — "감시 알람 체인" | **제한** | 미션 앱들의 실제 방식: 알람 하나가 아니라 +1·+3·+6·+10·+15분에 후속 알람을 미리 깔고, 미션을 끝내야 체인을 취소. ByeBed: "if you leave it unfinished the alarm rings again, up to 10 times over 15 minutes" [D3][D6]. 사용자가 앱 알람 권한을 끄면 전부 무력화 |
| 사용자가 그냥 껐는지 알 수 있나 | **제한** ⚠️ | DTS FAQ Q4: 스와이프·전원 버튼으로 꺼도 `stopIntent`가 실행됨 [B1]. 그러나 2025-06 베타 때 개발자(Simon Støvring)는 "there's no way to detect when a user swipes an alarm away. The stop intent doesn't get called"라고 보고 [A5]. FAQ가 더 나중(2025-08). iOS 27에서는 상태 전달 자체가 앱 다음 실행 때까지 밀리는 버그(§1-5) |
| Shortcuts "알람이 중단되면" 자동화 | **불가** ⚠️ | AlarmKit 알람에는 발동하지 않는다는 보고(Apple 미답변) [C7]. 시계 앱 알람에만 됨 |

### 1-5. 신뢰성 — 포럼 버그 보고 (전부 Apple의 수정 확인 없음)

| 증상 | 버전 | 상태 | 출처 |
|---|---|---|---|
| 26.1에서 예약한 알람이 26.2 베타3 이상으로 올리면 안 울림 | 26.2b3~RC | FB21273655, 답변 없음. 26.2b3 이후 새로 예약한 건 정상 | [C1] |
| 촘촘히 깔면(50분 간격) 잠금 상태에서 20초~4분 지연 또는 미발화, 26.5에서 "약 30%" 실패, 재부팅하면 잠시 나아짐 | 26.x | 답변 없음 | [C2] |
| 알람이 늦게 울리거나 **폰을 깨울 때까지** 안 울림, 밤에 더 심함 | 26.0~27.0 RC | FB22887867·FB24483266. DTS: "personally I do not know a workaround and I do not work for the AlarmKit team" | [C3] |
| iOS 27: 앱이 서스펜드 상태면 `.alerting` 전환이 **앱이 다음에 실행될 때**(보통 잠금 해제 때) 도착, 최대 19시간 51분 지연. 집중 모드에서 소리만 나고 잠금화면 UI 없음. 꺼둔 알람이 시간 지나 다시 표시 | 27.0 seed3~27.0 | FB23754550(열림). DTS: "unable to share any updates" | [C8] |
| 잠금 해제 상태에서 Dynamic Island를 스와이프하면 빈 "좀비" Live Activity가 남음(재부팅·다음 알람까지). 앱 삭제해도 남음 | 26.5~27.2 | FB22295664·FB22791285, 답변 없음 | [C9] |
| 화면이 켜져 있을 때 안 울림(Flutter) | 26 | DTS가 샘플 요청, 미해결 | [C10] |
| 전체 화면 알람은 **한 번만** 뜸: Stop 대신 잠금 해제를 하면 알람 화면이 사라지고 다시 안 옴(Live Activity만 남음) | 26 | KeyAlarm 개발자 경험 | [D4] |
| 시뮬레이터에서 알람 UI 미표시 → 실기기 필수 | Xcode 26.x | Apple 안내 | [C11] |

> 시사점: 2026-11 현재 AlarmKit은 "시계 앱만큼" 믿을 수는 없다. 사이클 1 데모(창업자 폰 하루)에서는 **시계 앱 백업 알람을 같이 켜고** 재는 것이 안전하다. 이는 iOS 27 보도에서 권하는 사용자 대응과 같다 [D8].

---

## 2. 실제 앱 사례

| 앱 | AlarmKit | 무엇을 구현했나 | 드러난 한계 |
|---|---|---|---|
| **Alarmy**(알라미, 한국 딜라이트룸) | 채택(25.45.0+) | iOS 26에서 잠금화면 위에 알람 표시, 탭(26.0)·스와이프(26.1)하면 **미션 화면이 열림** [D9]. 평점 4.8(25.3만) [D10] | 강제 종료 상태면 울리긴 하되 **기본음**으로, 사용자 소리·미션 화면은 앱이 백그라운드에 살아 있어야 함 [D6]. 즉 미션은 "끄기 → 앱 열림 → 미션"이지 "미션을 풀어야 꺼짐"이 아님. 2026-05 v26.23.1 크래시 사태 [D11]. 미션 알람 앱으로서의 AlarmKit 활용 방식은 Alarmy가 공식으로 밝힌 것 없음 ⚠️ |
| **ByeBed** | 처음부터 | 14종 미션(푸시업 온디바이스 비전, 사진 미션은 Gemini). Stop → 미션, 미완이면 15분간 최대 10회 재알람(체인) | iOS 26 필수. 체인은 알람 권한 끄면 무력 [D3] |
| **AutoSleep** | 채택 | 스마트 기상 알람. "uses AlarmKit so the alarm reliably fires through Sleep Focus" | — [D12] |
| **VariAlarm** | 채택 | 교대근무용 템플릿·폴더 알람·Siri·Shortcuts | 소리 30초 루프, 전원 버튼 = 해제(스누즈 아님), 스트리밍 불가. Apple DevRel에 요청했다고 함 [D2] |
| **KeyAlarm** | 채택 | 3개월 운영기 | 알람 화면이 앱 밖 프로세스라 로컬라이즈 키 노출, 알람 화면 한 번만 뜸, 앱 업데이트 시 BGTask 소실, "알람이 안 꺼져 폰을 재시작" 민원 [D4] |
| **ToDo Alarm, Mornio, Risly, FixSleep, WakeUp 3.0, 리얼알람(한국)** | 채택 | 대부분 "iOS 26 이상 필수", 그 아래는 알림+Live Activity로 폴백 | 리얼알람은 주 7.99달러 구독 [D12][D13] |
| **Sleep Cycle** | **증거 없음** | iOS 26 업데이트는 Liquid Glass·레이아웃 대응뿐. 2026-03 "Smart Alarm" SDK 공개는 파트너용 | [D14] |
| **Rise, Waking Up** | 못 찾음 | — | — |
| **Apple 미리알림** | iOS 26.2 "긴급" 토글이 유사 내부 구조 | — | 가로 모드 버그에 같이 걸림 [C4][D12] |

### AlarmKit 이전의 우회법과 심사 위험

- **로컬 알림**: 소리 최대 30초, 1회 재생 후 끝. 집중 모드·무음에 묻힘. 2024년 포럼 요청글: 서드파티 알람은 "standard local notifications, which fire once and then cease" [C12]. iOS 18 이하 Alarmy류의 기본 구조.
- **백그라운드 무음 오디오로 앱을 살려 두기**: 배터리 소모, 강제 종료하면 끝. 심사 2.5.4(백그라운드 오디오 모드 오용) 반복 리젝 보고. Apple 엔지니어: 그런 용도는 "stretching the intended use of the background audio mode" [C13]. 포럼: "not only against Apple's guidelines but also drains the device's battery life" [C12].
- **크리티컬 알림 권한**: 알람 앱에는 안 줌. 2026-01 포럼: 신청이 "the app is not the use case"로 거절, DTS는 AlarmKit을 쓰라고 답함 [C14]. 오픈소스 미션 알람도 Apple이 "routinely refuses" [D3].
- 결론: 2026년 iOS 26+ 앱에서 이 우회법을 쓸 이유가 없다. AlarmKit이 공식이고 심사 위험이 없다.

---

## 3. 우리 설계 판정 — "밤에 정한 첫 한 가지가 알람 화면에 보이고, 끄기가 곧 시작"

**판정: 부분 가능.** 나눠 보면:

| 설계 요소 | 판정 | 어떻게 |
|---|---|---|
| 전날 밤 AI가 정한 문장이 알람 화면에 보인다 | **가능** | 밤 10초 확정 화면에서 `AlarmManager.schedule`로 **다음 날 아침 알람을 고정 날짜로 예약**하며 `Alert.title`에 "첫 한 가지: 운동복 입기"를 넣는다. 같은 문장을 `AlarmMetadata`로 Live Activity에도 보낸다. 앱이 밤에 실행 중이라 예약이 확실하고, AI가 "밤에 생각하고 낮에 보여주기"라는 scenes.md 0장 원칙과 정확히 맞는다 |
| 알람 화면이 "끄는 화면"이 아니라 "시작 화면"처럼 보인다 | **제한** | 화면 레이아웃은 시스템 템플릿(제목 + 버튼 2개 + 틴트색). 이미지·카드·우리 폰트는 불가. 할 수 있는 건 제목 문장, 버튼 글자·아이콘, 틴트색. 잠금화면·Dynamic Island의 Live Activity는 우리가 그릴 수 있어 거기엔 더 꾸밀 수 있다 |
| 끄기 = 시작 | **제한(기본 경로로만)** | 두 번째 버튼을 `.custom` + `openAppWhenRun`으로 "시작"(틴트색으로 채워져 가장 눈에 띔) → 앱이 열리며 첫 한 가지 화면. 중단 버튼은 못 없애니 글자를 "나중에"처럼 바꿔 둔다. 하드웨어 버튼·스와이프로 그냥 끄는 길은 막을 수 없다. `stopIntent`로 "그냥 껐음"을 기록하고, 측정에 "첫 한 가지 시작 여부"로 쓴다 |
| 스누즈 | **가능** | 쓰려면 `.countdown` + postAlert + 위젯 확장. 다만 버튼이 두 개뿐이라 "시작"과 "스누즈"를 둘 다 둘 수 없다 → 시작/중단 두 개로 가고 스누즈는 없앤다 [제안]. 성공한 사람들의 방법("알람 후 즉시 한 동작")과도 맞음 |
| 끄기 전에 행동을 요구 | **불가** | §1-4. 원하면 감시 체인(+3·+6·+10분 재알람)을 쓸 수 있으나 "조용한 폰"과 어긋남 → 안 하는 쪽 [제안] |
| 기상 실패 시 15분 늦추기 | **제한** | 수정 API가 없어 cancel 후 재예약. 밤 확정 화면에서 하면 확실. 낮 중 BGTask는 보장 없음 |
| iOS 26.0~25 사용자 | **폴백 필요** | AlarmKit 없음 → 시간민감 알림 + Live Activity(소리 30초, 집중 모드에 묻힘). 사이클 1은 창업자 폰 하나이므로 iOS 26.4+ 전제(ios-feasibility.md §0 권고와 같음) |

### 대안과 각각의 한계

| 대안 | 되는 것 | 한계 |
|---|---|---|
| **A. 시스템 시계 알람 + 우리 잠금화면 위젯/Live Activity** | 신뢰성은 시계 앱 수준. 알람을 끈 뒤 잠금화면에 "첫 한 가지" 위젯이 보임 | 앱이 시계 알람을 만들거나 읽을 API가 없다(사용자가 직접 또는 Siri). 알람이 울리는 **동안**은 시계 화면이라 우리 문장이 안 보임. "끄기 = 시작"이 안 됨 |
| **B. A + Shortcuts 자동화 "알람이 중단되면 → Life OS 열기"** | 시계 알람을 끄면 앱이 자동으로 열림(iOS 17+ 자동화는 확인 없이 실행). Alarm 트리거에 "Is Stopped / Is Snoozed" 옵션 있음 [E1] | 사용자가 단축어 앱에서 자동화를 손수 만들어야 함(온보딩 마찰). AlarmKit 알람에는 발동 안 함 [C7]. 시계 알람의 "울릴 때"가 아니라 "끌 때"만 트리거 [E1] |
| **C. 수면 스케줄의 기상 알람 + "수면 집중 모드 꺼짐" 자동화** | 시계 앱 수면 스케줄과 통합, 워치 연동 | B와 같은 수동 설정. 집중 모드 트리거 존재는 Apple 문서로 확인 못 함 ⚠️(문서 페이지가 목차만 반환) |
| **D. AlarmKit 주 경로 + 시계 알람 백업(사용자가 켬)** [제안] | 우리 문장·시작 버튼이 되고, 버그로 안 울려도 백업이 있음 | 알람이 두 번 울릴 수 있음. 백업은 사용자 몫 |

**[제안] 사이클 1은 D.** AlarmKit으로 "제목 = 첫 한 가지, 두 번째 버튼 = 시작(앱 열림), 중단 버튼 = 나중에"를 만들고, 온보딩에서 "처음 2주는 시계 앱 알람도 같이 켜 두세요"라고 말한다. 스누즈는 뺀다. 감시 체인은 안 쓴다. ⚠️ 실기기에서 직접 재야 할 것: (1) 26.4+에서 밤새 잠금 상태로 미발화·지연이 나는지, (2) 스와이프·전원 버튼으로 껐을 때 `stopIntent`가 실제로 실행되는지, (3) 두 번째 버튼으로 앱이 열릴 때 알람이 먼저 꺼지는지.

---

## 4. 기상 확인 — 앱이 "일어났다"를 언제 알 수 있나

| 수단 | 앱이 아는 시점 | 근거 |
|---|---|---|
| **HealthKit 수면 종료(워치)** | 워치가 기상을 감지하면 수면 세션을 확정해 HealthKit에 쓰고, 그 쓰기가 iOS 앱을 백그라운드에서 깨운다(HKObserverQuery + enableBackgroundDelivery). DTS: "there is no direct, real-time callback that fires the exact second a user opens their eyes" [E2] | Apple DTS, 2026-06 [E2] |
| 그 지연 | "many minutes (sometimes even longer)"(개발자 보고) [E3]. 전달 빈도는 보장 없음: DTS "No, the delivery frequency is not guaranteed… due to the app's background execution time budget and other undocumented factors" [E4]. 관찰 쿼리에 주어지는 실행 시간은 "~4-15 seconds of runtime every hour"(Apple 엔지니어) [E5] | 수십 분 단위로 보는 게 안전 |
| 수면 단계 실시간 | **불가** | 수면 분석은 사후 기록. "HealthKit does sleep analysis some time after the sleep events happening" [E6] |
| **첫 걸음(stepCount)** | iOS에서 stepCount 백그라운드 전달은 **시간당 최대 1회** — DTS: "at most once per hour, even you use enableBackgroundDelivery + .immediate" [E4][E7] | 실시간 불가. 대신 앱이 열릴 때(시작 버튼) CoreMotion/HealthKit으로 **오늘 첫 걸음 시각을 소급 조회**하면 측정엔 충분 [제안] |
| **수면 집중 모드 꺼짐** | DTS가 대안으로 `INFocusStatusCenter`를 언급 [E2]. 단 Focus 상태 API는 커뮤니케이션 앱용 권한이라 심사 리스크 ⚠️(ios-feasibility.md B-3과 같은 결론). 사용자가 만든 Shortcuts 자동화("수면 집중 모드 꺼지면 → 앱 열기")가 현실적 | ⚠️ 트리거 명칭은 Apple 문서로 확인 못 함 |
| **워치 기상 감지(자체)** | 워치 앱의 스마트 알람용 확장 런타임 세션(기상 30분 전부터 센서 접근)이 AutoSleep류의 방식 ⚠️ 이번 조사에서 문서 확인 안 함 | 사이클 1 범위 밖(워치 제외) |
| 폰만 있을 때 | 수면 단계 없음. 수면 스케줄의 "침대에 있음"만 폰이 기록 ⚠️ 미확인 | 사이클 1 측정은 **알람 버튼 로그(시작/그냥 끔/미발화) + 소급 첫 걸음 시각**으로 충분 [제안] |

**[제안] 사이클 1 기상 측정 정의**: 실제 기상 시각 = max(알람 `stopIntent`/`secondaryIntent` 실행 시각, 오늘 첫 걸음 시각(소급)). 워치가 있으면 HealthKit 수면 종료 시각으로 보정(수십 분 늦게 도착해도 됨, 밤에 AI가 돌아볼 때 쓰면 되므로).

---

## 5. 열린 것 ⚠️

1. `stopIntent`가 스와이프·전원 버튼 해제에서 실제로 실행되는지 — FAQ(된다)와 개발자 보고(안 된다)가 충돌. 실기기로 확인.
2. iOS 27의 상태 전달 지연(FB23754550)이 인텐트 perform에도 영향을 주는지. 27.3 이후 수정 여부 못 찾음.
3. iOS 26.1부터 시계 앱 알람이 "밀어서 중단" 슬라이더로 바뀌었는데(접근성 > 터치에서 되돌림) [D15], AlarmKit 알람의 중단 UI가 같이 바뀌었는지, 중단 버튼 커스텀이 26.1에서 폐기됐다는 포럼 주장 [D16] — 미확인.
4. 소리 길이 상한(30초 vs 2분)은 문서 확인 못 함. 기본음으로 시작하면 무관.
5. Shortcuts의 Sleep("기상"), Focus 트리거 정확한 명칭 — Apple 지원 문서가 목차만 반환.

---

## 6. 다른 문서에 반영할 것

- scenes.md 1-2 아침 장면 iOS 접점·⚠️ 줄 (이 조사와 함께 수정함)
- 사이클 1 범위에 "시계 알람 백업 안내(온보딩 한 줄)" 추가 검토 — cycle.md는 이 조사에서 건드리지 않음

---

## 7. 출처 (확인일 2026-11-23)

### A. Apple 공식 문서·WWDC
- [A1] AlarmKit 프레임워크 문서(가용성 iOS/iPadOS/macCatalyst 26.0) — https://developer.apple.com/documentation/alarmkit
- [A2] WWDC25 세션 230 "Wake up to the AlarmKit API" 대본 — https://developer.apple.com/videos/play/wwdc2025/230/ (대본 텍스트: https://nonstrict.eu/wwdcindex/wwdc2025/230/)
- [A3] 샘플 "Scheduling an alarm with AlarmKit"(집중·무음 오버라이드, NSAlarmKitUsageDescription, 워치 전달, 위젯 확장 필수) — https://developer.apple.com/documentation/alarmkit/scheduling-an-alarm-with-alarmkit
- [A4] AlarmManager.AlarmConfiguration(stopIntent/secondaryIntent = LiveActivityIntent, "only available after first unlock"), AlarmAttributes(tintColor·metadata), AlarmManager — https://developer.apple.com/documentation/alarmkit/alarmmanager/alarmconfiguration · https://developer.apple.com/documentation/alarmkit/alarmattributes · https://developer.apple.com/documentation/alarmkit/alarmmanager
- [A5] Michael Tsai 정리(Steve Troughton-Smith "all handled at the system level, doesn't wake up your app", Simon Støvring 스와이프 감지 불가) — https://mjtsai.com/blog/2025/06/20/ios-26-alarmkit/
- [A6] ActivityKit AlertConfiguration.AlertSound(.default / .named) — https://developer.apple.com/documentation/activitykit/alertconfiguration/alertsound
- [A7] WWDC 노트(코드: OpenInApp: LiveActivityIntent, openAppWhenRun, weekly 반복) — https://wwdcnotes.com/documentation/wwdc25-230-wake-up-to-the-alarmkit-api/

### B. Apple DTS FAQ
- [B1] "AlarmKit FAQ", Argun Tekant(DTS Engineer, Core Technologies), 2025-08-15, Q1~Q8 — https://developer.apple.com/forums/thread/797158
- [B2] 볼륨 버튼 스누즈 질문에 DTS 답(버튼은 끄고, 스와이프는 스누즈) — https://developer.apple.com/forums/thread/810865

### C. Apple 개발자 포럼 버그·제약
- [C1] 26.2 베타3 업그레이드 후 안 울림(FB21273655) — https://developer.apple.com/forums/thread/809398
- [C2] 촘촘한 예약 시 지연·미발화, 26.5 약 30% 실패 — https://developer.apple.com/forums/thread/825427
- [C3] 늦게 울리거나 폰 깨울 때까지 안 울림, 26.0~27.0 RC(FB22887867·FB24483266), DTS "do not know a workaround" — https://developer.apple.com/forums/thread/846063
- [C4] 가로 모드 앱 위에서 알람 UI 미표시, preAlert 1초 우회 — https://developer.apple.com/forums/thread/806681
- [C5] 커스텀 소리 26.0 버그·26.1 수정, 30초·루프 보고 — https://developer.apple.com/forums/thread/788836 · https://developer.apple.com/forums/thread/802620 · https://developer.apple.com/forums/thread/814732
- [C6] entitlement 오해: Apple 엔지니어 "LLMs making up non-existent entitlements" — https://developer.apple.com/forums/thread/797950
- [C7] Shortcuts "알람 중단" 트리거가 AlarmKit에 발동 안 함(답변 없음) — https://developer.apple.com/forums/thread/829593
- [C8] iOS 27 alarmUpdates 지연·집중 모드에서 UI 없이 소리만(FB23754550) — https://developer.apple.com/forums/thread/844479
- [C9] 좀비 Live Activity(FB22295664·FB22791285) — https://developer.apple.com/forums/thread/819556
- [C10] 화면 켜져 있을 때 안 울림 — https://developer.apple.com/forums/thread/804749
- [C11] 시뮬레이터 미지원 — https://developer.apple.com/forums/thread/806998
- [C12] AlarmKit 이전 서드파티 알람의 한계 요청글(2024) — https://developer.apple.com/forums/thread/743885
- [C13] 백그라운드 오디오 2.5.4 리젝·Apple "stretching the intended use" — https://developer.apple.com/forums/thread/826849 · https://developer.apple.com/forums/thread/105443
- [C14] 크리티컬 알림 거절 → DTS가 AlarmKit 권고 — https://developer.apple.com/forums/thread/812358

### D. 실제 앱·보도
- [D1] shift-calendar 이슈(AlarmKit can/cannot 정리, FAQ 인용) — https://github.com/LukGer/shift-calendar/issues/2
- [D2] VariAlarm 개발자(30초 루프, 전원 버튼 = 해제, 스트리밍 불가) — https://forums.macrumors.com/threads/ios26-varialarm-my-solo-attempt-to-make-ios-alarm-experience-better.2480553/
- [D3] 오픈소스 미션 알람 README(감시 체인, Stop 버튼 제거 불가, 크리티컬 알림 거절) — https://github.com/shivam098/better-alarm-ios
- [D4] KeyAlarm 3개월 운영기 — https://dev.to/lightning_llc/what-three-months-of-shipping-an-alarmkit-app-taught-me-4hel
- [D5] Wakeprint 개발자 노트(stop/snooze 순서, 데몬이 먼저 끔) — https://wakeprint.app/developers/alarmkit-stop-snooze/
- [D6] Alarmy 미발화 원인 정리(경쟁사 블로그, 강제 종료 시 기본음) — https://www.wakeupbroo.com/blog/alarmy-alarm-not-going-off
- [D7] iOS 27 SDK AlarmKit 변경 요약(서드파티) — https://miniswift.run/whats-new/alarmkit/
- [D8] iOS 27 AlarmKit 앱 지연 보도(백업 알람 권고) — https://www.techbetweenthelines.com/iphone-alarm-no-sound-ios-27/
- [D9] Alarmy 고객센터 "New on iOS 26: Alarmy alarm now shows on your Lock Screen"(25.45.0, 검색 요약만 확인, 원문 403) — https://alarmy-ios.zendesk.com/hc/en-us/articles/53760654821785
- [D10] Alarmy App Store — https://apps.apple.com/us/app/alarmy-loud-alarm-clock/id1163786766
- [D11] Alarmy v26.23.1 크래시 — https://mwm.ai/articles/app-updates/alarmy-v26-23-1-update-causes-crashes-and-freezes-halting-alarms-in-may-2026
- [D12] AlarmKit 채택 앱 목록(ToDo Alarm 블로그, AutoSleep 포함) — https://todo-alarm.com/blog/ios-26-alarmkit-apps/
- [D13] 리얼알람(한국 App Store) — https://apps.apple.com/kr/app/id6748179827 · ByeBed 미션 앱 비교 — https://byebed.com/blog/best-mission-alarm-apps-2026/
- [D14] Sleep Cycle App Store(iOS 26 대응 내용) — https://apps.apple.com/us/app/sleep-cycle-tracker-sounds/id320606217 · Smart Alarm SDK — https://sleepcycle.com/sleep-talk/smart-alarm-now-available-in-the-sleep-cycle-sdk
- [D15] iOS 26.1 알람 슬라이더·되돌리기 — https://9to5mac.com/2025/12/11/ios-26-1-makes-alarms-use-a-slider-heres-how-to-get-stop-button-back/
- [D16] MacRumors "iOS 26 Makes Third-Party Alarm and Timer Apps Better"(2025-06-11) — https://www.macrumors.com/2025/06/11/ios-26-third-party-alarm-apps/

### E. 기상 확인(HealthKit·Shortcuts)
- [E1] Shortcuts 이벤트 트리거(Alarm: Is Stopped / Is Snoozed; 검색 요약으로 확인, 페이지 본문은 목차만 반환) — https://support.apple.com/guide/shortcuts/apd932ff833f/ios
- [E2] DTS(Albert, WWDR) 2026-06: 워치가 기상 감지 시 수면 세션 확정·HealthKit 기록·iOS 앱 백그라운드 기동, 실시간 콜백 없음, INFocusStatusCenter 대안 — https://developer.apple.com/forums/thread/834573
- [E3] 기상 감지 앱 개발자: 수면 샘플 "many minutes (sometimes even longer)" 지연 — https://developer.apple.com/forums/thread/763329
- [E4] DTS(Ziqiao Chen) 2026-04: 전달 빈도 보장 없음, stepCount 시간당 1회 — https://developer.apple.com/forums/thread/823699
- [E5] Apple Frameworks Engineer 2021: 관찰 쿼리 "~4-15 seconds of runtime every hour" — https://developer.apple.com/forums/thread/694441
- [E6] DTS: 수면 분석은 사후 기록 — https://developer.apple.com/forums/thread/780009 (연결 스레드 780012)
- [E7] enableBackgroundDelivery 문서: "on iOS, stepCount samples have an hourly maximum frequency" — https://developer.apple.com/documentation/healthkit/hkhealthstore/enablebackgrounddelivery(for:frequency:withcompletion:)
