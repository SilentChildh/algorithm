# 数据类型

## 基本数据类型

1. Number
2. String
3. Boolean

## 特殊数据类型

1. null
2. undefined
3. 转义字符

# typeof运算符

typeof运算符用于返回它的操作数当前所容纳的数据的类型，这对于判断一个变量是否已被定义特别有用。

例如：typeof(1)返回值是number，typeof("javascript")返回值是string。



# [let var const](http://t.csdn.cn/pxSR2)



# 类型转换

## 字符串---数值

在JavaScript中，将字符串型数据转换为数值型数据有parseInt()和parseFloat()这2种方法。其中，parseInt()可以将字符串转换为整型数据；parseFloat()可以将字符串转换为浮点型数据。

## 数值----字符串

在JavaScript中，将数值型数据（整型或浮点型）转换为字符串，都是使用toString()方法。



# 注意

1. JavaScript是严格区分大小写的。例如str和Str这是两个完全不同的变量。
2. 可不写分号，但实际上尽量写



# 函数

定义函数必须使用function关键字。

## 不指定函数名

~~~javascript
function(参数1,参数2,….,参数n)
{
    //函数体语句
}
~~~

## 指定函数名

~~~javascript
function 函数名(参数1,参数2,….,参数n)
{
    //函数体语句
    return 表达式;
}
~~~

1. 定义函数必须使用function关键字来定义。
2. 函数名必须是唯一的，尽量通俗易懂，并且跟你定义的代码有关。
3. 函数可以使用return语句将某个值返回，也可以没有返回值。
4. 参数是可选的，可以不带参数，也可以带多个参数。如果是多个参数的话，参数之间要用英文逗号隔开。



## 函数调用

1. 简单调用；
2. 在表达式中调用；
3. 在事件响应中调用；
4. 通过链接调用；

## arguments

1. argument是JavaScript中的一个关键字，用于指向调用者传入的所有参数。

    ~~~javascript
    function example(x){
        alert(x); //1
        alert(arguments.length); //3
        for(var i=0; i<arguments.length; i++){
            alert(arguments[i]);  //1,2,3   
        }      
    }
    example(1,2,3);
    ~~~

    

2. 即使不定义参数，也可以取到调用者的参数。

    ~~~javascript
    function abs() {
        if (arguments.length === 0) {
            return 0;
        }
        var x = arguments[0];
        return x >= 0 ? x : -x;
    }
     
    abs(); // 0
    abs(10); // 10
    abs(-9); // 9
    ~~~

    

3. 由于JavaScript函数允许接收任意个参数，所以不得不用arguments来获取函数定义a以外的参数。

    ~~~javascript
    function exm(a) {
        var rest = [];
        if (arguments.length > 1) {
            for (var i = 1; i<arguments.length; i++) {
                rest.push(arguments[i]);
            }
        }
    }
    ~~~

## 内置函数

### isFinite()函数

在JavaScript中，isFinite()函数用来确定某一个数是否是一个有限数值。

语法：`isFinite(number)`

说明：

number参数是必选的，可以是任意的数值，例如整型、浮点型数据。

如果该参数为非数字、正无穷数和负无穷数，则返回false；否则的话，返回true。如果是字符串类型的数字，就会自动转化为数字型。

### isNaN()函数

语法：`isNaN(参数)`

这里的参数可以是任何类型的数据，例如数字型、字符串型、日期时间型等。不过得注意一点，当参数是“字符串类型的数字”，就会自动转换为数字型。

~~~
123 //这不是NaN值
"123"  //这也不是NaN值，因为“字符串类型的数字”会被自动转换为数字型
"abc123"  //这是NaN值
~~~

### parseInt()函数和parseFloat()函数

在JavaScript中，将字符串型数据转换为数值型数据有parseInt()和parseFloat()这2种方法。其中，parseInt()可以将字符串转换为整型数据；parseFloat()可以将字符串转换为浮点型数据。

语法：

1. `parseInt()`  //将字符串型转换为整型
2. `parseFloat()`  //将字符串型转换为浮点型

### escape函数和unescape函数

escape()函数是编码,主要作用就是对字符串进行编码，以便它们能在所有计算机上可读。

语法：`escape(charString)`

charString是必选参数，表示要进行编码的字符串或文字。escape()函数返回一个包含charString内容的字符串值（Unicode格式）。除了个别如“*@”之类的符号外，其余所有空格、标点符号以及其他非ASCII字符均可用“%xx”这种形式的编码代替，其中xx等于表示该字符的十六进制数。


