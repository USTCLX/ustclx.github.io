---
title: leetcode-71-简化路径
date: 2020-11-21 22:57:38
tags: leetcode
---

# 71 路径总和

## 示例

示例 1：

输入："/home/"
输出："/home"
解释：注意，最后一个目录名后面没有斜杠。
示例 2：

输入："/../"
输出："/"
解释：从根目录向上一级是不可行的，因为根是你可以到达的最高级。
示例 3：

输入："/home//foo/"
输出："/home/foo"
解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。
示例 4：

输入："/a/./b/../../c/"
输出："/c"
示例 5：

输入："/a/../../b/../c//.//"
输出："/c"

## 问题分析

### Round One

一开始没有头绪，大概想到了依次读取字符串，然后利用栈。比如遇到`..`就 pop 一个元素。但是没有整理出完整的思路。

### Round Two

不啰嗦，直接看 leetcode 上的 discussion 吧。果然人家的思路清晰又简单。

- 使用'/'将路径字符串分割为路径数组。准备一个栈
- 遍历数组
  - 遇到 '' 直接 continue(分割了//)
  - 遇到 '.' 直接 continue(分割了/./)
  - 遇到'..' stack.pop() 需要返回上一级
  - 遇到普通字符串 stack.push() 进入一级

## 代码实现

```typescript
function simplifyPath(path: string): string {
  const arr = path.split("/");
  const stack: string[] = [];
  for (let str of arr) {
    if (str == "") {
      continue;
    } else if (str == ".") {
      continue;
    } else if (str == "..") {
      stack.pop();
    } else {
      stack.push(str);
    }
  }
  return `/${stack.join("/")}`;
}
```
