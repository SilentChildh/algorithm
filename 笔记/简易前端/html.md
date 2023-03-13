# html重点

## 核心

学习HTML的目的就是在你需要的地方用到符合语义的标签，把标签用“对”这才是HTML学习的目的。例如一段文字，应该使用p标签，而不是使用div标签或者其他标签。

网页语义结构良好，对于搜索引擎来说也是极为重要的一点。

## 规范

1. **标签属性用引号。**
2. **标签以及属性小写**

# HTML的基本标签

## html标签

整个网页从<html>开始，到</html>结束

## head标签

代表页面的头，定义特殊内容。**用户不可见**。

| <head>内部标签 | 说明                                                       |
| :------------- | :--------------------------------------------------------- |
| <title>        | 定义网页的标题                                             |
| <meta>         | 定义网页的基本信息（供搜索引擎）                           |
| <style>        | 定义CSS样式                                                |
| <link>         | 链接外部CSS文件或脚本文件                                  |
| <script>       | 定义脚本语言                                               |
| <base>         | 凡是body里的链接未以"/"开头的，默认都会将此bae里的链接加上 |

## body标签

代表页面的身，定义网页展示内容。**用户可见**。

# 主要body标签

| 标签名   | 英文全称         | 中文解释             |
| :------- | :--------------- | :------------------- |
| div      | division         | 分割（块元素）       |
| span     | span             | 区域（行内元素）     |
|          |                  |                      |
| p        | paragraph        | 段落                 |
| br       | break            | 换行                 |
|          |                  |                      |
| ol       | ordered list     | 有序列表             |
| ul       | unordered list   | 无序列表             |
| li       | list item        | 列表项               |
|          |                  |                      |
| h1~h6    | header1 ~header6 | 标题1~标题6          |
| hr       | horizontal rule  | 水平线               |
|          |                  |                      |
| a        | anchor           | 锚点，超链接         |
| strong   | strong           | 强调（粗体）         |
| em       | emphasized       | 强调（斜体）         |
|          |                  |                      |
| fieldset | fieldset         | 域集                 |
| legend   | legend           | 图例                 |
|          |                  |                      |
| caption  | caption          | （表格、图像等）标题 |
| thead    | table head       | 表头                 |
| tbody    | table body       | 表身                 |
| tfoot    | table foot       | 表脚                 |
| th       | table header     | 表头单元格           |
|          |                  |                      |
| td       | td               | 表身单元格           |

# 段落与文字

## 段落

| 标签      | 语义            | 说明             |
| :-------- | :-------------- | :--------------- |
| <h1>~<h6> | header          | 标题             |
| <p>       | paragraph       | 段落             |
| <br>      | break           | 换行             |
| <hr>      | horizontal rule | 水平线           |
| <div>     | division        | 分割（块元素）   |
| <span>    | span            | 区域（行内元素） |



## 文本格式化

| 标签     | 语义               | 说明 |
| :------- | :----------------- | :--- |
| <strong> | strong（加强）     | 加粗 |
| <em>     | emphasized（强调） | 斜体 |



## 行元素与行内元素

1. HTML元素根据浏览器表现形式分为两类：①块元素；②行内元素；

2. 块元素特点：

    1. 独占一行，排斥其他元素跟其位于同一行，包括块元素和行内元素；
    2. 块元素内部可以容纳其他块元素或行元素；

    **常见块元素有：h1~h6、p、hr、div等**

3. 行内元素特点：

    1. 可以与其他行内元素位于同一行；
    2. 行内内部可以容纳其他行内元素，但不可以容纳块元素，不然会出现无法预知的效果；

    **常见行内元素有：strong、em、span等。**



# 列表

| 标签 | 语义           | 说明     |
| :--- | :------------- | :------- |
| ol   | ordered list   | 有序列表 |
| ul   | unordered list | 无序列表 |
|      |                |          |

**最常用的是无序列表**

~~~html
<ul>
    <li>无序列表项</li>
    <li>无序列表项</li>
    <li>无序列表项</li>
</ul>
~~~

学习了CSS之后，无序列表列表项符号由list-style-type定义



# 表格

## 基本标签

| 标签  | 语义                          | 说明   |
| :---- | :---------------------------- | :----- |
| table | table（表格）                 | 表格   |
| tr    | table row（表格行）           | 行     |
| td    | table data cell（表格单元格） | 单元格 |

**这3者必须要有。**

~~~html
<table>
    <tr>
        <td>单元格1</td>
        <td>单元格2</td>
    </tr>
    <tr>
        <td>单元格1</td>
        <td>单元格2</td>
    </tr>
</table>

