---
tags:
  - 前端
  - JS
---

## jQuery 的介绍

### 引入 jQuery 的原因

在用 js 写代码时，会遇到一些问题：

- window.onload 事件有事件覆盖的问题，因此只能写一个事件。

- 代码容错性差。

- 浏览器兼容性问题。

- 书写很繁琐，代码量多。

- 代码很乱，各个页面到处都是。

- 动画效果很难实现。

### 什么是 jQuery

jQuery 是 js 的一个库，封装了我们开发过程中常用的一些功能，方便我们调用，提高开发效率。

js库是把我们常用的功能放到一个单独的文件中，我们用的时候，直接引用到页面里即可。

以下是jQuery的相关信息：

- 官网：<http://jquery.com/>

- 官网API文档：<http://api.jquery.com/>

  

## jQuery 的使用

### 使用 jQuery 的基本步骤

（1）引包

（2）入口函数

（3）功能实现代码（事件处理）

如下图所示：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182329613.png)

主要，导包的代码一定要放在js代码的最上面。

### jQuery 的版本

jQuery 有两个大版本：

- 1.x版本：最新版为 v1.11.3。

- 2.x版本：最新版为 v2.1.4（不再支持IE6、7、8）。

- 3.x版本。


PS：开发版本一般用1.10以上。

我们以 v1.11.1版本为例，下载下来后发现，里面有两个文件：

我们以 v1.11.1版本为例，下载下来后发现，里面有两个文件：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182332271.png)

它们的区别是：

- 第一个是未压缩版，第二个是压缩版。

- 平时开发过程中，可以使用任意一个版本；但是，项目上线的时候，推荐使用压缩版。

## jQuery 的入口函数和 `$` 符号

### 入口函数（重要）

原生 js 的入口函数指的是：`window.onload = function() {};` 如下：

```javascript
        //原生 js 的入口函数。页面上所有内容加载完毕，才执行。
        //不仅要等文本加载完毕，而且要等图片也要加载完毕，才执行函数。
       window.onload = function () {
           alert(1);
       }
```


而 jQuery的入口函数，有以下几种写法：

写法一：


```javascript
       //1.文档加载完毕，图片不加载的时候，就可以执行这个函数。
       $(document).ready(function () {
           alert(1);
       })
```

写法二：（写法一的简洁版）

```javascript
       //2.文档加载完毕，图片不加载的时候，就可以执行这个函数。
       $(function () {
           alert(1);
       });
```

写法三：

```javascript
       //3.文档加载完毕，图片也加载完毕的时候，在执行这个函数。
       $(window).ready(function () {
           alert(1);
       })
```

**jQuery入口函数与js入口函数的区别**：

区别一：书写个数不同：

- Js 的入口函数只能出现一次，出现多次会存在事件覆盖的问题。

- jQuery 的入口函数，可以出现任意多次，并不存在事件覆盖问题。


区别二：执行时机不同：

- Js的入口函数是在**所有的文件资源加载**完成后，才执行。这些**文件资源**包括：页面文档、外部的js文件、外部的css文件、图片等。

- jQuery的入口函数，是在文档加载完成后，就执行。文档加载完成指的是：DOM树加载完成后，就可以操作DOM了，不用等到所有的**外部资源**都加载完成。

文档加载的顺序：从上往下，边解析边执行。

### jQuery的`$`符号

jQuery 使用 `$` 符号原因：书写简洁、相对于其他字符与众不同、容易被记住。

jQuery占用了我们两个变量：`$` 和 jQuery。当我们在代码中打印它们俩的时候：


```html
    <script src="jquery-1.11.1.js"></script>
    <script>

        console.log($);
        console.log(jQuery);
        console.log($===jQuery);


    </script>
```

打印结果如下：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182332306.png)

从打印结果可以看出，$ 代表的就是 jQuery。

那怎么理解jQuery里面的 `$` 符号呢？

**`$` 实际上表示的是一个函数名** 如下：


