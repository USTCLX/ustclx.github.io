---
title: 面试题：平铺protobuf字符串
date: 2022-09-27 22:42:44
tags:
  - 算法与数据结构
  - 面试
---

最近遇到了一道很有意思的面试题目，属于算法题的类型，但是确不是 leetcode 的原题，特此记录思考过程与解答方法。

## 题目描述

输入一个 protobuf 类型的字符串，如下所示

```protobuf
syntax = "proto";
package webcast.api.user;

/* METHOD: GET */
message ReportReasonRequestParams {
  int64 room_id = 1; // different reasons for different roo
  int64 ab_scene = 2; // ab scene, deprecated
}

message ReportReasonResponse{
  message ReasonData {
    int64 reason = 1;
    string reason_str = 2;
  }
  repeated ReasonData data = 1;
  Extra extra=2;
}
```

输出仍然是一个字符串，要求把嵌套的结构拉出来铺到外层。如下所示

```protobuf
syntax = "proto";
package webcast.api.user;

/* METHOD: GET */
message ReportReasonRequestParams {
  int64 room_id = 1; // different reasons for different roo
  int64 ab_scene = 2; // ab scene, deprecated
}

message ReasonData {
  int64 reason = 1;
  string reason_str = 2;
}

message ReportReasonResponse{
  repeated ReasonData data = 1;
  Extra extra=2;
}
```

## 思路

这个问题很容易联想到类似的问题，比如解析 json 字符串，有效的括号数目，那么就一般就需要识别一些关键的特征，比如"{"，以及利用栈这样的数据结构来存储数据（因为栈有先入后出的特性，那么里层的嵌套结构就会后入，然后先从栈中弹出来）。

但是这里值得注意的是，我们需要把特征定为 `message xx {` 而不是简单的 `{`, 因为提取的时候，`message` 关键字也是提取数据的一个整体。

所以我想到的是，使用正则表达式，先把所有的特征提取出来，记录特征字符串和它的索引，然后遍历整个字符串，在遇到一个特征字符串的时候，入栈，在遇到`}`的时候，将栈顶元素弹出，拼接到结果字符串中，在遇到其他字符的时候，将它拼接到栈顶元素中。

## 代码

```javascript
function parse(str) {
  let res = "";
  const stack = [""];

  // 收集所有的特征字符串和他们的索引
  // flags = [['message R {', 14]]
  const reg = /message.*{/g;
  let flags = [];
  let temp = null;
  while ((temp = reg.exec(str)) !== null) {
    flags.push([temp[0], temp.index]);
  }

  // 遍历字符串
  for (let i = 0; i < str.length; i++) {
    if (flags.length && i === flags[0][1]) {
      const [str] = flags.shift();
      stack.push([str]);
      i = i + str.length - 1;
    } else if (str[i] === "}") {
      let top = stack.pop();
      top = top + str[i] + (str[++i] ?? "");
      res += top;
    } else {
      stack[stack.length - 1] = stack[stack.length - 1] + str[i];
    }
  }

  if (stack.length) {
    res = stack.pop() + res;
  }

  return res;
}

const str = `syntax = "proto";
package webcast.api.user;

/* METHOD: GET */
message ReportReasonRequestParams {
  int64 room_id = 1; // different reasons for different roo
  int64 ab_scene = 2; // ab scene, deprecated
}

message ReasonData {
  int64 reason = 1;
  string reason_str = 2;
}

message ReportReasonResponse{
  repeated ReasonData data = 1;
  Extra extra=2;
}`;

console.log(parse(str));
```

输出为

```protobuf
syntax = "proto";
package webcast.api.user;

/* METHOD: GET */


message ReportReasonRequestParams {
  int64 room_id = 1; // different reasons for different roo
  int64 ab_scene = 2; // ab scene, deprecated
}
message ReasonData {
  int64 reason = 1;
  string reason_str = 2;
}
message ReportReasonResponse{
  repeated ReasonData data = 1;
  Extra extra=2;
}
```
