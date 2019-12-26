---
title: 再读一次Vue官方文档带来的意外惊喜
author: Tomatoro
comments: true
tags:
  - Vue
top: 0
abbrlink: 3a867b5d
date: 2019-12-26 14:20:38
---

### 前言
> Vue目前算是我用的时间最长的一个框架了,但是最近总是在想,我真的了解Vue了吗,还是说,仅仅只是会用它而已了呢.
> 最开始用Vue的时候只是草草看了一遍文档,细节之处并未关心,以至于后面项目中遇到很多问题之后才又反复的去查文档,解决问题.
> 我认为,不应该是这样的,这种程度,仅仅只能让我有处理问题的能力,虽然经验让我再遇到问题的时候知道大概的导向,但这样永远不会建立起对Vue技术怀有的自信.
> 于是,我打算再来一遍官方文档,记录其中从未曾了解过的东西.在这之后,我便会去尝试着去读读Vue的源码,并分享出来.

<!-- more -->

### 动态参数

`<a :[attributeName]="url"> ... </a>`

这里的 `attributeName` 会被作为一个 JavaScript 表达式进行动态求值，求得的值将会作为最终的参数来使用。例如，如果你的 Vue 实例有一个 `data` 属性 `attributeName`，其值为 "href"，那么这个绑定将等价于 `v-bind:href`

注意点: 

- `attributeName` 应该全部都是小写
- `attributeName` 如果是通过表达式生成的,那应将其放在计算属性中,避免直接在HTML中书写表达式

### 动态class和style的几种写法

- class
```JS
  1. <div v-bind:class="{ 'active': isActive, 'text-danger': hasError }"></div> // 对象语法

  2. <div v-bind:class="classObject"></div>// 对象语法

  3. <div v-bind:class="[{ 'active': isActive }, errorClass]"></div>// 数组语法 

     data: {
     isActive: true,
     hasError: false,
     classObject: {
       active: true,
       'text-danger': false
     },
     errorClass: 'text-danger'
     }
```
- style
```JS
  1. <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>// 对象语法

  2. <div v-bind:style="styleObject"></div>// 对象语法

  3. <div v-bind:style="[styleObject, styleObject2]"></div>// 数组语法

  data: {
  	activeColor: 'red',
    fontSize: 30,
  	styleObject: {
      color: 'red',
      fontSize: '13px'
    },
  	styleObject2: {
      color: 'blac',
      fontSize: '13px'
    }
  }
```
### v-show

`v-show` 不支持 `<template>` 元素，也不支持 `v-else`

### v-for

`v-for`遍历对象
```JS
    <div v-for="(value, name, index) in object">
      {{ index }}. {{ name }}: {{ value }}
    </div>
```
### Vue无法检测的数据变动的情况

- 数组

  1. 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
  2. 当你修改数组的长度时，例如：`vm.items.length = newLength`

  解决办法:

  - 使用`vm.$set()` eg. `vm.$set(vm.items, indexOfItem, newValue)`
  - 为数组重新赋值新修改的数组

- 对象

  1. 对象属性的添加
  2. 对象属性的删除

  解决办法:

  - 为对象初始一个空的将要添加的对象
  - 使用`vm.$set()` eg. `vm.$set(vm.Obj, keyOfObj, newValue)`
  - 使用`Object.assign()` eg. `vm.userProfile = Object.assign({}, vm.userProfile, { age: 27, favoriteColor: 'Green'})`

### 事件修饰符

- `.stop`           阻止事件冒泡

- `.prevent`     阻止浏览器默认行为发生

- `.capture`     捕获冒泡，即有冒泡发生时，有该修饰符的dom元素会先执行，如果有多个，从外到内依次执行，然后再按自然顺序执行触发的事件

- `.self`           将事件绑定到自身，只有自身才能触发，通常用于避免冒泡事件的影响

- `.once`           设置事件只能触发一次，比如按钮的点击等

- `.passive`      执行浏览器的默认行为

>【浏览器只有等内核线程执行到事件监听器对应的JavaScript代码时，才能知道内部是否会调用preventDefault函数来阻止事件的默认行为，所以浏览器本身是没有办法对这种场景进行优化的。这种场景下，用户的手势事件无法快速产生，会导致页面无法快速执行滑动逻辑，从而让用户感觉到页面卡顿。】
> 通俗点说就是每次事件产生，浏览器都会去查询一下是否有preventDefault阻止该次事件的默认动作。我们加上passive就是为了告诉浏览器，不用查询了，我们没用preventDefault阻止默认动作。
> 这里一般用在滚动监听，@scoll，@touchmove 。因为滚动监听过程中，移动每个像素都会产生一次事件，每次都使用内核线程查询prevent会使滑动卡顿。我们通过passive将内核线程查询跳过，可以大大提升滑动的流畅度

