<script setup lang="ts">
import Swiper from "../index.vue";
</script>

<template>
  <div class="container">
    <Swiper
      :data="[
        { id: 1, value: 'a' },
        { id: 2, value: 'b' },
        { id: 3, value: 'c' },
      ]"
    />
  </div>
</template>

<style scoped lang="less">
.container {
  width: 100%;
  height: 100%;
  border: 1px solid #fff;
}
</style>
