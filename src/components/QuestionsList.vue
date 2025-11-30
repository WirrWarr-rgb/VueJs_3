<script setup>
import { ref } from 'vue'

defineProps({
  questions: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['update-question', 'delete-question'])

const editingQuestion = ref(null)
const editForm = ref({
  id: '',
  title: '',
  description: '',
  type: 'radio',
  placeholder: '',
  options: [],
})

function startEdit(question) {
  editingQuestion.value = question.id
  editForm.value = {
    id: question.id,
    title: question.title,
    description: question.description || '',
    type: question.type,
    placeholder: question.placeholder || '',
    options: question.options ? [...question.options] : [],
  }
}

function saveEdit() {
  if (!editForm.value.title.trim()) {
    alert('Заголовок вопроса обязателен')
    return
  }

  if (
    editForm.value.type !== 'text' &&
    (!editForm.value.options || editForm.value.options.length === 0)
  ) {
    alert('Для этого типа вопроса нужны варианты ответов')
    return
  }

  const questionToUpdate = {
    ...editForm.value,
    // Для текстовых вопросов убираем options
    ...(editForm.value.type === 'text' && { options: undefined }),
  }

  emit('update-question', questionToUpdate)
  cancelEdit()
}

function cancelEdit() {
  editingQuestion.value = null
  editForm.value = {
    id: '',
    title: '',
    description: '',
    type: 'radio',
    placeholder: '',
    options: [],
  }
}

function addOption() {
  if (!editForm.value.options) {
    editForm.value.options = []
  }
  editForm.value.options.push({ value: '', label: '' })
}

function removeOption(index) {
  if (editForm.value.options && editForm.value.options.length > 1) {
    editForm.value.options.splice(index, 1)
  }
}

function handleDelete(questionId) {
  if (confirm('Вы уверены, что хотите удалить этот вопрос?')) {
    emit('delete-question', questionId)
  }
}

function getQuestionTypeLabel(type) {
  const types = {
    radio: 'Один вариант',
    checkbox: 'Несколько вариантов',
    select: 'Выпадающий список',
    text: 'Текстовый ответ',
  }
  return types[type] || type
}

function getOptionsPreview(question) {
  if (question.type === 'text') {
    return question.placeholder || 'Текстовое поле'
  }
  if (!question.options || question.options.length === 0) {
    return 'Нет вариантов'
  }
  return question.options.map((opt) => opt.label).join(', ')
}
</script>

<template>
  <div class="bg-white dark:bg-gray-800 rounded-2xl shadow p-6 mt-6 transition-colors duration-300">
    <h2 class="text-xl font-semibold mb-4 text-gray-900 dark:text-gray-100">Список вопросов</h2>

    <div class="overflow-x-auto">
      <table class="w-full text-left border-collapse">
        <thead>
          <tr class="border-b border-gray-200 dark:border-gray-700">
            <th class="p-3 font-semibold text-gray-900 dark:text-gray-100">ID</th>
            <th class="p-3 font-semibold text-gray-900 dark:text-gray-100">Заголовок</th>
            <th class="p-3 font-semibold text-gray-900 dark:text-gray-100">Тип</th>
            <th class="p-3 font-semibold text-gray-900 dark:text-gray-100">Варианты</th>
            <th class="p-3 font-semibold text-gray-900 dark:text-gray-100">Действия</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="question in questions"
            :key="question.id"
            class="border-b border-gray-200 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
          >
            <td class="p-3">
              <code class="text-sm bg-gray-100 dark:bg-gray-600 px-2 py-1 rounded">{{
                question.id
              }}</code>
            </td>
            <td class="p-3">
              <div v-if="editingQuestion === question.id">
                <input
                  v-model="editForm.title"
                  type="text"
                  class="border border-gray-300 dark:border-gray-600 p-2 rounded w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
                  placeholder="Заголовок вопроса"
                />
              </div>
              <span v-else class="font-medium">{{ question.title }}</span>
            </td>
            <td class="p-3">
              <div v-if="editingQuestion === question.id">
                <select
                  v-model="editForm.type"
                  class="border border-gray-300 dark:border-gray-600 p-2 rounded bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
                >
                  <option value="radio">Один вариант</option>
                  <option value="checkbox">Несколько вариантов</option>
                  <option value="select">Выпадающий список</option>
                  <option value="text">Текстовый ответ</option>
                </select>
              </div>
              <span v-else class="text-sm">{{ getQuestionTypeLabel(question.type) }}</span>
            </td>
            <td class="p-3">
              <div v-if="editingQuestion === question.id && editForm.type !== 'text'">
                <div
                  v-for="(option, index) in editForm.options"
                  :key="index"
                  class="flex gap-2 mb-2"
                >
                  <input
                    v-model="option.value"
                    type="text"
                    class="border border-gray-300 dark:border-gray-600 p-1 rounded flex-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 text-sm"
                    placeholder="Значение"
                  />
                  <input
                    v-model="option.label"
                    type="text"
                    class="border border-gray-300 dark:border-gray-600 p-1 rounded flex-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 text-sm"
                    placeholder="Текст"
                  />
                  <button
                    @click="removeOption(index)"
                    class="px-2 py-1 bg-red-500 hover:bg-red-600 text-white rounded text-sm transition-colors"
                    :disabled="editForm.options.length === 1"
                  >
                    ×
                  </button>
                </div>
                <button
                  @click="addOption"
                  class="px-3 py-1 bg-green-500 hover:bg-green-600 text-white rounded text-sm transition-colors"
                >
                  + Вариант
                </button>
              </div>
              <div v-else-if="editingQuestion === question.id && editForm.type === 'text'">
                <input
                  v-model="editForm.placeholder"
                  type="text"
                  class="border border-gray-300 dark:border-gray-600 p-2 rounded w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 text-sm"
                  placeholder="Placeholder для текстового поля"
                />
              </div>
              <span v-else class="text-sm text-gray-600 dark:text-gray-400 max-w-xs truncate block">
                {{ getOptionsPreview(question) }}
              </span>
            </td>
            <td class="p-3">
              <div v-if="editingQuestion === question.id" class="flex flex-col gap-2">
                <button
                  @click="saveEdit"
                  class="px-3 py-1 bg-green-500 hover:bg-green-600 text-white rounded text-sm transition-colors"
                >
                  Сохранить
                </button>
                <button
                  @click="cancelEdit"
                  class="px-3 py-1 bg-gray-500 hover:bg-gray-600 text-white rounded text-sm transition-colors"
                >
                  Отмена
                </button>
              </div>
              <div v-else class="flex flex-col gap-2">
                <button
                  @click="startEdit(question)"
                  class="px-3 py-1 bg-blue-500 hover:bg-blue-600 text-white rounded text-sm transition-colors"
                >
                  Редактировать
                </button>
                <button
                  @click="handleDelete(question.id)"
                  class="px-3 py-1 bg-red-500 hover:bg-red-600 text-white rounded text-sm transition-colors"
                >
                  Удалить
                </button>
              </div>
            </td>
          </tr>
          <tr v-if="questions.length === 0">
            <td colspan="5" class="p-4 text-center text-gray-500 dark:text-gray-400">
              Нет вопросов. Добавьте первый вопрос выше!
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
