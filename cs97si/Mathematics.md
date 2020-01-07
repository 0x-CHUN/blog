# Mathematics

## Algebra 

幂的和：

$\sum_{k=1}^{n} k^{2}=\frac{1}{6} n(n+1)(2 n+1)$

$\sum k^{3}=\left(\sum k\right)^{2}=\left(\frac{1}{2} n(n+1)\right)^{2}$

快速求幂：
$$
a^{n}=\left\{\begin{array}{ll}{1} & {n=0} \\ {a} & {n=1} \\ {\left(a^{n / 2}\right)^{2}} & {n \text { is even }} \\ {a\left(a^{(n-1) / 2}\right)^{2}} & {n \text { is odd }}\end{array}\right.
$$

```c++
//递归版
double pow(double a, int n) {
    if(n == 0) return 1;
    if(n == 1) return a;
    double t = pow(a, n/2);
    return t * t * pow(a, n%2);
}
//非递归版
double pow(double a, int n) {
    double ret = 1;
    while(n) {
        if(n%2 == 1) ret *= a;
        a *= a; n /= 2;
    }
    return ret;
}
```

## Number Theory 

最大公因子：

* gcd(a,b) = gcd(a,b-a)
*  gcd(a,0) = a
*  gcd(a,b) 时{ax+by |x,y ∈ Z}里最小的正整数

欧几里得算法：

```reStructuredText
gcd(1989,867) = gcd(1989 − 2 × 867,867)
= gcd(255,867)
= gcd(255,867 − 3 × 255)
= gcd(255,102)
= gcd(255 − 2 × 102,102)
= gcd(51,102)
= gcd(51,102 − 2 × 51)
= gcd(51,0)
= 51
```

```c++
int gcd(int a, int b) {
    while(b){int r = a % b; a = b; b = r;}
    return a;
}
```

## Combinatorics

$C^{n}_{k}$是从n个物体中选出k个的选法的数量，也代表$(x+y)^{n}$中$x^{k}y^{n-k}$的系数；

计算方法：

* 方法一：
  $$
  C^{n}_{k} = \frac{n(n-1) \cdots(n-k+1)}{k !}
  $$
  不适用于n非常大，但是n-k比较小的情况；

* 方法二：

  杨辉三角：

  ```
  　　　　　　　　１
  　　　　　　　１　１
  　　　　　　１　２　１
  　　　　　１　３　３　１
  　　　　１　４　６　４　１
  　　　１　５　10　10　５　１
  　　１　６　15　20　15　６　１
  　１　７　21　35　35　21　７　１
  １　８　28　56　70　56　28　８　１
  ```

  第n层对应着$(a+b)^{n}$展开的系数；

  性质：

  * 第n行第k个数为：$C^{k-1}_{n-1}$
  * 除了首尾都为1，其他满足：$C^{i}_{n} = C^{i-1}_{n-1} + C^{i}_{n-1}$
  * 第n行的数字和为$2^{n-1}$

Fibonacci序列：

* $F_0 = 0, F_1 = 1$
* $F_n = F_{n-1} + F_{n-2}$

根据递推关系得：
$$
F_{n}=(1 / \sqrt{5})\left(\varphi^{n}-\overline{\varphi}^{n}\right)
$$
不能计算n非常大的时候（$ \sqrt{5}$是无理数）；

矩阵计算方法：
$$
\left[\begin{array}{c}{F_{n+1}} \\ {F_{n}}\end{array}\right]=\left[\begin{array}{ll}{1} & {1} \\ {1} & {0}\end{array}\right]\left[\begin{array}{c}{F_{n}} \\ {F_{n-1}}\end{array}\right]=\left[\begin{array}{ll}{1} & {1} \\ {1} & {0}\end{array}\right]^{n}\left[\begin{array}{c}{F_{1}} \\ {F_{0}}\end{array}\right]
$$

## Geometry

技巧：

* 使用double类型
* 尽量避免除法运算
* 比较等于时用：`if(abs(x) < EPD)`



向量(x, y)：

* 距离：$\sqrt{x^2 + y^2}$
* 逆时针旋转角度：$\left[\begin{array}{cc}{\cos \theta} & {-\sin \theta} \\ {\sin \theta} & {\cos \theta}\end{array}\right]\left[\begin{array}{l}{x} \\ {y}\end{array}\right]$



线段相交：

线段`ax+by+c=0`和`dx+ey+f=0`:

向量形式：$\left[\begin{array}{ll}{a} & {b} \\ {d} & {e}\end{array}\right]\left[\begin{array}{l}{x} \\ {y}\end{array}\right]=-\left[\begin{array}{l}{c} \\ {f}\end{array}\right]$

左乘$\left[\begin{array}{ll}{a} & {b} \\ {d} & {e}\end{array}\right]^{-1}=\frac{1}{a e-b d}\left[\begin{array}{cc}{e} & {-b} \\ {-d} & {a}\end{array}\right]$（边界条件：`ae=bd`，两直线重复或平行）



三个点`A、B、C`，求到三个点距离相等得点：

求AB和BC得二等分线得相交点。



三个点`A、B、C`，求围成的面积：

$2 S=|(B-A) \times(C-A)|$

$\left(x_{1}, y_{1}\right) \times\left(x_{2}, y_{2}\right)=\left|\begin{array}{ll}{x_{1}} & {x_{2}} \\ {y_{1}} & {y_{2}}\end{array}\right|=x_{1} y_{2}-x_{2} y_{1}$

