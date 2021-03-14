---
title: 哈希简介
date: 2019-09-17 15:19
tags: 
	- 哈希
	- Hash
categories: 
	- Algorithms
cover: https://w.wallhaven.cc/full/39/wallhaven-39r1v9.jpg
---
# ED8E5E1D507C95167720E8CE3BA5216B

标题 `"哈希简介"` 用了MD5算法加密，在下文会详细介绍。理解所谓哈希，可以类比一个F法则对于参数x而言的计算，最后得出一个能表示x的输出。可以把任意长度的输入转化为固定长度的输出（但是你不一定能看得懂输出，就像这个部分的标题一样），但是无论你做多少次将 `"哈希简介"` 经过MD5加密的运算，结果都应该是`ED8E5E1D507C95167720E8CE3BA5216B`，这也就是可以用MD5来校验你下载的游戏是不是完整的原因。

## 设计一个简单的哈希

如果我们有一些参数：`1 1024 65536 2147483648` 每个参数都对应一个值`a[1]=2 a[1024]=9 a[65536]=90 a[2147483648]=900` 如果开一个数组，就像上一个代码行那样，会有大量的内存浪费，所以我们可以对其优化，比如这样：

```cpp
struct node {
  int val,key;
}myhashmap[10];

myhashmap[1].val = 1, myhashmap[1].key = 2;
myhashmap[2].val = 1024, myhashmap[2].key = 9;
myhashmap[3].val = 65536, myhashmap[3].key = 90;
myhashmap[4].val = 2147483648, myhashmap[4].key = 900;
```

需要使用的时候遍历 `hashmap` 找到对应的 `val` 值就可以取出 `key` 值了。显然它并没有太大的应用意义（除了节省内存）。

通过一些简单的数学计算就可以用另外一种思路来对法则F定义，比如：

```cpp
int hash(int val){
  return log(2,val);
}
```

这样可以用大小为32的数组取代掉长度为21亿多的数组，但是如果某个 `val` 值不能被hash函数返回为整数，就可能造成损失。当然现代的哈希算法比以上的例子复杂好多。

# 实例

## 康托展开

**康托展开**用来构建全排列到自然数的完美映射，实质是计算当前排列在所有由小到大全排列中的顺序，是可逆的。例如 `3 5 7 4 1 2 9 6 8 ` 展开为 `98884` ，代表 数字1 到 数字8 的全排列中的第98884位（从第0位计数）

### 公式

这个完美哈希的计算式为：

>  *X(Sn)=an\*(n-1)!+an-1\*(n-2)!+…+ai\*(i-1)!+…+a2\*1!+a1\*0!*

三个数进行全排列，康托展开顺序如下：

```python
1 2 3 //0
1 3 2 //1
2 1 3	//2
2 3 1	//3
3 1 2	//4
3 2 1	//5
//表1-1
```

### 计算过程

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

### 代码实现

```cpp
//康托展开
int fac(int x) {
  if(x == 1) return 1;
  else return x*fac(x-1);
}
long long Cantor_Expansion(char str[]) {  
    int len = strlen(str);  
    long long ans = 0;  
    for(int i=0; i<len; i++)  
    {  
        int tmp = 0;  
        for(int j=i+1; j<len; j++)  
            if(str[j] < str[i]) tmp++;  
        ans += tmp * fac(len-i-1);  //fac[]为阶乘  
    }  
    return ans;  //返回该字符串是全排列中第几大，从1开始  
}  
```

### 逆过程

```cpp
//康托展开逆运算  
void inv(long long n,long long m)  
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
    vector<int>::iterator it;
    for(it = a.begin();it != a.end();it++)  
        cout<<*it;  
    cout<<endl;  
}  
```

## MD5 算法

