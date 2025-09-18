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
        { id: 4, value: 'd' },
        { id: 5, value: 'e' },
        { id: 6, value: 'f' },
      ]"
    />
  </div>
</template>

<style scoped lang="less">
.container {
  width: 600px;
  height: 200px;
  border: 5px solid #fff;
}
</style>
