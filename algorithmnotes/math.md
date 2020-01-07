# 数学问题

## 最大公约数和最小公倍数

### 最大公约数

欧几里得算法：

- gcd(a,b) = gcd(a,b-a)
- gcd(a,0) = a
- gcd(a,b) 时{ax+by |x,y ∈ Z}里最小的正整数

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

### 最小公倍数

最小公倍数：`a*b/d`（d为最大公约数，实际计算的时候采用`a/d*b`防溢出)

## 分数

```c++
struct Fraction
{
	int up, down;
};

Fraction reduction(Fraction res){
	if (res.down < 0) {
		res.up = -res.up;
		res.down = -res.down;
	}
	if (res.up == 0)
		res.down = 1;
	else {
		int d = gcd(abs(res.up), abs(res.down));
		res.up /= d;
		res.down /= d;
	}
	return res;
}

Fraction add(Fraction a, Fraction b) {
	Fraction res;
	res.up = a.up * b.down + b.up * a.down;
	res.down = a.down * b.down;
	return reduction(res);
}

Fraction sub(Fraction a, Fraction b) {
	Fraction res;
	res.up = a.up * b.down - b.up * a.down;
	res.down = a.down * b.down;
	return reduction(res);
}

Fraction mul(Fraction a, Fraction b){
	Fraction res;
	res.up = a.up * b.up;
	res.down = a.down * b.down;
	return reduction(res);
}

Fraction div(Fraction a, Fraction b) {
	Fraction res;
	res.up = a.up * b.down;
	res.down = a.down * b.up;
	return reduction(res);
}
```

## 素数

素数的判断：

```c++
bool isprime(int n) {
	if (n == 1)
		return false;
	for (int i = 2; i*i < n; i++){
		if (n % i == 0)
			return false;
	}
	return true;
}
```

素数筛：

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

## 质因数分解

**每个合数都可以写成几个质数相乘的形式**

```c++
int i, n;
printf("Please input an integer!\n");
cin >> n;
for (i = 2; i <= n; i++)
{
	while (n != i)     //若i=n，则质因数就是n本身  
	{
		if (n % i == 0)  //若i是质因数，则打印出i的值，并用商给n赋新值  
		{
			printf("%d\n", i);
			n = n / i;
		}
		else break;//若不能被i整除，则算下一个i  
	}
}
printf("%d\n", n);   //这里是打印最后一个质因数，也就是等于i时的那个  
```

## 大整数运算

```c++
using namespace std;

string sub(string n1, string n2)
{
	if (n1 < n2)
		return "-" + sub(n2, n1);
	string res;
	if (n1.length() >= n2.length())
		n2 = string(n1.length() - n2.length(), '0') + n2;
	int borrow = 0;
	int len = n1.length();
	for (int i = len - 1; i >= 0; i--)
	{
		if (n1[i] < n2[i])
		{
			res = char((n1[i] - n2[i] - borrow + 10) + '0') + res;
			borrow = 1;
		}
		else
		{
			res = char((n1[i] - n2[i] - borrow) + '0') + res;
			borrow = 0;
		}
	}
	for (int i = 0; i < res.length(); i++)
	{
		if (res[i] != '0')
		{
			return res.substr(i);
		}
	}
	return "0";
}

string add(string n1, string n2)
{
	if (n1[0] == '-' && n2[0] == '-')
		return "-" + add(n1.substr(1), n2.substr(1));
	else if (n1[0] == '-')
		return sub(n2, n1.substr(1));
	else if (n2[0] == '-')
		return sub(n1, n2.substr(1));
	else
	{
		string res;
		int carry = 0;
		if (n1.length() >= n2.length())
			n2 = string(n1.length() - n2.length(), '0') + n2;
		else
			n1 = string(n2.length() - n1.length(), '0') + n1;
		int len = n1.length();
		for (int i = len - 1; i >= 0; i--)
		{
			res = char((n1[i] - '0' + n2[i] - '0' + carry) % 10 + '0') + res;
			carry = (n1[i] - '0' + n2[i] - '0' + carry) / 10;
		}
		if (carry > 0)
			res = char(carry + '0') + res;
		return res;
	}
}

string mul(string a, string b)
{
	if (a.length() < b.length())
		swap(a, b);
	int len1 = b.length();
	int len2 = a.length();
	stack<string> st;
	for (int i = len1-1; i >= 0; i--)
	{
		string num;
		int carry = 0;
		for (int j = len2-1; j >= 0; j--)
		{
			num = char(((a[j] - '0') * (b[i] - '0') + carry) % 10 + '0') + num;
			carry = ((a[j] - '0') * (b[i] - '0') + carry) / 10;
		}
		if (carry > 0)
			num = char(carry + '0') + num;
		for (int k = 0; k < len1 - 1 - i; k++)
		{
			num += '0';
		}
		st.push(num);
	}
	while (st.size() > 1)
	{
		string a = st.top(); st.pop();
		string b = st.top(); st.pop();
		st.push(add(a, b));
	}
	return st.top();
}
```

