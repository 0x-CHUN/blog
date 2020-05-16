# 查　　找

## 符号表

符号表最主要的目的就是将一个键和一个值联系起来。

> 符号表是一种存储键值对的数据结构，支持两种操作：插入（put），即将一组新的键值对存入表中；查找（get），即根据给定的键得到相应的值。

### API

一种简单的泛型符号表 API:

| 方法名                       | 描述                                               |
| ---------------------------- | -------------------------------------------------- |
| ST()                         | 创建一张符号表                                     |
| void put(Key key, Value val) | 将键值对存入表中（若值为空则将键 key 从表中删除）  |
| Value get(Key key)           | 获取键 key 对应的值（若键 key 不存在则返回 null ） |
| void delete(Key key)         | 从表中删去键 key （及其对应的值）                  |
| boolean contains(Key key)    | 键 key 在表中是否有对应的值                        |
| boolean isEmpty()            | 表是否为空                                         |
| int size()                   | 表中的键值对数量                                   |
| Iterable<Key> keys()         | 表中的所有键的集合                                 |

## 二叉查找树

一棵二叉查找树（BST）是一棵二叉树，其中每个结点都含有一个 Comparable 的键（以及相关联的值）且每个结点的键都大于其左子树中的任意结点的键而小于右子树的任意结点的键。

### 基本实现

定义：

```java
public class BST<Key extends Comparable<Key>, Value>{
    private Node root; // 二叉查找树的根结点
    private class Node{
        private Key key; // 键
        private Value val; // 值
        private Node left, right; // 指向子树的链接
        private int N; // 以该结点为根的子树中的结点总数
        public Node(Key key, Value val, int N){ 
            this.key = key; this.val = val;this.N = N; }
    }
    public int size()	{ return size(root); }
    private int size(Node x)
    {
        if (x == null) return 0;
        else return x.N;
    }
}
```

查找和排序方法的实现：

```java
public Value get(Key key){
    return get(root, key);
}
private Value get(Node x, Key key){ 
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp < 0) return get(x.left, key);
    else if (cmp > 0) return get(x.right, key);
    else return x.val;
}
public void put(Key key, Value val){ 
    root = put(root, key, val);
}
private Node put(Node x, Key key, Value val){
    if (x == null) return new Node(key, val, 1);
    int cmp = key.compareTo(x.key);
    if (cmp < 0) x.left = put(x.left, key, val);
    else if (cmp > 0) x.right = put(x.right, key, val);
    else x.val = val;
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}
```

二叉查找树中的选择操作：

```java
public Key min(){
    return min(root).key;
}
private Node min(Node x){
	if (x.left == null) return x;
	return min(x.left);
}
public Key floor(Key key){
	Node x = floor(root, key);
	if (x == null) return null;
	return x.key;
}
private Node floor(Node x, Key key){
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp == 0) return x;
    if (cmp < 0) return floor(x.left, key);
    Node t = floor(x.right, key);
    if (t != null) return t;
    else return x;
}
```

二叉查找树中 select() 和 rank() 方法的实现:

```java
public Key select(int k){
	return select(root, k).key;
}
private Node select(Node x, int k){
    if (x == null) return null;
    int t = size(x.left);
    if (t > k) return select(x.left, k);
    else if (t < k) return select(x.right, k-t-1);
    else return x;
}
public int rank(Key key){
    return rank(key, root); 
}
private int rank(Key key, Node x){
    if (x == null) return 0;
    int cmp = key.compareTo(x.key);
    if (cmp < 0) return rank(key, x.left);
    else if (cmp > 0) return 1 + size(x.left) + rank(key, x.right);
    else return size(x.left);
}
```

删除最大键和删除最小键

