<template>
    <li>
    <label>
      <!-- 如下代码也能实现功能，但是不太推荐，因为有点违反原则，因为修改了props -->
      <!-- <input type="checkbox" v-model="todo.done"/> -->
      <input type="checkbox" :checked="done == true"/> &nbsp;
      <span>{{ title }}</span>
    </label>
    <button class="btn btn-danger">删除</button>
  </li>
</template>

<script setup lang="ts">
import { defineProps,toRefs } from 'vue'
// 引入mitt
import emitter from '@/utils/emitter'
// 接收数据
let props = defineProps(['data'])
// 结构赋值
let {data:{item:{id,title,done},index}} = props

emitter.emit("sendData")




</script>

<style scoped>
/*item*/
li {
    list-style: none;
    height: 36px;
    line-height: 36px;
    padding: 0 5px;
    border-bottom: 1px solid #ddd;
}

li label {
    float: left;
    cursor: pointer;
}

li label li input {
    vertical-align: middle;
    margin-right: 6px;
    position: relative;
    top: -1px;
}

li button {
    float: right;
    display: none;
    margin-top: 3px;
}

li:before {
    content: initial;
}

li:last-child {
    border-bottom: none;
}

li:hover {
    background-color: #ddd;
}

li:hover button {
    display: block;
}</style>