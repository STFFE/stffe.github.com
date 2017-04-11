---
layout: post
title: updatex，更原生的 Immutable 操作方式
category: Javascript
date: 2017-04-06
author: "walkon"
homepage: "https://github.com/tianjianchn"
---

## 来由
现在有那么多的 Immutability 库，像 [immutable](https://www.npmjs.com/package/immutable)、[seamless-immutable](https://www.npmjs.com/package/seamless-immutable)、[immutability-helper](https://www.npmjs.com/package/immutability-helper) 等，为什么还要造一个轮子呢？理由很感性，用着不怎么爽。要么像 immutable.js 那样，完全改变你的底层数据结构；要么就像 seamless-immutable 那样，你还得记住一堆额外的方法（如 `set`、`setIn` 等）。而我太懒，只想静静地用原生的方式来操作我的数据。  
我们知道最简单的方式就是使用 `...` 操作符来浅拷贝对象。像这样：
```js
const obj = { a: 1};
const obj2 = {...obj, a: 2}
```
写起来的确很爽，但是当你的对象层级比较深的时候：
```js
const obj = { a: { b: { c: { d: 1 } } } };
const obj2 = {...obj, a: {...obj.a, x: 't', b: { ...obj.a.b, x: 'j'}}}
// 或者
const obj2 = {...obj};
obj2.a = {...obj2.a};
obj2.a.x = 't';
```
就要提起精神了。一旦某个变量没控制好，导致直接修改原对象了，那就……都是泪啊。  
所以，归纳起来，有两个痛点：
1. 使用原生数据结构与操作方式，但降低 `...` 带来的繁琐度。
2. 确保不会意外地修改原对象。

这就是 [updatex](https://github.com/tianjianchn/updatex) 要做的事情。安全、高效地使用原生语法（如赋值 `obj.k = v`、`arr.push()`等）来达到 Immutability（不可变性）。

## 示例
```js
import updatex from 'updatex';

const state1 = { a: { b: { c: { d: 1 } } } };

const state2 = updatex(state1, (newState) => {
  const b = newState.select('a.b');
  b.x = 'j';// 这样写没问题，不会影响原先的对象
  newState.a.x = 't'; // 这样也是可以的！因为 a 也在 select 的路径里

  b.c.d = 2; // 但这样就不行了，会抛异常，因为 c 没有被 select
});

console.log(state); // { a: { b: { c: { d: 1 } } } }
console.log(state2): // { a: { x: 't', b: { x: 'j', c: { d: 1 } } } }
```
使用 [updatex](https://github.com/tianjianchn/updatex)，仅仅只是额外引入两个方法，`updatex()` 和 `select()`。没有 `set(k, v)`、`get(k)`、`asMutable()`、`$push`等需要额外记忆的方法与表达式，完全可以像操作原生对象与数组一样！

## API 使用说明

### updatex(obj, updater)
传入原对象（`obj`）和一个用于数据更新的函数（`updater`）。原对象将会被冻结，同时浅拷贝出一份新对象（`newObj`），传给 `updater`。所有的修改应都发生在 `newObj` 上。在 `updater` 执行完后，如果 `newObj` 没有任何修改，则返回原对象；否则返回新对象，同时新对象也会被冻结。
```js
updatex(obj, (newObj) => {
  newObj.x = 1;
})
```

### select(path)
在 `updater` 里，我们可以直接修改 `newObj`。但是如果要修改它的子对象（如 `newObj.a.b`)，就需要**预先**选择修改范围。这就是 `select` 的作用。给它提供一个路径，所有该路径上的节点都会被浅拷贝，这样就可以像往常一样操作对象了。
```js
updatex(obj, (newObj) => {
  newObj.select('a.b'); // 或者 newObj.select(['a', 'b'])
  newObj.a.x = 1;
  newObj.a.b.x = 1;
})
```

## 原理
结合前面的来由与示例，[updatex](https://github.com/tianjianchn/updatex) 的内部机制已经有一个轮廓了。
1. 当调用 `updatex(obj, updater)` 时，通过 `Object.freeze` 冻结整个原对象。这样任何对原对象的误操作（修改）都会抛出异常。
2. 当调用 `select(path)` API 时，整个路径（`path`）内的节点都会通过 `...` 来进行浅拷贝。这样你就可以对路径上的任何节点使用原生语法来操作数据了。

## 特性
1. 默认情况下，冻结只会发生在开发环境。在生产环境（`process.env.NODE_ENV=production`）会自动停用以提升部分性能（以及避免某些环境不支持 `Object.freeze`）。一个在开发环境上充分测试的代码，停用冻结应该不会导致生产环境上意外修改的情况发生。
2. 在 `updater` 返回时，会检查所有 `select` 的路径。如果有过度选择（over-select，即选取了但值最终没有发生变化）的话，会有警告。
3. `updater` 里的所有修改操作可以视作处于批量模式下，而不是修改一次就复制一次对象。自然，也就不需要重新赋值（像 `obj = obj.set()`)，不需要 `return newObj` 了