```html
	$(); // 调用上面我们自定义的函数$

	$(document）.ready(function(){}); // 调用入口函数

	$(function(){}); // 调用入口函数

	$(“#btnShow”) // 获取id属性为btnShow的元素

	$(“div”) // 获取所有的div标签元素

```

如上方所示，jQuery 里面的 `$` 函数，根据传入参数的不同，进行不同的调用，实现不同的功能。返回的是jQuery对象。

jQuery这个js库，除了` $` 之外，还提供了另外一个函数：jQuery。jQuery函数跟 `$` 函数的关系：`jQuery === $`。

##  js中的DOM对象 和 jQuery对象 比较（重点，难点）

### 二者的区别

通过 jQuery 获取的元素是一个**数组**，数组中包含着原生JS中的DOM对象。举例：

针对下面这样一个div结构：

```html
<div></div>
<div class="box"></div>
<div id="box"></div>
<div class="box"></div>
<div></div>
```

通过原生 js 获取这些元素节点的方式是：

```javascript
    var myBox = document.getElementById("box");           //通过 id 获取单个元素
    var boxArr = document.getElementsByClassName("box");  //通过 class 获取的是数组
    var divArr = document.getElementsByTagName("div");    //通过标签获取的是数组
```

通过 jQuery 获取这些元素节点的方式是：（获取的都是数组）

```javascript
    //获取的是数组，里面包含着原生 JS 中的DOM对象。
    var jqBox1 = $("#box");
    var jqBox2 = $(".box");
    var jqBox3 = $("div");
```

我们打印出来看看：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182333711.png)

上图显示，由于JQuery 自带了 css()方法，我们还可以直接在代码中给 div 设置 css 属性。

**总结**：jQuery 就是把 DOM 对象重新包装了一下，让其具有了 jQuery 方法。

### 二者的相互转换

**1、 DOM 对象 转为 jQuery对象**：

```javascript
	$(js对象);
```

举例：（拿上一段的代码举例）

```javascript
	//转换。
	jqBox1 = $(myBox);
	jqBox2 = $(boxArr);
	jqBox3 = $(divArr);
```

DOM 对象转换成了 jquery 对象之后，上面的功能可以直接调用。

**2、jQuery对象 转为 DOM 对象**：

```javascript
	jquery对象[index];      //方式1（推荐）

	jquery对象.get(index);  //方式2
```

jQuery对象转换成了 DOM 对象之后，可以直接调用 DOM 提供的一些功能。如：

```javascript
    //jquery对象转换成 DOM 对象之后
    jqBox3[0].style.backgroundColor = "black";
    jqBox3.get(4).style.backgroundColor = "pink";
```

**总结**：如果想要用哪种方式设置属性或方法，必须转换成该类型。

### 举例：隔行变色

代码如下：

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <script src="jquery-1.11.1.js"></script>
    <script>
        //入口函数
        jQuery(function () {
            var jqLi = $("li");
            for (var i = 0; i < jqLi.length; i++) {
                if (i % 2 === 0) {
                    //jquery对象，转换成了js对象
                    jqLi[i].style.backgroundColor = "pink";
                } else {
                    jqLi[i].style.backgroundColor = "yellow";
                }
            }
        });
    </script>
</head>
<body>
<ul>
    <li>生命壹号，永不止步</li>
    <li>生命壹号，永不止步</li>
    <li>生命壹号，永不止步</li>
    <li>生命壹号，永不止步</li>
    <li>生命壹号，永不止步</li>
    <li>生命壹号，永不止步</li>
    <li>生命壹号，永不止步</li>
