# 数据结构

## 基础

时间复杂度：算法中所有语句执行的次数

两种规则：

1. 加法：$T(n)=T_{1}(n)+T_{2}(n)=O(f(n))+O(g(n))=O(max(f(n),g(n)))$
2. 乘法：$T(n)=T_{1}(n)\cdot T_{2}(n)=O(f(n))\cdot O(g(n))=O(f(n)\cdot g(n))$

递归的时间复杂度取决于递归深度；

空间复杂度：运行时所用的辅助空间的大小；

## 线性表

线性表：

* 顺序表：类比数组
* 链表

### 顺序表

顺序表：一组**连续的**存储单元，一次存储，因此**逻辑上、物理上都相邻**，因此可以实现**随机存取**；

缺点：

* 插入、删除需移动大量元素
* 分配需要一段连续的空间

插入：

```cpp
// 位置i 插入 e
for(int j = L.length(); j >= i; j--)
    L.data[j] = L.data[j-1];
L.data[i-1] = e;
L.length++;
// 时间复杂度为O(n)
```

删除：

```cpp
// 删除位置i元素
for(int j = i;j < L.length(); j++)
    L.data[j-1] = L.data[j];
L.length--;
// 时间复杂度为O(n)
```

查找：

* 按下标查找：时间复杂度为O(1)；
* 按值查找：时间复杂度为O(n)；

### 链表

定义：

```cpp
struct node{
    elemtype data;
    node* next;
}
```

建立：

* 头插法：新节点插入表头

  ```cpp
  newNode->next = L->next;//L为表头
  L->next = newNode;
  ```

* 尾插法：新节点插入表尾

  ```cpp
  r->next = newNode;// r为尾指针
  r = newNode;
  ```

插入：

```cpp
// 在i插入新节点
node* p = get(i-1); // 获取前驱节点
newNode->next = p->next;
p->next = newNode;
```

删除：

```cpp
// 删除节点i
node* p = get(i-1); // 获取前驱节点
node* delNode = p->next;
p->next = delNode->next;
free(delNode);
```

求表长：

```cpp
int length(node* head){
    if(head == nullptr) return 0;
    int lens = 0;
    while(head != nullptr){
        head = head->next;
        lens++;
    }
    retrun lens;
}
```

### 栈和队列

操作受限的线性表；

栈：只允许一端进行插入、删除

队列：只允许一端插入，另一端删除

循环队列：

![image-20200326153032518](assets/image-20200326153032518.png)

双端队列：

![image-20200326155024216](assets/image-20200326155024216.png)

应用

队列：层次遍历、缓冲区、内存管理的就绪队列

栈：数制转换、括号匹配、表达式求值

### 特殊矩阵压缩存储

二维数组：

* 行优先
* 列优先

对称矩阵：$A[n][n]$可以存入$B[\frac{n(n+1)}{2}]$中

三角矩阵：

* $\left[\begin{array}{}a_{11}\\ \vdots & \ddots &\\ a_{n1} & \cdots & a_{nn}\end{array}\right]$

  

* $\left[\begin{array}{}a_{11} & \dots & a_{1 n} \\ & \ddots & \vdots \\ & & a_{n n}\end{array}\right]$

## 树与二叉树

节点的度：树中一个节点的子节点的数量

树的度：树中节点的最大度数

分支节点：度大于0的节点

叶子节点：度为0的节点

节点的深度：从上到下的层数

树的高度或深度：树中节点的最大层数

路径：两个节点之间所经过的节点序列

路径长度：路径上所经过的边的条数

性质：`节点总数 = 总度数 + 1`

### 二叉树

* 度为2

* 满二叉树：每一层都充满了

  完全二叉树：编号与节点次序一一对应

完全二叉树的性质：

