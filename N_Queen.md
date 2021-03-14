---
title: 「位运算」N皇后问题

date: 2017-08-3 17:24

tags: [位运算]

categories: C++

thumbnail: http://i2.bvimg.com/1949/a2778975e8059a75t.jpg
---

位运算可以很大程度上提高运算效率，而且还会有神奇的效果，下面我们用位运算来解决**n皇后问题**：

我们先声明一些变量

```cpp
int n;	//不解释了 —— n皇后中的 n
int upperlim=(1<<n)-1;	//终止状态
int row,ld,rd;
```

在这里强调一下：`upperlim=(1<<n)-1`对于n=4时，它的二进制值为`1111`（n个1），现在我们把状态列出来：

![img](http://i4.eiimg.com/1949/5c52836500a51b7c.png)

定义：

```
+ + + >第一行
+ + + >第二行
+ + + >第三行
^ ^ ^
第第第
一二三
列列列
```



n=4时就是这种情况，而`row`的意义就是，我们检查每一行，且每一行只能放一个皇后（不解释了），那么如果第一行我们放在这：

![](http://i1.ciimg.com/1949/7d3886859b7f49f1.png)

那么`row(BIN)=0100`，如果我们再放一个：

![](http://i1.ciimg.com/1949/c5829de731e02f34.png)

很显然，`row(BIN)=0101`，那么很容易看出，row是一个俯视图，我们不关心放置的是第几行，而且如果我们能填满`row`那么我们就结束了这次dfs，对吧。

那么接下来我来解释`ld,rd`是啥，可以叫他们`左斜斜，右斜斜`，一会儿看代码的时候就能知道他们的用处了，可以把他们理解为动态的2个描述哪里不能放的变量，因为在每一层这三个量都是不一样的！

`row,ld,rd`在上图表示：

我们现在在考虑第二行，此时`row=0101`表示第2，4列已经填上皇后了。`ld=1000`，`rd=0010`，能理解吗？其实因为第二行中的😂还没有称为上一行，所以我们左斜斜，右斜斜还不带他玩儿呢：（不扯了，看下图好好解释一番）

![img](http://i2.tiimg.com/1949/b43cbd6ec395f1d6.png)

我们看有左斜杠的`(A,2)`位置，对应在`ld=1000`中最左边，这回明白了吧，所以再看`rd=0010`就能清楚为什么右斜斜`(C,2)`是0010中的1了吧。

这就是位运算的好处，既可以用二进制数描述一个状态，又能用这个二进制数代表的十进制数描述这个二进制数出现的编号，这样可以达到一个判重的效果。

那么第三行的`ld=0010 rd=0001`不解释了。请注意观察这几个数：

```cpp
//Line 2
ld=1000  rd=0010  row=0001
//Line 3
ld=0010  rd=0001  row=xxxx
```

可以看出来，row是在向外扩散出`ld,rd`的对吧，实现这一步骤就是以下代码

```cpp
work(row+p,(ld+p)<<1,(rd+p)>>1);
```

用到一些二进制特性：

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

这段代码确实很晦涩难懂，但是熟练位运算之后也可以熟练写出这种代码，n皇后解决！
