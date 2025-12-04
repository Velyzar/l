<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <div class="max-w-4xl mx-auto">
      <nav class="p-4 bg-gray-100 rounded-b-lg shadow-md flex gap-4">
        <RouterLink to="/" class="text-blue-600 hover:underline">Home</RouterLink>
        <RouterLink to="/about" class="text-blue-600 hover:underline">About</RouterLink>
        <RouterLink to="/contact" class="text-blue-600 hover:underline">Contact</RouterLink>
        <RouterLink to="/dating" class="text-red-500 font-bold">Dating App</RouterLink>
      </nav>
    </div>
  </header>

  <main class="max-w-4xl mx-auto mt-5 px-4">
    <RouterView />
  </main>
</template>