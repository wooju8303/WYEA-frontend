<template>
  <div class="marquee" :style="rootStyle" aria-label="partners">
    <div class="strip">
      <div v-for="(logo, i) in repeated" :key="i" class="item">
        <img :src="logo.src" :alt="logo.alt || ''" :style="imgStyle(logo)" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
type Logo = { src: string; alt?: string; width?: number; height?: number }

const props = withDefaults(defineProps<{
  logos: Logo[]
  /** 한 바퀴 도는 시간(초) — 반복 개수가 늘어나면 이동량이 줄어드니 속도 유지하려면 비례해서 줄이세요 */
  duration?: number
  /** 로고 사이 간격(px) */
  gap?: number
  /** 로고 세로 높이(px) */
  logoHeight?: number
  /** 호버 시 멈춤 */
  pauseOnHover?: boolean
  /** 🔥 몇 세트를 이어붙일지 (기본 4세트) */
  repeat?: number
}>(), {
  duration: 30,
  gap: 48,
  logoHeight: 26,
  pauseOnHover: true,
  repeat: 4,
})

/** repeat회 반복한 배열 생성 */
const repeated = Array.from({ length: props.repeat }).flatMap(() => props.logos)

/** CSS 변수 주입 */
const rootStyle = {
  '--duration': `${props.duration}s`,
  '--gap': `${props.gap}px`,
  '--h': `${props.logoHeight}px`,
  '--pause': props.pauseOnHover ? 'paused' : 'running',
  // 이동량: 전체의 1/repeat 만큼만 이동하면 한 세트가 정확히 지나감 (예: 4세트면 25%)
  '--shift': `${100 / props.repeat}%`,
  '--shift-neg': `-${100 / props.repeat}%`, // -25% ✅
} as Record<string, string>

const imgStyle = (l: Logo) => ({
  height: l.height ? `${l.height}px` : `var(--h)`,
  width: l.width ? `${l.width}px` : 'auto',
})
</script>

<style scoped>
.marquee{
  /* 기본값(fallback) — IDE 경고 방지 */
  --gap: 48px;
  --duration: 30s;
  --h: 26px;
  --pause: paused;
  --shift: 50%;
  --shift-neg: -50%;

  position: relative;
  overflow: hidden;

  /* 좌우 끝단 페이드 (Safari 대응용 -webkit-mask 포함) */
  --fade: linear-gradient(to right, transparent 0, #000 8%, #000 92%, transparent 100%);
  mask-image: var(--fade);
  -webkit-mask-image: var(--fade);
}

/* 로고 띠 */
.strip{
  display: inline-flex;
  gap: var(--gap);
  padding-block: 20px;

  /* 이음매 보정: 시작/끝의 좌우 여백을 동일하게 */
  padding-inline: calc(var(--gap) / 2);

  /* 무한 루프: 한 세트 길이만큼 이동 */
  animation: scroll var(--duration) linear infinite;
  will-change: transform;
}

/* 호버 시 일시정지(옵션) */
.marquee:hover .strip { animation-play-state: var(--pause); }

.item img{
  display: block;
  height: var(--h);
  width: auto;
  opacity: .65;
  filter: grayscale(100%);
  transition: opacity .2s ease;
}
.item img:hover{ opacity: 1; }

/* 🔥 repeat에 따라 이동량을 변수로: -var(--shift) */
@keyframes scroll {
  from { transform: translateX(0); }
  to   { transform: translateX(var(--shift-neg));  }
}

/* 모션 민감 사용자 */
@media (prefers-reduced-motion: reduce){
  .strip { animation: none; }
}
</style>
