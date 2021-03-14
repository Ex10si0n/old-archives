---
title: 「构造」USACO 1.5-回文质数

date: 2017-07-16 17:46

tags: [构造]

categories: C++
thumbnail: http://i4.bvimg.com/1949/4ebd6d36233824f6t.jpg
---

# 「构造」USACO 1.5-回文质数(pprime)

访问原题：[USACO 1.5 pprime](http://www.nocow.cn/index.php/Translate:USACO/pprime)

## DESCRIPTION

因为151既是一个质数又是一个回文数(从左到右和从右到左是看一样的)，所以 151 是回文质数。

写一个程序来找出范围 [a,b] (5 <= a < b <= 100,000,000) 间的所有回文质数;

## FORM

**PROGRAM NAME**: pprime

**INPUT FORMAT**:

(file pprime.in)

第 1 行: 二个整数 a 和 b .

**OUTPUT FORMAT**:

(file pprime.out)

输出一个回文质数的列表，一行一个。

## SAMPLE INPUT

```
5 500 

```

## SAMPLE OUTPUT

```
5
7
11
101
131
151
181
191
313
353
373
383
```

# 题目分析

如果你打开了本篇文章给的链接，当你在USACO读完题的时候，你会发现有一个 **HINT**，我的题解也差不多根据此说明的。废话不多说，先看这道题要求：

>求5～10^8之间的回文质数

看似10^8这个数很大，如果直接打一亿次循环里开 **isprime** 再开 **判断回文** 不TLE算我输，可是这道题偏偏要求了要求回文质数，怎么办呢？其实你有可能会想用各种优化来降低时间成本，或许不用那么麻烦，我们来思考，为什么限定的是回文数呢？或者说是，我是不是可以利用回文数的性质解题呢？貌似有些同学开始上网查回文数的性质去了，找了半天没看到什么有用的，拜托你回来，我们写一个回文数试试：`30611211603` 这是一个11位回文数，除了第六位均出现两次(或2的倍数次)我们可以叫它 "奇回文" ,对于 `85711758` 来说，所有位都出现了两次(或2的倍数次)我们叫它 “偶回文” ，那么我们会发现，偶回文数一定是11的倍数，不信自己算一下。所以说呢：

> 偶回文数一定不是质数

嗷嗷嗷好精彩，然后我们继续看奇回文，我们可以将3位数奇回文写成 `101*a + 10*b` 的形式，同理我们可以将5位回文写成 `10001*a + 1010*b + 100*c` 的形式，之后的就不写了，我们构造了任意奇回文数，剩下的事情就是判素，而对于回文数我们只需要判断a,b,c,d......位的值就可以了，判素朴素也不会超时，最后注意特判和首位a为1～9的奇数。

精髓就是：***巧用构造解大数***

# 代码

```cpp
#include<iostream>
#include<cmath>
using namespace std;
int x,a,b;
int cnt=3;
int pprime[1000];
bool isprime(int k){
	for(int i=3;i<=sqrt(k+0.5);i++){
		if(k%i==0) return 0;
	}
	return 1;
}
int main(){
//个位，十位数特判
	pprime[1]=5;
	pprime[2]=7;
	pprime[3]=11;
//三位数判断
	for(int i=1;i<=9;i+=2)
		for(int j=0;j<=9;j++){
			x=i*101+j*10;
			if(isprime(x)) pprime[++cnt]=x;
		}
//五位数判断
	for(int i=1;i<=9;i+=2)
		for(int j=0;j<=9;j++)
			for(int k=0;k<=9;k++){
				x=i*10001+j*1010+k*100;
				if(isprime(x)) pprime[++cnt]=x;
			}
//七位数判断
	for(int i=1;i<=9;i+=2)
		for(int j=0;j<=9;j++)
			for(int k=0;k<=9;k++)
				for(int l=0;l<=9;l++){
					x=i*1000001+j*100010+k*10100+l*1000;
					if(isprime(x)) pprime[++cnt]=x;
				}
	cin>>a>>b;
	for(int j=1;j<=cnt;j++){
		if(pprime[j]>=a && pprime[j]<=b) cout<<pprime[j]<<endl;
	}
	return 0;
}
```

