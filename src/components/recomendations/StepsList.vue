<script setup lang="ts">
import BaseList from '../elements/list/BaseList.vue'

 defineProps<{
  steps: Array<string>
}>()
</script>

<template>
  <BaseList :items="steps" />
</template>
