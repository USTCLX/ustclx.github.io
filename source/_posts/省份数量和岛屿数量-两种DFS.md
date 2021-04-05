---
title: 省份数量和岛屿数量-两种DFS
date: 2021-03-13 22:26:19
tags: 算法与数据结构
---

# 省份数量和岛屿数量-两种 DFS

## 省份数量

> 计算省份数量，也可以是计算朋友圈的数量。从 1 到 n，一共有 n 个城市，他们的连接关系可以用一个二维数组来表示。

```typescript
const isConnected = [
  [1, 1, 0],
  [1, 1, 0],
  [0, 0, 1],
];

// isConnected[i][j]=1 代表了i和j这两个连接，isConnected[i][j]=0则代表不直接连接。
```

连接具有传递性。i->j 连接，j->k 连接，那么 i->k。
所有相连接的城市（朋友）构成了一个省份（朋友圈）。求所有省份的数量。

这里需要用到 DFS 去搜索，并且需要一个 visited 来记录哪些城市已经被访问过了。
遍历这 n 个城市，如果没有被 visited，省份就加 1。
比如搜索到了城市 i，那么就顺着把 i 这个链条都去访问一遍，并且记录在 visited 里面。

```typescript
function dfs(
  isConnected: number[][],
  visited: boolean[],
  i: number,
  m: number
) {
  if (visited[i]) return;

  visited[i] = true;

  for (let j = 0; j < m; j++) {
    if (isConnected[i][j]) {
      dfs(isConnected, visited, j, m);
    }
  }
}

function findCircleNum(isConnected: number[][]): number {
  if (!isConnected.length || !isConnected[0].length) return 0;

  const m = isConnected.length;
  const visited = Array(m).fill(false);
  let count = 0;
  for (let i = 0; i < m; i++) {
    if (!visited[i]) {
      count++;
      dfs(isConnected, visited, i, m);
    }
  }

  return count;
}
```

## 岛屿数量

使用二维矩阵代表一篇区域，如果 grid[i][j]为 1 代表陆地，如果 grid[i][j]为 0 代表海洋，所有连接的 1 构成一个岛屿，求岛屿的数量。

也是 dfs，遍历整个二维矩阵，如果当前是陆地，就 dfs 搜索这个点的周围区域，并且把周围区域标记为 0。需要向四个方向来进行 dfs。

```typescript
function bfs(grid: string[][], i: number, j: number, m: number, n: number) {
  const queue = [[i, j]];

  while (queue.length) {
    const [a, b] = queue.shift();
    if (grid[a][b] === "1") {
      grid[a][b] = "0";
      if (a - 1 >= 0 && grid[a - 1][b] === "1") queue.push([a - 1, b]);
      if (a + 1 < m && grid[a + 1][b] === "1") queue.push([a + 1, b]);
      if (b - 1 >= 0 && grid[a][b - 1] === "1") queue.push([a, b - 1]);
      if (b + 1 < n && grid[a][b + 1] === "1") queue.push([a, b + 1]);
    }
  }
}

function dfs(grid: string[][], i: number, j: number, m: number, n: number) {
  if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == "0") {
    return;
  }

  grid[i][j] = "0";

  const dirs = [
    [1, 0],
    [-1, 0],
    [0, -1],
    [0, 1],
  ];

  for (const [dx, dy] of dirs) {
    dfs(grid, dx + i, dy + j, m, n);
  }
}

function numIslands(grid: string[][]): number {
  if (!grid.length || !grid[0].length) return 0;

  const copy = grid.map((item) => [...item]);
  const m = copy.length;
  const n = copy[0].length;
  let count = 0;

  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (copy[i][j] == "1") {
        count++;
        dfs(copy, i, j, m, n);
      }
    }
  }

  return count;
}
```

## 对比

- 省份是沿着一个链接链条进行 dfs，并标记 visited
- 岛屿是沿着一个点，向四个方向进行 dfs，并将 grid 置为 0。
  - 也可以使用 bfs
