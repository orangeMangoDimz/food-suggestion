<script setup lang="ts">
import { ref } from 'vue'
import { FwbListGroup, FwbListGroupItem, FwbRadio } from 'flowbite-vue'

const picked = ref<string>('')

export interface Items {
  id: number
  label: string
  name: string
  value: string
}

 defineProps<{
  items: Items[]
}>()

const emit = defineEmits<{
  (e: 'chooseAns', el: MouseEvent, choice: Items): void
}>()

</script>

<template>
  <fwb-list-group class="w-full">
    <fwb-list-group-item v-for="(item, index) in items" :key="index">
      <fwb-radio v-model="picked" :label="item.label" :name="item.name" :value="item.value" @update:model-value="($event) => emit('chooseAns', $event, item)"/>
    </fwb-list-group-item>
  </fwb-list-group>
</template>
