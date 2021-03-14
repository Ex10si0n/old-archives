---
title: 「优先级队列」codeVS-6130 布丁

date: 2017-10-07 20:21

tags: [优先级队列]

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-113387.png
---

###题目描述 Description

FJ建立了一个布丁工厂。在接下来的N(1<=N<=40,000)个星期里，原料牛奶和劳动力的价格会有很大波动。FJ希望能够在满足消费者需求的前提下尽量减小花费。

FJ预计接下来每个星期会需要Ci(1<=Ci<=5,000)元钱来生产一单位布丁，且消费者会需要Pi(0<=Pi<=10,000)单位布丁。

FJ每星期即可以生产布丁，也可以储存布丁供以后使用。它的仓库存储一星期一单位布丁需要S(1<=S<=100)元钱。但是由于布丁有保质期，所以最多只能保存T(0<=T<=40,000)星期。也就是说x星期生产的布丁可以在(x+T)星期销售，但是不能在(x+T+1)星期销售。

请帮助FJ安排生产与存储的方案使得在满足消费者需求的前提下尽量减小花费。

###输入描述 Input Description

第一行为N,S与T.

第二行到第(N+1)行：每一行两个数，即Ci与Pi。

###输出描述 Output Description

仅一个数，即满足顾客需求前提下的最小花费。

### 样例输入 Sample Input

```
5 10 3
12 1
21 2
27 4
45 5
52 3
```

###样例输出 Sample Output

```
488
```

###数据范围及提示 Data Size & Hint

1<=N<=40,000

1<=Ci<=5,000

0<=Pi<=10,000

1<=S<=100

0<=T<=40,000

结果可能很大，请使用合适的数据类型存储

## 题解报告

本题说来也很神奇，我是暴力70分，依然在想pq优化，先码起来算了，这道题重点就是在于需要从最后一天往前推，并且证明一下任意一周的布丁获得的渠道只能是本周生产或者之前几周保存下来的，然后比较在保质期内（包括本周）的布丁生产总花费 （包括生产费用和保存费用）得到本周的最优方案，以此类推。

```cpp
#include <iostream>
#include <queue>
#include <algorithm>
#include <cstdio>
#define maxn 10000005
using namespace std;
long long sum,pos,n,s,t,c[maxn],p[maxn];
int workto(int d){
	int m=99999999;
	for(int i=pos;i>=pos-t+1 && i>0;i--){
		if(p[d]*(c[i]+(pos-i)*s)<m){
			m=p[d]*(c[i]+(pos-i)*s);
		}
	}
	return m;
}
int main(){
	scanf("%d%d%d",&n,&s,&t);
	for(int i=1;i<=n;i++){
		scanf("%d%d",&c[i],&p[i]);
	}
	for(int i=1;i<=n;i++){
		pos=i;
		sum+=workto(i);
	}
	cout<<sum<<endl;
	return 0;
}
```

