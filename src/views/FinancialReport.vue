<script setup lang="ts">
import { computed, ref } from 'vue'


type Item = { label: string; amount: number; color?: string }
type ItemWithPct = Item & { pct: number }

// 포맷터
const fmtKRW = (n: number) => new Intl.NumberFormat('ko-KR').format(n)
const fmtPct = (n: number) => `${n.toFixed(1)}%`

// 아크 유틸
const polar = (cx: number, cy: number, r: number, angle: number) => {
  const rad = (angle - 90) * (Math.PI / 180)
  return { x: cx + r * Math.cos(rad), y: cy + r * Math.sin(rad) }
}
const arcPath = (cx: number, cy: number, r: number, start: number, end: number) => {
  const s = polar(cx, cy, r, end)
  const e = polar(cx, cy, r, start)
  const large = end - start <= 180 ? 0 : 1
  return `M ${s.x} ${s.y} A ${r} ${r} 0 ${large} 0 ${e.x} ${e.y}`
}

/**********************************수입**************************************/
// 1) 데이터
const items1 = ref<Item[]>([
  { label: '보험 tw', amount: 2_000_000, color: '#1C77F2' },
  { label: 'g팡 환불 tw', amount: 369_000, color: '#2D99FF' },
  { label: '장학금', amount: 2_100_000, color: '#5EB1FF' },
])
// #1C77F2 (진한 블루)
// #2D99FF (중간 블루)
// #5EB1FF (밝은 블루)
// #7DC3FF (하늘색 톤)
// #A6C7F9 (파스텔 블루)
// #CFE4FF (아주 연한 블루)
// #525B61 (짙은 차콜)
// #6B7280 (미디엄 그레이)
// #9AA9B2 (밝은 그레이)
// #E2E8F0 (연한 실버톤)

// 2) 합계/비율
const total = computed(() => items1.value.reduce((sum, i) => sum + i.amount, 0))
const withPct = computed<ItemWithPct[]>(() =>
  items1.value.map((i) => ({
    ...i,
    pct: total.value ? (i.amount / total.value) * 100 : 0
  }))
)


// 3) 도넛 누적각 (0~360)
const accumAngles = computed<number[]>(() => {
  const arr = withPct.value
  const a: number[] = [0]
  let acc = 0
  for (const it of arr) {
    acc += it.pct || 0
    a.push((acc / 100) * 360)
  }
  return a
})

// 4) 세그먼트(템플릿 타입안정)
const segments = computed(() =>
  withPct.value.map((i, idx) => ({
    item: i,
    start: accumAngles.value[idx] ?? 0,
    end: accumAngles.value[idx + 1] ?? 0
  }))
)

// 5) 차트 인터랙션 (호버 시 중앙 라벨 변경/강조)
const hoverIdx = ref<number | null>(null)
const centerTitle = computed(() =>
  hoverIdx.value == null ? '총 수입' : segments.value[hoverIdx.value]?.item.label ?? '총 수입'
)

const centerSub = computed(() => {
  if (hoverIdx.value == null) return fmtKRW(total.value)
  const seg = segments.value[hoverIdx.value]
  return seg ? `${fmtPct(seg.item.pct)} · ${fmtKRW(seg.item.amount)}` : ''
})
/**********************************지출**************************************/
// 수입 - 지출
const remaining = computed(() => total.value - total2.value)

// 1) 데이터
const items2 = ref<Item[]>([
  { label: '행정사 tw', amount: 1_500_000, color: '#065F46' },
  { label: 'BB 파워에이드 tw', amount: 4_800, color: '#047857' },
  { label: '비상주사무실 1년계약', amount: 259_000, color: '#10B981' },
])
// #065F46 (딥 그린·짙은 초록)
// #047857 (청록빛 중간 초록)
// #10B981 (밝은 에메랄드)
// #34D399 (민트 톤 밝은 초록)
// #6EE7B7 (파스텔 민트)
// #A7F3D0 (아주 연한 민트)
// #064E3B (딥 포레스트·짙은 차콜 초록)
// #15803D (리치 그린·미디엄 톤)
// #4ADE80 (라이트 그린)
// #BBF7D0 (연한 실버톤 그린)


// 2) 합계/비율
const total2 = computed(() => items2.value.reduce((sum, i) => sum + i.amount, 0))
const withPct2 = computed<ItemWithPct[]>(() =>
  items2.value.map((i) => ({
    ...i,
    pct: total2.value ? (i.amount / total2.value) * 100 : 0
  }))
)

// 3) 도넛 누적각 (0~360)
const accumAngles2 = computed<number[]>(() => {
  const arr = withPct2.value
  const a: number[] = [0]
  let acc = 0
  for (const it of arr) {
    acc += it.pct || 0
    a.push((acc / 100) * 360)
  }
  return a
})

