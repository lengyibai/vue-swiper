<script setup lang="ts">
import Swiper from "../index.vue";
</script>

<template>
  <div class="container">
    <Swiper>
      <div class="slider-item">0</div>
      <div class="slider-item">1</div>
      <div class="slider-item">2</div>
      <div class="slider-item">3</div>
      <div class="slider-item">4</div>
    </Swiper>
  </div>
</template>

<style scoped lang="less">
.container {
  width: 700px;
  height: 400px;
  border: 1px solid #fff;

  .slider-item {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-shrink: 0;
    width: 500px;
    margin-right: 32px;
    outline: 1px solid #000;
    color: #000;
    font-size: 50px;
    background-color: #fff;
    user-select: none;

    &:first-child {
      margin-left: 32px;
    }
  }
}
</style>
