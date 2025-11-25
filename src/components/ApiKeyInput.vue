<script setup>
import { ref, onMounted } from 'vue';
import { Key } from 'lucide-vue-next';

const apiKey = ref('');
const emit = defineEmits(['update:apiKey']);

onMounted(() => {
  const storedKey = localStorage.getItem('gemini_api_key');
  if (storedKey) {
    apiKey.value = storedKey;
    emit('update:apiKey', storedKey);
  }
});

const saveKey = () => {
  localStorage.setItem('gemini_api_key', apiKey.value);
  emit('update:apiKey', apiKey.value);
};
</script>

<template>
  <div class="flex items-center space-x-2 bg-white/10 backdrop-blur-md p-3 rounded-lg border border-white/20 shadow-sm">
    <Key class="w-5 h-5 text-yellow-400" />
    <input 
      type="password" 
      v-model="apiKey" 
      @input="saveKey"
      placeholder="Enter Gemini API Key" 
      class="bg-transparent border-none outline-none text-white placeholder-gray-400 flex-1 text-sm"
    />
  </div>
</template>
