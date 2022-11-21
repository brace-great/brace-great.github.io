---
title: Vue3新特性:Teleport组件
date: 2022-2-7 21:31:19
tags: Vue3
categories: Vue
---

# Teleport

`<Teleport>` 是一个内置组件，它可以将一个组件内部的一部分模板“传送”到该组件的 DOM 结构外层的位置去。

## 基本用法

应用场景：一个组件模板的一部分在逻辑上从属于该组件，但从整个应用视图的角度来看，它在 DOM 中应该被渲染在整个 Vue 应用外部的其他地方。如模态框。

template

```
<button @click="open = true">Open Modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>
```

`<Teleport>` 接收一个 `to` prop 来指定传送的目标。`to` 的值可以是一个 CSS 选择器字符串，也可以是一个 DOM 元素对象。这段代码的作用就是告诉 Vue“把以下模板片段**传送到 `body`** 标签下”。

你可以点击下面这个按钮，然后通过浏览器的开发者工具，在 `<body>` 标签下找到模态框元素：

Open Modal

我们也可以将 `<Teleport>` 和 [`<Transition>`](https://cn.vuejs.org/guide/built-ins/transition.html) 结合使用来创建一个带动画的模态框。你可以看看[这个示例](https://cn.vuejs.org/examples/#modal)。

TIP

`<Teleport>` 挂载时，传送的 `to` 目标必须已经存在于 DOM 中。理想情况下，这应该是整个 Vue 应用 DOM 树外部的一个元素。如果目标元素也是由 Vue 渲染的，你需要确保在挂载 `<Teleport>` 之前先挂载该元素。

## 搭配组件使用

`<Teleport>` 只改变了渲染的 DOM 结构，它不会影响组件间的逻辑关系。也就是说，如果 `<Teleport>` 包含了一个组件，那么该组件始终和这个使用了 `<teleport>` 的组件保持逻辑上的父子关系。传入的 props 和触发的事件也会照常工作。

这也意味着来自父组件的注入也会按预期工作，子组件将在 Vue Devtools 中嵌套在父级组件下面，而不是放在实际内容移动到的地方。

## 禁用 Teleport

在某些场景下可能需要视情况禁用 `<Teleport>`。举例来说，我们想要在桌面端将一个组件当做浮层来渲染，但在移动端则当作行内组件。我们可以通过对 `<Teleport>` 动态地传入一个 `disabled` prop 来处理这两种不同情况。

template

```
<Teleport :disabled="isMobile">
  ...
</Teleport>
```

这里的 `isMobile` 状态可以根据 CSS media query 的不同结果动态地更新。

## 多个 Teleport 共享目标

一个可重用的模态框组件可能同时存在多个实例。对于此类场景，多个 `<Teleport>` 组件可以将其内容挂载在同一个目标元素上，而顺序就是简单的顺次追加，后挂载的将排在目标元素下更后面的位置上。

比如下面这样的用例：

template

```
<Teleport to="#modals">
  <div>A</div>
</Teleport>
<Teleport to="#modals">
  <div>B</div>
</Teleport>
```

渲染的结果为：

html

```
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```
