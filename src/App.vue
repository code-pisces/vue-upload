<script setup lang="ts">
import { ref } from 'vue';
import FileUpload from './components/FileUpload.vue';
const file = ref<null | File>(null)
</script>

<template>
  <FileUpload v-model="file" />
  <output>
    <strong>V-Model Output: </strong>
    {{ JSON.stringify(file)  }}
  </output>
</template>