<script setup lang="ts">
import { FwbListGroup, FwbListGroupItem } from 'flowbite-vue'

 defineProps<{
  items: Array<string>
}>()
</script>

<template>
  <fwb-list-group class="w-full">
    <fwb-list-group-item v-for="(item, index) in items" :key="index" class="w-full">
      {{ item }}
    </fwb-list-group-item>
  </fwb-list-group>
</template>
