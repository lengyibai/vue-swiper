<script setup lang="ts">
import { libJsRandomColor } from "lyb-js/Random/LibJsRandomColor.js";
import { onMounted, ref } from "vue";

interface Props {
  data: number[];
  /** 一页个数 */
  pageNum: number;
}
const $props = withDefaults(defineProps<Props>(), {
  pageNum: 5,
});

const swiperRef = ref<HTMLElement>();
const sliderContentRef = ref<HTMLElement>();
const sliderItemRefs = ref<HTMLElement[]>([]);

const activeIndex = ref(0);

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

/**
 * 开始拖拽
 * @param event PointerEvent
 */
const start = (event: PointerEvent) => {
  isDragging = true;
  startX = currentX = event.pageX;
  startTime = new Date().getTime();
  sliderContentRef.value!.style.transition = "none";
};

/**
 * 拖拽移动
 * @param event PointerEvent
 */
const move = (event: PointerEvent) => {
  if (!isDragging) return;
  left += event.pageX - currentX;
  currentX = event.pageX;
  sliderContentRef.value!.style.transform = `translateX(${left}px)`;
};

/**
 * 结束拖拽
 * @param event PointerEvent
 */
const end = (event: PointerEvent) => {
  if (!isDragging) return;
  isDragging = false;
  const endTime = new Date().getTime() - startTime;
  const slide = startX - event.pageX;
  const slideSpeed = Math.abs(slide) / endTime;
  if (Math.abs(slide) > sliderItemRefs.value[0].offsetWidth / 2 || slideSpeed > 1) {
    slide > 0 ? activeIndex.value++ : activeIndex.value--;
  }
  activeIndex.value = Math.max(0, Math.min(activeIndex.value, $props.data.length - 1));
  left = -sliderItemRefs.value[0].offsetWidth * activeIndex.value;
  sliderContentRef.value!.style.transition = `all 0.25s`;
  sliderContentRef.value!.style.transform = `translateX(${left}px)`;
};

onMounted(() => {
  sliderItemRefs.value.forEach((el) => {
    el.style.width = swiperRef.value!.offsetWidth / $props.pageNum + "px";
    el.style.height = swiperRef.value!.offsetHeight + "px";
  });
});
</script>

<template>
  <div ref="swiperRef" class="swiper" @pointerdown="start" @pointermove="move" @pointerup="end">
    <div class="swiper-container">
      <div ref="sliderContentRef" class="slider-content">
        <div v-for="(el, index) in data" ref="sliderItemRefs" :key="index" class="slider-item">
          {{ el }}
        </div>
      </div>
    </div>

    <div class="pagination">
      <span
        v-for="(_, index) in $props.data.length"
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
  }

  /* pagination */
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
