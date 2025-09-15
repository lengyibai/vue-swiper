<script setup lang="ts">
import { onMounted, onUnmounted, ref, shallowReactive } from "vue";

interface Props {
  data: Array<{ id: string | number; value: string }>;
}
const $props = defineProps<Props>();

const swiperRef = ref<HTMLElement>();
const sliderContentRef = ref<HTMLElement>();
const sliderItemRefs = ref<HTMLElement[]>([]);

const activeIndex = ref(1);
const list = shallowReactive([...$props.data]);

let isDragging = false;
let startX = 0;
let currentX = 0;
let startTime = 0;
let left = 0;

const move = (event: PointerEvent) => {
  if (!isDragging) return;
  const width = sliderItemRefs.value[0].offsetWidth;

  left += event.pageX - currentX;
  currentX = event.pageX;

  if (left < -width) {
    list.push(list.shift()!);
    left = 0;
    activeIndex.value = 1;
  } else if (left > 0) {
    list.unshift(list.pop()!);
    left = -width;
    activeIndex.value = 2;
  }

  sliderContentRef.value!.style.transform = `translateX(${left}px)`;
};

const end = (event: PointerEvent) => {
  if (!isDragging) return;
  isDragging = false;

  const width = sliderItemRefs.value[0].offsetWidth;
  const endTime = Date.now() - startTime;
  const slide = startX - event.pageX;
  const slideSpeed = Math.abs(slide) / endTime;

  if (Math.abs(slide) > sliderItemRefs.value[0].offsetWidth / 2 || slideSpeed > 0.5) {
    if (slide > 0) {
      left = -width;
      activeIndex.value = 2;
    } else {
      left = 0;
      activeIndex.value = 1;
    }
  } else {
    left = 0;
    activeIndex.value = 1;
  }

  sliderContentRef.value!.style.transition = "all 0.25s ease-out";
  sliderContentRef.value!.style.transform = `translateX(${left}px)`;

  window.removeEventListener("pointermove", move);
  window.removeEventListener("pointerup", end);
};

const start = (event: PointerEvent) => {
  isDragging = true;
  startX = currentX = event.pageX;
  startTime = Date.now();
  sliderContentRef.value!.style.transition = "none";

  window.addEventListener("pointermove", move);
  window.addEventListener("pointerup", end);
};

onMounted(() => {
  sliderItemRefs.value = Array.from(sliderContentRef.value!.children) as HTMLElement[];
});

onUnmounted(() => {
  window.removeEventListener("pointermove", move);
  window.removeEventListener("pointerup", end);
});
</script>

<template>
  <div ref="swiperRef" class="swiper" @pointerdown="start">
    <div class="swiper-container">
      <div ref="sliderContentRef" class="slider-content">
        <div
          v-for="(item, index) in list"
          :key="item.id"
          :style="{
            color: index === activeIndex ? 'red' : 'white',
          }"
          class="slider-item"
        >
          {{ item.value }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="less">
.swiper {
  position: relative;
  width: 100%;
  height: 100%;

  .swiper-container {
    position: relative;
    overflow: hidden;
    width: 100%;
    height: 100%;

    .slider-content {
      position: relative;
      display: flex;
      height: 100%;

      .slider-item {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-shrink: 0;
        width: 200px;
        height: 200px;
        outline: 1px solid #000;
        font-size: 50px;
        background-color: var(--blue);
        user-select: none;
      }
    }
  }
}
</style>
