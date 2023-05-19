<template>
    <div>
    <v-card :style="{ backgroundColor: '255, 252, 249' }" title="Prompt" :text="prompt" />
    <v-card :style="{ backgroundColor: stat ? 'rgb(211, 63, 73)' : 'rgb(95, 187, 151)' }" :title="stat ? 'Urgent' : 'Not Urgent'"
    :text="reason" />
</div>
  </template>
  
  <script setup>
import { defineProps } from 'vue';

const props = defineProps({
  stat: {
    type: Boolean,
    required: true,
  },
  reason: {
    type: String,
    required: true,
  },
  prompt: {
    type: String,
    required: true,
  },
});
</script>