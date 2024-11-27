<script setup lang="ts">
import { FwbCard } from 'flowbite-vue'
const props = defineProps<{
  className?: string
  style?: any
}>()

const emit = defineEmits<{
  (e: 'handleClick'): void
}>()

const handleClick = (): void => {
  emit('handleClick')
}

const initClass = `hover:bg-white bg-white border-solid border-2 border-sky-500  ${props.className}`
</script>

<template>
  <div :class="['card-wrapper', initClass]">
    <div @click="handleClick">
      <div class="card-header">
        <slot name="header"></slot>
      </div>
      <div class="card-body px-5">
        <slot name="body"></slot>
      </div>
      <div class="card-footer">
        <slot name="footer"></slot>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card-wrapper {
  min-width: 350px;
  max-width: 650px;
  scroll-snap-align: start;
  flex-shrink: 0;
}
</style>
