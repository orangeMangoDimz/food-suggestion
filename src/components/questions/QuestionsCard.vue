<script setup lang="ts">
import BaseCard from '../elements/cards/BaseCard.vue';
import BaseRadio, { type Items } from '../elements/input/radio/BaseRadio.vue';

const props = defineProps<{
  header: string,
  body: Items[]
}>()

const emit = defineEmits<{
  (e: 'chooseAns', el: MouseEvent, choice: Items): void
}>()

</script>

<template>
  <BaseCard>
    <template v-slot:header>
      <h1>{{ header }}</h1>
    </template>
    <template v-slot:body>
      <BaseRadio :items="body" @choose-ans="($event, choice) => emit('chooseAns', $event, choice)"/>
    </template>
  </BaseCard>
</template>
