<script setup lang="ts">
import { GoogleGenerativeAI } from '@google/generative-ai'
import { computed, ref, watch } from 'vue'
import QuestionsCard from './components/questions/QuestionsCard.vue'
import RecomendationCard from './components/recomendations/RecomendationCard.vue'
const GOOGLE_API_KEY = import.meta.env.VITE_API_GOOGLE_API_KEY

const questions = [
  {
    id: 1,
    question: 'Bagaimana perasaanmu hari ini?',
    choices: [
      {
        id: 1,
        value: 'Senang dan Berenergi',
        name: 'mood',
        label: 'Senang dan Berenergi',
      },
      {
        id: 2,
        value: 'Sedih dan Lelah',
        name: 'mood',
        label: 'Sedih dan Lelah',
      },
      {
        id: 3,
        value: 'Marah dan Frustrasi',
        name: 'mood',
        label: 'Marah dan Frustrasi',
      },
      {
        id: 4,
        value: 'Tenang dan Rileks',
        name: 'mood',
        label: 'Tenang dan Rileks',
      },
    ],
  },
  {
    id: 2,
    question: 'Makanan seperti apa yang sedang kamu inginkan?',
    choices: [
      {
        id: 1,
        value: 'Sesuatu yang Asin dan Renyah',
        name: 'preference',
        label: 'Sesuatu yang Asin dan Renyah',
      },
      {
        id: 2,
        value: 'Sesuatu yang Manis dan Menggoda',
        name: 'preference',
        label: 'Sesuatu yang Manis dan Menggoda',
      },
      {
        id: 3,
        value: 'Sesuatu yang Hangat dan Nyaman',
        name: 'preference',
        label: 'Sesuatu yang Hangat dan Nyaman',
      },
      {
        id: 4,
        value: 'Sesuatu yang Ringan dan Segar',
        name: 'preference',
        label: 'Sesuatu yang Ringan dan Segar',
      },
    ],
  },
  {
    id: 3,
    question: 'Berapa lama waktu yang kamu miliki untuk memasak?',
    choices: [
      {
        id: 1,
        value: 'Sama sekali tidak ada waktu',
        name: 'time',
        label: 'Sama sekali tidak ada waktu',
      },
      { id: 2, value: '15-30 menit', name: 'time', label: '15-30 menit' },
      { id: 3, value: '30-45 menit', name: 'time', label: '30-45 menit' },
      { id: 4, value: '45+ menit', name: 'time', label: '45+ menit' },
    ],
  },
  {
    id: 4,
    question: 'Untuk acara apa makanan ini?',
    choices: [
      {
        id: 1,
        value: 'Makan Malam Santai',
        name: 'occasion',
        label: 'Makan Malam Santai',
      },
      {
        id: 2,
        value: 'Makan Malam Romantis',
        name: 'occasion',
        label: 'Makan Malam Romantis',
      },
      { id: 3, value: 'Pesta', name: 'occasion', label: 'Pesta' },
      {
        id: 4,
        value: 'Makan Sendiri',
        name: 'occasion',
        label: 'Makan Sendiri',
      },
    ],
  },
  {
    id: 5,
    question: 'Seberapa pedas yang kamu inginkan?',
    choices: [
      { id: 1, value: 'Ringan', name: 'spiciness', label: 'Ringan' },
      { id: 2, value: 'Sedang', name: 'spiciness', label: 'Sedang' },
      { id: 3, value: 'Pedas', name: 'spiciness', label: 'Pedas' },
      {
        id: 4,
        value: 'Sangat Pedas',
        name: 'spiciness',
        label: 'Sangat Pedas',
      },
    ],
  },
  {
    id: 6,
    question: 'Metode memasak apa yang kamu sukai?',
    choices: [
      {
        id: 1,
        value: 'Memanggang',
        name: 'cookingMethod',
        label: 'Memanggang',
      },
      {
        id: 2,
        value: 'Membakar',
        name: 'cookingMethod',
        label: 'Membakar',
      },
      {
        id: 3,
        value: 'Menumis',
        name: 'cookingMethod',
        label: 'Menumis',
      },
      {
        id: 4,
        value: 'Merebus',
        name: 'cookingMethod',
        label: 'Merebus',
      },
    ],
  },
  {
    id: 7,
    question: 'Berapa anggaranmu untuk makanan ini?',
    choices: [
      {
        id: 1,
        value: 'Anggaran Rendah',
        name: 'budget',
        label: 'Anggaran Rendah',
      },
      {
        id: 2,
        value: 'Anggaran Sedang',
        name: 'budget',
        label: 'Anggaran Sedang',
      },
      {
        id: 3,
        value: 'Anggaran Tinggi',
        name: 'budget',
        label: 'Anggaran Tinggi',
      },
      {
        id: 4,
        value: 'Tidak Ada Anggaran',
        name: 'budget',
        label: 'Tidak Ada Anggaran',
      },
    ],
  },
  {
    id: 8,
    question: 'Seberapa besar porsi makanan yang kamu inginkan?',
    choices: [
      { id: 1, value: 'Sedikit', name: 'portionSize', label: 'Sedikit' },
      { id: 2, value: 'Sedang', name: 'portionSize', label: 'Sedang' },
      { id: 3, value: 'Banyak', name: 'portionSize', label: 'Banyak' },
      {
        id: 4,
        value: 'Tidak Penting',
        name: 'portionSize',
        label: 'Tidak Penting',
      },
    ],
  },
]

export interface Choice {
  id: number
  name: string
  label: string
  value: string
}

export interface Question {
  id: number
  question: string
  choices: Choice[]
}

export interface Answer {
  value: string
  defMsg?: string
  hasAns?: boolean
  hasErr?: boolean
}

const data: Question[] = questions
const ansList = ref<Array<Choice>>([] as Choice[])
const currQuestionNum = ref<number>(0)
const response = ref<Answer>({
  value: '',
  defMsg: 'Processing ...',
  hasAns: false,
  hasErr: false,
})

const chooseAns = (_: MouseEvent, value: Choice) => {
  ansList.value.push(value)
  currQuestionNum.value++
}

const currQuestion = computed<Question>(() => {
  return data[currQuestionNum.value]
})

const processAns = computed<Answer>(() => response.value)

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
2. Jawaban harus dalam bentuk markdown
3. Untuk setiap baris baru, gunakan \n
4. Jangan memberikan respon dalam format code
5. List dalam bentuk urutan nomor
6. Gunakan format jawaban berikut:

    ##### [Judul Makanan]
    [Enter two times]

    ##### Deskripsi
      [Deskripsi Makanan]
    [Enter two times]

    ##### Alat
      - [List Alat]
    [Enter two times]

    ##### Bahan
      - [List Bahan]
    [Enter two times]

    ##### Langkah-langkah
      1. [List Langkah]
`

  const handleResponse = (res: string): void => {
    console.log('res: ', res)
    response.value = {
      value: res,
      hasAns: true,
      hasErr: false,
    }
  }

  model
    .generateContent(prompt)
    .then(res => {
      const result: string = res.response.text()
      handleResponse(result)
    })
    .catch(err => {
      console.error(err)
      response.value = {
        value: '',
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
  <main class="h-screen flex flex-col justify-center items-center">
    <div v-if="currQuestionNum < data.length">
      <QuestionsCard :header="currQuestion" :total-quesiton="data.length" :body="currQuestion.choices"
        @choose-ans="chooseAns" />
    </div>
    <div v-else>
      <div v-if="processAns.hasAns">
        <RecomendationCard :body="processAns.value" />
      </div>
      <p v-else>{{ processAns.defMsg }}</p>
    </div>
  </main>
</template>
