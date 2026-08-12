# 시황 리포트 작성 규칙

- 리포트 실행 시 항상 portfolio.json을 먼저 읽어 보유종목을 파악한다.
- 결과물은 reports/YYYY-MM-DD-HHMM.html 로 저장한다. 날짜는 `date +%Y-%m-%d`, 시각은 실행 회차(0700/1200/1800)로 고정한다.
- 상단: 한눈에 보는 요약 대시보드. matplotlib으로 그린 차트를 PNG로 만든 뒤 base64로 인코딩해 <img> 태그로 HTML에 직접 임베드한다. 외부 CDN이나 인터넷 이미지 링크에 의존하지 않는다.
- 하단: 등락 이유, 전망, 개별 종목 분석 등 설명이 필요한 부분은 문자로 상세히 서술한다.
- 수치는 실제 검색 결과에 근거해서만 작성하고, 출처를 함께 표기한다.
- 표는 HTML <table>로 만든다.
- 완성된 HTML 파일은 git add/commit/push까지 완료한다.
- `<head>`에 `<meta name="viewport" content="width=device-width, initial-scale=1">`를 반드시 포함한다 (없으면 모바일 브라우저가 데스크톱 너비로 렌더링한 뒤 축소해서 글씨가 전부 깨알같이 보인다). 아래 디자인 가이드의 반응형 CSS와 `.table-scroll` 래퍼도 반드시 함께 적용한다.

## 리서치 범위 (2026-08-12 개편)

- 기존 항목(미국증시 마감, 대형주, 반도체 섹터, 기타 섹터, 등락 이유, 지정학/기상재해/원자재, 한국증시 전망) 외에도 그날 국내외 주요 뉴스(정치·외교·산업정책·규제·기업 이슈 등 증시에 영향 줄 수 있는 사안)를 폭넓게 검색하고, 실제로 관련성 있는 것만 선별해 반영한다.
- 서술은 추상적 표현("긍정적 흐름 예상") 대신 구체적 수치·날짜·발언자·기관명을 명시한다.

## 디자인 가이드 (2026-08-12 개편)

리포트는 아래 CSS를 그대로 재사용한다 (기존 리포트와 시각적 일관성 유지). 값만 교체하고 클래스/구조는 임의로 바꾸지 않는다.

