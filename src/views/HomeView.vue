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

import busan from '@/assets/image/busan.svg'
import kyungnam from '@/assets/image/kyungnam.svg'
import inje from '@/assets/image/inje.svg'
import gyeongsang from '@/assets/image/gyeongsang.svg'
import yonsei from '@/assets/image/yonsei.svg'
import kyunghee from '@/assets/image/kyunghee.svg'
import woosongcollege from '@/assets/image/woosongcollege.svg'
import seoul from '@/assets/image/seoul.webp'

/**
 * 홈 메인 이미지 컴포넌트
 */
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

/**
 * 협력 대학 이미지 컴포넌트
 */
const logos = [
  { src: busan },
  { src: kyungnam },
  { src: inje },
  { src: gyeongsang },
  { src: yonsei },
  { src: kyunghee },
  { src: woosongcollege },
  { src: seoul },
]

/**
 * 홈 메인 이미지 컴포넌트 스크롤시 숨기기
 */
const section1Ref = ref<HTMLElement | null>(null)
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

  if (section1Ref.value) io.observe(section1Ref.value)
})

onBeforeUnmount(() => {
  io?.disconnect()
  io = null
})
</script>

<template>
  <section class="section1" ref="section1Ref">
    <div class="section1-div1">
      <img src="@/assets/image/wyea-logo.png" width="300">
      <h3>World Youth<br> Exchange Association</h3>
      <br>
      <p>세계 청년 교류회</p>
      <a href="https://docs.google.com/forms/d/e/1FAIpQLSddVMd4CovJdhX6IbOXtXcjhXRsrSNJkXXqRtY3RuvOF-HrWA/viewform?pli=1" target="_blank" rel="noopener noreferrer">
        <button>가입하러 가기</button>
      </a>
    </div>
    <div class="section1-div2">
      <p>협력 대학</p>
      <LogoMarquee :logos="logos" :duration="18" :gap="56" :repeat="4" :logoHeight="60"
                   style="max-width: 800px; width: 100%; margin: 10px auto 0;"/>
    </div>
    <div class="section1-div3">
      <p>스크롤 해서 계속 보기 ↓</p>
    </div>
  </section>

  <FloatingDecor class="decor" :items="decorItems" :hidden="decorHidden"/>

  <hr class="hr1">

  <section class="section2">
    <div>
      <h3>우리는 무엇을 하나요?</h3>
      <div class="section2-div1">
        <div class="section2-card1">
          <h4>설립 배경</h4>
          <p>어떻게 설립하게 되었는가</p>
        </div>
        <div class="section2-card1">
          <h4>주요 활동</h4>
          <p>무슨 활동을 하는가</p>
        </div>
      </div>
    </div>
  </section>

  <section class="section3">
    <div>
      <h3>활동사진 활동사진</h3>
      <p>사진</p>
    </div>
  </section>

  <section class="section4">
    <div>
      <h3>활동사진 활동사진</h3>
      <p>사진</p>
    </div>
  </section>

  <footer class="footer">
    <div class="footer-top">
      <p>주소</p>
      <p>
        회장 이창현 010-1234-1234
      </p>
    </div>

    <div class="footer-middle">
      <p>
        Copyright © 2025.WYEA.All rights reserved.
      </p>
    </div>

    <div class="footer-bottom">
      <a href="https://www.instagram.com" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/instaricon.png" class="footer-logo" alt="instagram"/>
      </a>
      <a href="mailto:wyea@wyea.info" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/mailicon.png" class="footer-logo" alt="maili"/>
      </a>
      <a href="https://www.instagram.com" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/kakaoicon.png" class="footer-logo" alt="kakao"/>
      </a>
    </div>
  </footer>
</template>

<style scoped>

* {
  box-sizing: border-box;
}

@media (max-width: 1000px) {
  .decor {
    display: none !important;
  }
}

@keyframes slidePop {
  0%   { opacity:0; transform: translateY(24px) scale(0.98); }
  60%  { opacity:1; transform: translateY(-4px) scale(1.02); }
  100% { opacity:1; transform: translateY(0) scale(1); }
}