MD5是输入不定长度信息，输出固定长度128-bits的算法。经过程序流程，生成四个32位数据，最后联合起来成为一个128-bits[散列](https://zh.wikipedia.org/wiki/散列)。基本方式为，求余、取余、调整长度、与链接变量进行循环运算。得出结果。

### 散列生成

一般128位的MD5散列被表示为32位[十六进制](https://zh.wikipedia.org/wiki/十六进制)数字。以下是一个43位长的仅[ASCII](https://zh.wikipedia.org/wiki/ASCII)字母列的MD5散列：

```
MD5("The quick brown fox jumps over the lazy dog")
= 9e107d9d372bb6826bd81d3542a419d6
```

即使在原文中作一个小变化（比如用c取代d）其散列也会发生巨大的变化：

```
MD5("The quick brown fox jumps over the lazy cog")
= 1055d3e698d289f2af8663725127bd4b
```

空文的散列为：

```
MD5("")
= d41d8cd98f00b204e9800998ecf8427e
```

### 算法实现

由于各种库都有可以供Coder使用的MD5算法模型，研究算法本质在此由于个人能力问题就不详细讲解了，可以参考其他博客，粗略来说，MD5以512位分组来处理输入的信息，且每一分组又被划分为16个32位子分组，经过了一系列的处理后，算法的输出由四个32位分组组成，将这四个32位分组级联后将生成一个128位散列值。 

### 代码实现*

```pascal
//Note: All variables are unsigned 32 bits and wrap modulo 2^32 when calculating
var int[64] r, k

//r specifies the per-round shift amounts
r[ 0..15]：= {7, 12, 17, 22,  7, 12, 17, 22,  7, 12, 17, 22,  7, 12, 17, 22} 
r[16..31]：= {5,  9, 14, 20,  5,  9, 14, 20,  5,  9, 14, 20,  5,  9, 14, 20}
r[32..47]：= {4, 11, 16, 23,  4, 11, 16, 23,  4, 11, 16, 23,  4, 11, 16, 23}
r[48..63]：= {6, 10, 15, 21,  6, 10, 15, 21,  6, 10, 15, 21,  6, 10, 15, 21}

//Use binary integer part of the sines of integers as constants:
for i from 0 to 63
    k[i] := floor(abs(sin(i + 1)) × 2^32)

//Initialize variables:
var int h0 := 0x67452301
var int h1 := 0xEFCDAB89
var int h2 := 0x98BADCFE
var int h3 := 0x10325476

//Pre-processing:
append "1" bit to message
append "0" bits until message length in bits ≡ 448 (mod 512)
append bit length of message as 64-bit little-endian integer to message

//Process the message in successive 512-bit chunks:
for each 512-bit chunk of message
    break chunk into sixteen 32-bit little-endian words w[i], 0 ≤ i ≤ 15

    //Initialize hash value for this chunk:
    var int a := h0
    var int b := h1
    var int c := h2
    var int d := h3

    //Main loop:
    for i from 0 to 63
        if 0 ≤ i ≤ 15 then
            f := (b and c) or ((not b) and d)
            g := i
        else if 16 ≤ i ≤ 31
            f := (d and b) or ((not d) and c)
            g := (5×i + 1) mod 16
        else if 32 ≤ i ≤ 47
            f := b xor c xor d
            g := (3×i + 5) mod 16
        else if 48 ≤ i ≤ 63
            f := c xor (b or (not d))
            g := (7×i) mod 16
 
        temp := d
        d := c
        c := b
        b := leftrotate((a + f + k[i] + w[g]),r[i]) + b
        a := temp
    Next i
    //Add this chunk's hash to result so far:
    h0 := h0 + a
    h1 := h1 + b 
    h2 := h2 + c
    h3 := h3 + d
End ForEach
var int digest := h0 append h1 append h2 append h3 //(expressed as little-endian)
```

\* 摘自Wikipedia

## 更多

- MD4（RFC 1320）是 MIT 的 Ronald L. Rivest 在 1990 年设计的，MD 是 Message Digest 的缩写。其输出为 128 位。MD4 已证明不够安全。
- MD5（RFC 1321）是 Rivest 于1991年对 MD4 的改进版本。它对输入仍以 512 位分组，其输出是 128 位。MD5 比 MD4 复杂，并且计算速度要慢一点，更安全一些。MD5 已被证明不具备"强抗碰撞性"。
- SHA （Secure Hash Algorithm）是一个 Hash 函数族，由 NIST（National Institute of Standards and Technology）于 1993 年发布第一个算法。目前知名的 SHA-1 在 1995 年面世，它的输出为长度 160 位的 hash 值，因此抗穷举性更好。SHA-1 设计时基于和 MD4 相同原理，并且模仿了该算法。SHA-1 已被证明不具"强抗碰撞性"。
- 为了提高安全性，NIST 还设计出了 SHA-224、SHA-256、SHA-384，和 SHA-512 算法（统称为 SHA-2），跟 SHA-1 算法原理类似。SHA-3 相关算法也已被提出。

# 参考 References

https://en.wikipedia.org/wiki/Hash_function

https://zh.wikipedia.org/zh-hans/MD5

