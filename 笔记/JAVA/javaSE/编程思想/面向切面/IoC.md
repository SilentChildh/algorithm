# IoC

1. 需要创建对应的接口
2. 接口的实现类
3. 需要工厂类

# 基于Xml

## 创建javabean对象

1. id表示命名
2. class表示类路径



## 注入javabean的属性

1. 使用set方法。在xml文件中使用propertiy标签name、value属性
2. 使用构造器。在xml文件中使用constructor-arg标签和name、value属性

需要注意的地方：

1. 注入的字面量
    1. 注入的属性是null
    2. 包含特殊符号（CDATA）
2. 注入其他javabean
    1. property标签内的属性是name和ref，ref指向另一个bean的id
    2. 也可以直接在property标签下创建子标签，即在编写一个bean标签
3. 

