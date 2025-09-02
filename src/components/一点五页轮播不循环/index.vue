<script setup lang="ts">
import { onMounted, ref } from "vue";

const swiperRef = ref<HTMLElement>();
const sliderContentRef = ref<HTMLElement>();
const sliderItemRefs = ref<HTMLElement[]>([]);

const GAP = 32;

/** 是否正在拖拽 */
let isDragging = false;
/** 起始触摸点 X */
let startX = 0;
/** 当前触摸点 X */
let currentX = 0;
/** 拖拽开始时间戳 */
let startTime = 0;
/** 内容偏移量 */
let left = 0;

const activeIndex = ref(0);

/**
 * 开始拖拽
 */
const start = (event: PointerEvent) => {
  (event.target as HTMLElement).setPointerCapture?.((event as any).pointerId);
  isDragging = true;
  startX = currentX = event.pageX;
  startTime = new Date().getTime();
  sliderContentRef.value!.style.transition = "none";
};

/**
 * 拖拽移动
 */
const move = (event: PointerEvent) => {
  if (!isDragging) return;
  left += event.pageX - currentX;
  currentX = event.pageX;
  sliderContentRef.value!.style.transform = `translateX(${left}px)`;
};

/**
 * 结束拖拽
 */
const end = (event: PointerEvent) => {
  if (!isDragging) return;
  isDragging = false;
  const endTime = new Date().getTime() - startTime;
  const slide = startX - event.pageX;
  const slideSpeed = Math.abs(slide) / endTime;
  const offsetWidth = sliderItemRefs.value[0].offsetWidth + GAP;

  if (Math.abs(slide) > offsetWidth / 2 || slideSpeed > 0.5) {
    slide > 0 ? activeIndex.value++ : activeIndex.value--;
  }

  activeIndex.value = Math.max(0, Math.min(activeIndex.value, sliderItemRefs.value.length - 1));

  left = -offsetWidth * activeIndex.value;

  if (activeIndex.value === sliderItemRefs.value.length - 1) {
    left += swiperRef.value!.offsetWidth - offsetWidth + GAP;
  }

  sliderContentRef.value!.style.transition = `all 0.25s ease-out`;
  sliderContentRef.value!.style.transform = `translateX(${left}px)`;
};

onMounted(() => {
  sliderItemRefs.value = Array.from(sliderContentRef.value!.children) as HTMLElement[];
});
</script>

<template>
  <div
    ref="swiperRef"
    class="swiper"
    @pointerdown="start"
    @pointercancel="end"
    @pointerleave="end"
    @pointermove="move"
    @pointerup="end"
  >
    <div class="swiper-container">
      <div ref="sliderContentRef" class="slider-content">
        <slot />
      </div>
    </div>

    <div class="pagination">
      <span
        v-for="(_, index) in sliderItemRefs.length"
        :key="index"
        :class="{ active: activeIndex === index }"
      ></span>
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
    }
  }

  .pagination {
    position: absolute;
    left: 50%;
    bottom: 0.5rem;
    display: flex;
    transform: translateX(-50%);
    gap: 0.5rem;

    span {
      display: inline-block;
      width: 0.5rem;
      height: 0.5rem;
      border-radius: 50%;
      background-color: var(--white-25);
      cursor: pointer;
      transition: 0.5s;

      &.active {
        background-color: #fff;
        transform: scale(1.25);
      }
    }
  }
}
</style>
