---
title: "LeetCode-236 二叉树的最近公共祖先"
date: 2021-10-07T11:09:24+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Lowest Common Ancestor of a Binary Tree"
---

题目地址：[LeetCode-236 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

## 思路

用 DFS 将对应节点到根节点的路径存储到链表中，问题就变成了[找两个相交链表的第一个交点]({{<ref "leetcode/intersection-of-two-linked-lists.md">}})。

## 代码

```go
/*
	Runtime:4 ms, faster than 99.72% of Go online submissions.
	Memory Usage:7.1 MB, less than 99.49% of Go online submissions.
*/
type ListNode struct {
	val  *TreeNode
	next *ListNode
}

func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
	first := buildPath(root, p, nil)
	second := buildPath(root, q, nil)

	return intersection(first, second).val
}

func buildPath(root, p *TreeNode, l *ListNode) *ListNode {
	if root == nil {
		return nil
	} else if root == p {
		return &ListNode{p, l}
	} else {
		left := buildPath(root.Left, p, &ListNode{root, l})
		if left != nil {
			return left
		}
		right := buildPath(root.Right, p, &ListNode{root, l})
		return right
	}
}

func intersection(headA, headB *ListNode) *ListNode {
	a := headA
	b := headB
	for a == nil || b == nil || a.val != b.val {
		if a == nil {
			a = headB
		} else {
			a = a.next
		}

		if b == nil {
			b = headA
		} else {
			b = b.next
		}
	}
	return a
}
```

