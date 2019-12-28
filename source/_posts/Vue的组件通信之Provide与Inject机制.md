---
title: Vue的组件通信之Provide与Inject机制
author: Tomatoro
comments: true
tags:
  - Vue
top: 0
abbrlink: f29baeb3
date: 2019-12-28 16:33:43
---


> Vue中父组件到子组件的通信主要由子组件的props属性实现。但是在一些情况下，父组件无法直接向子组件的props传值。比如子组件通过父组件的slot进入父组件，父组件根本不知道子组件是谁，更不用说用子组件的props了。这时应该怎么办呢？Vue在2.2.0版本引入了provide与inject，正好适合处理这一情况。

<!-- more -->
### 什么是provide与inject
用文档的话说：

provide/inject需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。如果你熟悉 React，这与 React 的上下文特性很相似。

这就是说从父组件的provide属性传入一个对象，子组件（或者是孙组件，只要是子级组件）可以用inject属性接收父组件的provide属性。比如
```JS
    // main.vue
    <template>
        <c1 message="hello world">
            <c2></c2>
        </c1>
    </template>
     
    // c1.vue
    <template>
      <div id="c1">
        <slot></slot>
      </div>
    </template>
     
    <script>
    export default {
      props: ['message'],
      provide () {
        return {
          message: this.message
        }
      }
    }
    </script>
     
    // c2.vue
    <template>
      <div id="c2">
          {{ message }}
      </div>
    </template>
     
    <script>
    export default {
      inject: ['message']
    }
    </script>
```
上面的main组件会被渲染为:
```HTML
    <div id="c1">
      <div id= "c2">hello world</div>
    </div>
```
可以看到，c1组件在不清楚子组件是什么的情况下，将它的props中的message传给了c2组件。在这里c1组件就像是一个数据源一样，为子组件提供数据。但是，c1组件提供的数据仅在c1的子孙组件中可见，因此可以算作是有作用域限定的数据源。

父到子孙组件方向的数据流
父到子孙组件方向是provide/inject机制设计时的数据流方向。我们可能会猜想，在父组件中更改provide的值，子组件会响应式的发生改变。但是注意到文档中话。

提示：provide和inject绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

这意味着，如果provide的值不是可监听对象时，在父组件中更改provide的值，子组件不会发生任何变化。比如模板仍然为上面那个例子的模板，message的值是一个props属性，不是可监听对象，如果我们在c1的mounted钩子函数里改变message的值。如:
```JS
    // c1.vue
    <script>
    export default {
      //...
      mounted () {
        setTimeout( () => {
          this.message = 'Opps, it would not be rendered'
        }, 1000)
      }
    }
    </script>
```
子组件不会响应修改后的值。

但是如果provide的值是一个可监听对象呢？请看一下例子：
```JS
    <script>
    // c1.vue
    export default {
      data () {
        return {
          message: 'hello world'
        }
      },
      provide () {
        messageData: this.$data
      },
      mounted () {
        setTimeout(() => {
          this.message = 'I can show in c2.'
        }, 10000)
      }
    }
    </script>
     
    // c2.vue
    <template>
      <div id="c2">
        {{ messageData.message }}
      </div>
    </template>
     
    <script>
    export default {
      inject: ['messageData']
    }
    </script>
```
此时在c1挂载10s后，子组件将会显示I can show in c2。为什么呢？c2中messageData实际上就是c1实例的this.$data。而this.$data上有message的响应式getter与setter。所以c2的视图会被message的dep收集，因此在c1中更新message，c2的视图也会更新。

纵观整个过程，provide/inject机制是非响应式的，即provide与inject之间没有绑定。具体的值是在子组件初始化过程中决定的。

### 总结
provide/inject提供了一种新的组件间通信的方法。它允许父组件向子孙组件间进行跨层级的数据分发。但是provide/inject是非响应式的，如果要子孙组件根据父组件的值进行改变，provide/inject机制不是一个好的选择。此时可以使用Vuex来管理状态。