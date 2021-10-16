---
title: "LeetCode-300  最长上升子序列"
date: 2021-10-12T21:41:09+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Longest Increasing Subsequence"
---

题目地址：[LeetCode-300  最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

## 思路

`dp[i]` 表示以第 $i$ 个元素为结尾的上升子序列的长度。对于每个 `dp[i]`，遍历在它前面的所有元素，如果当前元素小于 `dp[i]` 就使 `dp[i] = max(dp[i], dp[j] + 1)`。

为什么不能让 `dp[i]` 表示以第 $i$ 个元素为结尾的序列中的最长上升子序列的长度？因为那样的话不能判断最长上升子序列中最后一个元素是几，也就无法判断新增的元素会不会增加最长上升子序列的长度。

## 代码

```go
/*
	执行耗时:60 ms,击败了43.31% 的Go用户
	内存消耗:3.7 MB,击败了44.95% 的Go用户
*/
func lengthOfLIS(nums []int) int {
	n := len(nums)
	result := 1
	dp := make([]int, n)
	dp[0] = 1
	for i := 1; i < n; i++ {
		dp[i] = 1
		for j := i - 1; j >= 0; j-- {
			if nums[i] > nums[j] {
				dp[i] = max(dp[i], dp[j]+1)
			}
		}
		result = max(result, dp[i])
	}
	return result
}

func max(x, y int) int {
	if x > y {
		return x
	} else {
		return y
	}
}
```



