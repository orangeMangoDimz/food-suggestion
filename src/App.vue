<script setup lang="ts">
import { GoogleGenerativeAI } from '@google/generative-ai'
import { computed, ref, watch } from 'vue'
const GOOGLE_API_KEY = import.meta.env.VITE_API_GOOGLE_API_KEY

//import questions from '@/assets/data/questions.json'

// TODO: Fetch data from API
const questions = [
  {
    id: 1,
    question: 'Bagaimana perasaanmu hari ini?',
    choices: [
      { id: 1, value: 'Senang dan Berenergi' },
      { id: 2, value: 'Sedih dan Lelah' },
      { id: 3, value: 'Marah dan Frustrasi' },
      { id: 4, value: 'Tenang dan Rileks' },
    ],
  },
  {
    id: 2,
    question: 'Makanan seperti apa yang sedang kamu inginkan?',
    choices: [
      { id: 1, value: 'Sesuatu yang Asin dan Renyah' },
      { id: 2, value: 'Sesuatu yang Manis dan Menggoda' },
      { id: 3, value: 'Sesuatu yang Hangat dan Nyaman' },
      { id: 4, value: 'Sesuatu yang Ringan dan Segar' },
    ],
  },
  {
    id: 3,
    question: 'Berapa lama waktu yang kamu miliki untuk memasak?',
    choices: [
      { id: 1, value: 'Sama sekali tidak ada waktu' },
      { id: 2, value: '15-30 menit' },
      { id: 3, value: '30-45 menit' },
      { id: 4, value: '45+ menit' },
    ],
  },
  {
    id: 4,
    question: 'Untuk acara apa makanan ini?',
    choices: [
      { id: 1, value: 'Makan Malam Santai' },
      { id: 2, value: 'Makan Malam Romantis' },
      { id: 3, value: 'Pesta' },
      { id: 4, value: 'Makan Sendiri' },
    ],
  },
  {
    id: 5,
    question: 'Seberapa pedas yang kamu inginkan?',
    choices: [
      { id: 1, value: 'Ringan' },
      { id: 2, value: 'Sedang' },
      { id: 3, value: 'Pedas' },
      { id: 4, value: 'Sangat Pedas' },
    ],
  },
  {
    id: 6,
    question: 'Metode memasak apa yang kamu sukai?',
    choices: [
      { id: 1, value: 'Memanggang' },
      { id: 2, value: 'Membakar' },
      { id: 3, value: 'Menumis' },
      { id: 4, value: 'Merebus' },
    ],
  },
  {
    id: 7,
    question: 'Berapa anggaranmu untuk makanan ini?',
    choices: [
      { id: 1, value: 'Anggaran Rendah' },
      { id: 2, value: 'Anggaran Sedang' },
      { id: 3, value: 'Anggaran Tinggi' },
      { id: 4, value: 'Tidak Ada Anggaran' },
    ],
  },
  {
    id: 8,
    question: 'Seberapa besar porsi makanan yang kamu inginkan?',
    choices: [
      { id: 1, value: 'Sedikit' },
      { id: 2, value: 'Sedang' },
      { id: 3, value: 'Banyak' },
      { id: 4, value: 'Tidak Penting' },
    ],
  },
]

interface Choice {
  id: number
  value: string
}

interface Question {
  id: number
  question: string
  choices: Choice[]
}

const data: Question[] = questions
const ansList = ref<Array<Choice>>([] as Choice[])
const currQuestionNum = ref<number>(0)
const response = ref<object>({
  defMsg: 'Processing ...',
  hasAns: false,
  hasErr: false,
  title: '',
  body: '',
})

const chooseAns = (e: MouseEvent, value: Choice) => {
  ansList.value.push(value)
  console.log('ansList: ', ansList.value)
  currQuestionNum.value++
}

const processAns = computed<object>(() => response.value)

const askAI = (): void => {
  const genAI = new GoogleGenerativeAI(GOOGLE_API_KEY)
  const model = genAI.getGenerativeModel({ model: 'gemini-1.5-flash' })
  const prompt = `Tolong rekomendasikan resep makanan untuk saya berdasarkan jawaban-jawaban saya berikut ini:
    ${data.map((item, index) => {
    return `
        Nomor: ${index + 1}
        Pertanyaan: ${item.question}
        Jawaban: ${ansList.value[index].value}`
  })}
  Aturan:
1. Makanan harus berasal dari Indonesia
2. Gunakan format jawaban berikut:
    Judul: [Nama Makanan]
    Isi: [Deskripsi Makanan]
3. Gunakan huruf kecil untuk semua kata dan tidak mengandung format markdown
`
  //> text = originalText.split(/Title:|Body:|Footer:/).map(item => item.trim()).slice(1);

  console.log('prompt: ', prompt)
  model
    .generateContent(prompt)
    .then(res => {
      const result: string = res.response.text()
      console.log('result: ', result)
      if (result.toLowerCase().includes('judul')) {
        const deconstructAns: Array<string> = result
          .split(/judul:|isi:/)
          .map(item => item.trim())
          .slice(1)
        console.log('test: ', deconstructAns)
        response.value = {
          title: deconstructAns[0],
          body: deconstructAns[1],
          hasAns: true,
          hasErr: false,
        }
      }
    })
    .catch(err => {
      console.error(err)
      response.value = {
        defMsg: 'Error: Something went wrong',
        hasAns: false,
        hasErr: true,
      }
    })
}

watch(currQuestionNum, newVal => {
  if (newVal === data.length) {
    askAI()
  }
})
</script>

<template>
  <div v-if="currQuestionNum < data.length">
    <div v-for="(item, index) in data" :key="item.id">
      <div v-if="currQuestionNum === index">
        <p>
          {{ item.question }}
        </p>
        <div v-for="choice in item.choices" :key="choice.id">
          <p @click="chooseAns($event, choice)">
            {{ choice.value }}
          </p>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <div v-if="processAns.hasAns">
      <h3>{{ processAns.title }}</h3>
      <p>{{ processAns.body }}</p>
    </div>
      <p v-else>{{ processAns.defMsg }}</p>
  </div>
</template>

<style scoped></style>
