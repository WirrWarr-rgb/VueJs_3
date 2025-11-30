<script setup>
import { computed } from 'vue'

const props = defineProps({
  currentStep: { type: Number, required: true },
  totalSteps: { type: Number, required: true },
})

const percent = computed(() => {
  if (!props.totalSteps) return 0
  return ((props.currentStep + 1) / props.totalSteps) * 100
})
</script>

<template>
  <div class="mb-6">
    <div class="h-2 w-full bg-gray-200 dark:bg-gray-700 rounded-full overflow-hidden">
      <div
        class="h-2 bg-blue-500 dark:bg-blue-400 transition-all duration-300"
        :style="{ width: percent + '%' }"
      />
    </div>
    <p class="mt-2 text-sm text-gray-600 dark:text-gray-400 text-center">
      Вопрос {{ currentStep + 1 }} из {{ totalSteps }}
    </p>
  </div>
</template>
