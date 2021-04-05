---
title: leetcode 208 实现Trie树
date: 2020-11-15 11:35:35
tags:
  - leetcode
  - 算法与数据结构
---

# 208 实现前缀树

前缀树其实就是字典树，实现一个 Trie (前缀树)，包含 insert, search, 和 startsWith 这三个操作。

## 示例

示例:

```typescript
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 true
trie.search("app");     // 返回 false
trie.startsWith("app"); // 返回 true
trie.insert("app");
trie.search("app");     // 返回 true
```

## 问题分析

这个数据结构直接看的极客时间上的课程学来的。但是使用数组来存储 children 的方式确实不错，主要字符集数量有限（26 个英文字母），如果是更大的字符集，使用 map 来存储也不错。

> 可以用来解决路由表的最长前缀匹配问题，可以使用 trie 树来加快搜索匹配的速度。

### Round One

1. trie 树主要支持两个操作
   1. insert - 插入的过程，就是构建这个树的过程，需要动态的创建 children
   2. search - 搜索的过程，需要验证单词是否完全匹配，因此节点上需要一个 isEndOfWord 的标记
   3. starsWith - 验证是否是一个合格的前缀，简化版的 search

## 代码实现

### children 是数组

```typescript
class TrieNode {
  char: string;
  isEndOfWord: boolean = false;
  children: TrieNode[] = [];

  constructor(char: string = "") {
    this.char = char;
  }
}

class Trie {
  root: TrieNode;
  constructor() {
    this.root = new TrieNode();
  }

  insert(word: string) {
    let node = this.root;
    for (let char of word) {
      const index = char.charCodeAt(0) - 97;
      if (!node.children[index]) {
        // 当前字符不再children中，需要创建
        node.children[index] = new TrieNode(char);
      }
      node = node.children[index];
    }
    node.isEndOfWord = true;
  }

  search(word: string) {
    let node = this.root;
    for (let char of word) {
      const index = char.charCodeAt(0) - 97;
      if (!node.children[index]) {
        return false;
      }
      node = node.children[index];
    }
    return node.isEndOfWord;
  }

  startsWith(word: string) {
    let node = this.root;
    for (let char of word) {
      const index = char.charCodeAt(0) - 97;
      if (!node.children[index]) {
        return false;
      }
      node = node.children[index];
    }
    return true;
  }
}
```

### children 是 map

map 的方法在 leetcode 上更快，应该是因为数组方法里面 charCodeAt 比较耗时吧。

```typescript
class TrieNode {
    char: string;
    isEndOfWord: boolean = false;
    children: Map<string,TrieNode> = new Map()

    constructor(char:string = ''){
        this.char = char;
    }
}

class Trie {
    root: TrieNode = new TrieNode();

    insert(word: string){
        let node = this.root;
        for(let char of word){
            if(!node.children.has(char)){
                node.children.set(char, new TrieNode(char));
            }
            node = node.children.get(char);
        }
        node.isEndOfWord = true;
    }

    search(word: string){
        let node = this.root;
        for(let char of word){
            if(!node.children.has(char)){
                return false;
            }
            node = node.children.get(char);
        }
        retur node.isEndOfWord;
    }

    startsWith(word: string){
        let node = this.root;
        for(let char of word){
            if(!node.children.has(char)){
                return false;
            }
            node = node.children.get(char);
        }
        return true;
    }

}
```
