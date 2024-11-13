<script setup lang="ts">
import { FwbCard } from 'flowbite-vue'
</script>

<template>
  <fwb-card class="w-full hover:bg-white" variant="horizontal">
    <div class="p-5 flex flex-col gap-y-5 justify-center items-start">
      <slot name="header"></slot>
      <slot name="body"></slot>
      <slot name="footer"></slot>
    </div>
  </fwb-card>
</template>
