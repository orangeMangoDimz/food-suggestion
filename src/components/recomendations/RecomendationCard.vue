<script setup lang="ts">
import { computed } from 'vue'
import BaseCard from '../elements/cards/BaseCard.vue'
import { markdown } from 'markdown'

const props = defineProps<{
  body: string
}>()

const resMarked = computed(() => markdown.toHTML(props.body))
</script>

<template>
  <BaseCard>
    <template v-slot:body>
      <div v-html="resMarked"></div>
    </template>
  </BaseCard>
</template>