推荐直接用Java的BigInteger

```java
import java.math.BigInteger;

Scanner cin = new Scanner(System.in); 
while(cin.hasNext()){ 
    BigInteger a;
    a = cin.nextBigInteger();
}
//BigInteger支持任意精度的整数。
 
// 常用方法：
abs()//返回其值是此BigInteger的绝对值的BigInteger。
add(BigInteger val)//返回其值为(this+val)的BigInteger。
subtract(BigIntegerval)//返回其值为(this-val)的BigInteger。
multiply(BigInteger val) //返回其值为(this*val)的BigInteger。
divide(BigInteger val)//返回其值为(this/val)的BigInteger。
remainder(BigInteger val)//返回其值为(this%val)的BigInteger。
compareTo(BigInteger val)//将此BigInteger与指定的BigInteger进行比较。返回值1、0、-1分别表示大于、等于、小于
pow(intexponent) //返回当前大数的exponent次幂。
toString()//返回此BigInteger的十进制字符串表示形式。
toString(intradix)//返回此BigInteger的给定基数(radix进制)的字符串表示形式。
```

## 组合数

$$
C_{n}^{m}=\frac{n !}{m !(n-m) !}
$$

* 方法一：直接计算

  ```c++
  LL C(LL n, LL m) {
  	LL ans = 1;
  	for (LL i = 1; i <= n; i++)
  		ans *= i;
  	for (LL i = 1; i <= m; i++)
  		ans /= i;
  	for (LL i = 1; i <= n-m; i++)
  		ans /= i;
  	return ans;
  }
  //较差，21！就溢出了
  ```

* 方法二：递推

  $\mathrm{C}_{\mathrm{n}}^{\mathrm{m}}=\mathrm{C}_{\mathrm{n}-1}^{\mathrm{m}}+\mathrm{C}_{\mathrm{n}-1}^{\mathrm{m}-1}$

  ```c++
  LL res[67][67];
  
  LL C(LL n, LL m) {
  	for (int i = 0; i <= n; i++)
  		res[i][0] = res[i][i] = 1;
  	for (int i = 2; i <= n; i++){
  		for (int j = 0; j <= i / 2; j++){
  			res[i][j] = res[i - 1][j] + res[i - 1][j - 1];
  			res[i][i - j] = res[i][j];
  		}
  	}
  	return res[n][m];
  }
  // n = 67, m = 33时溢出
  ```

* 方法三：变形式

  $\frac{(n-m+1) \times(n-m+2) \times \cdots \times(n-m+i)}{1 \times 2 \times \cdots \times i}$

  ```c++
  LL C(LL n, LL m) {
  	LL ans = 1;
  	for (LL i = 1; i <= m; i++)
  		ans = ans * (n - m + i) / i;
  	return ans;
  }// n = 66, m = 31时溢出
  ```

  