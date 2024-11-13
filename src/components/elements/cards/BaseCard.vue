<script setup lang="ts">
import { FwbCard } from 'flowbite-vue'
</script>

<template>
  <fwb-card href="#">
    <slot name="header"></slot>
    <slot name="body"></slot>
    <slot name="footer"></slot>
  </fwb-card>
</template>
