---
title: 【转】为什么我喜欢JavaScript的Optional Chaining
author: Tomatoro
comments: true
tags: [ES6]
top: 0
date: 2019-08-27 11:37:19
---

### 前言
> JavaScript 的特性极大地改变了你的编码方式。从 ES2015 开始，对我代码影响最多的功能是解构、箭头函数、类和模块系统。
> 截至 2019 年 8 月，一项新提案 optional chaining 达到了第3阶段，这将是一个很好的改进。Optional Chaining 改变了从深层对象结构访问属性的方式。
> 下面让我们来看看 optional chaining 是如何通过在深度访问可能缺少的属性时删除样板条件和变量来简化代码的。

<!-- more -->

-----
### 1. 问题

由于 JavaScript 的动态特性，对象可以有区别很大的嵌套对象结构。

通常，你在以下情况下处理此类对象：

*   获取远程 JSON 数据
*   使用配置对象
*   具有 optional 属性

虽然这为对象提供了支持不同结构数据的灵活性，但是在访问这些对象的属性时会增加复杂性。

`bigObject` 在运行时可以有不同的属性集：

```
// One version of bigObject
const bigObject = {
  // ...
  prop1: {
    //...
    prop2: {
      // ...
      value: 'Some value'
    }
  }
};

// Other version of bigObject
const bigObject = {
  // ...
  prop1: {
    // Nothing here   
  }
};

```

因此，你必须手动检查属性是否存在：

```
// Later
if (bigObject && 
    bigObject.prop1 != null && 
    bigObject.prop1.prop2 != null) {
  let result = bigObject.prop1.prop2.value;
}

```

这会产生很多样板代码。如果不需要写这些代码那就太好了。

让我们看看 optional chaining 如何解决这个问题，并减少样板条件。

------------
### 2. 轻松的深入访问属性

让我们设计一个保存电影信息的对象。该对象包含一个 `title` 属性，以及可选的 `director` 和 `actors`。

`movieSmall` 对象只包含 `title`，而 `movieFull` 包含完整的属性集：

```
const movieSmall = {
  title: 'Heat'
};

const movieFull = {
  title: 'Blade Runner',
  director: { name: 'Ridley Scott' },
  actors: [{ name: 'Harrison Ford' }, { name: 'Rutger Hauer' }]
};

```

让我们写一个获取导演名字的函数。请记住，`director` 属性可能会不存在：

```
function getDirector(movie) {
  if (movie.director != null) {
    return movie.director.name;
  }
}

getDirector(movieSmall); // => undefined
getDirector(movieFull);  // => 'Ridley Scott'

```

`if (movie.director) {...}` 条件用于验证 `director` 属性是否已定义。如果没有这个预防措施，在访问`movieSmall` 对象 `director` 的时候，JavaScript 会抛出错误 `TypeError: Cannot read property 'name' of undefined`。

这是使用新的 optional chaining 功能的正确位置，并删除 `movie.director` 的存在验证。新版本的`getDirector()`看起来要短得多：

```
function getDirector(movie) {
  return movie.director?.name;
}

getDirector(movieSmall); // => undefined
getDirector(movieFull);  // => 'Ridley Scott'

```

在表达式 `movie.director?.name` 中你可以找到 `?.`： optional chaining 运算符。

在 `movieSmall` 的情况下，如果属性 `director` 丢失了。那么 `movie.director?.name` 的计算结果为 `undefined`。 optional chaining 运算符可防止抛出 `TypeError:Cannot read property 'name' of undefined`。

相反，在 `movieFull` 的情况下，属性 `director` 可用。 `movie.director?.name` 的值为 `'Ridley Scott'`.。

简单来说，代码片段：

```
let name = movie.director?.name;

```

相当于：

```
let name;
if (movie.director != null) {
  name = movie.director.name;
}

```

`?.` 通过减少 2 行代码简化了 `getDirector()` 函数。这就是我喜欢 optional chaining 的原因。

### 2.1 数组项

但是 optional chaining 功能可以做更多的事情。你可以在同一表达式中使用多个 optional chaining 运算符。甚至可以使用它来安全地访问数组项目！

接下来的任务是编写一个返回电影主角名字的函数。

在 movie 对象中，`actors` 数组可以为空甚至丢失，因此你必须添加其他条件：

```
function getLeadingActor(movie) {
  if (movie.actors && movie.actors.length > 0) {
    return movie.actors[0].name;
  }
}

getLeadingActor(movieSmall); // => undefined
getLeadingActor(movieFull);  // => 'Harrison Ford'

```

`if (movie.actors && movies.actors.length > 0) {...}` 条件需要确保 `movie` 中包含 `actors` 属性，并且此属性至少有一个 actor。

通过使用 optional chaining，此任务很容易解决：

```
function getLeadingActor(movie) {
  return movie.actors?.[0]?.name;
}

getLeadingActor(movieSmall); // => undefined
getLeadingActor(movieFull);  // => 'Harrison Ford'

```

`actors?.` 确保 `actors` 属性存在。 `[0]?.` 确保第一个 actor 存在于列表中。很好！

-------------
### 3. nullish 合并

