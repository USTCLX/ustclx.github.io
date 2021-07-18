---
title: 使用React设计一个Form表单组件
date: 2021-07-18 17:18:44
tags:
  - react
---

通过设计一个 Form 组件，学习 React 中，`React.Children.forEach` 和 `React.CloneElement` 以及 `React.isValidElement` 这三个 API 的用法。

# 组件 API 设计

组件用法如下

```javascript
import { useRef } from "react";
import { Form, FormItem, Input } from "./Form";

export default function App() {
  const form = useRef();
  const onSubmit = () => {
    form.current.submit((data) => {
      console.log("data", data);
    });
  };
  const onReset = () => {
    form.current.reset();
  };
  return (
    <div className="App">
      <Form ref={form}>
        <FormItem name="name" label="姓名">
          <Input />
        </FormItem>
        <FormItem name="age" label="年龄">
          <Input />
        </FormItem>
        <FormItem name="sex" label="性别">
          <Input />
        </FormItem>
      </Form>

      <button onClick={onSubmit}>提交</button>
      <button onClick={onReset}>重置</button>
    </div>
  );
}
```

# 组件实现

```javascript
import React from "react";

export class Form extends React.Component {
  state = {
    formData: {},
  };

  submit = (cb) => {
    cb && cb({ ...this.state.formData });
  };

  reset = () => {
    const { formData } = this.state;
    Object.keys(formData).forEach((key) => {
      formData[key] = "";
    });
    this.setState({ formData });
  };

  setValue = (name, value) => {
    const { formData } = this.state;
    formData[name] = value;
    this.setState({ formData });
  };

  render() {
    const { children } = this.props;
    const { formData } = this.state;
    const renderChildren = [];

    React.Children.forEach(children, (child) => {
      if (child.type.displayName === "FormItem") {
        const { name } = child.props;
        renderChildren.push(
          React.cloneElement(
            child,
            {
              key: name,
              value: formData[name] || "",
              handleChange: this.setValue,
            },
            child.props.children
          )
        );
      }
    });

    return renderChildren;
  }
}

export class FormItem extends React.Component {
  static displayName = "FormItem";

  onChange = (value) => {
    const { name, handleChange } = this.props;
    handleChange && handleChange(name, value);
  };

  render() {
    const { label, value, children } = this.props;
    return (
      <div>
        <span>{label}</span>
        {React.isValidElement(children) &&
        children.type.displayName === "FormItem-Input"
          ? React.cloneElement(children, { onChange: this.onChange, value })
          : null}
      </div>
    );
  }
}

export class Input extends React.Component {
  static displayName = "FormItem-Input";
  render() {
    const { value, onChange } = this.props;
    return (
      <input
        value={value}
        onChange={(e) => {
          onChange && onChange(e.target.value);
        }}
      />
    );
  }
}
```