<table>和</table>标记着表格的开始和结束，<tr>和</tr>标记着行的开始和结束，在表格中包含几组<tr></tr>就表示该表格为几行。<td>和</td>标记着单元格的开始和结束。
~~~



## 强化语义（结构）标签

| 标签  | 语义         | 说明       |
| :---- | :----------- | :--------- |
| thead | table head   | 表头       |
| tbody | table body   | 表身       |
| tfoot | table foot   | 表脚       |
| th    | table header | 表头单元格 |



## 表格完整结构

表格完整结构应该包括表格标题（caption）、表头（thead）、表身（tbody）和表脚（tfoot）4部分。

表格语义化之后，使得代码更清晰和更利于后期维护。





~~~html
<table>
    <caption>表格标题</caption>
    
    <!--表头-->
    <thead>
        <tr>
            <th>表头单元格1</th>
    		<th>表头单元格2</th>
        </tr>
    </thead>
    
    <!--表身-->
    <tbody>
        <tr>
            <td>标准单元格1</td>
            <td>标准单元格2</td>
        </tr>
        <tr>
            <td>标准单元格1</td>
            <td>标准单元格2</td>
        </tr>
    </tbody>

    <!--表脚-->
    <tfoot>
        <tr>
            <td>标准单元格1</td>
            <td>标准单元格2</td>
        </tr>
    </tfoot>
</table>
~~~



## 合并行、列

### 行

~~~html
<body>
    <table>
        <!--第1行-->
        <tr>
            <td>姓名:</td>
            <td>小明</td>
        </tr>
        
        <!--第2行-->
        <tr>
            <td rowspan="2">喜欢水果:</td>
            <td>苹果</td>
        </tr>

        <!--第3行-->
        <tr>
            <td>香蕉</td>
        </tr>
    </table>
</body>
</html>
~~~

