---
title: ReactNode ReactElement ReactNode和Component
date: 2022-02-06 11:18:45
tags:
  - react
---

本文尝试分析一下，ReactNode ReactElement 和 Component 之间的关系和区别。

## ReactNode

`ReactNode`是一种 typescript 的类型。在使用 antd 这样的组件库的时候，可以看到某些 prop 的类型是`ReactNode`，比如`PageHeader`组件的 extra 属性的类型就是`ReactNode`。

使用 vscode 的代码追踪，可以进入到`@types/react`这个包，查看`ReactNode`的类型定义如下：

```typescript
type ReactNode =
  | ReactChild
  | ReactFragment
  | ReactPortal
  | boolean
  | null
  | undefined;
```

可以发现，`ReactNode`是一个联合类型。比较符合我们平时写代码时的直觉。

其中的`ReactChild`可以继续跟踪，它的类型定义如下：

```typescript
type ReactText = string | number;
type ReactChild = ReactElement | ReactText;
```

可以发现`ReactChild`也是一个联合类型，其中一个类型就是`ReactElement`。因此可以发现，`ReactNode` 包含了`ReactElement`。

## ReactElement

`ReactElement`也是一种 typescript 类型。通常，我们在使用 typescript 书写`Function Component`的时候，如果加上了返回类型，会像这样：

```typescript
function App(): JSX.Element {
  return <div>App</div>;
}
```

追踪`JSX.Element`可以看到

```typescript
declare global {
  namespace JSX {
    interface Element extends React.ReactElement<any, any> {}
    // ...
  }
}
```

所以`JSX.Element`就是继承自`ReactElement`。查看`ReactElement`的类型定义如下：

```typescript
interface ReactElement<
  P = any,
  T extends string | JSXElementConstructor<any> =
    | string
    | JSXElementConstructor<any>
> {
  type: T;
  props: P;
  key: Key | null;
}
```

所以本质上`ReactElement`就是一个对象,包含三个属性`type`,`props`,`key`。

其中最特别的就是`type`这个属性，它是一个范型，可以是`string`或者`JSXElementConstructor`。
多说一句，type 如果是`string`，那么它可能就是类似于`div`这样的字符串，代表 HTML 的原生标签。再来看`JSXElementConstructor`，它的定义如下：

```typescript
type JSXElementConstructor<P> =
  | ((props: P) => ReactElement<any, any> | null)
  | (new (props: P) => Component<any, any>);
```

这也是一个联合类型，一个是函数，返回`ReactElement`。一个类，可以实例化为一个`Component`，也就是一个类组件。

其实`ReactElement`这个类型，就是代表了 React 中[Element](https://reactjs.org/docs/glossary.html#elements)这个概念。官方解释如下：

> React elements are the building blocks of React applications. One might confuse elements with a more widely known concept of “components”. An element describes what you want to see on the screen. React elements are immutable.Typically, elements are not used directly, but get returned from components.

```tsx
const element = <h1>Hello, world</h1>;
```

## Component

这里直接引用 [React 的官方文档](https://reactjs.org/docs/glossary.html#components)

> React components are small, reusable pieces of code that return a React element to be rendered to the page. The simplest version of React component is a plain JavaScript function that returns a React element:

只看函数组件，简而言之，函数组件就是可以返回一个`ReactElement`的函数。

## JSX

我们平时书写`Element`使用的是 JSX 语法，但是这种含有标签的语法（比如 `<h1>Hello, world<h1>`）并不是 js 语法的一部分。因此我们熟悉的 JSX 语句会被编译为标准的 js 代码。

> JSX is a syntax extension to JavaScript. It is similar to a template language, but it has full power of JavaScript. JSX gets compiled to React.createElement() calls which return plain JavaScript objects called “React elements”. To get a basic introduction to JSX see the docs here and find a more in-depth tutorial on JSX here.

使用 [Babel](https://babeljs.io/repl/) 可以让我们看到 JSX 编译后的代码。如下：

```jsx
function Button() {
  return <button>click</button>;
}

function App() {
  return (
    <div>
      <h1>Hello World</h1>
      <Button />
    </div>
  );
}
```

编译后

```jsx
"use strict";

function Button() {
  return /*#__PURE__*/ React.createElement("button", null, "click");
}

function App() {
  return /*#__PURE__*/ React.createElement(
    "div",
    null,
    /*#__PURE__*/ React.createElement("h1", null, "Hello World"),
    /*#__PURE__*/ React.createElement(Button, null)
  );
}
```
