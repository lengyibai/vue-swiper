<script setup lang="ts">
import { onMounted, onUnmounted, ref, shallowReactive } from "vue";

interface Props {
  data: Array<{ id: string | number; value: string }>;
}
const $props = defineProps<Props>();

const swiperRef = ref<HTMLElement>();
const sliderContentRef = ref<HTMLElement>();
const sliderItemRefs = ref<HTMLElement[]>([]);

const activeIndex = ref(0);
const list = shallowReactive([...$props.data]);

let isDragging = false;
let startX = 0;
let currentX = 0;
let startTime = 0;
let left = 0;

const move = (event: PointerEvent) => {
  if (!isDragging) return;
  const width = swiperRef.value!.offsetWidth;

  left += event.pageX - currentX;
  currentX = event.pageX;

  if (left < -width) {
    list.push(list.shift()!);
    left += width;
    activeIndex.value = (activeIndex.value + 1) % $props.data.length;
  } else if (left > 0) {
    list.unshift(list.pop()!);
    left -= width;
    activeIndex.value = (activeIndex.value - 1 + $props.data.length) % $props.data.length;
  }

  sliderContentRef.value!.style.transform = `translateX(${left}px)`;
};

const end = (event: PointerEvent) => {
  if (!isDragging) return;
  isDragging = false;

  const width = swiperRef.value!.offsetWidth;
  const endTime = Date.now() - startTime;
  const slide = startX - event.pageX;
  const slideSpeed = Math.abs(slide) / endTime;

  if (Math.abs(slide) > sliderItemRefs.value[0].offsetWidth / 2 || slideSpeed > 0.5) {
    if (slide > 0) {
      activeIndex.value = (activeIndex.value + 1) % $props.data.length;
      left = -width;
    } else {
      activeIndex.value = (activeIndex.value - 1 + $props.data.length) % $props.data.length;
      left = 0;
    }
  } else {
    left = 0;
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
  sliderItemRefs.value.forEach((el) => {
    el.style.width = swiperRef.value!.offsetWidth + "px";
    el.style.height = swiperRef.value!.offsetHeight + "px";
  });
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
        <div v-for="item in list" :key="item.id" class="slider-item">
          {{ item.value }}
        </div>
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

      .slider-item {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-shrink: 0;
        font-size: 50px;
        background-color: var(--blue);
        user-select: none;
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
