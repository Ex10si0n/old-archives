---
title: 「图论」SPFA单源最短路径算法

date: 2017-08-08 17:24

tags: [SPFA]

categories: C++

thumbnail: http://i1.bvimg.com/1949/8396ddeb1359b6e9t.jpg
---

## 最短路径算法

首先感谢[GGN的博客](http://blog.csdn.net/ggn_2015/article/details/75502100)带来精彩的最短路讲解，让我受益匪浅！

目前的最短路算法有几种：

>Floyed-Warshall   $O(n^3)$
>
>Dijkstra  $O(n^2)$
>
>SPFA  $O(KE) K<=2$ 

那么这几种最短路的求法在GGN博客也已经有所讲解，今天我就来讲解一下SPFA算法，我们用SPFA如何来求单源最短路径（就是一个源点求到每个点的最短距离）

## SPFA详解

说起详细解释SPFA怎么理解，类似BFS的操作，但是有区别。慢慢来！

我们分析最短路径怎么求：

>如果 a -> b -> c 长度大于 a -> c ，那么我们将前者更新后者

算是很好理解的，区别于Dijkstra，SPFA降低了时间复杂度，因为Dijkstra的$O(n^2)$可能会超时爆内存，比起SPFA，Dijkstra更像枚举，而SPFA用队列持续更新最短路径，保证了高效的算法核心。

![实例图](http://www.layz.net/LAOJ/suanfa/pic/s9-4-1.jpg)

![运行队列](http://www.layz.net/LAOJ/suanfa/pic/s9-4-2.jpg)



观察过SPFA大概的操作，发现了其实很像BFS的，以下用代码解释SPFA，仔细看，我觉得写的很好理解哦。

```cpp
#include<iostream>
#define maxn 1000
using namespace std;
int map[maxn][maxn];
bool vis[maxn]={0};//标记数组
int dis[maxn];//存每个点对应的最短路长度
int que[maxn];//上图的队列
int n,e,s,x,y,z,head,tail;//不着急知道这些都是干什么的，下面能看懂
void SPFA(int u){//u为源点
	int x;
	//初始化过程
	head=0;
	tail=1;
	dis[u]=0;//从u到u距离为0
	que[1]=u;//将源点u入队
	vis[u]=1;//标明此时u在队列中，但是u可以再次进入队列
	while(head<tail)//当队列非空，没有任何情况能更新最短路的时候
	{
		head++;
		x=que[head];
		for(int i=1;i<=n;i++)
		{
			if(map[x][i]!=0 && dis[i]>dis[x]+map[x][i])//寻找与x(队头)相连的点 && 新的路径长度小于dis存储的最短路
			{
				dis[i]=dis[x]+map[x][i];
				if(!vis[i])//i点不在队里时
				{
					tail++;
					que[tail]=i;//放入队尾
					vis[i]=1;//表明i在队里
				}
			}
		}
		vis[x]=0;//将x点出队
	}
}
int main(){
	cin>>n>>e;//点数 边数
	for(int i=1;i<=e;i++)//邻接矩阵存
	{
		cin>>x>>y>>z;
		map[x][y]=z;
		map[y][x]=z;
	}
	memset(dis,127,sizeof(dis));//赋值int_max
	cin>>s;
	SPFA(s);//从源点开始SPFA
	for(int i=1;i<=n;i++){
		cout<<"第"<<i<<"个点的最短路："<<dis[i]<<endl;
	}
	return 0;
}
/*
:数据 1
5 7
1 2 4
1 3 29
3 2 6
2 5 3
3 5 4
4 5 6
1 4 4
1
*/
```

但是用次方法的邻接矩阵存会很慢，而且还需要考虑：

> 负环会炸
>
> 可以用vector优化

以后陆续更新哦！