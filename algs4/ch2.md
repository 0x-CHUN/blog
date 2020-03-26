# 排序

排序就是将一组对象按照某种逻辑顺序重新排列的过程。

## 排序算法类模板

```java
public class Example
{
	public static void sort(Comparable[] a)
    { /* 请见算法2.1、算法2.2、算法2.3、算法2.4、算法2.5或算法2.7*/ }
    private static boolean less(Comparable v, Comparable w)
    { return v.compareTo(w) < 0; }
    private static void exch(Comparable[] a, int i, int j)
    { Comparable t = a[i]; a[i] = a[j]; a[j] = t; }
    private static void show(Comparable[] a)
    { // 在单行中打印数组
        for (int i = 0; i < a.length; i++)
            StdOut.print(a[i] + " ");
        StdOut.println();
    }
    public static boolean isSorted(Comparable[] a)
    { // 测试数组元素是否有序
        for (int i = 1; i < a.length; i++)
            if (less(a[i], a[i-1])) return false;
        return true;
    }
}
```



## 初级排序算法

### 选择排序

步骤：

1. 找到最小的数
2. 与数组的第一个元素交换位置
3. 在剩下的数中找最小的数，与数组的第二个元素交换位置，以此类推

```java
public class Selection
{
    public static void sort(Comparable[] a)
    { // 将a[]按升序排列
        int N = a.length; // 数组长度
        for (int i = 0; i < N; i++)
        { // 将a[i]和a[i+1..N]中最小的元素交换
            int min = i; // 最小元素的索引
            for (int j = i+1; j < N; j++)
                if (less(a[j], a[min])) min = j;
            exch(a, i, min);
        }
    }// less()、exch()、isSorted()和main()方法见“排序算法类模板”
}
```

算法时间复杂度：$O(n^2)$

空间复杂度：$O(1)$

### 插入排序

步骤：

* 第一个元素默认排好序
* 将当前的元素插入适当的位置

```java
public class Insertion
{
    public static void sort(Comparable[] a)
	{ // 将a[]按升序排列
		int N = a.length;
		for (int i = 1; i < N; i++)
		{ // 将 a[i] 插入到 a[i-1]、a[i-2]、a[i-3]...之中
			for (int j = i; j > 0 && less(a[j], a[j-1]); j--)
                exch(a, j, j-1);
        }
    }
}
```

算法时间复杂度：$O(n^2)$(最好为$O(n)$)

空间复杂度：$O(1)$

### 希尔排序

希尔排序是基于插入排序的快速的排序算法

希尔排序的思想是使数组中任意间隔为 h 的元素都是有序的。这样的数组被称为 h 有序数组。换句话说，一个 h 有序数组就是 h 个互相独立的有序数组编织在一起组成的一个数组。在进行排序时，如果 h 很大，我们就能将元素移动到很远的地方，为实现更小的 h 有序创造方便。用这种方式，对于任意以 1 结尾的 h 序列，我们都能够将数组排序。这就是希尔排序。

希尔排序更高效的原因是它权衡了子数组的规模和有序性。

```java
public class Shell
{
    public static void sort(Comparable[] a)
    { // 将a[]按升序排列
        int N = a.length;
        int h = 1;
        while (h < N/3) h = 3*h + 1; // 1, 4, 13, 40, 121, 364, 1093, ...
        while (h >= 1)
        { // 将数组变为h有序
            for (int i = h; i < N; i++)
            { // 将a[i]插入到a[i-h], a[i-2*h], a[i-3*h]... 之中
                for (int j = i; j >= h && less(a[j], a[j-h]); j -= h)
                    exch(a, j, j-h);
            }
            h = h/3;
        }
    }// less()、exch()、isSorted()和main()方法见“排序算法类模板”
}
```

算法时间复杂度：平均：$O(n^{1.5})$，最好：$O(n\ log\ n)$，最坏：$O(n^2)$

空间复杂度：$O(1)$