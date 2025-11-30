<script setup>
defineProps({
  options: {
    type: Array,
    required: true,
  },
  name: {
    type: String,
    default: 'radio-group',
  },
})
const modelValue = defineModel({ type: [String, Number] })
</script>

<template>
  <div class="space-y-2">
    <label
      v-for="option in options"
      :key="option.value"
      class="flex items-center gap-2 cursor-pointer text-gray-900 dark:text-gray-100 hover:text-gray-700 dark:hover:text-gray-300 transition-colors"
    >
      <input
        type="radio"
        :name="name"
        :value="option.value"
        v-model="modelValue"
        class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
      />
      <span class="select-none">{{ option.label }}</span>
    </label>
  </div>
</template>
