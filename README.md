

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

#### 4.在vue3 props是只读的，不能在子组件直接修改，会严重破坏vue设计原则

数据我在app.vue设置，通过props传值到，todolist.vue，再通过todolist.vue传值到todoitem.vue，层层传值，在todoitems组件我接收到了App.vue组件的值，通过 

```
let props = defineProps(['item'])
```

设置变量props接受defineprops传来的对象，因为是引用类型，当时直接修改对象内部的属性来实现对传入 `props` 的修改

代码如下

```
// 点击勾选框改变done的值
function changeCheck(){
    props.item.done = !props.item.done
    emitter.emit("changeCheck",props.item)
}
```

其中

```
props.item.done = !props.item.done
```

虽然达到了效果，但是直接修改了app组件传来的值，但是不利于后期维护，这里并不推荐这种写法

##### 推荐做法

推荐的做法是通过向父组件发送事件，父组件来修改 `props` 的值。这样可以确保数据的流向是单向的，从而保持数据管理的清晰和可维护性

```
<template>
  <li>
    <label>
      <!-- 使用事件来通知父组件修改数据 -->
      <input type="checkbox" :checked="item.done" @click="toggleDone" /> &nbsp;
      <span>{{ item.title }}</span>
    </label>
    <button class="btn btn-danger" @click="del">删除</button>
  </li>
</template>

<script setup lang="ts">
import { defineProps } from 'vue';
import emitter from '@/utils/emitter';

const props = defineProps({
  item: {
    type: Object,
    required: true
  }
});

function del() {
  emitter.emit("delTodo", props.item);
}

function toggleDone() {
  emitter.emit("changeCheck", { ...props.item, done: !props.item.done });
}
</script>

```

##### 资料解释

1. **单向数据流原则**：在 Vue 中，数据流应该是单向的。父组件通过 `props` 向子组件传递数据，子组件通过事件向父组件传递修改请求。
2. **引用类型的修改**：在 JavaScript 中，对象是引用类型，所以直接修改对象的属性实际上是修改了引用本身。这种做法虽然可行，但破坏了 Vue 的单向数据流原则，可能导致数据流混乱，难以维护。
3. **事件通信**：通过事件通信让父组件来处理数据的修改，可以保持数据流的清晰和可维护性。

通过以上的改进，可以确保数据流符合 Vue 的设计理念，增强代码的可读性和可维护性

#### 一些操作数组方法总结

##### map()

一般参数是一个回调函数

map() 的返回值是一个新的数组，新数组中的元素为 “原数组调用函数处理过后的值”

```
const array = [2, 3, 4, 4, 5, 6]

console.log("array",array)
const map = array.map(x => {
    if (x == 4) {
        return x * 2
    }
    return x
})

console.log("map",map)

```

###### 参数解释

```
array.map((item,index,arr)=>{
	//item是操作的当前元素
	//index是操作元素的下表
	//arr是需要被操作的元素
	//具体需要哪些参数 就传入那个
})
```

###### 例子详解

```
 const array = [2, 3, 4, 4, 5, 6]
 console.log("原数组array为",array)
 const map2=array.map((item,index,arr)=>{
            console.log("操作的当前元素",item)
            console.log("当前元素下标",index)
            console.log("被操作的元素",arr)
            //对元素乘以2
            return item*2
 })
 console.log("处理之后先产生的数组map",map2)
```

###### 总结

map()方法经常拿来遍历数组，但是不改变原数组，但是会返回一个新的数组

##### filter()

filter用于对数组进行**过滤**。

它创建一个新数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。

注意：filter()不会对空数组进行检测、不会改变原始数组

```
Array.filter(function(currentValue, indedx, arr), thisValue)
```

其中，函数 function 为必须，数组中的每个元素都会执行这个函数。且如果返回值为 true，则该元素被保留；
函数的第一个参数 currentValue 也为必须，代表当前元素的值。

###### 实例

返回数组nums中所有大于5的元素

```
let nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

let res = nums.filter((num) => {
  return num > 5;
});

console.log(res);  // [6, 7, 8, 9, 10]
```

##### forEach()

主要功能是遍历数组

就是 for 循环的加强版，该语句需要一个回调函数，作为参数。回调函数的形参，依次为，value：遍历的数组内容；index：对应的数组索引，array：数组本身

```
<script type="text/javascript">
	// 分别对应：数组元素，元素的索引，数组本身
	var arr = ['a','b','c'];	
	arr.forEach(function(value,index,array){
		console.log(value);
		console.log(index);
		console.log(array);
		})
</script>
```

###### 总结

**for 循环比较步骤多比较复杂，forEach 循环比较简单好用，不容易出错**

##### reduce()

###### 语法

```
arr.reduce(function(prev,cur,index,arr){
...
}, init);
```

