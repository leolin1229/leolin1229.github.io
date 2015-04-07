
众所周知，JS中Array对象有个slice方法（String也有一个），这个方法具体作用是返回从数组中指定下标范围的元素，具体使用请查看 [W3C文档](http://www.w3school.com.cn/jsref/jsref_slice_array.asp)。
今天在看Curring时候发现经常出现：

    Array.prototype.slice.call(arguments, 0);
于是整理一下Array.prototype.slice()的奇技淫巧。


----------

 1. 把类数组对象转化为数组

   var foo = function() {
		 return Array.prototype.slice.call(arguments);
  };
foo(1, 2, 3); // [1, 2, 3]

除此之外，Array.prototype.slice.call(arguments) == [].slice.call(arguments)。
另外，还可以使用 bind 进行更酷炫的变形：

    var slice = Function.prototype.call.bind(Array.prototype.slice);
var foo = function() {
return slice(arguments);
}
foo(1, 2, 3); // [1, 2, 3]
slice([1, 2, 3]); // [1, 2, 3]
// 但是
slice('abc'); // ['a', 'b', 'c']
foo('abc'); // ['abc'] 为什么这样呢？事实上相当于foo({0: 'abc', length:1}),其实arguments内部远远没有这么简单


一般这样对于习惯函数式编程的码农是比较容易上手。
说起这个稍微拓展一下，在某个blog看到下面一句很优雅的代码：

    var bind = Function.prototype.call.bind(Function.prototype.bind); 

var context = { foo: "bar" };
function returnFoo () {
  return this.foo;
}
var amazing = bind(returnFoo, context);
amazing(); // => bar

这句表示 foo.bind(arguments) => bind(foo, arguments)，对于熟悉函数式编程的人一眼就看出来，补一句，这里的bind在ES6还处于草案阶段。

 2. 复制数组

    Array.prototype.clone = function() {
      return this.slice(0);
};
var a = [1,2,3];
var b = a.clone();
console.log(a == b); // false 因为clone函数创建一块新内存存放新数组，变量a与b指向不同数组对象