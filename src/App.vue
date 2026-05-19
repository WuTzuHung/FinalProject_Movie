<script setup>
import { RouterLink, RouterView } from 'vue-router'
import Header from './components/Header.vue';
</script>

<template>
  <div class="total">
    <div class="header">
      <Header />
    </div>
    <div class="content">
      <RouterView />
    </div>
  </div>
</template>

<style scoped lang="scss">
.total{
  width: 100%;
  min-height: 100dvh;

  .header {
    width: 100%;
  }

  .content{
    width: 100%;
    min-height: calc(100dvh - var(--app-header-height, 72px));
    padding-top: var(--app-header-height, 72px);
    text-align: center;
  }
}

</style>
