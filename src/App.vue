<script setup>
import { ref } from 'vue';
import { Sparkles } from 'lucide-vue-next';
import ApiKeyInput from './components/ApiKeyInput.vue';
import ImageUploader from './components/ImageUploader.vue';
import StyleSelector from './components/StyleSelector.vue';
import ResultDisplay from './components/ResultDisplay.vue';
import { generateOOTD } from './services/gemini';

const apiKey = ref('');
const selectedFile = ref(null);
const selectedStyle = ref('styleA');
const loading = ref(false);
const result = ref(null);
const error = ref(null);

const handleFileSelect = (file) => {
  selectedFile.value = file;
  result.value = null;
  error.value = null;
};

const handleGenerate = async () => {
  if (!apiKey.value) {
    error.value = { message: "Please enter your Gemini API Key first." };
    return;
  }
  if (!selectedFile.value) {
    error.value = { message: "Please upload an image." };
    return;
  }

  loading.value = true;
  error.value = null;
  result.value = null;

  try {
    const response = await generateOOTD(apiKey.value, selectedFile.value, selectedStyle.value);
    result.value = response;
  } catch (err) {
    console.error(err);
    error.value = { message: err.message || "Failed to generate content. Please check your API key and try again." };
  } finally {
    loading.value = false;
  }
};

const reset = () => {
  result.value = null;
  error.value = null;
  // Keep file and key for convenience
};
</script>

<template>
  <div class="min-h-screen bg-black text-white selection:bg-yellow-400 selection:text-black font-sans">
    <!-- Background Gradient -->
    <div class="fixed inset-0 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-gray-800 via-black to-black -z-10"></div>

    <div class="container mx-auto px-4 py-8 max-w-4xl">
      <!-- Header -->
      <header class="flex flex-col md:flex-row items-center justify-between mb-12 space-y-4 md:space-y-0">
        <div class="flex items-center space-x-3">
          <div class="bg-yellow-400 p-2 rounded-lg">
            <Sparkles class="w-6 h-6 text-black" />
          </div>
          <div>
            <h1 class="text-2xl font-bold tracking-tight">Nano Banana OOTD</h1>
            <p class="text-gray-400 text-sm">Powered by Gemini 3 Pro</p>
          </div>
        </div>
        <ApiKeyInput v-model:apiKey="apiKey" />
      </header>

      <main class="space-y-8">
        <!-- Input Section -->
        <div v-if="!result && !loading" class="space-y-8 animate-fade-in">
          <section>
            <h2 class="text-lg font-semibold mb-4 text-gray-200">1. Upload your photo</h2>
            <ImageUploader @file-selected="handleFileSelect" />
          </section>

          <section v-if="selectedFile">
            <h2 class="text-lg font-semibold mb-4 text-gray-200">2. Choose your style</h2>
            <StyleSelector v-model="selectedStyle" />
          </section>

          <div v-if="selectedFile" class="flex justify-center pt-4">
            <button 
              @click="handleGenerate"
              class="bg-yellow-400 hover:bg-yellow-300 text-black font-bold py-4 px-12 rounded-full text-lg shadow-lg shadow-yellow-400/20 transform hover:scale-105 transition-all duration-300 flex items-center space-x-2"
            >
              <Sparkles class="w-5 h-5" />
              <span>Generate OOTD</span>
            </button>
          </div>
        </div>

        <!-- Result Section -->
        <ResultDisplay 
          :result="result" 
          :loading="loading" 
          :error="error" 
          @reset="reset" 
        />
      </main>

      <footer class="mt-24 text-center text-gray-600 text-sm pb-8">
        <p>Built with Vue 3 + Tailwind CSS + Gemini API</p>
      </footer>
    </div>
  </div>
</template>

<style>
body {
  margin: 0;
  font-family: 'Inter', sans-serif;
}
</style>
