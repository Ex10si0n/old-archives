---
title: Peak Finder 的 1D,2D算法解释
date: 2019-11-03 16:54
tags: 
	- PeakFinder
	- 峰值查找
categories:
	- Algorithms
cover: https://w.wallhaven.cc/full/r2/wallhaven-r26k37.jpg
---

# 概述

本文简要描述峰值查找算法的一维和二维实现
MIT OpenCourse 6.006 Introduction to Algorithms (Spring 2008) 课程笔记

# 一维实现代码(Python)
```python
##############################
# 1D PeakFinder Algorithm    #
# Time Complexity: O(log N)  #
# CC-BY 2.0 Author: Ex10si0n #
##############################
def peak_finder1d():
  l = 0
  r = 7
  while l < r:
    mid = ( l + r ) / 2
    if a[mid-1] <= a[mid] <= a[mid + 1]:
      l = mid + 1
    elif a[mid - 1] >= a[mid] >= a[mid+1]:
      r = mid - 1
    else:
      return a[mid]
```
时间复杂度证明：
> T (n) = T (n/2) + Θ(1) = Θ(1) + . . . + Θ(1) (log2(n) times) = Θ(log2(n))

• Ifa[n/2] < a[n/2−1]then only look at left half 1...n/2 −−− 1 to look for peak
• Else if a[n/2] > a[n/2+1] then only look at right half n/2+1...n to look for peak 
• Else n/2 position is a peak

# 二维实现代码(Python)

```python
##############################
# 2D PeakFinder Algorithm    #
# Time Complexity: O(MlogN)  #
# CC-BY 2.0 Author: Ex10si0n #
##############################
def peak_finder2d():
  l = 0
  r = 3
  while l < r:
    mid = ( l + r ) / 2
    mmax = 0
    j = 0
    for i in range(0,4):
      if mmax < b[mid][i]:
        mmax = b[mid][i]
        j = i
    if b[mid-1][j] <= b[mid][j] <= b[mid+1][j]:
      l = mid + 1
    elif b[mid+1][j] >= b[mid][j] >= b[mid-1][j]:
      r = mid - 1
    else:
      return b[mid][j]
```
实现方法 (原文链接：[PDF Files Download](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-006-introduction-to-algorithms-fall-2011/lecture-videos/MIT6_006F11_lec01.pdf) )
• Pick middle column j = m/2
• Find global maximum on column j at (i,j)
• Compare(i,j−1),(i,j),(i,j+1)
• Pickleftcolumnsof(i,j−1)>(i,j)
• Similarly for right
• (i,j) is a 2D-peak if neither condition holds ← WHY?
• Solve the new problem with half the number of columns.

