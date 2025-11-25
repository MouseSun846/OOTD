<script setup>
import { ref } from 'vue';
import { Shirt, User, CheckCircle } from 'lucide-vue-next';

const props = defineProps(['modelValue']);
const emit = defineEmits(['update:modelValue']);

const styles = [
  {
    id: 'styleA',
    title: 'OOTD Collage',
    description: 'Fashion magazine style with item breakdown',
    icon: Shirt,
    color: 'from-pink-500 to-rose-500'
  },
  {
    id: 'styleB',
    title: 'Character Sheet',
    description: 'Concept art style with detailed deconstruction',
    icon: User,
    color: 'from-blue-500 to-indigo-500'
  }
];

const selectStyle = (id) => {
  emit('update:modelValue', id);
};
</script>

<template>
  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
    <div 
      v-for="style in styles" 
      :key="style.id"
      @click="selectStyle(style.id)"
      :class="[
        'relative p-4 rounded-xl border-2 cursor-pointer transition-all duration-300 overflow-hidden group',
        modelValue === style.id ? 'border-transparent bg-gray-800' : 'border-gray-700 bg-gray-900 hover:border-gray-600'
      ]"
    >
      <!-- Active Border Gradient -->
      <div v-if="modelValue === style.id" class="absolute inset-0 bg-gradient-to-r opacity-20" :class="style.color"></div>
      <div v-if="modelValue === style.id" class="absolute inset-0 border-2 rounded-xl border-transparent bg-gradient-to-r" :class="style.color" style="mask: linear-gradient(#fff 0 0) padding-box, linear-gradient(#fff 0 0); -webkit-mask-composite: xor; mask-composite: exclude;"></div>

      <div class="relative flex items-start space-x-4 z-10">
        <div :class="['p-3 rounded-lg bg-gradient-to-br shadow-lg', style.color]">
          <component :is="style.icon" class="w-6 h-6 text-white" />
        </div>
        <div class="flex-1">
          <h3 class="font-bold text-white text-lg">{{ style.title }}</h3>
          <p class="text-gray-400 text-sm mt-1">{{ style.description }}</p>
        </div>
        <div v-if="modelValue === style.id" class="text-green-400">
          <CheckCircle class="w-6 h-6" />
        </div>
      </div>
    </div>
  </div>
</template>
