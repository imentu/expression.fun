---
title: "LeetCode-23 合并 K 个升序链表"
date: 2021-08-08T19:13:13+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Merge K Sorted List"
---

题目地址：[LeetCode-23 合并 K 个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

## 方案一：排序所有节点后拼接链表

### 代码

```java
/*
	执行耗时:8 ms,击败了37.09% 的Java用户
	内存消耗:40.1 MB,击败了60.86% 的Java用户
*/
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        List<ListNode> nodes = new ArrayList<>();
        for (ListNode list : lists) { // 把链表拆散放入List
            addToNodeList(nodes, list);
        }

        if (nodes.size() == 0) {
            return null;
        }

        Collections.sort(nodes, (a, b) -> Integer.compare(a.val, b.val)); // 排序

        int length = nodes.size();
        for (int i = 1; i < length; i++) { // 将排序后的 nodes 拼接成新的链表
            nodes.get(i - 1).next = nodes.get(i);
        }
        nodes.get(length - 1).next = null; // 尾节点没有后续节点

        return nodes.get(0); // 返回头节点
    }

    public void addToNodeList(List<ListNode> nodes, ListNode list) {
        while (list != null) {
            nodes.add(list);
            list = list.next;
        }
    }
}
```

### 复杂度分析

- 时间复杂度：假设每个链表长度是 $n$。把链表中每个节点放入 List 的时间代价是 $O(kn)$，排序的时间代价是$O(kn\log{kn})$，拼接链表的时间代价是$O(kn)$，总的时间复杂度是$O(kn\log{kn})$。
- 空间复杂度：需要用额外的空间存储每个节点，所以空间复杂度是$O(kn)$。

### 问题

没有利用到各个链表已经有序的特性。

## 方案二：依次合并

### 代码

```java
/*
	执行耗时:111 ms,击败了25.77% 的Java用户
	内存消耗:40.2 MB,击败了46.48% 的Java用户
*/
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }

        for (int i = 1; i < lists.length; i++) {
            lists[i] = merge(lists[i], lists[i - 1]);
        }

        return lists[lists.length - 1];
    }

    public ListNode merge(ListNode l1, ListNode l2) {
        ListNode sentinel = new ListNode();
        ListNode prev = sentinel;

        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                prev.next = l1;
                l1 = l1.next;
            } else {
                prev.next = l2;
                l2 = l2.next;
            }
            prev = prev.next;
        }
        if (l1 == null) {
            prev.next = l2;
        }
        if (l2 == null) {
            prev.next = l1;
        }

        return sentinel.next;
    }
}
```

### 复杂度分析

- 时间复杂度：第一次合并的时间复杂度是 $O(2n)$，第二次合并的时间复杂度是 $O(3n)$，第三次合并的时间复杂度是 $O(4n)$，第 $i$ 次合并的时间复杂度是$O((i + 1)n)$，总的时间复杂度是$O(\sum_{i=2}^{k}in)=O((\sum_{i=1}^{k}in) - n)=O(k^2n)$
- 空间复杂度：不需要用额外空间，所以空间复杂度是$O(1)$。

### 问题

合并后的链表越来越长，且越是先被合并的链表中的节点参与合并的次数越多，时间复杂度高。

## 方案三：分治

以类似归并排序的方式，分多次将链表两两合并，减少每个节点参与合并的次数。

### 代码

```java
/*
	执行耗时:2 ms,击败了82.61% 的Java用户
	内存消耗:40.1 MB,击败了55.78% 的Java用户
*/
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }

        for (int sz = 1; sz < lists.length; sz += sz) {
            for (int lo = 0; lo + sz < lists.length; lo += sz + sz) {
                lists[lo] = merge(lists[lo], lists[lo + sz]);
            }
        }

        return lists[0];
    }

    public ListNode merge(ListNode l1, ListNode l2) {
        ListNode sentinel = new ListNode();
        ListNode prev = sentinel;

        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                prev.next = l1;
                l1 = l1.next;
            } else {
                prev.next = l2;
                l2 = l2.next;
            }
            prev = prev.next;
        }
        if (l1 == null) {
            prev.next = l2;
        }
        if (l2 == null) {
            prev.next = l1;
        }

        return sentinel.next;
    }
}
```

### 复杂度分析

- 时间复杂度：第一次合并 $\frac{k}{2}$​​ 组链表，每组的时间代价是 $2n$​​，第二次合并 $\frac{k}{4}$​​ 组链表，每组的时间代价是 $4n$​​，第三次合并 $\frac{k}{8}$​​ 组链表，每组的时间代价是 $8n$​​，总的时间复杂度是 $O(\sum_{i = 1}^{\infty}\frac{k}{2^i}\times{2^in})=O(kn\log{k})$​​。可以这样理解，每次合并的时间复杂度是 $O(kn)$​​，共需合并 $\log{k}$​​ 次，所以总的时间复杂度是 $O(kn\log{k})$​​。
- 空间复杂度：不需要用额外空间，所以空间复杂度是$O(1)$。

## 方案四：每次取数组中最小节点追加至链表

每次取数组中最小节点追加至链表，直至数组中所有节点为 `null`。

### 代码

```java
/*
	执行耗时:221 ms,击败了10.48% 的Java用户
	内存消耗:40.2 MB,击败了43.63% 的Java用户
*/
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }

        ListNode sentinel = new ListNode();
        ListNode prev = sentinel;

        int min = -1;
        while ((min = select(lists)) != -1) {
            prev.next = lists[min];
            lists[min] = lists[min].next;

            prev = prev.next;
        }

        return sentinel.next;
    }

    public int select(ListNode[] lists) {
        int min = -1;

        for (int i = 0; i < lists.length; i++) {
            if (lists[i] != null && (min == -1 || lists[i].val < lists[min].val)) {
                min = i;
            }
        }

        return min;
    }
}
```

### 复杂度分析

- 时间复杂度：每一次都需要遍历 $k$ 个链表，共遍历 $kn$ 次，所以总的时间复杂度是 $O(k^2n)$。
- 空间复杂度：不需要用额外空间，所以空间复杂度是$O(1)$。

### 问题

时间复杂度高。

## 方案五：优先队列

利用优先队列可以再 $O(\log{k})$ 的时间取出最小值的特性优化方案四。

### 代码

```java
/*
	执行耗时:5 ms,击败了58.22% 的Java用户
	内存消耗:40.1 MB,击败了53.49% 的Java用户
*/
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> priorityQueue = new PriorityQueue<>((a, b)-> Integer.compare(a.val, b.val));
        for (int i = 0; i < lists.length; i++){
            if (lists[i] != null) {
                priorityQueue.offer(lists[i]);
                lists[i] = lists[i].next;
            }
        }

        ListNode sentinel = new ListNode();
        ListNode prev = sentinel;

        while (!priorityQueue.isEmpty()) {
            prev.next = priorityQueue.poll();
            prev = prev.next;

            if (prev.next != null) {
                priorityQueue.offer(prev.next);
            }
        }

        return sentinel.next;
    }
}
```

### 复杂度分析

- 时间复杂度：每次插入优先队列的时间复杂度是 $O(\log{k})$，从优先队列中取出最小值的时间复杂度是 $O(\log{k})$，共需要操作 $kn$ 次没所以总的时间复杂度是 $O(kn\log{k})$，和分治的方案时间复杂度相同。
- 空间复杂度：需要使用大小为 $k$ 的优先队列 ，所以空间复杂度是$O(k)$。

### 问题

优先队列和分治的渐进时间复杂度相同，但比分治的运行时间多出一倍，是因为常数大还是因为分治的多轮合并能够更好地利用 CPU 缓存？
