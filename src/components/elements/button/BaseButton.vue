<script setup lang="ts">
import { FwbButton } from 'flowbite-vue'

const props = withDefaults(
  defineProps<{
    color?: 'light'
  }>(),
  {
    color: 'light',
  },
)

const emit = defineEmits<{
  (e: 'handleClick'): void
}>()

const handleClick = () => {
  emit('handleClick')
}
</script>

<template>
  <fwb-button :color="color" @click="handleClick">
    <slot name="content"></slot>
  </fwb-button>
</template>