.section1 {
  position: relative;
  display: flex;
  flex-direction: column;
  place-items: center;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
  padding: 16px;
  overflow-x: clip; /* 섹션 안에서 넘침 차단 */
}

/* 중앙 카피 애니메이션 (슬라이드 튀어나옴) */
.section1-div1 > * {
  opacity: 0;
  transform: translateY(24px) scale(0.98);
  animation: slidePop .6s cubic-bezier(.2,.8,.2,1) forwards;
}
.section1-div1 img {
  animation-delay: .10s;
}
.section1-div1 h3 {
  animation-delay:  .12s;
  font-weight: 700;
}
@media (max-width: 1000px) {
  .section1-div1 h3 {
    font-size: 30px; /* 모바일에서 다른 크기 */
  }
}
.section1-div1 p {
  animation-delay: .24s;
  font-weight: 700;
  font-size: 1.25rem;
}
@media (max-width: 1000px) {
  .section1-div1 p {
    font-size: 20px; /* 모바일에서 다른 크기 */
  }
}
.section1-div1 button {
  animation-delay: .36s;
  pointer-events: auto;
  background:#000; color:#fff;
  padding:12px 20px; border-radius:999px;
  font-weight:700; border:none; cursor:pointer;
  box-shadow:0 6px 18px rgba(0,0,0,.12);
}

.section1-div2 {
  margin-top: 60px;
}

.section1-div2 > * {
  opacity: 0;
  transform: translateY(24px) scale(0.98);
  animation: slidePop .6s cubic-bezier(.2,.8,.2,1) forwards;
}

.section1-div2 p {
  color:#7a7a7a;
  font-weight: 700;
  font-size:16px;
  margin-bottom: 8px;
}
@media (max-width: 1000px) {
  .section1-div2 p {
    font-size: 15px; /* 모바일에서 다른 크기 */
  }
}
.section1-div3 {
  margin-top: 60px;
}

.section1-div3 p{
  color: #afafaf;
  font-weight: 500;
  font-size:12px;
}

.hr1 {
  border: none;
  border-top: 1px solid #ccc;   /* 두께/색상 */
  width: 70%;
  margin: 40px auto;  /* 위아래 여백 */
}

.section2 {
  position: relative;
  display: flex;
  flex-direction: column;
  place-items: center;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
}

.section2 h3 {
  font-weight: 700;
}
@media (max-width: 1000px) {
  .section2 h3 {
    font-size: 20px; /* 모바일에서 다른 크기 */
  }
}

.section2 h4 {
  font-weight: 700;
}

.section2 p {
  font-weight: bold;
  font-size: 1.1rem;
}

.section2-div1 {
  display: flex;              /* 카드들을 가로로 나란히 */
  justify-content: center;
  gap: 50px;                  /* 카드 사이 간격 */
  margin-top: 40px;
  flex-wrap: wrap;            /* 작은 화면에서는 자동 줄바꿈 */
}

.section2-card1 {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.08);
  padding: 32px 24px;
  max-width: 320px;
  flex: 1 1 280px;            /* 반응형 */
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.section2-card1:hover {
  transform: translateY(-6px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.12);
}

.section2-card1 .icon {
  width: 64px;
  height: 64px;
  margin-bottom: 16px;
}

.section3 {
  background-color: #e9ecef;
  position: relative;
  display: flex;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
}

.section4 {
  background-color: #FFFFFF;
  position: relative;
  display: flex;
  flex-direction: column;
  place-items: center;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
}

.footer {
  background: #fff;
  padding: 20px;
  text-align: center;
  border-top: 1px solid #ddd;
  font-size: 14px;
  line-height: 1.6;
  color: #000;
}

.footer .footer-top p {
  margin: 2px 0;
  font-weight: bold;
}

.footer .footer-middle {
  margin: 12px 0;
  font-size: 13px;
  color: #333;
}

.footer .footer-bottom {
  margin-top: 16px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 32px;
}

.footer .footer-logo {
  height: 30px; /* 로고 크기 조절 */
  filter: grayscale(100%);  /* 흑백 변환 */
  opacity: 0.9; /* 투명도 */
}

</style>