</ul>
</body>
</html>
```

效果如下：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182333537.png)

## jQuery 选择器

我们以前在CSS中学习的选择器有：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182334076.png)

### 1、jQuery 的基本选择器

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182334695.png)

解释如下：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182334601.png)

举例：

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <script src="jquery-1.11.1.js"></script>
    <script>
        //入口函数
        jQuery(document).ready(function () {

            //三种方式获取jquery对象
            var jqBox1 = $("#box");
            var jqBox2 = $(".box");
            var jqBox3 = $("div");

            //操作标签选择器
            jqBox3.css("width", 100);
            jqBox3.css("height", 100);
            jqBox3.css("margin", 10);
            jqBox3.css("background", "pink");

            //操作类选择器(隐式迭代，不用一个一个设置)
            jqBox2.css("background", "red");

            //操作id选择器
            jqBox1.css("background", "yellow");

        });
    </script>
</head>
<body>

<div></div>
<div class="box"></div>
<div id="box"></div>
<div class="box"></div>
<div></div>

</body>
</html>
```

效果如下：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182335743.png)

### 2、层级选择器

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182335293.png)

解释如下：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182336837.png)

举例：

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <script src="jquery-1.11.1.js"></script>
    <script>
        $(function () {
            //获取ul中的li设置为粉色
            //后代：儿孙重孙曾孙玄孙....
            var jqLi = $("ul li");
            jqLi.css("margin", 5);
            jqLi.css("background", "pink");

            //子代：亲儿子
            var jqOtherLi = $("ul>li");
            jqOtherLi.css("background", "red");
        });
    </script>
</head>
<body>
<ul>
    <li>111</li>
    <li>222</li>
    <li>333</li>
    <ol>
        <li>aaa</li>
        <li>bbb</li>
        <li>ccc</li>
    </ol>
</ul>
</body>
</html>
```

效果：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182336389.png)

### 3、基本过滤选择器

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182336935.png)

解释：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182337545.png)

举例：

```html
    <script src="jquery-1.11.1.js"></script>
    <script>
        $(document).ready(function () {

            // :odd
            $("li:odd").css("background", "red");

            // :even
            $("li:even").css("background", "green");

            // :eq(index)
            $("ul li:eq(3)").css("font-size", "30px");  //设置第四个li的字体

            // :lt(index)
            $("li:lt(6)").css("font-size", "30px");

            // :gt(index)
            $(".ulList1 li:gt(7)").css("font-size", "40px");

            // :first
            $(".ulList li:first").css("font-size", "40px");

            // :last
            $("li:last").css("font-size", "40px");
        });
    </script>
```

### 4、属性选择器

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182337612.png)

### 5、筛选选择器

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182338292.png)

举例：

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <script src="jquery-1.11.1.js"></script>
    <script>
        jQuery(function () {
            var jqul = $("ul");

            //find(selector); 从jquery对象的后代中查找
            //必须制定参数，如果不指定获取不到元素。length === 0
            jqul.find("li").css("background", "pink");
            console.log(jqul.find());

            //chidlren(selector); 从jquery对象的子代中查找
            //不写参数代表获取所有子元素。
            jqul.children("li").css("background", "green");

            //eq(索引值); 从jquery对象的子代中查找该索引值的元素
            //要写该数组中的第几个。
            jqul.children().eq(0).css("background", "red");

            //next(); 该元素的下一个兄弟元素
            jqul.children().eq(0).next().css("background", "yellow");

            //siblings(selector); 该元素的所有兄弟元素
            jqul.children().eq(0).next().siblings().css("border", "1px solid blue");

            //parent(); 该元素的父元素（和定位没有关系）
            console.log(jqul.children().eq(0).parent());
        });
    </script>
</head>
<body>

<ul>
    <li>生命壹号，永不止步</li>
    <li class="box">生命壹号，永不止步</li>
    <span>生命壹号，永不止步</span>
    <li class="box">生命壹号，永不止步</li>
    <i>生命壹号，永不止步</i>
    <li>生命壹号，永不止步</li>
    <a id="box" href="#">生命壹号，永不止步</a>
    <ol>
        <li>我是ol中的li</li>
        <li>我是ol中的li</li>
        <li>我是ol中的li</li>
        <li>我是ol中的li</li>
    </ol>
</ul>

</body>
</html>
```

效果：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302182339831.png)