---
title: 八皇后算法在C++和Java中的实现
date: 2019-09-11 15:19
tags: 
	- 深度优先搜索 
	- DFS
categories:
	- Algorithms
cover: https://w.wallhaven.cc/full/qd/wallhaven-qdl8e7.jpg
---
# 八皇后问题简述

**八皇后问题**是一个以国际象棋为背景的问题：如何能够在8×8的国际象棋棋盘上放置八个[皇后](https://zh.wikipedia.org/wiki/后_(国际象棋))，使得任何一个皇后都无法直接吃掉其他的皇后？为了达到此目的，任两个皇后都不能处于同一条横行、纵行或斜线上。八皇后问题可以推广为更一般的**n皇后摆放问题**：这时棋盘的大小变为*n*×*n*，而皇后个数也变成*n*。当且仅当 *n* = 1或 *n* ≥ 4 时问题有解。更多可见 [维基百科](https://zh.wikipedia.org/wiki/八皇后问题) 。

## 算法实现

### 深度优先搜索

该问题使用**深度优先搜索**算法 （Depth-First-Search，以后简称DFS） 可以轻而易举的解决，DFS 是一种用于遍历或搜索[树](https://zh.wikipedia.org/wiki/树_(数据结构))或[图](https://zh.wikipedia.org/wiki/图_(数学))的[算法](https://zh.wikipedia.org/wiki/算法)。沿着树的深度遍历树的节点，尽可能深的搜索树的分支。当节点v的所在边都己被探寻过，搜索将回溯到发现节点v的那条边的起始节点。这一过程一直进行到已发现从源节点可达的所有节点为止。如果还存在未被发现的节点，则选择其中一个作为源节点并重复以上过程，整个进程反复进行直到所有节点都被访问为止。深度优先搜索是图论中的经典算法，利用深度优先搜索算法可以产生目标图的相应[拓扑](https://zh.wikipedia.org/wiki/拓扑)排序表，利用拓扑排序表可以方便的解决很多相关的[图论](https://zh.wikipedia.org/wiki/图论)问题，如最大路径问题等等。

我们以下面的这棵树为例子来实现DFS，从根节点1开始，沿着根左右顺序进行前序遍历，即

> 1 - 2 - 3 - 4 - 5 - 6 - 7 - 8 - 9 - 10 - 11 - 12

![](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/600px-Depth-first-tree.svg.png)

实现方法需要用到函数的递归，即回溯。也就是说要注意函数里面关键语句的位置，即什么时候标记，什么时候回溯。我们用C++举例子：

```cpp
void dfs(int now){
	// ...
		for(int j=1;j<=N;j++){
			if((!d[j]) && (!l[now+j]) && (!r[now-j+N])){
				pos[now]=j; d[j]=1; l[now+j]=1; r[now-j+N]=1;   //标记
				dfs(now+1);                                     //递归
				d[j]=0; l[now+j]=0; r[now-j+N]=0;               //回溯
			}
	// ...
}
```

如果顺序错误，甚至会导致函数死循环。回溯的要义在于：

### 皇后置放方法

我们可以用 pos[] 数组来存放每一列*（纵向下降的表格）的皇后位置，d[] 数组来标志每一个皇后下方不能存在皇后，l[] r[] 数组用来表示每个皇后的左下右下所有的格不能存在皇后，在每次递归到更深层时，l[] r[]数组都会进行一定的数学运算来计算该位置是否能放皇后。此过程用坐标系理解非常方便，具体见C++代码。

## 代码示例

### C++实现：

```cpp
/*
From LG P1219
问题：国际象棋中有一个棋子——皇后，她可以吃掉她所在的行、列、左右斜八个方向的棋子。
现在请你思考一个问题，在N*N的棋盘里，应该怎样安排才能放下N个皇后，
并且保证她们不能吃掉彼此。(4)
*/

#include <iostream>
#include <cstdio>
#include <cstdlib>

using namespace std;

int N,cnt,pos[100],d[100],r[100],l[100];

void print(){
	if(cnt>3) return ;
	for(int i=1;i<=N;i++) cout<<pos[i]<<" ";
	cout<<endl;
}

void dfs(int now){
	if(now>N){
		cnt++;
		print();
		return ;
	}else{
		for(int j=1;j<=N;j++){
			if((!d[j]) && (!l[now+j]) && (!r[now-j+N])){
				pos[now]=j; d[j]=1; l[now+j]=1; r[now-j+N]=1;
				dfs(now+1);
				d[j]=0; l[now+j]=0; r[now-j+N]=0;
			}
		}
	}
}

int main(){
	cin>>N;
	dfs(1);
	cout<<cnt<<endl;
	return 0;
}

/*
Tags: DFS
记录 l r 数组是难点，需要考虑坐标系。

   1 2 3 4 (x)
 1 . x . .
 2 l d r . 
 3 . d . r
 4 . d . .
(y)

x ➜ d,l,r;
d.all=x;
r.all=x-y;
l.all=x+y;

. . . x . .
x . . . . .
. . . . x .
. x . . . .
. . . . . x
. . x . . .

*/
```

### Java实现：

```java
import java.util.Scanner;
public class eightQueensProblem {
	
	static Scanner input = new Scanner(System.in);
	
	static int TableScale, AnswerNumberCounter = 0;
	static int[] Position = new int[105];
	static boolean[] DownSideQueen = new boolean[105], RightDownSideQueen = new boolean[105], LeftDownSideQueen = new boolean[105];
	
	public static void DepthFirstSearch(int now) {
		if(now > TableScale) {
			AnswerNumberCounter ++;
			for(int i = 1; i <= TableScale; i++) 
				System.out.print(Position[i] + " ");
			System.out.print("\n");
		}else {
			for(int j = 1; j <= TableScale; j++) {
				if( (DownSideQueen[j] == false) && (LeftDownSideQueen[now+j] == false) && (RightDownSideQueen[now-j+TableScale] == false) ) {
					Position[now] = j; DownSideQueen[j] = true; LeftDownSideQueen[now+j] = true; RightDownSideQueen[now-j+TableScale] = true;
					DepthFirstSearch(now+1);
					DownSideQueen[j] = false; LeftDownSideQueen[now+j] = false; RightDownSideQueen[now-j+TableScale] = false;
				}
			}
		}
	}
	
	public static void main(String[] args) {
		
		TableScale = input.nextInt();
		DepthFirstSearch(1);
		System.out.printf("Solution counter: %d", AnswerNumberCounter);

	}

}
```

# [补充]++位运算版本

```cpp
int upperlim=(1<<n)-1;  //初始化upperlim为111...111
void work(int row,int ld,int rd){
	int pos,p;
	if(row!=upperlim){
		pos=upperlim & ~(row|ld|rd);  
//row|ld|rd 表示下一个排列没有被标1，然后把这个值取反，就是能够选的标1，然后和upperlim与运算，得到的结果就是下一次标的位置
		while(pos!=0){  //pos此时的状态还有没标满的，进入循环
			p=pos & -pos;   //从最右面的1开始标
			pos=pos-p;  //标记pos
			work(row|p,(ld|p)<<1,(rd|p)>>1);  //dfs下一次排列
		}
	}
	else sum++;
}
```



