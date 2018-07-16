本部分主要是babel相关的内容。

#### babel的polyfill和runtime的区别
Babel默认只转换新的JavaScript 语法，而不转换新的API。例如，Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如 Object.assign）都不会转译。如果想使用这些新的对象和方法，必须使用babel-polyfill。

babel-runtime为什么适合JavaScript库和工具包的实现？
<pre>
1.避免babel编译的工具函数在每个模块里重复出现，减小库和工具包的体积(例如lodash)；
2.不会污染全局变量
</pre>







参考资料:

[babel的polyfill和runtime的区别](https://segmentfault.com/q/1010000005596587)