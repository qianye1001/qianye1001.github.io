---
title: LC912 数组排序
description: 快排超时！坑！
slug: lc-912
date: 2023-03-13 14:28:00+0000
image: cover.png
comments: false
categories:
    - 算法
tags:
    - 数组
    - 排序
---

# 快排为什么会超时

今日做LC912的排序，经历了惨痛的失败，信心满满的写出来一个快排，结果毫不留情超时，以为是自己的排序写的不多，核对再三发现样例中有超长逆序样例。

## 原解

原本用的就是一个简单的快速排序，用最左作为基准点，但是这题目坑就在这了

``` go
func sortArray(nums []int) []int {
    quickSort(nums)
    return nums
}

func quickSort(nums []int){
    if len(nums)<=1{
        return
    }

    i,j := 0,len(nums)-1
    order := true
    for i<j{
        if order{
            if nums[i]<nums[j]{
                j--
            }else{
                nums[i],nums[j]=nums[j],nums[i]
                order = !order
                i++
            }
        }else{
            if nums[i]<nums[j]{
                i++
            }else{
                nums[i],nums[j]=nums[j],nums[i]
                order = !order
                j--
            }
        }
    }
    quickSort(nums[0:i])
    if i <len(nums)-1{
        quickSort(nums[i+1:])
    }
}
```

## 快排优化的方式

快排优化的方式常见的有：

- 当数列近乎有序的时，由于每次选取的都是第一个数，所以造成数列分割的极其不等，此时快排蜕化成O (n * n)的算法， 此时只要随机选取基准点即可

-当数列中包含大量的重复元素的时候，这一版的代码也会造成"分割不等“的问题，此时需要将重复元素均匀的分散的自数列旁

- 使用三路快排

## 尝试优化
首先观察发现超时样例确实是因为数列近乎有序，所以首先加上随机取点，代码果然AC了

```go
func sortArray(nums []int) []int {
    rand.Seed(2)
    quickSort(nums,0,len(nums))
    return nums
}

func quickSort(nums []int, l,r int){
    if r-l<=1{
        return
    }

    //随机选择一个点与最左进行交换
    point := rand.Intn(r-l)+l
    nums[point],nums[l] =nums[l],nums[point]

    i,j := l,r-1
    order := true
    for i<j{
        if order{
            if nums[i]<nums[j]{
                j--
            }else{
                nums[i],nums[j]=nums[j],nums[i]
                order = !order
                i++
            }
        }else{
            if nums[i]<nums[j]{
                i++
            }else{
                nums[i],nums[j]=nums[j],nums[i]
                order = !order
                j--
            }
        }
    }
    quickSort(nums,l,i)
    if i <len(nums)-1{
        quickSort(nums,i+1,r)
    }
}
```

## 三路快排

三路快排考虑了大量重复元素的情况，可以参考LC75. 颜色分类

``` go
func sortArray(nums []int) []int {
    rand.Seed(2)
    quickSort(nums,0,len(nums))
    return nums
}

func quickSort(nums []int, l,r int){
    if r-l<=1{
        return
    }

    point := rand.Intn(r-l)+l
    nums[point],nums[l] =nums[l],nums[point]

    lt,gt:=l,r
    i:=l+1
    for i<gt{
        if nums[i] == nums[l]{
            i++
        }else if nums[i] < nums[l]{
            nums[lt+1],nums[i]=nums[i],nums[lt+1]
            lt++
            i++
        }else{
            nums[gt-1],nums[i]=nums[i],nums[gt-1]
            gt--
        }
    }
    nums[l],nums[lt]=nums[lt],nums[l]
    quickSort(nums,l,lt)
    quickSort(nums,gt,r)
}
```