```css
body { font-family: -apple-system, "Segoe UI", "Noto Sans KR", sans-serif; max-width: 980px; margin: 0 auto; padding: 24px 20px 60px; color: #0b0b0b; background: #fcfcfb; line-height: 1.65; }
h1 { font-size: 24px; margin-bottom: 4px; }
.subtitle { color: #52514e; font-size: 14px; margin-bottom: 24px; }
h2 { font-size: 19px; margin-top: 40px; border-left: 4px solid #2a78d6; padding-left: 10px; }
h3 { font-size: 16px; margin-top: 24px; color: #1c1c1a; }
.dashboard { background: #fff; border: 1px solid #e1e0d9; border-radius: 10px; padding: 16px; margin-bottom: 16px; }
.dashboard img { max-width: 100%; height: auto; display: block; margin: 0 auto; }
table { border-collapse: collapse; width: 100%; margin: 14px 0 20px; font-size: 14px; }
th, td { border: 1px solid #e1e0d9; padding: 8px 10px; text-align: center; }
th { background: #f3f2ee; font-weight: 600; }
td.name { text-align: left; font-weight: 500; }
.up { color: #c0392b; font-weight: 600; }
.down { color: #2a78d6; font-weight: 600; }
.flat { color: #52514e; font-weight: 600; }
.note { background: #f9f9f7; border: 1px solid #e1e0d9; border-radius: 8px; padding: 12px 16px; font-size: 13.5px; color: #52514e; margin: 16px 0; }
.sources { font-size: 12.5px; color: #898781; margin-top: 40px; border-top: 1px solid #e1e0d9; padding-top: 16px; }
.sources li { margin-bottom: 4px; }
.holding-box { background: #fff8e6; border: 1px solid #eda100; border-radius: 8px; padding: 14px 18px; margin: 16px 0; }
.tag { display:inline-block; background:#eef4fc; color:#184f95; border-radius:4px; padding:1px 8px; font-size:12px; margin-left:6px; }

/* 향후 5거래일 주요 일정 카드 */
.schedule-grid { display:grid; grid-template-columns: repeat(5, 1fr); gap:10px; margin: 16px 0 8px; }
.schedule-day { background:#fff; border:1px solid #e1e0d9; border-radius:10px; overflow:hidden; display:flex; flex-direction:column; }
.schedule-day.today { border-color:#2a78d6; box-shadow: 0 0 0 1px #2a78d6; }
.schedule-day-header { background:#1c1c1a; color:#fff; font-size:12.5px; font-weight:700; padding:8px 6px; text-align:center; }
.schedule-day.today .schedule-day-header { background:#184f95; }
.schedule-day-header .today-tag { display:block; font-size:10px; font-weight:600; color:#9ec5f4; margin-top:1px; }
.schedule-cards { padding:8px; display:flex; flex-direction:column; gap:7px; flex:1; }
.sched-card { border-left:3px solid #898781; background:#f9f9f7; border-radius:5px; padding:7px 8px; }
.sched-card .cat { display:block; font-size:10px; font-weight:700; margin-bottom:3px; letter-spacing:.02em; }
.sched-card .title { font-size:12px; color:#0b0b0b; line-height:1.4; margin-bottom:3px; }
.sched-card .time { font-size:10.5px; color:#898781; }
.sched-card.cat-macro { border-left-color:#2a78d6; } .sched-card.cat-macro .cat { color:#184f95; }
.sched-card.cat-earnings { border-left-color:#eb6834; } .sched-card.cat-earnings .cat { color:#c14e1f; }
.sched-card.cat-news { border-left-color:#1baf7a; } .sched-card.cat-news .cat { color:#0f8c5f; }
.sched-card.cat-geo { border-left-color:#4a3aa7; } .sched-card.cat-geo .cat { color:#4a3aa7; }
.schedule-legend { display:flex; gap:16px; font-size:11.5px; color:#52514e; margin-bottom:28px; flex-wrap:wrap; }
.schedule-legend span { display:inline-flex; align-items:center; gap:5px; }
.schedule-legend i { width:9px; height:9px; border-radius:2px; display:inline-block; }

/* 표 가로 스크롤 래퍼 (모바일에서 표가 찌그러지지 않도록 모든 <table>은 이 div로 감싼다) */
.table-scroll { overflow-x:auto; -webkit-overflow-scrolling:touch; margin: 14px 0 20px; }
.table-scroll table { margin: 0; }

/* 반응형: 좁은 화면(모바일) 대응 */
@media (max-width: 640px) {
  body { padding: 16px 14px 48px; font-size: 15.5px; }
  h1 { font-size: 21px; }
  .subtitle { font-size: 13px; }
  h2 { font-size: 17px; margin-top: 32px; }
  h3 { font-size: 15px; }
  table { font-size: 12.5px; }
  th, td { padding: 6px 7px; }
  .schedule-grid { display:flex; grid-template-columns: unset; overflow-x: auto; -webkit-overflow-scrolling: touch; padding-bottom: 6px; scroll-snap-type: x proximity; }
  .schedule-day { min-width: 168px; flex: 0 0 auto; scroll-snap-align: start; }
  .note, .holding-box { font-size: 13px; }
}
```

### 반응형 · 표 규칙 (필수)

- 모든 `<table>`은 `<div class="table-scroll"><table>...</table></div>` 형태로 감싼다. 열이 많은 표(등락률 상위 20/30종목 등)가 좁은 화면에서 찌그러지지 않고 옆으로 스크롤되도록 하기 위함이다.
- "향후 5거래일 주요 일정" 카드는 데스크톱에서는 5열 그리드, 모바일(640px 이하)에서는 가로 스크롤 캐러셀로 자동 전환된다(위 CSS가 처리) — 별도 대응 불필요, CSS를 그대로 사용만 하면 된다.
- 리포트 HTML `<head>`에는 항상 `<meta charset="utf-8">` 다음 줄에 `<meta name="viewport" content="width=device-width, initial-scale=1">`를 넣는다.

### 차트: 심플 가로 막대형 (필수)

여러 개의 서브플롯이나 복잡한 대시보드를 만들지 않는다. 지수·섹터·원자재 등락률은 **하나의 가로 막대(diverging horizontal bar) 차트**로 표현한다.

