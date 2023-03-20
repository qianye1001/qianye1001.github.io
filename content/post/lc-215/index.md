---
title: LC215 数组中的第K个最大元素
description: 堆排与快排的基本功
slug: lc-215
date: 2023-03-15 14:28:00+0000
image: cover.png
comments: false
categories:
    - 算法
tags:
    - 数组
    - 排序
---

# 数组中的第K个最大元素

今日做LC215的排序，深感自己堆排与快排的基本功不扎实，写一篇博客记录一下。

## 堆排解

如果是笔试题目，那么可以直接实现go的heap接口，就能使用堆排序了，但是面试显然不能这么做，还是需要搞清楚堆排序的原理。这一题的堆排解的思路大致分为三个部分：

- 构建最大堆
- 从最大堆中Pop 个数

### 官方库实现

思路有了，最关键的就是如何实现堆排序，我们可以看一下go官方库`container/heap`

官方库中首先定义了一个接口，如果我们平时需要用堆可以实现该接口

```go
type Interface interface {
    sort.Interface
    Push(x any) // add x as element Len()
    Pop() any   // remove and return element Len() - 1.
}
```

初始化函数，用来构造一个堆，由于我们只需要关注非叶子节点，所以这里的循环只需要`i := n/2 - 1`，然后我们可以看到关键的函数是`down`下面来看如何是实现

```go
func Init(h Interface) {
	// heapify
	n := h.Len()
	for i := n/2 - 1; i >= 0; i-- {
		down(h, i, n)
	}
}
```

发现`j1 := 2*i + 1`是找左孩子节点，同理`j2`就是右孩子节点，通过比较找出父节点、左右孩子节点中最小的一个并与父节点交换，依次是一个从上至下的下沉过程

```go
func down(h Interface, i0, n int) bool {
	i := i0
	for {
		j1 := 2*i + 1
		if j1 >= n || j1 < 0 { // j1 < 0 after int overflow
			break
		}
		j := j1 // left child
		if j2 := j1 + 1; j2 < n && h.Less(j2, j1) {
			j = j2 // = 2*i + 2  // right child
		}
		if !h.Less(j, i) {
			break
		}
		h.Swap(i, j)
		i = j
	}
	return i > i0
}
```

构造之后再来看看如何加入节点，发现其中关键是`up`函数，看看其实现，其与`down`函数不同的是，这是自下而上，从叶子节点逐渐与父节点比较，最终到root结束，是一个上浮的过程。

```go
func Push(h Interface, x any) {
	h.Push(x)
	up(h, h.Len()-1)
}

func up(h Interface, j int) {
	for {
		i := (j - 1) / 2 // parent
		if i == j || !h.Less(j, i) {
			break
		}
		h.Swap(i, j)
		j = i
	}
}
```

最后再来看看如何pop最小值，将root与最后一个叶子交换，并让叶子下沉

```go
func Pop(h Interface) any {
	n := h.Len() - 1
	h.Swap(0, n)
	down(h, 0, n)
	return h.Pop()
}
```

### 官方使用例

最后再看看go官方给的例子

```go
// Copyright 2012 The Go Authors. All rights reserved.
// Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

// This example demonstrates an integer heap built using the heap interface.
package heap_test

import (
	"container/heap"
	"fmt"
)

// An IntHeap is a min-heap of ints.
type IntHeap []int

func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *IntHeap) Push(x any) {
	// Push and Pop use pointer receivers because they modify the slice's length,
	// not just its contents.
	*h = append(*h, x.(int))
}

func (h *IntHeap) Pop() any {
	old := *h
	n := len(old)
	x := old[n-1]
	*h = old[0 : n-1]
	return x
}

// This example inserts several ints into an IntHeap, checks the minimum,
// and removes them in order of priority.
func Example_intHeap() {
	h := &IntHeap{2, 1, 5}
	heap.Init(h)
	heap.Push(h, 3)
	fmt.Printf("minimum: %d\n", (*h)[0])
	for h.Len() > 0 {
		fmt.Printf("%d ", heap.Pop(h))
	}
	// Output:
	// minimum: 1
	// 1 2 3 5
}

```

### 题目解

``` go
func findKthLargest(nums []int, k int) int {
    size := len(nums)
    build(nums, size)
    for i:=0;i<k-1;i++{
        nums[0],nums[size-1]=nums[size-1],nums[0]
        size--
        fetch(nums, size, 0)
    }
    return nums[0]
}

func build(nums []int,size int){
    for i:=size/2;i>=0;i--{
        fetch(nums, size, i)
    }
}

func fetch(nums[]int,size int,i int){
    l,r := i*2+1,i*2+2
    choose := i
    if l<size && nums[l] > nums[choose]{
        choose = l
    }
    if r<size && nums[r] > nums[choose]{
        choose = r
    }
    if choose != i{
        nums[choose],nums[i] = nums[i],nums[choose]
        fetch(nums, size , choose)
    }
}
```

## 快排解

快排解是利用快排的分治思路，每次分割 `<p` 和 `>p`两个部分，但是我们需要的第k个数只在其中的一个区间内，而另一个区间是否有序我们并不关心，所以可以只选择一个部分继续排序，相较于原本的快排，不完整的快排的时间复杂度为`O(n)`，上一篇博客详细写了快排的实现
```go
package main
import "math/rand"
/**
 * 
 * @param a int整型一维数组 
 * @param n int整型 
 * @param K int整型 
 * @return int整型
*/

func findKth( a []int ,  n int ,  K int ) int {
    // write code here
    rand.Seed(2)
    return quick(a,n,K)
}

func quick(a []int, n int, K int) int {
	t := rand.Intn(n)
	a[0], a[t] = a[t], a[0]
	lptr, rptr := 1, n-1
	for lptr <= rptr {
		if a[lptr] > a[0] {
			a[lptr], a[rptr] = a[rptr], a[lptr]
			rptr--
		} else {
			lptr++
		}
	}
	if n-lptr+1 == K {
		return a[0]
	} else if K <= n-lptr {
		return quick(a[lptr:], n-lptr, K)
	} else {
		a[0], a[lptr-1] = a[lptr-1], a[0]
		return quick(a[:lptr-1], lptr-1, K-n+lptr-1)
	}
}
```