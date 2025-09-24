<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, nextTick } from 'vue'
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
import masan from '@/assets/image/masan.png'

import HomeViewPhoto from '@/components/HomeViewPhoto.vue'
import SwipeCarousel from '@/components/SwipeCarousel.vue'
/**
 * 홈 메인 이미지
 */
const decorItems: DecorItem[] = [
  { src: coin, from: 'right', right: '0px', top: '30%', width: 140, rotZ:70, delay: .25 },
  { src: carrier, from: 'top-right', right: '-250px', top: '-30%', width: 700, rotZ:70, delay: .25 },
  { src: passport, from: 'right',  right: '0px', top: '60%',  width: 200, rotZ:-60, delay: .25 },
  { src: charger, from: 'right',  right: '-40px', top: '34%', width: 300, rotZ:30, delay: .30 },
  { src: compass, from: 'down',  right: '350px', top: '0%',  width: 150, rotZ:30, delay: .25 },
  { src: bill, from: 'bottom-right',  right: '-120px', top: '70%',  width: 200, rotZ:-20, delay: .25 },
  { src: map, from: 'up',  right: '-80px', top: '60%',  width: 700, rotZ:100, delay: .25 },

  { src: coin, from: 'left', left: '0px', top: '50%', width: 100, rotZ:170, delay: .30 },
  { src: backpack, from: 'top-left',  left: '-90px', top: '-5%',  width: 400, rotZ:30, delay: .25 },
  { src: camera, from: 'down',  left: '140px', top: '0%',  width: 220, rotZ:110, delay: .25 },
  { src: pens, from: 'left',  left: '100px', top: '55%',  width: 150, rotZ:0, delay: .23 },
  { src: headphones, from: 'left',  left: '0px', top: '30%',  width: 250, rotZ:-50, delay: .27 },
  { src: mobile, from: 'left',  left: '-80px', top: '55%',  width: 200, rotZ:45, delay: .25 },
  { src: wallet, from: 'bottom-left',  left: '0px', top: '80%',  width: 150, rotZ:-10, delay: .27 },
  { src: sunglasses, from: 'left',  left: '10px', top: '65%',  width: 200, rotZ:-10, delay: .23 },
  { src: sticker, from: 'up',  left: '100px', top: '80%',  width: 300, rotZ:30, delay: .25 },
]
/**
 * 협력 대학 로고
 */
const logos = [
  { src: gyeongsang },
  { src: inje },
  { src: kyunghee },
  { src: seoul },
  { src: yonsei },
  { src: kyungnam },
  { src: busan },
  { src: woosongcollege },
  { src: masan },
]

const section1Ref = ref<HTMLElement | null>(null)
const section2Ref = ref<HTMLElement | null>(null)
const decorHidden = ref(false)
const footerVisible = ref(false)

const year = new Date().getFullYear()

let section1IO: IntersectionObserver | null = null
let section2IO: IntersectionObserver | null = null
let section3IO: IntersectionObserver | null = null
let prevHidden = decorHidden.value
let initialized = false // 초기 1회 콜백 무시

const onScroll = () => {
  const scrollTop = window.scrollY
  const windowHeight = window.innerHeight
  const docHeight = document.documentElement.scrollHeight
  const scrollable = docHeight - windowHeight // 전체 스크롤 가능 높이
  const progress = scrollTop / scrollable // 스크롤 진행률 (0~1)
  footerVisible.value = progress >= 0.95  // 95% 이상 내려오면 footer 보이기
}

