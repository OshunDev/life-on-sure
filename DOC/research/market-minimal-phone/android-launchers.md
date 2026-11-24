# Android 미니멀 런처 조사 (2026-11-23 기준, 참조 수준)

주의: Play Store 페이지는 직접 못 읽어서(WebFetch 불가) chrome-stats.com 스냅샷(2026-11-23)을 사용. 다운로드는 정확한 구간(band)이 아니라 스냅샷 추정치. Reddit 직접 스레드는 검색에 안 잡힘 -> 리뷰/블로그/Lemmy/Product Hunt로 대체.

## 1. 표

| 런처 | 가격 | 규모(2026-11-23 chrome-stats) | 강점 | 불만 | 목표/의도 연결 |
|---|---|---|---|---|---|
| Minimalist Phone (QQ Labs, com.qqlabs.minimalistlauncher, 현재명 "minimalist phone Screen Time") | 구독. TechRadar 기준 월 $4.99 / 12개월 $21.43 (기사 날짜 못 찾음). 일회성 플랜도 있다고 표기 | 8.63M 다운로드, 4.65점/51,849건, 최종 업데이트 2026-10-13 | 앱 차단, 세션 길이 선택, 열기 전 "mindful delay", 차단 스케줄 | 유료화, 알림/회의를 놓침, 앱 접근이 느려짐(TechRadar) | 약함. 사이트에 "intention/goal" 기능 설명 없음. 세션 제한+지연이 전부. 사이트는 "일일 제한은 효과 없다"고 주장 |
| Niagara Launcher | Pro 연 $13.99 / 평생 $42.99 (공식 help). 2022년엔 평생 $14.99였음 -> 점진 인상 | 18.37M, 4.73점/268,475건, 업데이트 2026-11-22 | 한 손 리스트형 홈, 활발한 개발 | Pro 가격(구독), Pro 인증 반복 알림, 배터리/랙 보고(로드맵 문서) | 없음 |
| Olauncher | 무료, 오픈소스(GPLv3) | 약 2.44M, 4.84~4.85점/약 78K건(2026-07-31 스냅샷) | 텍스트 홈, 무광고, 설정 쉬움 | 소수 의견: "3시간 만에 포기"(블로거) | 없음 |
| Before Launcher | 무료 + 일회성 Pro (Gizmodo 약 $6, 날짜 오래됨. 현재가 못 찾음) | 740K, 4.76점/13,477건, 업데이트 2026-10-31 | 텍스트 UI, 앱 8개 제한 컨셉 | Pro 현재가 불명 | 없음 |
| Blloc Ratio (com.bllocosn) | 구독/평생 멤버십 필요(APKMirror 변경로그). 포럼: 짧은 체험 후 연 약 $50 "공격적" | 1.87M, 3.34점/19,283건, 업데이트 2026-06-08 | 디자인, 메시지 통합(Tree) | 가격 불투명, 체험 후 유료 | 약함. 노트/날씨 Root 화면 정도 |
| Indistract(Indistractable) Launcher | 무료 베타 + Pro $10 (Gizmodo, 오래됨) | 1.38M, 평점 못 찾음, 마지막 업데이트 2024-10-28 | Light Phone 영향 | 2년 넘게 업데이트 없음 | 없음. 책 영감만 명시 (Nir Eyal 제작 증거 없음) |
| Dumbify | 유료 느낌, 가격 못 찾음 | 14K, 3.09점/143건, 업데이트 2026-03-19 | - | 체험 불명확, 구독 해지 방법 못 찾음(리뷰) | 없음 |
| Unlauncher / mLauncher / LessPhone | Unlauncher MIT 무료. mLauncher 정보 거의 없음. LessPhone은 iOS 앱만 확인 | 못 찾음 (Unlauncher 최신 2.1.1, 2024-01) | 무료, 7앱 제한 계열 | 못 찾음 | 없음 |
| Nothing (Launcher/OS) | OS 포함 | 해당 없음 | Essential Space(AI 메모/리마인더), Essential Notifications, Glyph | 미니멀보다 AI 정리 쪽 | 부분적. 노트/할 일 정리이지 시간 환원 아님 |
| Samsung Digital Wellbeing / Focus mode | 무료 내장 | - | 기본 탑재 | 한 번 탭으로 해제("Take a break"), 앱 설치/삭제/제한 우회 방지 불가(Samsung 커뮤니티 요청글). One UI 9에 네트워크 차단형 실험 기능 | 없음 |
| Google Digital Wellbeing | 무료 | 11.2B 다운로드(시스템 앱 특성), 3.62점/1.51M건, 2026-11-09 | 기본 탑재 | 타이머 해제 쉬움 (2차 출처) | 없음 |

## 2. 공통 불만 (근거 강도 순)
1. 유료화/구독: Niagara(Lemmy "구독이라 너무 비쌈", Android Authority), Minimalist Phone(TechRadar, 개발자는 "광고/데이터 판매 없음"으로 방어), Ratio(체험 후 연 약 $50), Dumbify(해지 방법 못 찾음). Lemmy 댓글: 업데이트로 기능 상당수가 유료로 이동해 대안 탐색.
2. 알림/일정 놓침: TechRadar 필자는 알림을 줄이자 회의/약속을 잊고 대화를 놓쳐 중단. (일화 1건)
3. 우회 쉬움: Samsung Focus는 한 탭 해제, Google 타이머는 닫기 버튼 한 번 (2차 출처). TikTok 시간제한 실험(Nesi 요약)에서 대부분 조건에서 제한이 효과 없었고, 매우 낮은 제한만 효과(원 논문 미확인).
4. 설정 번거로움/앱 서랍 접근성: 이 키워드로 직접 근거는 못 찾음.
5. 회귀: Android Authority 필자는 Niagara로 빈 홈화면을 2주 시험했는데 "결국 옛 습관으로 돌아갔다"고 기술.