1. $i \leq \left \lfloor \frac{n}{2} \right \rfloor$，则节点i为分支节点
2. $i > \left \lfloor \frac{n}{2} \right \rfloor$，则节点i为叶子节点
3. 叶子节点只可能出现在最后一层
4. 若存在度为1的节点，则只存在一个，且只存在左孩子，不存在右孩子；
5. 编号后，若节点i为叶子节点，则j>i的节点i均为节点；
6. 若n为奇数，则每个分支节点都有左右子节点；若n为偶数，则编号最大的分支节点为$\frac{n}{2}$，且只有左节点，其他均有左右子节点；
7. 节点i所在层数：$\left \lfloor log_{2} i \right \rfloor + 1$
8. n个节点的树的高度$h = \left \lceil log_{2}(n+1) \right \rceil$或$h = \left \lfloor log_{2} n \right \rfloor + 1$

遍历：

* 先序：根左右

  ```cpp
  void preorder(node* root){
      if(root == nullptr)	return;
      // 处理根
      preorder(root->left);
      preorder(root->right);
  }
  ```

* 中序：左右根

  ```cpp
  void inorder(node* root){
      if(root == nullptr)	return;
      inorder(roo->left);
      //处理根
      inorder(root->right);
  }
  ```

* 后序：左右根

  ```cpp
  void postorder(node* root){
      if(root == nullptr) return;
      postorder(root->left);
      postorder(root->right);
      // 处理根
  }
  ```

* 层次遍历

  ```cpp
  void levelorder(node* root){
      if(root == nullptr) return;
      queue<node*> q;
      q.push(root);
      while(!q.empty()){
          int size = q.size();
          for(int i = 0; i < size; i++){
              node* tmp = q.front();
              q.pop();
              // 处理tmp
              if(tmp->left) q.push(tmp->left);
              if(tmp->right) q.push(tmp->right);
          }
      }
  }
  ```

只有`中序+先序、中序+后序、中序+层次`才可以唯一确定一棵树；

### 二叉排序树

定义：左子树节点 < 根节点，右子树节点 > 根节点（或全相反）

**中序遍历可得到递增有序序列！**

查找：

```cpp
bool find(node* root,elemtype key){
    if(root == nullptr)	return false;
    if(key == root)	return true;
    if(key > root->data)	return find(p->right, key);
    if(key < root->data)	return find(p->left, key);
}
```

平均时间：$O(log_{2}n)$

最坏时间：$O(n)$

插入：

```cpp
void insert(node* root,elemtype key){
    if(root == nullptr)	root = new node(key);
    else if(key == root->data)	return;
    else if(key < root->data) insert(root->left, key);
    else	insert(root->right, key);
}
```

删除：

* 若为叶节点，则直接删除

* 若左空，则用右填；若右空，则用左填；

* 若左右非空，则令X的中序的后继节点代替X，从二叉树中删除X；

  ![image-20200326171238269](assets/image-20200326171238269.png)

### 平衡二叉树

平衡因子：左右子树的高度差

平衡二叉树：任意节点的平衡因子小于等于1；

插入：

1. 像二叉树一样先插入节点

2. 不平衡则进行旋转

   **LL旋转：**

   ![image-20200326171543745](assets/image-20200326171543745.png)

   **RR旋转：**

   ![image-20200326171610574](assets/image-20200326171610574.png)

   **LR旋转**：先RR旋转，然后LL旋转

   ![image-20200326171652627](assets/image-20200326171652627.png)**RL旋转**：先LL旋转，然后RR旋转

   ![image-20200326171757505](assets/image-20200326171757505.png)

树转换为二叉树：森林中每个节点，左指针指向第一个孩子节点，右节点指向相邻的兄弟节点；

森林转换为二叉树：每棵树转换为二叉树，然后根部相连；

### 线索二叉树

二叉树线索化，便于直接查找节点的前驱和后继；

结构：

```cpp
// 前驱、后继由遍历方式决定，如前序、中序、后序线索二叉树
struct node{
    int ltag;// 为0时，lchild指向左孩子；为1时，lchild指向前驱
    node* lchild;
    int rtag;// 为0时，rchild指向右孩子；为1时，rchild指向后继
    node* rchild;
}
```

