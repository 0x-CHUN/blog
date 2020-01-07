# 链表

## [两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

思路：

* 短的链表后添加0，然后一个一个相加，注意进位

  ```c++
  class Solution {
  public:
  	ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
  		int Len1 = 0, Len2 = 0;
  		ListNode* p1 = l1;
  		ListNode* p2 = l2;
  		while (p1->next!=nullptr)
  		{
  			Len1++;
  			p1 = p1->next;
  		}
  		while (p2->next!=nullptr)
  		{
  			Len2++;
  			p2 = p2->next;
  		}
  		if (Len1 > Len2) {//L1长度大于于L2
  			for (int i = 0; i < Len1-Len2; i++)
  			{//L2后添加
  				p2->next = new ListNode(0);
  				p2 = p2->next;
  			}
  		}
  		else{//L1长度小于L2
  			for (int i = 0; i < Len2-Len1; i++)
  			{//L1后添加
  				p1->next = new ListNode(0);
  				p1 = p1->next;
  			}
  		}
  		ListNode* res = new ListNode(-1);
  		int carry = 0, sum = 0;
  		p1 = l1, p2 = l2;
  		ListNode* p3 = res;
  		while (p1!=nullptr&&p2!=nullptr)
  		{
  			sum = carry + p1->val + p2->val;
  			carry = sum / 10;
  			sum = sum % 10;
  			p3->next = new ListNode(sum);
  			p3 = p3->next;
  			p1 = p1->next;
  			p2 = p2->next;
  		}
  		if (carry != 0) {
  			p3->next = new ListNode(1);
  		}
  		return res->next;
  	}
  };
  ```


## [删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

思路：

* 设置两个指针p、q，p先移动N个节点，然后p、q一起移动，直到p移动到null；

  ![img](linkedlist.assets/cc43daa8cbb755373ce4c5cd10c44066dc770a34a6d2913a52f8047cbf5e6e56-file_1559548337458.gif)

  ```c++
  class Solution {
  public:
  	ListNode* removeNthFromEnd(ListNode* head, int n) {
  		ListNode* dummyHead = new ListNode(0);
  		dummyHead->next = head;
  		ListNode* p = dummyHead;
  		ListNode* q = dummyHead;
  		for (int i = 0; i < n+1; i++)
  		{
  			p = p->next;
  		}
  		while (p!=nullptr)
  		{
  			p = p->next;
  			q = q->next;
  		}
  		ListNode* delNode = q->next;
  		q->next = delNode->next;
  		delete delNode;
  		ListNode* res = dummyHead->next;
  		delete dummyHead;
  		return res;
  	}
  };
  ```

## [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

思路：

* ​	逐一插入

  ```c++
  class Solution {
  public:
  	ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
  		ListNode* preHead = new ListNode(-1);
  		ListNode* p = preHead;
  		while (l1 != nullptr && l2 != nullptr)
  		{
  			if (l1->val <= l2->val) {
  				p->next = l1;
  				l1 = l1->next;
  			}
  			else {
  				p->next = l2;
  				l2 = l2->next;
  			}
  			p = p->next;
  		}
  		if (l1 != nullptr)
  			p->next = l1;
  		else
  			p->next = l2;
  		return preHead->next;
  	}
  };
  ```

