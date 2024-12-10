<script setup>
const quizzes = await queryContent('/quiz').find()

function startQuiz(slug) {
    navigateTo(`/quiz/${slug}`)
}
</script>

<template>
    <div class="container mx-auto p-4 bg-gray-900 text-white">
        <h1 class="text-3xl font-bold mb-6">Liste des Quiz</h1>
        <div v-for="quiz in quizzes" :key="quiz.slug" class="quiz-item bg-gray-800 shadow-md rounded-lg p-6 mb-4">
            <h2 class="text-2xl font-semibold mb-2">{{ quiz.title }}</h2>
            <p class="text-gray-400 mb-4">{{ quiz.questions.length }} questions</p>
            <button @click="startQuiz(quiz.slug)" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
                Lancer le quiz
            </button>
        </div>
    </div>
</template>