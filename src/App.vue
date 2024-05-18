<script setup lang="ts">
import { ref, reactive,toRefs } from 'vue'
import { nanoid } from "nanoid"
//注册组件
import TodoHeaderVue from '@/components/TodoHeader.vue'
import TodoListVue from '@/components/TodoList.vue'
import TodoFooter from '@/components/TodoFooter.vue'
//引入emitter
import emitter from './utils/emitter'
//数据
let todos:any = reactive([
  { id: nanoid(), title: '抽烟', done: true },
  { id: nanoid(), title: '喝酒', done: false },
  { id: nanoid(), title: '开车', done: true },
  { id: nanoid(), title: '睡觉', done: false }
])
//提交功能
emitter.on("sendTitleData",(Newtitle) => {
  let obj:any = { id: nanoid(), title:Newtitle, done: false }
  todos.push(obj)
})
//删除功能
emitter.on("delTodo",(delData:any) => {
  //对接受的数据结构赋值
  let { id } = delData
  const index = todos.findIndex((item:any) => item.id === id)
  if (index !== -1) {
    todos.splice(index, 1)
  }
})
</script>

<template>
  <div id="root">
    <div class="todo-container">
      <div class="todo-wrap">
        <TodoHeaderVue />
        <TodoListVue :todos="todos"/>
        <TodoFooter />
      </div>
    </div>
  </div>
</template>

<style>
/*base*/
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}

.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
.TodoHeaderVue{
  margin-top: 10px;
  margin-bottom: 10px;
}
</style>
