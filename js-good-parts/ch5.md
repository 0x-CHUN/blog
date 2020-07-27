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

## 原型

在一个纯粹的原型模式中，我们会摒弃类，转而专注于对象。基于原型的继承相比基于类的继承在概念上更为简单：一个新对象可以继承一个旧对象的属性。通过构造一个有用的对象开始，接着可以构造更多和那个对象类似的对象。这就可以完全避免把一个应用拆解成一系列嵌套抽象类的分类过程。

```javascript
var myMammal = {
    name : 'Herb the Mammal',
    get_name : function(){
        return this.name;
    },
    says : function(){
        return this.saying || '';
    }
};
```

一旦有了一个想要的对象，可以利用Object.create方法构造出更多的实例来：

```javascript
var myCat = Object.create(myMammal);
myCat.name = 'Henrietta';
myCat.saying = 'meow';
myCat.purr = function(n){
    var i, s = ''; 
       for (i = 0; i < n; i += 1) { 
          if (s) { 
             s += '-'; 
          }
          s += 'r'; 
       } 
       return s; 
};
myCat.get_name = function(){
    return this.saying + ' ' + this.name + ' ' + this.saying;
};
```

这是一种差异化继承。

## 函数化

继承模式的一个弱点就是没法保护隐私。

解决办法：应用模块模式；

步骤：

1. 创建一个对象
2. 有选择性的定义私有变量和方法
3. 给这个新对象扩充方法
4. 返回那个新对象

函数化构造器的伪代码模板：

```javascript
var constructor = function(spec, my){
    var that,其他的私有变量；
    my = my || {};
    
    // 把共享的变量和函数添加到my中
    that = 一个新对象;
    // 添加给that的特权方法
    return that;
}
```

将应用模块模式应用到mammal例子中

```javascript
var mammal = function(spec){ // 此处不需要my
    var that = {};
    that.get_name = function(){
        return spec.name;
    };
    that.says = function(){
        return spec.saying || '';
    };
    return that;
};

var cat = function(spec){
    spec.saying = spec.saying || 'meow';
    var that = mammal(spec);
    that.purr = function(n){
        var i, s = ''; 
       for (i = 0; i < n; i += 1) { 
          if (s) { 
             s += '-'; 
          }
          s += 'r'; 
       } 
       return s; 
    }
    that.get_name = function(){
        return that.says() + ' ' + spec.name + ' ' + that.says();
    };
    return that;
}

```

函数化模式还给我们提供了一个处理父类方法的方法，构造一个superior方法，取得一个方法名并返回调用那个方法的函数。

```javascript
Function.prototype.method = function(name, func){
    this.prototype[name] = func;
    return this;
};

Object.method('superior', function(name){
    var that = this,
        method = that[name];
    return function(){
        return method.apply(that, arguments);
    };
});

var coolcat = function(spec){
    var that = cat(spec),
        super_get_name = that.superior('get_name');
    that.get_name = function(n){
        return 'like ' + super_get_name() + ' baby';
    };
    return that;
};
```

## 部件

可以从一套部件中把对象组装出来。例如，可以构造一个给任何对象添加简单事件处理特性的函数。它会给对象添加一个on方法、一个fire方法和一个私有的事件注册表对象：

```javascript
var eventuality = function(that){
    var registry = {};
    that.fire = function(event){
        var array,
            func,
            handler,
            i,
            type = typeof event === 'string' ? event : event.type;
        if (registry.hasOwnProperty(type)){
            array = registry[type];
            for(i = 0; i < array.length; i++){
                func = handler.method;
                if(typeof func === 'string'){
                    func = this[func];
                }
                func.apply(this,handler.parameters || [event]);
            }
        }
        return this;
    };
    that.on = function(type, method, parameters){
        var handler = {
            method : method,
            parameters : parameters
        };
        if(registry.hasOwnProperty(type)){
            registry[type].push(handler);
        }else{
            registry[type] = [handler];
        }
        return this;
    };
    return that;
};
```

可以在任何单独的对象上调用eventuality，授予它事件处理方法。