---
title: 数据结构之建图方法
date: 2019-09-18 15:19
tags:
	- 图论
	- Graph
categories:
	- Data Structure
cover: https://w.wallhaven.cc/full/d5/wallhaven-d588xm.jpg
---
# 关于图

## 简介

图有多种变体，包括简单图、多重图、有向图、无向图等。**James Joseph Sylvester**在1878年首次提出“图”这一名词。其中树是一种特殊的图，其边总等于结点数少1。图也有许多类型，不同类型存在不同局限，例如：有向图的边是有方向的，比如从结点1到结点2存在有向边，其为结点1指向结点2的单向边，且结点2到结点1没有连接，那么你就无法直接从结点2到达结点1，除非走更多的其他可行的边。图最重要的两个参数，即边 (Edge) 和结点 (Node) ，其中边或者结点都有可能有更多定义。

![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/6n-graf.svg/1920px-6n-graf.svg.png)

## 拓扑学上的意义*

In [topology](https://en.wikipedia.org/wiki/Topology), a subject in [mathematics](https://en.wikipedia.org/wiki/Mathematics), a **graph** is a topological space which arises from a usual [graph](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics))![{\displaystyle G=(E,V)}](https://wikimedia.org/api/rest_v1/media/math/render/svg/d7c252f40ee7bdd95534eb8482c51bb0097dee9a) by replacing vertices by points and each edge ![{\displaystyle e=xy\in E}](https://wikimedia.org/api/rest_v1/media/math/render/svg/493e6a1737b0c28c5f5f8e23309bac96c339a595) by a copy of the unit interval ![[0,1]](https://wikimedia.org/api/rest_v1/media/math/render/svg/738f7d23bb2d9642bab520020873cccbef49768d) where ![{\displaystyle 0}](https://wikimedia.org/api/rest_v1/media/math/render/svg/2aae8864a3c1fec9585261791a809ddec1489950) is identified with the point associated to ![x](https://wikimedia.org/api/rest_v1/media/math/render/svg/87f9e315fd7e2ba406057a97300593c4802b53e4) and ![1](https://wikimedia.org/api/rest_v1/media/math/render/svg/92d98b82a3778f043108d4e20960a9193df57cbf) with the point associated to ![y](https://wikimedia.org/api/rest_v1/media/math/render/svg/b8a6208ec717213d4317e666f1ae872e00620a0d). That is, as topological spaces, graphs are exactly the [simplicial 1-complexes](https://en.wikipedia.org/wiki/Simplicial_complex) and also exactly the one-dimensional [CW complexes](https://en.wikipedia.org/wiki/CW_complex).

Thus, in particular, it bears the quotient topology of the set

![{\displaystyle X_{0}\sqcup \bigsqcup _{e\in E}I_{e}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0623fb44ed9cd31c10ca069661005723b5c29f79) ([disjoint union](https://en.wikipedia.org/wiki/Disjoint_union))

*转载自维基百科

## 图的参数

- **阶**（Order）：图G中顶集V的大小称作图G的阶。
- **子图**（Sub-Graph）：图G'称作图G的子图如果以及![E(G')\subseteq E(G)](https://wikimedia.org/api/rest_v1/media/math/render/svg/b39363db01c9e8c25725744bd91f6b729a2f7ab7)。
- **生成子图**（Spanning Sub-Graph）：指满足条件 V(G') = V(G) 的G的子图G'。
- **度**（Degree）：一个顶点的度是指与该顶点相关联的总边数，顶点v![v](https://wikimedia.org/api/rest_v1/media/math/render/svg/e07b00e7fc0847fbd16391c778d65bc25c452597)的度记作。度和边有如下关系：![\sum_{v\in V} d(v)=2\left|E\right|](https://wikimedia.org/api/rest_v1/media/math/render/svg/3135ba94ea6fdaca02cf88a156b9f2bdaae6d546)。
- **出度**（Out-degree）和**入度**（In-degree）：对有向图而言，顶点的度还可分为出度和入度。一个顶点的出度为d0，是指有d0条边以该顶点为起点，或说与该点关联的出边共有d0条。入度的概念也类似。
- **邻接矩阵**（Adjacency matrix）
- **自环**（Loop）：若一条边的两个顶点相同，则此边称作自环。
- **路径**（Path）：从顶点u到顶点v的一条路径是指一个序列![v_0,e_1,v_1,e_2,v_2,...e_k,v_k](https://wikimedia.org/api/rest_v1/media/math/render/svg/2fc75ab7becb8860fd3229d274ab29a278bb54ee)，ei的起点终点为vi-1及vi；k称作路径的长度；v0=u，称为路径的起点；vk=u，称为路径的终点。如果u=v，称该路径是**闭**的，反之则称为**开**的；如果v1,…,vk两两不等，则称之为**简单路径**（Simple path，注意，u=v是允许的）。
- **行迹**（Trace）：如果路径P(u,v)中边各不相同，则该路径称为u到v的一条行迹。
- **轨道**（Track）：即简单路径。
- 闭的行迹称作**回路**（Circuit），闭的轨道称作**圈**（Cycle）。（现存文献中的命名法并无统一标准。比如在另一种定义中，walk对应上述的path，path对应上述的track，trail对应上述的trace。）
- **距离**（Distance）： 从顶点![u](https://wikimedia.org/api/rest_v1/media/math/render/svg/c3e6bb763d22c20916ed4f0bb6bd49d7470cffd8)出发到顶点![v](https://wikimedia.org/api/rest_v1/media/math/render/svg/e07b00e7fc0847fbd16391c778d65bc25c452597)的最短路径若存在，则此路径的长度称作从![u](https://wikimedia.org/api/rest_v1/media/math/render/svg/c3e6bb763d22c20916ed4f0bb6bd49d7470cffd8)到![v](https://wikimedia.org/api/rest_v1/media/math/render/svg/e07b00e7fc0847fbd16391c778d65bc25c452597)的距离。若从u到v根本不存在路径，则记该距离为无穷。
- **距离矩阵**（Distance matrix）
- **桥**（Bridge）：若去掉一条边，便会使得整个图不连通，该边称为桥。

# 建图

我在此推荐**利用到STL的vector<>建图** 和 **前向星**两种方式。

这篇文章介绍vector<>建图。

## 基础实现

vector<>是向量数组，可以在C++中如此定义：

vector中存放的是一个结构体，结构体包含 **目标边to** 和 **边权值val** ，通过输入图的边，按照边数循环进行输入每一条边的参数。存储原理：

> from为一个结构体，结构体内容表示这个from结点的参数to和val
>
> G[]数组包含唯一from值，但是from值对应的参数不一定唯一，也就是说一个结点可以连接许多的结点
>
> 换一种说法就是，G[from]是一个结构体



```cpp
#include <iostream>
#include <vector>
#define N 105
using namespace std;
struct Edge{ int val, to;}edge;
vector<Edge>G[N];

void add_single_edge(int from,int to,int value){
	Edge e;
	e.to=to, e.val=value;
	G[from].push_back(e);
}

void add_double_edge(int from,int to,int value){
	Edge e1,e2;
	e1.to=to, e1.val=value;
	e2.to=from, e2.val=value;
	G[from].push_back(e1);
	G[from].push_back(e2);
}

int main(){
	int node, edge, a, b, c;
	cin>>node>>edge;
	//输入图
	for(int i=1;i<=edge;i++){
		cin>>a>>b>>c;
		addedge(a, b, c);
	}
	//输出图测试
	for(int i=1;i<=node;i++){
		for(int j=0;j<G[i].size();j++){
			cout<<i<<" - "<<G[i][j].to<<"     val = "<<G[i][j].val<<endl;
		}
	}
	return 0;
}

```

