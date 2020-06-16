# 函数

## 函数对象

JavaScript中函数就是对象。对象是“Key/Value”对的集合，并拥有一个连到原型对象的隐藏连接。

对象字面量会连接到Object.prototype，函数对象会连接到Function.prototype。

每个函数在创建时会附加两个隐藏属性：函数的上下文和实现函数行为的代码；每个函数在创建时也随配有一个prototype属性。它的值是一个拥有constructor属性且值为该函数的对象。

## 函数字面量

函数对象通过函数字面量来创建：

```javascript
var add = function(a, b){
    return a + b;
}
```

函数字面量包括4个部分，第一个部分是保留字function，第二部分是函数名（可省略）。第三部分是包围在括号内的一组参数，第四部分是包围在花括号内的一组语句。

函数字面量可以出现在任何允许表达式出现的地方，函数也可以定义在其他函数内部。一个内部函数除了可以访问自己的参数和变量，同时也能访问把它嵌套在其中的父函数的参数和变量；

通过函数字面量创建的函数对象包含一个连到外部上下文的连接，这称为**闭包**；

## 调用

调用一个函数会暂停当前函数的执行，传递控制权和参数给新函数。

JavaScript中有四种调用模式：

* 方法调用模式
* 函数调用模式
* 构造器调用模式
* apply调用模式

### 方法调用模式

当一个函数被保存为对象的一个属性时，称它为一个方法。当一个方法被调用时，this被绑定到该对象。

```javascript
var myObject = {
    value : 0,
    increment : function(inc){
        this.value += typeof inc === 'number' ? inc : 1;
    }
};
myObject.increment();
document.writeln(myObject.value); // 1
myObject.increment(2);
document.writeln(myObject.value); // 3
```

方法可以使用this访问自己所属的对象，所以它能从对象中取值或对象进行修改。

通过this可取得它们所属对象得上下文的方法称为**公共方法**；

### 函数调用模式

当一个函数并非一个对象的属性时，那么就被当作函数来调用；

以此模式调用函数时，this被绑定到全局对象。

解决办法：为该方法定义一个变量并赋值为this；

```javascript
myObject.double = function(){
    var that = this;
    var helper = function(){
        that.value = add(that.value, that.value);
    }
    helper();
}
```

### 构造器调用模式

JavaScript是一门基于原型继承的语言。如果在一个函数前用new来调用，则会自动地创建一个连接到该函数地prototype成员地新对象，同时this会被绑定到那个新对象上；

```javascript
var Quo = function(string){
    this.status = string;
};

Quo.prototype.get_status = function(){
    return this.status;
};

var myQuo = new Quo("confused");
document.writeln(myQuo.get_status()); // 打印显示“confused”
```

如果调用构造器函数时没有在前面加上new，没有编译时警告，也没有运行时警告，因此不推荐使用这种形式地构造器函数；

### Apply调用模式

JavaScript是一门函数式的面向对象编程语言，所以函数可以拥有方法。

apply方法让我们构建一个参数数组传递给调用函数，apply方法接收两个参数，第一个要绑定给this的值，第二个就是一个参数数组；

```javascript
var arr = [3 , 4];
var sum = add.apply(null, arr); // sum = 7
```

## 参数

当函数被调用时，会有一个默认的参数，那就是arguments数组。

函数可以通过此参数访问呢所有它被调用时传递给它得到参数列表，包括那些没有被分配给函数声明时定义的形式参数的多余参数。

```javascript
var sum = function(){
    var i, sum = 0;
    for(i = 0; i < arguments.length; i+= 1){
        sum += arguments[i];
    }
    return sum;
}
```

arguments不是一个真正的数组，只是一个“类似数组”的对象，拥有一个length的属性，但没有任何数组的方法。

## 返回

当一个函数被调用时，return可以用来使函数提前返回。一个函数总会返回一个值，未指定返回值，则返回undefined。

## 异常

JavaScrip提供了一套异常处理机制：

```javascript
var add = function(a, b){
    if(typeof a !== 'number' || typeof b != 'number'){
        throw {
            name : 'TypeError',
            message : 'add needs numbers'
        }
    }
    return a + b;
}
```

throw语句中断函数的执行，抛出一个exception对象，该对象包含用来识别异常类型的name属性和一个描述性的message属性，也可以包含其他属性；

