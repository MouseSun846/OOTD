<script setup>
import { ref } from 'vue';
import { UploadCloud, Image as ImageIcon, X } from 'lucide-vue-next';

const emit = defineEmits(['file-selected']);
const previewUrl = ref(null);
const isDragging = ref(false);

const handleFile = (file) => {
  if (file && file.type.startsWith('image/')) {
    previewUrl.value = URL.createObjectURL(file);
    emit('file-selected', file);
  }
};

const onDrop = (e) => {
  isDragging.value = false;
  const file = e.dataTransfer.files[0];
  handleFile(file);
};

const onFileSelect = (e) => {
  const file = e.target.files[0];
  handleFile(file);
};

const clearImage = () => {
  previewUrl.value = null;
  emit('file-selected', null);
};
</script>

<template>
  <div class="w-full">
    <div 
      v-if="!previewUrl"
      @dragover.prevent="isDragging = true"
      @dragleave.prevent="isDragging = false"
      @drop.prevent="onDrop"
      :class="[
        'border-2 border-dashed rounded-xl p-8 transition-all duration-300 flex flex-col items-center justify-center cursor-pointer group',
        isDragging ? 'border-yellow-400 bg-yellow-400/10' : 'border-gray-600 hover:border-yellow-400 hover:bg-white/5'
      ]"
      @click="$refs.fileInput.click()"
    >
      <input type="file" ref="fileInput" class="hidden" accept="image/*" @change="onFileSelect" />
      <div class="bg-gray-800 p-4 rounded-full mb-4 group-hover:scale-110 transition-transform">
        <UploadCloud class="w-8 h-8 text-yellow-400" />
      </div>
      <p class="text-gray-300 font-medium">Click or drag image here</p>
      <p class="text-gray-500 text-sm mt-1">Supports JPG, PNG, WEBP</p>
    </div>

    <div v-else class="relative group rounded-xl overflow-hidden border border-white/10 shadow-lg">
      <img :src="previewUrl" class="w-full h-auto object-contain max-h-[80vh]" />
      <div class="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center">
        <button @click="clearImage" class="bg-red-500 hover:bg-red-600 text-white p-2 rounded-full transform scale-90 group-hover:scale-100 transition-all">
          <X class="w-5 h-5" />
        </button>
      </div>
    </div>
  </div>
</template>
