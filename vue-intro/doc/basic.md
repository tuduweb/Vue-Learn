## Vue入门 学习
### 介绍章节：基础部分
#### 声明式渲染
也就是基础性的渲染`{{ message }}`使用这样的方式接受变量。从而实现文本插值。

#### 处理用户输入

1. 使用`v-on`，如`v-on:click`等实现事件监听器
2. 使用`v-model`实现双向绑定：表单输入和应用状态之间的双向绑定

#### 条件与循环
1. `v-if` 此外，Vue 也提供一个强大的过渡效果系统，可以在 Vue 插入/更新/移除元素时自动应用过渡效果。
2. `v-for` 用`v-for="item in items"`实现对数据数组实现渲染

#### 组件化应用
组件系统是 Vue 的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。仔细想想，几乎任意类型的应用界面都可以抽象为一个组件树：
```javascript
app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})
```

```html
<div id="todo-list-app">
  <ol>
     <!--
      现在我们为每个 todo-item 提供 todo 对象
      todo 对象是变量，即其内容可以是动态的。
      我们也需要为每个组件提供一个“key”，稍后再
      作详细解释。
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```

### 模板语法
#### 插值
1. 文本`{{ msg }}`
2. 原始HTML：不同于输出文本，html输出应该使用`v-html`，如：

   ```html
   <p>Using mustaches: {{ rawHtml }}</p>
   <p>Using v-html directive: <span v-html="rawHtml"></span></p>
   ```

   ```js
   const RenderHtmlApp = {
       data() {
           return {
               rawHtml: '<span style="color: red">This should be red.</span>'
               }
            }
        }
    ```
3. Attribute使用`v-bind`可以绑定属性。对于bool值的属性，如果参数值为`null`或者`undefined`，那么这个属性甚至可能不会包含在渲染出来的元素中。
4. 使用JavaScript表达式：数据绑定支持js表达式语法，但注意，每个绑定只能包含<b>单个表达式</b>

#### 指令
##### 参数

一些指令能接受参数，比如`v-bind:href='url'`。这里的`href`就是参数。

另一个例子是`v-on:click="doSomething"`,用于监听DOM事件。

##### 动态参数
也可以在指令参数中使用JavaScript表达式，方法是用方括号括起来：
```js
<a v-bind:[attributeName]="url">...</a>
```
这里的`attributeName`会被作为一个JavaScript表达式进行动态求值，求得的值将会作为最终的参数来使用。

同样的，这种方法也可以运用于动态事件名绑定处理函数：
```js
<a v-on:[eventName]="doSomething">...</a>
```

##### 修饰符
修饰符 (modifier) 是以半角句号.指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：

<b>暂时无法理解</b>

#### 缩写
Vue为`v-bind`和`v-on`两个最常用的指令，提供了缩写。
1. `v-bind:`变成`:`
2. `v-on:`变成`@`

##### 注意事项
###### 对动态参数值的约定
动态参数值预期是求出一个字符串，异常情况下值为`null`。这个特殊的`null`值可以被显示性的用于移除绑定。任何其他非字符串类型的值都将会出发一个警告。

###### 对动态参数表达式约定
动态参数表达式有一些语法约束，因为某些字符，如空格和引号，放在 HTML attribute 名里是无效的。例如：
```javascript
<!-- 这会触发一个编译警告 -->
<a v-bind:['foo' + bar]="value"> ... </a>
```
变通的办法是使用没有空格或引号的表达式，或用计算属性替代这种复杂表达式。

在 DOM 中使用模板时 (直接在一个 HTML 文件里撰写模板)，还需要避免使用大写字符来命名键名，因为浏览器会把 attribute 名全部强制转为小写：
```js
<!--
在 DOM 中使用模板时这段代码会被转换为 `v-bind:[someattr]`。
除非在实例中有一个名为“someattr”的 property，否则代码不会工作。
-->
<a v-bind:[someAttr]="value"> ... </a>
```

###### JavaScript表达式
模板表达式都被放在沙盒中，只能访问<b>全部变量的一个白名单</b>。不应该在模板表达式中，试图访问用户定义的全局变量。


#### Data Property和方法
##### Data Property

组件的`data`选项是一个函数。Vue在创建新组件实例的过程中调用此函数。它应该返回一个对象`{}`，然后Vue会通过响应性系统将其包裹起来，并以`$data`的形式存储在组件实例中。

Vue使用`$`前缀通过组件实例暴露自己内置的API。它还为内部property保留`_`前缀。你应该避免使用这两个字母开头的顶级`data property`名称。

