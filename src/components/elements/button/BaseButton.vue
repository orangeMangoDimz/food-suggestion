<script setup lang="ts">
import { FwbButton } from 'flowbite-vue'
import { computed } from 'vue';

const props = withDefaults(
  defineProps<{
    color?: 'light'
    className?: string
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

const defBtnClass = computed<string>(() => {
  return `w-full ${props.className}`
});
</script>

<template>
  <fwb-button :color="color" @click="handleClick" :class="defBtnClass">
    <slot name="content"></slot>
  </fwb-button>
</template>
