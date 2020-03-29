# Leetcode

## [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

一遍哈希表:

进行一次迭代，检查表中是否存在`target-nums[i]`的元素，如果存在则返回对应解；

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> m; //哈希表，对应<值，下标>
        for(int i = 0; i < nums.size();i ++){ // 遍历
            if(m.count(target-nums[i])) // 检查表中是否存在target-nums[i]
                return {m[target-nums[i]], i};
            else
                m[nums[i]] = i;
        }
        return {0, 0};
    }
};
```

## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

类比加法，表头开始模拟逐位相加的过程。因为为逆序存储，直接遍历即可。

增加一个dummyHead可以使头节点为子节点，不需要特殊处理。

```cpp
class Solution
{
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2)
    {
        ListNode *dummyHead = new ListNode(0); // 虚节点
        ListNode *p = dummyHead;
        int carry = 0; // 进位标志
        while (l1 || l2 || carry != 0)
        {
            int tmp = carry; // 加上进位
            if (l1) // 逐位相加
                tmp += l1->val;
            if (l2)
                tmp += l2->val;
            carry = tmp / 10; // 进位
            tmp = tmp % 10;
            ListNode *node = new ListNode(tmp); // 新建node
            p->next = node; // p指向新的node
            p = node; // p指针移动
            if (l1)
                l1 = l1->next;
            if (l2)
                l2 = l2->next;
        }
        return dummyHead->next;
    }
};
```

#### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

思路：用一个数组映射每个字符最后出现的位置，设置一个变量left为当前字符的最后出现的位置，然后遍历整个字符，先将left赋值为当前字符最后出现的位置（默认为-1，便于计算距离），然后更新映射的数组里的值，然后更新无重复字符的子串的长度为max(之前的无重复字符的子串长度，当前位置-当前字符最后出现的位置)；

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> m(256,-1); //每个字符与最后出现的位置之间的映射
        int left = -1;// 初始化为-1，便于计算
        int res=0;
        for (int i = 0; i < s.length(); i++)
        {
            left = max(left, m[s[i]]);// left记录当前字符的最后出现的位置
            m[s[i]]=i;//跟新当前字符的最后出现的位置，为下一轮循环准备
            res = max(res, i - left);// 跟新无重复字符的最长子串长度
        }
        return res;
    }
};
```

#### [4. 寻找两个有序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

思路：因为为有序数组，且要求的时间复杂度为$O(log(m+n))$，则一般为二分查找；

```cpp
class Solution {
public:
	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
		int n = nums1.size();
		int m = nums2.size();

		if (n > m)  
		{
			return findMedianSortedArrays(nums2, nums1);
		}
		int LMax1, LMax2, RMin1, RMin2, c1, c2, lo = 0, hi = 2 * n;  

		while (lo <= hi)   //二分
		{
			c1 = (lo + hi) / 2; 
			c2 = m + n - c1;

			LMax1 = (c1 == 0) ? INT_MIN : nums1[(c1 - 1) / 2];
			RMin1 = (c1 == 2 * n) ? INT_MAX : nums1[c1 / 2];
			LMax2 = (c2 == 0) ? INT_MIN : nums2[(c2 - 1) / 2];
			RMin2 = (c2 == 2 * m) ? INT_MAX : nums2[c2 / 2];

			if (LMax1 > RMin2)
				hi = c1 - 1;
			else if (LMax2 > RMin1)
				lo = c1 + 1;
			else
				break;
		}
		return (max(LMax1, LMax2) + min(RMin1, RMin2)) / 2.0;
	}
};
```

#### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

思路：动态规划

* 令$dp[i][j]$代表字符串从i到j是否为字符串

* 递推关系：
  $$
  dp[i][j]=(dp[i+1][j-1] \&\& S_i == S_j)
  $$

* base case：单个字符为回文串：$dp[i][i] == true$，长度为2的回文字符串：$dp[i][i+1]=(S_i==S_j)$;

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        if(n == 0 || n == 1)
            return s;
        int start=0;//起始点
        int maxlen=1;//最大的长度
        vector<vector<int>> dp(n,vector<int>(n));
        for (int i = 0; i < n; i++)
        {
            dp[i][i]=1;
            if(i < n-1 && s[i] == s[i+1]){
                dp[i][i+1] = 1;
                maxlen = 2;
                start=i;
            }
        }
        for(int len = 3; len <= n;len++){
            for (int i = 0; i + len -1 < n; i++)
            {
                int j = i + len -1;
                if(s[i] == s[j] && dp[i+1][j-1] ==1){
                    dp[i][j]=1;
                    maxlen=len;
                    start=i;
                }
            }
            
        }
        return s.substr(start, maxlen);
    }
};
```

#### [6. Z 字形变换](https://leetcode-cn.com/problems/zigzag-conversion/)

思路：按照行读取，一个小勾为一个单元，规律如下：

![image-20200328112017504](assets/image-20200328112017504.png)

```cpp
class Solution
{
public:
    string convert(string s, int numRows)
    {
        if (numRows == 1)
            return s;
        int n = s.size();
        int steps = 2 * (numRows - 1);
        string res;
        for (int i = 0; i < numRows; i++)
        {
            for (int j = 0; j + i < n; j += steps)
            {
                res += s[j + i];
                if (i != 0 && i != numRows - 1 && j + steps - i < n)
                    res += s[j + steps - i];
            }
        }
        return res;
    }
};
```

#### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

思路：注意溢出

```cpp
class Solution {
public:
    int reverse(int x) {
        int res=0;
        while (x != 0)
        {
            int pop = x%10;
            x /= 10;
            if (res > INT_MAX/10 || (res == INT_MAX / 10 && pop > 7))
                return 0;
            if (res < INT_MIN/10 || (res == INT_MIN / 10 && pop < -8)) 
                return 0;
            res = res * 10 + pop;
        }
        return res;
    }
};
```

#### [8. 字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

```cpp
class Solution
{
public:
    int myAtoi(string str)
    {
        int flag = 1;
        int res = 0;
        int idx = 0;
        int len = str.length();
        while (str[idx] == ' ')
        {
            idx++;
        }
        if (str[idx] == '-')
            flag = -1;
        if(str[idx] == '-' || str[idx] == '+')
            idx++;
        while (idx < len && isdigit(str[idx]))
        {
            int tmp = str[idx] - '0';
            if (res > INT_MAX / 10 || (res == INT_MAX / 10 && tmp > 7)) { 
                return flag > 0 ? INT_MAX : INT_MIN;
            }
            res = res * 10 + tmp;
            idx++;
        }
        return flag * res;
    }
};
```

#### [9. 回文数](https://leetcode-cn.com/problems/palindrome-number/)

思路：

* 小于0的都不是回文数，能被10整除（0除外）都不是回文数，0到9都是回文数
* 计算后半段翻转后的数字，若与前半段相等（数的长度为偶数），或者是前半段的数的10倍（数的长度为奇数），则返回true；

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0 || (x % 10 == 0 && x != 0))
            return false;
        if( x > 0 && x < 9)
            return true;
        int reversedNum = 0;
        while (x  > reversedNum)
        {
            reversedNum = reversedNum * 10 + x % 10;
            x /= 10;
        }
        return x == reversedNum || x == reversedNum / 10;
    }
};
```