其中，
**arr** 表示原数组；
**prev** 表示上一次调用回调时的返回值，或者初始值 init;
**cur** 表示当前正在处理的数组元素；
**index** 表示当前正在处理的数组元素的索引，若提供 init 值，则索引为0，否则索引为1；
**init** 表示初始值。

###### 实例

先提供一个原始数组：

```
var arr = [3,9,4,3,6,0,9];
```

###### 1. 求数组项之和

```
var sum = arr.reduce(function (prev, cur) {
    return prev + cur;
},0);
```

由于传入了初始值0，所以开始时prev的值为0，cur的值为数组第一项3，相加之后返回值为3作为下一轮回调的prev值，然后再继续与下一个数组项相加，以此类推，直至完成所有数组项的和并返回。

###### 2. 求数组项最大值

```
var max = arr.reduce(function (prev, cur) {
    return Math.max(prev,cur);
});
```

由于未传入初始值，所以开始时prev的值为数组第一项3，cur的值为数组第二项9，取两值最大值后继续进入下一轮回调。

###### 3. 数组去重

```
var newArr = arr.reduce(function (prev, cur) {
    prev.indexOf(cur) === -1 && prev.push(cur);
    return prev;
},[]);
```

```tex
实现的基本原理如下：

① 初始化一个空数组
② 将需要去重处理的数组中的第1项在初始化数组中查找，如果找不到（空数组中肯定找不到），就将该项添加到初始化数组中
③ 将需要去重处理的数组中的第2项在初始化数组中查找，如果找不到，就将该项继续添加到初始化数组中
④ ……
⑤ 将需要去重处理的数组中的第n项在初始化数组中查找，如果找不到，就将该项继续添加到初始化数组中
⑥ 将这个初始化数组返回
```

##### splice()

该方法有3个参数

```
splice(index,len,[item])
```

其中，

index:数组开始下标 

len: 替换/删除的长度 

item:替换的值，删除操作的话 item为空即可

###### splice作用

删除元素/插入元素/替换元素，该方法会改变原始数组

###### 作用1：删除元素 — [item]为0

```
arr.splice(1,1) //[‘1’,‘3’,‘4’]

arr.splice(1,0) //[‘1’,‘2’,‘3’,‘4’]

arr.splice(1,2) //[‘1’,‘4’]
```

###### 作用2：替换元素 — item为替换的值

```
arr.splice(1,1,‘x’) //[‘1’,‘x’,‘3’,‘4’] 替换起始下标为1，长度为1的值为x’

arr.splice(1,2,‘y’) //[‘1’,‘y’’,‘4’] 替换起始下标为1，长度为2的两个值为‘y’

arr.splice(1,2,‘x’,‘y’) //[‘1’,‘x’,‘y’,‘4’] 替换起始下标为1，长度为2的两个值
```

###### 作用3：插入元素 — len设置为0，item为添加的值

```
arr.splice(1,0,‘x’) //[‘1’,‘x’,‘2’,‘3’,‘4’]

arr.splice(1,0, ‘x’, ‘y’, ‘z’) //[‘1’,‘x’, ‘y’, ‘z’,‘2’,‘3’,‘4’]
```

##### findIndex()

###### 用法

`findIndex()` 方法返回数组中通过测试的第一个元素的索引（作为函数提供）。

`findIndex()` 方法对数组中存在的每个元素执行一次函数：

- 如果找到函数返回 true 值的数组元素，则 findIndex() 返回该数组元素的索引（并且不检查剩余值）
- 否则返回 -1

**注释：**`findIndex()` 不会为没有值的数组元素执行函数。

**注释：**`findIndex()` 不会改变原始数组。

###### 语法

```
array.findIndex(function(currentValue, index, arr), thisValue)
```

| *currentValue* | 必需。当前元素的值。         |
| -------------- | ---------------------------- |
| *index*        | 可选。当前元素的数组索引。   |
| *arr*          | 可选。当前元素所属的数组对象 |

| *thisValue* | 可选。要传递给函数以用作其 "this" 值的值。如果此参数为空，则值 "undefined" 将作为其 "this" 值传递 |
| ----------- | ------------------------------------------------------------ |

###### 用例

获取数组中第一个值等于或大于 18 的元素的索引：

```
var ages = [3, 10, 18, 20];

function checkAdult(age) {
  return age >= 18;
}

function myFunction() {
  document.getElementById("demo").innerHTML = ages.findIndex(checkAdult);
}
```

获取数组中第一个值高于特定数字的元素的索引：

```
<p>Minimum age: <input type="number" id="ageToCheck" value="18"></p>
<button onclick="myFunction()">Try it</button>

<p>Any ages above: <span id="demo"></span></p>

<script>
var ages = [4, 12, 16, 20];

function checkAdult(age) {
  return age >= document.getElementById("ageToCheck").value;
}

function myFunction() {
  document.getElementById("demo").innerHTML = ages.findIndex(checkAdult);
}
</script>
```