```javascript
try{
    //
}catch(e){
    //
}
```

try内抛出一个异常，控制权就会跳转到catch从句中。

## 扩充类型的功能

JavaScript允许给语言的基本类型扩展功能。

例如，可以给Function.prototype增加方法来使得该方法对所有函数可用：

```javascript
Function.prototype.method = function(name, func){
    this.prototype[name] = func;
    return this;
}
```

如给Number添加一个integer方法：

```javascript
Number.method('integer', function(){
    return Math[this < 0 ? 'ceil' : 'floor'](this);
});
```

应为JavaScript原型继承的动态本质，新的方法立刻被赋予到所有的对象实例上，因此基本类型的原型不要轻易修改，所以在类库混用时务必小心；一个保险的做法就是只在确定没有该方法时才添加；

```javascript
Function.prototype.method = function(name, func){
    if(!this.prototype[name]){
        this.prototype[name] = func;       
    }
    return this;
}
```

## 递归

递归函数就是会直接地或间接地调用自身的一种函数。

JavaScript未提供尾递归优化。

## 作用域

作用域控制着变量与参数的可见性及生命周期。

JavaScript确实有函数作用域，函数中的参数和变量在函数外部不可见，而在函数内部任何位置定义的变量，在函数内部都可见。

因此，尽可能地在函数体地顶部声明函数中可能用到地所有变量。

## 闭包

函数和对其周围状态（**lexical environment，词法环境**）的引用捆绑在一起构成**闭包**（**closure**）。也就是说，闭包可以让你从内部函数访问外部函数作用域。在 JavaScript 中，每当函数被创建，就会在函数生成时生成闭包。

闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。这显然类似于面向对象编程。在面向对象编程中，对象允许我们将某些数据（对象的属性）与一个或者多个方法相关联。

因此，通常你使用只有一个方法的对象的地方，都可以使用闭包。

```javascript
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```

## 模块

可以使用模块和闭包来构造模块，模块时一个提供接口却隐藏状态与实现地函数或对象。

例子：给String增加一个deentityify方法，寻找字符串中的HTML字符实体并把它们替换为对应的字符。

```javascript
String.method('deentityify', function(){
    var entity = {
        quot : '"',
        lt : '<',
        gt : '>'
    }
    return function(){
        return this.replace(/&([^&;]+);/g,
                           function (a, b){
            var r = entity[b];
            return typeof r === 'string' ? r : a;
        });
    };
}());
```

模块模式利用了函数作用域和闭包来创建被绑定对象与私有成员的关联；

模块模式的一般形式：一个定义了私有变量和函数的函数；利用闭包创建可以访问私有变量和函数的特权函数；最后返回这个特权函数，或者把它们保存到一个可以访问到的地方。

使用模块模式可以摒弃全局变量的使用，促进了信息隐藏和其他的优秀设计实践。对于应用程序的封装，或者构造其他单例对象，模块模式非常有效。

## 级联

让方法返回this而不是undefined就可以启用级联。

级联技术可以产生极富表现力的接口。

## 柯里化

函数也是值，柯里化允许把函数与传递给它的参数相结合，产生出一个新的函数。

JavaScript没有curry方法，但可以给Function.prototype扩展此功能：

```javascript
Function.method('curry',function(){
    var args = arguments, that = this;
    retrun function(){
        return that.apply(null, args.concat(arguments));
    }
})
```

curry方法通过创建一个保存着原始函数和要被套用的参数的闭包来工作；它返回另一个函数，该函数被调用时，会返回调用原始函数的结果，并传递调用curry时的参数加上当前调用的参数。

应为`arguments`不是一个数组，因此没有concat方法，所以：

```javascript
Function.method('curry',function(){
    var slice = Array.prototype.slice;
    var args = slice.apply(arguments), that = this;
    retrun function(){
        return that.apply(null, args.concat(slice.apply(arguments)));
    }
})
```

## 记忆

函数可以将先前的操作的结果记录在某个对象内，从而避免无谓的重复。这种优化称为**记忆**；

```javascript
var fibonacci = function(){
    var memo = [0, 1];
    var fib = function(n){
        var result = memo[n];
        if(typeof result !== 'number'){
            result = fib(n-1) + fib(n-2);
            memo[n] = result;
        }
        return result;
    }
    return fib;
}();
```