onMounted(async () => {
  /* ===== 배경데코 토글 IO ===== */
  const rootStyles = getComputedStyle(document.documentElement)
  const headerH = parseFloat(rootStyles.getPropertyValue('--header-h')) || 64

  const HIDE_AT = 0.8
  const SHOW_AT = 0.7

  await nextTick() // DOM 렌더 보장

  section1IO?.disconnect()
  section1IO = new IntersectionObserver(
    (entries: IntersectionObserverEntry[]) => {
      const entry = entries[0]
      if (!entry) return

      const r = entry.intersectionRatio
      if (!initialized) { initialized = true; return }

      let nextHidden = prevHidden
      if (!prevHidden && r < HIDE_AT) nextHidden = true
      else if (prevHidden && r > SHOW_AT) nextHidden = false

      if (nextHidden !== prevHidden) {
        decorHidden.value = nextHidden
        prevHidden = nextHidden
      }
    },
    {
      threshold: [0, 0.5, 0.8, 0.9, 1],
      rootMargin: `-${headerH}px 0px 0px 0px`,
    }
  )
  if (section1Ref.value) section1IO.observe(section1Ref.value)
  window.addEventListener('scroll', onScroll, { passive: true })

  /* ===== (B) 클립1회 등장 IO ===== */
  // 스태거 지연값 주입
  section2Ref.value?.querySelectorAll<HTMLElement>('.clip')
    .forEach((el, i) => el.style.setProperty('--clip-d', `${i * 120}ms`))
  // IO 등록 (1회만)
  if (section2Ref.value) {
    section2IO?.disconnect()
    section2IO = new IntersectionObserver((entries, obs) => {
      const e = entries[0]
      if (e?.isIntersecting) {
        section2Ref.value!.classList.add('clip-start')
        obs.disconnect() // 한 번만
      }
    }, {
      threshold: 0.4,
      rootMargin: '0px 0px 0px 0px' // 아래쪽 여유 주기
    })
    section2IO.observe(section2Ref.value)
  }

  /* ===== 타임라인 1회 등장 IO ===== */
  // 항목별 스태거 지연 주입
  document.querySelectorAll<HTMLElement>('.section3-div2 li')
    .forEach((el, i) => el.style.setProperty('--d', `${i * 80}ms`))
  document.querySelectorAll<HTMLElement>('.section3-div3 li')
    .forEach((el, i) => el.style.setProperty('--d', `${i * 80}ms`))

  // 섹션 진입 시 한 번만 클래스 추가
  const section3 = document.querySelector('.section3')
  if (section3) {
    section3IO?.disconnect()
    section3IO = new IntersectionObserver((entries, obs) => {
      const e = entries[0]
      if (e?.isIntersecting) {
        section3.classList.add('reveal-start')
        obs.disconnect() // 한 번만 실행
      }
    }, {
      threshold: 0.3,
      rootMargin: '0px 0px -10% 0px',
    })
    section3IO.observe(section3)
  }
})

onBeforeUnmount(() => {
  section1IO?.disconnect()
  section2IO?.disconnect()
  section3IO?.disconnect()
  section1IO = null
  section2IO = null
  section3IO = null
  window.removeEventListener('scroll', onScroll)
})
</script>

