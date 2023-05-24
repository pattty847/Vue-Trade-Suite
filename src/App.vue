<script setup lang="js">
import { ref, reactive } from 'vue'
import Chart from './components/Chart.vue'
import Orderbook from './components/Orderbook.vue'

const layout = reactive([
  { x: 0, y: 0, w: 2, h: 2, i: '0', component: Chart},
  { x: 2, y: 0, w: 2, h: 2, i: '1', component: Orderbook},
  { x: 4, y: 0, w: 2, h: 2, i: '2' },
  { x: 6, y: 0, w: 2, h: 2, i: '3' },
  { x: 8, y: 0, w: 2, h: 2, i: '4' }
])

const activeComponents = reactive([])

let index = layout.length
const draggable = ref(true)
const resizable = ref(true)
const colNum = ref(12)

function addItem() {
  layout.push({
    x: (layout.length * 2) % (colNum.value || 12),
    y: layout.length + (colNum.value || 12), // puts it at the bottom
    w: 2,
    h: 2,
    i: `${index++}`
  })
}

function removeItem(id) {
  const index = layout.findIndex(item => item.i === id)

  if (index > -1) {
    layout.splice(index, 1)
  }
}
</script>

<template>
  <!-- <div class="layout-json">
    Displayed as <code>[x, y, w, h]</code>:
    <div class="columns">
      <div v-for="item in layout" :key="item.i" class="layout-item">
        <b>{{ item.i }}</b>: [{{ item.x }}, {{ item.y }}, {{ item.w }}, {{ item.h }}]
      </div>
    </div>
  </div> -->
  <button type="button" @click="addItem">
    Add an item dynamically
  </button>
  <input v-model="draggable" type="checkbox" /> Draggable
  <input v-model="resizable" type="checkbox" /> Resizable
  <GridLayout
    v-model:layout="layout"
    :col-num="colNum"
    :row-height="30"
    :is-draggable="draggable"
    :is-resizable="resizable"
  >
    <template #item="{ item }">
      <component :is="item.component"></component>
      <span class="remove" @click="removeItem(item.i)">x</span>
    </template>
  </GridLayout>
</template>

<style scoped>
.vgl-layout {
  background-color: #eee;
}

:deep(.vgl-item:not(.vgl-item--placeholder)) {
  background-color: #ccc;
  border: 1px solid black;
  overflow: auto;
}

:deep(.vgl-item--resizing) {
  opacity: 90%;
}

:deep(.vgl-item--static) {
  background-color: #cce;
}

.text {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  margin: auto;
  font-size: 24px;
  text-align: center;
}

.layout-json {
  padding: 10px;
  margin-top: 10px;
  background-color: #ddd;
  border: 1px solid black;
}

.columns {
  columns: 120px;
}

.remove {
  position: absolute;
  top: 0;
  right: 2px;
  cursor: pointer;
}
</style>