<script setup>
import { computed } from 'vue';
import { Loader2, Download, RefreshCw } from 'lucide-vue-next';

const props = defineProps(['result', 'loading', 'error']);
const emit = defineEmits(['reset']);

const thinkingText = computed(() => {
  if (!props.result) return '';
  // Try to find thinking text in candidates
  // The structure depends on the API response.
  // Usually candidates[0].content.parts
  // Thinking parts usually have a specific role or just text.
  // For now let's just dump the text parts.
  try {
    const parts = props.result.candidates[0].content.parts;
    return parts.filter(p => p.text).map(p => p.text).join('\n\n');
  } catch (e) {
    return '';
  }
});

const imageUrl = computed(() => {
  if (!props.result) return null;
  // Try to find image in candidates
  // Inline data is usually in parts
  // But the SDK might return it differently?
  // Actually, the SDK returns `inlineData` in parts if it's an image?
  // Wait, the model generates an image.
  // The response structure for generated image:
  // It might be a base64 string in inlineData.
  try {
    // Check if there are any parts with inlineData
    // Note: The Gemini API for image generation usually returns the image as a part.
    // Let's look at the guide example:
    // for part in response.candidates[0].content.parts:
    //     if image := part.inline_data:
    //         ...
    // So we need to look for inlineData.
    const parts = props.result.candidates[0].content.parts;
    const imagePart = parts.find(p => p.inlineData);
    if (imagePart) {
      return `data:${imagePart.inlineData.mimeType};base64,${imagePart.inlineData.data}`;
    }
  } catch (e) {
    console.error("Error parsing image:", e);
  }
  return null;
});

const downloadImage = () => {
  if (!imageUrl.value) return;
  const link = document.createElement('a');
  link.href = imageUrl.value;
  link.download = 'ootd-generated.png';
  link.click();
};
</script>

<template>
  <div class="w-full">
    <!-- Loading State -->
    <div v-if="loading" class="flex flex-col items-center justify-center py-12 space-y-4">
      <div class="relative">
        <div class="w-16 h-16 border-4 border-yellow-400/30 border-t-yellow-400 rounded-full animate-spin"></div>
        <div class="absolute inset-0 flex items-center justify-center">
          <Loader2 class="w-8 h-8 text-yellow-400 animate-pulse" />
        </div>
      </div>
      <p class="text-gray-300 font-medium animate-pulse">Designing your look...</p>
      <p class="text-gray-500 text-sm">This might take a moment to "think"</p>
    </div>

    <!-- Error State -->
    <div v-else-if="error" class="bg-red-500/10 border border-red-500/50 rounded-xl p-6 text-center">
      <p class="text-red-400 font-medium mb-2">Something went wrong</p>
      <p class="text-gray-400 text-sm mb-4">{{ error.message }}</p>
      <button @click="$emit('reset')" class="text-white bg-red-600 hover:bg-red-700 px-4 py-2 rounded-lg text-sm transition-colors">
        Try Again
      </button>
    </div>

    <!-- Result State -->
    <div v-else-if="result" class="space-y-6 animate-fade-in">
      <!-- Image Display -->
      <div v-if="imageUrl" class="relative group rounded-xl overflow-hidden shadow-2xl border border-white/10">
        <img :src="imageUrl" class="w-full h-auto" />
        <div class="absolute top-4 right-4 opacity-0 group-hover:opacity-100 transition-opacity">
          <button @click="downloadImage" class="bg-black/50 hover:bg-black/70 backdrop-blur-md text-white p-2 rounded-lg flex items-center space-x-2">
            <Download class="w-5 h-5" />
            <span class="text-sm font-medium">Download</span>
          </button>
        </div>
      </div>

      <!-- Thinking Process / Text -->
      <div v-if="thinkingText" class="bg-gray-900/50 rounded-xl p-6 border border-white/5">
        <h3 class="text-gray-400 text-xs uppercase tracking-wider font-bold mb-3">Model Thoughts</h3>
        <div class="prose prose-invert prose-sm max-w-none text-gray-300 whitespace-pre-wrap font-mono text-xs">
          {{ thinkingText }}
        </div>
      </div>

      <div class="flex justify-center pt-4">
        <button @click="$emit('reset')" class="flex items-center space-x-2 text-gray-400 hover:text-white transition-colors">
          <RefreshCw class="w-4 h-4" />
          <span>Generate Another</span>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.animate-fade-in {
  animation: fadeIn 0.5s ease-out;
}
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
