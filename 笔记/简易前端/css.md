[csdn](http://t.csdn.cn/E2vG2)

# css的引入方式

## 外部样式表

就是把CSS代码和HTML代码都单独放在不同文件中，然后在HTML文档中使用link标签来引用CSS样式表

~~~html
<!DOCTYPE html>
 <html xmlns="http://www.w3.org/1999/xhtml">
 <head> 
 <title></title>
    <!--在HTML页面中引用文件名为index的css文件-->
<link href="index.css" rel="stylesheet" type="text/css" />
</head>
<body>   
     <div></div>
</body>
</html>
~~~

## 内部样式表

指的就是把CSS代码和HTML代码放在同一个文件中

~~~html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>  
    <title></title>    
    <!--这是内部样式表，CSS样式在style标签中定义-->    
    <style type="text/css">
        p{
            color:Red;
        }      
    </style>
</head>
    <body>   
        <p>绿叶学习网</p>  
        <p>绿叶学习网</p>
    </body>
~~~

CSS样式在<style>标签内定义，而<style>标签必须放在<head>标签内。



## 内敛样式表

也是把CSS代码和HTML代码放在同一个文件中，但是跟内部样式表不同，CSS样式不是在<style></style>标签对中定义，而是在标签的style属性中定义。

~~~html
<!DOCTYPE html> 
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
</head>
<body>
    <p style="color:Red; ">绿叶学习网</p>
    <p style="color:Red; ">绿叶学习网</p>
    <p style="color:Red; ">绿叶学习网</p>
</body>
</html>
~~~



# id与class

- 一个标签可以同时定义多个class；
- d也可以写成name，区别在于name是HTML中的标准，而id是XHTML中的标准，现在网页的标准都是使用id，所以大家尽量不要用name属性；



# 选择器

## 元素选择器

元素选择器，就是“选中”相同的元素，然后对相同的元素设置同一个CSS样式。

~~~html
元素符号{
	属性:值;
	属性:值;
}
~~~

## id选择器

我们可以为元素设置一个id，然后针对这个id的元素进行CSS样式操作

~~~
#id名称{
	属性:值;
	属性:值;
}
~~~

id名前面必须要加上前缀“#”，否则该选择器无法生效。id名前面加上“#”，表明这是一个id选择器。

## class选择器

class选择器，也就是“类选择器”。我们可以对“相同的元素”或者“不同的元素”设置一个class（类名），然后针对这个class的元素进行CSS样式操作。

~~~
.类名{
	属性:值;
	属性:值;
}
~~~

class名前面必须要加上前缀“.”（英文点号），否则该选择器无法生效。类名前面加上“.”，表明这是一个class选择器。

## 子元素选择器

子元素选择器，就是选中某个元素下的子元素，然后对该子元素设置CSS样式。

该选择器实际上就是上面三种选择器的组合使用

~~~
元素名/#id名/.类名 元素名/#id名/.类名{
	属性:值;
}
~~~

父元素与子元素必须用空格隔开，从而表示选中某个元素下的子元素。

## 相邻选择器

相邻选择器，就是选中该元素的下一个兄弟元素，在这里注意一点，相邻选择器的操作对象是该元素的同级元素。

还是选择器的组合

~~~
...+...{
	属性:值;
}
~~~

## 群组选择器

~~~
..., ..., ..., ...{
	属性:值;
}
~~~

对于群组选择器，两个选择器之间必须用“,”（英文逗号）隔开，不然群组选择器无法生效。