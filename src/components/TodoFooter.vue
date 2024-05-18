<template>
   <div class="todo-footer" >
    <label>
      <input type="checkbox" :checked="isAll" @click="checkAll"/>
    </label>
    <span>
      <span>已完成 ({{ autoClickAll }})</span> / 全部 ({{ total }})
    </span>
    <button class="btn btn-danger" @click="$emit('deleteCompleted')">清除已完成任务</button>
  </div>
</template>

<script setup lang="ts">
import { ref,defineProps } from 'vue'
import emitter from '@/utils/emitter'

let isAll = ref(false)

function checkAll(){
  isAll.value = !isAll.value
  emitter.emit("ischeckAll",isAll.value)
}
emitter.on('autoIsTrue',(val:any) => {
    isAll.value = val
})

defineProps(['autoClickAll','total'])
</script>

<style scoped>
/*footer*/
.todo-footer {
    height: 40px;
    line-height: 40px;
    padding-left: 6px;
    margin-top: 5px;
}

.todo-footer label {
    display: inline-block;
    margin-right: 20px;
    cursor: pointer;
}

.todo-footer label input {
    position: relative;
    top: -1px;
    vertical-align: middle;
    margin-right: 5px;
}

.todo-footer button {
    float: right;
    margin-top: 5px;
}</style>