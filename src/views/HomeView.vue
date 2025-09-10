<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'
import FloatingDecor, { type DecorItem } from '@/components/FloatingDecor.vue'

import backpack from '@/assets/image/backpack.png'
import camera from '@/assets/image/camera.png'
import carrier from '@/assets/image/carrier.png'
import charger from '@/assets/image/charger.png'
import coin from '@/assets/image/coin.png'
import compass from '@/assets/image/compass.png'
import headphones from '@/assets/image/headphones.png'
import mobile from '@/assets/image/mobile.png'
import passport from '@/assets/image/passport.png'
import pens from '@/assets/image/pens.png'
import sunglasses from '@/assets/image/sunglasses.png'
import wallet from '@/assets/image/wallet.png'
import sticker from '@/assets/image/sticker.png'
import bill from '@/assets/image/bill.png'
import map from '@/assets/image/map.png'
import LogoMarquee from '@/components/LogoMarquee.vue'

const decorItems: DecorItem[] = [
  { src: coin, from: 'right', right: '0px', top: '30%', width: 140, rotZ:70, delay: .25 },
  { src: carrier, from: 'top-right', right: '-250px', top: '-30%', width: 700, rotZ:70, delay: .25 },
  { src: passport, from: 'right',  right: '0px', top: '60%',  width: 200, rotZ:-60, delay: .25 },
  { src: charger, from: 'right',  right: '-40px', top: '34%', width: 300, rotZ:30, delay: .30 },
  { src: compass, from: 'down',  right: '350px', top: '0%',  width: 150, rotZ:30, delay: .25 },
  { src: bill, from: 'right',  right: '-120px', top: '70%',  width: 200, rotZ:-20, delay: .25 },
  { src: map, from: 'up',  right: '-80px', top: '60%',  width: 700, rotZ:100, delay: .25 },

  { src: coin, from: 'left', left: '0px', top: '50%', width: 100, rotZ:170, delay: .30 },
  { src: backpack, from: 'top-left',  left: '-90px', top: '-5%',  width: 400, rotZ:30, delay: .25 },
  { src: camera, from: 'down',  left: '140px', top: '0%',  width: 220, rotZ:110, delay: .25 },
  { src: pens, from: 'left',  left: '100px', top: '55%',  width: 150, rotZ:0, delay: .23 },
  { src: headphones, from: 'left',  left: '0px', top: '30%',  width: 250, rotZ:-50, delay: .27 },
  { src: mobile, from: 'left',  left: '-80px', top: '55%',  width: 200, rotZ:45, delay: .25 },
  { src: wallet, from: 'left',  left: '0px', top: '80%',  width: 150, rotZ:-10, delay: .27 },
  { src: sunglasses, from: 'left',  left: '10px', top: '65%',  width: 200, rotZ:-10, delay: .23 },
  { src: sticker, from: 'up',  left: '100px', top: '80%',  width: 300, rotZ:30, delay: .25 },
]

const logos = [
  { src: coin, alt: 'Axis' },
  { src: backpack, alt: 'Raposa Coffee' },
  { src: camera, alt: 'Superteam' },
  { src: pens, alt: 'OpenZeppelin' },
  { src: headphones, alt: 'Forma' },
  { src: mobile, alt: 'WONDERSTRUCK' },
]

/**
 * 히어로 가시성 → 데코 숨김 여부
 */
const heroRef = ref<HTMLElement | null>(null)
const decorHidden = ref(false)

let io: IntersectionObserver | null = null
let prevHidden = decorHidden.value
let initialized = false // 초기 1회 콜백 무시

