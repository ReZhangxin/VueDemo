# `vue`中常用指令

- [`v-if`指令](#v-if)
- [`v-for`指令](#v-for)
- [`v-text` & `v-html`指令](#v-text)
- [`v-on`：绑定事件监听器](#v-on)
- [`v-model`双向绑定](#v-model)
- [`v-bind`指令：绑定属性](#v-bind)
- [`v-pre`,`v-bloak`,`v-once`pre,加载完渲染，只显示一次](#v-pre)

<h2 id="v-if">v-if指令</h2>

v-if：用来判断是否加载DOM。

```js
/*
<h1>判断</h1>
<hr>
<div id="app">
   <div >亲，1+1等于几啊？</div>
   <button v-on:click="sumShow">点我，点我</button>
   <h3 v-if="sum">哈哈，笨蛋是2</h3>
   <h3 v-else="sum">哈哈，你猜你猜</h3>
</div>
*/
var app = new Vue({
    el:'#app',
    data:{
        sum:false
    },
    methods:{
        sumShow:function(){
            if(this.sum == false){
                this.sum = true;
            }else{
                this.sum = false;
            }
        }
    }
})
```

- `v-if`： 判断是否加载，可以减轻服务器的压力，在需要时加载。
- `v-show`：调整`css dispaly`属性，可以使客户端操作更加流畅。

<h2 id="v-for">`v-for`指令 ：解决模板循环问题</h2>

`v-for`指令是循环渲染一组`data`中的数组，`v-for` 指令需要以 `item in items` 形式的特殊语法，`items`是源数据数组并且`item`是数组元素迭代的别名。

```html
<div id="app">
    <ul>
        <li v-for="item in sortItems">{{item}}</li>
    </ul>
</div>
```

```js
var app = new Vue({
        el:'#app',
        data:{
            items:[23,343,44,5,6767,899,33]
        },
        computed:{
            sortItems:function(){
                return this.items.sort(sortNumber);
            }
        }

});
function sortNumber(a,b){
    return a-b
}
```

**要注意：** `li`中的`item in sortItems`而不是`item in items` `{{item}}`而不是`{{items}}`

## 对象循环输出

```html
<div id="app">
    <ul>
        <li v-for="(xingzuo,index) in sortStar">
           {{index +1}}： {{xingzuo.star}} ==> {{xingzuo.date}}
        </li>
    </ul>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        constellation:[
            {star:"摩羯座",date:"12月22日~01月19日"},
            {star:"水瓶座",date:"01月20日~02月18日"},
            {star:"双鱼座",date:"02月19日~03月20日"},
            {star:"白羊座",date:"03月21日~04月19日"},
            {star:"金牛座",date:"04月20日~05月20日"},
            {star:"双子座",date:"05月21日~06月21日"},
            {star:"巨蟹座",date:"06月22日~07月22日"},
            {star:"狮子座",date:"07月23日~08月22日"},
            {star:"处女座",date:"08月23日~09月22日"},
            {star:"天秤座",date:"09月23日~10月23日"},
            {star:"天蝎座",date:"10月24日~11月21日"},
            {star:"射手座",date:"11月22日~12月21日"}
        ]
    },
    computed:{
        sortStar:function(){
            return sortByKey(this.constellation,'date');
        }
    }
});
//数组对象方法排序:
function sortByKey(array,key){
    return array.sort(function(a,b){
        var x=a[key];
        var y=b[key];
        return ((x<y)?-1:((x>y)?1:0));
    });
}
```
<h2 id="v-text">`v-text` & `v-html`</h2>

> 我们已经会在`html`中输出`data`中的值了，我们已经用的是`{{xxx}}`,这种情况是有弊端的，就是当我们网速很慢或者`javascript`出错时，会暴露我们的`{{xxx}}`。`Vue`给我们提供的`v-text`,就是解决这个问题的。

```html
 <span>{{ message }}</span>=<span v-text="message"></span><br/>
 ```
 
 双大括号会将数据解释为纯文本，而非`HTML`。为了输出真正的`HTML`，你就需要使用`v-html` 指令。

需要注意的是：在生产环境中动态渲染`HTML`是非常危险的，因为容易导致`XSS`攻击。所以只能在可信的内容上使用`v-html`，永远不要在用户提交和可操作的网页上使用。

```html
<div id="app">
   <span>{{message}}</span>===<span v-text="message"></span>
   <div v-html="dodo"></div>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:`你好，世界 ! `,
        dodo:"<h2>hellofei</h2>"
    }
})
```

<h2 id="v-on">`v-on`：绑定事件监听器</h2>

> `v-on` 就是监听事件，可以用`v-on`指令监听`DOM`事件来触发一些`javascript`代码。

我们的`v-on` 还有一种简单的写法，就是用`@`代替。

```html
<button @click="reduce">减分</button>
```

我们除了绑定`click`之外，我们还可以绑定其它事件，比如键盘回车事件`v-on:keyup.enter`,现在我们增加一个输入框，然后绑定回车事件，回车后把文本框里的值加到我们的`count`上。

```html
<div id="app">
   本场得分：{{count}}
   <p>
       <button @click="add">加分</button>
       <button @click="reduce">减分</button>
       <input type="text" @keyup.enter="onEnter" v-model="second">
   </p>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{    
        count:0     
    },
    methods:{
        add:function(){
            this.count++;
        },
        reduce:function(){
            this.count--;
        },
        onEnter:function(){
            this.count = this.count + parseInt(this.second);
        }
    }
})
```

![键盘](http://oy9skqng0.bkt.clouddn.com/20170227001137.jpg)

<h2 id="v-model">`v-model`</h2>

双向绑定

```html
<div id="app">
    <p>原始文本数据：{{message}}</p>
    <p><input type="text" v-model="message"></p>
</div>

```

```js
var app = new Vue({
    el:'#app',
    data:{
      message:'model'
    }
})
```

- `v-model="message"`
- `v-model.lazy="message"`
- `v-model.number="message`
- `v-model.trim="message"`

```html
<p>原始文本数据：{{message}}</p>
<p>v-model <input type="text" v-model="message"></p>
<p>v-model.lazy <input type="text" v-model.lazy="message"></p>
<p>v-model.number <input type="text" v-model.number="message"></p>
<p>v-model.trim <input type="text" v-model.trim="message"></p>
```

## 多选框绑定数组

```html
<h3>多选框绑定数组</h3>
<p>
    <input type="checkbox" id="01" value="小鑫" v-model="names">
    <label for="01">小鑫</label>
    <input type="checkbox" id="02" value="大鑫" v-model="names">
    <label for="02">大鑫</label>
    <input type="checkbox" id="03" value="老鑫" v-model="names">
    <label for="03">老鑫</label>
    <p>{{names}}</p>
</p>
```

```js
var app = new Vue({
    el:'#app',
    data:{
      message:'model',
      strue:'true',
      names:[]
    }
})
```

<h2 id="v-bind">`v-bind`指令</h2>

`v-on`绑定的是事件 `click``keyup``mouseover`等等

`v-bind`是处理HTML中的标签属性的，例如`<div></div>`就是一个标签，`<img>`也是一个标签，我们绑定`<img>`上的`src`进行动态赋值。

```html
<div id="app">
    <img v-bind:src="imgSrc" alt="" width="400">
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        imgSrc:'http://ouch71mj2.bkt.clouddn.com/ship.jpg'
    }
})
```

#### 简写方式 `:`

```html
<p><a :href="blogUrl" target="_blank">我的博客地址</a></p>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:`你好，世界 ! 现在是 ${new Date().toLocaleTimeString()}`,
        imgSrc:'http://ouch71mj2.bkt.clouddn.com/ship.jpg',
        blogUrl:"http://zhangss.win"
    }
})

```

#### 绑定`class`

```html
<div :class="className">1、绑定class</div>
<div :class="{classA:isOk}">2、绑定class判断</div>
<div :class="[classA,classB]">3、绑定class数组</div>
<div :class="isOk?classA:classB">4、绑定class数组三元运算</div>
<hr>
<div>
    <input type="checkbox" id="isOk" v-model="isOk">
    <label for="isOk">isOk={{isOk}}</label>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:`你好，世界 ! 现在是 ${new Date().toLocaleTimeString()}`,
        imgSrc:'http://ouch71mj2.bkt.clouddn.com/ship.jpg',
        blogUrl:"http://zhangss.win",
        className:'classA',
        isOk:false,
        classA:'classA',
        classB:'classB'
    }
})
```

`v-bind`绑定样式

```html
<div :style="{color:aqua,fontSize:font}">5、绑定style</div>
<div :style="styleObj">6、绑定style对象</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        aqua:'aqua',
        font:'150%',
        styleObj:{
            color:'aqua',
            fontSize:'150%'
        }
    }
})
```

<h2 id="v-pre">`v-pre`,`v-bloak`,`v-once`</h2>

```html
<div id="app">
    <div v-pre>{{message}}</div>
    <div v-cloak>渲染完成后才显示</div>
    <div v-once>{{message}}</div>
    <div><input type="text" v-model="message"></div>
    <div>{{message}}</div>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:`你好，世界 !`
    }
})
```
# 全局API

- [第1节：Vue.directive 自定义指令](#directive)
- [第2节：Vue.extend构造器的延伸](#extend)
- [第3节：Vue.set全局操作](#set)
- [第4节：Vue的生命周期（钩子函数）](#live)
- [第5节：Template 制作模版](#Template)
- [第6节：Component 初识组件](#Component0)
- [第7节：Component 组件props 属性设置](#Component1)
- [第8节：Component 父子组件关系](#Component2)
- [第9节：Component 标签](#Component3)

<h2 id="directive">Vue.directive 自定义指令</h2>

### 1、全局`API`？

> 全局`API`并不在构造器里，而是先声明全局变量或者直接在`Vue`上定义一些新功能，`Vue`内置了一些全局`API`，比如指令`Vue.directive`。说的简单些就是，**在构造器外部用`Vue`提供给我们的`API`函数来定义新的功能。**

### 2、`Vue.directive`自定义指令

```html
<div id="app">
    <div v-xin="color">{{init}}</div>
    <p><button @click="add">ADD</button></p>
</div>
```

```js
Vue.directive('xin',function(el,binding){
    console.log(el);
    console.log(binding);
    console.log(binding.name);
    console.log(binding.value);
    console.log(binding.expression);
    el.style="color:"+binding.value;
})
var app = new Vue({
    el:'#app',
    data:{
        init:10,
        color:"coral"
    },
    methods:{
        add:function(){
            this.init+=10;

        }
    }
})
```

### 自定义指令中传递的三个参数

- `el`: 指令所绑定的元素，可以用来直接操作`DOM`。

- `binding`:  一个对象，包含指令的很多信息。

- `vnode`: `Vue`编译生成的虚拟节点。

### 自定义指令的生命周期

自定义指令有五个生命周期（也叫钩子函数），分别是 `bind`,`inserted`,`update`,`componentUpdated`,`unbind`

- `bind`:只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个绑定时执行一次的初始化动作。

- `inserted`:被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于`document`中）。

- `update`:被绑定于元素所在的模板更新时调用，而无论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新。

- `componentUpdated`:被绑定元素所在模板完成一次更新周期时调用。

- `unbind`:只调用一次，指令与元素解绑时调用。

```js
 Vue.directive('xin',{
    bind:function(){//被绑定
        console.log('1 - bind');
    },
    inserted:function(){//绑定到节点
        console.log('2 - inserted');
    },
    update:function(){//组件更新
        console.log('3 - update');
    },
    componentUpdated:function(){//组件更新完成
        console.log('4 - componentUpdated');
    },
    unbind:function(){//解绑
        console.log('1 - bind');
    }
})
```

<h2 id="extend">Vue.extend构造器的延伸</h2>

> `Vue.extend`返回的是一个“扩展实例构造器”,也就是预设了部分选项的`Vue`实例构造器。经常服务于`Vue.component`用来生成组件，可以简单理解为当在模板中遇到该组件名称作为标签的自定义元素时，会自动调用“扩展实例构造器”来生产组件实例，并挂载到自定义元素上。

> #### 自定义无参数标签

就想下面的`author`标签

```html
<div id="author"></div>
<author></author>
```

```js
var authorName = Vue.extend({
    template:"<p><a :href='authorURL'>{{authorName}}</a></p>",
    data:function(){
        return {
            authorName:"张鑫",
            authorURL:"http://zhangss.win"
        }
    }
});
new authorName().$mount("#author");
```

这时`html`中的标签还是不起作用的，因为扩展实例构造器是需要挂载的，我们再进行一次挂载。

```js
new authorExtend().$mount('author');
```

> #### 挂载到普通标签上

还可以通过`HTML`标签上的`id`或者`class`来生成扩展实例构造器，`Vue.extend`里的代码是一样的，只是在挂载的时候，我们用类似`jquery`的选择器的方法，来进行挂载就可以了。

```js
new authorExtend().$mount('#author');

```

<h2 id="set">Vue.set全局操作</h2>

> `Vue.set` 的作用就是在构造器外部操作构造器内部的数据、属性或者方法。比如在`vue`构造器内部定义了一个`count`为`1`的数据，我们在构造器外部定义了一个方法，要每次点击按钮给值加`1`.就需要用到`Vue.set`。

#### 一、引用构造器外部数据：

什么是外部数据，就是不在`Vue`构造器里里的`data`处声明，而是在构造器外部声明，然后在`data`处引用就可以了。外部数据的加入让程序更加灵活，我们可以在外部获取任何想要的数据形式，然后让`data`引用。

```html
<div id="app">{{count}}</div>
<p>
    <button onclick="add()">add</button>
</p>
```

```js
function add(){
    Vue.set(outData,'count','666')
}
var outData ={
    count:1,
    goods:"car"
}
var app = new Vue({
    el:'#app',
    data:outData
})
```

##### 在外部改变数据的三种方法：

- 1、用`Vue.set`改变

```js
 function add(){
       Vue.set(outData,'count',4);
 }
```
- 2、用`Vue`对象的方法添加

```js
app.count++;
```
- 3、直接操作外部数据

```js
outData.count++;
```

其实这三种方式都可以操作外部的数据，`Vue`也给我们增加了一种操作外部数据的方法。

### 为什么要有`Vue.set`的存在?

由于`Javascript`的限制，`Vue`不能自动检测以下变动的数组。

- 当你利用索引直接设置一个项时，`vue`不会为我们自动更新。

- 当你修改数组的长度时，`vue`不会为我们自动更新。

```js
function add(){
    // Vue.set(outData,'count','666')
    // app.count++;
    //outData.count++;
    //app.arr[0]=0
    Vue.set(app.arr,0,0);
}
var outData ={
    count:1,
    arr:['a','b','c','d']
}
var app = new Vue({
    el:'#app',
    data:outData
})
```

> ##### 这时我们的界面是不会自动跟新数组的，我们需要用`Vue.set(app.arr,0,0);`来设置改变，`vue`才会给我们自动更新，这就是`Vue.set`存在的意义。

<h2 id="live">Vue的生命周期（钩子函数）</h2>

```html
<div id="app">
    {{count}}
<p><button @click="add">add</button></p>
</div>
<button onclick="app.$destroy()">销毁</button>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        count:1
    },
    methods:{
        add:function(){
            this.count++;
        }
    },
    beforeCreate:function(){
        console.log('1-beforeCreate 初始化之前');
    },
    created:function(){
        console.log('2-created 创建完成');
    },
    beforeMount:function(){
        console.log('3-beforeMount 挂载之前');
    },
    mounted:function(){
        console.log('4-mounted 被挂载之后');
    },
    beforeUpdate:function(){
        console.log('5-beforeUpdate 数据更新前');
    },
    updated:function(){
        console.log('6-updated 被更新后');
    },
    activated:function(){
        console.log('7-activated');
    },
    deactivated:function(){
        console.log('8-deactivated');
    },
    beforeDestroy:function(){
        console.log('9-beforeDestroy 销毁之前');
    },
    destroyed:function(){
        console.log('10-destroyed 销毁之后');
    }
})
```

<h2 id="Template">Template 制作模版</h2>

- 1、直接写在选项里的模板

> 直接在构造器里的template选项后边编写。这种写法比较直观，但是如果模板html代码太多，不建议这么写。

```js

var app=new Vue({
    el:'#app',
    data:{
        message:'hello Vue!'
    },
    template:`<h1 style="color:red">我是选项模板</h1>`
})
```

- 2、写在`<template>`标签里的模板

```html
<template id="app2">这里是template标签模板</tempalte>
```

```js
var app = new Vue({
    el:"#app",
    template:"#app2"
})
```

- 3、写在`<script>`标签里的模板

```html
<script type="x-template" id="app3">
    <h2>我是script模板</h2>
</script>
```

```js
var app = new Vue({
    el:'#app',
    template:'#app3'
})
```

<h2 id="Component0">Component 初识组件</h2>

**非常重要**

> Component组件就是自定义标签例如<xin></xin>

- 1、全局化注册组件

全局化就是在构造器的外部用`Vue.component`来注册，我们注册现在就注册一个`<xin></xin>`的组件来体验一下。

```html
<div id="app">
    <blog></blog>
</div>
```
```js
Vue.component('blog',{
    template:
    `<div>我是全局template模板
        <a href="http://zhangss.win">
            这是我的个人网址
        </a>
    </div>`
})
var app = new Vue({
    el:'#app',
})
```

- 2、局部注册组件

局部注册组件和全局注册组件是相对应的，局部注册的组件只能在组件注册的作用域里进行使用，其他作用域使用无效。

```html
<div id="app">
    <blog></blog>
</div>
```

```js
var app = new Vue({
    el:'#app',
    Components:{
        "blog":{
            template:'<div>局部定义标签</div>'
        }
    }
})
```

<h2 id="Component1">Component 组件props 属性设置</h2>

props选项就是设置和获取标签上的属性值的，例如我们有一个自定义的组件`<xin></xin>`,这时我们想给他加个标签属性写成`<xin here='huaibei'></xin>` 意思就是熊猫来自中国，当然这里的`huaibei`可以换成任何值。定义属性的选项是`props`。

- 定义属性并获取属性值

> 定义属性我们需要用`props`选项，加上数组形式的属性名称，例如：`props:[‘here’]`。在组件的模板里读出属性值只需要用插值的形式，例如`{{ here }}`.

```html
<div id="app">
  <xin here="huaibei"></xin>
</div>
```

```js
var app = new Vue({
    el:'#app',
    components:{
        template:`<div>Xin from {{here}}</div>`,
        props:['here']
    }
})
```
### 在构造器里向组件中传值

> 把构造器中data的值传递给组件，我们只要进行绑定就可以了。

```html
<div id="app">
    <xin :here="address"></xin>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        address:'huaibei'
    },
    components:{
        "xin":{
            template:`<div>Xin from {{here}}</div>`,
            props:['here']
        }
    }
})
```
<h2 id="Component2">Component 父子组件关系</h2>

> 在实际开发中我们经常会遇到在一个自定义组件中要使用其他自定义组件，这就需要一个父子组件关系。

### 1、构造器外部写局部注册组件

我们把组件编写的代码放到构造器外部或者说单独文件。

我们需要先声明一个对象,对象里就是组件的内容。

```html
<xin></xin>
```

```js
var xin = {
    template:`<div>我在上海哈哈!</div>`
}
```
声明好对象后在构造器里引用就可以了。

```js
components:{
    "xin":xin
}
```

### 2、父子组件的嵌套

我们先声明一个父组件，比如叫`xin`，然后里边我们加入一个`city`组件

```html
<div id="app">
   <xin></xin>
</div>
```

```js
var city = {
    template:`<div>我在上海哈哈!</div>`
}
var xinComponent ={
    template:`
        <div>
            <p>你现在在哪里啊？</p>
            <city></city>
        </div>
    `,
    components:{
        "city":city
    }
}

var app = new Vue({
    el:'#app',
    components:{
        "xin":xinComponent
    }
})
```

<h2 id="Component3">Component 标签</h2>

> `<component></component>`标签是`Vue`框架自定义的标签，它的用途就是可以动态绑定我们的组件，根据数据的不同更换不同的组件。

- 1.我们先在构造器外部定义三个不同的组件，分别是`componentA`,`componentB`和`componentC`.

```js
var componentA = {
    template:`<div>I'm componentA</div>`
};
var componentB = {
    template:`<div>I'm componentB</div>`
}
var componentC = {
    template:`<div>I'm componentC</div>`
}

```

- 2.我们在构造器的`components`选项里加入这三个组件。

```js
components:{
    "componentA":componentA,
    "componentB":componentB,
    "componentC":componentC
}
```

- 3.我们在`html`里插入`component`标签，并绑定`who`数据，根据`who`的值不同，调用不同的组件。

```html
 <component v-bind:is="who"></component>
 ```
 
 ```html
<div id="app">
    <component v-bind:is="who"></component>
    <button @click="changeComponent">change</button>
</div>
```

```js
 var componentA = {
    template:`<div>I'm componentA</div>`
};
var componentB = {
    template:`<div>I'm componentB</div>`
}
var componentC = {
    template:`<div>I'm componentC</div>`
}

var app = new Vue({
    el:'#app',
    data:{
       who:componentC
    },
    components:{
        "componentA":componentA,
        "componentB":componentB,
        "componentC":componentC
    },
    methods:{
        changeComponent:function(){
            if(this.who == "componentA"){
                this.who = "componentB"
            }else if(this.who == "componentB"){
                this.who = "componentC"
            }else{
                this.who = "componentA"
            }
        }
    }
})
```
