---
title: leetcode-142-环形链表
date: 2021-03-14 11:05:49
tags: leetcode
---

# 142 环形链表

## 解法 1

遍历链表，同时使用一个 set 存储已经访问过的节点，如果当前正在访问的节点存在于 set 中，则说明找到了换的入口。返回该节点。

```typescript
function detectCycle(head: ListNode | null): ListNode | null {
  const set = new Set<ListNode>();
  let node = head;
  while (node) {
    if (set.has(node)) {
      return node;
    } else {
      set.add(node);
      node = node.next;
    }
  }
  return null;
}
```

## 解法 2

假设起点到环的入口点的距离是 a，慢指针和快指针在环内相遇，慢指针在环内走过的距离是 b，环内剩下的距离是 c，则环的周长是 b+c。
快指针走过的距离是慢指针的 2 倍。

```
slow * 2 = fast
slow = a + b
fast = a + b + (b + c)
2 * (a + b) = a + b + (b + c);
a = c
```

可以推导出环内剩余路程 c，和 a 距离相等。因此定义一个指针从 head 出发，慢指针继续走，相遇点即为入口点。

```typescript
function detectCycle(head: ListNode | null): ListNode | null {
  let slow = head;
  let fast = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;

    if (slow === fast) {
      break;
    }
  }

  // 没有环
  if (!fast || !fast.next) return null;

  // 有环
  let p = head;
  while (p !== slow) {
    p = p.next;
    slow = slow.next;
  }

  return p;
}
```
