<script setup>
import RadioQuestion from '../form/RadioQuestion.vue'
import CheckboxQuestion from '../form/CheckboxQuestion.vue'
import TextQuestion from '../form/TextQuestion.vue'
import SelectQuestion from '../form/SelectQuestion.vue'
import { computed } from 'vue'

const props = defineProps({
  question: { type: Object, default: () => ({}) },
  answerQuestion: { type: [String, Number, Array], required: false },
})

const emit = defineEmits({ answer: null })

const componentMap = {
  radio: RadioQuestion,
  checkbox: CheckboxQuestion,
  text: TextQuestion,
  select: SelectQuestion,
}

const currentComponent = computed(() => componentMap[props.question.type])

const normalized = computed(() => {
  if (props.question.type === 'text') {
    return {
      id: props.question.id,
      placeholder: props.question.placeholder,
    }
  }
  return {
    id: props.question.id,
    options: props.question.options,
  }
})

function onChildAnswer(value) {
  emit('answer', { id: props.question.id, value })
}
</script>

<template>
  <div class="mb-6">
    <h2 class="text-xl font-semibold mb-2 text-gray-900 dark:text-gray-100">
      {{ question.title }}
    </h2>
    <p v-if="question.description" class="text-gray-500 dark:text-gray-400 mb-3">
      {{ question.description }}
    </p>

    <!-- Динамический компонент -->
    <component
      :is="currentComponent"
      v-bind="normalized"
      :modelValue="answerQuestion"
      @update:modelValue="onChildAnswer"
    />
  </div>
</template>
