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


[^1]: # 理解Object.defineProperty的作用

    ---

    * [https://segmentfault.com/a/1190000007434923 - SegmentFault](https://segmentfault.com/a/1190000007434923)
    * 对象是由多个名/值对组成的无序的集合。对象中每个属性对应任意类型的值。定义对象可以使用构造函数或字面量的形式： {代码...} 除了以上添加属性的方式，还...
    * 2022-08-25 11:24:17

    ---

    对象是由多个名/值对组成的无序的集合。对象中每个属性对应任意类型的值。  
    定义对象可以使用构造函数或字面量的形式：

    ```
    var obj = new Object;  //obj = {}
    obj.name = "张三";  //添加描述
    obj.say = function(){};  //添加行为
    ```

    除了以上添加属性的方式，还可以使用**Object.defineProperty**定义新属性或修改原有的属性。

    ## Object.defineProperty()

    语法：

    ```
    Object.defineProperty(obj, prop, descriptor)
    ```

    参数说明：

    > obj：必需。目标对象  
    > prop：必需。需定义或修改的属性的名字  
    > descriptor：必需。目标属性所拥有的特性
    >

    返回值：

    > 传入函数的对象。即第一个参数obj
    >

    针对属性，我们可以给这个属性设置一些特性，比如是否只读不可以写；是否可以被*for..in*或 *Object.keys()* 遍历。

    给对象的属性添加特性描述，目前提供两种形式：数据描述和存取器描述。

    ### **数据描述**

    当修改或定义对象的某个属性的时候，给这个属性添加一些特性：

    ```
    var obj = {
        test:"hello"
    }
    //对象已有的属性添加特性描述
    Object.defineProperty(obj,"test",{
        configurable:true | false,
        enumerable:true | false,
        value:任意类型的值,
        writable:true | false
    });
    //对象新添加的属性的特性描述
    Object.defineProperty(obj,"newKey",{
        configurable:true | false,
        enumerable:true | false,
        value:任意类型的值,
        writable:true | false
    });
    ```

    数据描述中的属性都是可选的，来看一下设置每一个属性的作用。

    #### **value**

    属性对应的值,可以使任意类型的值，默认为undefined

    ```
    var obj = {}
    //第一种情况：不设置value属性
    Object.defineProperty(obj,"newKey",{

    });
    console.log( obj.newKey );  //undefined
    ------------------------------
    //第二种情况：设置value属性
    Object.defineProperty(obj,"newKey",{
        value:"hello"
    });
    console.log( obj.newKey );  //hello
    ```

    #### **writable**

    属性的值是否可以被重写。设置为true可以被重写；设置为false，不能被重写。默认为false。

    ```
    var obj = {}
    //第一种情况：writable设置为false，不能重写。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false
    });
    //更改newKey的值
    obj.newKey = "change value";
    console.log( obj.newKey );  //hello

    //第二种情况：writable设置为true，可以重写
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:true
    });
    //更改newKey的值
    obj.newKey = "change value";
    console.log( obj.newKey );  //change value
    ```

    #### **enumerable**

    此属性是否可以被枚举（使用for...in或Object.keys()）。设置为true可以被枚举；设置为false，不能被枚举。默认为false。

    ```
    var obj = {}
    //第一种情况：enumerable设置为false，不能被枚举。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false,
        enumerable:false
    });

    //枚举对象的属性
    for( var attr in obj ){
        console.log( attr );  
    }
    //第二种情况：enumerable设置为true，可以被枚举。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false,
        enumerable:true
    });

    //枚举对象的属性
    for( var attr in obj ){
        console.log( attr );  //newKey
    }
    ```

    #### **configurable**

    是否可以删除目标属性或是否可以再次修改属性的特性（writable, configurable, enumerable）。设置为true可以被删除或可以重新设置特性；设置为false，不能被可以被删除或不可以重新设置特性。默认为false。

    这个属性起到两个作用：

    1. 目标属性是否可以使用delete删除
    2. 目标属性是否可以再次设置特性

    ```
    //-----------------测试目标属性是否能被删除------------------------
    var obj = {}
    //第一种情况：configurable设置为false，不能被删除。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false,
        enumerable:false,
        configurable:false
    });
    //删除属性
    delete obj.newKey;
    console.log( obj.newKey ); //hello

    //第二种情况：configurable设置为true，可以被删除。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false,
        enumerable:false,
        configurable:true
    });
    //删除属性
    delete obj.newKey;
    console.log( obj.newKey ); //undefined

    //-----------------测试是否可以再次修改特性------------------------
    var obj = {}
    //第一种情况：configurable设置为false，不能再次修改特性。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false,
        enumerable:false,
        configurable:false
    });

    //重新修改特性
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:true,
        enumerable:true,
        configurable:true
    });
    console.log( obj.newKey ); //报错：Uncaught TypeError: Cannot redefine property: newKey

    //第二种情况：configurable设置为true，可以再次修改特性。
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:false,
        enumerable:false,
        configurable:true
    });

    //重新修改特性
    Object.defineProperty(obj,"newKey",{
        value:"hello",
        writable:true,
        enumerable:true,
        configurable:true
    });
    console.log( obj.newKey ); //hello
    ```

    除了可以给新定义的属性设置特性，也可以给已有的属性设置特性

    ```
    //定义对象的时候添加的属性，是可删除、可重写、可枚举的。
    var obj = {
        test:"hello"
    }

    //改写值
    obj.test = 'change value';

    console.log( obj.test ); //'change value'

    Object.defineProperty(obj,"test",{
        writable:false
    })


    //再次改写值
    obj.test = 'change value again';

    console.log( obj.test ); //依然是：'change value'
    ```

    提示：一旦使用Object.defineProperty给对象添加属性，那么如果不设置属性的特性，那么configurable、enumerable、writable这些值都为默认的false

    ```
    var obj = {};
    //定义的新属性后，这个属性的特性中configurable，enumerable，writable都为默认的值false
    //这就导致了neykey这个是不能重写、不能枚举、不能再次设置特性
    //
    Object.defineProperty(obj,'newKey',{

    });

    //设置值
    obj.newKey = 'hello';
    console.log(obj.newKey);  //undefined

    //枚举
    for( var attr in obj ){
        console.log(attr);
    }
    ```

    设置的特性总结：

    > value:  设置属性的值  
    > writable:   值是否可以重写。true | false  
    > enumerable: 目标属性是否可以被枚举。true | false  
    > configurable:   目标属性是否可以被删除或是否可以再次修改特性 true | false
    >

    ### **存取器描述**

    当使用存取器描述属性的特性的时候，允许设置以下特性属性：

    ```
    var obj = {};
    Object.defineProperty(obj,"newKey",{
        get:function (){} | undefined,
        set:function (value){} | undefined
        configurable: true | false
        enumerable: true | false
    });
    ```

    **注意：当使用了getter或setter方法，不允许使用writable和value这两个属性**

    #### **getter/setter**

    当设置或获取对象的某个属性的值的时候，可以提供getter/setter方法。

    * getter 是一种获得属性值的方法
    * setter是一种设置属性值的方法。

    在特性中使用get/set属性来定义对应的方法。

    ```
    var obj = {};
    var initValue = 'hello';
    Object.defineProperty(obj,"newKey",{
        get:function (){
            //当获取值的时候触发的函数
            return initValue;  
        },
        set:function (value){
            //当设置值的时候触发的函数,设置的新值通过参数value拿到
            initValue = value;
        }
    });
    //获取值
    console.log( obj.newKey );  //hello

    //设置值
    obj.newKey = 'change value';

    console.log( obj.newKey ); //change value
    ```

    **注意：get或set不是必须成对出现，任写其一就可以。如果不设置方法，则get和set的默认值为undefined**

    **configurable和enumerable同上面的用法。**

    ---

    ### 兼容性

    在ie8下只能在DOM对象上使用，尝试在原生的对象使用 Object.defineProperty()会报错。

    ---



[^2]: # Vue中computed和watch的区别

    ---

    * [https://segmentfault.com/a/1190000022740275 - SegmentFault](https://segmentfault.com/a/1190000022740275)
    * 我们在 Vue 项目中多多少少都会有用到 computed 和 watch，这两个看似都能实现对数据的监听，但还是有区别。所以以下通过一个小栗子来理解一下这两者的区别。
    * 2022-08-25 17:02:52

    ---

    我们在 Vue 项目中多多少少都会有用到 computed 和 watch，这两个看似都能实现对数据的监听，但还是有区别。所以以下通过一个小栗子来理解一下这两者的区别。

    [[ 戳 我 查 看 官 网 ]](https://link.segmentfault.com/?enc=7Zqctk9pjWJEFl5By2OTsA%3D%3D.OIUjWcK2xUAta%2Bs7THqpQs880on80U2cRi%2FDj5qxsXsrLksZ%2FfZe%2BPVpxmvgZLIm)

    **computed 计算属性**  
      计算属性基于 data 中声明过或者父组件传递的 props 中的数据通过计算得到的一个 **新值** ，这个新值只会根据已知值的变化而变化，简言之：这个属性依赖其他属性，由**其他属性计算而来**的。

    ```
    <p>姓名：{{ fullName }}</p>
    ... ...
    data: {
        firstName: 'David',
        lastName: 'Beckham'
    },
    computed: {
        fullName: function() { //方法的返回值作为属性值
                return this.firstName + ' ' + this.lastName
        }
    }
    ```

    在 computed 属性对象中定义计算属性的方法，和取data对象里的数据属性一样以**属性访问**的形式调用，即在页面中使用 {{ 方法名 }} 来显示计算的结果。

     **注** ：计算属性 fullName 不能在 data 中定义，而计算属性值的相关已知值在data中；  
    如果 fullName 在 data 中定义了会报错如下图：

    ![QQ截图20200525144944.png](https://segmentfault.com/img/bVbHzVp "QQ截图20200525144944.png")  
    因为如果 computed 属性值是一个函数，那么默认会走 get 方法，必须要有一个返回值，函数的返回值就是属性的属性值。计算属性定义了 fullName 并返回对应的结果给这个变量，变量不可被重复定义和赋值。

    在官方文档中，还强调了 computed 一个重要的特点，就是  **computed 带有缓存功能** 。比如我在页面中多次显示 fullName：

    ```xml
    <p>姓名：{{ fullName }}</p>
    <p>姓名：{{ fullName }}</p>
    <p>姓名：{{ fullName }}</p>
    <p>姓名：{{ fullName }}</p>
    <p>姓名：{{ fullName }}</p>
    ... ... 

    computed: {
        fullName: function () {
             console.log('computed') // 在控制台只打印了一次
             return this.firstName + ' ' + this.lastName
        }
    }
    ```

    我们知道 computed 内定义的 function 只执行一次，仅当初始化显示或者相关的 data、props 等属性数据发生变化的时候调用；  
    而 computed 属性值默认会缓存计算结果，计算属性是基于它们的响应式依赖进行缓存的；  
    只有当 computed 属性被使用后，才会执行 computed 的代码，在重复的调用中，只要依赖数据不变，直接取缓存中的计算结果。只有**依赖型数据**发生 **改变** ，computed 才会重新计算。

     **计算属性的高级** ：  
      在computed 中的属性都有一个 **get** 和一个 **set** 方法，当数据变化时，调用 set 方法。下面我们通过计算属性的 getter/setter 方法来实现对属性数据的显示和监视，即双向绑定。

    ```
    computed: {
        fullName: {
            get() { //读取当前属性值的回调，根据相关的数据计算并返回当前属性的值
                return this.firstName + ' ' + this.lastName
            },
            set(val) { // 当属性值发生改变时回调，更新相关的属性数据，val就是fullName的最新属性值
                const names = val ? val.split(' ') : [];
                this.firstName = names[0]
                this.lastName = names[1]
            }
        }
    }
    ```

    **watch 监听属性**  
      通过 vm 对象的 $watch() 或 watch 配置来监听 Vue 实例上的属性变化，或某些特定数据的变化，然后执行某些具体的业务逻辑操作。当属性变化时，回调函数自动调用，在函数内部进行计算。其可以监听的数据来源：data，props，computed 内的数据。

    以上示例通过 watch 来实现：

    ```
    watch: {
        // 监听 data 中的 firstName，如果发生了变化，就把变化的值给 data 中的 fullName， val 就是 firstName 的最新值
        firstName: function(val) { 
            this.fullName = val + ' ' + this.lastName
        },
        lastName: function(val) {
            this.fullName = this.firstName + ' ' + val
        }  
    }
    // 由上可以看出 watch 要监听两个数据，而且代码是同类型的重复的，所以相比用 computed 更简洁
    ```

     **注：** 监听函数有两个参数，第一个参数是最新的值，第二个参数是输入之前的值，顺序一定是 **新值，旧值** ，如果只写一个参数，那就是最新属性值。

    在使用时选择 watch 还是 computed，还有一个参考点就是官网说的：当需要在数据变化时执行异步或开销较大的操作时，watch方式是最有用的。所以 watch 一定是**支持异步**的。

    上面仅限监听简单数据类型，监听复杂数据类型就需要用到深度监听 deep。  
    **|**    **deep：** 为了发现对象内部值的变化，可以在选项参数中指定 deep: true。注意监听数组的变更不需要这么做。

    ```
    data: {
        fullName: {
            firstName: 'David',
            lastName: 'Beckham'
        }
    },
    watch: {
        fullName: {
            handler(newVal, oldVal) {
                console.log(newVal);
                console.log(oldVal);
            },
            deep: true
        }
    }
    ```

    以上打印结果：  
    ​![QQ截图20200525162250.png](https://segmentfault.com/img/bVbHzVP "QQ截图20200525162250.png")  
    打印出来的 newVal 和 oldVal 值是一样的，所以深度监听虽然可以监听到对象的变化，但是无法监听到对象里面哪个具体属性的变化。这是因为它们的引用指向同一个对象/数组。Vue 不会保留变更之前值的副本。[[ vm.$watch 深度监听 ]](https://link.segmentfault.com/?enc=fVoegyzou9E14Xa82y8j%2BA%3D%3D.zBFQR07fsJDEGUIdqb7FGptSx%2BszWaIrKMw93715AspTqrB2qHTolq5f6LKYc1Bd)

    若果要监听对象的单个属性的变化，有两种方法：  
    1.直接监听对象的属性

    ```
    watch:{
        fullName.firstName: function(newVal,oldVal){
            console.log(newVal,oldVal);
        }
    }
    ```

    2.与 computed 属性配合使用，computed 返回想要监听的属性值，watch 用来监听

    ```
    computed: {
        firstNameChange() {
        return this.fullName.firstName
        }
    },
    watch: {
        firstNameChange() {
            console.log(this.fullName)
        }
    }
    ```

    **总结：**

    **watch和computed都是以Vue的依赖追踪机制为基础**的，当某一个依赖型数据（依赖型数据：简单理解即放在 data 等对象下的实例数据）发生变化的时候，所有依赖这个数据的相关数据会自动发生变化，即自动调用相关的函数，来实现数据的变动。  
    **当依赖的值变化时，在watch中，是可以做一些复杂的操作的，而computed中的依赖，仅仅是一个值依赖于另一个值，是值上的依赖。**

    应用场景：  
    　　computed：用于处理复杂的逻辑运算；一个数据受一个或多个数据影响；用来处理watch和methods无法处理的，或处理起来不方便的情况。例如处理模板中的复杂表达式、购物车里面的商品数量和总金额之间的变化关系等。  
    　　watch：用来处理当一个属性发生变化时，需要执行某些具体的业务逻辑操作，或要在数据变化时执行异步或开销较大的操作；一个数据改变影响多个数据。例如用来监控路由、inpurt 输入框值的特殊处理等。

    区别：　　  
    **computed**

    * 初始化显示或者相关的 data、props 等属性数据发生变化的时候调用；
    * 计算属性不在 data 中，它是基于data 或 props 中的数据通过计算得到的一个新值，这个新值根据已知值的变化而变化；
    * 在 computed 属性对象中定义计算属性的方法，和取data对象里的数据属性一样，以属性访问的形式调用；
    * 如果 computed 属性值是函数，那么默认会走 get 方法，必须要有一个返回值，函数的返回值就是属性的属性值；
    * computed 属性值默认会**缓存**计算结果，在重复的调用中，只要依赖数据不变，直接取缓存中的计算结果，只有**依赖型数据**发生 **改变** ，computed 才会重新计算；
    * 在computed中的，属性都有一个 get 和一个 set 方法，当数据变化时，调用 set 方法。

    **watch**

    * 主要用来监听某些特定数据的变化，从而进行某些具体的业务逻辑操作，可以看作是 computed 和 methods 的结合体；
    * 可以监听的数据来源：data，props，computed内的数据；
    * watch **支持异步** ；
    * **不支持缓存** ，监听的数据改变，直接会触发相应的操作；
    * 监听函数有两个参数，第一个参数是最新的值，第二个参数是输入之前的值，顺序一定是新值，旧值。



[^3]: # Vue中watch对象内属性的方法

    ---

    * [https://segmentfault.com/a/1190000018080301 - SegmentFault](https://segmentfault.com/a/1190000018080301)
    * 上述情况里data中的count属性可以直接监听，但是如果需要监听的数据是对象内的某一属性值的变化，直接watch对象blog是检测不到变化的，这是因为blog这个对象...
    * 2022-08-25 16:38:41

    ---

    vue提供了`watch`方法，用于监听实例内`data`数据的变化。通常写法是：

    ```js
    new Vue({
      data: {
        count: 10，
        blog:{
            title:'my-blog',
            categories:[]
        }
      },
      watch: {
        count: function (newval, oldVal) {
          console.log(`new: %s, old: %s`, newVal, oldVal);
        }
      }
    })
    ```

    上述情况里`data`中的`count`属性可以直接监听，但是如果需要监听的数据是对象内的某一属性值的变化，直接`watch`对象`blog`是检测不到变化的，这是因为`blog`这个对象的指向并没有发生改变。有几个解决方法

    ## 1.深度监测

    ```js
    new Vue({
      data: {
        count: 10，
        blog:{
            title:'my-blog',
            categories:[]
        }
      },
      watch: {
        blog:{
            handler(newVal,oldVal){
                console.log(`new: ${newVal}, old: ${oldVal}`);
            },
            deep:true
        }
      }
    })
    ```

    里面的`deep`设为了`true`，这样的话，如果修改了这个`blog`中的任何一个属性，都会执行`handler`这个方法。不过这样会造成更多的性能开销，尤其是对象里面属性过多，结构嵌套过深的时候。而且有时候我们就只想关心这个对象中的某个特定属性，这个时候可以这样

    ## 2.用字符串来表示对象的属性调用

    ```js
    new Vue({
      data: {
        count: 10，
        blog:{
            title:'my-blog',
            categories:[]
        }
      },
      watch: {
        'blog.categories'(newVal, oldVal) {
            console.log(`new:${newVal}, old:${oldVal}`);
        }, 
      }
    })
    ```

    ## 3.使用`computed`计算属性

    ```js
    new Vue({
      data: {
        count: 10，
        blog:{
            title:'my-blog',
            categories:[]
        }
      },
      computed: {
        categories() {
          return this.blog.categories;
        }
      },
      watch: {
        categories(newVal, oldVal) {
          console.log(`new:${newVal}, old:${oldVal}`);
        }, 
      },
    })
    ```

    ## Reference

    [计算属性和侦听器 — Vue.js](https://link.segmentfault.com/?enc=kXoLnG8Trguz7ZGYKXTCuQ%3D%3D.1%2FqN5Ac2th8hpAvozv24P8FJXpWwXBb%2BFJV53vc51AsRywRPIPdxeDx6Z%2FEmZuD7frXUpWKUjtprVEEVnoJho4DAbjLflL8qQuMRLyA%2FhHE%3D)  
    [vue watch对象内的属性监听](https://link.segmentfault.com/?enc=uyqc9KjcvDkLDgnV%2FF%2BY1Q%3D%3D.Hf0KsQUcYJh4al%2B%2BMLJbJ8Xhh1jQKyRLUxF7Qh9H2b27VGe9%2FwafKmtC9vfM4URm8LdDVGLsFeqDVwGC6Q2j%2FQ%3D%3D)  
    [Vue使用watch监听一个对象中的属性](https://link.segmentfault.com/?enc=oNFUCiWygWsvEN7vyiLfVQ%3D%3D.Z1vYRNMs03ZLRc0%2BshixdXsoWqKTFTJwN%2FSwOY7awY%2FSY3E%2FLHFmH9mQk8XfHaNE%2FbSQao%2Bq%2BiYbTlkI5hI4LA%3D%3D)
