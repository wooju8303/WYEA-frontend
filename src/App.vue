<template>
  <div class="app">
    <header id="site-header" class="glass-header" :class="{ scrolled: isScrolled }">
      <div class="inner">
        <div class="brand">
          <RouterLink to="/">
            <img src="@/assets/image/wyea-logo-home.png" alt="WYEA" height="24" />
          </RouterLink>
        </div>
        <nav class="nav">
<!--          <RouterLink to="/">Home</RouterLink>-->
<!--          <RouterLink to="/about">About</RouterLink>-->
<!--          <RouterLink to="/blog">Blog</RouterLink>-->
<!--          <RouterLink to="/guides">Guides</RouterLink>-->
        </nav>
        <div class="actions">
<!--          <RouterLink class="btn ghost" to="/login">Log in</RouterLink>-->
<!--          <RouterLink class="btn solid" to="/register">Sign up</RouterLink>-->
        </div>
      </div>
    </header>
    <main class="page" >
      <RouterView />
    </main>
  </div>
</template>


<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

const isScrolled = ref(false)
const onScroll = () => (isScrolled.value = window.scrollY > 6)

onMounted(() => {
  window.addEventListener('scroll', onScroll, { passive: true })
})
onBeforeUnmount(() => {
  window.removeEventListener('scroll', onScroll)
})
</script>

<style scoped>
:global(:root){
  --header-h: 64px;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
:global(html), :global(body), :global(#app) { height: 100%; margin: 0; }
* { box-sizing: border-box; }

/* ==== Glass Header ==== */
.glass-header {
  position: fixed;
  top: 12px;
  left: 50%;
  transform: translateX(-50%);
  width: min(1100px, calc(100% - 28px));
  z-index: 1000;

  background: linear-gradient(180deg, rgba(255,255,255,.7), rgba(255,255,255,.6));
  backdrop-filter: blur(10px) saturate(160%);
  -webkit-backdrop-filter: blur(10px) saturate(160%);

  border-radius: 999px;
  border: 1px solid rgba(0,0,0,.06);
  box-shadow: 0 10px 30px rgba(0,0,0,.12), inset 0 1px 0 rgba(255,255,255,.7);
  transition: background .25s ease, box-shadow .25s ease;
}
.glass-header.scrolled {
  background: rgba(255,255,255,.9);
  box-shadow: 0 12px 34px rgba(0,0,0,.16), inset 0 1px 0 rgba(255,255,255,.9);
}

.inner {
  height: 56px;
  padding: 0 16px;
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 12px;
}

.brand {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 800;
  color: #0d47a1;
  text-decoration: none;
}

.brand img{
  margin-top: 10px;
  width: 90px;   /* 원하는 가로 크기 */
  height: auto;   /* 세로 비율은 자동 */
}

.nav {
  display: flex;
  justify-content: center;
  gap: 22px;
}
.nav a {
  text-decoration: none;
  font-weight: 600;
  color: #1f2937;
  opacity: .85;
}
.nav a.router-link-active {
  color: #0d47a1;
  opacity: 1;
}
.nav a:hover { opacity: 1; }

.actions {
  display: flex;
  gap: 10px;
}

/* Page content */
.page {
  position: relative;
  z-index: 0;
  min-height: 120vh; /* 충분히 길게 */
  background: #2d6a4f;
}

/* 반응형 */
@media (max-width: 768px) {
  .glass-header { width: calc(100% - 16px); top: 8px; }
  .inner { height: 52px; padding: 0 10px; }
  .nav { display: none; } /* 모바일에선 햄버거로 교체하는 게 일반적 */
}
</style>
