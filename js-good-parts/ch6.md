# 数组

## 数组字面量

```javascript
var num = [1, 2, 3, 4];
num.length // 4
```

## 长度

每个数组都有个length属性。JavaScript数组的length是没有上界的，如果用一个大于或等于当前length的数字作为下标来存储一个元素，那么length值会被增大以容纳新元素，不会发送数组越界错误；

```javascript
var arr = [];
arr.length // 0
arr[10000] = true;
arr.length // 10001
```

## 删除

JavaScript中的数组就是对象，直接可以用delete来移出元素：

```javascript
delete arr[i];
```

但是会遗留一个空洞；

需要移出后后面的向前移，则使用splice方法的：

```javascript
arr.splice(i, j);
// 第一个参数为数组的下标，第二个为删除元素的个数
```

## 枚举

可以用for in来遍历，但是不能保证顺序。

要保证顺序则用常规的for语句；

## 容易混淆的地方

当属性名是小而连续的整数时，使用数组；否则，使用对象；

JavaScript中数组的类型是object，因此无法很好的区分数组和对象，判断方法：

```javascript
function is_array(value){
    return Object.prototype.toString.apply(value) === '[object Array]';
}
```

## 方法

JavaScript提供了一套数组的可用方法，存储在Array.prototype中。

也可以给array拓展：

```javascript
Function.prototype.method = function(name, func){
    this.prototype[name] = func;
    return this;
}
Array.method('reduce', function(f, value){
    var i;
    for(i = 0; i < this.length; i += 1) {
        value = f(this[i], value);
    }
    return value;
});
```

## 指定初始值

JavaScript的数组通常不会预置值，因此可以实现一个类似Array.dim的方法来做这件事：

```javascript
Function.prototype.method = function(name, func){
    this.prototype[name] = func;
    return this;
} // 给对象拓展方法
Array.dim = function(dimension, initial){
    var a = [], i;
    for (i = 0; i < dimension; i += 1) {
        a[i] = initial;
    }
    return a;
};
Array.matrix = function(m, n, initial){
    var a, i, j, mat = [];
    for (i = 0; i < m; i += 1) {
        a = [];
        for(j = 0; j < n; j += 1) {
            a[j] = initial;
        }
        mat[i] = a;
    }
    return mat;
};
```

