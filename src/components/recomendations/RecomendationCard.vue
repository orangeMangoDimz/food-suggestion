<script setup lang="ts">
import type { Answer } from '@/App.vue';
import BaseCard from '../elements/cards/BaseCard.vue';
import ToolsList from './ToolsList.vue';
import StepsList from './StepsList.vue';
import IngredientsList from './IngredientsList.vue';

 defineProps<{
  header?: string,
  body: Answer
}>()

</script>

<template>
  <BaseCard>
    <template v-slot:header>
      <h3>{{ header }}</h3>
    </template>
    <template v-slot:body>
      <p>Deskripsi</p>
      <p>{{ body.body.description }}</p>
      <p>Alat</p>
      <ToolsList :tools="body.body.tools" />
      <p>Bahan</p>
      <IngredientsList :ingredients="body.body.ingredients" />
      <p>Steps</p>
      <StepsList :steps="body.body.steps" />
    </template>
  </BaseCard>
</template>
