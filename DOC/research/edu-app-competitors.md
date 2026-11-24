# 조사 — 자막 기반 언어 학습 앱, 이미 있는가

> 조사일 2026-11-16. 아이디어: 넷플릭스 자막 파일을 넣으면 단어·표현을 예습·복습하고, 결국 그 영상을 **자막 없이** 볼 수 있게 하는 앱. 첫 언어는 영어(한국 시장)와 한국어(글로벌).
> 출처는 대부분 각 서비스의 자체 소개 페이지와 스토어 설명이라 기능·가격은 광고 수준으로 보고, 결제 전 재확인 필요.

---

## 한 줄 결론

**"보기 전에 그 작품의 단어를 미리 공부한다"는 아이디어는 이미 여러 팀이 만들었다.** 다만 모두 작고(1~2인, 2025~2026 출시), 영어·독어 중심이며, 한국어 UI나 한↔영 쌍을 다루는 곳은 없다. 한국 시장에는 이 방식이 아직 없다. 큰 플레이어(Migaku, Language Reactor)는 "보면서 클릭"이 중심이고 예습은 부수적이다.

---

## 1. 가장 가까운 것 — 예습(pre-watch) 전용

| 앱 | 자막 입력 | 언어 | 학습 방식 | 플랫폼 | 가격 | 규모·시점 |
|---|---|---|---|---|---|---|
| **Subvoca** (Lamma, Enes Ozturk) | 카탈로그에서 작품·에피소드 선택 → 앱이 자막을 가져옴. 파일 업로드 없음 | UI 영어만. 타깃 언어는 온보딩에서 선택 | 내 어휘와 대본의 "gap"만 5분 덱으로. 플래시카드·빈칸. 단어 상태 New/Learning/Almost/Known. **Coverage score, Watch Readiness score** | iPhone (Mac·Vision 미검증) | 무료+광고. 구독 "곧" | 2026-07 출시 v1.0, 평점 표시 안 될 정도로 적음 |
| **Flurret** (쌍둥이 형제, 파트타임) | 수천 개의 **미리 만든 덱** (영화·쇼·유튜브 자막) | 독어·영어만 | SRS 플래시카드, "등장 순서대로 보기 전에 학습". 둘째 기기에서 실시간 뜻 보기 | 미상 | 미상 | Product Hunt 약 1년 전(2025 말) |
| **TeakReader** (Ternary Space Labs 추정) | **자막 파일 import** + 퍼블릭도메인 영화 몇 편 | 영어 학습 전용 (CEFR). 한국어 언급 없음 | 빈도순 모르는 단어, CEFR 분포, Known/Learning/Ignored, **FSRS** 복습, 책(EPUB)과 어휘 기억 공유 | iPhone·iPad·Mac·Android | 무료 한도 + 프리미엄(가격 비공개) | 2026-10 신기능, 소규모 SEO 사이트 |
| LearnSubtitles (오픈소스) | .srt 파싱·표제어화 → 쉬움/중간/어려움 분류 | 영어 | 리스트만 | Python | 무료 | 오래됨 |

- 공통점: **목표를 "자막 없이"라고 말하는 곳은 없다.** 다들 "덜 멈추고 보기", "들어가서 모든 대사를 따라가기"로 표현한다. Subvoca의 Watch Readiness score가 가장 가까운 지표.
- 공통 약점: 영어 UI, 작은 팀, 광고 모델, 한↔영 없음.

## 2. 큰 플레이어 — "보면서 클릭"형

| 앱 | 핵심 | 예습·진척 지표 | 언어 | 가격 |
|---|---|---|---|---|
| **Migaku** | 크롬 확장 (Netflix·Disney+·Viki·YouTube) + iOS/Android. 단어 클릭 → 원클릭 플래시카드(문장·음성·클립·스샷). 자체 자막 import 있음 | **콘텐츠별 comprehension score**, "이 드라마가 쉬울지 어려울지 예측". 작품별 예습 기능은 없음. 기초 코스 1,300단어 = "콘텐츠의 80%+" | 11개: 한국어·영어 포함 | $10/월, $96/년, 평생 $499 |
| **Language Reactor** | 크롬 확장, 이중자막, 단어 클릭·저장, Anki 내보내기. 넷플릭스 자막 업로드는 프리미엄(비공식 정보) | 단어를 learning/known/skipped로 표시. **에피소드별 아는 단어 %는 없음** (포럼 요청만) | 다수 | 무료 + Pro |
| Lingopie | 자체 콘텐츠 카탈로그 + 크롬 확장. 이중자막, SRS | — | 다수 (한국어 포함) | 구독 |
| Lingflix | 유튜브 링크 또는 **로컬 영상+SRT/VTT 업로드**. 이중자막 클릭, SRS | 없음 | 68개 "모든 조합" | $14.99/월, $99.99/년 |
| Kimchi Reader | 한국어 학습용 확장. Netflix·Viki 이중자막, 자동 일시정지, 사전 기록 자동 추적 | — | 한국어 | — |
| Viki Learn Mode | Viki 자체. 한국어·중국어 자막 클릭, 북마크, 퀴즈 | — | 한·중 | 무료(Viki) |

## 3. 한국 시장

