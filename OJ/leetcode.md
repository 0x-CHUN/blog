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

思路：因为为有序数组，且时间复杂度为$O(log(m+n))$，则一般为二分查找；[]()