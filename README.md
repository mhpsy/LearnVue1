# LearnVue1
# vue

## 实例和容器

```js
//创建Vue实例
const x = new Vue({
    'el':document.getElementById('root')//el用于指定当前Vue实例为哪个容器服务，值为css选择器字符串
})
```

```js
//创建Vue实例
const x = new Vue({
    'el':'#root'//el用于指定当前Vue实例为哪个容器服务，值为css选择器字符串
})
```

上面这两种就是一样的作用

## ：绑定

```js
    <div id="root">
        <h1>hello {{name}}</h1>
        <h1>my age is {{age}}</h1>
    </div>    

//{{}}可以取出来data里面的数据，el就是容器的名字，容器应该与Vue实例一一对应
//双花括号中可以写js表达式，eg：3+2

        new Vue({
            el:'#root',
            data: {
                name:'mhpsy',
                age:22
            }
        })
```

```js
    <a :href="MhpsyUrl">{{name}}</a><br>
    <a v-bind:href="MhpsyUrl">{{name}}</a>
```

`v-bind:`可以直接简写为`:`

## 数据绑定

* v-bind是单向数据绑定

  * 可以简写为`:value='name'`
* v-model是双向数据绑定

  * 可以简写为`v-model='name'`

```html
<div id="inputModel">
    姓名：<input type="text" :value="name">
    <br>
    地址：<input type="text" v-model="name">
</div>
```

## $mount

```html
    <div id="root">
        {{name}}
    </div>
    <script>
        var x = new Vue({
            data:{
                name:'test setTimeout and mount'
            }
        })
        setTimeout(()=>{
            x.$mount('#root')
        },1000)
    </script>
```

mount这里有挂载的意思，就是挂载上这个节点

## data的两种写法

一种是对象式，一种是函数式。

```html
    <div id="root">
        {{name}}
    </div>
    <script>
        var x = new Vue({
            data:function () {
                return{
                    name:'mhpsy'
                }
            }
        })
        setTimeout(()=>{
            x.$mount('#root')
        },1000)
    </script>
```

```html
    <div id="root">
        {{name}}
    </div>
    <script>
        var x = new Vue({
//简写之后就变成了这样子
            data() {
                return{
                    name:'mhpsy'
                }
            }
        })
        setTimeout(()=>{
            x.$mount('#root')
        },1000)
    </script>
```

> vue管理的函数一定不能写成箭头函数
>

## MVVM模型

理解Object.defineProperty的作用[^1]

首先先明白defineProperty是在干什么，主要就是对追加上去的属性加一些限制

```js
    Object.defineProperty(person,'sex',{
        get(){
            console.log("have some one read")
            return sex;
        }
    })
//这是一个属性方法，当有人获取sex的时候就会执行
```

```js
    Object.defineProperty(person,'sex',{
        get(){
            console.log("have some one read")
            return sex;
        },
        set(value){
            sex = value
            console.log("have some one modify is",sex)
        }
    })
```

---

## 数据代理

通过一个对象代理对另一个对象中属性的操作 读或者写

```js
    let obj = {x: 100}
    let obj2 = {y: 200}
    Object.defineProperty(obj2, 'x', {
        get() {
            return obj.x
        },
        set(v) {
            obj.x = v
        }
    })
```

上面就是一个最简单的数据代理

## 事件处理

1. methods中配置的函数都是被vue所管理的函数，this指向的是vm
2. `@click="demo"`和`@click="demo($event)"`效果一致，但是后者可以传参
3. 如果在methods中使用箭头函数，那么this就不是vm

### 事件修饰符

如果方法写为`@click="demo($event).prevent"`就相当于在方法内部加了一个`e.preventDefault()`目的是阻止原始行为

1. prevent 阻止默认事件
2. stop 阻止时间冒泡
3. once 事件只触发一次
4. capture 使用事件的捕获模式
5. self 只有event.target是当前操作的元素时才触发事件
6. passive 事件的默认行为立即执行，无需等待事件回调执行完毕

## 计算属性

```html
不使用计算属性
<div id="root">
    姓<input type="text" v-model="firstName"><br>
    名<input type="text" v-model="lastName"><br>
    全名:<span>{{fullName()}}</span><br>
    这个地方必须要携程方法名()的形式才可以,跟方法绑定不一样
</div>
<script>
    const x = new Vue({
        el:"#root",
        data:{
            firstName:'xiao',
            lastName:'qiqi'
        },
        methods:{
            fullName(){
                return this.firstName+'-'+this.lastName
            }
        }
    })
</script>
```

