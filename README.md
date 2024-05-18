

## todoLists

此项目是我学习完vue3练手小demo

todolist待办事项管理

### 为什么选用vue3

这个本应该是vue2就做的小demo，因为拖延症的原因

一直拖到学习完vue3，思来想去，索性直接用vue3写

顺便可以检验一下vue3的学习成果

### 项目启动

#### 进入项目文件夹

```node
cd todoLists
```

#### 安装依赖

```sh
npm i
```

#### 运行项目

```sh
npm run dev
```

#### 项目打包

```sh
npm run build
```

### 项目预览

https://todo.5b2.cn

### 遇到的问题

#### 1.使用reactive定义对象类型的响应式数据，对属性重新赋值，失去响应式

##### 数据定义

```vue
//数据
let todos:any = reactive([
  { id: nanoid(), title: '抽烟', done: true },
  { id: nanoid(), title: '喝酒', done: false },
  { id: nanoid(), title: '开车', done: true },
  { id: nanoid(), title: '睡觉', done: false }
])
```

##### 删除功能

```vue
//删除功能
emitter.on("delTodo",(delData:any) => {
  //对接受的数据结构赋值
  let {id,index} = delData
  console.log(todos.filter((item:any) => item.id != id))
  todos = todos.filter((item:any) => item.id != id)
})
```

因为todos是通过reactive定义的数据，如果对reactive定义的数据进行重新赋值就会失去其todos的响应式，对于dom的及时更新数据是一个问题，这里当时还是沿用vue2天禹老师的方法，filter方法对todos数组过滤反转返回一个新的数组给todos数组，此方法行不通，只要对todos重新赋值，就会丢失其响应式

##### 解决方法

###### 方法1

将数据定义改为ref定义

```
//数据
let todos:any = ref([
  { id: nanoid(), title: '抽烟', done: true },
  { id: nanoid(), title: '喝酒', done: false },
  { id: nanoid(), title: '开车', done: true },
  { id: nanoid(), title: '睡觉', done: false }
])
```

###### 方法2

在 Vue 3 中，使用 `reactive` 定义的数据是响应式的。如果要修改 `reactive` 对象的数组属性，不能直接重新赋值整个数组（如 `todos = ...`），而是需要直接修改数组的内容。可以使用操作数组的方法（如 `splice`）来实现这一点

```
//删除功能
emitter.on("delTodo",(delData:any) => {
  //对接受的数据结构赋值
  let { id } = delData
  const index = todos.findIndex((item:any) => item.id === id)
  if (index !== -1) {
    todos.splice(index, 1)
  }
})
```

这里使用到了 findIndex 这个方法 此方法会遍历这个数组 将符合 `item.id === id` 的数组返回其index的索引下标，如果不符合要求则返回值 `-1`

#### 2.组件名称和html标签冲突

在命名组件的时候 命名 `header.vue`  在app.vue 引入head组件的时候，会遇到报错问题

实际上是组件的标签和HTML自带的header标签冲突，这里可以使用多个单词拼凑，vue官方推荐大驼峰命名法

例如`TodoHeader.vue` 这样更加语义化，也更符合标准一些

#### 3.组件参数传递

##### 1.defineProps传值 （父->子）

```
<TodoListVue :todos="todos"/>
```

在子组件只需要接收即可

```vue
import {defineProps} from 'vue'
//接收app.vue组件传来的todos数据
defineProps(['todos'])
```

##### 2.自定义事件传值 （子->父）

需要区分好：原生事件、自定义事件。
●原生事件： 
○事件名是特定的（click、mosueenter等等）
○事件对象$event: 是包含事件相关信息的对象（pageX、pageY、target、keyCode）
●自定义事件： 
○事件名是任意名称
**○**事件对象$event: 是调用emit时所提供的数据，可以是任意类型！！！

```
<!--在父组件中，给子组件绑定自定义事件：-->
<Child @send-toy="toy = $event"/>

<!--注意区分原生事件与自定义事件中的$event-->
<button @click="toy = $event">测试</button>
```

```
//子组件中，触发事件：
this.$emit('send-toy', 具体数据)
```

##### 3.mitt || 全局事件总线 (任意组件的传值)

mitt工具实际上就是全局事件总线的封装

在vue2 我们需要在vm实例对象挂载的时候使用生命周期钩子

```
beforeCreate(){
 Vue.propertype.$bus = this
}
```

在vue的实例对象的原型链上挂载$bus属性

在任意组件我们可以通过 this.$bus.$emit() 发送参数 其他组件接收值就用 this.$bus.$on()  接受参数即可

在vue3  mitt就是全局事件总线的封装

新建文件：src\utils\emitter.ts

```
npm i mitt
```

```
// 引入mitt 
import mitt from "mitt";

// 创建emitter
const emitter = mitt()
```

我们在接收数据的组件这样写

```
import emitter from "@/utils/emitter";
import { onUnmounted } from "vue";

// 绑定事件
emitter.on('send-toy',(value)=>{
  console.log('send-toy事件被触发',value)
})

onUnmounted(()=>{
  // 解绑事件
  emitter.off('send-toy')
})
```

发送数据的组件这样写

```
import emitter from "@/utils/emitter";

function sendToy(){
  // 触发事件
  emitter.emit('send-toy',toy.value)
}
```

