---
title: BM98 螺旋矩阵
description: 牛客 - 数组 - 螺旋输出矩阵
slug: nc-bm98
date: 2023-03-01 14:28:00+0000
image: cover.png
comments: false
categories:
    - 算法
tags:
    - 数组
    - Easy
---
# BM98 螺旋矩阵
> https://www.nowcoder.com/practice/7edf70f2d29c4b599693dc3aaeea1d31
## 描述
给定一个mxn大小的矩阵（m行，n列），按螺旋的顺序返回矩阵中的所有元素。
## 要求：
空间复杂度 O(nm) ，时间复杂度 O(nm)

## 思路
1. 按照圈层遍历矩阵
2. 通过左上角坐标与右下角坐标确认便利圈层
3. 边界情况：构不成圈，一行或者一列或者一个节点的特殊处理
4. 特殊情况：空矩阵


## AC代码

``` go
package main
/**
 * 
 * @param matrix int整型二维数组 
 * @return int整型一维数组
*/
func spiralOrder( matrix [][]int ) []int {
    // write code here
    
    m := len(matrix)-1
    if m <= -1{
        return []int{}
    }
    n := len(matrix[0])-1
    s := 0
    t := 0

    res := make([]int,0,m*n)
    for s<=m && t<=n{
        if s == m{
            for i:=t;i<=n;i++{
                res = append(res, matrix[s][i])
            }
            return res
        }

        if n == t {
            for i:=s;i<=m;i++{
                res = append(res,matrix[i][n])
            }
            return res
        }

        for i:=t;i<n;i++{
            res = append(res, matrix[s][i])
        }

        for i:=s;i<m;i++{
            res = append(res, matrix[i][n])
        }

        for i:=n;i>t;i--{
            res = append(res,matrix[m][i])
        }

        for i:=m;i>s;i--{
            res = append(res,matrix[i][t])
        }

        s += 1
        t += 1
        m -= 1
        n -= 1
    }
    return res
}
```