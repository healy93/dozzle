<template>
  <slot></slot>
  <teleport to="body">
    <transition name="fade">
      <div
        v-show="show && (delayedShow || glopbalShow)"
        class="border-base-content/20 bg-base-100 fixed z-50 rounded-sm border p-4 shadow-sm"
        ref="content"
      >
        <slot name="content"></slot>
      </div>
    </transition>
  </teleport>
</template>

<script lang="ts" setup>
import { globalShowPopup } from "@/composable/popup";

const glopbalShow = globalShowPopup();
const show = ref(glopbalShow.value);
const delayedShow = refDebounced(show, 1000);
const content = ref<HTMLElement>();

const onMouseEnter = (e: Event) => {
  show.value = true;
  glopbalShow.value = true;

  if (content.value && e.target instanceof HTMLElement) {
    const { left, top, width } = e.target.getBoundingClientRect();
    const x = left + width + 10;
    const y = top;

    content.value.style.left = `${x}px`;
    content.value.style.top = `${y}px`;
  }
};

const onMouseLeave = () => {
  show.value = false;
  glopbalShow.value = false;
};

const el: Ref<HTMLElement> = useCurrentElement();
useEventListener(() => el.value?.nextElementSibling, "mouseenter", onMouseEnter);
useEventListener(() => el.value?.nextElementSibling, "mouseleave", onMouseLeave);
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  @apply transition-opacity;
}

.fade-enter-from,
.fade-leave-to {
  @apply opacity-0;
}
</style>
