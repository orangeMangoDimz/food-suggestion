<script setup lang="ts">
import BaseList from '../elements/list/BaseList.vue';

const props = defineProps<{
  ingredients: Array<string>
}>()
</script>

<template>
  <BaseList :items="ingredients" />
</template>
