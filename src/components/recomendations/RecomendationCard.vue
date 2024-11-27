<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import BaseCard from '../elements/cards/BaseCard.vue'
import { marked } from 'marked'
import BaseButton from '../elements/button/BaseButton.vue'
import axios from 'axios'
import YoutubeCards from './YoutubeCards.vue'
import BaseHeading from '../elements/heading/BaseHeading.vue'
import BaseLink from '../elements/link/BaseLink.vue'

const GOOGLE_API_KEY = import.meta.env.VITE_API_YOUTUBE_KEY

interface SearchParam {
  part: 'snippet'
  maxResults: 3
  order: 'viewCount'
  key: string
  q: string
}

// TODO: handle if the description is null
export interface YoutubeVideo {
  id: {
    videoId: string
  }
  snippet: {
    channelId: string
    channelTitle: string
    thumbnails: {
      default: {
        height: number
        url: string
        width: number
      }
      medium: {
        height: number
        url: string
        width: number
      }
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
const currTitle = ref<string>('')

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
      q: 'cara membuat ' + title || '',
    }
    currTitle.value = initParams.q
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
  const url: string = `https://www.youtube.com/channel/${channelId}`
  window.open(url, '_blank')
}

const viewAllLink = (): string => {
  if (currTitle.value) {
    return `https://www.youtube.com/results?search_query=${currTitle.value}`
  }
  return 'https://www.youtube.com/'
}
</script>

<template>
  <BaseCard>
    <template v-slot:body>
      <div ref="responseContainer" class="mt-3" v-html="resMarked"></div>
      <div class="flex w-full justify-between items-center">
        <BaseHeading class-name="w-fit" tag="h4" content="Referensi Video" />
        <BaseLink :link="viewAllLink()" content="Lihat Semua" />
      </div>
      <YoutubeCards
        :video-list="videoList"
        @open-video-link="openVideoLink"
        @open-channel-url="openChannelUrl"
      />
    </template>
  </BaseCard>
  <BaseButton @handle-click="handleClick" class-name="mt-5">
    <template v-slot:content> Generate Again </template>
  </BaseButton>
</template>