---

计算属性:其实就是拿着已经有了的属性进行加工计算得到一个新的属性,这就是计算属性了

```html
    <div id="root">
        姓<input type="text" v-model="firstName"><br><br>
        名<input type="text" v-model="lastName"><br><br>
        全名<span>{{fullName}}</span><br><br>
    </div>
    <script>
        new Vue({
            el:'#root',
            data: {
                firstName:'qwer',
                lastName:'asdf'
            },
            computed:{
                fullName:{
                    get(){
                        return this.firstName+'-'+this.lastName
                    }
                }
            }
        })
    </script>
使用计算属性大概就是这样子实现,目的就是为了降低耦合度
```

如果数据复杂程度大了最好使用计算属性,因为使用起来会很方便

同理有get也会有set,set就是在被改变值的时候去运行

computed里面最好只使用vue管理的值,因为如果不是vue管理的数据,那么可以会不受管理

```html
<div id="root">
    <h1>今天的天气{{showWeather}}</h1>
    <button @click="isHot=!isHot">反转</button>
</div>
<script>
    new Vue({
        el: '#root',
        data: {
            isHot: true
        },
        computed: {
            showWeather() {
                return this.isHot ? "炎热" : "不热";
            }
        }
    })
</script>
```

上面就是计算属性简写和最常用的方法    

## 监视属性

Vue中computed和watch的区别[^2]

Vue中watch对象内属性的方法[^3]

```html
<div id="root">
    <h1>今天的天气{{showWeather}}</h1>
    <button @click="isHot=!isHot">反转</button>
</div>
<script>
    new Vue({
        el: '#root',
        data: {
            isHot: true
        },
        computed: {
            showWeather() {
                return this.isHot ? "炎热" : "不热";
            }
        },
        watch:{
            isHot(newV,oldV){
                console.log(newV,oldV)
            }
        }
    })
</script>
```

```html
<div id="root">
    <h1>今天的天气{{showWeather}}</h1>
    <button @click="isHot=!isHot">反转</button>
</div>
<script>
    const x = new Vue({
        el: '#root',
        data: {
            isHot: true
        },
        computed: {
            showWeather() {
                return this.isHot ? "炎热" : "不热";
            }
        }
    })
    x.$watch('isHot',function (newV,oldV) {
        console.log(newV,oldV)
    })
</script>
挂载的形式
```

如果一个值依赖多个属性（多对一），用computed肯定是更加方便的。如果一个值变化后会引起一系列操作，或者一个值变化会引起一系列值的变化（一对多），用watch更加方便一些。

Watch 支持异步代码而 computed 不支持。

```html
使用监视属性完成之前的需求
<div id="root">
  xing<input type="text" v-model="firstName"><br><br>
  ming<input type="text" v-model="lastName"><br><br>
    FullName<span> {{fullName}} </span>
</div>
<script>
    new Vue({
        el:"#root",
        data:{
            firstName:'zhang',
            lastName:'san',
            fullName:'zhang-san'
        },
        watch:{
            //如果直接使用fullName(val),这个val是新值的意思
            firstName(val){
                this.fullName=val+'-'+this.lastName
            },
            lastName(val){
                this.fullName=this.firstName+'-'+val
            }
        }
    })
</script>
```

## 绑定样式

