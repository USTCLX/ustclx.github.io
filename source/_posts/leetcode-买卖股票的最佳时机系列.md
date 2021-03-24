---
title: leetcode-买卖股票的最佳时机系列(121,122,123,188)
date: 2021-03-14 18:32:46
tags:
---

# 121 买卖股票的最佳时机 I

给定一只股票，但是只能买卖一次。

## 暴力法

进行两层循环，第一层循环是买入的日子，第二层循环是卖出的日子，遍历左右的利润，求出最大值。

### 代码

```typescript
function maxProfit(prices: number[]): number {
  if (prices.length < 2) return 0;
  const length = prices.length;
  let profit = 0;
  for (let i = 0; i < length; i++) {
    for (let j = i + 1; j < length; j++) {
      profit = Math.max(profit, prices[j] - prices[i]);
    }
  }
  return profit;
}
```

### 优化

进行一次遍历，在遍历的过程中修改变量，一个是当前最低的价格，一个是当前的利润。
如果当前价格低于最低价格，则更新最低价格。用当前的价格减去最低价格，即是这一天能够获得的最大利润。

### 代码

```typescript
function maxProfit(prices: number[]): number {
  if (prices.length < 2) return 0;
  const length = prices.length;
  let minPrice = prices[0];
  let profit = 0;
  for (let i = 1; i < prices.length; i++) {
    if (prices[i] < minPrice) {
      minPrice = prices[i];
    }
    profit = Math.max(profit, prices[i] - minPrice);
  }
  return profit;
}
```

# 122 买卖股票的最佳时机 II

## 暴力法

对于每个股票交易日，都有两种选择，一种是按兵不动，即不进行任何操作。还有一种是根据当前股票的持有情况，进入买进或者卖出，比如当前持有股票就可以卖出，当前没有股票就进行买入。

每一个交日易都有不同的选择，一次一次的选择形成了一棵操作树，这是典型的 dfs 了。

### 暴力法代码

```typescript
function maxProfit(prices: number[]): number {
  let max = 0;
  const length = prices.length;

  // status:0代表手上没有股票，可以买。1代表手上有股票，可以卖。
  const dfs = (count: number, profit: number, status: number) => {
    if (count === length) {
      max = Math.max(max, profit);
      return;
    }

    // 按兵不动
    dfs(count + 1, profit, status);

    // 进行操作
    if (status === 0) {
      // 进行买入，利润要减掉当前价格
      dfs(count + 1, profit - prices[count], 1);
    } else if (status === 1) {
      // 进行卖出，利润要加上当前价格
      dfs(count + 1, profit + prices[count], 0);
    }
  };

  dfs(0, 0, 0);

  return max;
}
```

## 贪心算法

这个问题，可以使用贪心算法，即根据当日的价格来决定上个交易日是否进行买入，只要今天的价格比昨天高，就昨天买日，今天卖出。当然现实情况下，是不可能买入昨天的股票的。

### 贪心法代码

```typescript
function maxProfit(prices: number[]): number {
  if (!prices || prices.length < 2) return 0;
  let profit = 0;

  for (let i = 1; i < prices.length; i++) {
    if (prices[i] > prices[i - 1]) {
      profit += prices[i] - prices[i - 1];
    }
  }

  return profit;
}
```

## 动态规划

如果使用动态规划来解决这个问题，那么需要保存一个二维的状态，dp[i][j]。
dp[i][0] 代表第 i 天，手上没有股票时的收益。dp[i][1]代表第 i 天手上有股票的收益。
那么推导一下状态方程就是

- dp[i][0] = Math.max(dp[i-1][0],dp[i-1][1]+prices[i])
  - 也就是第 i 天手上没有股票时，可以是 i-1 天本来就没有股票，然后第 i 天不进行任何操作，或者是 i-1 天持有股票，然后将其卖出。取两者的最大值。
- dp[i][1] = Math.max(dp[i-1][1],dp[i-1][0]-prices[i])
  - 也就是第 i 天手上有股票时，可以是 i-1 天本来就有股票，然后第 i 天按兵不动，或者第 i-1 天手上没有骨片，然后今天买进。取两者的最大值。

最后我们期望的结果就是 dp[n][0]。也就是最后一天手上没有股票时的金额。
可以看出第 n 天的最大收益，是根据之前的每一天的收益情况，一点一点推导出来的。非常经典的动态规划。

### 动态规划代码

