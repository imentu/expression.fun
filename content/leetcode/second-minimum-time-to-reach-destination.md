---
title: "LeetCode-2045 到达目的地的第二短时间"
date: 2021-10-19T10:15:18+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Second Minimum Time to Reach Destination"
---

题目地址：[LeetCode-2045 到达目的地的第二短时间](https://leetcode-cn.com/problems/second-minimum-time-to-reach-destination/)

## 思路

因为每个节点的路径长度相等且红绿灯状态是同步的，所以移动的步数相同时耗时也相同。可以记录移动到每个节点花费的步数，到最后再求耗时。

要求出到某个节点第二短的时间，只需要在  BFS 的基础上增加一个记录到达节点时花费步数的二维数组 `dist`：`distance[1][1]` 表示第二次从起点到节点 2 花费的步数。

### 注意

同层节点指向同一个 `next` 节点时只取第一个，否则会影响到达次数的计算（题目要求第二短的时间严格大于第一短的时间）。

- 处理 `next` 前先判断是否已有同层节点到达。因为本题只求第二短时间，所以只需要判断 `dist[next][0]` 是否等于 `curr.d + 1`。
- 立即填充 `dist[next]` ，防止同层节点重复计算。

### 技巧

- List + 头部指针作为队列使用。
- 向队列添加节点后立即判断是否到达终点

## 代码

```go
/*
	执行耗时:324 ms,击败了32.43% 的Go用户
	内存消耗:9.2 MB,击败了72.97% 的Go用户
*/
func secondMinimum(n int, edges [][]int, time int, change int) int {
	// 邻接表建图
	g := make([][]int, n)
	for _, e := range edges {
		u, v := e[0]-1, e[1]-1
		g[u] = append(g[u], v)
		g[v] = append(g[v], u)
	}

	type pair struct {
		x int // position
		d int // step
	}
	// queue
	q := []pair{{}}
	f := 0
	// distance[1][1] 表示从 1 到 2 的第 2 短距离
	dist := make([][]int, n)
	dist[0] = append(dist[0], 0)
	for f < len(q) {
		curr := q[f]
		f += 1

		for _, next := range g[curr.x] {
			if len(dist[next]) < 2 {
				if len(dist[next]) == 1 && curr.d+1 == dist[next][0] { // 处理 next 被同一层的节点指向的情况
					continue
				}
				q = append(q, pair{next, curr.d + 1})
				dist[next] = append(dist[next], curr.d+1)
				if next == n-1 && len(dist[next]) == 2 {
					break
				}
			}
		}
	}

	d := dist[n-1][1]
	ans := 0
	for i := 0; i < d; i++ {
		if ans/change%2 != 0 {
			ans += change - ans%change
		}
		ans += time
	}
	return ans
}
```

