<script setup>
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

let quizzes = ref([])
let quiz = ref(null)

onMounted(async () => {
    try {
        const data = await queryContent('/quiz').find()
        quizzes.value = data
        quiz.value = quizzes.value.find((q) => q.slug === route.params.slug)

        if (!quiz.value) {
            router.push('/error')
        }
    } catch (error) {
        console.error(error)
        router.push('/error')
    }
})

const currentQuestionIndex = ref(0)
const score = ref(0)
const quizCompleted = ref(false)

const currentQuestion = computed(() => quiz.value ? quiz.value.questions[currentQuestionIndex.value] : null)

function answerQuestion(index) {
    if (index === currentQuestion.value.answer) {
        score.value++
    }

    if (currentQuestionIndex.value + 1 < quiz.value.questions.length) {
        currentQuestionIndex.value++
    } else {
        quizCompleted.value = true
    }
}

function resetQuiz() {
    currentQuestionIndex.value = 0
    score.value = 0
    quizCompleted.value = false
}

function goToHomePage() {
    router.push('/')
}
</script>

<template>
    <div class="container mx-auto p-4">
        <header class="mb-8">
            <h1 v-if="quiz" class="text-3xl font-bold mb-2">{{ quiz.title }}</h1>
            <p v-if="quiz" class="text-gray-600">{{ currentQuestionIndex + 1 }} / {{ quiz.questions.length }}</p>
        </header>

        <div v-if="quiz" class="mb-8">
            <p v-if="currentQuestion" class="text-xl mb-4">{{ currentQuestion.text }}</p>
            <div v-if="currentQuestion" v-for="(option, index) in currentQuestion.options" :key="index" class="mb-2">
                <button @click="answerQuestion(index)"
                    class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-700">
                    {{ option }}
                </button>
            </div>
        </div>

        <div v-if="quizCompleted" class="text-center">
            <p class="text-2xl mb-4">Votre score: {{ score }} / {{ quiz.questions.length }}</p>
            <button @click="resetQuiz"
                class="bg-green-500 text-white py-2 px-4 rounded hover:bg-green-700 mr-2">Recommencer</button>
            <button @click="goToHomePage" class="bg-gray-500 text-white py-2 px-4 rounded hover:bg-gray-700">
                Autre quiz
            </button>
        </div>
    </div>
</template>