<script setup lang="ts">
import Swiper from "../index.vue";
</script>

<template>
  <div class="container">
    <Swiper>
      <div class="slider-item">A</div>
      <div class="slider-item">B</div>
      <div class="slider-item">C</div>
    </Swiper>
  </div>
</template>

<style scoped lang="less">
.container {
  width: calc(700px * 1);
  height: 400px;
  border: 1px solid #fff;

  .slider-item {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-shrink: 0;
    font-size: 50px;
    background-color: var(--blue);
    user-select: none;

    &:nth-child(1) {
      background-color: var(--red);
    }

    &:nth-child(2) {
      background-color: var(--yellow);
    }

    &:nth-child(3) {
      background-color: var(--green);
    }

    &:nth-child(4) {
      background-color: var(--orange);
    }

    &:nth-child(5) {
      background-color: var(--purple);
    }
  }
}
</style>