unescape()函数是解码。

语法：`unescape(charString)`

charString是必选参数，表示要进行解码的字符串。unescape()函数返回指定值的ASCII字符串。与escape()函数相反，unescape()函数返回一个包含charString内容的字符串值，所有以“%xx”十六进制形式编码的字符都用ASCII字符集中等价的字符代替。




# 字符串

~~~javascript
//长度
字符串名.length;

//匹配返回字符串
如果存在的话，返回要检索的字符串；如果不存在的话，返回null。
stringObject.match(字符串)    //匹配字符串;
stringObject.match(正则表达式)  //匹配正则表达式

//匹配返回索引
返回的是子字符串的起始位置，如果没有找到任何匹配的子串，则返回-1。
stringObject.search(字符串)      //检索字符串;
stringObject.search(正则表达式)  //检索正则表达式

//查找首次出现
返回某个指定的字符串值在字符串中首次出现的位置。
stringObject.indexOf(字符串)

//替换
常常用于在字符串中用一些字符替换另一些字符，或者替换一个与正则表达式匹配的子串。
stringObject.replace(原字符,替换字符)   
stringObject.replace(正则表达式,替换字符)  //匹配正则表达式

//获取字符
来获取字符串中的某一个字符
stringObject.charAt(n)//n是数字，表示字符串中第几个字符

//转换大小写
字符串名. toLowerCase()    //将大写字符串转换为小写字符串
字符串名. toUpperCase()    //将小写字符串转换为大写字符串

//拼接
字符串1.concat(字符串2,字符串3,…,字符串n);//第一种
使用“+”运算符就可以了//第二种

//比较
如果字符串1小于字符串2，则返回小于0的数字；
如果字符串1大于字符串2，则返回数字1；
如果字符串1等于字符串2，则返回数字0；	
字符串1.localeCompare(字符串2);

//分割
把一个字符串分割成字符串数组
分割符可以是一个字符、多个字符或一个正则表达式。分割符并不作为返回数组元素的一部分
字符串.split(分割符)

//获取子串
字符串.substring(开始位置,结束位置)
~~~

# 日期

## 创建

~~~javascript
//1
var 日期对象名 = new Date();
//2
var 日期对象名 = new Date(日期字符串);
~~~

## 函数

| 方法             | 说明                                          |
| :--------------- | :-------------------------------------------- |
| getFullYear()    | 返回一个表示年份的4位数字                     |
| getMonth()       | 返回值是0（一月）到11（十二月）之间的一个整数 |
| getDate()        | 返回值是1~31之间的一个整数                    |
| getHours()       | 返回值是0~23之间的一个整数，来表示小时数      |
| getMinutes()     | 返回值是0~59之间的一个整数，来表示分钟数      |
| getSeconds()     | 返回值是0~59之间的一个整数，来表示秒数        |
|                  |                                               |
| setFullYear()    | 可以设置年、月、日                            |
| setMonth()       | 可以设置月、日                                |
| setDate()        | 可以设置日数                                  |
|                  |                                               |
| setHours()       | 可以设置时、分、秒、毫秒                      |
| setMinutes()     | 可以设置分、秒、毫秒                          |
| setSeconds()     | 可以设置秒、毫秒                              |
|                  |                                               |
| toString()       | 将日期时间转换为普通字符串                    |
| toUTCString()    | 将日期时间转换为世界时间（UTC）格式的字符串   |
| toLocaleString() | 将日期时间转换为本地时间格式的字符串          |
|                  |                                               |



# 数组

## 创建

1. 新建一个长度为0的数组：`var myArr = new Array();`

2. 新建长度为n的数组:

    ~~~javascript
    var myArr = new Array(3);
    myArr[0]="HTML";
    myArr[1]="CSS";
    myArr[2]="JavaScript";
    ~~~

3. 新建指定长度的数组，并赋值：`var myArr = new Array(1,2,3,4);`

## 属性

在Array对象中有3个属性，分别是length、constructor和prototype

## 方法

| 方法       | 说明                     |
| :--------- | :----------------------- |
| slice()    | 获取数组中的某段数组元素 |
| unshift()  | 在数组开头添加元素       |
| push()     | 在数组末尾添加元素       |
| shift()    | 删除数组中第一个元素     |
| pop()      | 删除数组最后一个元素     |
| toString() | 将数组转换为字符串       |
| join()     | 将数组元素连接成字符串   |
| concat()   | 多个数组连接为字符串     |
| sort()     | 数组元素正向排序         |
| reverse()  | 翻转                     |
|            |                          |