onMounted(() => {
  const rootStyles = getComputedStyle(document.documentElement)
  const headerH = parseFloat(rootStyles.getPropertyValue('--header-h')) || 64

  const HIDE_AT = 0.90  // 이 이하로 보이면 숨김(= 스크롤 ~10% 이상)
  const SHOW_AT = 0.95  // 이 이상 보이면 다시 표시 (경계 흔들림 방지)

  // 혹시 기존 io가 있으면 정리
  io?.disconnect()

  io = new IntersectionObserver(
    (entries: IntersectionObserverEntry[]) => {
      const entry = entries[0]
      if (!entry) return

      const r = entry.intersectionRatio

      // 최초 1회는 레이아웃 안정 전 호출될 수 있으니 무시
      if (!initialized) { initialized = true; return }

      // 현재 상태에 따라 서로 다른 임계값 적용(히스테리시스)
      let nextHidden = prevHidden
      if (!prevHidden && r < HIDE_AT) nextHidden = true
      else if (prevHidden && r > SHOW_AT) nextHidden = false

      // 값이 실제로 바뀔 때만 반영 (불필요한 재렌더/토글 방지)
      if (nextHidden !== prevHidden) {
        decorHidden.value = nextHidden
        prevHidden = nextHidden
      }
    },
    {
      threshold: [0, 0.5, 0.9, 0.95, 1], // 우리가 쓰는 경계 포함
      rootMargin: `-${headerH}px 0px 0px 0px`,
    }
  )

  if (heroRef.value) io.observe(heroRef.value)
})

onBeforeUnmount(() => {
  io?.disconnect()
  io = null
})


</script>

<template>
  <section class="home-hero" ref="heroRef">
    <div class="center">
      <img src="@/assets/image/wyea-logo.png" width="300">
      <h3>Young World<br> Exchange Association</h3>
      <br>
      <p>세계 청년 교류회</p>
      <button>가입하러 가기</button>
    </div>
    <div class="logo-bar">
      <p class="eyebrow">지부와 협력 대학</p>
      <!-- 4세트(기본값)로 사용 -->
      <LogoMarquee :logos="logos" :duration="18" :gap="56" :repeat="4" :logoHeight="28"
                   style="max-width: 800px; width: 100%; margin: 10px auto 0;"/>
    </div>
  </section>
  <FloatingDecor :items="decorItems" :hidden="decorHidden"/>

  <!-- 데코는 컴포넌트로 분리 -->

  <section id="about" class="about">
    <div class="container">
      <h3>우리는 무엇을 하나요?</h3>
      <br>
      <h3>
        여기에 소개/미션/프로그램 등 내용을 작성하세요.
      </h3>
      <br>
      <h3>
        작성
      </h3>
      <br>
      <h3>
        작성
      </h3>
      <br>
    </div>
  </section>
</template>

<style scoped>
.home-hero {
  position: relative;
  display: column;
  place-items: center;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
}

/* 중앙 카피 애니메이션 (슬라이드 튀어나옴) */
.center > * {
  opacity: 0;
  transform: translateY(24px) scale(0.98);
  animation: slidePop .6s cubic-bezier(.2,.8,.2,1) forwards;
}
.center img {
  animation-delay: .10s;
}
.center h3 {
  animation-delay: .12s;
  font-weight: 700;
}
.center p {
  animation-delay: .24s;
  font-weight: 700;
  font-size: 1.25rem;
}
.center button {
  animation-delay: .36s;
  pointer-events: auto;
  background:#000; color:#fff;
  padding:12px 20px; border-radius:999px;
  font-weight:700; border:none; cursor:pointer;
  box-shadow:0 6px 18px rgba(0,0,0,.12);
}

@keyframes slidePop {
  0%   { opacity:0; transform: translateY(24px) scale(0.98); }
  60%  { opacity:1; transform: translateY(-4px) scale(1.02); }
  100% { opacity:1; transform: translateY(0) scale(1); }
}

.logo-bar { margin-top: 60px; }

.eyebrow {
  color:#7a7a7a;
  font-weight: 700;
  font-size:16px;
  margin-bottom: 8px; }

.about {
  position: relative;
  display: flex;
  place-items: center;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
}
</style>
