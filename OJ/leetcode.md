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

