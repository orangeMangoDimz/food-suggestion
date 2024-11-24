<script setup lang="ts">
import { FwbCard } from 'flowbite-vue'
const emit = defineEmits<{
  (e: 'handleClick'): void
}>()

const handleClick = (): void => {
  emit('handleClick')
}
</script>

<template>
  <fwb-card
    class="w-full hover:bg-white bg-white"
    variant="horizontal"
    @click="handleClick"
  >
    <div class="p-5 flex flex-col gap-y-5 justify-center items-start">
      <slot name="header"></slot>
      <slot name="body"></slot>
      <slot name="footer"></slot>
    </div>
  </fwb-card>
</template>
