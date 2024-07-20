<script setup>
import { RouterLink, RouterView } from 'vue-router'
import Header from './components/Header.vue';
</script>

<template class="body">
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
  height: 8dvh;
  width: 100%;
  .header {
  height: 8dvh;
  width: 100%;
}
  .content{
  width: 100%;
  height: 92dvh;
  text-align: center;
  }
}

</style>
