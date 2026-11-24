# 일(Work) 벤치마크 — 가장 많이 쓰는 템플릿, 서비스, 오픈소스

> 조사일 2026-11-03. 세 갈래 동시 조사 결과를 Claude가 종합.
> 인기 수치는 대부분 제작사·제작자가 직접 밝힌 값. 확인 못 한 값은 (미검증).

---

## 1. 무엇을 봤나

| 갈래 | 대상 | 인기 근거 (대표) |
|---|---|---|
| **노션 템플릿** | Ultimate Brain, Ultimate Tasks (Thomas Frank) · Second Brain (Easlo) · PPV (August Bradley) · BASB PARA 템플릿 (Tiago Forte) · 노션 공식 Projects & Tasks | Ultimate Brain "4만+ 사용자", 템플릿 매출 약 76만 달러 (자체 주장) |
| **서비스** | Todoist · Things 3 · TickTick · MS To Do · Apple 미리알림 · Sunsama · Akiflow · Motion · Reclaim · **Structured** · Notion Calendar · Toggl · Rize · RescueTime · Timing | Todoist 등록자 3,000만+, **Structured 다운로드 1,500만+** |
| **깃허브** | AppFlowy · AFFiNE · Logseq · **Super Productivity** · **ActivityWatch** · Dayflow · danielmiessler/LifeOS · solidtime · Vikunja · Taskwarrior · obsidian-lifeos 등 | AppFlowy ★78k, Super Productivity ★23k, ActivityWatch ★19k (2026-11-03 확인) |

---

## 2. 세 갈래가 같이 말하는 것

### 2-1. 어디에나 있는 진짜 핵심
1. **오늘 화면.** 성공한 거의 모든 도구의 첫 화면이 "오늘"이다. (Things Today, MS To Do My Day, Todoist Today, Ultimate Brain My Day, PPV Action Zone)
2. **할 일.** 상태, 마감일, 반복. 여섯 개 노션 템플릿 모두에 있다.
3. **프로젝트 ↔ 할 일.** 할 일이 끝나면 프로젝트 진행률이 오른다. 역시 모두에 있다.
4. **빠르게 던져두기 → 오늘 실행.** Inbox나 Quick Capture에 모아두고, 오늘 화면에서 실행한다.
5. **주간 돌아보기.** 개인용 노션 시스템 네 개 모두에 있다. 일주일에 한 번 30분 정도.

### 2-2. 노션 템플릿에 없는 것 = 빈자리
- **시간.** 인기 노션 템플릿 어디에도 시간 기록과 타임블로킹이 사실상 없다. 날짜만 있다.
- **삶의 나머지.** 일 관리 서비스는 일만 본다. 운동, 식사, 잠과 이어지지 않는다.
- → **할 일 + 시간 + 삶의 나머지가 한 타임라인 위에 있는 곳이 없다.** 이게 Life and Sure 일 모듈의 자리.

### 2-3. 우리 홈과 가장 닮은 선례: Structured
- 하루를 **세로 타임라인 한 줄**로 보여주는 것만으로 다운로드 1,500만 이상. 그룹 내 1위.
- 무료로 타임라인과 Inbox, 유료(Pro)로 AI 하루 계획과 놓친 일 재배치 → **"직접은 무료, 자동은 유료"와 같은 구조.**
- 다만 일과 일정만 다룬다. 운동, 식사, 잠은 없다.

---

## 3. 가져올 아이디어

| 아이디어 | 출처 | 우리에게 |
|---|---|---|
| **오늘을 직접 고른다** | Things, My Day, Ultimate Brain | 할 일 중 오늘 할 것을 고르는 행동 자체가 하루의 시작 |
| **계획한 시간과 실제 시간을 나란히** | Sunsama | 타임라인에 계획 블록을 놓고, 시작·끝을 누르면 실제 시간이 됨. "계획 3시간 → 실제 4시간 20분" 한 줄 |
| **계획 블록을 그대로 실적으로** | Sunsama | 타이머를 켜는 걸 잊는 문제(Toggl의 1위 불만)를 피함. 다르면 그때만 고침 |
| **하루 마감 한 번** | Sunsama 종료 의식, BASB Shutdown | "못 끝낸 일 → 내일로?" 한 번만 묻기. 일과 삶의 경계 |
| **날짜별 쌓이는 시간** | Super Productivity의 할 일별 날짜별 소요 시간 | 할 일 하나에 오늘 쓴 시간이 쌓임 → 하루 리포트가 바로 나옴 |
| **모든 기록을 하나의 "구간"으로** | ActivityWatch 이벤트 모델 | {시작, 길이, 모듈, 출처(직접·기기·Sure)} 하나로 네 모듈을 한 타임라인에 그림 (공통 틀의 "순간"과 같은 생각) |
| **집중 세션은 할 일에 붙은 구간** | Super Productivity, TickTick | 뽀모도로를 따로 만들지 않고, 할 일에 이어진 시간 구간으로 |
| **주간 돌아보기** | 노션 개인 템플릿 전부 | 데스크톱에 잘 맞음 |
| **AI 요약** | Dayflow, Timing | 하루 요약, 주간 돌아보기 초안 → Sure+ |
| **빠른 입력** | Todoist 자연어·음성 | 한 줄로 할 일 던지기 |

---

## 4. 피해야 할 함정

