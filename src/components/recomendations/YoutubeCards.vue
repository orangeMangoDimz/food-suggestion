<script setup lang="ts">
import BaseCard from '../elements/cards/BaseCard.vue'
import BaseParagraph from '../elements/paragraph/BaseParagraph.vue'
import type { YoutubeVideo } from './RecomendationCard.vue'

const props = defineProps<{
  videoList: Array<YoutubeVideo>
}>()

const emit = defineEmits<{
  (e: 'openVideoLink', link: string): void
  (e: 'openChannelUrl', channel: string): void
}>()
</script>

<template>
  <div class="youtubeWrapper flex gap-x-5 justify-start items-start">
    <BaseCard
      v-for="(item, index) in videoList"
      :key="index"
      v-if="videoList.length !== 0"
      class-name="w-6/12 spect-square overflow-y-hidden"
    >
      <template v-slot:header>
        <div class="flex flex-col gap-y-5">
          <img :src="item.snippet.thumbnails.medium.url" alt="thumbnail" />
        </div>
      </template>
      <template v-slot:body>
        <div
          class="flex flex-col"
          @click="emit('openVideoLink', item.id.videoId)"
        >
          <BaseParagraph
            class-name="font-semibold"
            :content="item.snippet.channelTitle"
            @handle-click="emit('openChannelUrl', item.snippet.channelId)"
          />
          <BaseParagraph class-name="font-bold" :content="item.snippet.title" />
          <BaseParagraph
            :content="
              item.snippet.description.length === 0
                ? 'Tidak ada deskripsi'
                : item.snippet.description
            "
          />
        </div>
      </template>
    </BaseCard>
  </div>
</template>

<style scoped>
.youtubeWrapper {
  overflow-x: scroll;
  scroll-snap-type: x mandatory;
  scrollbar-width: none;
}

.youtubeWrapper::-webkit-scrollbar {
  display: none;
}

.card-wrapper {
  height: 500px;
}
</style>
