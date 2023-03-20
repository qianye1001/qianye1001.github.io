---
title: LC42 接雨水
description: 多种解法逐步寻找最优
slug: lc-42
date: 2023-03-19 14:28:00+0000
image: cover.png
comments: false
categories:
    - 算法
tags:
    - 动态规划
    - 双指针
---

# 接雨水

今日做LC42的接雨水，逐步优化代码，写一篇博客记录一下。

## 暴力解

## 动态规划解
动态规划解的思路是，将每一列的雨水看为收集的对象，可以发现每一列的雨水取决于这一列左边最高的柱子和右边最高的柱子中的较矮的一个，所以我们可以利用dp数组记录每一列左边最高的柱子和右边最高的柱子，再将每一列雨水通过`min(left[i], right[i]) - arr[i]`进行收集。
```go
package main

func maxWater(arr []int) int64 {
	// write code here
	left := make([]int, len(arr))
	right := make([]int, len(arr))
	left[0] = 0
	right[len(right)-1] = 0

	for i := 1; i < len(arr); i++ {
		left[i] = max(left[i-1], arr[i-1])
	}
	for i := len(right) - 2; i >= 0; i-- {
		right[i] = max(right[i+1], arr[i+1])
	}

	res := 0
	for i := 0; i < len(arr); i++ {
		w := min(left[i], right[i]) - arr[i]
		if w > 0 {
			res += w
		}
	}
	return int64(res)
}

func max(a, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

func min(a, b int) int {
	if a > b {
		return b
	} else {
		return a
	}
}
```
## 双指针
双指针方法的优点在于节约了dp数组的存储空间，空间复杂度`O(1)`且只遍历一次，相比dp多次要快，其核心思路是通过记录左右的最大值作为其边界，并控制左右边界向内收缩
```go
package main

func maxWater(arr []int) int64 {
	// write code here
	if len(arr) <= 2 {
		return 0
	}
	left, right := 0, len(arr)-1
	maxL, maxR := arr[left], arr[right]
	res := 0
	for left < right {
		maxL = max(maxL, arr[left])
		maxR = max(maxR, arr[right])

		if maxL <= maxR {
			left++
			if maxL-arr[left] > 0 {
				res += maxL - arr[left]
			}
		} else {
			right--
			if maxR-arr[right] > 0 {
				res += maxR - arr[right]
			}
		}
	}
	return int64(res)

}

func max(a, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}
```
