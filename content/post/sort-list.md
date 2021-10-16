---
title: "LeetCode-148 排序链表"
date: 2021-10-14T11:07:50+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Sort List"
---

题目地址：[LeetCode-148 排序链表](https://leetcode-cn.com/problems/sort-list/)

## 思路

使用归并排序链表比较简单，因为 `merge` 操作本质上就是合并两个有序链表。

归并排序有两种：自顶向下归并和自底向上归并。在本题中，自定向下的归并排序的时间复杂度是 $O(n log n)$，空间复杂度是 $O(log n)$ （递归需要的栈的大小）。自底向上的归并排序时间复杂度是 $O(n log n)$，空间复杂度是 $O(1)$。

## 代码

### 自顶向下归并排序

```go
/*
	执行耗时:28 ms,击败了72.60% 的Go用户
	内存消耗:7.1 MB,击败了76.84% 的Go用户
*/
func sortList(head *ListNode) *ListNode {
	tail := head
	for tail != nil && tail.Next != nil {
		tail = tail.Next
	}
	return mergeSort(head, tail)
}

// [head, tail]
func mergeSort(head, tail *ListNode) *ListNode {
	if head == tail {
		return head
	}

	ls, le, rs, re := split(head, tail)
	l := mergeSort(ls, le)
	r := mergeSort(rs, re)
	return merge(l, r)
}

func split(head, tail *ListNode) (*ListNode, *ListNode, *ListNode, *ListNode) {
	fast := head
	slow := head
	for fast != tail && fast.Next != tail {
		fast = fast.Next.Next
		slow = slow.Next
	}

	rs := slow.Next
	slow.Next = nil
	return head, slow, rs, tail
}

func merge(l1, l2 *ListNode) *ListNode {
	sentinel := &ListNode{}
	current := sentinel

	for l1 != nil || l2 != nil {
		if l1 == nil {
			current.Next = l2
			break
		}
		if l2 == nil {
			current.Next = l1
			break
		}

		if l1.Val < l2.Val {
			current.Next = l1
			l1 = l1.Next
		} else {
			current.Next = l2
			l2 = l2.Next
		}
		current = current.Next
	}

	return sentinel.Next
}
```

### 自底向上归并排序

```go
/*
	执行耗时:28 ms,击败了72.46% 的Go用户
	内存消耗:6.8 MB,击败了99.32% 的Go用户
*/
func sortList(head *ListNode) *ListNode {
	length := len(head)
	sentinel := &ListNode{Val: 0, Next: head}
	for sz := 1; sz < length; sz += sz {
		prev := sentinel
		current := sentinel.Next
		for current != nil {
			l1s := current
			l1e := moveAtMostX(l1s, sz)
			if l1e.Next == nil { // 没有后半边，无法归并，退出内循环
				break
			}

			l2s := l1e.Next
			l2e := moveAtMostX(l2s, sz)

			next := l2e.Next // 下一组待归并项的起始位置

			l1e.Next = nil // 打断链表，准备合并
			l2e.Next = nil

			subHead := merge(l1s, l2s)
			prev.Next = subHead

			tail := tailOf(subHead)
			tail.Next = next // 归并后链接到后续节点

			prev = tail
			current = next
		}
	}
	return sentinel.Next
}

func len(head *ListNode) int {
	length := 0
	for head != nil {
		length += 1
		head = head.Next
	}
	return length
}

func moveAtMostX(head *ListNode, x int) *ListNode {
	for i := 1; i < x && head.Next != nil; i++ {
		head = head.Next
	}
	return head
}

func merge(l1, l2 *ListNode) *ListNode {
	sentinel := &ListNode{}
	current := sentinel

	for l1 != nil || l2 != nil {
		if l1 == nil {
			current.Next = l2
			break
		}
		if l2 == nil {
			current.Next = l1
			break
		}

		if l1.Val < l2.Val {
			current.Next = l1
			l1 = l1.Next
		} else {
			current.Next = l2
			l2 = l2.Next
		}
		current = current.Next
	}

	return sentinel.Next
}

func tailOf(head *ListNode) *ListNode {
	for head.Next != nil {
		head = head.Next
	}
	return head
}
```

