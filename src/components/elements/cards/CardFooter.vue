<script setup lang="ts">

const props = defineProps<{
  footerContent: {
    type: string
    required: false
  }
}>()

</script>

<template>
  <div>
      {{ footerContent }}
  </div>
</template>
