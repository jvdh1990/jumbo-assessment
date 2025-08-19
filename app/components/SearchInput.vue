<template>
    <input type="text" placeholder="Search stores by name or city..." :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
        class="border p-2 rounded-3xl w-full md:w-[500px] text-lg px-4 mb-4" />
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
    modelValue: String
})

const emit = defineEmits(['update:modelValue'])
</script>
