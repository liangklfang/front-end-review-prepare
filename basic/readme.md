#### 1.函数柯里化
```js
//bind,reduce都是用函数柯里化
function curry(fn){
  var args = Array.prototype.slice.call(arguments,1);
  return function(){
      var innerArgs = Array.prototype.slice.call(arguments);
      var finalArgs = args.concat(innerArgs);
      return fn.apply(null,finalArgs);
  }
}
```

#### 2.函数柯里化与绑定函数
```js
function bind(fn,context){
    var args = Array.prototype.slice.call(arguments,2);
    return function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(context,finalArgs); 
    }
}
```

#### 3.函数防抖或者函数节流
```js
function throttle(method,context){
   clearTimeInterval(method.id);
   method.id = setTimeout(function(){
       method.call(context);
   },100);
}
//下面也是函数节流
function chunk(array,process,context){
    setTimeout(function(){
        var item = array.shift();
        process.call(context,item);
        if(array.length>0)
           setTimeout(arguments.callee,100);
    },100);
}
```

#### 4.for..in/for..of区别
```js
Object.prototype.objCustom = function () {}; 
Array.prototype.arrCustom = function () {};
let iterable = [3, 5, 7];
iterable.foo = "hello";
for (let i in iterable) {
  console.log(i); 
  // logs 0, 1, 2, "foo", "arrCustom", "objCustom"
}

for (let i of iterable) {
  console.log(i); 
  // logs 3, 5, 7
}
```

#### 5.instanceof操作符运算
![](./imgs/prototype.jpg)






参考资料:

[Object instanceof Function 还是 Function instance of Object，是真是假，一一道来](https://www.cnblogs.com/objectorl/archive/2010/01/11/Object-instancof-Function-clarification.html)
