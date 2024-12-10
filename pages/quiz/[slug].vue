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
        } else {
            startQuestion()
        }
    } catch (error) {
        console.error(error)
        router.push('/error')
    }
})

const currentQuestionIndex = ref(0)
const score = ref(0)
const quizCompleted = ref(false)
const questionStartTime = ref(0)

const currentQuestion = computed(() => quiz.value ? quiz.value.questions[currentQuestionIndex.value] : null)

function calculateScore(timeTaken) {
    if (timeTaken < 1) {
        return 5
    } else if (timeTaken > 5) {
        return 1
    } else {
        return 5 - (4 * (timeTaken - 1) / 4)
    }
}

function startQuestion() {
    questionStartTime.value = performance.now()
}

function answerQuestion(selectedIndex) {
    const timeTaken = (performance.now() - questionStartTime.value) / 1000

    if (selectedIndex === currentQuestion.value.answer) {
        score.value += calculateScore(timeTaken)
    }

    if (currentQuestionIndex.value + 1 < quiz.value.questions.length) {
        currentQuestionIndex.value++
        startQuestion()
    } else {
        quizCompleted.value = true
    }
}

function resetQuiz() {
    currentQuestionIndex.value = 0
    score.value = 0
    quizCompleted.value = false
    startQuestion()
}

function goToHomePage() {
    router.push('/')
}
</script>

<template>
    <div class="container mx-auto p-4 bg-gray-900 text-white">
        <header class="mb-4">
            <h1 v-if="quiz" class="text-2xl font-bold">{{ quiz.title }}</h1>
            <p v-if="quiz" class="text-gray-400">
                Question {{ currentQuestionIndex + 1 }} / {{ quiz.questions.length }}
            </p>
        </header>

        <div v-if="quiz && !quizCompleted">
            <p class="mb-4">{{ currentQuestion.text }}</p>
            <div v-for="(option, index) in currentQuestion.options" :key="index" class="mb-2">
                <button @click="answerQuestion(index)"
                    class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-700">
                    {{ option }}
                </button>
            </div>
            <p class="mt-4">Score actuel : {{ score.toFixed(3) }}</p>
        </div>

        <div v-if="quizCompleted" class="mt-4">
            <p class="text-xl font-semibold">Quiz terminé !</p>
            <p class="text-lg">Score final : {{ score.toFixed(3) }} / {{ quiz.questions.length * 5 }}</p>
            <button @click="resetQuiz" class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-700 mt-2">
                Recommencer
            </button>
            <button @click="goToHomePage" class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-700 mt-2 ml-2">
                Autre quiz
            </button>
        </div>
    </div>
</template>