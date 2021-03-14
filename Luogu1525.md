---
title: 「二分 & 二分图染色」NOIP 2010提高组 & 洛谷P1525 关押罪犯

date: 2017-10-14 21:12

tags: [二分]

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-153989.jpg
---

## 题目描述

S 城现有两座监狱，一共关押着N 名罪犯，编号分别为1~N。他们之间的关系自然也极不和谐。很多罪犯之间甚至积怨已久，如果客观条件具备则随时可能爆发冲突。我们用“怨气值”（一个正整数值）来表示某两名罪犯之间的仇恨程度，怨气值越大，则这两名罪犯之间的积怨越多。如果两名怨气值为c 的罪犯被关押在同一监狱，他们俩之间会发生摩擦，并造成影响力为c 的冲突事件。

每年年末，警察局会将本年内监狱中的所有冲突事件按影响力从大到小排成一个列表，然后上报到S 城Z 市长那里。公务繁忙的Z 市长只会去看列表中的第一个事件的影响力，如果影响很坏，他就会考虑撤换警察局长。

在详细考察了N 名罪犯间的矛盾关系后，警察局长觉得压力巨大。他准备将罪犯们在两座监狱内重新分配，以求产生的冲突事件影响力都较小，从而保住自己的乌纱帽。假设只要处于同一监狱内的某两个罪犯间有仇恨，那么他们一定会在每年的某个时候发生摩擦。

那么，应如何分配罪犯，才能使Z 市长看到的那个冲突事件的影响力最小？这个最小值是多少？

## 输入输出格式

输入格式：

输入文件的每行中两个数之间用一个空格隔开。第一行为两个正整数N 和M，分别表示罪犯的数目以及存在仇恨的罪犯对数。接下来的M 行每行为三个正整数aj，bj，cj，表示aj 号和bj 号罪犯之间存在仇恨，其怨气值为cj。数据保证1<aj=<=bj<=N ，0 < cj≤ 1,000,000,000，且每对罪犯组合只出现一次。

输出格式：

共1 行，为Z 市长看到的那个冲突事件的影响力。如果本年内监狱中未发生任何冲突事件，请输出0。

## 输入输出样例

输入样例#1：

```
4 6
1 4 2534
2 3 3512
1 2 28351
1 3 6618
2 4 1805
3 4 12884
```

输出样例#1：

```
3512
```

## 说明

【输入输出样例说明】罪犯之间的怨气值如下面左图所示，右图所示为罪犯的分配方法，市长看到的冲突事件影响力是3512（由2 号和3 号罪犯引发）。其他任何分法都不会比这个分法更优。

![img](https://cdn.luogu.org/upload/pic/298.png)

【数据范围】对于30%的数据有N≤ 15。对于70%的数据有N≤ 2000，M≤ 50000。对于100%的数据有N≤ 20000，M≤ 100000。



## 解题报告

主函数二分，dfs对新生成的图进行染色操作，立`flag`快速跳出递归，主要实现方式看代码：

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>
#include <cstdio>
#include <vector>
#define maxn 100005
using namespace std;
int l,r,mid,n,m,color[maxn];
bool flag;

struct edge{
	int x;int y;int v;
}p[maxn];

vector<int>G[maxn];

void addedge(int f,int t){
	G[f].push_back(t);
	G[t].push_back(f);
}

bool work(int x,int a){  //DFS 进行二分图染色
	if(!flag) return 0;
	if(color[x]){
		if(color[x]==a)
			return 1;
		else{
			flag=0;
			return 0;
		}
	}else{
		color[x]=a;
		for(int i=0;i<G[x].size();i++){
			work(G[x][i],3-a);
			if(!flag) return 0;
		}
	}
}

bool ok(){
	for(int i=1;i<=n;i++){
		if(!color[i])
			work(i,1);
		if(!flag) return 0;
	}
	return 1;
}

bool cmp(edge a,edge b) {return a.v<b.v;}

int main(){
	cin>>n>>m;
	for(int i=1;i<=m;i++) cin>>p[i].x>>p[i].y>>p[i].v;
	sort(p+1,p+m+1,cmp);
	l=1; r=m;
	if(m==1){cout<<0; return 0;}
	while(l<r){
		mid=(l+r)>>1;
		for(int i=mid+1;i<=m;i++){  //把大于mid值的边放到图中
			addedge(p[i].x,p[i].y);
		}
		if(ok()) r=mid;
		else l=mid+1;
	}
	cout<<p[mid].v<<endl;
	return 0;
}

```

以上是我还没有调试完成的code，附上GGN大佬的ACcode：

```cpp
#include<cstdio>
#include<algorithm>
#include<iostream>
#include<cstring>
#include<vector>
using namespace std;

const int maxn=200000+10;

struct Graph {
	vector<int>G[maxn];
	int color[maxn],n;bool flag;
	void init(int N) {
		for(int i=1;i<=N;i++)
			G[i].clear();
		memset(color,0x00,sizeof(int)*(N+1));
		flag=true;n=N;
	}
	void addedge(int f,int t) {
		G[f].push_back(t);
		G[t].push_back(f);
	}
	bool DFS(int x,int a) {
		if(!flag)return false;
		if(color[x]) {
			if(color[x]==a)
				return true;
			else {	
				flag=false;
				return false;
			}
		}else{
			color[x]=a;
			for(int i=0; i<G[x].size(); i++) {
				DFS(G[x][i],3-a);
				if(!flag)return false;
			}
		}
	}
	bool judge() {
		for(int i=1;i<=n;i++) {
			if(!color[i])
				DFS(i,1);
			if(!flag)return false;
		}
		return true;
	}
}G;

const int maxm=100000+10;
struct Edge{int f,t,c;}edges[maxm];
bool cmp(Edge A,Edge B){return A.c<B.c;}

int main() {
	int N,M;
	scanf("%d%d",&N,&M);
	for(int i=1;i<=M;i++)
		scanf("%d%d%d",&edges[i].f,&edges[i].t,&edges[i].c);
	if(M==1) {
		printf("0\n");
		return 0;
	}
	sort(edges+1,edges+M+1,cmp);
	int L=1,R=M;
	while(L<R) {
		int mid=(L+R)/2;
		G.init(N);
		for(int i=mid+1;i<=M;i++)
			G.addedge(edges[i].f,edges[i].t);
		if(G.judge())
			R=mid;
		else L=mid+1;
	}
	printf("%d\n",edges[R].c);
	return 0;
}

```

