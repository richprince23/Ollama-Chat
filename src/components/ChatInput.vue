<template>
    <div class="flex-shrink-0">
      <textarea
        class="w-full px-6 py-4 bg-slate-100 active:bg-white focus:bg-white rounded-xl resize-none"
        :placeholder="`Chat with ${model}`"
        :value="prompt"
        @input="updateValue"
        @keyup.enter.prevent="onEnterPress"
      ></textarea>
    </div>
  </template>
  
  <style scoped>
  textarea {
    min-height: 60px; /* Adjust this value as needed */
  }
  </style>
  
  <script setup>
  import { ref } from 'vue';
  
  const props = defineProps({
    prompt: {
      type: String,
      default: ""
    },
    model: {
      type: String,
      default: "deepseek-coder"
    }
  });
  
  const emit = defineEmits(['update:prompt', 'send']);
  
  
  const updateValue = (event) => {
    emit('update:prompt', event.target.value);
  };


  const onEnterPress = (event) => {
  if (!event.shiftKey) {
    event.preventDefault();
    emit('send');
  }
};
  </script>