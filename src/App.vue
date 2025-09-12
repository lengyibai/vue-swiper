<script setup lang="ts">
import Index from "./components/单页循环/demo/index.vue";
</script>

<template>
  <div class="app">
    <div class="container">
      <index />
    </div>
  </div>
</template>

<style scoped lang="less">
.app {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;

  .container {
    width: 50%;
    height: 50%;
  }
}
</style>
