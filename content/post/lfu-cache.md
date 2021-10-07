---
title: "LeetCode-460 LFU 缓存"
date: 2021-09-22T09:59:05+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Lfu Cache"
---

题目地址：[LeetCode-460 LFU 缓存](https://leetcode-cn.com/problems/lfu-cache/)

## 思路

题目要求需要淘汰相同频率节点时使用 LRU 机制，所以本题可以通过在 LRU 的基础上增加根据频率淘汰节点的机制完成。

为了使查询和插入操作都达到 $O(1)$ 的时间复杂度，需要用两个 `map` 来加速操作。

- `kl` 记录频率和链表的对应关系。
- `kn` 记录 `key` 和 `node` 的对应关系。

为了使淘汰过期节点操作也达到 $O(1)$ 的时间复杂度，使用 `minFrequency` 记录最低出现频率。

- 为什么只需要维护一个 `minFrequency` ？
  因为淘汰操作只出现在 `Put` 的时候，这时一定会插入一个新的 `frequency` 为 1 的节点，所以只需要记录一个最低出现频率即可。

 ### Get

如果 `key` 在 `cache` 中存在，更新对应节点的频率，否则返回 $-1$。

### Put

如果 `key` 在 `cache` 中存在，更新对应节点的频率。

如果 `key` 不在 `cache` 中，且 `cache` 已满，根据 LFU 的规则淘汰节点。

## 代码

```go
/*
	Runtime:520 ms, faster than 53.63% of Go online submissions.
	Memory Usage:90 MB, less than 37.91% of Go online submissions.
*/
type LFUCache struct {
	capacity     int
	size         int
	minFrequency int
	list         *LinkedList
	kn           map[int]*Node
	kl           map[int]*LinkedList
}

func Constructor(capacity int) LFUCache {
	return LFUCache{capacity, 0, 0, NewLinkedList(), map[int]*Node{}, map[int]*LinkedList{}}
}

func (this *LFUCache) Get(key int) int {
	if node, ok := this.kn[key]; ok {
		this.incrementNodeFrequency(node)
		return node.value
	} else {
		return -1
	}
}

func (this *LFUCache) Put(key int, value int) {
	if this.capacity == 0 {
		return
	}

	if node, ok := this.kn[key]; ok {
		node.value = value
		this.incrementNodeFrequency(node)
	} else {
		if this.size == this.capacity {

			removedNode := this.kl[this.minFrequency].removeNodeAtLast()
			delete(this.kn, removedNode.key)

			this.size -= 1
		}

		node := &Node{
			key:       key,
			value:     value,
			frequency: 1,
		}

		this.kn[key] = node

		this.createFrequencyListIfNotAbsent(1)
		this.kl[1].addToFirst(node)

		this.minFrequency = 1

		this.size += 1
	}
}

func (this *LFUCache) updateMinFrequency(node *Node) {
	if node.frequency == this.minFrequency && this.kl[this.minFrequency].length() == 1 {
		this.minFrequency += 1
	}
}

func (this *LFUCache) incrementNodeFrequency(node *Node) {
	this.updateMinFrequency(node)

	this.kl[node.frequency].removeNode(node)

	node.frequency += 1
	this.createFrequencyListIfNotAbsent(node.frequency)
	this.kl[node.frequency].addToFirst(node)
}

func (this *LFUCache) createFrequencyListIfNotAbsent(frequency int) {
	if _, ok := this.kl[frequency]; !ok {
		this.kl[frequency] = NewLinkedList()
	}
}

type LinkedList struct {
	head *Node
	tail *Node
	len  int
}

type Node struct {
	previous  *Node
	next      *Node
	key       int
	value     int
	frequency int
}

func (this *LinkedList) length() int {
	return this.len
}

func (this *LinkedList) addToFirst(node *Node) {
	node.previous = this.head
	node.next = this.head.next

	this.head.next.previous = node
	this.head.next = node

	this.len += 1
}

func (this *LinkedList) removeNodeAtLast() *Node {
	last := this.tail.previous
	this.removeNode(last)
	return last
}

func (this *LinkedList) removeNode(node *Node) {
	previous := node.previous
	next := node.next

	previous.next = next
	next.previous = previous

	this.len -= 1
}

func NewLinkedList() *LinkedList {
	head := new(Node)
	tail := new(Node)
	head.next = tail
	tail.previous = head

	return &LinkedList{head, tail, 0}
}

```

