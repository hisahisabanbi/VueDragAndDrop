<script setup lang="ts">
import { ref } from 'vue';

let arr = ref([1, 2, 3, 4, 5])
let arr_drop = ref<string[]>([])
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
  let data = e.dataTransfer?.getData("id") as string
  if (data) {
    arr_drop.value.push(data)
  }
}
function handleDragEnter(e: DragEvent) {
  const elem = e.target as HTMLElement
  classDragover.value = "dragover"
}
function handleDragLeaveAtDropzone(e: DragEvent) {
  // console.log("handleDragLeaveAtDropzone")
  // classDragover.value = ""
}

let sortItemIndex: number | undefined = undefined
function handleDragStartForSort(e: DragEvent, id: string, index: number) {
  let elem = e.target as HTMLElement
  elem.style.opacity = '0.4'
  elem.classList.add('sortTargetSelect')
  draggingElem.value = elem
  e.dataTransfer!.effectAllowed = 'move'
  e.dataTransfer?.setData("sort-id", id)
  sortItemIndex = index
}
function handleDragEndForSort(e: DragEvent) {
  let elem = e.target as HTMLElement
  elem.style.opacity = '1'
  elem.classList.remove('sortTargetSelect')
  sortItemIndex = undefined
  document.querySelectorAll(".sort-item").forEach(item => {
    item.classList.remove("over")
  })
}
function handleDragOverForSort(e: DragEvent) {
  e.preventDefault()
}
function handleDragEnterForSort(e: DragEvent) {
  console.log("handleDragEnterForSort")
  const elem = e.target as HTMLElement
  elem.classList.add("over")
}
function handleDragLeaveForSort(e: DragEvent) {
  const elem = e.target as HTMLElement
  elem.classList.remove("over")
}
function handleDragDropForSort(e: DragEvent, index: number) {
  e.stopPropagation()
  let data = e.dataTransfer?.getData("sort-id") as string
  if (data != "") {
    // 配列をソート
    arr_drop.value.splice(sortItemIndex!, 1)
    console.log("slice arr_drop.value", arr_drop.value)
    arr_drop.value.splice(index, 0, data)
    sortItemIndex = undefined
  }
  return false;
}
</script>

<template>
  <p>arr_drop:{{ arr_drop }}</p>
  <div class="grid grid-cols-4 gap-2 h-full">
    <div class="col-span-1 bg-slate-400">
      <div class="item drag-item" v-for="i in arr" draggable="true" @dragstart="handleDragStart($event, i)"
        @dragend="handleDragEnd" @dragover="handleDragOver">
        Parts {{ i }}
      </div>
    </div>
    <div class="col-span-3 dropzone" :class="classDragover" @dragover="handleDragOver" @dragenter="handleDragEnter"
      @dragleave="handleDragLeaveAtDropzone" @drop="handleDrop">
      <div class="item sort-item" v-for="(i, index) in arr_drop" draggable="true"
        @dragstart="handleDragStartForSort($event, i, index)" @dragend="handleDragEndForSort"
        @dragover="handleDragOverForSort" @dragenter="handleDragEnterForSort" @dragleave="handleDragLeaveForSort"
        @drop="handleDragDropForSort($event, index)">
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

.sort-item {
  @apply cursor-move
}

.sort-item.over {
  border: 3px dotted #666;
}

.targetSelect {
  @apply bg-orange-400;
}

.sortTargetSelect {
  @apply bg-blue-400;
}

.dropzone {
  @apply w-full bg-slate-300;
}

.dropzone.dragover {
  @apply bg-slate-400;
}
</style>