### 键盘按键修饰符

- `.enter`
- `.tab`
- `.delete`
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### 鼠标按钮修饰符

- `.left`
- `.right`
- `.middle`

### 系统修饰键

可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。

- `.ctrl`

- `.alt`

- `.shift`

- `.meta`         对应command 键 (⌘)和Windows 徽标键 (⊞)

- `.exact`
```HTML
      <!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
      <button @click.ctrl="onClick">A</button>
      
      <!-- 有且只有 Ctrl 被按下的时候才触发 -->
      <button @click.ctrl.exact="onCtrlClick">A</button>
      
      <!-- 没有任何系统修饰符被按下的时候才触发 -->
      <button @click.exact="onClick">A</button>
```
### v-model修饰符

- `.lazy`      在“change”时而非“input”时更新
- `.number`  自动将输入值转为number类型
- `.trim`          自动过滤用户输入的首尾空白字符

### Vue全局导入基础组件的示例代码

详述👉🏻[chrisvfritz/vue-enterprise-boilerplate](https://github.com/chrisvfritz/vue-enterprise-boilerplate/blob/master/src/components/_globals.js)
```JS
    import ElTableColumnPro from './ElTableColumnPro.vue'
    
    ElTableColumnPro.install = function (Vue) {
    	Vue.component(ElTableColumnPro.name, ElTableColumnPro)
    }
    
    if (window.Vue) {
    	window.Vue.use(ElTableColumnPro)
    }
    export default ElTableColumnPro
```
### 插槽
```HTML
    <!-- 插槽模板 -->
    <div class="container">
      <header>
        <slot name="header"></slot>
      </header>
      <main>
        <slot></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>
    <!-- 使用 -->
    <base-layout>
      <template v-slot:header>
        <h1>Here might be a page title</h1>
      </template>
    
      <template v-slot:default>
        <p>A paragraph for the main content.</p>
        <p>And another one.</p>
      </template>
    
      <template v-slot:footer>
        <p>Here's some contact info</p>
      </template>
    </base-layout>
```
### inheritAttrs , $attrs , $listeners

总结一句话: $attrs存储非prop特性，inheritAttrs控制vue对非prop特性默认行为

详述👉🏻[Vue inheritAttrs, $attrs,$listeners  详解 ](https://www.notion.so/Vue-inheritAttrs-attrs-listeners-f3c6e8175b2e4fd6a5d973b7b869c1a8)

### 依赖注入 provide 和 inject
```JS
    // 父组件
    provide: function () {
      return {
        getMap: this.getMap
      }
    }
    // 父组件下的所有组件(子,孙,重孙...)
    inject: ['getMap']
```
详述👉🏻[Vue的组件通信之Provide与Inject机制](https://www.notion.so/Vue-Provide-Inject-626f1d6c4841455b89b2ec3aa3388f22)

### 程序化的事件侦听器

（懵逼的定义--其实就是创建实例和清除实例放在一起,简化操作和无用的代码）

- 通过 `$on(eventName, eventHandler)` 侦听一个事件
- 通过 `$once(eventName, eventHandler)` 一次性侦听一个事件
- 通过 `$off(eventName, eventHandler)` 停止侦听一个事件

详述👉🏻[vue 程序化的事件侦听器](https://www.notion.so/vue-bf198384038841b7a5ae6b35ffe75cf0)

### 强制更新 $forceUpdate

如果你发现你自己需要在 Vue 中做一次强制更新，99.9% 的情况，是你在某个地方做错了事。

你可能还没有留意到数组和对象的变更检测注意事项，或者你可能依赖了一个未被 Vue 的响应式系统追踪的状态。

然而，如果你已经做到了上述的事项仍然发现在极少数的情况下需要手动强制更新，那么你可以通过 $forceUpdate 来做这件事。

### 通过 v-once 创建低开销的静态组件

渲染普通的 HTML 元素在 Vue 中是非常快速的，但有的时候你可能有一个组件，这个组件包含了大量静态内容。在这种情况下，你可以在根元素上添加 v-once 特性以确保这些内容只计算一次然后缓存起来,并不再更新. ( 正常情况下不会用到 )