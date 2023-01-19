<script setup lang="ts">
import { ref } from 'vue';

let arr = ref([1, 2, 3, 4, 5])
let arr_drop = ref<number[]>([])
let draggingElem = ref<HTMLElement>()
let classDragover = ref("")

function handleDragStart(e: DragEvent, id: number) {
  let elem = e.target as HTMLElement
  elem.style.opacity = '0.4'
  elem.classList.add('targetSelect')
  draggingElem.value = elem
  e.dataTransfer!.effectAllowed = 'copy'
  e.dataTransfer?.setData("id", id.toString())
}
function handleDragEnd(e: DragEvent) {
  let elem = e.target as HTMLElement
  elem.style.opacity = '1'
  elem.classList.remove('targetSelect')

  classDragover.value = ""
}
function handleDragOver(e: DragEvent) {
  e.preventDefault()
}
function handleDrop(e: DragEvent) {
  e.stopPropagation()
  const elem = e.target as HTMLElement
  console.log("handleDrop", e.target)
  let data = e.dataTransfer?.getData("id")
  arr_drop.value.push(data)
}
function handleDragEnter(e: DragEvent) {
  const elem = e.target as HTMLElement
  classDragover.value = "dragover"
}
function handleDragLeaveAtDropzone(e: DragEvent) {
  console.log("handleDragLeaveAtDropzone")
  classDragover.value = ""
}
</script>

<template>
  <p>arr_drop:{{ arr_drop }}</p>
  <div class="grid grid-cols-2 gap-2 h-full">
    <div class="w-full bg-slate-400">
      <div class="item drag-item" v-for="i in arr" draggable="true" @dragstart="handleDragStart($event, i)"
        @dragend="handleDragEnd" @dragover="handleDragOver">
        Parts {{ i }}
      </div>
    </div>
    <div class="dropzone" :class="classDragover" @dragover="handleDragOver" @dragenter="handleDragEnter"
      @dragleave="handleDragLeaveAtDropzone" @drop="handleDrop">
      <div class="item" v-for="i in arr_drop">
        {{ i }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.item {
  @apply bg-white m-2 p-2 h-12 drop-shadow
}

.drag-item {
  @apply cursor-move
}

.targetSelect {
  @apply bg-orange-400;
}

.dropzone {
  @apply w-full bg-slate-300;
}

.dropzone.dragover {
  @apply bg-slate-400;
}
</style>
