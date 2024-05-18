<template>
    <ul class="todo-main">
        <TodoItemVue  v-for="(item,index) in todos" :data="{item,index}"/>
    </ul>
</template>

<script setup lang="ts">
import TodoItemVue from '@/components/TodoItem.vue'
import {defineProps} from 'vue'
//接收app.vue组件传来的todos数据
defineProps(['todos'])


</script>

<style scoped>
/*main*/
.todo-main {
    margin-left: 0px;
    border: 1px solid #ddd;
    border-radius: 2px;
    padding: 0px;
    margin-top: 10px;
    margin-bottom: 10px;
}

.todo-empty {
    height: 40px;
    line-height: 40px;
    border: 1px solid #ddd;
    border-radius: 2px;
    padding-left: 5px;
    margin-top: 10px;
}
</style>