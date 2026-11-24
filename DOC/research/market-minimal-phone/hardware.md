# 미니멀폰 하드웨어 시장 조사 (2026-11-23)

목적: 라이프폰(Life On)의 끝그림인 "미니멀 기기" 시장이 실제로 어떤지 — 누가 사고, 왜 떠나고, 사업으로 어떤지 — 1차 자료(리뷰·레딧·인터뷰) 중심으로 정리. 마케팅 문구는 배제. 숫자는 전부 출처·날짜 병기. 못 찾은 것은 "모름/못 찾음".

> 요약 한 줄: 이 시장은 "전 세계 합쳐 10년에 10만 대" 규모의 니치이고, 살아남은 회사는 전부 **하드웨어 + 자기 OS + 구독/통신 요금** 조합으로 버티고 있으며, 이탈 사유는 거의 항상 **2FA·은행·지도·메신저(WhatsApp/카톡)** 네 가지다. 그리고 2025~26년 들어 "미니멀폰이 다시 스마트폰으로 되돌아가는" 현상(OLED, 카메라, 앱 허용)이 뚜렷하다.

---

## 1. 한눈에 보기 표

| 제품 | 회사(국가) | 가격 | 형태 | 되는 것 / 안 되는 것 | 구매층 | 주요 불만 |
|---|---|---|---|---|---|---|
| **Light Phone III** | Light (미국 브루클린) | 출시 $799 (2025-03-27), 프리오더 $399→$599, 2026년 프리오더 창 $699로 인하 | 3.92" OLED 바형, 자체 LightOS | 전화·SMS·지도(HERE)·카메라(50MP→12MP)·음악(파일 업로드)·팟캐스트·핫스팟·메모·알람. **WhatsApp/RCS/브라우저/이메일/은행 없음**. 지문·NFC·영상통화는 출시 시 비활성 | 20~40대 디지털 디톡스 희망자, 서브폰으로 쓰는 사람 다수 | 가격, 자동교정 없는 자판, 카메라 지연 1~3초, 내비 쓰면 배터리 급감, 버그, 고객지원 무응답 |
| Light Phone II | Light | $350 (2019-09) | 흑백 E-ink | 위와 유사(구형) | 동일 | E-ink 느림, GPS 불안정 |
| **Light Flip** (예고) | Light | $299, 통신 묶음 $39/월~ (2026-07-21 발표, 2027-04 출하) | 폴더형, 2.8" 비터치 OLED, T9 | LightOS 도구 전부 | 더 저렴한 입문자 | 두께 19mm, 아직 미출하 |
| **Punkt MP02** | Punkt (스위스 루가노, 2008~, 창업자 자본) | CHF 299~, 과거 $349~379 | 4G 피처폰 | 전화·SMS·핫스팟(Signal 지원 모델) | 디자인 중시 40대+, 유럽 | 비싸고 니치, 소프트웨어 버그 |
| **Punkt MC02** | Punkt | $749 / €699 (2024), 현재 CHF 499 세일 | 안드로이드(AOSP) 스마트폰, Apostrophy OS(구독 $15/월, 1년 무료) | 앱 가능(프라이버시 OS) | 프라이버시 지향 | "미니멀"이 아니라 그냥 비싼 보안폰 (Tom's Guide: "오랜만에 이렇게 짜증난 폰") |
| **Minimal Phone (1세대)** | The Minimal Company (미국) | 백커 $300~350, 정가 $399~499 | 4.3" E-ink + QWERTY, 풀 안드로이드 | 앱 전부 가능(은행·WhatsApp 포함) — "거친 화면"으로 억제 | 블랙베리 향수+디톡스 | **배송 1년 지연**, 고스팅, 느림, 배터리 2~3일(약속 4일), 환불 분쟁, Trustpilot "가짜 송장" 등 |
| **Minimal Phone 2** | 동사 | 얼리버드 $549, 일반 $599 (Kickstarter 2026-08) | 3.92" 90Hz **AMOLED**(E-ink 포기) + QWERTY, 5G | 풀 안드로이드, iMessage 지원 주장 | 동일 | "더 이상 미니멀이 아님"(Digital Trends), 전작 배송 전과 동일한 회사 |
| **Mudita Kompakt** | Mudita (폴란드 바르샤바, CD Projekt 공동창업자 Michał Kiciński) | KS 얼리버드 €299, 프리오더 €319~349/$335~369, 정가 €439/$439 | 4.3" E-ink, 탈구글 Android 12 기반 MuditaOS K | 전화·SMS·오프라인 지도·카메라 8MP·APK 사이드로드(WhatsApp 가능). **은행앱 거의 불가**, 브라우저/이메일 없음 | 프라이버시+디톡스, 유럽 | 느림(지도·카메라·WhatsApp 때때로 사용 불가), 보안 업데이트 불투명, 보증 응답 1달 무응답 |
| **Wisephone II** | Techless (미국 텍사스) | $399 (프로모 $299) + **WiseOS 구독 $14.99/월 필수** | Samsung Galaxy A16/A17 하드웨어 + 잠금 안드로이드 | 전화·문자·카메라·지도·음악·일부 승인 앱. 이메일/Android Auto 없음. 미국 한정. 90일 미활성 시 공장초기화 | 기독교 가정·부모·중독 회복 커뮤니티 | 청구 시스템 엉망, "돈을 짜내려 한다", 구독 강제 |
| **HMD Skyline** | HMD (핀란드) | $499 (2024) | 일반 스마트폰 + "Detox Mode"(앱·연락처 선택 차단, Soft/Hard Lock) | 풀 안드로이드 | 일반 | 판매 수치 미공개; HMD는 2025-07 미국 사업 축소 |
| **HMD Fuse** | HMD | £30 선납+£33/월 (Vodafone UK, 2025-08), AUD 799 | 어린이용 안드로이드 + HarmBlock+ 나체 콘텐츠 차단 | 풀 안드로이드 | 부모 | "가격 대비 성능 부족" |
| **HMD Barbie Phone** | HMD × Mattel | $129/€129 (2024-08) | 4G 폴더 피처폰 | 전화·문자·간단 게임 | Z세대·선물 | 영국 3일 만에 매진, 반복 품절 → 2025-04 재입고 |
| **Nokia 3210 (2024)** | HMD | 349~379위안 (중국) | 4G 피처폰 | 전화·문자·스네이크 | 향수층 | 중국 출시 2일 만에 매진(2024-05-10 Weibo) |
| **Boring Phone** | HMD × Heineken × Bodega | 비매품, 5,000대 경품 (2024-04) | Nokia 2660 Flip 기반 | 전화·문자만 | 마케팅 이벤트 | 해당 없음 |
| **Sunbeam F1 시리즈** | Sunbeam Wireless (미국) | F1 $195(초기) → Horizon $279 → F1 Pro $329~389 (2026 Poplar/Walnut $389) | 러기드 폴더 피처폰, 자체 OS | 전화·문자·지도(Waze 일부)·핫스팟·Signal(신형). 브라우저 없음 | 보수 성향/종교 가정, 10대 첫 폰(Orchid), Patriot Mobile 유통 | 고객지원 "제로", 수신감도 |
| **Bigme HiBreak Pro** | Bigme (중국) | $439 (리뷰 시점), Newegg $438~499, 리퍼 $320; Color판 $399.99 | 6.13" E-ink 풀 안드로이드 14 | 모든 앱 | E-ink 매니아 | 소프트웨어 완성도, 고스팅 |
| **Unihertz Jelly Star** | Unihertz (중국) | $200~250 | 3" 초소형 풀 안드로이드 13 | 모든 앱 | 서브폰/업무용 | 화면 너무 작아 답답 |
| **Ghost Phone Plus / 4** | Ghost (미국) | Plus $359 (2023, Pixel 4 기반), Ghost Phone 4 $580→$630 (리셀러) | 중고 Pixel + GhostOS | 브라우저·앱스토어·SNS 차단 | 10대 첫 폰, 부모 | 독립 리뷰 거의 없음 |
| **Dumber Mini** | Simple Phones (영국) | £149 / €175 / $199 (프리오더, 출하 2026-12→2027-03) | 2.8" 터치 + 물리 키패드, Dumber OS(AOSP 14) | WhatsApp·Google Maps·NFC 결제·은행 가능 + 앱 차단기 | 가성비 입문자 | 미국 T-Mobile만, Wi-Fi 통화 없음 |

출처는 7장 참조. 가격은 조사일(2026-11-23) 기준으로 가장 최근 확인된 값.

---

## 2. 누가 사나

### 2-1. 연령·동기 (정성 자료)
- **r/dumbphones 모더레이터 Jose Briones**: 2019년 말 하루 12~13시간 스크린타임 → Light Phone 첫 시도 **실패**(구글 지도 없이 못 살아서) → 팬데믹 때 재시도해 정착. 커뮤니티 특징: "장기 상주자는 드물다. 대부분 들어와서 맞는 걸 찾고 떠난다." (WIRED 2024-05-06)
- **Light 공동창업자 Hollier**: "젊은 층에서 특히 관심이 커졌다"고만 언급, 구매자 프로파일 공개 안 함 (Prospect 2024-08-27).
- **학술 연구** (Rothschild & Lindqvist, *PACM HCI* CSCW1 2024, DOI 10.1145/3637402): 전통 덤폰 사용자 8명 + 디자이너 덤폰 사용자 5명 + 개발자 2명 인터뷰. 동기는 (1) 주의 분산 회피, (2) 온라인 감시 회피, (3) **종교적 이유**, (4) 정치적 탈퇴. 상당수가 "필요할 때 가족·지인의 스마트폰을 빌려 씀".
- **Techless / Sunbeam / Ghost Phone**: 명시적으로 기독교 가정·부모·10대 첫 폰 시장. Techless 창업자 Chris Kaspar는 Pure Desire(포르노 중독 회복 사역), Pints with Aquinas 등 종교 팟캐스트에 반복 출연. Sunbeam은 Patriot Mobile(보수 성향 MVNO)에서 유통.
- **HMD Barbie/3210**: 향수·패션 아이템. "디톡스"보다는 레트로.

### 2-2. 정량 자료 (시장 규모)
- 전 세계 피처폰: 2024년 약 **2.1억 대, $32억**, 전체 휴대폰의 ~15%. 하지만 "점진적 감소". **북미 170만 대, 유럽 1,200만 대(대부분 동유럽)** (Counterpoint, 2025-10-01).
- **북미 피처폰 판매 2023→2024 −38%, 서유럽 −11%** (Counterpoint, HMD Fuse 인사이트, 2025). 2023년 Counterpoint가 "미국 디톡스 트렌드로 280만 대 회복"이라 했던 전망이 뒤집힘.
- 분석가 Patrick Moorhead(CNBC 2023): "북미 덤폰 시장은 사실상 플랫." 
- "18~24세 플립폰 구매 2021→2024 +148%"는 출처가 불명확(BGR이 인용한 보고서명이 깨져 있음) — **신뢰도 낮음**.
- **한국**: 2024~25 국내 피처폰 판매 통계 **못 찾음**. 참고치: 2022년 Newsis "국내 피처폰 생산 사실상 0, 효도폰형 폴더도 단종 수순". 2023년 번개장터 '피처폰' 검색량 전년比 +177%(아시아경제 2023-10-12). 2025-02 Korea Times "MZ 듀얼폰" 기사는 디톡스가 아니라 **구형 iPhone을 카메라용 세컨폰**으로 쓰는 레트로 트렌드(iPhone 6S 중고 거래 +28%, 매물 +519%, 2023 번개장터). 한국경제 2026-08-28 "디톡스폰" 기사에 나온 유일한 국내 사례는 30대 직장인 한 명: "스마트폰을 완전히 버릴 수는 없지만 필요한 기능만 남긴 휴대전화라면 써보고 싶다." 업계 전망은 "세컨폰 또는 특정 소비층의 니치".

### 2-3. 해석
- 구매층은 크게 셋: (a) 20~30대 디톡스 시도자(대부분 서브폰·단기), (b) 종교·부모(자녀 첫 폰), (c) 프라이버시 지향 성인. 한국에서 (b)는 "키즈폰"이 이미 통신사 상품으로 존재하고, (a)는 레트로 소비로 흘러감.
- 실제 단일 제품 최대 판매량은 Light Phone 누적 10만 대(10년), Light Phone III 1년 2만 대. 전 세계 미니멀폰 전문업체 합계는 **연 수만 대** 수준으로 추정(공식 합계 없음).

---

## 3. 왜 스마트폰으로 돌아가나 (실사용자 인용)

반복되는 네 가지: **2FA/은행, 지도, 메신저(WhatsApp), 업무 요구**. 그리고 두 대 들고 다니는 피로.

1. **r/LightPhone "Goodbye for now" (2023-02-07, Light Phone II 3개월 사용)**  
   > "For me, as much as I want to love it, it doesn't fit me right now. The way messages work and not being able to share files was a huge step back for me. I ended up just carrying another device (iPad or laptop) and hot-spotting way too often."  
   → 결국 두 기기 체제가 되어 포기. 다만 SNS 앱은 지운 채로 유지.

2. **Engadget Light Phone III 리뷰 (2025-05-09)** — 리뷰어 본인의 차단 요인: "2FA 앱, 스마트폰이 필요한 콘서트 티켓, 모바일 수표 입금, 회사 인증용 폰". 결론: "The Light Phone succeeds at its mission, but the concept may be too limited for its own good… isn't quite ready to be most people's only device."

3. **WIRED 2024-05-06** — 기자 Aaron Gordon 본인도 Light Phone을 포기하고 iPhone으로 복귀(SNS 삭제·흑백·손목시계로 대체). 모더레이터 Briones의 첫 실패 이유: "구글 지도 없이 사는 게 너무 힘들었다."

4. **Jose Briones, "The Ideal Phone" (2024-07-14)** — "Light Phone으로 바꾸자 스마트폰 12~13시간이 노트북 8~10시간으로 옮겨갔을 뿐." 지도 때문에 별도 GPS를 샀고, 업무용으로 Jelly Star를 따로 씀. "폰은 방정식의 일부일 뿐, 생활방식이 더 중요하다." 새 셋업은 최소 2개월 유지 후 평가 권고.

5. **Mudita 포럼 "Straw that broke the camel's back, didn't even last 2 weeks"** — 2주 만에 포기. 다른 Kompakt 6개월 리뷰: "그냥 너무 느리다. 지도·카메라·WhatsApp이 때때로 사용 불가." "**Banking apps are an absolute no-go on this device.**"

6. **학술 연구 (Aalto/Rothschild 2024)** 보도자료: "구매·결제, 2FA 처리, 근무시간 외에도 연락되길 기대하는 고용주 요구가 어려움." 많은 참가자가 **가족의 스마트폰을 빌리는** 우회책 사용.

7. **r/dumbphones 일반 패턴 (every.to 2022 정리)**: "일상은 덤폰, 업무는 스마트폰" — 2FA와 캘린더 때문에 업무폰을 못 버림. 한 사용자(Briones)는 출장·화상회의 많은 주엔 SIM을 iPhone으로 옮기고 평소 80%만 덤폰.

8. **Light III 공식 입장(TechCrunch 2026-05-19)**: "일부 고객은 SIM 없는 구형 스마트폰을 Light Phone 핫스팟에 물려 함께 쓴다" — 회사도 **두 대 체제를 전제**로 설명.

9. **고객지원·통신사 문제**: Trustpilot thelightphone.com 3/5 (리뷰 6개): "SIM 분실 후 번호 복구 요청에 1달 넘게 무응답"(2025-08). Public Mobile(캐나다) 포럼: VoLTE 화이트리스트 미등록으로 통화 불가 → 반품. Verizon 미지원(PauseGadget). 호주 Vodafone 네트워크 제한 통보(2026-10, 단일 출처 미확인).

10. **한국 특수성** (국내 라이트폰 실사용 후기는 **못 찾음**): PASS 본인인증은 "본인 명의 휴대폰의 앱"에서만 가능, 은행 앱은 설치→본인인증→로그인이 전제(뉴스토마토, 이투데이). 즉 한국에선 2FA 문제가 미국보다 훨씬 심함 — 카카오톡·PASS·은행·삼성페이/네이버페이가 전부 스마트폰 앱 전용.

**정리**: 돌아가는 이유는 "의지 부족"이 아니라 **사회 인프라(인증·결제·직장)가 스마트폰을 전제**하기 때문. 성공 사례는 거의 전부 (a) 두 대 체제 또는 (b) 스마트폰에 제약을 거는 방식으로 귀결됨 — WIRED의 Briones 조언도 "지금 쓰는 폰부터 바꿔라(안드로이드 런처, iOS 스크린타임)".

---

## 4. 사업 현실

### Light (Light Phone)
- **창업**: 2014 Google 30 Weeks 인큐베이터에서 Joe Hollier(디자이너)·Kaiwei Tang 만남. 브루클린.
- **크라우드펀딩**: Light Phone 1 Kickstarter 2015 **$415,127 / 3,187명**. Light Phone II Indiegogo 2018 **$3,513,838 / 10,732명**.
- **투자**: 2019년 기준 누적 **약 $840만** VC(Foxconn 포함) (TechCrunch 2019-09-04). 2017년엔 $360만.
- **판매**: Light Phone 1 출하 ~1.5만 대(2019 시점, 예약 4만 미출하). Light Phone III 출시(2025-03) 후 1년간 **2만 대 출하**(TechCrunch 2026-05-19). **10년 누적 10만 대 이상**(Light 발표, 한국경제 2026-08-28).
- **직원**: Wikipedia 12명(2024), 2019년 핵심 10명+외주. 3rd-party DB는 1~10 또는 21~50으로 제각각.
- **수익성**: Hollier "광고·데이터 수익이 없어 덜 수익적", "사업을 굴리고 편히 살 만큼 버는 건 struggle", "투자자는 도덕적 관점에서 미션을 지지하는 이들" (Prospect 2024-08). 공식 매출 **미공개**.
- **수익 구조**: 하드웨어 + **자체 MVNO**(AT&T망, $25/1GB, $45; 초기 Gigs 통해 $30/1GB) + 2026년 Noble Mobile(Andrew Yang) 제휴 — 2년 $50/월($1,200) 약정 시 즉시 출하. RAM 부족으로 프리오더 대기 9월까지. 즉 **통신 요금 수익에 점점 의존**.
- **가격 궤적**: III $799 → 2026 $699. Flip $299 + $39/월 번들. "더 싸게, 통신 묶어서"로 이동.
- **제품 방향 논란**: Fast Company 2025 "Is the Light Phone getting too smart?" — OLED·카메라·앱 추가로 정체성 흔들림.

### Punkt
- 2008년 Petter Neby(노르웨이인) 설립, 루가노. **창업자 자본만으로 운영(bootstrapped)**. MP01은 2015~2023 판매. 판매량 **미공개**.
- MC02(2024)로 "미니멀"에서 "프라이버시 스마트폰"으로 피벗. 소프트웨어 Apostrophy OS는 별도 법인 **Apostrophy**(외부 투자 유치)로 분리, **구독 $15/월**. 리뷰 혹평.
- Neby: "MP01은 단독 기기가 아니라 보조 기기." — 회사 스스로 세컨폰 포지션.

### The Minimal Company
- 1세대 Indiegogo 2024-03 **$60만+** 모금. 출하 약속 2024-08 → 실제 2025-06(백커 #261 기준 **485일**). 2025-06 하드웨어 결함 재작업, 분실 소포 200건, 중국 백커 차별 논란, Discord에서 비판자 추방(1q43.blog, 2025).
- 그 상태에서 2세대 Kickstarter 2026-08: 2주 만에 2,400명·$160만(한국경제) / 1,466명·$957K(중간 집계). E-ink 포기하고 AMOLED.
- Trustpilot(2025 말~2026 초): 미배송, 가짜 송장, 환불 수 주 지연·차지백. 창업자가 2025-11에도 주문 누락 인정.
- **교훈**: 크라우드펀딩은 되지만 생산·CS가 무너지면 브랜드가 "사기 의혹"까지 감.

### Mudita
- CD Projekt 공동창업자 Kiciński의 자금. Pure(2019 KS, 폴란드 생산) → Kompakt(2024-10 KS **€353,751 / 1,078명 / 34개국**, 중국 생산으로 원가 절감). 직원 수 **모름**.
- 하드웨어 다각화(Harmony/Bell 알람시계, Element 시계)로 버팀. 소프트웨어 업데이트 느림, 2026 로드맵에 "E-ink 리프레시 모드 선택"이 올라올 정도.

### Techless (Wisephone)
- 하드웨어는 삼성 A시리즈 그대로, 가치는 전부 **WiseOS 구독($14.99/월)**. 미국 한정. 구독 안 하면 공장초기화 → 사실상 **소프트웨어 회사**. Trustpilot 청구 불만 다수.

### HMD
- 글로벌 피처폰 1위급(인도 월 200만 대). 2024년 두 자릿수 성장(한국경제). 그러나 **2025-07 미국 사업 축소/철수** (관세). Skyline "Detox Mode" 판매 효과 **미공개**. Boring Phone은 5,000대 경품용 마케팅. Barbie Phone만 실제 품절 반복.
- 즉 HMD에게 "디톡스"는 피처폰 마케팅 레이어일 뿐, 핵심 사업은 신흥국 저가 피처폰(HMD 100/101 INR 949).

### 실패/피벗 사례
- **Blloc** (베를린, 2016~): Zero18 흑백 폰(2018, €359) → 하드웨어 접고 **Ratio 런처**(2020, 구독/평생권) → **2023 팀이 Nothing.tech에 합류** → 2024-12 Ratio는 Prometheus LLC에 매각. 즉 **폰→런처→인수**의 역순.
- **Runcible**(2016 Indiegogo $25만+) — 미출하.
- **OnwardMobility** BlackBerry 5G — 2022 폐업.
- Punkt MP01 단종(2023), Light Phone II 사실상 단종(III·Flip으로 대체).

### 종합
- 이 시장에서 **하드웨어 단독으로 흑자**라고 공개한 회사는 **없음**. 생존 공식 = 창업자 자본/미션 투자자 + 크라우드펀딩 선수금 + 구독(OS 또는 통신) + 소량 생산. 2025~26년 RAM·관세로 원가 압박 심화.
- 공통 리스크: 통신사 호환(VoLTE 화이트리스트), 소량 생산 단가, 소프트웨어 유지비(지도·메시징 자체 구현), CS 인력.

---

## 5. 커뮤니티 규모

| 커뮤니티 | 규모 | 출처·시점 |
|---|---|---|
| r/dumbphones | <1만(2020) → 1.4만(2022) → 5만+(2024-05) → **10만+**(모더레이터 "State of the Dumbphone 2025", 2025-01-14) | every.to 2022, WIRED 2024-05-06, 레딧 미러 2025-01 |
| r/nosurf | **약 31.8만** | thehiveindex.com (2026-11-05 동기화); Mel Magazine 과거 17.2만 |
| r/digitalminimalism | 약 3.57만 (연도 불명 스냅샷) → **현재 모름** | 구글 시트 스냅샷; Reddit이 2025-09 멤버 수 공개 중단 |
| r/LightPhone | **못 찾음** (미러에 수치 미표시) | — |
| Mudita Forum | 자체 포럼 운영, 규모 미공개 | forum.mudita.com |
| Minimal Company Discord | 있음, 규모 미공개(백커 소통 창구, 추방 논란) | 1q43.blog |
| Dumbphone Finder (Briones) | 퀴즈형 추천 도구, 이용자 수 미공개 | WIRED 2024 |

참고: Reddit이 2025-09부터 멤버 수를 숨겨서 이후 수치는 모더레이터 발언이나 3rd-party 추정뿐.

---

## 6. 소프트웨어 제품·런처→폰 선례

| 회사 | 소프트웨어 | 방향 | 비고 |
|---|---|---|---|
| **Light** | LightOS (자체, 폰 전용). 2026 "developer program"으로 오픈소스 도구 개방 예고 | 폰 → OS 개방 | 런처로는 안 팔음. 가치는 OS에 있는데 하드웨어로만 판매 |
| **Techless** | WiseOS — 삼성 기기 위 잠금 OS, **구독 $14.99/월** | 실질적으로 소프트웨어 회사가 하드웨어를 껍데기로 사용 | Life OS와 가장 유사한 구조. 단 MDM식 잠금이라 자유도 낮음 |
| **Punkt / Apostrophy** | Apostrophy OS(AphyOS) — 별도 법인, 구독 $15/월, 1년 무료 | 폰 → OS 분사 | 프라이버시 OS, 타 제조사 탑재 노림 |
| **Mudita** | MuditaOS(Pure, 오픈소스) → MuditaOS K(Android 기반), Mudita Center 데스크톱 앱, 2026 "InkApps·런처" 예고 | 폰 → 앱/런처 확장 | |
| **The Minimal Company** | Minimal OS 런처(안드로이드 위) + "Focus Hours" | 폰과 동시 | 런처 단독 판매 **못 찾음** |
| **Simple Phones (Dumber)** | Dumber OS(AOSP 14) + 앱 차단기 | 폰과 동시 | |
| **Ghost** | GhostOS — 중고 Pixel에 설치 | 소프트웨어가 본체 | |
| **Blloc** | Ratio 런처(구독/평생권) | **폰(Zero18, 2018) → 런처(2020) → Nothing 합류(2023) → 런처 매각(2024-12)** | 질문의 "런처→폰"이 아니라 **폰→런처**였고, 결국 소프트웨어만 남아 대기업에 흡수됨. "Z18+Ratio로 500만 시간 절약" 주장 |
| **Nothing** | Nothing OS "Essential Key/Space" | 대기업 스마트폰에 미니멀 레이어 | 2025년 300만 대 출하(+86%, Omdia) — 디톡스 수요 때문인지는 불명 |
| **HMD** | Detox Mode(Skyline), Better Phone Project | 대기업 피처 | 효과 미공개 |

**런처→폰으로 성공한 선례: 못 찾음.** 반대 방향(폰 만들다 소프트웨어만 남김)은 Blloc, Punkt(Apostrophy), Techless(처음부터 삼성 기기)가 있음. 모든 생존 업체가 **구독**을 붙였다는 점이 Life OS 가격 모델에 시사점.

### Life On에 대한 함의 (제안)
1. 시장이 말하는 "미니멀폰 구매자의 진짜 니즈"는 **"스마트폰은 유지하되 잃어버린 시간을 돌려받고 싶다"** — 한국경제 30대 사례, Dumber Mini(£149, 은행·카톡 가능+차단기)의 인기, Minimal Phone 2의 AMOLED 회귀가 전부 같은 방향.
2. 한국은 PASS·은행·카카오톡 의존이 미국보다 심해 전용 하드웨어의 이탈률이 더 높을 것. **Life OS(소프트웨어)가 먼저**라는 현재 결정은 시장 증거와 일치.
3. 하드웨어로 가더라도 단독 흑자 사례가 없음. 통신 번들 또는 OS 구독이 전제. 규모 기대치는 "성공해도 연 수천~2만 대".

---

## 7. 출처

### Light Phone
- TechCrunch 2025-03-13 출시 예고: https://techcrunch.com/2025/03/13/minimalist-light-phone-iii-launches-march-27
- 9to5Google 2025-03-27 $799 출시: https://9to5google.com/2025/03/27/minimalist-light-phone-3-launches-with-a-maximalist-799-price/
- TechRadar (프리오더 $399, 2024-06): https://techradar.com/phones/tired-of-ai-and-apple-intelligence-this-privacy-focused-minimalist-phone-now-boasts-an-oled-screen
- Engadget 리뷰 2025-05-09: https://www.engadget.com/light-phone-iii-review-minimalism-stretched-to-the-point-of-frustration-141559294.html
- Consumer Reports ($699, RAM 부족): https://www.consumerreports.org/electronics-computers/cell-phones/light-phone-3-review-a1105801271/
- TechCrunch 2026-05-19 Noble Mobile 제휴, 2만 대 출하, $699: https://techcrunch.com/2026/05/19/the-minimalist-light-phone-teams-up-with-andrew-yangs-noble-mobile-which-pays-you-to-stop-doomscrolling/
- 9to5Google 2026-07-21 Light Flip $299, III $700: https://9to5google.com/2026/07/21/light-flip-minimalist-phone-announcement/
- 한국경제 2026-08-28 (누적 10만 대, Flip 매진, Minimal 2·Dumber·Mudita·HMD 수치): https://www.hankyung.com/article/202608277415g
- TechCrunch 2019-09-04 창업자 인터뷰 ($840만 투자, 1.5만 대): https://techcrunch.com/2019/09/04/light-phones-founders-discuss-life-beyond-the-smartphone
- Technical.ly 2017-02-15 ($360만): https://technical.ly/brooklyn/2017/02/15/light-phone-new-lab-joe-hollier-kai-tang/
- Prospect 2024-08-27 (수익성 struggle 발언): https://prospectmagazine.co.uk/views/people/67647/can-the-light-phone-help-us-switch-off
- Fast Company 2025 "Is the Light Phone getting too smart?": https://www.fastcompany.com/91549434/is-the-light-phone-getting-too-smart (403으로 본문 미확인, 검색 요약만)
- Wikipedia Light Phone (KS $415,127/3,187명, IGG $3,513,838/10,732명, 직원 12명): https://en.wikipedia.org/wiki/Light_Phone
- Light MVNO 요금: https://bestmvno.com/featured/light-launch-mvno-phone-plans , https://phonecompass.com/carriers/light/
- r/LightPhone "Goodbye for now" 2023-02-07 (미러): https://redlib.belloworld.it/r/LightPhone/comments/10w2cxo/goodbye_for_now_light_phone_review/
- Trustpilot thelightphone.com: https://nl.trustpilot.com/review/thelightphone.com
- PauseGadget Light III (Verizon 미지원, WhatsApp 없음): https://pausegadget.com/light-phone-iii-review/
- MKBHD 비판(지문·진동): https://rosetta.to/u/mkbhd/i-have-a-problem-with-the-light-phone-iii
- Public Mobile 포럼(VoLTE): https://productioncommunity.publicmobile.ca/t5/Get-Support/Does-the-Light-Phone-work-with-Public-Mobile/td-p/1078019

### Punkt / Apostrophy
- Punkt 가격: https://www.punkt.ch/collections/all , https://www.punkt.ch/en/products
- TechRadar MC02 리뷰 ($749): https://www.techradar.com/pro/phone-communications/punkt-mc02-5g-secure-phone-review
- Tom's Guide MC02 리뷰: https://tomsguide.com/phones/punkt-mc02-phone-review
- TechCrunch 2024-03-04 (bootstrapped, Apostrophy 외부 투자): https://techcrunch.com/2024/03/04/punkt-rocks-its-minimalist-roots-with-the-mc02-privacy-first-android-based-smartphone/
- Digital Trends MP02 리뷰: https://qa2.www.digitaltrends.com/android/punkt-mp-02-review
- Punkt 회사사/MP01 2015~2023: https://www.punkt.ch/pages/our-story , https://www.punkt.ch/pages/mp01-mobile-phone
- Apostrophy 구독: https://support.punkt.ch/en-US/articles/apostrophy-with-mc02-(os-services-webmail)-311203

### Minimal Company
- 1q43.blog 2025 백커 기록(485일, 결함, 분실 200건): https://1q43.blog/en/post/11548/
- PhoneArena 지연: https://www.phonearena.com/news/minimal-e-ink-phone-new-rumored-shipping-date_id166400
- Liliputing IGG $350~: https://liliputing.com/the-minimal-phone-hits-indiegogo-for-350-and-up-e-ink-phone-with-a-qwerty-keyboard/
- New Atlas ($60만+): https://newatlas.com/mobile-technology/minimal-phone-production
- Trustpilot minimalcompany.com: https://dk.trustpilot.com/review/minimalcompany.com
- 9to5Google 리뷰 2025-06-04: https://9to5google.com/2025/06/04/minimal-phone-review/
- Tom's Guide 2주 리뷰: https://www.tomsguide.com/phones/android-phones/i-tried-this-anti-distraction-phone-for-two-weeks-heres-my-verdict
- WIRED 리뷰 6/10 (미러): https://wirednews.pages.dev/review/minimal-phone
- Minimal Phone 2 KS ($957K/1,466명 중간): https://androidauthority.com/minimal-phone-2-android-kickstarter-3696854 , https://gagadget.com/en/723529-minimal-phone-2-raises-16m-on-kickstarter-betting-on-physical-keyboards/
- Digital Trends 2026-08-20 "다시 스마트폰으로": https://www.digitaltrends.com/phones/the-minimalist-phone-is-slowly-turning-back-into-a-smartphone/

### Mudita
- 가격 공개: https://mudita.com/community/blog/mudita-kompakt-price-reveal
- 프리오더 종료 안내 (€349/$369): https://www.mudita.com/community/blog/last-chance-to-get-mudita-kompakt-at-pre-order-pricing/
- KS 결과 €353,751/1,078명: https://mudita.com/about/media/tremendous-mudita-kompakt-kickstarter-success/
- 중국 생산: https://xyz.pl/mudita-powraca-z-nowym-telefonem-michal-kicinski-zdradza-plany/
- 포럼 리뷰(느림, 은행 불가): https://forum.mudita.com/t/mudita-kompakt-review-after-6-months/13028 , https://forum.mudita.com/t/struggling-on-keeping-the-kompakt/9080 , https://forum.mudita.com/t/straw-that-broke-the-camels-back-didnt-even-last-2-weeks/11879
- PauseGadget 리뷰: https://pausegadget.com/mudita-kompakt-review/
- Android Authority CES 2026: https://www.androidauthority.com/mudita-kompakt-hands-on-ces-2026-3631800

### Techless / Wisephone
- Charity Mobile ($399/$299, $14.99 구독, 90일 초기화): https://charitymobile.com/products/phones/techless-wisephone-ii , https://charitymobile.com/support/wiseos-subscription
- PauseGadget 리뷰: https://pausegadget.com/wisephone-ii-review/
- Trustpilot techless.com: https://www.trustpilot.com/review/techless.com
- Kaspar 종교 팟캐스트: https://puredesire.org/podcast/pure-desire-podcast/268-techless-w-chris-kaspar

### HMD
- Detox Mode: https://www.hmd.com/blog/digital-detox-mode-comes-to-hmd-skyline-smartphones
- Skyline $499: https://www.techradar.com/pro/phone-communications/hmds-new-skyline-smartphone-promises-easy-repairs-and-the-ability-to-disconnect
- Fuse (Vodafone UK 2025-08-20): https://www.vodafone.co.uk/newscentre/press-release/the-hmd-fuse-protected-with-harmblock-available-exclusively-on-vfuk/ ; AUD 799: https://www.gadgetguy.com.au/hmd-fuse-harmblock-plus-technology-release-date-price
- Barbie Phone $129: https://www.gsmarena.com/hmd_announces_barbie_phone-news-64298.php ; 품절: https://nokiamob.net/2024/09/01/barbie-phone-surpasses-expectations-in-popularity/ ; 재입고 2025-04: https://www.gizmochina.com/2025/04/22/hmds-barbie-phone-is-back-in-stock-still-bright-pink-and-nostalgically-basic/
- Nokia 3210 중국 매진 2024-05: https://www.gizmochina.com/2024/05/13/nokia-3210-stock-out-china/
- Boring Phone 5,000대: https://www.gsmarena.com/boring_phone_is_an_hmdmade_antismartphone-news-62478.php
- HMD 미국 축소 2025-07: https://9to5google.com/2025/07/11/hmd-calls-it-quits-on-the-us-smartphone-market-taking-nokia-with-it/
- HMD 인도 피처폰: https://www.tribuneindia.com/news/business/hmd-doubles-down-on-affordable-featurephones-segment-launches-hmd-100-hmd-101-to-capture-volume-leadership-crown

### 기타 기기
- Sunbeam $389 신형: https://notebookcheck.net/New-389-rugged-flip-phones-can-message-Signal-users-but-won-t-let-you-browse-the-internet.1430276.0.html ; Patriot Mobile 가격: https://patriotmobile.com/shop/devices/1670 ; PCMag F1 $195: https://www.pcmag.com/reviews/sunbeam-f1 ; Trustpilot: https://www.trustpilot.com/review/sunbeamwireless.com
- Bigme HiBreak Pro: https://parkablogs.com/index.php/content/review-bigme-hibreak-pro-e-ink-phone , https://www.notebookcheck.net/Compact-E-Ink-smartphone-with-color-display-NFC-and-5G-Bigme-unveils-HiBreak-Pro-Color.1114874.0.html
- Jelly Star $200~250: https://www.notebookcheck.net/9-dumb-phones-that-are-actually-smart-purchases-in-this-day-and-age.1065015.0.html
- Ghost Phone Plus $359 (2023-08): https://www.thewhitehatter.ca/post/product-review-ghost-phone-plus-an-option-as-a-teens-first-phone ; Ghost Phone 4 $630: https://reach.dog/shop/ghost-mode/products/ghost-phone-4
- Dumber Mini £149: https://pausegadget.com/dumber-mini-review/ , https://www.techeblog.com/dumber-mini-phone-specs-price-release/
- Blloc: https://www.blloc.com (Nothing 합류 2023, Prometheus 매각 2024-12) ; Zero18 €359: https://www.phonearena.com/news/BllocZero18-simplistic-black-and-white-phone_id105317 ; Ratio: https://9to5google.com/2020/10/08/blloc-ratio-launcher/

### 시장 데이터·커뮤니티·연구
- Counterpoint 2025-10-01 (2.1억 대, $32억, 북미 170만): https://counterpointresearch.com/en/insights/Dumbphones%20are%20getting%20smarter%20and%20more%20premium.%20Are%20they%20the%20solution%20for%20disillusioned%20phone%20addicts
- Counterpoint 북미 −38%/서유럽 −11%: https://counterpointresearch.com/insight/hmd-unveils-phone-with-parental-controls-and-social-media-locks
- Counterpoint 2023 미국 280만 대 전망: https://mobileworldlive.com/north-america/feature-phones-mount-a-comeback-in-us
- CNBC/Moorhead "flatlined" (EdSource 인용): https://edsource.org/updates/flip-phones-may-be-making-a-comeback-as-some-try-to-limit-screen-time
- BGR +148% (출처 불명확): https://bgr.com/2225417/flip-phones-making-a-comeback-gen-z
- WIRED 2024-05-06 r/dumbphones: https://wirednews.pages.dev/story/dumbphones-reddit-last-good-place (wired.com 미러)
- every.to 2022 (1.4만 명, 업무폰 2FA): https://every.to/cybernaut/the-quest-for-a-dumber-phone
- State of the Dumbphone 2025 (10만+): https://pol1.lr.ggtyler.dev/r/dumbphones
- r/nosurf 31.8만: https://thehiveindex.com/communities/r-nosurf/
- Reddit 멤버 수 비공개 2025-09: https://www.newsbytesapp.com/news/science/reddit-removes-member-count-from-subreddits-replaces-it-with-metrics/story
- Jose Briones "The Ideal Phone" 2024-07-14: https://josebriones.substack.com/p/the-ideal-phone
- Rothschild & Lindqvist 2024 PACM HCI: https://acris.aalto.fi/ws/portalfiles/portal/142144861/Swapping_5G_for_3G_-_Motivations_Experiences_and_Implications_of_Contemporary_Dumbphone_Adoption.pdf ; 보도자료: https://www.eurekalert.org/news-releases/1040750
- Omdia Nothing 300만 대 (한국경제 2026-08-28 인용)

### 한국
- 아시아경제 2023-10-12 번개장터 +177%: https://view.asiae.co.kr/article/2023101209190119837
- 한국경제 매거진 2023-08 폴더폰 잇아이템: https://magazine.hankyung.com/business/article/202308098338b
- Korea Times 2025-02-14 MZ 듀얼폰: https://www.koreatimes.co.kr/www/art/2025/02/199_392217.html
- Newsis 2022-12-21 피처폰 생산 0: https://mobile.newsis.com/view/NISX20221221_0002131574
- 뉴스토마토 PASS 인증: https://newstomato.com/ReadNews.aspx?no=931501
- 못 찾음: 국내 2024~25 피처폰 판매량, 국내 라이트폰 실사용 후기, r/LightPhone 멤버 수, Light 공식 매출, Punkt·Mudita 판매량·직원 수, Minimal Phone 2 최종 모금액, HMD Skyline/Detox 판매 효과.
