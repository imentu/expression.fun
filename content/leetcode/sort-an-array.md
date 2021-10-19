---
title: "LeetCode-912 排序数组"
date: 2021-10-15T19:29:15+08:00
draft: false
tags: ["LeetCode"]
keywords: ["排序", "归并排序", "快速排序"]
description: ""
slug: "Sort an Array"
---

题目地址：[LeetCode-912 排序数组](https://leetcode-cn.com/problems/sort-an-array/)

## 代码

### 自顶向下归并

**注意**：右半边要选 $[mid + 1， r]$，因为右半边选 $[mid, r]$ 时，遇到 $(l = 2, r= 3, mid = 2)$ 的情况会无限递归爆栈(整数向下取整导致 $r - l = 1$ 时，$mid = l$ )。

```go
/*
	执行耗时:44 ms,击败了93.31% 的Go用户
	内存消耗:7.2 MB,击败了97.22% 的Go用户
*/
func sortArray(nums []int) []int {
	n := len(nums)
	aux := make([]int, n)
	mergeSort(nums, aux, 0, n-1)
	return nums
}

func mergeSort(arr, aux []int, l, r int) {
	if l == r {
		return
	}
	mid := l + (r-l)/2
	mergeSort(arr, aux, l, mid)
	mergeSort(arr, aux, mid+1, r)

	merge(arr, aux, l, mid, r)
}

func merge(arr, aux []int, l, mid, r int) {
	for i := l; i <= r; i++ {
		aux[i] = arr[i]
	}

	for i, j, k := l, mid+1, l; k <= r; k++ {
		if i > mid {
			arr[k] = aux[j]
			j += 1
		} else if j > r {
			arr[k] = aux[i]
			i += 1
		} else if aux[i] > aux[j] {
			arr[k] = aux[j]
			j += 1
		} else {
			arr[k] = aux[i]
			i += 1
		}
	}
}
```

### 自底向上归并

**注意**：$mid$ 的定义，以及各个边界的判断。

```go
/*
	执行耗时:48 ms,击败了81.57% 的Go用户
	内存消耗:7.2 MB,击败了98.31% 的Go用户
*/
func sortArray(nums []int) []int {
	n := len(nums)
	mr := n - 1
	aux := make([]int, n)
	for sz := 1; sz < n; sz += sz {
		for l := 0; l+sz < n; l += sz + sz {
			merge(nums, aux, l, l+sz-1, min(l+sz+sz-1, mr))
		}
	}
	return nums
}

func merge(arr, aux []int, l, mid, r int) {
	for i := l; i <= r; i++ {
		aux[i] = arr[i]
	}

	for i, j, k := l, mid+1, l; k <= r; k++ {
		if i > mid {
			arr[k] = aux[j]
			j += 1
		} else if j > r {
			arr[k] = aux[i]
			i += 1
		} else if aux[i] > aux[j] {
			arr[k] = aux[j]
			j += 1
		} else {
			arr[k] = aux[i]
			i += 1
		}
	}
}

func min(x, y int) int {
	if x < y {
		return x
	} else {
		return y
	}
}
```

### 快速排序

- $a[l..lt - 1] < a[l]$
- $a[gt + 1..r] > a[l]$
- $a[lt..i - 1] = a[l]$
- $a[i..gt]$ 未确定

```go
/*
	执行耗时:52 ms,击败了66.60% 的Go用户
	内存消耗:7.5 MB,击败了69.00% 的Go用户
*/
func sortArray(nums []int) []int {
	n := len(nums)
	qsort(nums, 0, n-1)
	return nums
}

func qsort(arr []int, l, r int) {
	if l >= r {
		return
	}
	lt, i, gt := l, l+1, r
	v := arr[l]
	for i <= gt {
		if arr[i] < v {
			swap(arr, lt, i)
			lt += 1
			i += 1
		} else if arr[i] > v {
			swap(arr, i, gt)
			gt -= 1
		} else {
			i += 1
		}
	}
	qsort(arr, l, lt-1)
	qsort(arr, gt+1, r)
}

func swap(arr []int, i, j int) {
	tmp := arr[i]
	arr[i] = arr[j]
	arr[j] = tmp
}
```

