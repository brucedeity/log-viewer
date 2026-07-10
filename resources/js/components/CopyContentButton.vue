<template>
  <button
    @click.stop.prevent="copy"
    :title="copied ? 'Copied!' : 'Copy raw content'"
    class="inline-flex items-center px-2 py-1 rounded-md text-xs font-medium shadow-sm backdrop-blur border transition-colors
           bg-white/80 border-gray-200 text-gray-500 hover:text-brand-600 hover:border-brand-500
           dark:bg-gray-700/80 dark:border-gray-600 dark:text-gray-300 dark:hover:text-brand-400 dark:hover:border-brand-500"
  >
    <CheckIcon v-if="copied" class="w-4 h-4 text-brand-500" />
    <ClipboardDocumentIcon v-else class="w-4 h-4" />
    <span class="ml-1 hidden sm:inline" :class="{ 'text-brand-500': copied }">{{ copied ? 'Copied!' : 'Copy' }}</span>
  </button>
</template>

<script setup>
import { ref } from 'vue';
import { ClipboardDocumentIcon, CheckIcon } from '@heroicons/vue/24/outline';
import { copyToClipboard } from '../helpers.js';

const props = defineProps({
  text: {
    type: String,
    default: '',
  },
});

const copied = ref(false);

const writeClipboard = async (text) => {
  if (navigator.clipboard && window.isSecureContext) {
    await navigator.clipboard.writeText(text);
  } else {
    copyToClipboard(text);
  }
};

const copy = async () => {
  try {
    await writeClipboard(props.text ?? '');
    copied.value = true;
    setTimeout(() => (copied.value = false), 1500);
  } catch (error) {
    console.log(error);
  }
};
</script>
