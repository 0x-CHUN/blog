# 方法

JavaScript包含一套小型的可用在标准类型上的标准方法集；

## Array

* array.concat(item...)：包含一个array的浅复制并把一个或多个参数item附加在其后；

  ```javascript
  var a = ['a', 'b', 'c'];
  var b = ['x', 'y', 'z'];
  var c = a.concat(b); // c = ['a', 'b', 'c', 'x', 'y', 'z']
  ```

* array.join(separator)：把array中的每一个元素构造成一个字符串，然后用一个separate分隔符把它们连接在一起，separate默认值为","；

  ```javascript
  var a = [1, 2, 3];
  var b = a.join(''); // b = '123'
  ```

* array.pop()：把array的最后一个元素移出并返回

* array.push(item...)：把item添加到数组的尾部，并返回array的新长度；

* array.reverse()：反转array里的元素的顺序，并返回array本身；

* array.shift()：移除第一个元素并返回该元素

* array.slice(start, end)：对array从start到end进行一段浅复制；

* array.sort(comparefn)：对array进行排序，大小按照comparefn进行比较

* array.splice(start, deleteCount, item..)：在array中，从start开始移除一个或多个，从start然后插入item（item参数存在的话）；

* array.unshift(item...)：把item从开始部分插入

## Function

* function.apply(thisArg, argArray)：apply方法调用function，传递一个会被绑定到this上的对象和一个可选的数组作为参数。apply方法被用在apply调用模式：

  ```javascript
  Function.method('bind', function (that) {
      var method = this,
          slice = Array.prototype.slice,
          args = slice.apply(arguments, [1]);
      return function () {
          return method.apply(that,
                             args.concat(slice.apply(arguments, [0])));
      };
  });
  ```

## Number

* number.toExponential(fractionDigits)：转换成一个指数形式的字符串，fractionDigits控制小数点后的数字位数；
* number.toFixed(fractionDigits)：转换为一个十进制数形式的字符串，fractionDigits控制小数点后的数字位数；
* number.toPrecision(precision)：转换为一个十进制数形式的字符串，precision控制数字的精度；
* number.toString(radix)：转换成字符串，radix控制基数，默认为10；

## Object

* object.hasOwnProperty(name)：检查这个object是否含有一个名为name的属性。原型链中的同名属性不会被检查；

## RegExp

* regexp.exec(string)：进行正则匹配，返回一个数组，下标0包含正则匹配的子字符串，下标1为分组1捕获的文本，下标2为分组2捕获的文本，以此类推；
* regexp.test(string)：返回是否匹配；

## String

* string.charAt(pos)：返回string中pos位置处的字符。
* string.charCodeAt(pos)：返回string中pos位置处的字符码位；
* string.concat(item...)：字符拼接
* string.indexOf(searchString, pos)：在string内从pos开始查找另外一个字符searchString，返回第一个匹配的字符的位置；
* string.lastIndexOf(searchString, pos)：在string内从pos开始查找另外一个字符searchString，返回最后一个匹配的字符的位置；
* string.localeCompare(that)：比较两个字符串
* string.match(regexp)：正则匹配
* string.replace(searchValue, replaceValue)：字符串替换
* string.search(regexp)：类似于indexOf，但是只能接受正则表达式位参数，且无position参数；
* string.slice(start, end)：从start复制到end
* string.split(separator, limit)：将string分割成字符串数组，分割符是separate，limit限制被分割的片段数；
* string.substring(start, end)：与slice一致，只是slice可以处理start为负数（start为负数则加上string.length）；
* string.toLocalLowerCase()：使用本地化规则，转换为小写
* string.toLocalUpperCase()：使用本地化规则，转换为大写
* string.toLowerCase()：转换为小写
* string.toUpperCase()：转换为大写
* string.fromCharCode(char...)：根据一串数字编码返回一个字符串

