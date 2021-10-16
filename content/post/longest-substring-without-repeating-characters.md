---
title: "LeetCode-3 无重复字符的最长子串"
date: 2021-10-14T13:18:55+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Longest Substring Without Repeating Characters"
---

题目地址：[LeetCode-3 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

## 思路

**双指针 $\neq$ 双重循环！**

## 代码

```go
/*
	执行耗时:4 ms,击败了91.43% 的Go用户
	内存消耗:6.6 MB,击败了11.32% 的Go用户
*/
func lengthOfLongestSubstring(s string) int {
	length := len(s)
	cs := []rune(s) // 在 LeetCode 评测系统中，比使用下标访问更快
	result := 0
	left := 0
	m := make(map[rune]int, 256) // 指定大小，避免扩容

	for right := 0; right < length; right++ {
		if index, ok := m[cs[right]]; ok {
			for i := left; i <= index; i++ {
				delete(m, cs[i])
			}
			left = index + 1
		}
		m[cs[right]] = right
		result = max(result, right-left+1)
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

