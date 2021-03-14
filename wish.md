---
title: 「区间dp」育才高中部信息学模拟赛 9.25 T3 男神的礼物

date: 2017-10-07 21:50

tags: [区间dp]

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-233088.jpg
---

## 题目背景

小阎有一个祝福信物，他希望使大家都会得到女神的祝福。所以他准备给每个人送一份祝福礼物。

## 题目描述

为了准备一份礼物，他要加工 n 份材料，并且每一次只能加工相邻的材料。当小阎 加工两个魔法值为 a,b 的材料，他都要消耗 a*b 的体力，同时在这个地方合成出魔法值(a+b)%100 的材料。为了能节省体力来完成他的礼物，小阎想找聪明的你帮他算一算他所要花费的最小体力。

## 输入输出格式

输入格式：

第一行，一个整数 T，表示男神所要准备的礼物数之后的 T 组数据，各有两行数据，第一行有一个整数 n，表示这份礼物的材料数。接下来一行，有 n 个整数 ai，表示这件礼物第 i 份材料的魔法值。

输出格式：

每组数据输出一行，表示小阎制作这份礼物所要的最小体力。

## 输入输出样例

输入样例#1：

```
2
2
15 19
3
30 40 20
```

输出样例#1：

```
285
2600
```

## 说明

30 % n<=9

100 % n,a<=100，T<=5



## 题解报告

在此感谢一下这篇博客 [【经典题目】& 合并吧！石子合并 与 合并果子](http://blog.csdn.net/Tiw_Air_Op1721/article/details/77434815#动规-区间dp) 讲的石子归并就是这道题的原题，其中的分析：

—————————————————————————————————————————

#### 区域DP->状态转移方程式

我们分析问题结构时，有提到大区间的最优值是由小区间【而且是相邻的】推导而来的。那么一般地，状态转移方程式有如下形式: 

f[i][j]=max(f[i+1][j],f[i][j−1])+g[i][j]

####深度解析-代码

在这道题中，我们定义状态f[i][len]为【以i为起点，长度为len的石堆合并的最小代价】，辅助数组g[i][j]为第i堆石子到第j堆石子的石子和，目标状态为f[1][n]，初始化f[i][1]为0。然后最重要的状态转移方程式就是—— 

f[i][len]=max(f[i][len],f[i][k−i+1]+f[k+1][j−k]+g[i][j])

其中，j=i+len-1，k是要合并的两堆石子的断开点。 

—————————————————————————————————————————

那么在学习完这位大神的代码之后，我也敲出了这道题(wish)的AC代码：

```cpp
#include <iostream>
#define maxn 105
#define INF 9999999
using namespace std;
int T,n;
int g[maxn][maxn],f[maxn][maxn];
//g维护第i到第j个礼物合并后的值
//f 优化：从第i个礼物合并len长度得到的最优解
//f (i,i+len) = min{ f ( i,i+len ) , f ( i,i+k ) + f( i+k+1 , i+len ) + g ( i , k )* g ( k+1 , i+len) } 
void dp(){
	cin>>n;
	for(int i=1;i<=n;i++)	cin>>g[i][i];
	for(int i=1;i<=n-1;i++)
		for(int j=i+1;j<=n;j++)
			g[i][j]=(g[i][j-1]+g[j][j])%100;
	for(int len=2;len<=n;len++)
        for(int i=1;i<=n-len+1;i++)
            f[i][len]=INF;
    for(int len=2;len<=n;len++)
        for(int i=1;i<=n-len+1;i++)
        {
            int j=i+len-1;
            for(int k=i;k<j;k++)
                f[i][len]=min(f[i][len],f[i][k-i+1]+f[k+1][j-k]+g[i][k]*g[k+1][j]);
        }
    cout<<f[1][n]<<endl;
}

int main(){
	cin>>T;
	while(T--){
		dp();
	}
	return 0;
}
```

