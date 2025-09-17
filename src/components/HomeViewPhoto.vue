<script setup lang="ts">
defineProps<{
  hidden?: boolean   // ⬅️ 스크롤에 따라 토글할 플래그
}>()
</script>

<template>
  <section class="hero" >
    <section>
      <h1>활동사진</h1>
    </section>

  </section>
  <div class="hero">

  </div>
</template>

<style scoped>
.hero {
  position: fixed;
  justify-content: center; /* 가로 중앙 */
  align-items: center;     /* 세로 중앙 */
  text-align: center;
  inset: var(--header-h, 64px) 0 0 0;
  z-index: -1;
  pointer-events: none;
}

.hero h1 {
  margin-top: 70px;
  font-weight: 600;
  font-size: 60px;
}

</style>