- 색상: **상승 = 빨강(#c0392b), 하락 = 파랑(#2a78d6)** — 한국 증시 관행(미국식 초록/빨강 아님).
- 0 기준선 하나만 표시, 눈금선은 옅은 회색(#e1e0d9) hairline, 축 테두리(spine)는 모두 제거.
- 막대 끝에 값(`+x.xx%` / `-x.xx%`)을 직접 라벨링. 범례는 "상승/하락" 2개만.
- 폰트가 깨지지 않도록 아래 방식으로 한글 폰트를 탐색해 모든 텍스트 요소(제목/축/범례/라벨)에 적용한다. 못 찾으면 라벨을 영문으로 대체해 글자 깨짐(네모 박스)을 방지한다.

```python
import matplotlib.font_manager as fm

font_path = None
for path in fm.findSystemFonts():
    name = path.lower()
    if any(k in name for k in ["notosanscjk", "nanumgothic", "notosanskr", "malgun"]):
        font_path = path
        break
fp = fm.FontProperties(fname=font_path) if font_path else fm.FontProperties()
```

참고 구현 (값만 교체):

```python
import matplotlib.pyplot as plt
from matplotlib.patches import Patch

UP, DOWN, INK, SUB, MUTED, GRID, SURFACE = "#c0392b", "#2a78d6", "#0b0b0b", "#52514e", "#898781", "#e1e0d9", "#fcfcfb"
labels = ["다우존스", "S&P500", "나스닥", "SOX(반도체)", "코스피", "코스닥", "WTI 원유", "국제 금"]
values = [...]  # 등락률(%)

fig, ax = plt.subplots(figsize=(7.6, 4.3), dpi=160)
fig.patch.set_facecolor(SURFACE); ax.set_facecolor(SURFACE)
y = list(range(len(labels)))
colors = [UP if v >= 0 else DOWN for v in values]
ax.barh(y, values, height=0.55, color=colors, zorder=3)
ax.axvline(0, color=MUTED, linewidth=1, zorder=2)
ax.xaxis.grid(True, color=GRID, linewidth=1, zorder=1); ax.set_axisbelow(True)
ax.set_yticks(y); ax.set_yticklabels(labels, fontproperties=fp, fontsize=11.5, color=INK)
ax.invert_yaxis(); ax.set_xticks([]); ax.tick_params(left=False)
for spine in ax.spines.values(): spine.set_visible(False)
xmax = max(abs(v) for v in values); pad = xmax * 0.07
for yi, v in zip(y, values):
    label = f"{v:+.2f}%"
    ax.text(v + (pad if v >= 0 else -pad), yi, label, va="center", ha="left" if v >= 0 else "right",
             fontsize=10.5, color=INK, fontproperties=fp)
ax.set_xlim(-xmax * 1.4, xmax * 1.4)
fig.text(0.02, 0.97, "주요 지수·원자재 등락률", fontproperties=fp, fontsize=14.5, color=INK, ha="left", va="top", fontweight="bold")
fig.text(0.02, 0.905, "전일 종가 기준, 자료: 각 거래소", fontproperties=fp, fontsize=9.5, color=SUB, ha="left", va="top")
fig.subplots_adjust(top=0.82)
leg = ax.legend(handles=[Patch(facecolor=UP, label="상승"), Patch(facecolor=DOWN, label="하락")],
                 loc="lower right", frameon=False, fontsize=10, prop=fp)
for t in leg.get_texts(): t.set_color(SUB)
```

### 향후 5거래일 주요 일정 카드 섹션 (신규, 0700 리포트 전용)

**이 섹션은 0700(아침) 리포트에만 넣는다. 1200/1800 리포트에는 넣지 않는다** — 해당 리포트들은 각자 요청받은 항목(정오 실황 비교·상위종목, 마감 시황·거래대금 상위·보유종목 분석 등)만 다룬다.

0700 리포트에서는 "한눈에 보는 요약 대시보드" 바로 다음에 "향후 5거래일 주요 일정" 섹션(`<h2>`)을 추가한다.

- 오늘부터 영업일 기준 5일치(오늘 포함, 주말 제외)를 요일별 카드로 구성한다. 오늘 컬럼은 `.schedule-day today` 클래스와 "오늘" 태그를 붙인다.
- 각 일정은 아래 4개 카테고리 중 하나로 분류해 클래스를 지정한다: `cat-macro`(경제지표, 파랑), `cat-earnings`(실적발표, 주황), `cat-news`(국내외 주요뉴스, 초록), `cat-geo`(지정학/이벤트, 보라).
- 각 카드에 한국시간 기준 시각을 표기한다.
- 채워 넣는 일정은 그날 웹 리서치로 확인한 실제 예정 이벤트만 사용한다(미 경제지표 발표 일정, 주요 기업 실적 발표일, 한국은행 금통위, 국내외 지정학 이벤트 등). 추측 금지, 출처는 하단 출처 목록에 함께 표기한다.
- HTML 구조는 위 CSS의 `.schedule-grid > .schedule-day > .schedule-day-header + .schedule-cards > .sched-card` 구조를 그대로 따르고, 하단에 `.schedule-legend`로 4개 카테고리 범례를 표시한다.
