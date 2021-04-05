---
title: leetcode-n数之和系列
date: 2021-03-26 09:17:51
tags: leetcode
---

# N 数之和系列

## 两数之和

给定一个数组，一个 target，找到数组中所有两个数字之和为 target 的组合。这里为了后面 N 数之和做准备，不是返回一个组合。

### 代码

```typescript
function twoSum(nums: number[], target: number): number[][] {
  // 先排序数组
  nums.sorts((a, b) => a - b);

  // 双指针遍历
  let left = 0;
  let right = nums.length - 1;
  const res = [];
  while (left < right) {
    const lVal = nums[left];
    const rVal = nums[right];
    if (lVal + rVal === target) {
      res.push([lVal, rVal]);
      // 排除重复的
      while (nums[left] === lVal) left++;
      while (nums[right] === rVal) right--;
    } else if (lVal + rVal < target) {
      left++;
    } else {
      right--;
    }
  }
  return res;
}
```

## 三数之和

有了两数之和，三数之和的计算就可以依赖现有的两数之和的函数，但是要稍微改造一下。

### 代码

```typescript
// 更加泛型（通用）的两数之和函数
// 默认数组已经排序
function twoSum(nums: number[], target: number, start: number): number[][] {
  // 双指针遍历
  let left = start;
  let right = nums.length - 1;
  const res = [];
  while (left < right) {
    const lVal = nums[left];
    const rVal = nums[right];
    if (lVal + rVal === target) {
      res.push([lVal, rVal]);
      // 排除重复的
      while (nums[left] === lVal) left++;
      while (nums[right] === rVal) right--;
    } else if (lVal + rVal < target) {
      left++;
    } else {
      right--;
    }
  }
  return res;
}

function threeSum(nums: number[]): number[][] {
  if (nums.length < 3) return [];

  nums.sort((a, b) => a - b);
  const res = [];
  for (let i = 0; i < nums.length; i++) {
    // 遍历第一个数字，下面要求两数之和，和为target-nums[i]
    const newTarget = 0 - nums[i];
    const newStart = i + 1;
    const temp = twoSum(nums, newTarget, newStart);
    for (let j = 0; j < temp.length; j++) {
      res.push([nums[i], ...temp[j]]);
    }
    // 去除重复的结果
    while (nums[i] === nums[i + 1]) i++;
  }

  return res;
}
```

## N 数之和

有了上面的例子，继而推演出 N 数之和的通用方法，也就有了思路。每次确定一个数字，最终只剩两个数字的时候，就是调用两数之和的时候。需要注意的是，常规的想法是 N 是几，就进行 N-1 层遍历。这里 N 是未知的。不过可以使用递归，来一层一层的减少 N

### 代码

```typescript
// 更加泛型（通用）的两数之和函数
// 默认数组已经排序
function twoSum(nums: number[], target: number, start: number): number[][] {
  // 双指针遍历
  let left = start;
  let right = nums.length - 1;
  const res = [];
  while (left < right) {
    const lVal = nums[left];
    const rVal = nums[right];
    if (lVal + rVal === target) {
      res.push([lVal, rVal]);
      // 排除重复的
      while (nums[left] === lVal) left++;
      while (nums[right] === rVal) right--;
    } else if (lVal + rVal < target) {
      left++;
    } else {
      right--;
    }
  }
  return res;
}

// 这里数组已经排序
function nSum(
  nums: number[][],
  target: number,
  n: number,
  start: number,
  visited: number[],
  ans: number[][] = []
): number[][] {
  // 注意这里是大于
  if (n + start > nums.length) return [];

  if (n === 2) {
    // 可以调用两数之和
    const temp = twoSum(nums, target, start);
    for (let i = 0; i < temp.length; i++) {
      ans.push([...visited, ...temp[i]]);
    }
    return;
  } else {
    // n大于2
    for (let i = start; i < nums.length; i++) {
      const newTarget = target - nums[i];
      const newN = n - 1;
      const newStart = i + 1;
      const newVisited = [...visited, nums[i]];
      nSum(nums, newTarget, newN, newStart, newVisited, ans);
      // 去除重复的
      while (nums[i] === nums[i + 1]) i++;
    }
  }
  return ans;
}

// 例如求四数之和
function fourSum(nums: number[], target: number): number[][] {
  nums.sort((a, b) => a - b);
  return nSum(nums, target, 4, 0, []);
}
```
