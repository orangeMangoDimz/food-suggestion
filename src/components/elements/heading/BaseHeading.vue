<script setup lang="ts">
import { FwbHeading } from 'flowbite-vue'
const props = defineProps<{
  tag: 'h1' | 'h2' | 'h3' | 'h4' | 'h5' | 'h6'
  className?: string
  content: string
}>()

const initClass = `${props.className}`
</script>

<template>
  <fwb-heading :class="initClass" :tag="tag">{{ content }}</fwb-heading>
</template>
