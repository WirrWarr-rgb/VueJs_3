<script setup>
defineProps({
  answers: {
    type: Object,
    required: true,
  },
  surveyQuestions: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['reset'])

// Функция для получения читаемого текста ответа
function getAnswerText(question, answerValue) {
  if (!answerValue && answerValue !== 0) return '—'

  if (question.type === 'checkbox' && Array.isArray(answerValue)) {
    return (
      answerValue
        .map((value) => {
          const option = question.options?.find((opt) => opt.value === value)
          return option?.label || value
        })
        .join(', ') || '—'
    )
  }

  if (question.type !== 'text') {
    const option = question.options?.find((opt) => opt.value === answerValue)
    return option?.label || answerValue
  }

  return answerValue
}
</script>

<template>
  <div class="bg-white dark:bg-gray-800 rounded-2xl shadow p-6 transition-colors duration-300">
    <h2 class="text-xl font-semibold mb-4">Ваши ответы</h2>
    <div class="space-y-3">
      <div
        v-for="question in surveyQuestions"
        :key="question.id"
        class="border border-gray-200 dark:border-gray-700 rounded-lg p-3 transition-colors duration-300"
      >
        <div class="text-sm text-gray-500 dark:text-gray-400">{{ question.title }}</div>
        <div class="font-medium text-gray-900 dark:text-gray-100">
          {{ getAnswerText(question, answers[question.id]) }}
        </div>
      </div>
    </div>

    <button
      class="mt-6 px-4 py-2 rounded bg-gray-100 dark:bg-gray-700 text-gray-900 dark:text-gray-100 hover:bg-gray-200 dark:hover:bg-gray-600 transition-colors"
      @click="emit('reset')"
    >
      Сбросить ответы
    </button>
  </div>
</template>
