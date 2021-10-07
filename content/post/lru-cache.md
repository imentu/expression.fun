---
title: "LeetCode-146 LRU 缓存机制"
date: 2021-09-21T19:55:02+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Lru Cache"
---

题目地址：[LeetCode-146 LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)

## 思路

用双向链表存储数据，最近一次被访问（`Get` 或 `Put`）的节点移动到链表头部。需要淘汰数据时只需要删除链表中最后一个节点的数据即可。

技巧：

- 双向链表中维护 `head` 和 `tail` 两个哨兵节点 ，可以免于处理节点前端或后端为 `nil` 的情况。
- 使用 `map` 记录 `key` 和 `node` 的对应关系，使查询和插入达到 $O(1)$ 的时间复杂度。

## 代码

```go
/*
	Runtime:476 ms, faster than 93.82% of Go online submissions.
	Memory Usage:85.9 MB, less than 38.35% of Go online submissions.
*/
type LRUCache struct {
	capacity int
	size     int
	list     *LinkedList
	mapping  map[int]*Node
}

func Constructor(capacity int) LRUCache {
	return LRUCache{capacity, 0, NewLinkedList(), map[int]*Node{}}
}

func (this *LRUCache) Get(key int) int {
	if node, ok := this.mapping[key]; ok {
		this.list.moveNodeToHead(node)
		return node.value
	} else {
		return -1
	}
}

func (this *LRUCache) Put(key int, value int) {
	if node, ok := this.mapping[key]; ok {
		node.value = value
		this.list.moveNodeToHead(node)
	} else {
		if this.size == this.capacity {
			removedNode := this.list.removeNodeAtLast()
			delete(this.mapping, removedNode.key)
			this.size -= 1
		}
		node := &Node{nil, nil, key, value}
		this.mapping[key] = node

		this.list.addNodeToFirst(node)

		this.size += 1
	}
}

type LinkedList struct {
	head *Node
	tail *Node
}

type Node struct {
	previous *Node
	next     *Node
	key      int
	value    int
}

func (this *LinkedList) addNodeToFirst(node *Node) {
	node.previous = this.head
	node.next = this.head.next

	this.head.next.previous = node
	this.head.next = node
}

func (this *LinkedList) moveNodeToHead(node *Node) {
	this.removeNode(node)
	this.addNodeToFirst(node)
}

func (this *LinkedList) removeNodeAtLast() *Node {
	node := this.tail.previous
	this.removeNode(node)
	return node
}

func (this *LinkedList) removeNode(node *Node) {
	previous := node.previous
	next := node.next

	previous.next = next
	next.previous = previous
}

func NewLinkedList() *LinkedList {
	head := new(Node)
	tail := new(Node)
	head.next = tail
	tail.previous = head

	return &LinkedList{head, tail}
}
```

