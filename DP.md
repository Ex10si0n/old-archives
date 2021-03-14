---
title: 「动态规划」01背包问题详解

date: 2017-08-18 12:24

tags: [动态规划]

categories: C++

thumbnail: http://img2.niutuku.com/desk/anime/4215/4215-4912.jpg
---

## 引入

什么是背包问题，按照顺序，我们先从装箱介绍：

## 装箱问题

> 有个一箱子的容量额为V（0<=V<=20000），同时有n个物品 (0<n<=30)，每个物品的体积值为正整数。
>
> 要求从ｎ个物品中，选取若干个装入箱内，使箱子的剩余空间最小。

#### 输入

2行整数，第1个数表示箱子的容量V，第2个数表示有n个物品，下一行n个数据表示n 个物品各自的体积。

#### 输出

背包剩余体积。

### 分析

贪心？或许可以解决，但是贪心不能保证全部是最优解。

暴力枚举？这个可以哦，还有什么方法吗？

搜索？很接近了，看到了搜索的万能了吧。

那是什么啊？

毕竟装箱嘛，我们暂且叫它一种能够达到最优的方案的规划问题，对不对。

换个小一点的表格：

我们造一组数据来描述这个思路：

```cpp
6 5								//volume=6 n=5
7 3 4 1 1						//Every goods' cost of volume
A B C D E						//Name
```

用表格描述一下：

第一行塞进去了A物品，不幸的是：冒了！

第二行塞进去了B，D，E三件物品，还剩下一个剩余空间。

第三行装进去了C，D，E三件物品，装满了。

其实都不需要往后面看了，因为我们已经得到了最优解。

>我们把它拆成了以下3种不需要再装入的情况：
>
>1.装冒了（类似第一行）
>
>2.装进去还剩下几个空，但是装不下去其他所有了（可能是最优解）
>
>3.装满了（一定是最优解）

|  1   |  2   |  3   |  4   |  5   |  6   |
| :--: | :--: | :--: | :--: | :--: | :--: |
|  x   |  x   |  x   |  x   |  x   |  x   |
|  x   |  x   |  B   |  D   |  E   | res  |
|  x   |  x   |  x   |  C   |  D   |  E   |
|  D   |  E   |  x   |  x   |  B   | res  |
|  D   |  E   |  x   |  x   |  x   |  C   |
|  E   |  D   |  x   |  x   |  x   |  C   |
|  x   |  x   |  x   |  C   | res  | res  |

怎么用代码来表述以上状态呢？你需要一个数组`f[V][n!]`来描述箱子中没一单位体积的状态，显然开二维很占空间，而且一个阶乘下来就不能做什么了。

#### 压缩！

我们将二维压缩成一位来节约时间空间，你可能会问那怎么存状态了啊。好问题，但是我们考虑一下：研究这道问题，我们终究需要什么？（可能变成了一个哲学问题），题目中是不是就问我们最优解剩余的体积，所以其他的一切东西我们就可以让他们消失了！

**也就是说，我能通过一种方式到达某个状态，那么这种状态就是能到达的，我们拥有了这个状态，之后我们可以不考虑它了，因为我们目的明确！** 接下来我们看`f[V]`数组。

我们先装入B(cost=3)试试，`1`代表存在此状态，即本图中体积为3时的状态已经存在了。

|  1   |  2   |  3   |  4   |  5   |  6   |
| :--: | :--: | :--: | :--: | :--: | :--: |
|      |      |  1   |      |      |      |

然后我们有几个选择：

>1.忘记`f[3]=1`状态我们从头开始
>
>2.接着`f[3]=1`状态继续生成下一个状态
>
>3.傻等着不动

对于现在，我们已经辛辛苦苦记好了一个状态，我们就跟着这个状态继续吧，尝试着装进去一个E物品。

|  1   |  2   |  3   |  4   |  5   |  6   |
| :--: | :--: | :--: | :--: | :--: | :--: |
|      |      |  1   |  1   |      |      |

此时存在着两种状态了，`f[3]=1`和`f[4]=1` 我们存在着更多的选择：

>1.从`f[3]=1`状态继续
>
>2.从`f[4]=1`状态继续
>
>3.从头开始
>
>~~4.傻等着不动~~

接下来更多复杂的就交给计算机囖。

回想一下这些状态，如果某组数据很棒棒，你有可能得到这样的`f[]数组`：

| 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 1    | 1    | 1    | 1    | 1    |

那也不要怕，这样的话，不就是最优解为`f[6]=1`这种状态嘛！

#### 但是注意了：（我知道你是不会注意到的）

每种物品只能放进去一次哦，这样的话可能会有bug的，比如`3=1(A)+2(B)=1(A)+1(A)+1(A)`

那么我们就得注意了，我们需要从后往前压入箱子。

```cpp
#include <iostream>
using namespace std;
int v,s,n,a[20005],f[20005];
int main(){
	cin>>v;
	cin>>n;
	f[0]=1;
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=n;i++)//枚举每一个物品
		for(int j=v;j<=a[i];j--)//对于每一个物品压到背包中，倒叙压
			if(f[j-a[i]]==1)//若此状态下j的前a[i]位为1，即装入此位置
				f[j]=f[j-a[i]];
	for(int i=v;i>=1;i--)
		if(f[i]==1){s=i;break;}
	cout<<s<<endl;
	return 0;
}
```

这就是装箱问题的总过程了，接下来提升一些难度，我们增加一个维度："价值"，接下来请看——

## 01背包问题

有N件物品和一个容量为V的背包。第i件物品的费用是`W[i]`，价值是`C[i]`。

求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

### **基本思路**

这是最基础的背包问题，特点是：每种物品仅有一件，可以选择放或不放。

用子问题定义状态：即`f[i][v]`表示前i件物品恰放入一个容量为`v`的背包可以获得的最大价值。

其实就是将装箱问题中的标`1`换成标`w[i]值`，易于理解。

装箱代码改造一下：

```cpp
#include <iostream>
using namespace std;
int V,s,n,v[20005],w[20005],f[20005];
int main(){
	cin>>V;
	cin>>n;
	//memset(f,-1,sizeof(f));
	f[0]=0;
	for(int i=1;i<=n;i++) cin>>v[i];
	for(int i=1;i<=n;i++) cin>>w[i];
	for(int i=1;i<=n;i++)//枚举每一个物品
		for(int j=V;j>=v[i];j--)//对于每一个物品压到背包中
			if(j-v[i]>0 && f[j-v[i]]>=0)//若此状态下j的前a[i]位不为0，即装入此位置
				f[j]=f[j-v[i]]+w[i];
	cout<<f[V]<<endl;
	return 0;
}

/*
/data 1
24
6
8 3 12 7 9 7
1 1 1  1 1 1 
out=3

/data 2
100
5
77 22 29 50 99
92 22 87 46 90
out=133

/data 3
300
10
95 75 23 73 50  22 6  57 89 98
89 59 19 43 100 72 44 16 7  64
out=388
*/

```

简化一下即：

```cpp
//背包问题标准码
#include <iostream>
using namespace std;
int V,s,n,v[20005],w[20005],f[20005];
int main(){
	cin>>V;
	cin>>n;
	for(int i=1;i<=n;i++) cin>>v[i]>>w[i];
	for(int i=1;i<=n;i++)
		for(int j=V;j>=v[i];j--)
			f[j]=max(f[j],f[j-v[i]]+w[i]);
	cout<<f[V]<<endl;
	return 0;
}
```