## todoLists

学习vue3练手小demo

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

```
//数据
let todos:any = reactive([
  { id: nanoid(), title: '抽烟', done: true },
  { id: nanoid(), title: '喝酒', done: false },
  { id: nanoid(), title: '开车', done: true },
  { id: nanoid(), title: '睡觉', done: false }
])
```

##### 删除功能

```
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



