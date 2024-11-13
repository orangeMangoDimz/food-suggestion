<script setup lang="ts">
import BaseList from '../elements/list/BaseList.vue'

const props = defineProps<{
  steps: Array<string>
}>()
</script>

<template>
  <BaseList :items="steps" />
</template>
