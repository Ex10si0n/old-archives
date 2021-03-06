---
title: 「算法」康托展开

date: 2017-07-30 17:24

tags: [哈希算法]

categories: C++

thumbnail: http://i4.bvimg.com/1949/c6ef215167602eect.jpg
---

## 在此之前

我对此的描述：

>生成第n个全排列的X(Sn)函数值的一种运算方式

换句话说，就是保证每一种全排列都有自己固定的值。

以下是GY大神的解释：

![](http://chuantu.biz/t5/154/1501336733x1881632359.png)

好的，那我就这样理解吧！

## 康托展开

对于**康托展开**的表达式

>  X(Sn)=an\*(n-1)!+an-1\*(n-2)!+…+ai\*(i-1)!+…+a2\*1!+a1*0!

看起来有可能会感觉头疼，但是我们只需要记住这种计算方法即可。

那么我们开始说明：

对于3个数字的全排列，共有一下所有排列方式：

```python
1 2 3   //0
1 3 2   //1
2 1 3	//2
2 3 1	//3
3 1 2	//4
3 2 1	//5
//表1-1
```

共有6种排列方式，我们按字典序（就是一种正常人全排列时的顺序）写出这6种全排列方式。对于表达式中的 `X(Sn)` 这个Sn就是一个全排列序列，比如`Sn={2 3 1}` ,我要说明的是，这个X(Sn)函数的变量是一个**全排列序列**，那么通过康托展开表达式，我们能得到什么呢？答案是它以字典序排列的序号，换句话说就是通过计算得到`X({2,3,1})=3`，对照上表1-1中的右边的注释，很明显发现这么神奇诶2333😱！

怎么计算的呢？以下我们来演示一组：

```python
//构造框架，对于我的例子，个数为n=3的全排列。
X(Sn)=a3 * 2! + a2 * 1! + a1 + 0! 
an为在剩下的数中第n的元素
//Example 1 : 2 3 1 //2是第1元素 3是第2元素 1是第0元素
2 3 1 : 1 * 2! + a2 * 1! + a1 + 0!   //因为2是(2 3 1)第1元素，故a3=1
3 1 : 1 * 2! + 1 * 1! + a1 + 0!   //因为3是剩下中(3 1)第1元素(1为第0元素)，故a2=1
1 : 1 * 2! + 1 * 1! + 0 + 0!   //因为1是剩下中(1)第0元素，故a3=0
//最后求得X(Sn)=3 与表1-1对应
```

再看看另外一篇博客中的讲解

```cpp
关键问题是 a4、a3、a2 和 a1 等于啥？
a4 = "D" 这个元素在子数组 ["D", "B", "A", "C"] 中是第几大的元素。"A"是第0大的元素，"B"是第1大的元素，"C" 是第2大的元素，"D"是第3大的元素，所以 a4 = 3。
a3 = "B" 这个元素在子数组 ["B", "A", "C"] 中是第几大的元素。"A"是第0大的元素，"B"是第1大的元素，"C" 是第2大的元素，所以 a3 = 1。
a2 = "A" 这个元素在子数组 ["A", "C"] 中是第几大的元素。"A"是第0大的元素，"C"是第1大的元素，所以 a2 = 0。
a1 = "C" 这个元素在子数组 ["C"] 中是第几大的元素。"C" 是第0大的元素，所以 a1 = 0。（因为子数组只有1个元素，所以a1总是为0）
所以，X(s1) = 3*3! + 1*2! + 0*1! + 0*0! = 20
```

给出另外一种理解方式，这里称第几大元素就是A>B>C>D这个意思，千万不要与我讲的第n元素弄混。

给出代码：

```cpp
//康托展开
long long Cantor_Expansion(char str[])  
{  
    int len = strlen(str);  
    long long ans = 0;  
    for(int i=0; i<len; i++)  
    {  
        int tmp = 0;  
        for(int j=i+1; j<len; j++)  
            if(str[j] < str[i]) tmp++;  
        ans += tmp * fac[len-i-1];  //fac[]为阶乘  
    }  
    return ans;  //返回该字符串是全排列中第几大，从1开始  
}  
```

## 康托展开逆运算

顾名思义，就是根据某个排列的在总的排列中的名次来确定这个排列。比如：

求1234所有排列中排第20的是啥，那么就利用辗转相除法确定康托展开中的系数，然后每次输出当前未出现过的第`a(n)`个元素。

代码实现康托展开逆运算：

```cpp
//康托展开逆运算  
void Cantor(long long n,long long m)  
{  
    n--;  
    vector<int> v;  
    vector<int> a;  
    for(int i=1;i<=m;i++)  
        v.push_back(i);  
    for(int i=m;i>=1;i--)  
    {  
        long long r = n % f[i-1];  
        long long t = n / f[i-1];  //取模 辗转相除
        n = r;  
        sort(v.begin(),v.end());  
        a.push_back(v[t]);  
        v.erase(v.begin()+t);  
    }  
    vector<int>::iterator it;  //迭代器-小机器人
    for(it = a.begin();it != a.end();it++)  
        cout<<*it;  
    cout<<endl;  
}  
```
