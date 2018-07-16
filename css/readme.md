#### 1.外边距折叠
##### 1.1 外边距折叠情况
<pre>
1.浮动元素不会与任何元素发生叠加，也包括它的子元素

2.创建了BFC的元素不会和它的子元素发生外边距叠加

3.绝对定位元素和其他任何元素之间不发生外边距叠加，也包括它的子元素(BFC)

4.inline-block元素和其他任何元素之间不发生外边距叠加，也包括它的子元素(BFC)

5.普通流中的块级元素的margin-bottom永远和它相邻的下一个块级元素的margin-top 叠加，除非相邻的兄弟元素clear

6.普通流中的块级元素（没有border-top、没有 padding-top）的margin-top和它的第一个普通流中的子元素（没有clear发生margin-top叠加

7.普通流中的块级元素（height为auto、min-height为0、没有 border-bottom、没有 padding-bottom）和它的最后一个普通流中的子元素（没有自身发生margin叠加或clear）发生 margin-bottom叠加

8.如果一个元素的min-height为0、没有border、没有padding、高度为0或者auto、不包含子元素，那么它自身的外边距会发生叠加
</pre>

毗邻说明了他们的位置关系，没有被**padding、border、BFC**分隔开。

##### 1.2 clear与margin重叠
我们只需要知道，**闭合浮动的元素的border-top会紧贴着相应的浮动元素的margin-bottom**。原来，通过w3c的官方规范可知，闭合浮动的块盒在margin-top上所产生的间距（clearance）的值与该块盒的margin-top之和应该足够让该块盒垂直的跨越浮动元素的margin-bottom，使闭合浮动的块盒的border-top恰好与浮动元素的块盒的margin-bottom相邻接。

```html
<div style="background-color: #f5f5f5;">
    <img src="http://img.mukewang.com/53d60af3000171a002560191.jpg" style="float:left;">
    <!-- clearance的margin-bottom还是会和下一个元素合并 -->
    <div style="clear:both; margin-bottom:100px;">clear:both;</div>
</div>
<div style="margin-top: 100px;">本文字离图片的距离是？</div>
```
所以文字距离图片的距离就是100px，原因是上一个父元素的最后一个子元素的margin-bottom和下一个兄弟元素的[margin-top重叠](https://www.imooc.com/code/2778)。
```html
<div style="background-color: #f5f5f5; overflow: hidden;">
    <img src="http://img.mukewang.com/53d60af3000171a002560191.jpg" style="float:left; margin-bottom: 100px;"/>
</div>
<div style="margin-top: 100px;">本文字离图片的距离是？</div>
```
而这个例子因为上一个兄弟元素创建了BFC，导致无法和下面的兄弟元素合并，从而距离变成了200px。其实此时也可以通过clear:both来实现同样的功能:
```html
<div style="background-color: #f5f5f5; ">
    <img src="http://img.mukewang.com/53d60af3000171a002560191.jpg" style="float:left; margin-bottom: 100px;">
    <!-- clearance导致多了100px的间距，而且为clear元素不管怎么修改margin-top都不会影响clear的位置,margin-bottom还是会和下面的margin-top重叠(注意!) -->
    <div style="clear:both">clear both</div>
</div>
<div style="margin-top: 100px;">本文字离图片的距离是？</div>
```

##### 1.3 浮动元素脱离BFC不会影响其他元素合并
但是浮动元素脱离了当前的BFC并不影响它后面的兄弟元素，后面的**兄弟元素与浮动元素前面的元素依然在同一个BFC当中，所以，它们之间的margin还是会折叠的**。下面我们对上面的demo做一下修改：
```html
<div class="wrapper overHid">
    <div class="big-box">non-float</div>
    <div class="middle-box green floatL">float left</div>
    <div class="middle-box red">non-clear</div>
</div>
```

参考资料:

[深入理解CSS外边距折叠（Margin Collapse）](https://segmentfault.com/a/1190000010346113)

[深入理解BFC和Margin Collapse](https://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)
