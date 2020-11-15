---
title: leetcode 139 单词拆分
date: 2020-11-15 11:34:51
tags: leetcode
---

# 139 单词拆分

## 示例 1：

输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

## 问题分析

### Round One

1. 遍历收集所有的 s 可以被拆分的组合
2. 遍历组合，判断是否有某个组合中的所有子串都在 dict 中

这种解法看上去没有问题，关键点在于收集所有的 s 可以被拆分的组合，这里显然需要深度优先和递归。但是如何实现第 1 步，却把我卡住了。没有理清代码的关系。一直卡在了那里。
这里暴露了我的一个问题，easy fall into a promble and can't walk out.
不知道是不是其他人也有会有这个问题呢？其实在问题卡住的时候，无非是两种做法。

1. 死磕，把卡住的问题解决（这样也不能算是卡住了）
2. 换一种思路，前提是能想到别的思路

综合上面的两个解决方案，不难发现，本质无非是你足够聪明（可以把卡住的问题解决掉），或是你见多识广（可以换一种方式解决问题）。无论哪一种，都是能力的体现。
不得不说，是否聪明比较客观，无法改变，但是思路的转化是可以通过经验的积累来实现的。

### Round Two

在参考了 leetcode 上的 solutions 之后，获得了很大的启发。将问题分解，先找到一个，然后再对子问题求解，如果子问题也满足条件，那么这个组合就是 ok 的。

1. 一个指针遍历字符串，验证指针前半子串 s1 是否在 dict 中，不存在就移动指针。
2. 如果存在，那么说明已经分解出一个单词，然后对指针的后半子串 s2 进行同样的验证操作（递归）。
3. 直到后半子串 s2 的长度为 0（递归基），说明前面的子串都可以在 dict 中找到了。

### Round Three

上面的解法基本解决了问题，但是存在很多重复计算。举个例子，一个字符串`abcd`，如过

1. a | bcd
   1. a | b | cd
      1. a | b | c | d
   2. a | bc | d
      1. a | b | c | d
   3. a | bcd
2. ab | cd
   1. ab | c | d
   2. ab | cd

可以看出 `c` 和 `d` 的切分进行了很多次。对于重复的计算，简单的做法就是记录一下，这个子串是否已经切分过了。如果切分过了，就直接返回

## 代码实现

### 非优化

```typescript
function wordBreak(s: stirng, dict: string[]): boolean {
  if (s.length == 0) return true;

  for (let i = 0; i < s.length; i++) {
    // 将s分解。[0,i]为s1，[i+1,end]为s2
    const s1 = s.slice(0, i + 1);
    const s2 = s.slice(i + 1);
    // 如果s1在dict中，再去验证s2
    if (dict.inclueds(s1) && wordBread(s2, dict)) {
      // 找到了一组
      return true;
    }
  }
  // 没找到
  return false;
}
```

### 优化 - memo 记录

```typescript
function canBreak(str: string, dict: string[], memo: Set<string>) {
  if (str.length === 0) return true;

  // 当前字符串已经尝试拆分过了，没有返回true，代表没有解
  if (memo.has(str)) return false;

  for (let i = 0; i < str.length; i++) {
    const s1 = str.slice(0, i + 1);
    const s2 = str.slice(i + 1);

    if (dict.includes(s1) && canBreak(s2, dict, memo)) {
      return true;
    }
  }

  // 当前子串无法拆分
  memo.add(str);

  return false;
}

function wordBreak(s: string, dict: string[]) {
  const memo = new Set<string>();
  return canBreak(s, dict, memo);
}
```

### 优化 - dp

1. 定义状态 dp[i]代表子串 s[0:i-1]是否可以被拆分。
2. `dp[i] = dp[j] && dict.has(s.slice(j,i)) j[0,i-1]`
   1. 将 s[0:i-1]以 j 分为两部分，如果 dp[j]为 true，说明 s[0,j-1]可以拆分
   2. 如果 s[j:i-1]在 dict 中，则说明这两部分合起来也可以拆分(s[0:j-1],s[j,i-1])
3. 最终求的就是 dp[s.length];

举个例子：
`s=leetcode; dict=['leet','code']; 那么dp[3]=true // 因为s[0,3]='leet'; 而且s[4,7]='code' 存在于dict中所以可以被拆分`

```typescript
function wordBreak(s: string, dict: string[]): boolean {
  const len = s.length;
  const dp = Array(s.length + 1).fill(false);
  dp[0] = true;

  for (let i = 1; i <= len; i++) {
    for (let j = i - 1; j >= 0; j--) {
      const suffix = s.slice(j, i); // 后缀
      if (dp[j] && dict.includes(suffix)) {
        dp[i] = true;
        break;
      }
    }
  }

  return dp[s.length];
}
```