##### 方法
我们用`methods`方法向组件实例添加方法，它应该是一个包含所需方法的对象。

Vue自动为`methods`绑定`this`，以便于它始终指向组件实例。这将确保方法在用作事件监听或回调时保持正确的`this`指向。在定义`mothods`时应该避免使用箭头函数`=> {}`，因为这会阻止Vue绑定恰当的`this`指向。

这些`methods`和组件实例的其它所有property一样可以在组件的模板中被访问。在模板中，它们通常被当做事件监听使用，如点击以下button会调用`increment`方法：
```html
<button @click="increment">Up Vote</button>
```

<b>从模板调用的方法不应该有任何副作用，比如更改数据或触发异步进程。</b>如果想这么做，应该更改换做生命周期钩子。

##### 防抖和截流
Vue没有内置支持防抖和截流。可以使用`Lodash`等库来实现。

#### 计算属性和侦听器
##### 计算属性
模板内的表达式十分便利，但是设计初衷是用于简单运算。在模板中放入太多的逻辑会让模板过重且难以维护。`computed`

##### 计算属性缓存VS方法
通过在表达式中调用方法可以达到同样的效果。但<b>计算属性是基于它们的反应依赖关系缓存的</b>。计算属性只在相关响应式依赖发生改变时它们才会重新求值。

也意味着下面的计算属性值不会再更新，因为`Date.now()`不是响应式依赖：
```js
computed: {
  now() {
    return Date.now()
  }
}
```
相比之下，每次当触发重新渲染时，调用方法总会再次执行函数。

<b>如果你不希望有缓存，请用`method`来替代。</b>

##### 计算属性的Setter
计算属性默认只有getter，不过在需要时你也可以提供一个setter。
```js
computed: {
  fullName: {
    //getter
    get() {
      return this.firstName + ' ' + this.lastName
    }
    //setter
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = name[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```
现在再运行`vm.fullName = 'John Doe'`时，setter会被调用，`vm.firstName`和`vm.lastName`也会相应地被更新。

##### 侦听器
虽然计算属性在大多数情况下合适，但有时也需要一个自定义的侦听器。Vue通过`Watch`选项提供了一个更通用的方法， 来响应数据的变化。在需要数据变化时执行异步（如ajax）或开销较大的操作时，这个方法是最有用的。

除了`watch`选项外，还可以使用命令式的`vm.$watch`Api。

##### 计算属性vs侦听器


#### Class与Style绑定
操作元素的class列表和内联样式是数据绑定的一个常见需求。因为它们都是attribute……

在将`v-bind`用于`class`和`style`时，Vue.js做了专门的增强。表达式结果除了字符串之外，还可以是对象或者数组。

##### 绑定Html Class
###### 对象语法
我们可以传给`:class`一个对象，以动态的切换class：
```html
<div :class="{ active: isActive }"></div>
```
上面的语法表示`active`这个class存在与否取决于数据property`isActive`的truthiness。

可以在对象中传入更多的字段来动态切换多个class。此外`:class`指令可以与普通的`class`共存。

<b>绑定的数据对象不必内联在模板里</b>，如：
```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```
渲染的结果和上面一样。也可以在这里绑定一个放回对象的`计算属性`。
```js
data() {
  return {
    isActive: true,
    error: null
  },
  computed: {
    classObject() {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  }
}
```

###### 数组语法
我们可以把一个数组传给`:class`，以应用一个class列表：
```html
<div :class="[activeClass, errorClass]"></div>
```
data()...

如果想根据条件切换列表中的class，可以使用三元表达式：
```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

不过，在多个条件class时这样写有些繁琐。所以在数组语法中也可以使用对象语法：
```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

###### 在组件上使用
可以在使用组件的时候再增加一些class，对于带数据绑定的class也同样适用。

当组件有多个跟元素的时候，`需要`定义那部分接收这个类。可以使用`$attrs`组件属性执行此操作：
```html
<div id="app">
  <my-component class="baz"></my-component>
</div>
```

```js
const app = Vue.createApp({})

app.component('my-component', {
  template: `
    <p :class="$attrs.class">Hi!</p>
    <span>This is a child component</span>
  `
})
```

