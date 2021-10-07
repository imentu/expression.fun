---
title: "LeetCode-8 字符串转换整数（atoi）"
date: 2021-09-23T21:45:42+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "String to Integer Atoi"
---

题目地址：[LeetCode-8 字符串转换整数（atoi）](https://leetcode-cn.com/problems/string-to-integer-atoi/)

## 思路

这道题主要难点在于对数值溢出的处理,有两种方式判断数字是否溢出：

1. 用一个可以表示更大范围类型的数据存储值(例如 `long`)，直接用大于小于判断数值是否超出 32 位整数可以表示的数值范围。
2. 在追加个位数字前就和上/下界比较：给上/下界除 10 比较高位，给上/下界模 10 比较个位大小。

面试中的标准答案是第二种，能够不借助数值范围更大的数据类型就判断出数值是否会溢出。

第一种方法无法处理没有比它的数值范围类型更大的数值类型，比如 `long`（虽然通过调用类似 `BigInteger` 的库获得更大的数据范围，但一般不是面试官预期的答案）。

## 代码

```go
/*
	Runtime:0 ms, faster than 100.00% of Go online submissions.
	Memory Usage:2.3 MB, less than 23.48% of Go online submissions.
*/
func myAtoi(s string) int {
	str := []rune(strings.Trim(s, " "))
	sign := 1
	index := 0
	num := 0
	length := len(str)

	if length == 0 {
		return 0
	}

	if ch := str[index]; ch == '+' || ch == '-' {
		index += 1
		if ch == '-' {
			sign = -1
		}
	}

	for ; index < length && unicode.IsNumber(str[index]); index += 1 {
		nu := sign * (int)(str[index]-'0')

		if willOverBound(num, nu) {
			return maxOfSign(sign)
		}

		num *= 10
		num += nu
	}

	return num
}

func maxOfSign(sign int) int {
	if sign == -1 {
		return math.MinInt32
	} else {
		return math.MaxInt32
	}
}

func willOverBound(num int, nu int) bool {
	if num > math.MaxInt32/10 || num == math.MaxInt32/10 && nu > math.MaxInt32%10 {
		return true
	}
	if num < math.MinInt32/10 || num == math.MinInt32/10 && nu < math.MinInt32%10 {
		return true
	}
	return false
}
```

