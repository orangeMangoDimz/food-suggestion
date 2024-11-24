<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import BaseCard from '../elements/cards/BaseCard.vue'
import { marked } from 'marked'
import BaseButton from '../elements/button/BaseButton.vue'
import axios from 'axios'

const GOOGLE_API_KEY = import.meta.env.VITE_API_YOUTUBE_KEY

interface SearchParam {
  part: 'snippet'
  maxResults: 3
  order: 'viewCount'
  key: string
  q: string
}

interface YoutubeVideo {
  id: {
    videoId: string
  }
  snippet: {
    channelId: string
    channelTitle: string
    thumbnails: {
      high: {
        height: number
        url: string
        width: number
      }
    }
    description: string
    title: string
  }
  title: string
  description: string
  thumbnail: string
  link: string
}

interface YoutubeResponse {
  items: Array<YoutubeVideo>
}

const responseContainer = ref<HTMLElement | null>(null)
const videoList = ref<Array<YoutubeVideo>>([] as YoutubeVideo[])

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

const searchVideoYoutube = async (param: SearchParam): Promise<void> => {
  const baseSearchUrl: string = 'https://www.googleapis.com/youtube/v3/search'
  const constructParams: string = `?part=${param.part}&maxResults${param.maxResults}&order=${param.order}&key=${param.key}&q=${param.q} `
  const finalUrl: string = baseSearchUrl + constructParams
  try {
    const response = await axios.get<YoutubeResponse>(finalUrl)
    console.log('response', response)
    response.data.items.map(item => {
      console.log('item', item)
      videoList.value.push(item)
    })
  } catch (error) {
    throw error
  }
}

onMounted(() => {
  if (responseContainer.value) {
    const title: string | null | undefined =
      responseContainer.value?.querySelector('h4')?.textContent
    const initParams: SearchParam = {
      part: 'snippet',
      maxResults: 3,
      order: 'viewCount',
      key: GOOGLE_API_KEY,
      q: title || '',
    }
    try {
      console.log('title', title)
      searchVideoYoutube(initParams)
    } catch (error) {
      console.error('error', error)
    }
  }
})

const openVideoLink = (link: string): void => {
  const baseWatchUrl: string = 'https://www.youtube.com/watch'
  const baseParam: string = `?v=${link}`
  const finalUrl: string = baseWatchUrl + baseParam
  window.open(finalUrl, '_blank')
}

const openChannelUrl = (channelId: string): void => {
  const baseChannelUrl: string = 'https://www.youtube.com/channel'
  const baseParam: string = `?channelId=${channelId}`
  const finalUrl: string = baseChannelUrl + baseParam
  window.open(finalUrl, '_blank')
}
</script>

<template>
  <div class="flex flex-col gap-y-5">
    <BaseCard>
      <template v-slot:body>
        <div ref="responseContainer" v-html="resMarked"></div>
        <BaseCard
          v-for="(item, index) in videoList"
          :key="index"
          v-if="videoList.length !== 0"
        >
          <template v-slot:header>
            <div class="flex flex-col gap-y-5">
              <img :src="item.snippet.thumbnails.high.url" alt="thumbnail" />
              <p @click="openChannelUrl(item.snippet.channelId)">
                {{ item.snippet.channelTitle }}
              </p>
            </div>
          </template>
          <template v-slot:body @click="openVideoLink(item.id.videoId)">
            <div class="flex flex-col">
              <h4>{{ item.snippet.title }}</h4>
              <p>{{ item.snippet.description }}</p>
            </div>
          </template>
        </BaseCard>
      </template>
    </BaseCard>
    <BaseButton @handle-click="handleClick">
      <template v-slot:content> Generate Again </template>
    </BaseButton>
  </div>
</template>