<template>
  <HomeViewPhoto/>
  <section class="section1" ref="section1Ref">
    <div class="section1-div1">
      <img src="@/assets/image/wyea-logo.png" width="300" alt="wyea-logo">
      <h3>World Youth<br> Exchange Association</h3>
      <br>
      <p>세계 청년 교류회</p>
      <a href="https://docs.google.com/forms/d/e/1FAIpQLSddVMd4CovJdhX6IbOXtXcjhXRsrSNJkXXqRtY3RuvOF-HrWA/viewform?pli=1" target="_blank" rel="noopener noreferrer">
        <button>가입하러 가기</button>
      </a>
    </div>
    <div class="section1-div2">
      <p>연합 지부</p>
      <LogoMarquee :logos="logos" :duration="60" :gap="56" :repeat="4" :logoHeight="60"
                   style="max-width: 800px; width: 100%; margin: 10px auto 0;"/>
    </div>
    <div class="section1-div3">
      <p>스크롤 해서 계속 보기 ↓</p>
    </div>
  </section>

  <FloatingDecor class="decor" :items="decorItems" :hidden="decorHidden"/>

  <section class="section2" ref="section2Ref">
    <div class="section2-div1">
      <h3>WYEA에서는 무엇을 하나요?</h3>
    </div>
    <div class="section2-div2">
      <div class="section2-card1">
        <img src="@/assets/image/clip.png" class="clip" alt="clip" style="--rot: 38deg;" />
          <h4>설립 배경</h4>
        <SwipeCarousel>
          <template #item-0>
            <p>
              21세기는 IT 기술의 눈부신 발전으로 인해,
              소통의 범위가 국가와 개인을 넘어 전 세계로 확대되었습니다.
            </p>
          </template>

          <template #item-1>
            <p>
              이제 우리는 언제 어디서든 쉽게 연락할 수 있지만,
              여전히 언어 장벽과 물리적 거리라는 한계를 실감합니다.
            </p>
          </template>

          <template #item-2>
            <p>
              이러한 글로벌 시대에 걸맞은 단체가 필요하다고 느껴,
              세계청년교류연합을 설립하게 되었습니다.
            </p>
          </template>
        </SwipeCarousel>
      </div>
      <div class="section2-card2">
        <img src="@/assets/image/clip.png" class="clip" alt="clip" style="--rot: 48deg;" />
        <h4>주요 활동</h4>
        <ul>
          <li>해외 봉사 프로젝트</li>
          <li>어학, 자격증, 취업 등 스터디와 튜터링</li>
          <li>해외 취업 컨설팅</li>
          <li>공동 프로젝트</li>
          <li>정기 총회·오픈 포럼</li>
        </ul>
      </div>
    </div>
  </section>

  <section class="section3">
    <div class="section3-div1">
      <h1>설립 역사</h1>
    </div>
    <div class="section3-divcontainer">
      <div class="section3-div2">
        <ul>
          <li>
            <span class="date">2025년 6월 25일</span>
            <span class="event">세계 청년 교류회 결성</span>
          </li>
          <li>
            <span class="date">2025년 6월 26일</span>
            <span class="event">경상국립대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 6월 30일</span>
            <span class="event">인제대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 7월 2일</span>
            <span class="event">미국 유학생 네트워크와 협력 체결</span>
          </li>
          <li>
            <span class="date">2025년 7월 3일</span>
            <span class="event">Kent Universtiy 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 7월 4일</span>
            <span class="event">경희대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 7월 5일</span>
            <span class="event">서울대학교 지부 설립</span>
          </li>
        </ul>
      </div>
      <div class="section3-div3">
        <ul>
          <li>
            <span class="date">2025년 7월 7일</span>
            <span class="event">연세대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 7월 14일</span>
            <span class="event">재일한국청년동맹과 협력 관계 체결</span>
          </li>
          <li>
            <span class="date">2025년 7월 17일</span>
            <span class="event">경남대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 7월 25일</span>
            <span class="event">부산대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 9월 2일</span>
            <span class="event">우송정보대학교 지부 설립</span>
          </li>
          <li>
            <span class="date">2025년 9월 17일</span>
            <span class="event">마산대학교 지부 설립</span>
          </li>
        </ul>
      </div>
    </div>
  </section>

  <section class="section4">
  </section>

  <footer class="footer" :class="{ show: footerVisible }">
    <div class="footer-top">
      <p>주소</p>
      <p>
        contact us: wyea@wyea.info
      </p>
    </div>
    <div class="footer-middle">
      <p>
        © {{ year }} WYEA · Icons by Freepik (flaticon.com)<br>
        대학 로고와 명칭은 각 대학의 자산이며, 식별 목적에 한해 사용됩니다.
      </p>
    </div>
    <div class="footer-bottom">
      <a href="https://www.instagram.com/wyea_official/" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/instaricon.png" class="footer-logo" alt="instagram"/>
      </a>
      <a href="mailto:wyea@wyea.info" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/mailicon.png" class="footer-logo" alt="maili"/>
      </a>
      <a href="https://open.kakao.com/o/sFkgaWQh" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/kakaoicon.png" class="footer-logo" alt="kakao"/>
      </a>
      <a href="https://x.com/wyea_official" target="_blank" rel="noopener noreferrer">
        <img src="@/assets/image/twitter.png" class="footer-logo" alt="kakao"/>
      </a>
    </div>
  </footer>

</template>

<style scoped>

