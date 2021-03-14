---
title: 「Floyed & 高精度」NOIP 2002普及组 & 洛谷P1037 产生数

date: 2017-10-13 22:12

tags: [高精度]

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-505767.jpg
---

## 题目描述

给出一个整数 n（n<10^30) 和 k 个变换规则（k<=15）。

规则：

一位数可变换成另一个一位数：

规则的右部不能为零。

例如：n=234。有规则（k＝2）：

2－>5

3－>6

上面的整数 234 经过变换后可能产生出的整数为（包括原数）:

234 534 264 564 共 4 种不同的产生数

问题：

给出一个整数 n 和 k 个规则。

求出：

经过任意次的变换（0次或多次），能产生出多少个不同整数。

仅要求输出个数。

## 输入输出格式

输入格式：

键盘输人，格式为：

n k x1 y1 x2 y2 ... ...

xn yn

输出格式：

屏幕输出，格式为：

一个整数（满足条件的个数）：

## 输入输出样例

输入样例#1：

```
234 2
2 5
3 6

```

输出样例#1：

```
4
```



## 题解报告

拿道题的第一反应是  建图。floyed也是我昨晚看黄书上的代码…

直接用的黄书上的代码 都没改… 位运算版本很高效哦。

两种情况：

>254
>
>2	3
>
>5	6
>
>
>
>2 -> 3
>
>5 -> 6
>
>4
>
>乘法原理 2x2=4种

>2534
>
>2	5
>
>5	3
>
>3	2
>
>
>
>2 -> 5 -> 3
>
>5 -> 3 -> 2
>
>3 -> 2 ->5
>
>4
>
>乘法原理 3x3x3=27种

看到第二组数据：苯萘蒽芘～各自成环～ 

depth指的是一个数下降的深度，floyed只是遍历一遍给的图，这样，每一位都能找到一个链或者环（环的话就是 2->5->3 x 2），但是不能环一圈的哦！统计每个数字depth，最后做乘法原理。注意输入时要以string输入（10^ 30 哦），乘法原理也得拿高精度优化一下，要么会WA。

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <cstdlib>
using namespace std;

int map[50][50],depth[10],num[1001],n,k,a,b;
string s;
//弗洛伊德 - 穿针引线
void floyed(){
	for(int k=0;k<=9;k++)
		for(int i=0;i<=9;i++)
			for(int j=0;j<=9;j++)
				map[i][j] = map[i][j] || (map[i][k] && map[k][j]);	
}

int main(){
	freopen("number.in","r",stdin);
	freopen("number.out","w",stdout);
	cin>>s>>k;
	
	n=(int)s.length();
	
	for(int i=1;i<=k;i++){
		cin>>a>>b;
		map[a][b]=1;
	}
	
	for(int i=0;i<=9;i++) map[i][i]=1;//自己和自己相连
	
	floyed();
	
	for(int i=0;i<=9;i++)
    	for(int j=0;j<=9;j++)
    		if(map[i][j]) depth[i]++; //统计一遍数链纵向深度
  	int len=2;
  	
	num[1]=1;
	//高精度 实测不拿高精60分
  	for(int i=0;i<n;i++){
  		
		for(int j=1;j<=100;j++)
			num[j]*=depth[s[i]-'0']; 
			
		for(int j=1;j<=100;j++)
    			if(num[j]>=10){
			        num[j+1]+=num[j]/10;
			        num[j]%=10;
    			}
    			
  		while(num[len]) len++;  //统计结果长度
  		
  	}
  	
  	for(int i=len-1;i>=1;i--)
	  	cout<<num[i]; 
	  	
 	return 0;
}

```