## 3. Blloc 실패 사례
- 2016 말: 공동창업자 Adham Badr, Mladen Hoyss가 선전에서 제조사 탐색. 약 1년 뒤 제조사 확보, 소규모 투자 유치. 2019 말까지 Blloc(Zero 18/1.0) 수천(네 자릿수) 대 판매. (consumerstartups, 2022-05-24)
- 하드웨어 포기 이유: 소규모 맞춤 생산을 부트스트랩 스타트업과 하려는 제조사가 없었고, 시장은 큰 화면/많은 기능을 원해 미니멀 기기로는 확장 불가. (같은 글)
- 2020 하반기 소프트웨어(Ratio)로 전환. 2020-10 베타 종료 후 Play Store 공개. Product Hunt 2위, 유튜버 1편 약 100만 조회.
- 투자: Speedinvest 주도, SoftBank(Emerge) 참여 $2M (techfundingnews/tech.eu, 2022-01). 이전 라운드 금액은 비공개.
- 성장: 750K+ 설치(2022-05), 뉴스레터 140K 구독. 유지율 수치는 미공개.
- 현재: 회사 폐업 증거는 못 찾음. Ratio는 2026-06 업데이트, 별점 3.34로 여전히 Play Store에 있음. 즉 "폰은 실패, 런처는 생존하나 평가 낮음". 최근 매출/직원/펀딩 후속은 모름. (사용자 전제의 "런처도 죽음"은 확인 안 됨)

## 4. 유지율 단서
- 공식 유지율/이탈 수치: 어느 런처도 못 찾음.
- 간접: Minimalist Phone 사이트의 "10M+ users", "93% screen time saved"는 방법론 없는 마케팅.
- 규모 비교(주간 증가): Niagara +226K/주, Minimalist Phone +29K/주(8.6M 대비 0.3%), Before +2.3K/주. 설치는 계속 늘지만 활성 유지는 불명.
- Indistract: 1.3M 설치 대비 월 활성 53.1K(AppGoblin) = 약 4%. 유지율의 유일한 근사치이나 방법론 불명, 단일 앱.
- 일화: Olauncher 장기 사용 긍정 사례(Make Tech Easier)와 3시간 포기 사례 공존. Niagara 2주 후 옛 습관 복귀.

## 5. 시사점 (창업자용)
- 조사한 모든 런처가 "숨기기/막기"에 머묾. 목표/의도와 연결된 것은 못 찾음 -> 빈 자리.
- 우회 용이성과 알림 누락 불안이 반복 문제이므로, 필요한 정보를 대신 올려주는 설계가 필요.
- 구독 가격 저항이 큼. 평생 옵션 존재.

## 6. 출처 (접근일 2026-11-23)
- Niagara 가격: https://help.niagaralauncher.app/article/104-price-of-niagara-pro
- Niagara 불만: https://niagaralauncher.featurebase.app/p/faults , https://feddit.it/post/16976278
- chrome-stats 스냅샷(2026-11-23): https://chrome-stats.com/d/bitpit.launcher , /d/com.qqlabs.minimalistlauncher , /d/com.bllocosn , /d/com.beforesoft.launcher , /d/com.indistractablelauncher.android , /d/com.app.dumbify , /d/com.google.android.apps.wellbeing , Olauncher https://chrome-stats.com/d/app.olauncher (2026-07-31), https://www.appbrain.com/app/app.olauncher
- Minimalist Phone 사이트: https://www.minimalistphone.com ; TechRadar: https://techradar.com/phones/i-tried-minimalist-phone-to-improve-my-mindfulness-and-focus-but-just-became-frustrated-and-forgetful ; APKMirror: https://www.apkmirror.com/apk/focus-experts/minimalist-phone-screen-time/
- Blloc: https://consumerstartups.substack.com/p/blloc (2022-05-24) ; https://tech.eu/2022/01/21/blloc-wants-to-reinvent-your-home-screen-speedinvest-and-softbank-agree/ ; https://techfundingnews.com/blloc-german-startup-that-develops-android-launchers-to-make-smartphones-less-addictive-dials-up-2m/ ; https://9to5google.com/2020/10/08/blloc-ratio-launcher/ ; https://community.humanetech.com/t/ratio-launcher-android-help-needed-to-test/5282
- Indistractable: https://appgoblin.info/apps/com.indistractablelauncher.android ; Gizmodo: https://gizmodo.com/the-best-android-launchers-for-completely-customizing-y-1848355232/9
- Before Launcher: https://gizmodo.com/the-best-android-launchers-for-completely-customizing-y-1848355232/slides/6
- Unlauncher: https://f-droid.org/en/packages/com.jkuester.unlauncher
- Nothing: https://9to5google.com/2026/03/13/nothing-essential-space-event-detection-search-update/ ; https://hypebeast.com/2026/8/nothing-os-5-0-updates-android-17-info
- Samsung: https://us.community.samsung.com/t5/Suggestions/Feature-request-More-robust-digital-wellbeing-tools/td-p/3336350 ; https://memeburn.com/one-ui-9-can-now-block-distracting-apps/ ; https://www.samsung.com/us/support/answer/ANS00085547
- 타이머 효과: https://technosapiens.substack.com/p/are-screen-time-limits-backfiring (2차 요약)
- 장기 사용 일화: https://maketecheasier.com/olauncher-minimalist-android-launcher ; https://androidauthority.com/blank-android-homescreen-3390580
