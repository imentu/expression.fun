---
title: "LeetCode-25 K 个一组翻转链表"
date: 2021-10-06T11:06:44+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Reverse Nodes in K Group"
---

题目地址：[LeetCode-25 K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

## 思路

每 K 个结点看作一组，按组反转链表并重新与前后的其它组连接。

这种链表题不需要技巧，可以当作模拟题做。想清楚各个节点的处理方式，多搞几个变量记录节点状态就行。

## 代码

```go
/*
	Runtime:4 ms, faster than 89.82% of Go online submissions.
	Memory Usage:3.6 MB, less than 75.73% of Go online submissions.
*/
func reverseKGroup(head *ListNode, k int) (result *ListNode) {
	var prevGroupTail *ListNode = nil
	curr := head

	for curr != nil {
		tail := curr
		for i := 1; i < k; i++ { // get the tail of current Group
			if tail.Next == nil {
				return
			}

			tail = tail.Next
		}

		if result == nil { // take the first Group's head as the result
			result = tail
		}

		nextGroupHead := tail.Next

		head, tail = reverseList(curr, nextGroupHead)

		// reconnect current group to previous Group and Next Group
		tail.Next = nextGroupHead
		if prevGroupTail != nil {
			prevGroupTail.Next = head
		}
		prevGroupTail = tail

		curr = nextGroupHead
	}

	return
}

// reverse [head, tail)
func reverseList(head *ListNode, tail *ListNode) (*ListNode, *ListNode) {
	var prev *ListNode = nil
	curr := head
	for curr != tail {
		next := curr.Next
		curr.Next = prev
		prev = curr
		curr = next
	}

	return prev, head // reversed List (head, tail)
}
```

