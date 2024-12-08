<script setup lang="ts">
import {
  GoogleGenerativeAI,
  GenerativeModel,
  ChatSession,
  type GenerateContentResult,
  type GenerateContentStreamResult,
} from '@google/generative-ai'
import { computed, ref, watch } from 'vue'
import QuestionsCard from './components/questions/QuestionsCard.vue'
import RecomendationCard from './components/recomendations/RecomendationCard.vue'
const GOOGLE_API_KEY = import.meta.env.VITE_API_GOOGLE_API_KEY

const questions = [
  {
    id: 1,
    question: 'Jenis makanan apa yang sedang kamu inginkan?',
    choices: [
      {
        id: 1,
        value: 'Makanan Berat',
        name: 'mood',
        label: 'Makanan Berat',
      },
      {
        id: 2,
        value: 'Makanan Ringan',
        name: 'mood',
        label: 'Makanan Ringan',
      },
      {
        id: 3,
        value: 'Cemilan',
        name: 'mood',
        label: 'Cemilan',
      },
      {
        id: 4,
        value: 'Desert',
        name: 'mood',
        label: 'Desert',
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
    question: 'Apakah makanan ini harus pedas?',
    choices: [
      { id: 1, value: 'Ya', name: 'spiciness', label: 'Ya' },
      { id: 2, value: 'Tidak', name: 'spiciness', label: 'Tidak' },
    ],
  },
  {
    id: 4,
    question: 'Apakah kamu miliki prioritas lauk?',
    choices: [
      {
        id: 1,
        value: 'Ayam',
        name: 'priority',
        label: 'Ayam',
      },
      {
        id: 2,
        value: 'Seafood (Ikan, udang, cumi, dsb)',
        name: 'priority',
        label: 'Seafood (Ikan, udang, cumi, dsb)',
      },
      {
        id: 3,
        value: 'Sayur',
        name: 'priority',
        label: 'Menumis',
      },
      {
        id: 4,
        value: 'Tidak ada',
        name: 'priority',
        label: 'Tidak ada',
      },
    ],
  },
  {
    id: 5,
    question: 'Metode memasak apa yang kamu sukai?',
    choices: [
      {
        id: 1,
        value: 'Menggoreng',
        name: 'cookingMethod',
        label: 'Menggoreng',
      },
      {
        id: 2,
        value: 'Merebus',
        name: 'cookingMethod',
        label: 'Merebus',
      },
      {
        id: 3,
        value: 'Menumis',
        name: 'cookingMethod',
        label: 'Menumis',
      },
      {
        id: 4,
        value: 'Apa saja',
        name: 'cookingMethod',
        label: 'Apa saja',
      },
    ],
  },
  {
    id: 6,
    question: 'Bagaimana cuaca hari ini?',
    choices: [
      {
        id: 1,
        value: 'Mendung',
        name: 'weather',
        label: 'Mendung',
      },
      {
        id: 2,
        value: 'Hujan',
        name: 'weather',
        label: 'Hujan',
      },
      {
        id: 3,
        value: 'Cerah seperti hatiku',
        name: 'weather',
        label: 'Cerah seperti hatiku',
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

interface History {
  role: 'user' | 'model'
  parts: [{ text: string }]
}

const defaultResponse = computed<Answer>(() => {
  return {
    value: '',
    defMsg: 'Processing ...',
    hasAns: false,
    hasErr: false,
  }
})

const data: Question[] = questions
const ansList = ref<Array<Choice>>([] as Choice[])
const currQuestionNum = ref<number>(0)
const generateAgain = ref<boolean>(false)
const historyChat = ref<Array<History>>([] as History[])
const response = ref<Answer>(defaultResponse.value)

const chooseAns = (_: MouseEvent, value: Choice) => {
  ansList.value.push(value)
  currQuestionNum.value++
}

const currQuestion = computed<Question>(() => {
  return data[currQuestionNum.value]
})

const processAns = computed<Answer>(() => response.value)
const baseAIRules = computed<string>(() => {
  return `
      Aturan:
    1. Makanan harus berasal dari Indonesia
    2. Jawaban harus dalam bentuk markdown
    3. Untuk setiap baris baru, gunakan \n
    4. Jangan memberikan respon dalam format code
    5. List dalam bentuk urutan nomor
    6. Jangan menggunakan <br> atau <br/> untuk baris baru
    7. Jawaban tidak boleh dalam bentuk code!
    8. Jawaban harus sama persis denagn format yang diberikan
    9. Gunakan format jawaban berikut:
    **IMPORTANT:**
    JANGAN MEMBERIKAN TAG (\`<code>\`, \`</code>\`, ATAU BACKTICKS) DALAM Jawaban
`
})

const baseAIResponseFormat = computed<string>(() => {
  return `
      #### [Judul Makanan]

      ##### Deskripsi
        Deskripsi Makanan

      ##### Alat
        - List Alat

      ##### Bahan
        - List Bahan

      ##### Langkah-langkah
        1. List Langkah
  `
})

const askAI = (): void => {
  const genAI = new GoogleGenerativeAI(GOOGLE_API_KEY)
  const model = genAI.getGenerativeModel({ model: 'gemini-1.5-flash' })
  const prompt = `Tolong rekomendasikan resep makanan untuk saya berdasarkan jawaban-jawaban saya berikut ini:\n
    ${data.map((item, index) => {
      return `
        Nomor: ${index + 1}
        Pertanyaan: ${item.question}
        Jawaban: ${ansList.value[index].value}\n`
    })}
    ${baseAIRules.value}\n
    ${baseAIResponseFormat.value}
`
  try {
    handleUserPrompt(prompt)
    handleModelPrompt(prompt)
  } catch (err) {
    console.error(err)
  }
}

const handleGenerateAgain = async (): Promise<void> => {
  response.value = defaultResponse.value
  const prompt = `Tolong cari rekomendasi makanan lain yang sejenis dengan menggunakan template yang sama!\n
${baseAIRules.value}`
  try {
    handleUserPrompt(prompt)
    handleModelPrompt(prompt)
  } catch (err) {
    console.error(err)
  }
}

const addUserHistory = (prompt: string): void => {
  let newHistory: History = {
    role: 'user',
    parts: [{ text: prompt }],
  }
  addNewHistory(newHistory)
}

const addModelHistory = (prompt: string): void => {
  let newHistory: History = {
    role: 'model',
    parts: [{ text: prompt }],
  }
  addNewHistory(newHistory)
}

const addNewHistory = (chat: History): void => {
  historyChat.value.push(chat)
}

const handleUserPrompt = (prompt: string) => {
  console.log('prompt', prompt)
  addUserHistory(prompt)
}

const handleModelPrompt = async (prompt: string): Promise<string> => {
  try {
    const genAI: GoogleGenerativeAI = new GoogleGenerativeAI(GOOGLE_API_KEY)
    const model: GenerativeModel = genAI.getGenerativeModel({
      model: 'gemini-1.5-flash',
    })
    const hasHistoryChat = historyChat.value.length > 0
    let res: GenerateContentResult | GenerateContentStreamResult
    if (!hasHistoryChat) {
      res = await model.generateContent(prompt)
    } else {
      const chat: ChatSession = model.startChat({ history: historyChat.value })
      res = await chat.sendMessage(prompt)
    }
    const result: string = res.response.text()
    response.value = {
      value: result,
      hasAns: true,
      hasErr: false,
    }
    addModelHistory(result)
    return result
  } catch (err) {
    console.error(err)
    response.value = {
      value: '',
      defMsg: 'Error: Something went wrong',
      hasAns: false,
      hasErr: true,
    }
    throw err
  }
}

watch(currQuestionNum, newVal => {
  if (newVal === data.length) {
    askAI()
  }
})
</script>

<template>
  <main class="flex flex-col justify-center items-center">
    <div v-if="currQuestionNum < data.length">
      <QuestionsCard
        :header="currQuestion"
        :total-quesiton="data.length"
        :body="currQuestion.choices"
        @choose-ans="chooseAns"
      />
    </div>
    <div v-else class="my-10">
      <div v-if="processAns.hasAns">
        <RecomendationCard
          :body="processAns.value"
          @handle-click="handleGenerateAgain"
        />
      </div>
      <p v-else>{{ processAns.defMsg }}</p>
    </div>
  </main>
</template>

<style scoped>
main {
  min-height: 100vh;
  background: var(--background);
}
</style>
