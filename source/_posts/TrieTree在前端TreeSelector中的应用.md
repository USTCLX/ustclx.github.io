---
title: TrieTree在前端TreeSelect中的应用
date: 2021-10-26T11:32:52+08:00
tags:
  - 算法与数据结构
  - 前端开发中的算法
---

## TreeSelect

Antd 作为最有最优秀的 React 开源组件库之一，被广泛的使用。最近笔者在工作中使用`TreeSelect`组件。它的使用非常简单，可以接收下面这样的数据结构，来生成一个[树型选择器](https://ant.design/components/tree-select-cn/)。

```typescript
const treeData = [
  {
    title: "Node1",
    value: "0-0",
    children: [
      {
        title: "Child Node1",
        value: "0-0-1",
      },
      {
        title: "Child Node2",
        value: "0-0-2",
      },
    ],
  },
  {
    title: "Node2",
    value: "0-1",
  },
];
```

## 场景

比如后端吐出给前端的数据如下。

```typescript
const rawData = ["a.bb", "a.cc", "a.dd.eee", "h.ff.llll"];
```

要求根据`.`作为分层级隔符，将数据展示为树形选择器。因此需要将数据转为如下形式。

```typescript
const treeData = [
  {
    title: "*",
    value: "*",
    children: [
      {
        title: "a",
        value: "a.*",
        children: [
          {
            title: "bb",
            value: "a.bb",
          },
          {
            title: "cc",
            value: "a.cc",
          },
          {
            title: "dd",
            value: "a.dd.*",
            children: [
              {
                title: "eee",
                value: "a.dd.eee",
              },
            ],
          },
        ],
      },
      {
        title: "h",
        value: "h.*",
        children: [
          {
            title: "ff",
            value: "h.ff.*",
            children: [
              {
                title: "llll",
                value: "h.ff.llll",
              },
            ],
          },
        ],
      },
    ],
  },
];
```

## 前缀树（字典树）

关于前缀树的文章可以参考[leetcode 208 实现 Trie 树](https://ustclx.github.io/2020/11/15/leetcode-208-%E5%AE%9E%E7%8E%B0Trie%E6%A0%91/)。

这种 case，根据共同的前缀，层层递进形成的数据结构，正是前缀树的应用场景了。那么我们如何将`rawData` 转换为`treeData`呢？笔者给了一个自己实现，如下

```typescript
interface TreeData {
  title: string;
  value: string;
  children?: TreeData[];
}

export class TrieNode {
  constructor(
    public value: string = "",
    public path: string = "",
    public isLeaf: boolean = false,
    public isRoot: boolean = false,
    public children: TrieNode[]
  ) {}
}

export class TrieTree {
  root: TrieNode;
  constructor(data: string[] = []) {
    this.root = new TrieNode("*", "", false, true, []);

    data.forEach((item) => {
      this.insert(item.split("."));
    });
  }

  findNode(node: TrieNode, key: string) {
    if (!key) return null;
    if (!node || !node.children?.length) return null;

    return node.children.find((child) => child.value === key);
  }

  insert(keys: string[]) {
    let node = this.root;
    const paths = [];
    for (const key of keys) {
      paths.push(key);
      let child = this.findNode(node, key);
      if (!child) {
        child = new TrieNode(key, paths.join("."), false, false, []);
        node.children.push(child);
      }
      node = child;
    }
    node.isLeaf = true;
  }

  getValue() {
    const treeData: TreeData[] = [];

    const dfs = (node: TrieNode, treeData: TreeData[]) => {
      if (!node) return;

      const data: TreeData = {
        title: node.value,
        value: node.isRoot
          ? node.value
          : node.isLeaf
          ? node.path
          : node.path + ".*",
      };

      if (node.children?.length) data.children = [];

      treeData.push(data);

      for (const child of node.children) {
        dfs(child, data.children);
      }
    };

    dfs(this.root, treeData);

    return treeData;
  }
}
```

## 结合 Antd 来使用

[codesandbox](https://codesandbox.io/s/quizzical-yonath-sbrns)
