<script setup lang="ts">
import { computed } from 'vue'
import BaseCard from '../elements/cards/BaseCard.vue'
import { marked } from 'marked'
import BaseButton from '../elements/button/BaseButton.vue'

const props = defineProps<{
  body: string
}>()

const emit = defineEmits<{
  (e: 'handleClick'): void
}>()

const resMarked = computed(() => marked.parse(props.body))
const handleClick = () => {
  emit('handleClick')
}
</script>

<template>
  <BaseCard>
    <template v-slot:body>
      <div v-html="resMarked"></div>
      <BaseButton @handle-click="handleClick">
        <template v-slot:content> Generate Again </template>
      </BaseButton>
    </template>
  </BaseCard>
</template>