##### 绑定内联样式
###### 对象语法
`:style`的对象语法可以使用CSS property。可使用驼峰式或短横线分隔：
```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

直接绑定一个样式对象会使模板变得更清晰。
```html
<div :style="styleObject"></div>
```

###### 数组语法
同上。

###### 自动添加前缀
在`:style`中使用需要 (浏览器引擎前缀) vendor prefixes 的 CSS property 时，如 `transform`，Vue 将自动侦测并添加相应的前缀。

###### 多重值
可以为style绑定中的property提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：
```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```
这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染`display: flex`。



#### 条件渲染
##### `v-if`
`v-if`用于条件性的渲染。同时，也可以使用`v-else`增加一个else块。

###### 在`template`元素上使用`v-if`条件渲染分组
`v-if`是一个指令，必须将它添加到一个元素上。如果想切换多个元素，可以使用一个`template`元素当作不可见的包裹元素，并在上面应用`v-if`。最终渲染结果不含`template`元素。

###### `v-else`
`v-else`必须紧跟在带`v-if`或者`v-else-if`的元素后面，否则它将不会被识别。

###### `v-else-if`
```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

##### `v-show`
另一个用于条件性展示的选项是`v-show`指令。用法类似。

不同的是带有`v-show`的元素始终渲染并保留在DOM中。`v-show`只是简单地切换CSS property的`display`属性。

<b>注意</b>，`v-show`不支持`<template>`元素，也不支持`v-else`。

##### `v-if`vs`v-show`
`v-if`是条件渲染，确保在切换过程中，条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if`是惰性的，若初始化为假，则什么也不做。知道条件第一次变为真时，才会开始渲染条件块。

`v-show`不管初始条件，元素总会被渲染，并且只是简单地基于CSS进行切换。

`v-if`有更高的切换开销，而`v-show`有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用`v-show`较好；如果在运行时条件很少改变，则使用`v-if`较好。

##### `v-if`与`v-for`一起使用
一起使用时，`v-if`具有比`v-for`更高的优先级。

#### 列表渲染
##### 用`v-for`把一个数组对应为一组元素
在`v-for`块中，我们可以访问所有父作用域的property。`v-for`还支持一个可选的第二个参数，即当前项的索引。
```html
<ul id="array-with-index">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      parentMessage: 'Parent',
      items: [{ message: 'Foo' }, { message: 'Bar' }]
    }
  }
}).mount('#array-with-index')
```
可以使用`of`代替`in`作为分隔符，因为它更接近JavaScript迭代器的语法：
```html
<div v-for="item of items"></div>
```

##### 在`v-for`里使用对象
```html
<ul id="v-for-object" class="demo">
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      myObject: {
        title: 'How to do lists in Vue',
        author: 'Jane Doe',
        publishedAt: '2016-04-10'
      }
    }
  }
}).mount('#v-for-object')
```
可以提供第二个参数为porperty名称（也就是键名key）

在遍历对象时，会按`Object.keys()`的结果遍历，但不能保证它在不同的JavaScript引擎下的结果都一致。

##### 维护状态

##### 数组更新检测
###### 变更方法
Vue将被侦听的数组的变更方法进行包裹，所以它们也将会触发视图更新。

###### 替换数组
相较于变更方法，也有非变更方法。如`filter()`、`concat()`和`slice()`，它们不会变更原始数组，而总是返回一个新数组。当使用非变更方法时，可以用新数组替换旧数组：
```js
example1.items = example1.items.filter(item => item.message.match(/Foo/))
```
Vue为了使DOM元素得到更大范围的重用而实现了一些智能的启发式方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

##### 显示过滤/排序后的结果
使用计算属性，来返回过滤、排序后的数组。

在计算属性不适用的情况下（例如，在嵌套`v-for`循环中），可以使用一个方法`methods`。

##### 在`v-for`里使用值的范围
`v-for`可以接受整数。在这种情况下，它会把模板重复对应的次数。

##### 在`<template>`中使用`v-for`
类似于`v-if`，可以使用带有`v-for`的`<template>`来循环渲染一段包含多个元素的内容。

##### `v-for`与`v-if`一同使用
当它们处在同一节点，`v-if`的优先级比`v-for`更高，这意味着`v-if`将没有权限访问`v-for`里的变量。

我们可以把`v-for`移动到`<template>`标签中来修正
```html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo }}
  </li>
</template>
```

##### 在组件上使用`v-for`
在自定义组件上使用`v-for`。

任何数据都不会被自动传递到组件里，因为组件有自己独立的作用域。为了把迭代数据传递到组件里，我们要使用props：
```html
<my-component
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
></my-component>
```

在这里不自动将`item`注入到组件中的原因是，这会使得组件与`v-for`的运作紧密耦合。明确组件的数据来源能够使组件在其他场合重复使用。


