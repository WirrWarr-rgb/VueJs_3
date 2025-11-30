<script setup>
defineProps({
  options: { type: Array, required: true },
})

const modelValue = defineModel({ type: [String, Number] })
</script>

<template>
  <select
    v-model="modelValue"
    class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:focus:ring-blue-600 dark:focus:border-blue-600 transition-colors"
  >
    <option value="" disabled class="text-gray-500 dark:text-gray-400">Выберите вариант...</option>
    <option
      v-for="option in options"
      :key="option.value"
      :value="option.value"
      class="text-gray-900 dark:text-gray-100 bg-white dark:bg-gray-700"
    >
      {{ option.label }}
    </option>
  </select>
</template>