# Math

| 方法     | 说明                       |
| :------- | :------------------------- |
| max(x,y) | 返回x和y中的最大值         |
| min(x,y) | 返回x和y中的最小值         |
| pow(x,y) | 返回x的y次幂               |
| abs(x)   | 返回数的绝对值             |
| round(x) | 把数四舍五入为最接近的整数 |
| random() | 返回0~1之间的随机数        |
| ceil(x)  | 对一个数进行上舍入         |
| floor(x) | 对一个数进行下舍入         |



# window对象（用于窗口）

## 方法

| 方法                           | 说明                           |
| :----------------------------- | :----------------------------- |
| open()、close()                | 打开窗口、关闭窗口             |
| resizeBy()、resizeTo()         | 改变窗口大小                   |
| moveBy()、moveTo()             | 移动窗口                       |
| setTimeout()、clearTimeout()   | 设置或取消“一次性”执行的定时器 |
| setInterval()、clearInterval() | 设置或取消“重复性”执行的定时器 |
| window.history.back()          | 在历史会话中向后退一页         |
| window.alert()                 | 提示（具体见下对话框）         |
| window.confirm()               | 确认（具体见下对话框）         |





# document对象（用于页面）

## 属性

| 属性             | 说明                      |
| :--------------- | :------------------------ |
| title            | 文档标题，即title标签内容 |
| URL              | 文档地址                  |
| fileCreateDate   | 文档创建日期              |
| fileModifiedDate | 文档修改时间（精确到天）  |
| lastModified     | 文档修改时间（精确到秒）  |
| fileSize         | 文档大小                  |
| fgColor          | 定义文档的前景色          |
| bgColor          | 定义文档的背景色          |
| linkColor        | 定义“未访问”的超链接颜色  |
| alinkColor       | 定义“被激活”的超链接颜色  |
| vlinkColor       | 定义“访问过”的超链接颜色  |

## 方法

| 方法                         | 说明                                       |
| :--------------------------- | :----------------------------------------- |
| document.write()             | 输入文本到当前打开的文档                   |
| document.writeIn()           | 输入文本到当前打开的文档，并添加换行符“\n” |
| document.getElementById()    | 获取某个id值的元素                         |
| document.getElementsByName() | 获取某个name值的元素，用于表单元素         |



## cookie

~~~js
// 创建/修改/删除（没有就创建，有就覆盖）
document.cookie="username=John Doe; expires=Thu, 18 Dec 2043 12:00:00 GMT; path=/";

// 读取
var x = document.cookie;// 返回由所有cookie组成的字符串
~~~

### 设置

~~~js
function setCookie(cname,cvalue,exdays)
{
  var d = new Date();
  d.setTime(d.getTime()+(exdays*24*60*60*1000));
  var expires = "expires="+d.toGMTString();
  document.cookie = cname + "=" + cvalue + "; " + expires;
}
~~~

### 获取

~~~js
function getCookie(cname)
{
  var name = cname + "=";
  var ca = document.cookie.split(';');
  for(var i=0; i<ca.length; i++) 
  {
    var c = ca[i].trim();
    if (c.indexOf(name)==0) return c.substring(name.length,c.length);
  }
  return "";
}
~~~



# DOM对象（用于节点）

DOM对象是通过document获得的节点

## 节点属性

| 属性            | 说明                         |
| :-------------- | :--------------------------- |
| parentNode      | 获取当前节点的父节点         |
| childNodes      | 获取当前节点的子节点集合     |
| firstChild      | 获取当前节点的第一个子节点   |
| lastChild       | 获取当前节点的最后一个子节点 |
| previousSibling | 获取当前节点的前一个兄弟节点 |
| nextSibling     | 获取当前节点的后一个兄弟节点 |
| attributes      | 元素的属性列表               |



## 节点方法

获取节点：

1. getElementById()；类似于css种的id选择器
2. getElementsByName()；用于获取表单元素。少用。
3. getElementByTagName()。少用。

创建节点：

~~~javascript
var e = document.createElement("元素名");
var txt = document.createTextNode("元素内容");
e.appendChild(t);    //把元素内容插入元素中去
~~~

插入节点：

