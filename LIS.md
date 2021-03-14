---
title: 「动态规划」最长不下降子序列

date: 2017-07-01 17:09

tags: [动态规划]

categories: C++

thumbnail: http://i4.bvimg.com/1949/2bd2b3bc332ec71ft.jpg
---



# 「动态规划初步」最长不下降子序列

动态规划作为OI的很重要的考点，值得我们认真学习，今天以LIS (最长不下降子序列) 为例，带大家实际了解一下LIS的原理

## 代码

```cpp
# include<cstdio>
# include<algorithm>
using namespace std;
int a[40005];
int d[40005];//编号数组
int main(){
    int n;
    scanf("%d",&n);
    for (int i=1;i<=n;i++) scanf("%d",&a[i]);
    d[1]=a[1];
    int len=1;
    for (int i=2;i<=n;i++){
        if(a[i]>=d[len]) d[++len]=a[i];
        else{
            d[upper_bound(d+1,d+len+1,a[i])-d]=a[i];
        }
    }
    printf("%d\n",len);
    return 0;
}

```

值得我们注意的是LIS是如何dp的，看到简短的代码大家可能会感觉有些模糊，现在我来做一个简短的分析：

首先看这个数列

```python
2 1 3 4 3 4 5 6 7 2 8 9
```

怎么手动找LIS呢？我们模拟一下过程

```python
//数组
//^
//到此的LIS长度


2 1 3 4 3 4 5 6 7 2 8 9
^
1

2 1 3 4 3 4 5 6 7 2 8 9
^ ^
1 1

2 1 3 4 3 4 5 6 7 2 8 9
^ ^ ^
1 1 2

2 1 3 4 3 4 5 6 7 2 8 9
^ ^ ^ ^
1 1 2 3

2 1 3 4 3 4 5 6 7 2 8 9
^ ^ ^ ^ ^
1 1 2 3 2

2 1 3 4 3 4 5 6 7 2 8 9
^ ^ ^ ^ ^ ^
1 1 2 3 2 3

...   ...   ...   ...   

2 1 3 4 3 4 5 6 7 2 8 9
^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^
1 1 2 3 2 3 4 5 6 2 7 8

//LIS长度为8
```

在填入数字的过程中，我们模拟一件事情：

>第一位标1
>
>对于第n位，若n-1位小于n，n的标号继承n-1位标号并在此基础上+1
>
>对于第n位，若n-1位不小于n，n的标号继承1～n-2位小于n的最大标号并在此基础上+1

```python
2 % 3 4 * 4 5 6 7 2 8 9
^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^
1 1 2 3 2 3 4 5 6 2 7 8
```

我们关注一下*号位的3，由于第四位比3大，它的标号继承了%位的1的下表 (值为1)，所以\*位置的标号为1+1

然而代码中的这句话

```cpp
d[upper_bound(d+1,d+len+1,a[i])-d]=a[i];
```

upper_bound的意思是二分查找上界，下面这段代码还原了upper_bound的用法和逻辑:

```cpp
int upper_bound(int *array, int size, int key)
{
    int len = size-1;
    int half, middle;

    while(len > 0){
        half = len >> 1;
        middle = first + half;
        if(array[middle] > key)     //中位数大于key,在包含last的左半边序列中查找。
            len = half;
        else{
            first = middle + 1;    //中位数小于等于key,在右半边序列中查找。
            len = len - half - 1;
        }
    }
    return first;
}
```

注意upper_bound是直接在STL中有的，比赛时可以直接使用，不需要自己再写函数了。

LIS的主要思想就是这些了，这是dp的基础，掌握好dp可谓是很有重要意义的。

