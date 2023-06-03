<script lang="ts" setup>
import { Upload, Warn, Trash } from '../assets/icons'
import { bytesToSize } from '../utils/transform'
import { reactive, watch } from 'vue';

type InputState = {
  file: null | File;
  preview: string;
  errorOnImageLoad: boolean
}

const props = defineProps(['modelValue'])
const emits = defineEmits(['update:modelValue'])

const INITIAL_INPUT_STATE = {
  file: props.modelValue,
  preview: "",
  errorOnImageLoad: false
}

const inputState = reactive<InputState>(INITIAL_INPUT_STATE)
const fileList = reactive<FileList[]>([]);

function onFileUpload(event: Event) {
  if (!event.target) return;
  const inputFiles = (event.target as any).files;
  fileList.pop();
  fileList.push(inputFiles);
}

function showPreview(file: File | null) {
  if (file && fileList.length) return URL.createObjectURL(file);
  return "";
};

function onImageError() {
  inputState.preview = ""
  inputState.errorOnImageLoad = true
}

function onImageLoad() {
  inputState.errorOnImageLoad = false
}

function holdOnFile() {
  fileList.pop()
  inputState.errorOnImageLoad = false
}

watch(fileList, () => {
  if (fileList.length) {
    const firstFileList = fileList[0];
    inputState.file = firstFileList[0];
  }
  else inputState.file = null;

  inputState.preview = showPreview(inputState.file)
  emits("update:modelValue", inputState.file)
})
</script>

<template>
  <div class="file-input--container">
    <div class="file-input--warning" v-if="inputState.errorOnImageLoad">
      <Warn />
      Preview indisponível
    </div>

    <img v-if="inputState.preview" :src="inputState.preview" @error="onImageError" @load="onImageLoad"
      alt="Image Preview" />

    <input type="file" id="file-input" @input="onFileUpload">

    <div v-if="inputState.file" class="file-input--info">
      <p><b>Nome:</b> {{ inputState.file?.name }}</p>
      <p><b>Tamanho:</b> {{ bytesToSize(inputState.file?.size) }}</p>
    </div>

    <button v-if="inputState.file" class="file-input--hold-on" @click="holdOnFile">
      <Trash />
    </button>

    <label for="file-input" class="file-input--dispatch">
      <span class="file-input--dispatch-button">
        <Upload />
        Selecione o arquivo
      </span>
    </label>
  </div>
</template>


<style lang="scss" scoped>
.file-input--container {
  display: flex;
  flex-direction: column;
  width: 250px;
  border: 1px solid #606359;
  border-radius: 6px;
  overflow: hidden;
  position: relative;
}

.file-input--dispatch-button {
  display: flex;
  width: 100%;
  place-items: center;
  gap: 1rem;
  font-size: 1.25rem;
  padding: .675rem 1.25rem;
  background-color: #31322e;
  border: 0;
  cursor: pointer;
  border-top: 1px solid #62625e;

  &:hover {
    filter: brightness(1.1);
  }
}

.file-input--hold-on {
  display: flex;
  place-items: center;
  justify-content: center;
  width: 30px;
  height: 30px;

  font-size: 1.25rem;
  background-color: #31322e;
  cursor: pointer;
  border-radius: 100%;
  border: 0;

  position: absolute;
  right: 10px;
  top: 10px;


  &:hover {
    filter: brightness(1.1);
  }
}

.file-input--warning {
  display: grid;
  place-items: center;
  padding: 1rem;
  gap: .5rem;
  color: #f2edb7;
}

input#file-input {
  opacity: 0;
  position: absolute;
}

.file-input--info {
  padding: .8rem;
  width: 100%;
  border-top: 1px solid #4c4c49;

  p {
    width: 200px;
    padding: 0;
    margin: 0;
    overflow: hidden;
    text-overflow: ellipsis;
  }
}
</style>