```java
public void deleteMin(){
	root = deleteMin(root);
}
private Node deleteMin(Node x){
	if (x.left == null) return x.right;
	x.left = deleteMin(x.left);
	x.N = size(x.left) + size(x.right) + 1;
	return x;
}
public void delete(Key key){
    root = delete(root, key); 
}
private Node delete(Node x, Key key){
	if (x == null) return null;
	int cmp = key.compareTo(x.key);
	if (cmp < 0) x.left = delete(x.left, key);
	else if (cmp > 0) x.right = delete(x.right, key);
    else{
        if (x.right == null) return x.left;
        if (x.left == null) return x.right;
        Node t = x;
        x = min(t.right);
        x.right = deleteMin(t.right);
        x.left = t.left;
    }
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}
```

二叉查找树的范围查找操作：

```java
public Iterable<Key> keys(){
    return keys(min(), max()); 
}
public Iterable<Key> keys(Key lo, Key hi){
	Queue<Key> queue = new Queue<Key>();
	keys(root, queue, lo, hi);
	return queue;
}
private void keys(Node x, Queue<Key> queue, Key lo, Key hi){
    if (x == null) return;
    int cmplo = lo.compareTo(x.key);
    int cmphi = hi.compareTo(x.key);
    if (cmplo < 0) keys(x.left, queue, lo, hi);
	if (cmplo <= 0 && cmphi >= 0) queue.enqueue(x.key);
	if (cmphi > 0) keys(x.right, queue, lo, hi);
}
```

## 平衡查找树

### 2-3 查找树

一棵 2-3 查找树或为一棵空树，或由以下结点组成：

* 2- 结点，含有一个键（及其对应的值）和两条链接，左链接指向的 2-3 树中的键都小于
  该结点，右链接指向的 2-3 树中的键都大于该结点。
* 3- 结点，含有两个键（及其对应的值）和三条链接，左链接指向的 2-3 树中的键都小
  于该结点，中链接指向的 2-3 树中的键都位于该结点的两个键之间，右链接指向的 2-3
  树中的键都大于该结点。

一棵完美平衡的 2-3 查找树中的所有空链接到根结点的距离都应该是相同的。

### 红黑二叉查找树

红黑二叉查找树背后的基本思想是用标准的二叉查找树和一些额外的信息来表示 2-3树。将树中的链接分为两种类型：

* 红链接将两个 2- 结点连接起来构成一个 3- 结点
* 黑链接则是 2-3 树中的普通链接。

红黑树的另一种定义是含有红黑链接并满足下列条件的二叉查找树：

* 红链接均为左链接；
* 没有任何一个结点同时和两条红链接相连；
* 该树是完美黑色平衡的，即任意空链接到根结点的路径上的黑链接数量相同。

```java
private static final boolean RED = true;
private static final boolean BLACK = false;
private class Node{
	Key key; // 键
	Value val; // 相关联的值
	Node left, right; // 左右子树
	int N; // 这棵子树中的结点总数
	boolean color; // 由其父结点指向它的链接的颜色
    Node(Key key, Value val, int N, boolean color){
        this.key = key;
    	this.val = val;
    	this.N = N;
    	this.color = color;
    }
}
private boolean isRed(Node x){
	if (x == null) return false;
	return x.color == RED;
}
```

在实现的某些操作中可能会出现红色右链接或者两条连续的红链接，但在操作完成前这些情况都会被小心地旋转并修复。旋转操作会改变红链接的指向。首先，假设有一条红色的右链接需要被转化为左链接。这个操作叫做左旋转，它对应的方法接受一条指向红黑树中的某个结点的链接作为参数。假设被指向的结点的右链接是红色的，这个方法会对树进行必要的调整并返回一个指向包含同一组键的子树且其左链接为红色的根结点的链接。

```java
Node rotateLeft(Node h){
	Node x = h.right;
    h.right = x.left;
	x.left = h;
	x.color = h.color;
	h.color = RED;
	x.N = h.N;
	h.N = 1 + size(h.left)+ size(h.right);
	return x;
}
```

