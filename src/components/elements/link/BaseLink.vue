<script setup lang="ts">
import { FwbA } from 'flowbite-vue'
const props = defineProps<{
  link: string
  content: string
}>()
</script>

<template>
  <fwb-a :href="link" target="_blank"> {{ content }} </fwb-a>
</template>
