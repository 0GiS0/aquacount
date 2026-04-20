<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { api } from '../composables/useApi'
import type { HealthStatus } from '../types'

const health = ref<HealthStatus | null>(null)
const healthLoading = ref(true)
const glassesCount = ref(0)
const dailyGoal = 8
const isAnimating = ref(false)

const progress = computed(() => Math.min((glassesCount.value / dailyGoal) * 100, 100))
const goalReached = computed(() => glassesCount.value >= dailyGoal)

function addGlass() {
  glassesCount.value++
  isAnimating.value = true
  setTimeout(() => {
    isAnimating.value = false
  }, 300)
}

function resetCount() {
  glassesCount.value = 0
}

onMounted(async () => {
  try {
    const { data } = await api.get<HealthStatus>('/api/health')
    health.value = data
  } catch {
    // Backend not ready
  } finally {
    healthLoading.value = false
  }
})
</script>

<template>
  <div
    v-if="!health && healthLoading"
    class="flex min-h-[calc(100vh-73px)] flex-col items-center justify-center bg-gradient-to-b from-blue-50 to-cyan-50 text-center"
  >
    <div class="mb-8">
      <span class="animate-pulse text-9xl">💧</span>
    </div>
    <h1 class="text-3xl font-bold tracking-tight text-blue-900 sm:text-4xl">
      Cargando AquaCount...
    </h1>
    <p class="mt-3 text-lg text-blue-600">Preparando tu contador de agua</p>
  </div>

  <div
    v-else
    class="min-h-[calc(100vh-73px)] bg-gradient-to-b from-blue-50 via-cyan-50 to-blue-100"
  >
    <div class="mx-auto max-w-lg px-4 py-8">
      <div class="mb-8 text-center">
        <h1 class="text-4xl font-bold tracking-tight text-blue-900 sm:text-5xl">💧 AquaCount</h1>
        <p class="mt-2 text-blue-600">Tu contador de vasos de agua</p>
      </div>

      <div class="rounded-3xl bg-white/80 p-8 shadow-xl shadow-blue-200/50 backdrop-blur-sm">
        <div class="mb-8 text-center">
          <div
            class="mb-2 text-8xl font-bold text-blue-600 transition-transform duration-300"
            :class="{ 'scale-125': isAnimating }"
          >
            {{ glassesCount }}
          </div>
          <p class="text-xl text-blue-500">
            {{ glassesCount === 1 ? 'vaso' : 'vasos' }} de agua hoy
          </p>
        </div>

        <div class="mb-8">
          <div class="mb-2 flex justify-between text-sm text-blue-600">
            <span>Progreso diario</span>
            <span>{{ glassesCount }}/{{ dailyGoal }} vasos</span>
          </div>
          <div class="h-4 overflow-hidden rounded-full bg-blue-100">
            <div
              class="h-full rounded-full transition-all duration-500 ease-out"
              :class="goalReached ? 'bg-green-500' : 'bg-blue-500'"
              :style="{ width: progress + '%' }"
            ></div>
          </div>
          <p v-if="goalReached" class="mt-2 text-center font-medium text-green-600">
            🎉 ¡Meta alcanzada!
          </p>
        </div>

        <button
          type="button"
          class="w-full rounded-2xl bg-blue-500 py-8 shadow-lg shadow-blue-300/50 transition-all duration-200 hover:bg-blue-600 active:scale-95"
          @click="addGlass"
        >
          <span class="mb-2 block text-6xl">💧</span>
          <span class="text-2xl font-bold text-white">Añadir vaso</span>
        </button>

        <button
          v-if="glassesCount > 0"
          type="button"
          class="mt-4 w-full rounded-xl bg-blue-50 py-3 font-medium text-blue-600 transition-colors hover:bg-blue-100"
          @click="resetCount"
        >
          Reiniciar contador
        </button>
      </div>

      <div class="mt-6 text-center text-sm text-blue-500">
        <p>💡 Se recomienda beber 8 vasos de agua al día</p>
      </div>

      <div class="mt-4 text-center">
        <div
          v-if="health"
          class="inline-flex items-center gap-2 rounded-full bg-green-100 px-3 py-1 text-xs text-green-700"
        >
          <span class="h-1.5 w-1.5 rounded-full bg-green-500"></span>
          Conectado
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.scale-125 {
  transform: scale(1.25);
}
</style>
