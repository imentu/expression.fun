---
title: "LeetCode-160 相交链表"
date: 2021-10-07T11:09:38+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Intersection of Two Linked Lists"
---

题目地址：[LeetCode-160 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

## 代码

```go
/*
	Runtime:28 ms, faster than 92.29% of Go online submissions.
	Memory Usage:7.1 MB, less than 90.24% of Go online submissions.
*/
func getIntersectionNode(headA, headB *ListNode) *ListNode {
	a := headA
	b := headB
	for a != b {
		if a == nil {
			a = headB
		} else {
			a = a.Next
		}

		if b == nil {
			b = headA
		} else {
			b = b.Next
		}
	}
	return a
}
```



## 思路

用两个指针分别遍历两个链表直到两个指针指向同一节点，若遍历完当前链表，继续遍历另一个链表。

分别设两链表不相交部分长度为 $a,b$，相交部分长度为 $c$，$nil = 1$ 。共四种情况：

### 不相交

- $a = b$ 时，$a + 1 = b + 1$
- $a \neq b$ 时，$a + 1 + b + 1 = b + 1 + a + 1$

### 相交

- $a = b$ 时，$a + c = b + c$ 

- $a \neq b$，$(a + c + 1 + b) + c = (b + c + 1 + a) + c$

### 解释

式子最后一项代表指针同时指向对应部分第一个节点：

-  $c$ ：两个指针都指向相交部分第一个节点。
-  $1$ ：两个指针都指向 `nil`。

