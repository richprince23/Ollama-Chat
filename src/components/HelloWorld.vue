<script setup>
import { onMounted } from 'vue';

defineProps({
  msg: {
    type: String,
    required: true
  }
})

onMounted(()=>{


})

</script>

<template>
  <div class="greetings mx-auto">
    <h1 class="green text-center">{{ msg }}</h1>
    <h3 class="text-center">A simple chat interface for locally-served LLMs (Without Internet 😉)</h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
