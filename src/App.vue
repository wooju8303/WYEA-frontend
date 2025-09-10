<template>
  <div class="app">
    <!-- 상단 네비게이션 (공통) 헤더 숨김-->
<!--    <header class="header" id="site-header">-->
<!--      <div class="logo">MyLogo</div>-->
<!--      <nav class="nav">-->
<!--        <RouterLink to="/">Home</RouterLink>-->
<!--        <RouterLink to="/login">Login</RouterLink>-->
<!--        <RouterLink to="/register">Register</RouterLink>-->
<!--      </nav>-->
<!--    </header>-->
    <!-- 라우터 페이지 내용 -->
    <main class="page">
      <RouterView />
    </main>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onBeforeUnmount, nextTick } from 'vue'


function setHeaderVar() {
  const h = document.getElementById('site-header')?.offsetHeight ?? 64
  document.documentElement.style.setProperty('--header-h', `${h}px`)
}

let ro: ResizeObserver | null = null

onMounted(async () => {
  await nextTick()
  setHeaderVar()

  // 헤더 높이가 변하면 즉시 CSS 변수 업데이트
  const headerEl = document.getElementById('site-header')
  if (headerEl && 'ResizeObserver' in window) {
    ro = new ResizeObserver(() => setHeaderVar())
    ro.observe(headerEl)
  }

  // 윈도 리사이즈 시에도 업데이트
  window.addEventListener('resize', setHeaderVar)
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', setHeaderVar)
  ro?.disconnect()
  ro = null
})
</script>

<style scoped>
/* 전역 기본값: 커스텀 변수 미리 정의(경고 방지) */
:global(:root){
  --header-h: 64px;
}

/* 레이아웃 기본 */
.app { min-height: 100vh; display: flex; flex-direction: column; }
:global(html), :global(body), :global(#app) { height: 100%; }
:global(body) { margin: 0; }
* { box-sizing: border-box; }

/* 헤더 */
.header {
  position: fixed; top: 0; left: 0; right: 0;
  height: 64px; padding: 0 24px;
  display: flex; align-items: center; justify-content: space-between;
  border-bottom: 1px solid #eee; background: #fff; z-index: 1000;
}
.logo { font-weight: 800; font-size: 20px; }
.nav { display: flex; gap: 20px; }
.nav a { text-decoration: none; color: #333; font-weight: 500; }

/* 페이지 컨텐츠 (헤더만큼 아래에서 시작) */
.page {
  position: relative; z-index: 1;
  padding-top: var(--header-h);
  min-height: calc(100vh - var(--header-h));
}
</style>
