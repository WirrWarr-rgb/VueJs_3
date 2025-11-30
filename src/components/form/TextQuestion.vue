<script setup>
defineProps({
  placeholder: String,
})

const modelValue = defineModel({ type: String })
</script>

<template>
  <input
    type="text"
    v-model="modelValue"
    :placeholder="placeholder"
    class="border border-gray-300 dark:border-gray-600 rounded p-2 w-full bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:focus:ring-blue-600 dark:focus:border-blue-600 transition-colors"
  />
</template>
