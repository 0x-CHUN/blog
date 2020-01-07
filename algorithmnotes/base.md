# 基础算法

## 排序

1. 冒泡排序：

   两个数比较大小，较大的数下沉，较小的数冒起来
   ```c++
   void bubble_sort(vector<int>& v) {
       for (int i = 0; i < v.size()-1; i++){
           for (int j = 0; j < v.size() - 1 - i; j++){
               if (v[j] > v[j + 1])
	                swap(v[j], v[j + 1]);
	        }
	    }
	}
	```

2. 选择排序：

   n次操作，每次操作将最小的放入前面

    ```c++
    void select_sort(vector<int> &v)
    {
        int n = v.size();
        for (int i = 0; i < n; i++){//n次操作
            int min_idx = i;//最小的数的下标
            for (int j = i+1; j < n; j++){
                if (v[j] < v[i]){
                    min_idx = j;//更新下标
                }
            }
            swap(v[i], v[min_idx]);//交换
        }
    }
    ```
   
   时间复杂度为$O(n^2)$
   
3. 插入排序：

   n-1次操作（因为第一个默认有序），每次将未排序的插入到合适的位置

   ```c++
   void insert_sort(vector<int>& v)
   {
   	int j;//合适的位置
   	for (int i = 1; i < v.size(); i++){
   		j = i;
   		int tmp = v[i];//tmp为要插入的数据
   		while (j > 0 && tmp < v[j-1]){
   			v[j] = v[j - 1];//往后移
   			j--;
   		}
   		v[j] = tmp;
   	}
   }
   ```

   时间复杂度为$O(n^2)$

4. 归并排序

   思路：

   * 如果给的数组只有一个元素的话，直接返回
   * 把整个数组分为尽可能相等的两个部分
   * 对于两个被分开的两个部分进行整个归并排序
   * 把两个被分开且排好序的数组拼接在一起

   ```c++
   void merge(int arr[], int l, int m, int r) 
   { 
       int i, j, k; 
       int n1 = m - l + 1; 
       int n2 =  r - m; 
   
       int L[n1], R[n2]; 
   
       for (i = 0; i < n1; i++) 
           L[i] = arr[l + i]; 
       for (j = 0; j < n2; j++) 
           R[j] = arr[m + 1+ j]; 
   
       i = 0; 
       j = 0; 
       k = l; 
       while (i < n1 && j < n2) 
       { 
           if (L[i] <= R[j]) 
           { 
               arr[k] = L[i]; 
               i++; 
           } 
           else
           { 
               arr[k] = R[j]; 
               j++; 
           } 
           k++; 
       } 
     
       while (i < n1) 
       { 
           arr[k] = L[i]; 
           i++; 
           k++; 
       } 
     
       while (j < n2) 
       { 
           arr[k] = R[j]; 
           j++; 
           k++; 
       } 
   } 
     
   void mergeSort(int arr[], int l, int r) 
   { 
       if (l < r) 
       { 
           int m = l+(r-l)/2; 
     
           mergeSort(arr, l, m); 
           mergeSort(arr, m+1, r); 
     
           merge(arr, l, m, r); 
       } 
   } 
   ```

5. 快速排序

   ```c++
   void quicksort(int arr[], int l, int r) {
       if (l < r) {
           int pivot = partition(arr, l, r);
           quicksort(arr, l, pivot-1);
           quicksort(arr, pivot+1, r);
       }
   }
   
   int partition(int arr[], int l, int r) {
       int k = l, pivot = arr[r];
       for (int i = l; i < r; ++i)
           if (arr[i] <= pivot) 
               std::swap(arr[i], arr[k++]);
       std::swap(arr[k], arr[r]);
       return k;
   }
   ```

| 排序算法 | 平均时间复杂度 | 稳定性 |
| :------: | :------------: | -------- |
| 冒泡排序 |    $O(n^2)$    | 稳定 |
| 选择排序 |    $O(n^2)$    | 不稳定 |
| 插入排序 |    $O(n^2)$     | 稳定 |
| 希尔排序 |    $O(n^{1.5})$    | 不稳定 |
| 快速排序 |   $O(N*logN)$   | 不稳定 |
| 归并排序 |   $O(N*logN)$   | 稳定 |
|  堆排序  |   $O(N*logN)$   | 不稳定 |
| 基数排序 |   $O(d(n+r))$   | 稳定 |

做题的话直接使用STL的sort：

