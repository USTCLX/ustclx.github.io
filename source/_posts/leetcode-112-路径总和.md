---
title: leetcode 112 路径总和
date: 2020-11-15 11:30:23
tags: leetcode
---

# 112 路径总和

## 示例

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

说明: 叶子节点是指没有子节点的节点。

示例:
给定如下二叉树，以及目标和 sum = 22，

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1

返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

## 问题分析

### Round One

初看之下，让我想到了二叉树的最大深度，接着自然想到了 dfs 深度优先搜索，然后使用递归来实现代码。
但是这里需要使用一个变量来记录当前路径和，然后想到了使用全局变量 count（事后证明这并不是一个最佳实践），如此一来，再全局变量增加之后，还需要利用回溯来恢复 count，无疑增加了复杂度。

### Round Two

其实对于类似的问题，之所以使用全局变量，是基于我的一个刻板印象--count 是值类型，不是引用类型，无法直接操作修改 count 的值。
但是后来发现，并不需要直接修改 count 的值，只需要操作传递给下一层递归的 count 就可以了。相当于每一次的递归调用里 count 都是新的。

## 代码实现

### 使用 dfs 递归

最初想到的方案，使用 count 来记录当前的累加值。这里需要记住几种特殊 case

- 空树需要返回 false
- [1,2] 1 这个 case，虽然树根为 1，但是树根没有左子树，因此不符合题意（从根节点到叶子节点），因此也是返回 false

所以，真正的叶子节点就是本身不为 null，left 和 right 都是 null。

```typescript
function calcSum(node: TreeNode | null, sum: number, count: number): boolean {
  if (!node) return false;

  count += node.val;
  if (!node.left && !node.right) {
    return count === sum;
  }

  return calcSum(node.left, sum, count) || calcSum(node.right, sum, count);
}

function hasPathSum(root: TreeNode | null, sum: number): boolean {
  return calcSum(root, sum, 0);
}
```

### 使用 dfs 递归-优化传参

看了 leetcode 的留言之后，发现了更好的方式，递减 sum，这样避免了一个多余的 count 参数。
这种方式代码量更少，更加简洁。

```typescript
function hasPathSum(root: TreeNode | null, sum: number): boolean {
  // 没有根节点，直接false
  if (!root) return false;

  // 已经是叶子节点
  if (!root.left && !root.right) {
    return root.val === sum;
  }

  // 探索左右子树
  return (
    hasPathSum(root.left, sum - root.val) ||
    hasPathSum(root.right, sum - root.val)
  );
}
```

### 使用 bfs

使用 bfs 来遍历二叉树，自然要使用队列了。这里需要使用一个额外的队列来存储结果数据。并且数据队列与节点队列保持同步。

```typescript
function hasPathSum(root: TreeNode | null, sum: number): boolean {
  if (!root) return false;
  const queue = [root]; // 遍历节点
  const result = [root.val]; // 记录路径和
  while (queue.length) {
    const node = queue.pop();
    const value = result.pop();

    if (!node) continue;

    if (!node.left && !node.right) {
      if (sum === value) {
        return true;
      }
    }
    if (node.left) {
      queue.push(node.left);
      result.push((value as number) + node.left.val);
    }
    if (node.right) {
      queue.push(node.right);
      result.push((value as number) + node.right.val);
    }
  }
  return false;
}
```
