<template>
    <li>
    <label>
      <!-- 如下代码也能实现功能，但是不太推荐，因为有点违反原则，因为修改了props -->
      <!-- <input type="checkbox" v-model="todo.done"/> -->
      <input type="checkbox" :checked="item.done" @click="changeCheck"/> &nbsp;
      <span>{{ item.title }}</span>
    </label>
    <button class="btn btn-danger" @click="del">删除</button>
  </li>
</template>

<script setup lang="ts">
import { defineProps,toRefs,ref } from 'vue'
// 引入mitt
import emitter from '@/utils/emitter'
// 接收数据
let props = defineProps(['item'])
// 删除提交数据
function del(){
    emitter.emit("delTodo",props.item)
}
// 点击勾选框改变done的值
function changeCheck(){
    props.item.done = !props.item.done
    emitter.emit("changeCheck",props.item)
}




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