```c++
#include <algorithm>
#include <functional>
#include <array>
#include <iostream>
 
int main()
{
    std::array<int, 10> s = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3}; 
 
    // 用默认的 operator< 排序
    std::sort(s.begin(), s.end());
    for (auto a : s) {
        std::cout << a << " ";
    }   
    std::cout << '\n';
 
    // 用标准库比较函数对象排序
    std::sort(s.begin(), s.end(), std::greater<int>());
    for (auto a : s) {
        std::cout << a << " ";
    }   
    std::cout << '\n';
 
    // 用自定义函数对象排序
    struct {
        bool operator()(int a, int b) const
        {   
            return a < b;
        }   
    } customLess;
    std::sort(s.begin(), s.end(), customLess);
    for (auto a : s) {
        std::cout << a << " ";
    }   
    std::cout << '\n';
 
    // 用 lambda 表达式排序
    std::sort(s.begin(), s.end(), [](int a, int b) {
        return b < a;   
    });
    for (auto a : s) {
        std::cout << a << " ";
    } 
    std::cout << '\n';
}
```

## 散列

根据元素的 key 找到元素在散列表中存放的位置。

* 最直接的即下标i为关键字
* 散列函数
  * 除法散列法：通过取k除以m的余数，将关键字k映射到m个槽的某一个中去。**散列函数为：h(k)=k mod m** 。m不应是2的幂，通常m的值是与2的整数幂不太接近的质数。
  * 乘法散列法：用关键字k乘上常数A（0<A<1），并抽取kA的小数部分。然后，用m乘以这个值，再取结果的底。散列函数如下：h(k) = m(kA mod 1)。
  * 全域散列：给定一组散列函数H，每次进行散列时候从H中随机的选择一个散列函数h，使得h独立于要存储的关键字。全域散列函数类的平均性能是比较好的。

散列函数冲突时的解决办法：

* 线性探测
* 二次探测
* 链接法

一个常见的字符串散列：

```c++
int hash(const string & key,int tablesize)
{
        int hashVal = 0;
        for(int i =0;i<key.length();i++)
            hashVal = 37*hashVal + key[i];//Horner's 规则，选37
        hashVal %= tableSize;
        if(hashVal<0)  //计算的hashVal溢出
           hashVal += tableSize;
       return hashVal;
}
```

## 递归

### 分治

分而治之：

* 分解：分解为相似的子问题
* 解决：递归求解子问题
* 合并：将子问题的解合并

### 递归

递归最关键的一点：递归调用、递归边界

最经典的n的阶乘：

```c++
int F(int n){
    if(n == 0)
        return 1;//递归边界
    else
        return F(n-1) * F(n);//递归式
}
```

Fibonacci数列：

```c++
int F(int n){
    if(n == 0 || n == 1)//递归边界
        return 1;
    else
        return F(n-1) + F(n-2);//递归式
}
```

全排列问题的递归实现：

```c++
//E = [a , b , c]
//prem（E）= a.perm（b,c）+ b.perm（a,c）+ c.perm（a,b）
void perm(vector<int> arr, int start, int ends) {
	if (start == ends) {
		for (int i = 0; i < arr.size(); i++){
			cout << arr[i] << " ";
		}
		cout << endl;
	}
	else{
		for (int i = start; i < ends; i++){
			swap(arr[start], arr[i]);
			perm(arr, start+1,ends);
			swap(arr[start], arr[i]);
		}
	}
}
//C++:next_permutation(arr, arr+size);求下一个排列组合
//prev_permutation(arr, arr+size);求上一个排列组合
```

## 贪心

关键：总考虑局部最优，从而达到全局最优；

> 月饼是中国人在中秋佳节时吃的一种传统食品，不同地区有许多不同风味的月饼。现给定所有种类月饼的库存量、总售价、以及市场的最大需求量，请你计算可以获得的最大收益是多少。

```c++
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

struct mooncake {
	float mount, price, unit;
};

int cmp(mooncake a, mooncake b) {
	return a.unit > b.unit;
}

int main() {
	int n, need;
	cin >> n >> need;
	vector<mooncake> a(n);
	for (int i = 0; i < n; i++) scanf_s("%f", &a[i].mount);
	for (int i = 0; i < n; i++) scanf_s("%f", &a[i].price);
	for (int i = 0; i < n; i++) a[i].unit = a[i].price / a[i].mount;
	sort(a.begin(), a.end(), cmp);//单价从高到低
	float result = 0.0;
	for (int i = 0; i < n; i++) {
		if (a[i].mount <= need) {//需求高于数量，全卖出
			result = result + a[i].price;
		}
		else {
			result = result + a[i].unit * need;//卖出需求的
			break;
		}
		need = need - a[i].mount;//更新需求
	}
	printf("%.2f", result);
	return 0;
}
```

区间贪心：

>  区间不相交问题：给定x 轴上n 个闭区间。去掉尽可能少的闭区间，使剩下的闭区间都不相交。