* 递归

  $\left\{\begin{array}{ll}{\text { list } 1[0]+\text { merge (list1[1: ], list 2 ) }} & {\text { list1 }[0]<\text { list } 2[0]} \\ {\text { list2 }[0]+\text { merge (list1, list2[1: ]) }} & {\text { otherwise }}\end{array}\right.$

  ```c++
  class Solution {
  public:
  	ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
  		if (l1 == nullptr) {
  			return l2;
  		}
  		else if(l2 == nullptr){
  			return l1;
  		}
  		else if (l1->val < l2->val) {
  			l1->next = mergeTwoLists(l1->next, l2);
  			return l1;
  		}
  		else {
  			l2->next = mergeTwoLists(l1, l2->next);
  			return l2;
  		}
  	}
  };
  ```


## [合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

思路：

* 暴力解肯定不行，类比快排的思想，归并即可：

```c++
class Solution {
public:
	ListNode* merge(vector<ListNode*>& lists, int left, int right) {
		if (right < left)
			return nullptr;
		if (right == left)
			return lists[left];
		int mid = (left + right) / 2;
		ListNode* p = merge(lists, left, mid);
		ListNode* q = merge(lists, mid + 1, right);
		ListNode* dummyHead = new ListNode(-1);
		ListNode* cur = dummyHead;
		while (p != nullptr && q != nullptr)
		{
			if (p->val < q->val) {
				cur->next = p;
				p = p->next;
			}
			else {
				cur->next = q;
				q = q->next;
			}
			cur = cur->next;
		}
		if (p != nullptr)
			cur->next = p;
		else
			cur->next = q;
		return dummyHead->next;
	}
	ListNode* mergeKLists(vector<ListNode*>& lists) {
		return merge(lists, 0, lists.size() - 1);
	}
};
```

## [两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

思路：

* 按照题目要求即可

  ```c++
  class Solution {
  public:
  	ListNode* swapPairs(ListNode* head) {
  		ListNode* dummyHead = new ListNode(-1);
  		dummyHead->next = head;
  		ListNode* cur = dummyHead;
  		ListNode* p;
  		ListNode* q;
  		while (cur->next!=nullptr && cur->next->next!=nullptr)
  		{
  			p = cur->next;
  			q = cur->next->next;
  			cur->next = q;
  			p->next = q->next;
  			q->next = p;
  			cur = p;
  		}
  		return dummyHead->next;
  	}
  };
  ```

## [旋转链表](https://leetcode-cn.com/problems/rotate-list/)

思路:

* 遍历链表，保存长度

* 找到分割点，然后构建循环链表

* 在分割点分割，变单链表

  ```c++
  class Solution {
  public:
  	ListNode* rotateRight(ListNode* head, int k) {
  		if (head == nullptr)
  			return nullptr;
  		ListNode* p1 = head;
  		int len = 1;
  		while (p1->next) {
  			p1 = p1->next;
  			len++;
  		}
  		p1->next = head;
  		k = k % len;
  		for (int i = 1; i < len - k + 1; i++) {
  			p1 = p1->next;
  			head = head->next;
  		}
  		p1->next = nullptr;
  		return head;
  	}
  };
  ```


## [删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

思路：

* 设置一个dummyHead便于实现

* 遇到重复的就一直遍历直到出现不同

  ```c++
  class Solution {
  public:
  	ListNode* deleteDuplicates(ListNode* head) {
  		ListNode* dummyHead = new ListNode(-1);
  		dummyHead->next = head;
  		ListNode* pre = nullptr;
  		ListNode* cur = dummyHead;
  		while (cur)
  		{
  			pre = cur;
  			cur = cur->next;
  			while (cur && cur->next && cur->val == cur->next->val)
  			{
  				int tmp = cur->val;
  				while (cur && cur->val == tmp)
  				{
  					cur = cur->next;
  				}
  			}
  			pre->next = cur;
  		}
  		return dummyHead->next;
  	}
  };
  ```

## [删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

思路：

* 与上面一直

  ```c++
  class Solution {
  public:
  	ListNode* deleteDuplicates(ListNode* head) {
  		ListNode* cur = head;
  		ListNode* pre;
  		while (cur)
  		{
  			pre = cur;
  			while (cur && cur->val == pre->val) {
  				cur = cur->next;
  			}
  			pre->next = cur;
  		}
  		return head;
  	}
  };
  ```

## [分隔链表](https://leetcode-cn.com/problems/partition-list/)

思路：

* 设置两个指针，一个存储小于x的节点，一个存储大于等于x的节点

* 注意使用哑节点可减少判断

  ```c++
  class Solution {
  public:
  	ListNode* partition(ListNode* head, int x) {
  		ListNode* before = new ListNode(-1);
  		ListNode* after = new ListNode(-1);
  		ListNode* p = before;
  		ListNode* q = after;
  		while (head)
  		{
  			if (head->val < x) {
  				p->next = head;
  				p = p->next;
  			}
  			else {
  				q->next = head;
  				q = q->next;
  			}
  			head = head->next;
  		}
  		p->next = after->next;
  		q->next = nullptr;
  		return before->next;
  	}
  };
  ```

## [反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

思路：

* 先移动到第n个的前面，然后开始翻转

  ```c++
  class Solution {
  public:
  	ListNode* reverseBetween(ListNode* head, int m, int n) {
  		ListNode* dummyHead = new ListNode(-1);
  		dummyHead->next = head;
  		ListNode* pre = dummyHead;
  		for (int i = 0; i < m-1; i++)
  		{
  			pre = pre->next;
  		}
  		ListNode* cur = pre->next;
  		ListNode* tmp;
  		for (int i = m; i < n; i++)
  		{
  			tmp = cur->next;
  			cur->next = tmp->next;
  			tmp->next = pre->next;
  			pre->next = tmp;
  		}
  		return dummyHead->next;
  	}
  };
  ```

#### [有序链表转换二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

思路：（参考leetcode官方）

* 中序遍历最左边的元素一定是给定链表的头部，类似地下一个元素一定是链表的下一个元素，以此类推。这是肯定的因为给定的初始链表保证了升序排列。
* 遍历整个链表获得它的长度，我们用两个指针标记结果数组的开始和结束，记为 start 和 end，他们的初始值分别为 0 和 length - 1。
* 记住，我们当前需要模拟中序遍历，找到中间元素 (start + end) / 2。注意这里并不需要在链表中找到确定的元素是哪个，只需要用一个变量告诉我们中间元素的下标。我们只需要递归调用这两侧。
* 递归左半边，其中开始和结束的值分别为 start, mid - 1。
  在这个算法中，每当我们构建完二叉搜索树的左半部分时，链表中的头指针将指向根节点或中间节点（它成为根节点）。 因此，我们只需使用头指针指向的当前值作为根节点，并将指针后移一位，即 head = head.next。
* 递归右半部分 mid + 1, end。

```c++
class Solution {
public:
	ListNode* head;
	int getLens(ListNode* head) {
		ListNode* p = head;
		int res = 0;
		while (p != nullptr)
		{
			p = p->next;
			res++;
		}
		return res;
	}
	TreeNode* convert(int l,int r) {
		if (l > r)
			return nullptr;
		int mid = (l + r) / 2;
		TreeNode* left = convert(l, mid - 1);
		TreeNode* root = new TreeNode(this->head->val);
		root->left = left;
		this->head = this->head->next;
		root->right = this->convert(mid + 1, r);
		return root;
	}
	TreeNode* sortedListToBST(ListNode* head) {
		int lens = getLens(head);
		this->head = head;
		return convert(0,lens - 1);

	}
};
```

## [复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

思路：

* 先复制随机节点，其random先赋值为null

* 恢复random

* 断开新旧的节点

  ```c++
  class Solution {
  public:
  	Node* copyRandomList(Node* head) {
  		if (head == nullptr)
  			return nullptr;
  		Node* ptr = head;
  		while (ptr != nullptr)
  		{
  			Node* newNode = new Node(ptr->val,ptr->next,nullptr);
  			ptr->next = newNode;
  			ptr = newNode->next;
  		}
  		ptr = head;
  		while (ptr != nullptr)
  		{
  			if (ptr->random != nullptr)
  				ptr->next->random = ptr->random->next;
  			else
  				ptr->next->random = nullptr;
  			ptr = ptr->next->next;
  		}
  		Node* old_ptr = head;
  		Node* new_ptr = head->next;
  		Node* new_head = head->next;
  		while (old_ptr != nullptr)
  		{
  			old_ptr->next = old_ptr->next->next;
  			if (new_ptr->next != nullptr)
  				new_ptr->next = new_ptr->next->next;
  			else
  				new_ptr->next = nullptr;
  			old_ptr = old_ptr->next;
  			new_ptr = new_ptr->next;
  		}
  		return new_head;
  	}
  };
  ```

## [环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

思路：

* 快慢指针

  ```c++
  class Solution {
  public:
  	bool hasCycle(ListNode* head) {
  		if (head == nullptr || head->next == nullptr)
  			return false;
  		ListNode* slow = head;
  		ListNode* fast = head->next;
  		while (slow != fast)
  		{
  			if (fast == nullptr || fast->next == nullptr)
  				return false;
  			slow = slow->next;
  			fast = fast->next->next;
  		}
  		return true;
  	}
  };
  ```


## [环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

思路：

* 利用set保存已访问的节点

* 返回第一个重复的节点

  ```c++
  class Solution {
  public:
  	ListNode* detectCycle(ListNode* head) {
  		set<ListNode*> st;
  		ListNode* p = head;
  		while (p != nullptr)
  		{
  			if (st.find(p) != st.end()) {
  				return p;
  			}
  			st.insert(p);
  			p = p->next;
  		}
  		return nullptr;
  	}
  };
  ```

[快慢指针的方法（Floyd 算法）](https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/huan-xing-lian-biao-ii-by-leetcode/)

## [重排链表](https://leetcode-cn.com/problems/reorder-list/)

思路：

* 找到中点
* 翻转后半部分
* 连接前半部分和后半部分

```c++
class Solution {
public:
	void reorderList(ListNode* head) {
		if (head == nullptr)
			return;
		ListNode* slow = head;
		ListNode* fast = head;
		while (fast && fast->next) {
			slow = slow->next;
			fast = fast->next->next;
		}
		ListNode* preNode = nullptr;
		ListNode* p = slow->next;
		slow->next = nullptr;
		while (p) {
			ListNode* next = p->next;
			p->next = preNode;
			preNode = p;
			p = next;
		}
		ListNode* res = new ListNode(-1);
		while (preNode) {
			res->next = head;
			head = head->next;
			res = res->next;
			res->next = preNode;
			preNode = preNode->next;
			res = res->next;
		}
		res->next = head;
		head = res;
	}
};
```



## 总结

技巧：

1. 快慢指针适用于找链表中点或者进行尾部对齐
2. 哑节点（dummy head）有利于各种操作