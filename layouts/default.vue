<template>
    <div class="flex flex-col bg-gray-900 text-gray-100">
        <header class="bg-gray-800 text-white p-4 flex justify-between items-center">
            <h1 class="text-3xl font-bold">Mon Projet de Quiz</h1>
            <nav>
                <router-link to="/" class="text-gray-400 hover:text-white">Page principale</router-link>
            </nav>
        </header>
        <main class="flex-grow p-4">
            <slot />
        </main>
    </div>
</template>