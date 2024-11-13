<script setup lang="ts">
import type { PropType } from 'vue';
import BaseRadio from '../input/radio/BaseRadio.vue';

const props = defineProps<{
  bodyContent: string
}>()

</script>

<template>
  <div>
      {{ bodyContent }}
  </div>
</template>