![image-20200326172558759](assets/image-20200326172558759.png)

### Huffman树

$WPL = \sum_{i=1}^{n} w_{i}l_{i}$

Huffman树：WPL最小的二叉树（不唯一）；

构造方式：

1. N个节点作为N个二叉树
2. 每次选择根节点权值最小的2个二叉树合并，一直重复到只有一个二叉树

## 图

* 边：顶点的无序对$(v,w)$

* 弧：顶点的有序对$<v,w>$

* 无向图：边的集合

* 有向图：弧的集合

* 度：以该顶点为一个端点的边的数目

  无向图的顶点v的度记为TD(v)

  有向图：

  * 入度：以顶点v为终点的边的数目，记为ID(v)
  * 出度：以顶点v为起点的边的数目，记为OD(v)
  * TD(v) = ID(v) + OD(v)

* 连通：从v到w有路径，则称v，w是连通的

* 连通图：任意2个节点之间是连通的

* 连通分量：无向图的极大连通子图

* 强连通：有向图中从v到w，从w到v都有路径，则称v、w强连通；

* 强连通图：任意2个节点是强连通的

* 强连通分量：有向图的极大强连通子图

* 完全图：

  * 无向图：任意2个点之间存在边，边数：$\frac{n(n-1)}{2}$
  * 有向图：任意2个顶点之间存在方向相反的2条边，边数：$n(n-1)$

* 子图：$G=(V,E),G^{'}=(V^{'},E^{'})$，若$V^{'}$是$V$的子集，$E^{'}$是$E$的子集，则$G^{'}$为$G$的子图；

* 生成树：连通图的生成树是包含图中全部顶点的极小连通子图；

* 生成森林：非连通图中连通分量的生成树组成的生成森林；

* 路径：顶点p到q的顶点序列

* 路径长度：顶点p到q的顶点序列的长度

* 简单路径：路径序列中顶点不重复出现

* 稠密图：边数较多（一般判断准则：$|E| < |V|log|V|$

* 稀疏图：变数较少

### 图的存储

