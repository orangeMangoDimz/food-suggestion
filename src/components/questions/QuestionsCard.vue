<script setup lang="ts">
import type { Question } from '@/App.vue'
import BaseCard from '../elements/cards/BaseCard.vue'
import BaseRadio, { type Items } from '../elements/input/radio/BaseRadio.vue'
import BaseHeading from '../elements/heading/BaseHeading.vue'
import BaseParagraph from '../elements/paragraph/BaseParagraph.vue'

const props = defineProps<{
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
    <template v-slot:body>
      <section
        class="w-full flex justify-between items-center px-5 mb-3 gap-x-3"
      >
        <BaseParagraph
          class-name="font-medium mb-0"
          :content="header.question"
        />
        <BaseParagraph
          :content="header.id + '/' + totalQuesiton"
          class-name="px-2 py-1 border border-borderColor rounded-lg"
        />
      </section>
      <BaseRadio
        :items="body"
        @choose-ans="($event, choice) => emit('chooseAns', $event, choice)"
      />
    </template>
  </BaseCard>
</template>
