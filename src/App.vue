<script setup>
import { ref, watch, onMounted } from 'vue'
import SurveyForm from './components/SurveyForm.vue'
import SummaryPanel from './components/SummaryPanel.vue'
import QuestionCreator from './components/QuestionCreator.vue'
import QuestionsList from './components/QuestionsList.vue'
import { surveyQuestions as initialQuestions } from './data/questions.js'

const answers = ref({})
const surveyQuestions = ref([...initialQuestions])
const isDarkMode = ref(false)
const currentStep = ref(0)
const isSurveyCompleted = ref(false)

function onAnswer({ id, value }) {
  answers.value[id] = value
}

function resetSurvey() {
  answers.value = {}
  currentStep.value = 0
  isSurveyCompleted.value = false
}

function addQuestion(question) {
  surveyQuestions.value.push(question)
}

function updateQuestion(updatedQuestion) {
  const index = surveyQuestions.value.findIndex((q) => q.id === updatedQuestion.id)
  if (index !== -1) {
    surveyQuestions.value[index] = { ...updatedQuestion }
  }
}

function deleteQuestion(questionId) {
  surveyQuestions.value = surveyQuestions.value.filter((q) => q.id !== questionId)
  // Если удалили текущий вопрос, сбрасываем шаг
  if (currentStep.value >= surveyQuestions.value.length) {
    currentStep.value = Math.max(0, surveyQuestions.value.length - 1)
  }
}

function updateCurrentStep(step) {
  currentStep.value = step
}

function completeSurvey() {
  isSurveyCompleted.value = true
}

function restartSurvey() {
  resetSurvey()
}

// Применяем класс темы к html элементу
watch(isDarkMode, (newValue) => {
  if (newValue) {
    document.documentElement.classList.add('dark')
  } else {
    document.documentElement.classList.remove('dark')
  }
  localStorage.setItem('darkMode', JSON.stringify(newValue))
})

// Сохранение в LocalStorage при изменении ответов
watch(
  answers,
  (newAnswers) => {
    localStorage.setItem('surveyAnswers', JSON.stringify(newAnswers))
  },
  { deep: true },
)

// Загрузка из LocalStorage при загрузке приложения
onMounted(() => {
  const savedAnswers = localStorage.getItem('surveyAnswers')
  if (savedAnswers) {
    answers.value = JSON.parse(savedAnswers)
  }

  const savedQuestions = localStorage.getItem('surveyQuestions')
  if (savedQuestions) {
    surveyQuestions.value = JSON.parse(savedQuestions)
  }

  const savedTheme = localStorage.getItem('darkMode')
  if (savedTheme) {
    isDarkMode.value = JSON.parse(savedTheme)
    // Применяем тему сразу после загрузки
    if (isDarkMode.value) {
      document.documentElement.classList.add('dark')
    } else {
      document.documentElement.classList.remove('dark')
    }
  }

  const savedCompletion = localStorage.getItem('surveyCompleted')
  if (savedCompletion) {
    isSurveyCompleted.value = JSON.parse(savedCompletion)
  }
})

// Сохранение вопросов при изменении
watch(
  surveyQuestions,
  (newQuestions) => {
    localStorage.setItem('surveyQuestions', JSON.stringify(newQuestions))
  },
  { deep: true },
)

// Сохранение состояния завершения опроса
watch(isSurveyCompleted, (newValue) => {
  localStorage.setItem('surveyCompleted', JSON.stringify(newValue))
})
</script>

<template>
  <div :class="isDarkMode ? 'dark' : ''">
    <div
      class="min-h-screen bg-gray-50 dark:bg-gray-900 text-gray-900 dark:text-gray-100 p-6 transition-colors duration-300"
    >
      <div class="max-w-6xl mx-auto space-y-8">
        <div class="flex justify-center">
          <h1 class="text-3xl font-bold">Опрос о путешествиях</h1>
        </div>

        <div class="grid gap-6 md:grid-cols-2">
          <div
            class="bg-white dark:bg-gray-800 rounded-2xl shadow p-6 transition-colors duration-300"
          >
            <SurveyForm
              v-if="!isSurveyCompleted"
              :questions="surveyQuestions"
              :answers="answers"
              :current-step="currentStep"
              @answer="onAnswer"
              @update:current-step="updateCurrentStep"
              @complete="completeSurvey"
            />

            <!-- Сообщение о завершении опроса -->
            <div v-else class="text-center py-8">
              <div class="mb-6">
                <div class="text-6xl mb-4">🎉</div>
                <h2 class="text-2xl font-bold text-gray-900 dark:text-gray-100 mb-2">
                  Поздравляем!
                </h2>
                <p class="text-gray-600 dark:text-gray-400">Вы успешно прошли опрос!</p>
              </div>
              <button
                @click="restartSurvey"
                class="px-6 py-3 bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600 text-white rounded-lg font-semibold transition-colors"
              >
                Пройти еще раз
              </button>
            </div>
          </div>

          <SummaryPanel
            :answers="answers"
            :survey-questions="surveyQuestions"
            @reset="resetSurvey"
          />
        </div>

        <QuestionCreator @add-question="addQuestion" />

        <QuestionsList
          :questions="surveyQuestions"
          @update-question="updateQuestion"
          @delete-question="deleteQuestion"
        />
      </div>
    </div>
  </div>
</template>
