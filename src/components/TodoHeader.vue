<template>
    <div class="todo-header">
        <input type="text" placeholder="请输入你的任务名称，按回车键确认" v-model="inputTitie" @keyup.enter="add" />
    </div>
</template>

<script setup lang="ts">
import { ref, reactive } from "vue"
import emitter from "@/utils/emitter"
//数据
let inputTitie = ref('')
//方法
function add() {
    //逻辑增强 如果为空数据则阻止提交
    if (inputTitie.value) {
        //提交数据给App.vue
        emitter.emit("sendTitleData", inputTitie.value)
        //清空输入框数据
        inputTitie.value = ''
    }else{
        alert("亲,您还没有提交数据呢")
    }
}

</script>

<style scoped>
/*header*/
.todo-header input {
    width: 560px;
    height: 28px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 4px 7px;
}

.todo-header input:focus {
    outline: none;
    border-color: rgba(82, 168, 236, 0.8);
    box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
}
</style>