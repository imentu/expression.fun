---
title: "LeetCode-51 N 皇后"
date: 2021-10-11T10:18:26+08:00
draft: false
tags: ["LeetCode"]
keywords: []
description: ""
slug: "N Queens"
---

题目地址：[LeetCode-51 N 皇后](https://leetcode-cn.com/problems/n-queens/)

## 思路

用 DFS 搜索所有方案即可。

注意：不能用设置标志位的方式判断能否放皇后，因为两个皇后的冲突标志可能重复，如果去除一个的标志位可能把另一个的标志位也抹去。正确的方法是以新的皇后为起点检测冲突。

## 代码

```go
func solveNQueens(n int) [][]string {
    result := [][]string{}

    m := [][]int{}
    for i := 0; i < n; i++ {
        m = append(m, make([]int, n))
    }

    count(0, n, &m, &result)
    return result
}

func count(column, n int, m *[][]int, result *[][]string) {
    if column >= n {
        appendResult(n, m, result)
        return
    }

    for i := 0; i < n; i++ {
        if (noConflict(i, column, n, m)) {
            (*m)[i][column] = 1

            count(column + 1, n, m, result)

            (*m)[i][column] = 0
        }
    }
}

func noConflict(x, y, n int, m *[][]int) bool {
    dx := [8]int{-1, 1, 0, 0, -1, -1, 1, 1}
    dy := [8]int{0, 0, -1, 1, -1, 1, -1, 1}

    for i := 0; i < 8; i++ {
        nx := x + dx[i]
        ny := y + dy[i]
        for {
            if nx < 0 || nx >= n || ny < 0 || ny >= n {
                break
            }

            if ((*m)[nx][ny] == 1) {
                return false
            }

            nx += dx[i]
            ny += dy[i]
        }
    }

    return true
}

func appendResult(n int, m *[][]int, result *[][]string) {
    current := []string{}
    for i := 0; i < n; i++ {
        line := ""
        for j:= 0; j < n; j++ {
            if (*m)[i][j] == 1 {
                line += "Q"
            } else {
                line += "."
            }
        }
        current = append(current, line)
    }
    *result = append(*result, current)
}
```

