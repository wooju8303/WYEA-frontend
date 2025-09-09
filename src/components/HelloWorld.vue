<script setup lang="ts">
defineProps({
  msg: {
    type: String,
    required: true,
  },
})
import { computed } from 'vue'
import { useUserStore } from '@/stores/user-store'
const userStore = useUserStore()

const loginStatus = computed(() => (userStore.isLoggedIn ? '로그인 됨' : '로그인 안됨'))
</script>

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>{{ loginStatus }}</h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
