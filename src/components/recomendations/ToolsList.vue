<script setup lang="ts">
import BaseList from '../elements/list/BaseList.vue'

const props = defineProps<{
  tools: Array<string>
}>()
</script>

<template>
  <BaseList :items="tools" />
</template>