```typescript
function maxProfit(prices: number[]): number {
  if (!prices || prices.length < 2) return 0;

  const dp = prices.map(() => [0, 0]);
  dp[0][0] = 0;
  dp[0][1] = -prices[0];

  for (let i = 1; i < prices.length; i++) {
    dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
    dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
  }

  return dp[prices.length - 1][0];
}
```

# 123 买卖股票的最佳时机 III

不同于买卖股票的最佳时机 II。这次只能交易两次了。这次我们只选择动态规划来解决这个问题。我们把买进股票作为交易一次股票的标志。

## 动态规划

首先需要增加一个状态，来记录当前已经交易了几次。dp[i][j][k] ,j 代表了是第几次买进股票了。可以的取值为 0，1，2。k 还是代表当前手上是否持有股票，0 表示不持有，1 表示持有。

### 代码

```typescript
function maxProfit(prices: number[]): number {
  if (prices.length < 2) return 0;
  const length = prices.length;
  // 初始化三维dp数组
  const dp = prices.map(() => Array(2));
  for (let i = 0; i < length; i++) {
    for (let j = 0; j <= 2; j++) {
      dp[i][j] = [];
    }
  }

  // 初始化状态
  for (let i = 0; i < length; i++) {
    dp[i][0][0] = 0; // 第i天，没有交易股票，手上也没有股票
    dp[i][0][1] = -Infinity; // 第i天，没有交易股票，手上有一股，是不可能的。
  }
  for (let j = 0; j <= 2; j++) {
    dp[0][j][0] = 0; // 第1天，买入了j次，手上没有股票
    dp[0][j][1] = -pirces[0]; // 第一天，买入了j次，手上有股票
  }

  for (let i = 1; i < length; i++) {
    for (let j = 1; j <= 2; j++) {
      dp[i][j][0] = Math.max(dp[i - 1][j][0], dp[i - 1][j][1] + prices[i]);
      dp[i][j][1] = Math.max(dp[i - 1][j][1], dp[i - 1][j - 1][0] - prices[i]);
    }
  }

  // 求最后一天，手上没有股票时的最大利润
  let maxProfit = 0;
  for (let j = 0; j <= 2; j++) {
    maxProfit = Math.max(maxProfit, dp[length - 1][j][0]);
  }
  return maxProfit;
}
```

# 188 买卖股票的最佳时机 IV

这一次又升级了，可以买卖 k 次

## 动态规划

状态方程可以参考买卖两次。其实已经将算法范型化，支持将 2 次变成 k 次就可以了。
需要注意的是，当 k>=len/2 的时候，问题相当于变成了可以交易无数次，也就是 122，这时可以直接采用 122 的贪心算法。

### 代码

```typescript
function greedy(prices: number[]): number {
  if (!prices || prices.length < 2) return 0;
  let profit = 0;

  for (let i = 1; i < prices.length; i++) {
    if (prices[i] > prices[i - 1]) {
      profit += prices[i] - prices[i - 1];
    }
  }

  return profit;
}
function maxProfit(k: number, prices: number[]): number {
  if (prices.length < 2) return 0;

  if (k >= prices.length / 2) return greedy(prices);

  const length = prices.length;
  // 初始化三维dp数组
  const dp = prices.map(() => Array(k + 1));
  for (let i = 0; i < length; i++) {
    for (let j = 0; j <= k; j++) {
      dp[i][j] = [];
    }
  }

  // 初始化状态
  for (let i = 0; i < length; i++) {
    dp[i][0][0] = 0; // 第i天，没有交易股票，手上也没有股票
    dp[i][0][1] = -Infinity; // 第i天，没有交易股票，手上有一股，是不可能的。
  }
  for (let j = 0; j <= k; j++) {
    dp[0][j][0] = 0; // 第1天，买入了j次，手上没有股票
    dp[0][j][1] = -prices[0]; // 第一天，买入了j次，手上有股票
  }

  for (let i = 1; i < length; i++) {
    for (let j = 1; j <= k; j++) {
      dp[i][j][0] = Math.max(dp[i - 1][j][0], dp[i - 1][j][1] + prices[i]);
      dp[i][j][1] = Math.max(dp[i - 1][j][1], dp[i - 1][j - 1][0] - prices[i]);
    }
  }

  // 求最后一天，手上没有股票时的最大利润
  let maxProfit = 0;
  for (let j = 0; j <= k; j++) {
    maxProfit = Math.max(maxProfit, dp[length - 1][j][0]);
  }
  return maxProfit;
}
```