```java
Node rotateRight(Node h){
	Node x = h.left;
	h.left = x.right;
	x.right = h;
	x.color = h.color;
	h.color = RED;
	x.N = h.N;
    h.N = 1 + size(h.left) + size(h.right);
	return x;
}
```

在插入新的键时可以使用旋转操作帮助保证 2-3 树和红黑树之间的一一对应关系，因为旋转操作可以保持红黑树的两个重要性质：有序性和完美平衡性。

```java
public class RedBlackBST<Key extends Comparable<Key>, Value>{
	private Node root;
	private class Node;
	private boolean isRed(Node h){};
    private Node rotateLeft(Node h) {};
    private Node rotateRight(Node h) {};
    private void flipColors(Node h){
		h.color = RED;
		h.left.color = BLACK;
		h.right.color = BLACK;
    };
    private int size() {};
    public void put(Key key, Value val){ 
        root = put(root, key, val);
        root.color = BLACK;
    }
    private Node put(Node h, Key key, Value val){
        if (h == null)
            return new Node(key, val, 1, RED);
        int cmp = key.compareTo(h.key);
        if (cmp < 0) h.left = put(h.left, key, val);
        else if (cmp > 0) h.right = put(h.right, key, val);
        else h.val = val;
        if (isRed(h.right) && !isRed(h.left)) h = rotateLeft(h);
        if (isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);
        if (isRed(h.left) && isRed(h.right)) flipColors(h);
        h.N = size(h.left) + size(h.right) + 1;
        return h;
    }
}
```

## 散列表

基于拉链法的散列表

```java
public class SeparateChainingHashST<Key, Value>{
	private int N; // 键值对总数
	private int M; // 散列表的大小
	private SequentialSearchST<Key, Value>[] st; // 存放链表对象的数组
	public SeparateChainingHashST(){ this(997); }
    public SeparateChainingHashST(int M){ // 创建M条链表
        this.M = M;
        st = (SequentialSearchST<Key, Value>[]) new SequentialSearchST[M];
        for (int i = 0; i < M; i++)
            st[i] = new SequentialSearchST();
    }
	private int hash(Key key){ return (key.hashCode() & 0x7fffffff) % M; }
    public Value get(Key key){ return (Value) st[hash(key)].get(key); }
    public void put(Key key, Value val){ st[hash(key)].put(key, val); }
    public Iterable<Key> keys()
}
```

基于线性探测法的散列表

```java
public class LinearProbingHashST<Key, Value>{
	private int N; // 符号表中键值对的总数
	private int M = 16; // 线性探测表的大小
	private Key[] keys; // 键
	private Value[] vals; // 值
	public LinearProbingHashST(){
		keys = (Key[]) new Object[M];
        vals = (Value[]) new Object[M];
    }
    private int hash(Key key){ return (key.hashCode() & 0x7fffffff) % M; }
    private void resize()
	public void put(Key key, Value val){
		if (N >= M/2) resize(2*M); // 将M加倍（请见正文）
		int i;
		for (i = hash(key); keys[i] != null; i = (i + 1) % M)
			if (keys[i].equals(key)) {
                vals[i] = val; return; 
            }
        keys[i] = key;
        vals[i] = val;
        N++;
    }
    public Value get(Key key){
        for (int i = hash(key); keys[i] != null; i = (i + 1) % M)
            if (keys[i].equals(key))
                return vals[i];
        return null;
    }
    public void delete(Key key){
        if (!contains(key)) return;
        int i = hash(key);
        while (!key.equals(keys[i]))
            i = (i + 1) % M;
        keys[i] = null;
        vals[i] = null;
        i = (i + 1) % M;
        while (keys[i] != null){
			Key keyToRedo = keys[i];
			Value valToRedo = vals[i];
			keys[i] = null;
			vals[i] = null;
            N--;
            put(keyToRedo, valToRedo);
            i = (i + 1) % M;
        }
        N--;
        if (N > 0 && N == M/8) resize(M/2);
    }
}
```

