# 继承

JavaScript是基于原型链的语言，因此对象可以直接从其他对象继承；

## 伪类

JavaScript不直接让对象从其他对象继承，而是插入一个多余的中间层：通过构造器函数产生对象。

当一个函数对象被创建时，Function构造器产生的函数对象会运行类似这样的一些代码：

```javascript
this.prototype = {constructor : this};
```

新函数对象被赋予了一个prototype属性，其值是一个包含constructor属性且值为该新函数的对象；这个prototype对象是存放继承特征的地方。

例子：

```javascript
Function.prototype.method = function(name, func){
    this.prototype[name] = func;
    return this;
};
// 修改new运算符
Function.method('new', function(){
    var that = Object.create(this.prototype);
    var other = this.apply(that, arguments);
    return (typeof other === 'object' && other) || that;
});
// 定义一个构造器
var Mammal = function(name){
    this.name = name;
};
Mammal.prototype.get_name = function(){
    return this.name;
};
Mammal.prototype.says = function(){
    return this.saying || '';
};
var myMammal = new Mammal('Herb the mammal');
console.log(myMammal.get_name()); // Herb the mammal

// 构造另外一个伪类来继承Mammal
var Cat = function(name){
    this.name = name;
    this.saying = 'meow';
}
Cat.prototype = new Mammal();
Cat.prototype.purr = function(n){
    var i,s='';
    for(i = 0; i < n; i+= 1){
        if(s){
            s += '-';
        }
        s += 'r';
    }
    return s;
};
Cat.prototype.get_name = function(){
    return this.says() + ' ' + this.name + ' ' + this.says();
};
var myCat = new Cat('Tom');
console.log(myCat.says());  // meow
console.log(myCat.purr(5)); // r-r-r-r-r 
console.log(myCat.get_name()); // meow Tom meow
```

伪类模式与寻常的面向对象不太类似，可以通过使用method方法来定义一个inherits方法来实现：

```javascript
Function.method('inherits', function(Parent){
    this.prototype = new Parent();
    return this;
})
```

修改上面的继承：

```javascript
var Cat = function(name){
    this.name = name;
    this.saying = 'meow';
}
.inherits(Mammal)
.method('purr', function(n){
    var i,s='';
    for(i = 0; i < n; i+= 1){
        if(s){
            s += '-';
        }
        s += 'r';
    }
    return s;
})
.method('get_name', function(){
    return this.says() + ' ' + this.name + ' ' + this.says();
});
```

## 对象说明符

有时候构造器要接受一大串参数，一般这种情况下编写一个简单的对象说明符会更加友好：

```javascript
var myobject = maker(f, l, m, c, s);
// 下面的更好
var myobject = maker({
    first : f,
    middle : m,
    last : l,
    state : s,
    city : c
});
```

