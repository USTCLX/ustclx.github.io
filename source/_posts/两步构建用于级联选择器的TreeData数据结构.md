---
title: 极简代码构建用于级联选择器的TreeData数据结构
date: 2022-05-03 20:05:43
tags:
  - 数据结构
  - 前端应用
---

## 背景

工作中遇到了一个需求，使用 Antd 的 TreeSelect 组件实现一个 gitlab 仓库文件级联选择器。

后端返回的数据结构如下，这是一种扁平的数据结构，每个节点有三个属性。

```json
{
  "tree_nodes": [
    {
      "name": "admin",
      "type": "tree",
      "path": "ansible/plays/admin"
    },
    {
      "name": "test",
      "type": "blob",
      "path": "ansible/plays/admin/test.yml"
    },
    {
      "name": "audit",
      "type": "tree",
      "path": "ansible/plays/audit"
    },
    {
      "name": "config.yml",
      "type": "blob",
      "path": "ansible/plays/config.yml"
    },
    {
      "name": "debug_gather_facts.yml",
      "type": "blob",
      "path": "ansible/plays/debug_gather_facts.yml"
    }
  ]
}
```

但是 Antd 的 TreeSelect 组件的 treeData prop 接收的却是一种如下的树型数据结构。

```js
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

因此需要前端自己对数据进行一次转换。

## TrieTree 字典树

其实 Antd 的 treeData 是一种典型的字典树数据结构，如下图。因此问题就抽象成了字典树的构造问题。

![文件夹结构](https://raw.githubusercontent.com/USTCLX/images/main/static/20220503203020.png)

## 构造 treeData

构造 treeData 的代码如下，为方便起见，思路直接写在了注释里面，除了入口函数`buildTree`，只使用了两个工具函数 `insert` 和 `findChild`。

```typescript
interface RawDataNode {
  name: string;
  type: "tree" | "blob";
  path: string;
}

interface TreeNode {
  title: string;
  value: string;
  isLeaf: boolean;
  children: TreeNode[];
}

function findChild(node: TreeNode, title: string) {
  // 从当前node的children中寻找title相同的节点（如果有，代表之前已经创建过同名的节点了）。
  return node.children.find((node) => node.title === title);
}

function insert(node: TreeNode, data: RawDataNode) {
  // 2. 将path 拆解为数组，方便进行深度遍历操作，数组里面就是一层一层的文件（夹）名称
  const pathArr = data.path.split("/");
  // 3. 用于记录进入到第几层 path，方便在创建child时生成value
  const temp = [];

  // 4. 记录当前node
  let currentNode = node;

  // 5. 对pathArr进行遍历
  for (const pathSlice of pathArr) {
    // 6. 尝试从当前节点的children中找到title为pathSlice的节点
    let child = findChild(currentNode, pathSlice);
    temp.push(pathSlice);

    if (!child) {
      // 7. 如果没有找到，则创建一个新的child，并将它加入node的children
      child = {
        title: pathSlice,
        value: temp.join("/"),
        isLeaf: data.type === "blob",
        children: [],
      };
      currentNode.children?.push(child);
    }

    // 8. 将当前节点指向node，继续下一轮对pathArr的遍历
    currentNode = child;
  }
}

export function buildTree(tree_nodes: RawDataNode[]): TreeNode[] {
  const root: TreeNode = {
    title: "root",
    value: "root",
    isLeaf: false,
    children: [],
  };

  // 1. 对原始数据进行遍历并依次插入tree中
  tree_nodes.forEach((node) => insert(root, node));

  return root.children;
}
```