body {
  background: linear-gradient(180deg, #f0f7ff 0%, #ffffff 50%, #f9fcff 100%);
}

* {
  box-sizing: border-box;
}

/* 1024px 이하 → 모바일 */
@media (max-width: 1024px) {
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
  background: linear-gradient(180deg, #f0f7ff 0%, #ffffff 50%, #f9fcff 100%);
  position: relative;
  display: flex;
  flex-direction: column; /* 아이템을 세로 방향으로 쌓음 */
  place-items: center;
  justify-content: center;  /* 가로 중앙 */
  align-items: center;  /* 세로 중앙 */
  text-align: center; /* 텍스트 가운데 정렬 */
  min-height: calc(100vh - var(--header-h, 64px));  /* 최소 높이를 화면 높이에서 헤더 높이를 뺀 값으로 설정 (화면 꽉 차게) */
  pointer-events: none; /* 버튼만 클릭 가능하게 다시 켜줘도 됨 */
  padding: 16px;  /* 안쪽 여백 (상하좌우 16px) */
  overflow-x: clip; /* 섹션 안에서 넘침 차단 */
  font-family: 'PretendardFont', sans-serif;
}

/* 중앙 카피 애니메이션 (슬라이드 튀어나옴) */
.section1-div1 > * {
  opacity: 0;
  transform: translateY(24px) scale(0.98);
  animation: slidePop .6s cubic-bezier(.2,.8,.2,1) forwards;
}

.section1-div1 img {
  animation-delay: .10s;
  user-select: none;
  -webkit-user-drag: none;
  margin-top: 50px;
}

.section1-div1 h3 {
  line-height: 1.5;
  animation-delay:  .12s;
  font-weight: 700;
}
@media (max-width: 1024px) {
  .section1-div1 h3 {
    font-size: 30px; /* 모바일에서 다른 크기 */
  }
}

.section1-div1 p {
  animation-delay: .24s;
  font-weight: 700;
  font-size: 1.25rem;
}
@media (max-width: 1024px) {
  .section1-div1 p {
    font-size: 20px; /* 모바일에서 다른 크기 */
  }
}

.section1-div1 button {
  animation-delay: .36s;
  pointer-events: auto;
  background:#000;
  color:#fff;
  padding:12px 20px;
  border-radius:999px;
  font-weight:700;
  border:none;
  cursor:pointer;
  box-shadow:0 6px 18px rgba(0,0,0,.12);
}

.section1-div2 {
  margin-top: 60px;
}
@media (max-width: 1024px) {
  .section1-div2 {
    margin-top: 80px; /* 모바일에서 다른 크기 */
  }
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
@media (max-width: 1024px) {
  .section1-div2 p {
    font-size: 15px; /* 모바일에서 다른 크기 */
  }
}

.section1-div3 {
  margin-top: 60px;
}
@media (max-width: 1024px) {
  .section1-div3 {
    margin-bottom: 10px;
  }
}

.section1-div3 p{
  color: #afafaf;
  font-weight: 500;
  font-size:12px;
}

/*-------------------------------section2---------------------------------*/
.section2 {
  background: linear-gradient(180deg, #f9fcff 0%, #ffffff 30%, #f0f7ff 100%);
  display: block;
  text-align: center;
  min-height: calc(100vh - var(--header-h, 64px));
  gap: 4rem;        /* 카드 사이 간격 더 넓게 */
  padding: 4rem;    /* 화면 테두리와 카드 사이 여백 */
  font-family: 'PretendardFont', sans-serif;
}

.section2-card1 img {
  position: absolute;
  width: 200px;
  top: -70px;
  left: -66px;
  z-index: 2;
  user-select: none;
  -webkit-user-drag: none;
}
@media (max-width: 1024px) {
  .section2-card1 img {
    display: none;
  }
}

.section2-div1 h3 {
  font-weight: 700;
  margin-bottom: 60px;
}
@media (max-width: 1024px) {
  .section2-div1 h3 {
    font-size: 28px; /* 모바일에서 다른 크기 */
  }
}

.section2-div2 {
  display: flex;
  justify-content: center; /* 가운데 정렬 */
  gap: 20px;  /* 카드 사이 간격 */
  flex-wrap: wrap;  /* 화면이 좁으면 줄바꿈 */
}

.section2-card1 {
  flex: 1 1 450px;  /* 최소 450px, 공간 있으면 늘어남 */
  max-width: 550px; /* 카드 최대 폭 */
  min-height: 400px;  /* 세로 최소 높이 */
  background: #fff;  /* 카드 배경색 */
  border-radius: 12px;  /* 모서리 둥글게 */
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);  /* 부드러운 그림자 */
  padding: 2rem;  /* 내부 여백 */
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  position: relative; /* 이미지 겹치기 시 카드 기준으로도 가능 */
  z-index: 1;
}
@media (max-width: 1024px) {
  .section2-card1 {
    min-height: 300px; /* 세로 최소 높이 */
  }
}

.section2-card1:hover {
  transform: translateY(-6px);
  box-shadow: 0 8px 20px rgba(0,0,0,0.15);
}

.section2-card1 h4 {
  text-align: center;
  font-weight: 700;
}

.section2-card1 h4::after {
  content: '';
  display: block;
  width: 40px;
  height: 3px;
  background: #0d47a1; /* 포인트 색상 */
  margin: 0.5rem auto 0;
  border-radius: 2px;
}

.section2-card1 p {
  text-align: left;
  margin-top: 25px;
  margin-left: 40px;
  margin-right: 40px;
  font-weight: 400;
  font-size: 1.1rem;
  line-height: 1.8;
}

@media (max-width: 1024px) {
  .section2-card1 p {
    margin-left: 10px;
    margin-right: 10px;
  }
}


.section2-card2 {
  flex: 1 1 450px;  /* 최소 450px, 공간 있으면 늘어남 */
  max-width: 550px; /* 카드 최대 폭 */
  min-height: 400px;  /* 세로 최소 높이 */
  background: #fff;  /* 카드 배경색 */
  border-radius: 12px;  /* 모서리 둥글게 */
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);  /* 부드러운 그림자 */
  padding: 2rem;  /* 내부 여백 */
  text-align: left; /* 가운데 정렬 (원하면 left로 변경) */
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  position: relative; /* 이미지 겹치기 시 카드 기준으로도 가능 */
  z-index: 1;
}
@media (max-width: 1024px) {
  .section2-card2 {
    min-height: 300px; /* 세로 최소 높이 */
  }
}

.section2-card2:hover {
  transform: translateY(-6px);
  box-shadow: 0 8px 20px rgba(0,0,0,0.15);
}

.section2-card2 img {
  position: absolute;
  width: 200px;
  top: -63px;
  left: -57px;
  user-select: none;
  -webkit-user-drag: none;
}
@media (max-width: 1024px) {
  .section2-card2 img {
    display: none;
  }
}

.section2-card2 h4 {
  text-align: center;
  font-weight: 700;
}

.section2-card2 h4::after {
  content: '';
  display: block;
  width: 40px;
  height: 3px;
  background: #0d47a1; /* 포인트 색상 */
  margin: 0.5rem auto 0;
  border-radius: 2px;
}

.section2-card2 ul {
  padding-left: 1.2rem;
  line-height: 1.8;
}

.section2-card2 li {
  list-style: "✔ ";  /* 체크마크 스타일 */
  color: #333;
  margin-top: 15px;
  font-weight: 400;
  font-size: 1.1rem;
}

/* 애니메이션 초기 상태: 위로 살짝 숨김 + 투명 */
.section2 .clip {
  --clip-d: 0ms;
  opacity: 0;
  transform: rotate(var(--rot, 0deg)) translateY(-24px);
  transition:
    transform .7s cubic-bezier(.2,.8,.2,1),
    opacity   .7s cubic-bezier(.2,.8,.2,1);
  transition-delay: var(--clip-d, 0ms);
  will-change: transform, opacity;
}

/* 섹션2가 보이면 1회만 아래로 '툭' 내려오며 나타남 */
.section2.clip-start .clip {
  opacity: 1;
  transform: rotate(var(--rot, 0deg)) translateY(0);
}


@media (max-width: 1024px) {
  .section2-div2 h4 {
    font-size: 28px; /* 모바일에서 다른 크기 */
  }
}

@media (max-width: 1024px) {
  .section2-div2 p {
    font-size: 14px; /* 모바일에서 다른 크기 */
  }
}

@media (max-width: 1024px) {
  .section2-div2 li {
    font-size: 14px; /* 모바일에서 다른 크기 */
  }
}

/*-------------------------------section3---------------------------------*/
:root {
  --d: 0ms;
}

.section3 {
  background: linear-gradient(180deg, #f9fcff 0%, #ffffff 30%, #f0f7ff 100%);
  padding: 60px 20px;
  text-align: center;
  border-bottom-left-radius: 20px;   /* 왼쪽 아래만 둥글게 */
  border-bottom-right-radius: 20px;  /* 오른쪽 아래만 둥글게 */
  box-shadow: 0 18px 32px rgba(0,0,0,.18);
  font-family: 'PretendardFont', sans-serif;
}

.section3-div1 h1{
  font-size: 4rem;
  font-weight: 700;
  margin-bottom: 60px;
  color: #0d47a1;
}
@media (max-width: 1024px) {
  .section3-div1 h1 {
    font-size: 30px; /* 모바일에서 다른 크기 */
  }
}

.section3-divcontainer {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  max-width: 1200px;
  margin: 0 auto;
  text-align: left;
}
@media (max-width: 1024px) {
  .section3-divcontainer {
    grid-template-columns: 1fr;
  }
}

.section3-div2 {
  display: flex;
  align-items: center; /* 세로 중앙 */
  padding-right: clamp(16px, 6vw, 200px); /* 오른쪽 여백 */
}

.section3-div2 ul li {
  opacity: 0;
  transform: translateY(22px);
  filter: blur(2px);
  transition:
    opacity .6s ease,
    transform .6s ease,
    filter .6s ease;
  transition-delay: var(--d, 0ms);
  margin-bottom: 30px;
  padding-left: 50px;
  position: relative;
}

.section3-div2 h1 {
  font-size: 4rem;
  font-weight: 700;
  margin-bottom: 60px;
  color: #0d47a1;
}

.section3-div3 {
  display: flex;
  align-items: center;  /* 세로 중앙 */
}
@media (max-width: 1024px) {
  .section3-div3 {
    padding-right: clamp(16px, 3vw, 200px); /* 오른쪽 여백 */
  }
}

.section3-div3 ul li {
  opacity: 0;
  transform: translateY(22px);
  filter: blur(2px);
  transition:
    opacity .6s ease,
    transform .6s ease,
    filter .6s ease;
  transition-delay: calc(var(--d, 0ms) + 800ms);  /* 늦게 작동 */
  margin-bottom: 30px;
  padding-left: 50px;
  position: relative;
}

.section3 ul {
  list-style: none;
  margin: 0 auto;
  padding: 0;
  max-width: 600px;
  text-align: left;
  position: relative;
}

.section3 ul::before {
  content: '';
  position: absolute;
  left: 20px;
  top: 0;
  bottom: 0;
  width: 2px;
  background: #d0e2f7;
}

/* 트리거 후: 자연스럽게 나타남 */
.section3.reveal-start li {
  opacity: 1;
  transform: translateY(0);
  filter: blur(0);
}

.section3 ul li::before {
  content: '';
  position: absolute;
  left: 12px;
  top: 5px;
  width: 16px;
  height: 16px;
  background: #0d47a1;
  border-radius: 50%;
}

.section3-div2 ul li:nth-child(1)::before { background: #0d47a1; }
.section3-div2 ul li:nth-child(2)::before { background: #1c59af; }
.section3-div2 ul li:nth-child(3)::before { background: #2a6cbd; }
.section3-div2 ul li:nth-child(4)::before { background: #387ecc; }
.section3-div2 ul li:nth-child(5)::before { background: #4790da; }
.section3-div2 ul li:nth-child(6)::before { background: #56a3e8; }
.section3-div2 ul li:nth-child(7)::before { background: #64b5f6; }

.section3-div3 ul li:nth-child(1)::before { background: #64b5f6; }
.section3-div3 ul li:nth-child(2)::before { background: #42a5f5; }
.section3-div3 ul li:nth-child(3)::before { background: #2196f3; }
.section3-div3 ul li:nth-child(4)::before { background: #1e88e5; }
.section3-div3 ul li:nth-child(5)::before { background: #1976d2; }
.section3-div3 ul li:nth-child(6)::before { background: #0d47a1; }


.section3 ul .date {
  display: block;
  font-weight: 600;
  color: #0d47a1;
  margin-bottom: 4px;
}

.section3 ul .event {
  color: #333;
  line-height: 1.4;
}

.section3 ul span {
  text-align:left;
  font-size: 17px;
  font-weight: 500;
}
@media (max-width: 1024px) {
  .section3 ul span {
    font-size: 15px;
    font-weight: 500;
  }
}

/*-------------------------------section4---------------------------------*/
.section4 {
  position: relative;
  min-height: calc(100vh - var(--header-h, 64px));
  pointer-events: none;
}
/*-------------------------------footer---------------------------------*/
.footer {
  background: #fff;
  padding: 20px;
  text-align: center;
  border-top: 1px solid #ddd;
  font-size: 14px;
  line-height: 1.6;

  position: fixed;
  bottom: -200px; /* footer 높이보다 더 아래 */
  left: 0;
  width: 100%;
  transition: bottom 0.4s ease; /* 애니메이션 */
  border-top-left-radius: 16px;
  border-top-right-radius: 16px;
  clip-path: inset(0 37% 0 37% round 64px 64px 0 0);
}
@media (max-width: 1500px) {
  .footer {
    clip-path: inset(0 0px 0 0px round 0px 0px 0 0);
  }
}

.footer.show {
  bottom: 0;  /* show 클래스가 붙으면 나타남 */
}

.footer .footer-top p {
  color: #000;
  margin: 2px 0;
  font-weight: bold;
}

.footer .footer-middle {
  margin: 12px 0;
  font-size: 13px;
  color: #666;
  line-height:1.6;
}

.footer .footer-bottom {
  margin-top: 16px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 32px;
  user-select: none;
  -webkit-user-drag: none;
}

.footer .footer-logo {
  height: 30px; /* 로고 크기 조절 */
  filter: grayscale(100%);  /* 흑백 변환 */
  opacity: 0.9; /* 투명도 */
}

</style>
