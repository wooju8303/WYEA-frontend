<script setup lang="ts">
import { computed, ref } from 'vue'

type Item = { label: string; amount: number; color?: string }
type ItemWithPct = Item & { pct: number }

// 1) 데이터
const items = ref<Item[]>([
  { label: '행정사 tw', amount: 1_500_000, color: '#5EB1FF' },
  { label: 'BB 파워에이드 tw', amount: 4_800, color: '#2D99FF' },
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
const total = computed(() => items.value.reduce((sum, i) => sum + i.amount, 0))
const withPct = computed<ItemWithPct[]>(() =>
  items.value.map((i) => ({
    ...i,
    pct: total.value ? (i.amount / total.value) * 100 : 0
  }))
)

// 3) 포맷터
const fmtKRW = (n: number) => new Intl.NumberFormat('ko-KR').format(n)
const fmtPct = (n: number) => `${n.toFixed(1)}%`

// 4) 아크 유틸
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

// 5) 도넛 누적각 (0~360)
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

// 6) 세그먼트(템플릿 타입안정)
const segments = computed(() =>
  withPct.value.map((i, idx) => ({
    item: i,
    start: accumAngles.value[idx] ?? 0,
    end: accumAngles.value[idx + 1] ?? 0
  }))
)

// 7) 차트 인터랙션 (호버 시 중앙 라벨 변경/강조)
const hoverIdx = ref<number | null>(null)
const centerTitle = computed(() =>
  hoverIdx.value == null ? '총 수입' : segments.value[hoverIdx.value]?.item.label ?? '총 수입'
)

const centerSub = computed(() => {
  if (hoverIdx.value == null) return fmtKRW(total.value)
  const seg = segments.value[hoverIdx.value]
  return seg ? `${fmtPct(seg.item.pct)} · ${fmtKRW(seg.item.amount)}` : ''
})

</script>

<template>
  <section class="frsection1">
    <header class="pageHead">
      <h1>재정보고</h1>
      <p class="sub">2025 재원 사용 보고 · <span class="accent">2025 수입</span></p>
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
