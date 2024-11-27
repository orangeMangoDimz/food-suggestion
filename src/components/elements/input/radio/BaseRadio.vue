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

const props = defineProps<{
  items: Items[]
  className?: string
}>()

const emit = defineEmits<{
  (e: 'chooseAns', el: MouseEvent, choice: Items): void
}>()

const initClass = `w-full m-0 px-0 ${props.className}`
</script>

<template>
  <fwb-list-group :class="initClass">
    <fwb-list-group-item
      v-for="(item, index) in items"
      :key="index"
      class="m-0 px-0 border-none"
    >
      <fwb-radio
        v-model="picked"
        :label="item.label"
        :name="item.name"
        :value="item.value"
        class="m-0 px-0"
        @update:model-value="$event => emit('chooseAns', $event, item)"
      />
    </fwb-list-group-item>
  </fwb-list-group>
</template>