1. 使用obj.appendChild(``new``)方法把新的节点插入到当前节点的“内部”
2. 使用obj.insertBefore(new,ref)方法
    1. obj表示父节点；
    2. new表示新的子节点；
    3. ref指定一个子节点，在这个节点前插入新的节点。

删除节点：

1. obj.removeChild(oldChild);删除当前节点下的某个子节点

复制节点：

1. obj.cloneNode(bool)
    1. 1或true：表示复制节点本身以及复制该节点下的所有子节点；
    2. 0或false：表示仅仅复制节点本身，不复制该节点下的子节点；

替换节点：

1. obj.replaceChild(new,old)
    1. obj，表示被替换节点的父节点；
    2. new，表示替换后的新节点；
    3. old，需要被替换的旧节点。



## inner

在JavaScript中，我们可以使用innerHTML和innerText这2个属性很方便地获取和设置某一个元素内部子元素或文本。

innerHTML属性被多数浏览器所支持，而innerText只能被IE、chrome等支持而不被Firefox支持。



## 操作css

```
obj.style.属性名;
```

# javascript对话框

不仅可以被window调用，还可以写javascript

## alert()

该对话框只是用于提示，并不能对JavaScript脚本产生任何影响

语法：`alert(message)`

message是必选参数，即提示框的信息，这是一个字符串。alert()方法没有返回值。

## confirm()

一般用于确认信息，它只有一个参数，返回值为true或false

语法:`confirm(message)`

message是必选项，表示弹出对话框中的文本，这是一个字符串。如果用户点击“确定”，则confirm()返回true。如果用户点击“取消”按钮，则confirm()返回false。confirm()方法往往都是和按钮结合使用。



# 事件

## 认识

用户按下鼠标或者提交表单，甚至在页面移动鼠标时，事件都会出现。

## 调用

1. 在script标签中调用；也就是在<script></script标签内部调用事件。

    ~~~html
    <body>
        <input id="btn" type="button" value="提交" />
        <script type="text/javascript">
            var e = document.getElementById("btn");
            e.onclick = function () {
                alert("绿叶学习网");
            }
        </script>
    </body>
    ~~~

2. 在元素中调用；在元素的某一个属性中直接编写JavaScript程序或调用JavaScript函数，这个属性指的是元素的“事件属性”。

    ~~~html
    <head>
        <title></title>
        <script type="text/javascript">
            function alertMessage()
            {
                alert("绿叶学习网");
            }
        </script>
    </head>
    <body>
        <input type="button" onclick="alertMessage()" value="按钮"/>
    <body>
    </html>
    ~~~

    第2种方法不需要使用getElementById()等方法来获取DOM，然后才调用函数或方法。因为它是直接在JavaScript元素中调用的。

## 五种事件

### 鼠标

| 事件        | 说明         |
| :---------- | :----------- |
| onclick     | 鼠标单击事件 |
| ondbclick   | 鼠标双击事件 |
| onmouseover | 鼠标移入事件 |
| onmouseout  | 鼠标移出事件 |
| onmousemove | 鼠标移动事件 |
| onmousedown | 鼠标按下事件 |
| onmouseup   | 鼠标松开事件 |

### 键盘

| 方法       | 说明                             |
| :--------- | :------------------------------- |
| onkeydown  | 按下键事件（包括数字键、功能键） |
| onkeypress | 按下键事件（只包含数字键）       |
| onkeyup    | 放开键事件（包括数字键、功能键） |

三个事件的执行顺序如下：onkeydown -> onkeypress ->onkeyup。

### 表单

| 事件     | 说明         |
| :------- | :----------- |
| onfocus  | 获取焦点事件 |
| onblur   | 失去焦点事件 |
| onchange | 状态改变事件 |
| onselect | 选中文本事件 |

### 编辑

| 方法    | 说明     |
| :------ | :------- |
| oncopy  | 复制事件 |
| oncut   | 剪切事件 |
| onpaste | 粘贴事件 |

这3个事件都对应有一个“onbeforeXXX”事件，表示发生在该事件之前的事件。

## 页面

| 方法     | 说明                   |
| :------- | :--------------------- |
| onload   | 页面加载事件           |
| onresize | 页面大小事件           |
| onerror  | 页面或图片加载出错事件 |





# 发送请求

前端向后端发送请求

路径从`/项目名`开始

~~~javascript
// 1 见名知意，故以这个最好
document.location.href = "";
// 2
document.location = "";
// 3
window.location.href = "";
// 4
window.location = "";
~~~

