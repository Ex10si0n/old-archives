---
title: 「图论」SPFA优化算法

date: 2017-08-08 17:24

tags: [SPFA]

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-451325.jpg
---

## 误删，尴尬

昨天搞到很晚的SPFA优化一不小心被今天的POJ1860覆盖了，我只好再码一遍，昨天转了[某位大神的博客](http://www.layz.net/LAOJ/suanfa/s9-4.html)，很好的解释了SPFA，凭我的记忆，还有NOCOW上的码起来的链接：[NOCOW](http://www.nocow.cn/index.php/SPFA)，以及超棒的注释代码：

```cpp
/*
 * 单源最短路算法SPFA,时间复杂度O(kE),k在一般情况下不大于2,对于每个顶点使用可以在O(VE)的时间内算出每对节点之间的最短路
 * 使用了队列,对于任意在队列中的点连着的点进行松弛,同时将不在队列中的连着的点入队,直到队空则算法结束,最短路求出
 * SPFA是Bellman-Ford的优化版,可以处理有负权边的情况
 * 对于负环,我们可以证明每个点入队次数不会超过V,所以我们可以记录每个点的入队次数,如果超过V则表示其出现负环,算法结束
 * 由于要对点的每一条边进行枚举,故采用邻接表时时间复杂度为O(kE),采用矩阵时时间复杂度为O(kV^2)
 */
#include<cstdio>
#include<vector>
#include<queue>
#define MAXV 10000
#define INF 1000000000 //此处建议不要过大或过小,过大易导致运算时溢出,过小可能会被判定为真正的距离
 
using std::vector;
using std::queue;
 
struct Edge{
	int v; //边权
	int to; //连接的点
};
 
vector<Edge> e[MAXV]; //由于一般情况下E<<V*V,故在此选用了vector动态数组存储,也可以使用链表存储
int dist[MAXV]; //存储到原点0的距离,可以开二维数组存储每对节点之间的距离
int cnt[MAXV]; //记录入队次数,超过V则退出
queue<int> buff; //队列,用于存储在SPFA算法中的需要松弛的节点
bool done[MAXV]; //用于判断该节点是否已经在队列中
int V; //节点数
int E; //边数
 
bool spfa(const int st){ //返回值:TRUE为找到最短路返回,FALSE表示出现负环退出
	for(int i=0;i<V;i++){ //初始化:将除了原点st的距离外的所有点到st的距离均赋上一个极大值
		if(i==st){
			dist[st]=0; //原点距离为0;
			continue;
		}
		dist[i]=INF; //非原点距离无穷大
	}
	buff.push(st); //原点入队
	done[st]=1; //标记原点已经入队
	cnt[st]=1; //修改入队次数为1
	while(!buff.empty()){ //队列非空,需要继续松弛
		int tmp=buff.front(); //取出队首元素
		for(int i=0;i<(int)e[tmp].size();i++){ //枚举该边连接的每一条边
			Edge *t=&e[tmp][i]; //由于vector的寻址速度较慢,故在此进行一次优化
			if(dist[tmp]+(*t).v<dist[(*t).to]){ //更改后距离更短,进行松弛操作
				dist[(*t).to]=dist[tmp]+(*t).v; //更改边权值
				if(!done[(*t).to]){ //没有入队,则将其入队
					buff.push((*t).to); //将节点压入队列
					done[(*t).to]=1; //标记节点已经入队
					cnt[(*t).to]+=1; //节点入队次数自增
					if(cnt[(*t).to]>V){ //已经超过V次,出现负环
						while(!buff.empty())buff.pop(); //清空队列,释放内存
						return false; //返回FALSE
					}
				}
			}
		}
		buff.pop();//弹出队首节点
		done[tmp]=0;//将队首节点标记为未入队
	}
	return true; //返回TRUE
} //算法结束
 
int main(){ //主函数
	scanf("%d%d",&V,&E); //读入点数和边数
	for(int i=0,x,y,l;i<E;i++){
		scanf("%d%d%d",&x,&y,&l); //读入x,y,l表示从x->y有一条有向边长度为l
		Edge tmp; //设置一个临时变量,以便存入vector
		tmp.v=l; //设置边权
		tmp.to=y; //设置连接节点
		e[x].push_back(tmp); //将这条边压入x的表中
	}
	if(!spfa(0)){ //出现负环
		printf("出现负环,最短路不存在\n");
	}else{ //存在最短路
		printf("节点0到节点%d的最短距离为%d",V-1,dist[V-1]);
	}
	return 0;
}
```

## 囤起来的链接

**基本遍历算法：**[宽度优先搜索](http://www.nocow.cn/index.php/%E5%AE%BD%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2) - [深度优先搜索](http://www.nocow.cn/index.php/%E6%B7%B1%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2) - [A*](http://www.nocow.cn/index.php/A*) - **并查集求连通分支** - **Flood Fill**

**最短路：**[Dijkstra](http://www.nocow.cn/index.php/Dijkstra%E7%AE%97%E6%B3%95) - [Bellman-Ford](http://www.nocow.cn/index.php/Bellman-Ford%E7%AE%97%E6%B3%95)（[SPFA](http://www.nocow.cn/index.php/SPFA)） - [Floyd-Warshall](http://www.nocow.cn/index.php/Floyd-Warshall%E7%AE%97%E6%B3%95) - [Johnson算法](http://www.nocow.cn/index.php/Johnson%E7%AE%97%E6%B3%95)

**最小生成树：**[Prim](http://www.nocow.cn/index.php/Prim%E7%AE%97%E6%B3%95) - [Kruskal](http://www.nocow.cn/index.php/Kruskal%E7%AE%97%E6%B3%95)

**强连通分支：**[Kosaraju](http://www.nocow.cn/index.php/Kosaraju%E7%AE%97%E6%B3%95) - [Gabow](http://www.nocow.cn/index.php/Gabow%E7%AE%97%E6%B3%95) - [Tarjan](http://www.nocow.cn/index.php/Tarjan%E7%AE%97%E6%B3%95)

**网络流：**[增广路法](http://www.nocow.cn/index.php?title=%E5%A2%9E%E5%B9%BF%E8%B7%AF%E6%B3%95&action=edit&redlink=1)（[Ford-Fulkerson](http://www.nocow.cn/index.php/Ford-Fulkerson%E7%AE%97%E6%B3%95)，[Edmonds-Karp](http://www.nocow.cn/index.php/Edmonds-Karp%E7%AE%97%E6%B3%95)，[Dinic](http://www.nocow.cn/index.php/Dinic%E7%AE%97%E6%B3%95)） - [预流推进](http://www.nocow.cn/index.php/%E9%A2%84%E6%B5%81%E6%8E%A8%E8%BF%9B%E7%AE%97%E6%B3%95) - [Relabel-to-front](http://www.nocow.cn/index.php/Relabel-to-front)

**图匹配 - 二分图匹配：**[匈牙利算法](http://www.nocow.cn/index.php/%E5%8C%88%E7%89%99%E5%88%A9%E7%AE%97%E6%B3%95) - [Kuhn-Munkres](http://www.nocow.cn/index.php/Kuhn-Munkres%E7%AE%97%E6%B3%95) - [Edmonds' Blossom-Contraction](http://www.nocow.cn/index.php/Edmonds%27_Blossom-Contraction)

## 上我的弱弱的代码

```cpp
#include<iostream>
#define maxn 1000
using namespace std;
int map[maxn][maxn];
bool vis[maxn]={0};
int dis[maxn];//存每个点对应的最短路长度
int que[maxn];
int cnt[maxn];
int loop=0;
bool judge=1;//开启判负环
int n,e,s,x,y,z,head,tail;
void SPFA(int u){//u为源点
	int x;
	//初始化过程
	head=0;
	tail=1;
	dis[u]=0;//从u到u距离为0
	que[1]=u;//将源点u入队
	vis[u]=1;//标明此时u在队列中，但是u可以再次进入队列
	cnt[u]++;
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
	if(judge==1 && loop==1){
		cout<<"无法求最短路，存在负环"<<endl;
		return 0;
	}
	for(int i=1;i<=n;i++){
		cout<<"第"<<i<<"个点的最短路："<<dis[i]<<endl;
	}
	return 0;
}

//判断负环 

/*

node n;
if visited_time[n] > n then
node n -> 存在负环

*/


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

:数据2 (存在负环)
3
3
1 2 1
2 3 3
3 1 -8
1

*/
```

## 补充

另外一位大神的**SPFA解释**：

SPFA(Shortest Path Faster Algorithm)是Bellman-Ford[算法](http://lib.csdn.net/base/datastructure)的一种队列实现，减少了不必要的冗余计算。

SPFA，要从Bellman-ford的优化说起。在n个点m条边的图中，Bellman-ford的复杂度是n*m，依次对每条边进行松弛操作，重复这个操作n-1次后则一定得到最短路，如果还能继续松弛，则有负环。这是因为最长的没有环路的路，也只不过是n个点n-1条边构成的，所以松弛n-1次一定能得到最短路。

SPFA的意义在于，**如果一个点上次没有被松弛过，那么下次就不会从这个点开始松弛。每次把被松弛过的点加入到队列中，就可以忽略掉没有被松弛过的点。**
但是最外层的循环还是n-1次。如果把被松弛的点放到前边，他相当于没有进行完这一轮松弛，就开始了一些其他的操作。但是这些其他的操作可能是无用的，因为这些操作的起始点可能还会被这一轮松弛更新。

所以传统的SPFA的复杂度不会超过n*m，并且每个点都不会第n次入队。但是SLF优化...其实就不是个优化..它丢掉了一轮一轮松弛的这个特性..导致复杂度可能呈指数级上升。

其实那个SLF优不算是优化？只是在某些图上可能会更好，但是可以通过直接构造图将其由原本spfa最坏的n*m的复杂度卡成2^n复杂度了...

这是有可能的。[HDU 4889](http://acm.hdu.edu.cn/showproblem.php?pid=4889)就是这样的题。题目让你造数据卡SPFA。

SPFA出自民科。一般流传的版本O(k*E)复杂度中有常数就足以证明这一点。原始论文中k并非常数，不能当作常数对待，也就是说什么都没证明。

算法大致流程是用一个队列来进行维护。 初始时将源加入队列。 每次从队列中取出一个元素，并对所有与他相邻的点进行松弛，若某个相邻的点松弛成功，则将其入队。 直到队列为空时算法结束。

这个算法，简单的说就是队列优化的bellman-ford,利用了每个点不会更新次数太多的特点发明的此算法。

SPFA——Shortest Path Faster Algorithm，它可以在O(kE)的时间复杂度内**求出源点到其他所有点的最短路径**，**可以处理负边**。SPFA的实现甚至比Dijkstra或者Bellman_Ford还要简单：

步骤：
设Dist代表S到I点的当前最短距离，Fa代表S到I的当前最短路径中I点之前的一个点的编号。开始时Dist全部为+∞，只有Dist[S]=0，Fa全部为0。

维护一个队列，里面存放所有需要进行迭代的点。初始时队列中只有一个点S。用一个布尔数组记录每个点是否处在队列中。

每次迭代，取出队头的点v，依次枚举从v出发的边v->u，设边的长度为len，判断Dist[v]+len是否小于Dist[u]，若小于则改进Dist[u]，将Fa[u]记为v，并且由于S到u的最短距离变小了，有可能u可以改进其它的点，所以若u不在队列中，就将它放入队尾。这样一直迭代下去直到队列变空，也就是S到所有的最短距离都确定下来，结束算法。若一个点入队次数超过n，则有负权环。

**SPFA 在形式上和宽度优先搜索非常类似，不同的是宽度优先搜索中一个点出了队列就不可能重新进入队列，但是SPFA中一个点可能在出队列之后再次被放入队列，**也就是一个点改进过其它的点之后，过了一段时间可能本身被改进，于是再次用来改进其它的点，这样反复迭代下去。设一个点用来作为迭代点对其它点进行改进的平均次数为k，有办法证明对于通常的情况，k在2左右。

SPFA算法（Shortest Path Faster Algorithm），也是求解单源最短路径问题的一种算法，用来解决：

给定一个加权有向图G和源点s，对于图G中的任意一点v，求从s到v的最短路径。 SPFA算法是Bellman-Ford算法的一种队列实现，减少了不必要的冗余计算，他的基本算法和Bellman-Ford一样，并且用如下的方法改进：

 1、第二步，不是枚举所有节点，而是通过队列来进行优化 设立一个先进先出的队列用来保存待优化的结点，优化时每次取出队首结点u，并且用u点当前的最短路径估计值对离开u点所指向的结点v进行松弛操作，如果v点的最短路径估计值有所调整，且v点不在当前的队列中，就将v点放入队尾。这样不断从队列中取出结点来进行松弛操作，直至队列空为止。

 2、同时除了通过判断队列是否为空来结束循环，还可以通过下面的方法： 判断有无负环：如果某个点进入队列的次数超过V次则存在负环（SPFA无法处理带负环的图）。

**SPFA算法有两个优化算法 SLF 和 LLL：**

**SLF**：Small Label First 策略，设要加入的节点是j，队首元素为i，若dist(j)<dist(i)，则将j插入队首，否则插入队尾。

**LLL：**Large Label Last 策略，设队首元素为i，队列中所有dist值的平均值为x，若dist(i)>x则将i插入到队尾，查找下一元素，直到找到某一i使得dist(i)<=x，则将i出对进行松弛操作。 SLF 可使速度提高 15 ~ 20%；SLF + LLL 可提高约 50%。 在实际的应用中SPFA的算法时间效率不是很稳定，为了避免最坏情况的出现，通常使用效率更加稳定的Dijkstra算法。

来自：http://blog.csdn.net/liangzhaoyang1/article/details/62423135

## 提醒

一！定！要！谨慎保存文件！

误删之后很难过

じあねみなさん