![](https://imgconvert.csdnimg.cn/aHR0cDovL3d3dy5sdnllc3R1ZHkuY29tL0FwcF9pbWFnZXMvbGVzc29uL2hqLzYtNS0xLnBuZw?x-oss-process=image/format,png)

### 列

~~~html
<body>
    <table>
        <!--第1行-->
        <tr>
            <td colspan="2">绿叶学习网精品教程</td>
        </tr>

        <!--第2行-->
        <tr>
            <td>HTML教程</td>
            <td>CSS教程</td>
        </tr>

        <!--第3行-->
        <tr>
            <td>jQuery教程</td>
            <td>SEO教程</td>
        </tr>
    </table>
</body>
~~~

![](https://imgconvert.csdnimg.cn/aHR0cDovL3d3dy5sdnllc3R1ZHkuY29tL0FwcF9pbWFnZXMvbGVzc29uL2hqLzYtNi0xLnBuZw?x-oss-process=image/format,png)



# 图像

## 自闭合标签

在HTML中，图像标签为<img>。<img>是一个自闭合标签。[img标签](http://www.lvyestudy.com/les_hj/hj_7.1.aspx)只需要掌握3个属性就可以了：src、alt、title。

## 语法

~~~html
<img src="图片地址" alt="图片描述（给搜索引擎看）" title="图片描述（给用户看）"/>
~~~



## 属性

| 属性  | 说明                       |
| :---- | :------------------------- |
| src   | 图像的文件地址             |
| alt   | 图片显示不出来时的提示文字 |
| title | 鼠标移到图片上的提示文字   |

src和alt这两个属性是img标签必不可少的属性。



# 链接

## 语法

~~~html
<a href="链接地址" target="目标窗口的打开方式"/>
~~~

## 属性

| target属性值 | 说明                           |
| :----------- | :----------------------------- |
| _self        | 默认方式，即在当前窗口打开链接 |
| _blank       | 在一个全新的空白窗口中打开链接 |

## 分类

1. 外部链接
2. 内部链接
    1. 内部页面链接；
    2. 锚点链接；

锚点链接，就是点击某一个超链接，它就会跳到当前页面的某一部分。如下例：

~~~html
<body>
    <div>
        <a href="#music">推荐音乐</a><br />
        <a href="#movie">推荐电影</a><br />
        <a href="#article">推荐文章</a><br />
    </div>
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    <div id="music">
        <h3>推荐音乐</h3>
        <ul>
            <li>林俊杰-被风吹过的下图</li>
            <li>曲婉婷-在我的歌声里</li>
            <li>许嵩-灰色头像</li>
        </ul>
    </div>
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    <div id="movie">
        <h3>推荐电影</h3>
        <ul>
            <li>蜘蛛侠系列</li>
            <li>钢铁侠系统</li>
            <li>复仇者联盟</li>
        </ul>
    </div>
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    ……<br />
    <div id="article">
        <h3>推荐文章</h3>
        <ul>
            <li>朱自清-荷塘月色</li>
            <li>余光中-乡愁</li>
            <li>鲁迅-阿Q正传</li>
        </ul>
    </div>
</body>
~~~

只要我们点击“推荐音乐”、“推荐电影”和“推荐文章”这三个超链接，滚动条就会滚动到相应的版块。



# 表单

## 基本标签

表单标签共有4个：<input>、<textarea>、<select>和<option>。其中<select>和<option>是配合使用的。

## input

~~~html
<input type="表单类型"/>
~~~

## textarea

~~~html
<textarea rows="行数" cols="列数">多行文本框内容</textarea>
~~~

## 文本框的选择

单行文本框和密码文本框使用<input>标签，而多行文本框使用<textarea>标签。

### 单行文本框

~~~html
<input type="text" value="默认文字" size="文本框长度" maxlength="最多输入字符数"/>
~~~

### 密码文本框

~~~html
<input type="password">
~~~

### 多行文本框

~~~html
<textarea rows="行数" cols="列数">多行文本框内容</textarea>
~~~

## select与option

~~~html
<select multiple="mutiple" size="可见列表项的数目">
    <option value="选项值" selected="selected">选项显示的内容</option>
    <option value="选项值">选项显示的内容</option>
</select>
~~~

## 关于name-value

一般都在表单中定义name，对于value一般时选择时指定value，若是文本则不写value，交由默认处理（value=用户输入）。

name-value相当于键值对，适用于提交数据给后台使用。

# 浮动框架

## 认识

可以完全由设计者定义宽度和高度，并且可以放置在一个网页的任何位置

## 语法

~~~html
<iframe src="浮动框架的源文件" width="浮动框架的宽" height="浮动框架的高" scrolling="取值"></iframe>
~~~

1. src属性是iframe的必须属性，它定义浮动框架页面的源文件地址。

2. 浮动框架的宽度和高度都是以像素为单位。width和height这2个都是可选属性。

    

3. | scrolling属性值 | 说明                                                         |
    | :-------------- | :----------------------------------------------------------- |
    | auto            | 默认值，整个表格在浏览器页面中左对齐                         |
    | yes             | 总是显示滚动条，即使页面内容不足以撑满框架范围，滚动条的位置也预留 |
    | no              | 在任何情况下都不显示滚动条                                   |



# html5

## 新增标签

- `header` --- 头部标签
- `nav` --- 导航标签
- `article` --- 内容标签
- `section` --- 块级标签
- `aside` --- 侧边栏标签
- `footer` --- 尾部标签

![](https://img-blog.csdnimg.cn/20191229132829821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXhpbnU=,size_16,color_FFFFFF,t_70)

## 允许部分属性的属性值省略

| 省略形式 | 等价于               | 作用           |
| -------- | -------------------- | -------------- |
| checked  | checked=checked"     | 默认选中       |
| readonly | readonly="readonly"  | 只读，不可修改 |
| defer    | defer="defer"        |                |
| ismap    | ismap="ismap"        |                |
| nohref   | nohref="nohref"      |                |
| noshade  | noshade="noshade"    |                |
| nowrap   | nowrap="nowrap"      |                |
| selected | selected="selected"  |                |
| disabled | disabled="disableci" | 只读且不可选中 |
| multiple | multiple="multiple"  |                |
| noresize | noresize="disabled"  |                |

![](https://img-blog.csdnimg.cn/20191229133014181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXhpbnU=,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191229133048182.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXhpbnU=,size_16,color_FFFFFF,t_70)



# 运用html

## id与class

### 认识

id和class是HTML元素中两个最基本的公共属性。

一般情况下，id和class都用来选择（定位）元素，以便进行CSS操作或者JavaScript操作。

### id

id属性具有唯一性，也就是说在一个页面中相同的id只允许出现一次。

W3C建议，对 于页面关键的结构或者大结构，我们才使用id。所谓的关键结构，指的是诸如LOGO、导航、 主体内容、底部信息栏等结构

### class

如果你要为两个或者两个以上元素定义相同的样式，建议使用class属性。因为这样可以 减少大量的重复代码。

### 总结

1. 对于页面关键结构，建议使用id；
2. 对于小地方，建议 使用class。

就算我们不需要对关键结构进行CSS操作或者JavaScript操作，也建议加上id.以便搜索引擎识别页面结构。



## 浏览器标题栏小图标

在head标签添加一个link标签即可

~~~html
<link rel="icon" href="favicon.icon"/>
~~~

1. rel的取值是固定形式
2. href属性取值为小图标的地址。**相对路径**
3. 小图标格式是.ico（网站：在线icon）

## 尽量使用语义化标签

div和span没有任何语义,正是因为没有语义，这两个标签一般都是配合CSS来定义元素 样式的。

## 标题语义化

1. 一个页面只能有一个h1标签。
2. hl ~ h6之间不要断层。
3. 不要用h1 ~ h6来定义样式。
4. 不要用div来代替h1 ~ h6.

## 图片语义化

1.  alt属性和title属性。

    1. alt与src一定要写
    2. title尽量写

2. figure 元素和 figcaption 元素。

    在实际开发中， 对于“图片+图注”效果，我们都建议使用figure和figcaption这两个元素来实现，从而使 得页面的语义更加良好。

    ~~~html
    <figure>
        <img src="" alt=""/>
        <figcaption></figcaption>
    </figure>
    ~~~

    1. figure元素用于包含图片和图注，
    2. figcaption元素用于表示图注文字。

## 表格语义化

在实际开发中，我们不建议使用表格布局，应该使用浮动布局或者定位布局。虽然表格拿来做布局的方式被抛弃了，但是这并没有说明表格就一无是处了。

~~~html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <style type="text/css">
        table, thead, tbody, tfoot, th, td 
        {
            border: 1px dashed gray;
        }
    </style>
</head>
<body>
    <table>
        <caption>考试成绩表</caption>
        <thead>
            <tr>
                <th>姓名</th>
                <th>语文</th>
                <th>英语</th>
                <th>数学</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>小明</td>
                <td>80</td>
                <td>80</td>
                <td>80</td>
            </tr>
            <tr>
                <td>小红</td>
                <td>90</td>
                <td>90</td>
                <td>90</td>
            </tr>
            <tr>
                <td>小杰</td>
                <td>100</td>
                <td>100</td>
                <td>100</td>
            </tr>
        </tbody>
        <tfoot>
            <tr>
                <td>平均</td>
                <td>90</td>
                <td>90</td>
                <td>90</td>
            </tr>
        </tfoot>
    </table>
</body>
</html>
~~~

对于theadx tbody和tfoot这3个标签，不一定能够全部都用得上，例如tfoot就很少用。 一般情况下，我们都是根据实际需要来使用这3个标签的。



## 表单语义化

### label 标签。

语法：`<label for=""> 说明性文字 </label>`。其中for属性值为所关联的**表单元素的id**

label标签的for属性有两个作用:

1. 语义上绑定了 label元素和表单元素。
2. 增强了鼠标可用性。也就是说我们点击label中的文本时，其所关联的表单元素也会 获得焦点。

对比使用：

~~~html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
</head>
<body>
    <div>
        <input id="Radio1" type="radio"/>单选框
        <input id="Checkbox1" type="checkbox" />复选框
    </div>
    <hr />
    <div>
        <input id="rdo" name="rdo" type="radio"/><label for="rdo">单选框</label>
        <input id="cbk" name="cbk" type="checkbox" /><label for="cbk">复选框</label>
    </div>
</body>
</html>
~~~

两种关联方式：

~~~html
<input id="cbk" type="checkbox" /><label for="cbk"> 复选框 </label> 

<label>复选框<input id="cbk" type="checkbox"/></label>
~~~

1. 需要for指定id
2. 不需要for指定，但指定元素需要成为<lebel>的子元素

### fieldset 标签和 legend 标签

使用fieldset和legend标签有两个作用

1. 增强表单的语义。
2. 可以定义fieldset元素的disabled属性来禁用整个组中的表单元素。
3. 美观

~~~html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
 
<head>
    <title></title>
</head>
 
<body>
    <form action="index.aspx" method="post">
        <fieldset>
            <legend>登录绿叶学习网</legend>
            <p>
                <label for="name"> 账号：</1abel><input type="text" id="name" name="name" />
            </p>
            <label for="pwd"> 密码：</label><input type="password" id="pwd" name="pwd" />
            </p>
            <input type="checkbox" id="remember-me" name="remember-me" /> <label for="remember-me"> 记住我 </label>
            <input type="submit" value="登录" />
 
        </fieldset>
    </form>
</body>

</html>
~~~



## 其他细节

1. 换行符`<br/>`，应该用在`<p></p>`标签内部
2. 尽量使用无序列表
3. 对于<strong>和<em>，用于增强语义，而不是增加样式
4. <del>与<ins>，是配合使用的。但实际上对于样式还是由css来搞定。
5. img标签在需要识别时运用（如图标可以被点击），不需要被识别时，用css显示背景图片即可。
6. 在表单中可以使用input的type属性中的button，但其他位置最好还是直接使用button标签