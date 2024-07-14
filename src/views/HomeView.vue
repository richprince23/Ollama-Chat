<template>
  <main class="h-screen flex flex-col">
    <TheWelcome class="text-5xl"/>
    <section class="flex-grow flex flex-col overflow-hidden">
      <div class="mb-4">
        <label for="">Select AI Model  </label>
        <select v-model="selectedModel" class="w-64 px-4 py-2 border rounded-lg">
          <option value="">Select a model</option>
          <option v-for="model in models" :key="model" :value="model">
            {{ model }}
          </option>
        </select>
      </div>
      <ChatContent class="flex-grow overflow-y-auto h-96" :content="`${content}`" />
      <div class="flex flex-row mt-4 p-4 bg-white">
        <ChatInput class="flex-grow" v-model:prompt="prompt" :model="`deepseek-coder`" @send="sendMessage" id="input"/>
        <button type="button" id="sendBtn" @click="sendMessage" class="ml-4 bg-black text-white px-8 hover:bg-white hover:border-2 border-blue-500 hover:text-blue-500 rounded-xl">Send</button>
      </div>
    </section>
    <p class="italic text-gray-600 text-center p-2">Created with love by Richard Kweku Aikins ❤️</p>
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  height: 86vh;
}

section {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
</style>

<script setup>
import { ref , onMounted} from 'vue';
import TheWelcome from '../components/TheWelcome.vue';
import ChatInput from '@/components/ChatInput.vue';
import ChatContent from '@/components/ChatContent.vue';


const prompt = ref("");
const content = ref("");

const chat = ref([]);


const models = ref([]);
const selectedModel = ref('');

// Fetch models when component mounts
onMounted(async () => {
  try {
    const response = await fetch('http://localhost:11434/api/tags');
    const data = await response.json();
    models.value = data.models.map(model => model.name);
  } catch (error) {
    console.error('Error fetching models:', error);
  }
});

const sendMessage = async () => {
  if (prompt.value.trim() !== "") {
    // Add user's message to chat
    chat.value.push({ role: "user", content: prompt.value });
    updateContent();

    try {
      const response = await fetch('http://localhost:11434/api/chat', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          // model: "llama3",
          model: selectedModel.value ?? "llama3",
          messages: [
            {
              role: "user",
              content: prompt.value
            }
          ]
        })
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const reader = response.body.getReader();
      let fullResponse = '';
      prompt.value = "";
      // Add an initial empty assistant message
      chat.value.push({ role: "assistant", content: "" });

      while (true) {
        const { done, value } = await reader.read();
        if (done) break;
        
        const chunk = new TextDecoder().decode(value);
        const lines = chunk.split('\n');

        for (const line of lines) {
          if (line.trim() !== '') {
            try {
              const jsonResponse = JSON.parse(line);
              if (jsonResponse.message && jsonResponse.message.content) {
                fullResponse += jsonResponse.message.content;
                // Update the last message in chat (which is the assistant's response)
                chat.value[chat.value.length - 1].content = fullResponse;
                updateContent();
              }
            } catch (parseError) {
              console.error('JSON Parse Error:', parseError, 'on line:', line);
            }
          }
        }
      }

      // Final update after stream is complete
      chat.value[chat.value.length - 1].content = fullResponse;
      updateContent();

    } catch (error) {
      console.error('Error:', error);
      chat.value.push({ role: "system", content: "Sorry, there was an error processing your request." });
      updateContent();
    }

    prompt.value = "";
  }
};

const updateContent = () => {
  content.value = chat.value.map(message => {
    if (message.role === "user") {
      return `User: ${message.content}`;
    } else if (message.role === "assistant") {
      return `Assistant: ${message.content}`;
    } else {
      return `${message.role}: ${message.content}`;
    }
  }).join('\n\n');  // Add an extra newline between messages for better readability
};
</script>