[vue绑定样式 菜鸟教程](https://www.runoob.com/vue2/vue-class-style.html)

[vue官方的绑定样式文档](https://cn.vuejs.org/guide/essentials/class-and-style.html#binding-html-classes)

可以直接`:class='arr'`arr是一个数组的形式,arr也要在vue里面的data里面创建出来

## 条件渲染

```html
v-show  
    适合用于展示频率较高的场景,不展示的DOM仅仅是使用样式隐藏掉
v-if
v-else-if
v-else
    适合切换频率比较低的场景,不展示的DOM元素直接被移除  
    如果这些标签一起使用结构不可以被打断
```

如果用div包裹起来可能会破坏一些页面的样式,但是可以使用<template>包裹起来

template包裹起来不会破坏页面的样式

## 列表渲染

```html
<div id="root">
  <ul>
    <li v-for="p in persons" :key="p.id">
      {{p.id}}-{{p.name}}-{{p.age}}
    </li>
  </ul>
  <ul>
    <li v-for="(p,index) in persons" :key="index">
      {{index+1}} {{p.id}}-{{p.name}}-{{p.age}}
    </li>
  </ul>
  <ul>
    <li v-for="(val,key) in cat" :key="key">
      {{key}}--{{val}}
    </li>
  </ul>
</div>
<script>
  new Vue({
    el:'#root',
    data:{
      persons:[
        {id:1,name:'san',age:18},
        {id:2,name:'sbn',age:19},
        {id:3,name:'scn',age:20}
      ],
      cat:{
        name:'mhpsy',
        type:'jia',
        sex:'女',
        age:3
      }
    }
  })
</script>
```

简单来说v-for 可以遍历数组和对象,当然也可以遍历字符串和指定次数

`:key`需要是唯一的,是用来防止数据错乱导致乱起来的

![image](assets/image-20220826151834-bi5ks13.png)​

![image](assets/image-20220826151824-pg842ql.png)​

如果遍历的过程中没有写key,那么vue就默认把index当作key使用,如果不存在破坏数据顺序的插入和添加的话,那么使用index作为key是没有问题的,一般最好使用唯一标识作为key比如uuid或者学号之类的


## 列表过滤

```html
    <div id="root">
        <input type="text" placeholder="please input name" v-model="selectWord">
        <ul>
            <li v-for="(m,index) in meth" :key="m.id">{{m.id}}--{{m.name}}--{{m.sex}}</li>
        </ul>
    </div>
    <script>
        new Vue({
            el:'#root',
            data:{
                meth:[
                    {id:1,name:'query',sex:'man'},
                    {id:2,name:'select',sex:'man'},
                    {id:3,name:'insert',sex:'man'},
                    {id:4,name:'update',sex:'man'},
                ],
                selectWord:''
            },
            watch:{
                selectWord(newV,oldV){
                    console.log('selectWord被调用了'+newV+'-'+oldV)
                    this.meth = this.meth.filter((m)=>{
                        return m.name.indexOf(newV)!==-1
                    })
                }
            }
        })
    </script>
```

这个目前是有问题的,因为只能一次性的使用正确的数据,一般不能直接修改原来的数据,这样数据会越来越少

> 有一个值得注意的地方就是如果indexof空字符串得到的结果会是0
>

```html
<div id="root">
    <input type="text" placeholder="please input name" v-model="selectWord">
    <ul>
        <li v-for="(m,index) in showMeth" :key="m.id">{{m.id}}--{{m.name}}--{{m.sex}}</li>
    </ul>
</div>
<script>
    new Vue({
        el: '#root',
        data: {
            selectWord: '',
            meth: [
                {id: 1, name: 'query', sex: 'man'},
                {id: 2, name: 'select', sex: 'man'},
                {id: 3, name: 'insert', sex: 'man'},
                {id: 4, name: 'update', sex: 'man'},
            ],
            showMeth: []
        },
        watch: {
            selectWord: {
                immediate: true,
                handler(newV, oldV) {
                    console.log('selectWord被调用了' + newV + '-' + oldV)
                    this.showMeth = this.meth.filter((m) => {
                        return m.name.indexOf(newV) !== -1
                    })
                }
            }
        }
    })
</script>
```

利用上面提到的indexof空字符串会得到0,在利用`immediate: true`,外加给函数写完全,这样就可以实现这个需求了

---

## Vue.set

* **参数** ：

  * `{Object | Array} target`
  * `{string | number} propertyName/index`
  * `{any} value`
* **返回值** ：设置的值。
* **用法** ：  
  向响应式对象中添加一个 property，并确保这个新 property 同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增property (比如 `this.myObject.newProperty = 'hi'`)

> 注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。
>

## 收集表单数据

收集表单数据：

* 若：<input type="text"/>，则v-model收集的是value值，用户输入的就是value值。
* 若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。
* 若：<input type="checkbox"/>

  1. 没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
  2. 配置input的value属性:

      1. v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
      2. v-model的初始值是数组，那么收集的的就是value组成的数组

          1. 备注：v-model的三个修饰符：  
              lazy：失去焦点再收集数据  
              number：输入字符串转为有效的数字  
              trim：输入首尾空格过滤


