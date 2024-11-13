<script setup lang="ts">

const props = defineProps<{
  headerContent: {
    type: string
    required: false
  }
}>()

</script>

<template>
  <div>
    {{ headerContent }}
  </div>
</template>
