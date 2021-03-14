---
title: 「图论」数据结构常用存图方式

date: 2017-09-28 21:15

tags: 图论

categories: C++

thumbnail: https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-194049.jpg

---



## 数据结构常用的存图方式

在我和YHS大佬讨论半天存图方式之后，我更加坚信用我的习惯 `vector<int>G[maxn]` 的方式存图。（%%% YHS 大佬的 list 表存图）

### 结构

![vector 存图](http://chuantu.biz/t6/70/1506604487x3659076724.png)

我们观察这张不完全的图，运用到vector的不定场性质，可以大大缩减了数据规模，但是要注意G中要用到edge(自定)类型。

代码如下：

```cpp
#include <iostream>
#include <cstdio>
#include <vector>
#define maxn 100005
using namespace std;
struct edge{
	int to;
	int value;
};
vector<edge>G[maxn];
edge temp;
void addedge(int from,int to,int value){
	G[from].push_back(temp);
}
int e;//边数 
void for_each(){
	for(int i=1;i<=e;i++){
		for(int j=0;j<G[i].size();j++){
			edge tmp=G[i][j];
			printf("From %d to %d, the cost is %d\n",i,tmp.to,tmp.value);  
		}
	}
} 
int main(){
	cin>>e;
	for(int i=1;i<=e;i++){	
		int from;
		cin>>from>>temp.to>>temp.value;
		addedge(from,temp.to,temp.value);
	}
	for_each();
	return 0;
}
```

大家可以自己怼一怼vector表，半个小时之内撸完的博客。大家晚安！