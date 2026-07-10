<template>
  <button @click.stop.prevent="copyRaw" :disabled="copying">
    <SpinnerIcon v-show="copying" />
    <DocumentDuplicateIcon v-show="!copying && !copied" class="w-4 h-4 mr-2" />
    <CheckIcon v-show="!copying && copied" class="w-4 h-4 mr-2 text-brand-500" />
    <span v-show="!copied">Copy raw content</span>
    <span v-show="copied" class="text-brand-500">Copied!</span>
  </button>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { DocumentDuplicateIcon, CheckIcon } from '@heroicons/vue/24/outline';
import SpinnerIcon from './SpinnerIcon.vue';
import { copyToClipboard } from '../helpers.js';

const props = defineProps({
  identifier: {
    type: String,
    required: true,
  },
});

const copying = ref(false);
const copied = ref(false);

const writeClipboard = async (text) => {
  if (navigator.clipboard && window.isSecureContext) {
    await navigator.clipboard.writeText(text);
  } else {
    copyToClipboard(text);
  }
};

const copyRaw = async () => {
  if (copying.value) return;
  copying.value = true;

  try {
    const { data } = await axios.get(`${window.LogViewer.basePath}/api/files/${props.identifier}/raw`, {
      responseType: 'text',
      transformResponse: [(value) => value],
    });

    await writeClipboard(typeof data === 'string' ? data : String(data ?? ''));

    copied.value = true;
    setTimeout(() => (copied.value = false), 2000);
  } catch (error) {
    console.log(error);
    const detail = error.response?.data?.message || error.message;
    alert(`Could not copy the log contents: ${detail}. Check the developer console for more info.`);
  } finally {
    copying.value = false;
  }
};
</script>
