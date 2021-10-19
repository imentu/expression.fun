---
title: "LeetCode-470 用 Rand7() 实现 Rand10()"
date: 2021-10-09T20:51:52+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "Implement Rand10 Using Rand7"
---

题目地址：[LeetCode-470 用 Rand7() 实现 Rand10()](https://leetcode-cn.com/problems/implement-rand10-using-rand7/)

## 思路

**定义：** $randN()$ 可等概率生成 $[1, N]$ 之间的数字。

1. 使用 $(randX() - 1) \times Y + randY()$ 即可等概率生成 $[1, X \cdot Y]$ 之间的数字。
   1. $randX() - 1$ ：等概率生成 $[0, X - 1]$ 之间的数字。
   2. $(randX() - 1) \times Y$ ：将前一步的结果映射成序列 `[0, Y, 2Y, 3Y, ..., XY]` 中的数字。
   3. $(randX() - 1) \times Y + randY()$ ：给第二步生成的数字序列加上 $randY()$ ，用 $[1, Y]$ 填充数字序列间的空隙，即可等概率得到 $[1, X \cdot Y]$ 之间的数字。
2. 若 $A > B$ ，则可使用 $randA()$ + 拒绝采样得到 $randB()$ ：
   - 对于 $randA()$ 得到的结果 $n$ ，若 $n > B$ 则再次生成 $n$ ，直到 $n <= B$ 。
3. 若 $A \mod B = 0$ ，则 $randA() % B + 1$ 即可等概率生成 $[1, B]$ 之间的数字

## 代码

```go
/*
	执行用时：8 ms, 在所有 Go 提交中击败了97.61%的用户
	内存消耗：5.5 MB, 在所有 Go 提交中击败了16.06%的用户
*/
func rand10() int {
    for {
        n := (rand7() - 1) * 7 + rand7()
        if n <= 40 {
            return n % 10 + 1
        }
    }
}
```

### 优化

```go
/*
	执行用时：12 ms, 在所有 Go 提交中击败了79.00%的用户
	内存消耗：5.5 MB, 在所有 Go 提交中击败了59.06%的用户
*/
func rand10() int {
    for {
        n := (rand7() - 1) * 7 + rand7() // 1-49
        if n <= 40 {
            return n % 10 + 1
        }

        n -= 40 // 1-9
        n = (n - 1) * 7 + rand7() // 1-63
        if n <= 60 {
            return n % 10 + 1
        }

        n -= 60 // 1-3
        n = (n - 1) * 7 + rand7() // 1-21
        if n <= 20 {
            return n % 10 + 1
        }
    }
}
```

优化完了反而更慢，可能是因为优化前的代码对分支预测更友好一些？
