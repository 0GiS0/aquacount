<script setup lang="ts">
import { RouterLink } from 'vue-router'
</script>

<template>
  <header class="border-b border-blue-200 bg-gradient-to-r from-blue-500 to-cyan-500">
    <div class="mx-auto flex max-w-7xl items-center justify-between px-4 py-4 sm:px-6 lg:px-8">
      <RouterLink to="/" class="flex items-center gap-2 text-xl font-bold text-white">
        <span>💧</span>
        <span>AquaCount</span>
      </RouterLink>
      <nav class="flex items-center gap-6">
        <RouterLink
          to="/"
          class="text-sm font-medium text-white/80 transition-colors hover:text-white"
        >
          Inicio
        </RouterLink>
      </nav>
    </div>
  </header>
</template>
