<script setup lang="ts">
import { FwbP } from 'flowbite-vue'
const props = defineProps<{
  content: string
  className?: string
}>()

const emit = defineEmits<{
  (e: 'handleClick'): void
}>()

const initClass = `${props.className}`
</script>

<template>
  <fwb-p @click="emit('handleClick')" :class="initClass">
    {{ content }}
  </fwb-p>
</template>
