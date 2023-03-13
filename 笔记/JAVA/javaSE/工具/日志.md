

## Logger的级别



| 类型    | 说明                 |
| ------- | -------------------- |
| SEVERE  | 严重                 |
| WARNING | 警告                 |
| INFO    | 信息                 |
| CONFIG  | 配置                 |
| FINE    | 良好                 |
| FINER   | 较好                 |
| FINEST  | 最好                 |
| ALL     | 开启所有级别日志记录 |
| OFF     | 关闭所有级别日志记录 |





## 创建

```java
Logger.getGlobal();//获得全局Logger
Logger.getGlobal().info("log test");//打印全局日志信息

public static Logger getLogger(String name);//返回指定名字的Logger
```



## logging包下的各类

Logger记录的日志消息会被转发到已注册的Handler对象，handler对象可以将消息发送到：控制台，文件，网络等等。

Handler类(抽象类)：主要用于转发日志消息

Hanlder类下有2个子类：MemoryHandler、StreamHandler。

StreamHandler下有3个子类：ConsoleHandler(将日志消息打印到控制台)、FileHandler(将日志消息输出到文件)、SocketHandler(将日志发送到网络中的某个主机)。具体详情，查看API文档。

Formatter类(抽象类)：主要用于格式化日志记录消息。

有2个子类：SimpleFormatter(纯文本形式), XmlFormatter(XML形式)



## 配置文件

在D:\java\jdk8\jre\lib中存在文件Default Logging Configuration File。

### 默认Handler

`handlers= java.util.logging.ConsoleHandler`

以下配置可同时在两个终端打印日志

`handlers= java.util.logging.FileHandler, java.util.logging.ConsoleHandler`



### 默认Formatter

`java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter`

`java.util.logging.FileHandler.formatter = java.util.logging.XMLFormatter`