方法：

将所有的线段按照**终点升序，起点降序**的顺序进行排列。贪心策略是：选择第一个线段，之后每次选择不相交的线段，凡是相交或者是覆盖的线段均丢弃。

```c++
#include <iostream>
#include <algorithm> 
using namespace std;

struct line
{
    int s;
    int e;
} a[100000];

bool cmp(line a, line b)
{
    if(a.e == b.e)
    {
        return a.s > b.s;
    }
    else return a.e < b.e;
}
  
int main()
{
    int n,s = 1;
    cin>>n;
    for(int i=0; i<n; i++)
    {
        cin>>a[i].s>>a[i].e;
    }

    sort(a,a+n,cmp);
    
    for(int i=1; i<n; i++)
    {
        if (a[i].e==a[i-1].e || a[i].s<a[i-1].e)
        {
            a[i]=a[i-1];
        }
        else
        {
            s++;
        }
    }
    cout<<s;
    
    return 0;
}
```

> 数轴上有n个闭区间[ai,bi]。取尽量少的点，使得每个区间内都至少有一个点（不同区间内含的点可以是同一个）。

贪心策略：按照**终点升序，起点降序**的方式排序排序，从前向后遍历，当遇到没有加入集合的区间时，选取这个区间的右端点b。

```c++
#include <iostream>
#include <algorithm>
using namespace std;

struct line
{
    int s,e;
} a[10000];

bool cmp(line a, line b)
{
    if(a.e == b.e)
    {
        return a.s > b.s;
    }
    else return a.e < b.e;
}

int main()
{
    int n;
    while(cin>>n)
    {
        for(int i=0; i<n; i++)
        {
            cin>>a[i].s>>a[i].e;
        }
        sort(a,a+n,cmp);
        int ans = 1;
        int point = a[0].e;
        for(int i=1; i<n; i++)
        {
            if(a[i].s > point)
            {
                point = a[i].e;
                ans++;
            }
        }
        cout<<ans<<endl;
    }
    return 0;
}
```

## 二分

### 二分搜索

```c++
int binary_search(vector<int> arr, int key) {
	int left = 0;
	int right = arr.size() - 1;
	while (left <= right){
		int mid = (left + right) / 2;
		if (arr[mid] == key)
			return mid;
		else if (arr[mid] > key)
			right = mid - 1;
		else
			left = mid + 1;
	}
	return -1;
}
```

### 二分查找

```c++
int lower_bound(vector<int> arr, int key) {
	int left = 0;
	int right = arr.size() - 1;
	while (left < right) {
		int mid = (left + right) / 2;
		if (arr[mid] >= key)
			right = mid;
		else
			left = mid + 1;
	}
	return left;
}

int upper_bound(vector<int> arr, int key) {
	int left = 0;
	int right = arr.size() - 1;
	while (left < right) {
		int mid = (left + right) / 2;
		if (arr[mid] > key)
			right=mid;
		else
			left = mid + 1;
	}
	return left;
}
```

其模型就是为了解决有序序列中第一个满足某条件的元素的位置：

```c++
//解题模板
int solve(int left,int right){
    while(left < right){
        mid = (left+right) / 2;
        if(条件成立){
            right = mid;
        }else{
            left = mid+1;
        }
    }
    return left;
}
```

### 二分法拓展

求根

```c++
const double eps = 1e-5;

double sqrt(double k) {
	double l = 0.0, r, mid;
	if (k >= 1) r = k; 
	if (k < 1)  r = 1;  
	while (fabs(l - k / l) > eps) {
		mid = l + (r - l) / 2;
		if (mid < k / mid)
			l = mid;
		else
			r = mid;
	}
	return l;
}
```

快速幂

```c++
typedef long long LL;

LL Pow(LL a, LL b, LL m) {
	LL ans = 1;
	while (b > 0){
		if (b % 2 == 1) {
			ans = ans * a % m;
		}
		a = a * a % m;
		b /= 2;
	}
	return ans;
}

```

## 其他技巧

### 打表

素数筛

```c++
const int N = 1000000;
int prime[N + 10];  //第i个素数是prime[i],从0开始记录
bool is_prime[N + 10];  //is_prime[i]为true代表i是素数

int sieve(int n)  //埃氏筛，筛出小于等于n的所有素数并返回个数
{
	int p = 0;  //记录个数
	for (int i = 0; i <= n; i++)
		is_prime[i] = true;
	is_prime[0] = is_prime[1] = false;
	for (int i = 2; i <= n; i++)
	{
		if (is_prime[i])
		{
			prime[p++] = i;
			for (int j = 2 * i; j <= n; j += i)
				is_prime[j] = false;
		}
	}
	return p;
}
```

