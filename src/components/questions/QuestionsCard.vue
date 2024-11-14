<script setup lang="ts">
import type { Question } from '@/App.vue'
import BaseCard from '../elements/cards/BaseCard.vue'
import BaseRadio, { type Items } from '../elements/input/radio/BaseRadio.vue'

defineProps<{
  header: Question
  totalQuesiton: number
  body: Items[]
}>()

const emit = defineEmits<{
  (e: 'chooseAns', el: MouseEvent, choice: Items): void
}>()
</script>

<template>
  <BaseCard>
    <template v-slot:header>
      <section class="w-full flex justify-between items-center">
        <h5>{{ header.question }}</h5>
        <p>{{ header.id }}/{{ totalQuesiton }}</p>
      </section>
    </template>
    <template v-slot:body>
      <BaseRadio :items="body" @choose-ans="($event, choice) => emit('chooseAns', $event, choice)
        " />
    </template>
  </BaseCard>
</template>
