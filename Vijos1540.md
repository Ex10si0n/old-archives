---
title: 「带权并查集」Vijos-1540 月亮之眼

date: 2017-08-22 22:22

tags: [并查集]

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-147755.jpg
---

## 描述

吉儿是一家古董店的老板娘，由于她经营有道，小店开得红红火火。昨天，吉儿无意之中得到了散落民间几百年的珍宝—月亮之眼。吉儿深知“月亮之眼”价值连城：它是由许多珍珠相连而成的，工匠们用金线连接珍珠，每根金线连接两个珍珠；同时又对每根金线染上两种颜色，一半染成银白色，一半染成黛黑色。由于吉儿自小熟读古籍，所以还晓得“月亮之眼”的神秘传说：“月亮之眼”原是一个古代寺庙的宝物，原本是挂在佛堂的一根顶梁柱上的，整个宝物垂直悬挂，所有珍珠排成一线，且都镶嵌在柱子里，而每一根金线又都是绷紧的，并且金线的银白色一端始终在黛黑色一端的上方；然而，在一个月圆之夜，“月亮之眼”突然从柱里飞出，掉落下来，宝物本身完好无损，只是僧侣们再也无法以原样把“月亮之眼”嵌入柱子中了。吉儿望着这个神秘的宝物，回忆着童年读到的传说，顿时萌发出恢复“月亮之眼”的冲动，但是摆弄了几天依旧没有成功。

现在，要麻烦您来帮助吉儿完成这项使命。

您要设计一个程序，对于给定的“月亮之眼”进行周密分析，然后给出这串宝物几百年前嵌在佛堂顶梁柱上的排列模样。给定的“月亮之眼”有N个珍珠和P根金线，所有珍珠按一定顺序有了一个序号：1、2…、N。

## 输入格式

输入数据包含一个“月亮之眼”的特征描述：
文件第一行有两个整数N和P，其中N表示宝物中的珍珠个数，P表示宝物中的金线根数；
以下P行描述珍珠连接情况：
文件第I+1行有三个整数，Ri1,Ri2,Li。其中Ri1表示第I根金线的银白色一端连接的珍珠序号；Ri2表示第I根金线的黛黑色一端连接的珍珠序号；Li表示第I根金线的长度。

## 输出格式

由于珍珠尺寸很小，所以几个珍珠可以同时镶嵌在一个位置上。

您的输出数据描述的是“月亮之眼”各个珍珠在顶梁柱上的位置，输出文件共N行：
第I行，一个整数S，它表示标号为I的珍珠在顶梁柱上距离最高位置珍珠的距离。

注意：若无解则输出仅一行，包含一个整数“-1”。

### Sample Input

```
9 9
1 2 3
2 3 5
2 7 1
4 5 4
5 6 1
5 9 1
6 7 1
7 8 3
9 8 4
```

### Sample Output

```
2
5
10
0
4
5
6
9
5
```

###限制

1s

###提示

N,P<=500

###来源

Balkan OI 1998

## 题解

简单的带权并查集，主要理解`find()`函数的功能，如果用数组模拟这道题可能会TLE掉，应该说一定会，毕竟一道难度5的题诶。

```cpp
#include <iostream>
using namespace std;
int N,P;
int dis[1005],pre[1005];//dis -> 到祖先的距离
void init(){
	for(int i=1;i<=N;i++){
		dis[i]=0;
		pre[i]=i;
	}
}
int find(int x){
	if(pre[x]==0) return x;
	int t=find(pre[x]);
	dis[x]+=dis[pre[x]];
	return pre[x]=t;
}

int main(){
	init();
	cin>>N>>P;
	for(int i=1;i<=P;i++){
		int r1,r2,l;
		cin>>r1>>r2>>l;
		int aa=find(r1);
		int bb=find(r2);
		if(aa!=bb)    //b----->a
		{
			if(dis[r1]-dis[r2]+l>=0)
			{
				pre[bb]=aa;
				dis[bb]=dis[r1]-dis[r2]+l;
			}
			if(dis[r1]-dis[r2]+l<0)
			{
				pre[aa]=bb;  
                dis[aa]=-dis[r1]+dis[r2]-l;//dis[r1]-dis[r2]+l 的相反数
			}
			}else
			{
				if(dis[r2]!=dis[r1]+l)
				{
					cout<<-1<<endl;
					return 0;
				}
			}
	
	}
	for(int i=1;i<=N;i++){
		find(i);
		cout<<dis[i]<<endl;
	}
	return 0;
}
```

