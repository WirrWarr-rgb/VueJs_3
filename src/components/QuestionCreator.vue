<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-question'])

const newQuestion = ref({
  id: '',
  title: '',
  description: '',
  type: 'radio',
  placeholder: '',
  options: [{ value: '', label: '' }],
})

function addOption() {
  newQuestion.value.options.push({ value: '', label: '' })
}

function removeOption(index) {
  if (newQuestion.value.options.length > 1) {
    newQuestion.value.options.splice(index, 1)
  }
}

function addQuestion() {
  if (!newQuestion.value.id || !newQuestion.value.title) {
    alert('Заполните обязательные поля: ID и заголовок')
    return
  }

  // Валидация опций для типов, которые их требуют
  if (newQuestion.value.type !== 'text') {
    const hasEmptyOptions = newQuestion.value.options.some((opt) => !opt.value || !opt.label)
    if (hasEmptyOptions) {
      alert('Заполните все варианты ответов')
      return
    }
  }

  const questionToAdd = {
    ...newQuestion.value,
    // Убираем опцию у текстовых запросов
    ...(newQuestion.value.type === 'text' && { options: undefined }),
  }

  emit('add-question', questionToAdd)
  resetForm()
}

function resetForm() {
  newQuestion.value = {
    id: '',
    title: '',
    description: '',
    type: 'radio',
    placeholder: '',
    options: [{ value: '', label: '' }],
  }
}

function onTypeChange() {
  // Очистка placeholder при смене типа, если это не text
  if (newQuestion.value.type !== 'text') {
    newQuestion.value.placeholder = ''
  }
}
</script>

<template>
  <div class="bg-white dark:bg-gray-800 rounded-2xl shadow p-6 mt-6 transition-colors duration-300">
    <h2 class="text-xl font-semibold mb-4 text-gray-900 dark:text-gray-100">
      Создать новый вопрос
    </h2>

    <div class="space-y-4">
      <div>
        <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1"
          >ID вопроса *</label
        >
        <input
          v-model="newQuestion.id"
          type="text"
          class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
          placeholder="Например: favorite_color"
        />
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1"
          >Заголовок *</label
        >
        <input
          v-model="newQuestion.title"
          type="text"
          class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
          placeholder="Введите вопрос"
        />
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1"
          >Описание</label
        >
        <textarea
          v-model="newQuestion.description"
          class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
          placeholder="Необязательное описание"
        />
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1"
          >Тип вопроса</label
        >
        <select
          v-model="newQuestion.type"
          @change="onTypeChange"
          class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 transition-colors"
        >
          <option value="radio">Один вариант (radio)</option>
          <option value="checkbox">Несколько вариантов (checkbox)</option>
          <option value="select">Выпадающий список (select)</option>
          <option value="text">Текстовый ответ</option>
        </select>
      </div>

      <div v-if="newQuestion.type === 'text'">
        <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1"
          >Placeholder</label
        >
        <input
          v-model="newQuestion.placeholder"
          type="text"
          class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
          placeholder="Текст-подсказка для поля ввода"
        />
      </div>

      <div v-else>
        <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1"
          >Варианты ответов *</label
        >
        <div v-for="(option, index) in newQuestion.options" :key="index" class="flex gap-2 mb-2">
          <input
            v-model="option.value"
            type="text"
            class="border border-gray-300 dark:border-gray-600 rounded p-2 flex-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
            placeholder="Значение (value)"
          />
          <input
            v-model="option.label"
            type="text"
            class="border border-gray-300 dark:border-gray-600 rounded p-2 flex-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 transition-colors"
            placeholder="Текст (label)"
          />
          <button
            @click="removeOption(index)"
            class="px-3 py-2 bg-red-500 hover:bg-red-600 text-white rounded transition-colors"
            :disabled="newQuestion.options.length === 1"
          >
            ×
          </button>
        </div>
        <button
          @click="addOption"
          class="px-4 py-2 bg-green-500 hover:bg-green-600 text-white rounded transition-colors"
        >
          + Добавить вариант
        </button>
      </div>

      <button
        @click="addQuestion"
        class="w-full px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded transition-colors"
      >
        Добавить вопрос
      </button>
    </div>
  </div>
</template>
