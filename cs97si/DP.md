# Dynamic Programming

动态规划（DP)：将问题分解成更加简单的子问题。

解决DP问题的步骤：

* 定义子问题
* 原问题与子问题之间的递归关系
* 最基本的子问题求解

## 1-dimensional DP

> Problem: given n, find the number of different ways to write n as the sum of 1, 3, 4
>
> Example: for n = 5, the answer is 6
>
> 5 = 1 + 1 + 1 + 1 + 1
> = 1 + 1 + 3
> = 1 + 3 + 1
> = 3 + 1 + 1
> = 1 + 4
> = 4 + 1

* 定义子问题：令$D_{n}$为n写成1，3，4的和的不同种类数；
* 递归关系：$D_{n}=D_{n-1}+D_{n-3}+D_{n-4}$
* 最基本的子问题：$D_{0}=D_{1}=D_{2}=1,D_{3}=2$

```c
D[0] = D[1] = D[2] = 1; D[3] = 2;
for(i = 4; i <= n; i++)
    D[i] = D[i-1] + D[i-3] + D[i-4];
```



> Given n, find the number of ways to fill a 3 × n board with dominoes

* 定义子问题

  ![img](assets/{EAFD58C4-316B-D3FA-217B-F3FE94FD55C9}.png)

* 递归关系

  

  ![img](assets/{0DD4C1E3-8E8C-3AE0-34CE-8F3FCBF5F1B7}.png)

  由此可得递推关系

* A[0]=1、A[1]=3

