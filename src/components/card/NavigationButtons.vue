<script setup>
defineProps({
  canGoBack: {
    type: Boolean,
    default: false,
  },
  canGoNext: {
    type: Boolean,
    default: false,
  },
  isLastStep: {
    type: Boolean,
    default: false,
  },
})

const emit = defineEmits(['next', 'back', 'complete'])
</script>

<template>
  <div class="flex justify-between mt-6">
    <button
      class="px-4 py-2 rounded bg-gray-100 dark:bg-gray-700 text-gray-900 dark:text-gray-100 hover:bg-gray-200 dark:hover:bg-gray-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
      :disabled="!canGoBack"
      @click="emit('back')"
    >
      Назад
    </button>

    <button
      v-if="!isLastStep"
      class="px-4 py-2 rounded bg-blue-600 dark:bg-blue-500 text-white hover:bg-blue-700 dark:hover:bg-blue-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
      :disabled="!canGoNext"
      @click="emit('next')"
    >
      Далее
    </button>

    <button
      v-else
      class="px-4 py-2 rounded bg-green-600 dark:bg-green-500 text-white hover:bg-green-700 dark:hover:bg-green-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
      :disabled="!canGoNext"
      @click="emit('complete')"
    >
      Завершить
    </button>
  </div>
</template>
