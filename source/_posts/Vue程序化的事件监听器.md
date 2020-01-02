---
title: Vue程序化的事件监听器
author: Tomatoro
comments: true
tags:
  - Vue
top: 0
abbrlink: a85e4219
date: 2019-12-31 15:00:55
---
> 某些第三方插件必须在当前组件卸载后清除该实例（比如说百度的富文本框UEditor 如果不清除再次在下个组件使用时会有bug， 类似于小程序的语音实例，必须离开页面的时候销毁当前语音实例，不然语音会一直播放）

<!-- more -->

方案1：
```JS
    data() {            
        return {                              
            timer: null  // 定时器名称          
        }        
    },
```
然后这样使用定时器：
```JS
    this.timer = setIterval (() => {
        // 某些操作
    }, 1000)
```
最后在beforeDestroy()生命周期内清除定时器：
```JS
    beforeDestroy() {
        clearInterval(this.timer);        
        this.timer = null;
    }
```
次方案有两点不好的地方，引用尤大的话来说就是：

### (1)它需要在这个组件实例中保存这个数据timer，这是多余的。

### (2)我们的建立定时器代码独立于我们的清理代码（定时器需要卸载页面的时候卸载），这使得我们比较难于程序化的清理我们建立的所有东西（意思是执行代码和清除代码不在一起）。

方案2： 该方法是通过$once这个事件侦听器器在定义完定时器之后的位置来清除定时器。以下是完整代码：
```JS
    mounted: function () {
      const timer = setInterval(() =>{                    
          // 某些定时器操作                
      }, 500);            
      // 通过$once来监听定时器，在beforeDestroy钩子可以被清除。
      this.$once('hook:beforeDestroy', () => {            
          clearInterval(timer);                                    
      })
    }
```
### 简单来说就是把所有创建实例和需要销毁的实例代码放在一起了，放在一起而已(创建实例和销毁实例)........

### 甚至可以在一个页面开启多个这种创建实例和销毁实例
```JS
    mounted: function () {
      this.attachDatepicker('startDateInput')
      this.attachDatepicker('endDateInput')
    },
    methods: {
      attachDatepicker: function (refName) {
        var picker = new Pikaday({
          field: this.$refs[refName],
          format: 'YYYY-MM-DD'
        })
    
        this.$once('hook:beforeDestroy', function () {
          picker.destroy()
        })
      }
    }
```
综合来说，我们更推荐使用方案2，使得代码可读性更强，一目了然。如果不清楚`$once、$on、$off`的使用，这里送上官网的地址教程，[在程序化的事件侦听器那里](https://link.juejin.im/?target=https%3A%2F%2Fcn.vuejs.org%2Fv2%2Fguide%2Fcomponents-edge-cases.html%23%25E7%25A8%258B%25E5%25BA%258F%25E5%258C%2596%25E7%259A%2584%25E4%25BA%258B%25E4%25BB%25B6%25E4%25BE%25A6%25E5%2590%25AC%25E5%2599%25A8)。