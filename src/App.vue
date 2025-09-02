<script setup lang="ts">
import Swiper from "./components/Swiper/index.vue";
</script>

<template>
  <div class="container">
    <Swiper :page-num="1" :data="[1, 2, 3, 4, 5, 6]" />
  </div>
</template>

<style scoped lang="less">
.container {
  width: calc(500px * 1);
  height: 300px;
  border: 1px solid #fff;
}
</style>
