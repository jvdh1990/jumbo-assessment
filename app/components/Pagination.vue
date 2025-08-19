<template>
  <div class="flex gap-2 justify-between items-center my-4 mt-7">
    <button @click="$emit('update:page', currentPage - 1)" :disabled="currentPage <= 1"
      class="px-3 py-1 border rounded-3xl text-xl disabled:opacity-50">
      Prev
    </button>
    <span class="text-lg">Page {{ currentPage }} of {{ totalPages }}</span>
    <button @click="$emit('update:page', currentPage + 1)" :disabled="currentPage >= totalPages"
      class="px-3 py-1 border rounded-3xl text-xl disabled:opacity-50">
      Next
    </button>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  currentPage: Number,
  totalPages: Number
})

const emit = defineEmits(['update:page'])
</script>

<style lang="scss" scoped>
button {
  transition: .2s ease-in-out;

  &:not(:disabled):hover {
    border-color: black;
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  }
}
</style>