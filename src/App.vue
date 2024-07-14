<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'

</script>

<template>
  <header class="px-8 py-4 flex justify-center">
    <img alt="Vue logo" class="logo mr-4" src="/public/favicon.png" width="96" height="96" />
    <div class="wrapper">
      <HelloWorld msg="Ollama Chat" />
    </div>
  </header>
 
  <RouterView class="px-8"/>
</template>

