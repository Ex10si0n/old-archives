---
title: 「数论」杨辉三角预处理组合数

date: 2017-06-23 21:23

tags: [数论]

categories: C++

thumbnail: http://i4.bvimg.com/1949/fe83b19803e81a9ct.jpg
---

# 杨辉三角预处理组合数

今天，我要和和大家好好研究一下杨辉三角为什么这么神奇！

## 引进

需要数学基础并不是很多，高中狗都学过了：

>排列组合
>
>二项式定理

那么今天我们首先来研究一下组合数和杨辉三角的关系：

提到组合数，大家都不陌生了，下面上公式

![](http://latex.codecogs.com/gif.latex?c%5Cbinom%7Bm%7D%7Bn%7D%3D%5Cfrac%7BP%5Cbinom%7Bm%7D%7Bn%7D%7D%7Bm%21%7D%3D%5Cfrac%7Bn%21%7D%7B%28n-m%29%21%5Ctimes%20m%21%7D)

可是，如果把这个公式直接模拟到程序中写了一套甚至带阶乘的代码显然是很不理智的行为，即使你用long long，因为组合数这种东西数一大真的很危险，不信你在[Wolframalpha](http://www.wolframalpha.com/)输入一个：

```markdown
C(2000,256)
```

我反正验证了一下得出了这个数：

```
5246695504797406350371114957553464279587245870067638796390168428899811763472081872049320489731744109451143566302961371189076452290657748449919198708497969225053826218448474613280684867925111376891511983327736218149880627796243209436467244060670470422176695658748097135504364544291240162864574987155021856754524790806611467746230625	
```

据我的了解，这个数可以秒long long好几圈了！其实很不幸的是，杨辉三角预处理并不是高精度，所以这么大的数也是无法存的，但是我没说不可以取模啊，一般的考题中都是取一个大质数的模，这样就可以在生成这个数的过程中一步一步地把数模小了。

## 推导

下面仔细研究一下杨辉三角和组合数：

```
  n 
m 1 1
  1 2 1
  1 3 3 1
  1 4 6 4 1
  1 5 10 10 5 1
  1 6 15 20 15 6 1
...    ...     ...
```

好的，杨辉三角的性质不难发现，总结一下

```cpp
table[n][m]=table[n-1][m-1]+table[n][m]
```

然而我们打一个组合数的表

```
C(1,1)=1
C(2,1)=2
C(2,2)=1
C(3,1)=3
C(3,2)=3
C(3,3)=1
C(4,1)=4
C(4,2)=6
C(4,3)=4
C(4,4)=1
```

天啦噜，貌似有一些蹊跷：

```
1
  2
    1
  3
    3
      1
  4
    6
      4
        1
```

等等，这不就是杨辉三角嘛...

所以说，组合数还有一种递推式：

![](http://latex.codecogs.com/gif.latex?%5CLARGE%20C%5Cbinom%7Bm%7D%7Bn%7D%3DC%5Cbinom%7Bm-1%7D%7Bn-1%7D&plus;C%5Cbinom%7Bm%7D%7Bn-1%7D)

嗯，这样就可以愉快的用for循环解决组合数了：

```cpp
long long C[maxn][maxn];  
void Comb(int n, int m){  
    memset(C, 0, sizeof(C));  
    C[0][0] = 1;  
    for(int i = 0; i <= n; i++){  
        C[i][0] = C[i][i] = 1;  
        for(int j = 1; j < i; j++)  
            C[i][j] = (C[i-1][j-1] + C[i-1][j]);
    }  
}  
```

## 优化

当数大了的时候要求到取模，那么加入一个p就好啦：

```cpp
long long C[maxn][maxn];  
void Comb(int n, int m, int p){  
    memset(C, 0, sizeof(C));  
    C[0][0] = 1;  
    for(int i = 0; i <= n; i++){  
        C[i][0] = C[i][i] = 1;  
        for(int j = 1; j < i; j++)  
            C[i][j] = (C[i-1][j-1] + C[i-1][j]) % p;  
    }  
}  
```

用Lucas优化的组合数取模：

```cpp
long long mod_pow(long long n, long long k, long long p) {  
    if (k == 0) return 1;  
    if (k == 1) return n;  
    long long ans = mod_pow(n * n % p, k>>1, p);  
    if (k&1) ans = ans * n % p;  
    return ans;  
}  
  
long long Comb(long long n, long long m, long long p) {  
    if (m > n) return 0;  
    m = min(m, n - m);  
    long long x = 1, y = 1;  
    for (long long i = 0; i < m; i++) {  
        x = x * (n - i) % p;  
        y = y * (i + 1) % p;  
    }  
    return y * mod_pow(x, p - 2, p) % p;  
}  
  
long long Lucas(long long n, long long m, long long p) {  
    if (m == 0) return 1;  
    return Comb(n % p, m % p, p) * Lucas(n / p, m / p, p) % p;  
}  
```

其中的构造请同学们再想想，代码里会有很多技巧等待你去发现，在此我就保密咯！

今天写到这里，因为我感冒了，所以脑子和身体都有些不好使了，出错见谅。

## 补充

附带一个打印杨辉三角的代码

```cpp
#include<iostream>
#define ll long long
using namespace std;
ll c[10001][10001];
ll p,ans;
int main(){
    cin>>p;
    for(int n=1;n<=p;n++) c[n][1]=1;
    for(int n=1;n<=p;n++)
    c[n][n]=1;
    for(int n=3;n<=p;n++){
        for(int k=2;k<=p;k++){
            c[n][k]=c[n-1][k-1]+c[n-1][k];
        }
    }
    for(int n=1;n<=p;n++){
        for(int k=1;k<=n;k++){
            cout<<c[n][k]<<" ";
        }cout<<endl;
    }
    return 0;
}
```

用的原理就是上述公式，同学们可以手敲一遍来理解。
