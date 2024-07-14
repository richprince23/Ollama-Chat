<template>
  <section class="bg-slate-50 rounded-xl p-4 overflow-y-auto flex-grow">
    <div class="overflow-y-auto flex-grow" v-html="markdown.render(content)"> </div>
  </section>
</template>


<script setup>
import MarkdownIt from "markdown-it";

const markdown = new MarkdownIt();

defineProps({
  content: {
    type: String,
    default: ""
  }
});
</script>

<style scoped>
section {
  max-height: calc(100vh - 200px); /* Adjust this value as needed */
}
</style>