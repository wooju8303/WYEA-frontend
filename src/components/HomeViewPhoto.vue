<script setup lang="ts">
defineProps<{
  hidden?: boolean   // 스크롤에 따라 토글할 플래그
}>()
</script>

<template>
  <section class="hero" >
    <section class="section1">
      <h1>활동 사진</h1>
    </section>
    <section class="section2">
      <h1>추후 추가</h1>
    </section>

  </section>
  <div >

  </div>
</template>

<style scoped>
.hero > * { position: relative; z-index: 0; }
.hero {
  position: fixed;
  justify-content: center;  /* 가로 중앙 */
  align-items: center;  /* 세로 중앙 */
  text-align: center;
  inset: var(--header-h, 64px) 0 0 0;
  z-index: -1;
  pointer-events: none;

}
.hero::before {
  --cell: 32px; /* 칸 크기 */
  --thin: rgba(255,255,255,.08);  /* 얇은 선 색 */
  --bold: rgba(255,255,255,.14);  /* 굵은 선 색 */

  content: "";
  position: absolute;
  inset: 0;
  pointer-events: none; /* 클릭 방해 X */
  z-index: -1;

  /* 1) 얇은 격자  2) 굵은 보조선(5칸마다) */
  background-image:
    linear-gradient(transparent calc(100% - 1px), var(--thin) 0), /* 가로 얇은선 */
    linear-gradient(90deg, transparent calc(100% - 1px), var(--thin) 0),  /* 세로 얇은선 */
    linear-gradient(transparent calc(100% - 1px), var(--bold) 0), /* 가로 굵은선 */
    linear-gradient(90deg, transparent calc(100% - 1px), var(--bold) 0);  /* 세로 굵은선 */

  /* 얇은 격자 간격 / 굵은 보조선 간격(5칸마다) */
  background-size:
    var(--cell) var(--cell),
    var(--cell) var(--cell),
    calc(var(--cell) * 5) calc(var(--cell) * 5),
    calc(var(--cell) * 5) calc(var(--cell) * 5);

  opacity: .9;  /* 전체 격자 투명도 */
  mix-blend-mode: overlay;  /* 배경과 자연스럽게 섞임 (필요 없으면 지워도 됨) */

}

.hero::after {
  content: "";
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 0;
  background-image: url("data:image/svg+xml;utf8,\
  <svg xmlns='http://www.w3.org/2000/svg' width='120' height='120' viewBox='0 0 120 120'>\
    <filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/><feColorMatrix type='saturate' values='0'/><feComponentTransfer><feFuncA type='table' tableValues='0 0 0 0 .06 .08 .1 .12 .14'/></feComponentTransfer></filter>\
    <rect width='120' height='120' filter='url(%23n)'/>\
  </svg>");
  opacity: .4;
  mix-blend-mode: soft-light;
}

.section1 {
  font-family: 'PretendardFont', sans-serif;
}
.section1 h1{
  margin-top: 20px;
  font-weight: 600;
  font-size: 60px;
  position: relative;
  z-index: 0; /* 격자보다 위 */
  background: #2d6a4f;
  box-shadow: 0 0 30px 15px #2d6a4f;
  padding: 0.5em 1em; /* 배경이 글자보다 약간 크게 */
  display: inline-block;  /* 텍스트 길이에 맞게 박스 */
}

@media (max-width: 1024px) {
  .section1 h1 {
    font-size: 30px;
  }
}

.section2 h1 {
  margin-top: 70px;
  font-weight: 600;
  font-size: 100px;
  position: relative;
  z-index: 0; /* 격자보다 위 */
  background: #2d6a4f;
  box-shadow: 0 0 30px 15px #2d6a4f;
  padding: 0.5em 1em; /* 배경이 글자보다 약간 크게 */
  display: inline-block;  /* 텍스트 길이에 맞게 박스 */
}
@media (max-width: 1024px) {
  .section2 h1 {
    font-size: 40px;
  }
}

</style>