名为 [nullish coalescing operator](https://github.com/tc39/proposal-nullish-coalescing) 的新提案建议用 `??` 处理 `undefined`或`null`，将它们默认为特定的值。

如果 `variable` 是`undefined`或`null`，则表达式 `variable ?? defaultValue` 的结果为`defaultValue`， 否则表达式的值为`variable` 的值。

```
const noValue = undefined;
const value = 'Hello';

noValue ?? 'Nothing'; // => 'Nothing'
value   ?? 'Nothing'; // => 'Hello'

```

当评估为 `undefined` 时，Nullish 合并可以通过默认值来改进 optional chaining。

例如，当 movie 对象中没有 actor 时，让我们改变 `getLeading()` 函数返回 `"Unknown actor"`：

```
function getLeadingActor(movie) {
  return movie.actors?.[0]?.name ?? 'Unknown actor';
}

getLeadingActor(movieSmall); // => 'Unknown actor'
getLeadingActor(movieFull);  // => 'Harrison Ford'

```

----------------------------
### 4. optional chaining 的 3 种形式

可以用以下 3 种形式使用 optional chaining 。

第一种形式 `object?.property` 用于访问静态属性：

```
const object = null;
object?.property; // => undefined

```

第二种形式 `object？.[expression]` 用于访问动态属性或数组项：

```
const object = null;
const name = 'property';
object?.[name]; // => undefined

const array = null;
array?.[0]; // => undefined

```

最后，第三种形式 `object?.([arg1,[arg2,...]])` 执行一个对象方法：

```
const object = null;
object?.method('Some value'); // => undefined

```

如果需要，可以通过组合这些表单来创建长的可选链：

```
const value = object.maybeUndefinedProp?.maybeNull()?.[propName];

```

--------------------------
### 5. 短路：停止于 _null/undefined_

有关 optional chaining 运算符的有趣之处在于，只要在其左侧 `leftHandSide?.rightHandSide` 中遇到无效值，右侧访问器的评估就会停止。这称为短路。

我们来看一个例子：

```
const nothing = null;
let index = 0;

nothing?.[index++]; // => undefined
index;              // => 0

```

`nothing` 保持一个 nullish 值，因此 optional chaining 评估为 `undefined` ，并跳过右侧访问器的评估。因为 `index` 编号不会增加。

-------------------------
### 6. 何时使用 optional chaining

一定要克制使用 optional chaining 操作符访问任何类型属性的冲动：这将会导致误导使用。下一节将介绍何时正确使用它。

### 6.1 访问可能无效的属性

`?.` 必须只在可能无效的属性附近使用：`maybeNullish?.prop`。在其他情况下，使用旧的属性访问器：`.property` 或 `[propExpression]`。

回想一下 movie 对象。查看表达式 `movie.director?.name`，因 为`director` 可以是 `undefined`，在`director`属性附近使用 optional chaining 运算符是正确的。

相反，使用 `?.` 来访问电影标题是没有意义的：`movie?.title`。movie 对象不会是无效的。

```
// Good
function logMovie(movie) {
  console.log(movie.director?.name);
  console.log(movie.title);
}

// Bad
function logMovie(movie) {
  // director needs optional chaining
  console.log(movie.director.name);

  // movie doesn't need optional chaining
  console.log(movie?.title);
}

```

### 6.2 通常有更好的选择

以下函数 `hasPadding()` 接受带有可选 `padding` 属性的样式对象。 `padding` 具有可选属性`left`、`top`、`right`、`bottom`。

下面尝试使用 optional chaining 运算符：

```
function hasPadding({ padding }) {
  const top = padding?.top ?? 0;
  const right = padding?.right ?? 0;
  const bottom = padding?.bottom ?? 0;
  const left = padding?.left ?? 0;
  return left + top + right + bottom !== 0;
}

hasPadding({ color: 'black' });        // => false
hasPadding({ padding: { left: 0 } });  // => false
hasPadding({ padding: { right: 10 }}); // => true

```

虽然函数正确地确定元素是否具有填充，但是对于每个属性都使用 optional chaining 是非常困难的。

更好的方法是使用对象扩展运算符将填充对象默认为零值：

```
function hasPadding({ padding }) {
  const p = {
    top: 0,
    right: 0,
    bottom: 0,
    left: 0,
    ...padding
  };
  return p.top + p.left + p.right + p.bottom !== 0;
}

hasPadding({ color: 'black' });        // => false
hasPadding({ padding: { left: 0 } });  // => false
hasPadding({ padding: { right: 10 }}); // => true

```

在我看来，这个版本的 `hasPadding()` 更容易阅读。

-----------
### 7. 为什么我喜欢它？

我喜欢 optional chaining 运算符，因为它允许从嵌套对象轻松访问属性。它可以减少通过编写样板文件来验证来自访问器链的每个属性访问器上无效值的工作。

当 optional chaining 与无效合并运算符组合时，你可以获得更好的结果，能够更轻松地处理默认值。

> 转载信息
> 作者：Dmitri Pavlutin
> 翻译：疯狂的技术宅
> 原文：[https://dmitripavlutin.com/ja...](https://dmitripavlutin.com/javascript-optional-chaining/)
> 来源：思否