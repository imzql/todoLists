<script setup lang="ts">
import { ref, reactive,toRefs,computed,watch } from 'vue'
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
  todos.unshift(obj)
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
//勾选功能
emitter.on("changeCheck",(item:any) => {
  todos.forEach((element:any) => {
    if(element.id == item.id){
      element.done = item.done
    }
  })
})
//全选功能
emitter.on("ischeckAll",(value:any) => {
  if(value){
    todos.forEach((item:any) => {
      item.done = value
    })
  }else{
    todos.forEach((item:any) => {
      item.done = value
    })
  }
  
})

/**
 * 如果全选所有按钮，默认勾选全选
 */
let total =computed(() => {
  return todos.length
})

let autoClickAll = computed(() => {
  return todos.reduce((pre:any,todo:any)=> pre += (todo.done ? 1 : 0)
  ,0)
})

let returntrue = computed(() => {
  return total.value == autoClickAll.value
})
let all = watch(returntrue,(newVal) => {
    emitter.emit('autoIsTrue',newVal)
})
/**
 * 
 */
// 删除已完成的待办事项
function deleteCompleted() {
    // 遍历todos数组，使用splice删除done为true的项
    for (let i = todos.length - 1; i >= 0; i--) {
    if (todos[i].done) {
      todos.splice(i, 1);
    }
  }
}
</script>

<template>
  <div id="root">
    <div class="todo-container">
      <div class="todo-wrap">
        <TodoHeaderVue />
        <TodoListVue :todos="todos"/>
        <TodoFooter @deleteCompleted="deleteCompleted" :total="total" :autoClickAll="autoClickAll" />
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
