---
title: leetcode-122-买卖股票的最佳时机
date: 2021-03-14 18:32:46
tags:
---

# 买卖股票的最佳时机 II

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