1. 邻接矩阵：存储在二维矩阵$A[n][n]$中

   $A[i][j] = \left\{\begin{matrix}
   & 1, \ i,j相连\\ 
   & 0, \ i,j不相连
   \end{matrix}\right.$

   适用于稠密图

   优点：判断相连快

   缺点：统计边数代价大

2. 邻接表法：适用于稀疏图

   ![image-20200326175521178](assets/image-20200326175521178.png)

### 图的遍历

BFS（广度优先）：

1. 访问起始点v
2. 依次访问未访问过的邻接顶点$w_i$
3. 从访问过的顶点出发，再访问它们所有未被访问过的邻接顶点
4. 另选一个未被访问过的顶点，重复上述步骤；

时间复杂度：

* 采用邻接矩阵：$O(|V|^2)$
* 采用邻接表：$O(|E|+|V|)$

空间复杂度：$O(|V|)$

DFS（深度优先）：

1. 访问起始点v
2. 从v出发，访问v的邻接顶点中未被访问过的顶点$w_1$，再访问$w_1$的邻接顶点中未被访问的顶点，依次类推
3. 不能继续向下访问时，退回到最近被访问的点，若有未访问的邻接顶点，则从该顶点重复上面的步骤，直到全部访问过；

时间复杂度：

* 采用邻接矩阵：$O(|V|^2)$
* 采用邻接表：$O(|E|+|V|)$

空间复杂度：$O(|V|)$

### 最小生成树

所有生成树中边的权值之和最小的那颗生成树；

性质：

* 不唯一
* 各边权值不相同，则生成树唯一
* 权值唯一

方法：

* prim

  ![image-20200326181426090](assets/image-20200326181426090.png)

* kruskal：

  ![image-20200326181458529](assets/image-20200326181458529.png)

### 最短路径

* 单源最短路径
* 点对点最短路径

#### Dijkstra

1. S为已经求得最短路径的顶点，初始化为{0}
2. 从顶点V-S中选出$V_j$，满足$dist[j]=min\{ dist[i] \ | V_i \in V-S\}$，$V_j$为当前求得的一条从$V_0$出发的最短路径的终点。令$S = S \cup \{j\}$
3. 修改从$V_0$出发到集合$V-S$上任一顶点$V_k$可达的最短路径：$dist[k] = min(dist[j] + arcs[j][k],dist[k])$;
4. 重复2-3共n-1次；

![image-20200328145526398](assets/image-20200328145526398.png)

#### Floyd

实际上是遍历判断$Dis(i,k) + Dis(k,j) < Dis(i,j)$

1. 计算计算矩阵$A^{-1},A^{0},A^{1},...,A^{n-1}$
2. $A^{-1} = arcs$
3. $A^{k}[i][j] = min(A^{k-1}[i][j],A^{k-1}[i][k]+A^{k-1}[k][j])$，其中$A^{k}[i][j]$代表从i到j序号个数小于k的路径长度。

Floyd可以解负权值边的图，但是不能形成环路。时间复杂度：$O(|V|^3)$

### 拓扑排序

有向无环图的一个顶点组成的序列称为拓扑排序；

条件：

* 每个顶点出现且只出现一次
* A在拓扑排序中排在B前，则不存在B到A的路径

拓扑排序：

1. 从图中选择一个没有前驱的顶点并输出
2. 从图中删除该顶点和所有以它为起点的有向边
3. 重复1-2直到空或不存在无前驱的顶点（这说明图存在环）；

![image-20200328150832048](assets/image-20200328150832048.png)

### 关键路径

AOE网：带权有向图以顶点表示事件，有向边表示活动，权值表示开销；

关键路径：AOE网中从源到汇点的最大路径长度；

关键活动：关键路径上的活动；

求关键路径：

1. 求最早发生时间：$V_{e}(k) = max(V_e(j) + weight(V_j,V_k))$
2. 求最迟发生时间：$V_l(k)=min(V_l(k)-weight(V_j,V_k))$
3. 活动$a_i$的最早开始时间：$e(i)=V_e(k)$
4. 活动$a_j$的最迟开始时间：$l(j)=V_l(j)-weight(V_k,V_j)$
5. 求$d(i) = l(i)-e(i)$，找差值为0的顶点构成关键路径；

![image-20200328152432040](assets/image-20200328152432040.png)

![image-20200328152453669](assets/image-20200328152453669.png)

## 查找

顺序查找：平均查找长度$ASL = \sum_{i=1}^{n}P_i(n-i+1)=\frac{n+1}{2}$，时间复杂度：$O(n)$

折半查找：仅适用于有序的顺序表

```cpp
int binarySearch(int[] arr,int key){
    int low = 0,high = arr.length() - 1, mid;
    while(low < high){
        mid = (high - low) / 2 + low; // 等同于(low+high)/2，但是不溢出，这个加法会溢出
        if(arr[mid] == key) return mid;
        else if(arr[mid] > key) high = mid;
        else low = mid + 1;
    }
    return low;
}
```

$ASL = \frac{n+1}{n}log_2(n+1)-1 \approx log_2(n+1)-1$

时间复杂度：$O(log_2n)$

### B树

对于m阶的B树：

* 若根节点不是终端节点，则至少有两颗子树，至多m棵子树

* 除根节点以外的所有非叶节点至少有$\lceil \frac{m}{2} \rceil$棵子树，至多m棵子树

* 所有的叶节点都出现在同一层次上，并且不带信息

* 非叶节点结构：$[n | p_o | k_0 | p_1 | k_1 | ...|k_n|p_n]$

  其中：

  * $p_{i-1}$所指的子树中所有节点的关键字均小于$k_i$
  * $p_i$所指子树中所有节点的关键字均大于$k_i$

#### 插入

1. 定位：类似于二叉查找树
2. 插入：插入后节点的关键字个数大于$m-1$，则需分裂：选择中间($\lceil \frac{m}{2} \rceil$)处的节点插入父节点；

![image-20200328155438732](assets/image-20200328155438732.png)

#### 删除

* 若为非终端节点（最底层非叶节点），设关键字为k
  1. 左子树的关键字个数$> \lceil \frac{m}{2} \rceil-1$，则找前驱$k^{'}$代替k
  2. 右子树的关键字个数$> \lceil \frac{m}{2} \rceil-1$，则找后继$k^{'}$代替k
  3. 若左右子树的关键字个数$= \lceil \frac{m}{2} \rceil-1$，则合并两个合并后直接删除k
* 若为终端节点：
  1. 若所在节点的关键字个数$> \lceil \frac{m}{2} \rceil-1$，则直接删除
  2. 若所在节点的关键字个数$= \lceil \frac{m}{2} \rceil-1$
     1. 兄弟够借（左右中有关键字$> \lceil \frac{m}{2} \rceil-1$，则找前、后继
     2. 不够借，则删除后与左（右）以及双亲节点的1个关键字合并

### B+树

定义：

1. 分支节点最多为m棵子树
2. 非叶节点至少有2棵子树，其他每个分支节点至少$\lceil \frac{m}{2} \rceil$棵子树
3. 节点的子树个数与关键字个数相等
4. 所有叶节点包含全部关键字及指向记录的指针，按关键字大小顺序排列，且相邻的叶节点用指针相连
5. 分支节点仅用于索引

### 散列表

定义：建立关键字与位置之间的映射关系；

散列函数：关键字与位置之间的映射关系（Hash（key）=address）

冲突：2个及以上的不同关键字映射到同一地址上（同义词）

常用散列函数：

1. 直接定址法：$H(key) = a \cdot key + b$
2. 除留余数法：$H(key) = key \% p$（p：小于散列表的最大质数）
3. 数字分析法：平方取中法、折叠法

冲突处理方法：

1. 开放地址法：可存放新表项的空闲地址既向它的同义词开放又向非同义词开放

   $H_i=(H(key)+d_i) \% m $

   1.  线性探测：$d_i=0,1,3,...,m-1$，易聚集(m必须可表示为4k+3的质数)
   2. 平方探测：$d_i=0,1^2,-1^2,...,k^2,-k^2$
   3. 再散列法：$H_i=(H(key)+i\cdot hash_2(key)) \% m$
   4. 伪随机法

2. 拉链法：冲突的关键字用链表链接

装填因子：$α = \frac{表中记录数}{散列表长度m}$

### KMP

![image-20200329145233650](assets/image-20200329145233650.png)

```cpp
void getnext(string p,int next[]){
    int p_len = p.size();
    int i = 0, j = -1;
    next[0] = -1;
    while(i < p_len - 1){
        if(j == -1 || p[i] == p[j]){
            i++;j++;
            next[i] = j;
        }
        else j = next[j];
    }
}

int KMP(string s,stirng p,int next[]){
    getnext(p,next);
    int i = 0, j = 0;
    int s_len = s.size(), p_len = p.size();
    while(i < s_len && j < p_len){
        if(j == -1 || s[i] == p[j]){
            i++;j++;
        }
        else	j = next[j];
    }
    if(j == p_len)	return i - j;
    return -1;
}
```

![image-20200329145948523](assets/image-20200329145948523.png)

## 排序

### 直接插入排序

将待排序的序列中的元素插入到前面已经排好序的子序列中，直到全部排好序；

```cpp
void insertSort(vector<int>& v){
    for(int i = 1; i < v.size(); i++){
        int val = v[i];
        int j = i;
        while(j > 0 && v[j-1] > val){
            v[j] = v[j-1];
            j--;
        }
        v[j] = val;
    }
}
```