// 4) 세그먼트
const segments2 = computed(() =>
  withPct2.value.map((i, idx) => ({
    item: i,
    start: accumAngles2.value[idx] ?? 0,
    end: accumAngles2.value[idx + 1] ?? 0
  }))
)

// 5) 차트 인터랙션 (호버 시 중앙 라벨 변경/강조)
const hoverIdx2 = ref<number | null>(null)
const centerTitle2 = computed(() =>
  hoverIdx2.value == null ? '총 지출' : segments2.value[hoverIdx2.value]?.item.label ?? '총 지출'
)

const centerSub2 = computed(() => {
  if (hoverIdx2.value == null) return fmtKRW(total2.value)
  const seg = segments2.value[hoverIdx2.value]
  return seg ? `${fmtPct(seg.item.pct)} · ${fmtKRW(seg.item.amount)}` : ''
})


</script>

<template>
  <section class="frsection1">
    <header class="pageHead">
      <h1>재정보고</h1>
      <p class="sub">재원 사용 보고 · <span class="accent">2025 수입</span></p>
    </header>

    <div class="grid">
      <!-- 표 -->
      <div class="card">
        <h2>수입 내역</h2>
        <table class="tbl" aria-label="2025 수입 내역">
          <thead>
          <tr>
            <th scope="col">구분</th>
            <th scope="col" class="num">금액(원)</th>
            <th scope="col" class="num">비율(%)</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="i in withPct" :key="i.label">
            <th scope="row">
              <span class="dot" :style="{ background: i.color ?? '#999' }" aria-hidden="true" />
              {{ i.label }}
            </th>
            <td class="num">{{ fmtKRW(i.amount) }}</td>
            <td class="num">{{ fmtPct(i.pct as number) }}</td>
          </tr>
          </tbody>
          <tfoot>
          <tr>
            <th scope="row">합계</th>
            <td class="num">{{ fmtKRW(total) }}</td>
            <td class="num">100.0%</td>
          </tr>
          </tfoot>
        </table>
      </div>

      <!-- 도넛 차트 -->
      <div class="card chartCard">
        <h2>수입 비율</h2>
        <svg viewBox="0 0 300 300" role="img" aria-label="수입 비율 도넛 차트">
          <!-- 도넛 베이스 -->
          <circle cx="150" cy="150" r="110" class="ring" />
          <circle cx="150" cy="150" r="75" fill="#fff" />

          <!-- 세그먼트 -->
          <g>
            <template v-for="(seg, idx) in segments" :key="seg.item.label">
              <path
                :d="arcPath(150,150,110, seg.start, seg.end)"
                :stroke="seg.item.color ?? '#999'"
                :stroke-width="hoverIdx === idx ? 40 : 30"
                fill="none"
                class="seg"
                @mouseenter="hoverIdx = idx"
                @mouseleave="hoverIdx = null"
              />
            </template>
          </g>

          <!-- 중앙 라벨 -->
          <text x="150" y="138" text-anchor="middle" class="centerTitle">
            {{ centerTitle }}
          </text>
          <text x="150" y="160" text-anchor="middle" class="centerSub">
            {{ centerSub }}
          </text>
        </svg>

        <ul class="legend">
          <li v-for="(i, idx) in withPct" :key="i.label" :class="{ active: hoverIdx === idx }"
              @mouseenter="hoverIdx = idx" @mouseleave="hoverIdx = null">
            <span class="swatch" :style="{ background: i.color ?? '#999' }" />
            <span class="name">{{ i.label }}</span>
            <span class="val">{{ fmtPct(i.pct as number) }}</span>
          </li>
        </ul>
      </div>
    </div>
  </section>
  <!-- ▼▼ 섹션 2: 지출 (item2) ▼▼ -->
  <section class="frsection2">
    <header class="pageHead">
      <p class="sub">재원 사용 보고 · <span class="accent">2025 지출</span></p>
    </header>

    <div class="grid">
      <!-- 표 -->
      <div class="card">
        <h2>지출 내역</h2>
        <table class="tbl" aria-label="2025 지출 내역">
          <thead>
          <tr>
            <th scope="col">구분</th>
            <th scope="col" class="num">금액(원)</th>
            <th scope="col" class="num">비율(%)</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(i, idx) in withPct2" :key="`${i.label}-${idx}`">
            <th scope="row">
              <span class="dot" :style="{ background: i.color ?? '#999' }" aria-hidden="true" />
              {{ i.label }}
            </th>
            <td class="num">{{ fmtKRW(i.amount) }}</td>
            <td class="num">{{ fmtPct(i.pct as number) }}</td>
          </tr>
          </tbody>
          <tfoot>
          <tr>
            <th scope="row">합계</th>
            <td class="num">{{ fmtKRW(total2) }}</td>
            <td class="num">100.0%</td>
          </tr>
          <!-- ▼ 남은금액 행 -->
          <tr>
            <th scope="row">남은금액</th>
            <td class="num" :class="{ neg: remaining < 0 }">
              {{ fmtKRW(remaining) }}
            </td>
          </tr>
          </tfoot>
        </table>
      </div>

      <!-- 도넛 차트 -->
      <div class="card chartCard">
        <h2>지출 비율</h2>
        <svg viewBox="0 0 300 300" role="img" aria-label="지출 비율 도넛 차트">
          <!-- 도넛 베이스 -->
          <circle cx="150" cy="150" r="110" class="ring" />
          <circle cx="150" cy="150" r="75" fill="#fff" />

          <!-- 세그먼트 -->
          <g>
            <template v-for="(seg, idx) in segments2" :key="`${seg.item.label}-${idx}`">
              <path
                :d="arcPath(150,150,110, seg.start, seg.end)"
                :stroke="seg.item.color ?? '#999'"
                :stroke-width="hoverIdx2 === idx ? 40 : 30"
                fill="none"
                class="seg"
                @mouseenter="hoverIdx2 = idx"
                @mouseleave="hoverIdx2 = null"
              />
            </template>
          </g>

          <!-- 중앙 라벨 -->
          <text x="150" y="138" text-anchor="middle" class="centerTitle">
            {{ centerTitle2 }}
          </text>
          <text x="150" y="160" text-anchor="middle" class="centerSub">
            {{ centerSub2 }}
          </text>
        </svg>

        <ul class="legend">
          <li v-for="(i, idx) in withPct2" :key="`${i.label}-legend-${idx}`"
              :class="{ active: hoverIdx2 === idx }"
              @mouseenter="hoverIdx2 = idx" @mouseleave="hoverIdx2 = null">
            <span class="swatch" :style="{ background: i.color ?? '#999' }" />
            <span class="name">{{ i.label }}</span>
            <span class="val">{{ fmtPct(i.pct as number) }}</span>
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<style scoped>
:global(.financialreport.page) {   /* 이 뷰에서만 */
  background: #fff !important;
}
:global(html), :global(body), :global(#app) { background:#fff; }
:root { --bg:#ffffff; --ink:#0f172a; --muted:#667085; --line:#e7eef7; }
.frsection1 {
  max-width: 1100px;
  margin: 40px auto;
  padding: 0 20px;
}
.frsection2 {
  max-width: 1100px;
  margin: 40px auto;
  padding: 0 20px;
}
.pageHead h1 {
  font-size: 36px;
  margin: 100px 0 8px;
  font-weight: 700;
}
.pageHead .sub { color: var(--muted); margin: 0 0 24px; }
.pageHead .accent { color:#1C77F2; font-weight:600; }

.grid { display: grid; grid-template-columns: 1.1fr 1fr; gap: 24px; }
.card { background: var(--bg); border: 1px solid var(--line); border-radius: 16px; padding: 20px; box-shadow: 0 2px 10px rgba(15,23,42,.06); }
.card h2 {
  margin: 0 0 16px;
  font-size: 18px;
  font-weight: 500;
}

/* 음수면 색만 살짝 경고톤 */
.neg { color: #b91c1c; }


.tbl { width: 100%; border-collapse: collapse; font-size: 14px; }
.tbl th, .tbl td { padding: 10px 12px; border-bottom: 1px solid var(--line); }
.tbl thead th { background: #f8fbff; text-align: left; }
.tbl .num { text-align: right; font-variant-numeric: tabular-nums; }
.tbl tfoot th, .tbl tfoot td { font-weight: 700; }
.dot { display:inline-block; width:10px; height:10px; border-radius:50%; margin-right:8px; vertical-align:middle; }

.chartCard { display:flex; flex-direction: column; align-items:center; }
.ring { fill: none; stroke: #f1f5f9; stroke-width: 30; }

.seg { cursor: pointer; transition: stroke-width .18s ease, opacity .18s ease; opacity: .95; }

.legend { list-style: none; display:grid; grid-template-columns: 1fr 1fr; gap: 8px 12px; padding: 0; margin: 8px 0 0; width:100%; }
.legend li { display:flex; align-items:center; justify-content:space-between; gap:10px; border-bottom:1px dashed var(--line); padding:6px 0; transition: color .15s ease; }
.legend li.active .name { color: #0f172a; font-weight: 600; }
.legend .swatch { width:10px; height:10px; border-radius:50%; }
.legend .name { flex:1; margin-left:8px; color: var(--muted); }
.legend .val { font-variant-numeric: tabular-nums; }

.centerTitle { font-size: 13px; font-weight: 600; fill: #64748b; }
.centerSub { font-size: 14px; font-weight: 700; fill: #1C77F2; }
@media (max-width: 900px) { .grid { grid-template-columns: 1fr; } }
</style>