| 앱 | 콘텐츠 | 방식 | 목표 문구 | 규모 |
|---|---|---|---|---|
| **랭플릭스 Langflix** (Metalingo) | 유튜브 영상 분석 + 자체 큐레이션 "인기 시리즈와 영화" 클립. **넷플릭스·업로드 아님** | 이중자막 → 표현 탭 → 섀도잉 → 퀴즈 → "오늘의 복습"(SRS). 크롬 확장 | **"자막 없이 미드 보는 게 가능해질 때까지"** | 4.8점 127개, 월 $9.99~ |
| 레드키위 RedKiwi | 자체 클립(미드·영화·BBC) | 듣기 퀴즈, 워드맵 | — | 대형 |
| 케이크 Cake | 자체 짧은 클립 | 표현 저장·복습 | — | 1억 다운로드 |
| 돌림 Dollim (1인 개발, 클리앙 2026-05) | 유튜브 iframe + 자막 오버레이 | 자막 탭 → 저장·번역, 루프 | 자막 없이 보고 → 자막 켜서 확인 | 개인 |
| Lingvoca | 영상 자막 탭 번역, 단어장 | 미니게임 | — | 평가 18개 |

- 한국 앱은 전부 **자체 클립 또는 유튜브**. 사용자가 보는 넷플릭스 작품을 그대로 다루는 곳은 없다.
- 랭플릭스가 "자막 없이"를 목표로 내건 유일한 한국 앱이지만, 방식은 "보면서 섀도잉". 예습 아님.

---

## 4. 빈 자리 (아직 아무도 안 하는 조합)

1. **내가 보려는 그 작품** (넷플릭스) + **보기 전 예습** + **자막 없이 보기를 측정 가능한 목표로.** 셋을 함께 하는 곳이 없다. Subvoca가 1·2에 가장 가깝지만 영어 UI, 광고, 초기 단계.
2. **한↔영 쌍.** 한국인의 영어, 외국인의 한국어를 한 제품에서. 한국어 학습은 K-드라마 수요가 큰데 도구는 Migaku·Kimchi Reader(확장 프로그램)뿐.
3. **표현(expression) 단위.** 대부분 단어 단위. "구동사·관용구를 뽑는다"는 Language Reactor의 약점으로도 지적됨.
4. **쌓이는 진척.** 어느 앱도 "이 작품에서 아는 비율이 62% → 71%"를 중심 화면에 놓지 않는다. LIFE AND SURE의 "판정하지 않고 쌓인다" 원칙이 그대로 들어갈 자리.

## 5. 주의할 것

- **자막 파일 확보가 사용자 몫.** 넷플릭스는 자막 파일을 공식 제공하지 않는다. Subvoca·TeakReader는 카탈로그/OpenSubtitles로 돌아감. "파일을 넣는다"가 첫 마찰이자 약관 회색지대. ⚠️ 창업자가 정할 것: 업로드형으로 갈지, 카탈로그형으로 갈지.
- **큰 플레이어가 한 기능으로 따라올 수 있다.** Migaku는 이미 comprehension score가 있어서 "작품별 예습 덱" 하나만 붙이면 된다. 차별점은 기능이 아니라 **목표 중심 경험과 한국 시장 UX**에 두어야 한다 (브리프 3-8과 같은 결론).
- 모든 비교 대상의 기능·가격은 2026-11-16 자체 소개 기준. 실제 설치해 확인한 것은 없다.

## 6. 다음에 확인하면 좋은 것

- Subvoca와 TeakReader를 직접 설치해 "예습 덱 → 시청" 경험을 써보기.
- Migaku의 comprehension score가 에피소드 단위인지, 예습으로 이어지는지.
- 랭플릭스 사용자 리뷰에서 "자막 없이"에 대한 기대와 불만.

---

## 출처

- Subvoca: https://apps.apple.com/app/id6779768440 · https://play.google.com/store/apps/details?id=dev.lamma.subvoca
- Flurret: https://www.producthunt.com/p/flurret/flurret
- TeakReader: https://teakreader.com/movie-vocabulary-before-watching · https://teakreader.com/about
- LearnSubtitles: https://github.com/heitor31415/LearnSubtitles
- Migaku: https://migaku.com/ · https://migaku.com/learn-korean
- Language Reactor: https://languagereactor.com/pro-mode · https://languagereactor.com/help/faq · https://forum.languagelearningwithnetflix.com/t/percentage-of-already-learnt-vocabulary-in-subtitles/25381
- Lingflix: https://www.lingflix.app/en/
- Kimchi Reader: https://kimchi-reader.app/feature
- Viki Learn: https://www.soompi.com/article/1225667wpp/supercharge-korean-studies-viki-learn-vikis-brand-new-language-learning-site
- 랭플릭스: https://apps.apple.com/us/app/id6739905184?l=ko
- 레드키위: https://apps.apple.com/us/app/id1227219560?l=ko · 케이크: https://apps.apple.com/KR/app/id1350420987
- 돌림(클리앙): https://www.clien.net/service/board/lecture/19194931
- Lingvoca: https://apps.apple.com/kr/app/id1664034863
- 참고 비교글(경쟁사 작성, 편향 주의): https://lingopie.com/blog/language-reactor-review/ · https://www.trancy.org/blog/best-migaku-alternative-for-netflix-learning-2026-32e9d225200580aab232d657ebfceb13