| 함정 | 사례 | 이유 |
|---|---|---|
| **다층 분류 체계** | PARA, 영역·라벨·우선순위, GTD 세부 상태 | 노션 Life OS 불만 1위가 학습 부담과 설정 부담 |
| **범용 데이터베이스 빌더** | AppFlowy, Focalboard, 노션 그 자체 | 유연한 만큼 개인은 만들다 지침 |
| **매일 해야 하는 긴 의식** | Sunsama | 하루 빠지면 앱이 쓸모없어지고 이탈 |
| **승인 없는 자동 재배치** | Motion | 통제감을 잃는다는 불만이 가장 큼. Sure는 코파일럿이지 조종사가 아님 |
| **화면 감시형 자동 기록** | RescueTime, Rize, Dayflow | 사생활 부담, 아이폰에서는 불가능 |
| **외부 연동 과잉** | Akiflow, Super Productivity(연동 10개 이상) | 처음엔 캘린더 하나면 충분 |
| **무료가 없는 구조** | Sunsama, Motion, Akiflow | 세 곳 모두 가격 불만이 1순위 |
| **팀 기능** | Planka, solidtime, 노션 공식 템플릿 | 개인 앱에 필요 없음 |

---

## 5. Claude 종합 — 일 모듈의 뼈대 후보 [제안]

조사 결과를 우리 결정(오늘 중심, 타임라인 홈, 직접은 무료·자동은 유료, 심플, 채팅 없음)에 맞춰 줄이면:

### 무료 — 내가 직접
```
던져두기  →  할 일  →  오늘 고르기  →  타임라인에 놓기  →  시작·끝  →  하루 마감
 (한 줄)    (프로젝트·    (오늘 할 것)    (계획한 시간)      (실제 시간)   (못 끝낸 일 → 내일?)
            목표와 이을 수 있음)
                                                          + 주간 돌아보기 (데스크톱)
```
- 남기는 것: 던져두기, 할 일, 프로젝트(선택), 오늘, 타임라인 블록, 실제 시간, 하루 마감, 주간 돌아보기.
- 버리는 것: 영역·자료·보관 체계, 라벨, 우선순위 매트릭스, 여러 보기, 팀 기능.
- **프로젝트 → 목표 연결**: 일이 삶의 목표로 이어지는 유일한 고리. (노션 Life OS의 핵심 가치를 가볍게)

### Sure+ — Sure가 자동으로
- 캘린더(그리고 나중에 쓰던 할 일 앱)에서 가져오기
- 오늘 할 일 제안 (사용자가 승인)
- 놓친 블록 다시 놓기 제안 (승인 후 적용, Motion 함정 회피)
- 하루 요약, 주간 돌아보기 초안
- 일이 길어진 날 다른 모듈과 이어서 말 걸기 (야근 → 운동 빠짐 → 잠 줄어듦)

### 차별점 한 줄
> **노션 Life OS에는 시간이 없고, 일 관리 앱에는 삶이 없다. Life and Sure의 일은 삶의 타임라인 위에 놓인다.**

---

## 6. 창업자가 정할 것
1. 위 뼈대(던져두기 → 할 일 → 오늘 → 타임라인 → 시작·끝 → 하루 마감 → 주간 돌아보기)가 "너무 심플하지도, 너무 무겁지도 않은지".
2. 프로젝트를 둘지. 둔다면 목표와 이을지.
3. 하루 마감과 주간 돌아보기를 무료에 둘지 (직접 하는 의식) — Sure+는 그 초안을 대신 써주는 쪽.
4. 데스크톱 자동 기록(화면 활동)을 아예 안 할지, 나중에 Sure+ 데스크톱 앱으로 할지.

---

## Sources

### 노션 템플릿
- https://thomasjfrank.com/brain/
- https://typefully.com/TomFrankly/dollar1-million-in-notion-template-sales-kuFT0iD
- https://www.starterstory.com/stories/thomas-frank
- https://www.notion.com/templates/ultimate-brain
- https://www.producthunt.com/products/ultimate-brain-for-notion/reviews
- https://thomasjfrank.com/templates/task-and-project-notion-template/
- https://www.easlo.co/templates/second-brain
- https://money.yahoo.com/made-239-000-selling-budgeting-120626735.html
- https://benlangsnotes.substack.com/p/tens-thousands-customers-selling-notion-templates-meet-easlo-22-yo
- https://augustbradley.kit.com/posts/when-minds-become-media
- https://ransurf.github.io/quartz/LDP/600-Resources/Pillars-Pipelines-Vaults-PPV
- https://buildingasecondbrain.com/notion-template
- https://www.notion.com/help/guides/getting-started-with-projects-and-tasks

### 서비스
- https://en.wikipedia.org/wiki/Todoist
- https://apps.apple.com/us/app/structured-daily-planner/id1499198946
- https://structured.app
- https://www.sunsama.com/pricing
- https://blog.saner.ai/sunsama-reviews
- https://www.usemotion.com/pricing
- https://blog.saner.ai/motion-reviews
- https://reclaim.ai/pricing
- https://toggl.com/track/
- https://rize.io/pricing
- https://www.rescuetime.com/pricing
- https://timingapp.com/pricing
- https://www.todoist.com/ramble

### 깃허브
- https://github.com/super-productivity/super-productivity
- https://github.com/super-productivity/super-productivity/blob/master/src/app/features/tasks/task.model.ts
- https://github.com/ActivityWatch/activitywatch
- https://github.com/ActivityWatch/docs/blob/master/src/buckets-and-events.rst
- https://github.com/JerryZLiu/Dayflow
- https://github.com/danielmiessler/LifeOS
- https://github.com/quanru/obsidian-lifeos
- https://github.com/logseq/logseq
- https://github.com/AppFlowy-IO/AppFlowy
- https://github.com/solidtime-io/solidtime
- https://github.com/go-vikunja/vikunja
