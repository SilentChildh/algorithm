

[掘金](https://juejin.cn/post/6967175965659103240#heading-5)

# DOM（Document Object Model）解析

## 优点

1. 允许应用程序对数据和结构做出更改
2. 访问是双向的，可以在任何时候再树中上、下导航获取、操作任意部分的数据

## 缺点

解析XML文档的需要加载整个文档来构造层次结构，消耗内存资源大。

## 应用范围

遍历能力强，常应用于XML文档需要频繁改变的服务中。

## 流程以及方法

1. DocumentBuilderFactory工厂统一管理了文件解析器，通过静态方法newInstance()得到
2. 通过DocumentBuilderFactory工厂实例调用方法newDocumentBuilder()可以得到xml文件解析器：DocumentBuilder
3. DocumentBuilder  xml解析器通过parse() 方法 加载并解析指定的xml文件
4. 最终parse() 方法返回解析好的Document实例
5. 通过Document实例 获得xml中的节点元素
6. 第五步后的具体方法如下

```java
doucument.getElementsByTagName(String tagname) 获得NodeList
nodeList.item(int index) 获得指定节点 Node

node.getAttributes() 获得节点的属性映射集合 NamedNodeMap
namedNodeMap.getLength() 获得映射集合的长度 int
namedNodeMap.item(int index) 获得指定节点 Node

node.getChildNodes() 获得节点的子结点集合 NodeList

node.getNodeType() 获得节点的类型，包括元素、注释、文本、属性...

node.getNodeName() 获得节点名
node.attr.getTextContent 获得节点值

```

## 实例

~~~java
package com.xml;

import java.io.FileInputStream;
import java.io.IOException;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;

import org.junit.jupiter.api.Test;
import org.w3c.dom.Document;
import org.w3c.dom.NamedNodeMap;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;

public class DOMTest {

    @Test
    void DOMTest() {
        // 1. 通过工厂静态方法创建一个 DocumentBuilderFactory 对象
        DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();

        try {
            // 2. 通过工厂实例（DocumentBuilderFactory 对象）创建一个 DocumentBuilder 对象
            DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();

            // 3. 通过 DocumentBuilder 的 parse() 方法加载 XML 到当前工程目录下
            Document document = documentBuilder.parse(new FileInputStream("E:\\Git\\3122004572-huanghehua\\任务\\Test\\src\\hello.xml"));

            // 4. 通过 getElementsByTagName() 方法获取所有 XML 所有节点的集合
            NodeList nodeList = document.getElementsByTagName("book");	//节点集



            int bookCnt = nodeList.getLength();
            System.err.println("一共获取到" + bookCnt +"本书");


            // 5. 遍历所有节点
            for(int i=0; i<bookCnt; i++){
                // 6. 节点集通过 item() 方法获取某个节点
                Node node = nodeList.item(i);

                // 节点通过 getAttributes() 方法获取结点所有属性 的映射
                NamedNodeMap namedNodeMap = node.getAttributes();

                // 该节点下可能存在多个属性
                for(int j=0; j<namedNodeMap.getLength(); j++){

                    // 获得 每个属性（也可以视作一个点Node）
                    Node attr = namedNodeMap.item(j);

                    // 7. 通过 getNodeName() 和 getNodeValue() 方法获取属性名和属性值
                    System.err.println(attr.getNodeName()+"---"+attr.getTextContent());//id

                }


                // 8. 通过 getChildNodes() 方法获取 子节点集
                NodeList childNodes = node.getChildNodes();


                /*  打印结果如下
                    11
                    #text---
                    #comment--- id="1" 属性名和属性值
                    #text---

                    name---天龙八部
                    #text---

                    author---金庸
                    #text---

                    year---2014
                    #text---

                    price---88
                    #text---
                    解析：
                    #text: 表示该属性是文本信息 值就是空格或换行，具体看xml文件文本是啥
                    #comment: 表示该属性是注释信息 值就是...
                    name、author...之类的就是元素， 紧接着的就是元素值...
                */
                /*System.out.println(childNodes.getLength());
                for (int j = 0; j < 12; j++) {
                    // getTextContent()方法将不包含文本/值信息中的空格
                    //System.out.println(childNodes.item(j).getNodeName()+"---" + childNodes.item(j).getTextContent());

                }*/

                for(int k=0; k<childNodes.getLength(); k++){
                    // 每一个Node点都可以通过 getNodeType()方法 做一个类型判断
                    if(childNodes.item(k).getNodeType() == Node.ELEMENT_NODE){
                        // 9. 通过 getNodeName() 和 getTextContent() 方法获取子节点名称和子节点值
                        System.out.println(childNodes.item(k).getNodeName()+"---" + childNodes.item(k).getTextContent());
                    }
                }
            }
        } catch (ParserConfigurationException | IOException | SAXException e) {
            throw new RuntimeException(e);
        }
    }
}
~~~





# SAX（Simple API for XML）解析

## 优点

1. 不需要等待所有的数据被处理，解析就可以开始
2. 只在读取数据时检查数据，不需要保存在内存中
3. 可以在某一个条件满足时停止解析，不必要解析整个文档
4. 效率和性能较高，能解析大于系统内存的文档

## 缺点

1. 解析逻辑复杂，需要应用层自己负责逻辑处理，文档越复杂程序越复杂
2. 单向导航，无法定位文档层次，很难同时访问同一文档的不同部分数据，不支持 XPath



## 流程及方法

1. 通过SAXParserFactory工厂，静态方法newInstance()获取一个 SAXParserFactory 的实例
2. 通过工厂实例调用方法newSAXParser() 获得SAXParser解析器实例
3. 再额外创建一个解析器处理器SAXParserHandler实例。（该实例需要自己创建并默认实现DefaultHandler接口）
4. 解析器调用parse(File file, DefaultHandler defaultHandler)方法，通过处理器的配合成功解析xml文件，

## 自定义SAXParserHandler

1. 必须重写三个方法

    ~~~java
    // 用于检索所属根元素、获得根元素值、将根元素值保存在内存中...
    startElement(String uri, String localName, String qName,
                             Attributes attributes);
    // 用于获取子元素值
    characters(char[] ch, int start, int length);
    // 用于将子元素值保存在内存中
    endElement(String uri, String localName, String qName);
    ~~~

2. startElement()方法在解析时会获得前标签名放在String qName；属性名、值放在Attributes attributes

3. 自定义startElement()方法时，要合理运用条件控制语句，明确清楚要获得根元素下的哪些元素值。

4. characters()方法中ch数组是整个xml文档的数据，start和length决定从文档的哪个位置截取字符，从而得到元素值（每次获得的元素值以标签为分割线（不会获取标签里的内容），所以可能获得的是空格/换行符、元素、属性、文本、...）

5. endElement()同理也会获得后标签名放在qName内。

6. 在endElement()中，合理运用条件控制语句，将characters()方法得到的值value保存在内存中。



关于一些字段

1. qName是标签名
2. attribute是标签内的属性
    1. 有方法getQName(int index)，获取的是属性名
    2. getValue(int index/String qName)，根据索引/属性名获取的是属性值



### 符合两层节点的自定义模板

多层的方法也都差不多，记住SAX时顺序读取的即可。

~~~java
public class SAXParserHandler extends DefaultHandler {
    String value = null;
    Book book = null;
    private ArrayList<Book> bookList = new ArrayList<>();
    
    public ArrayList<Book> getBookList() {
        return bookList;
    }	
	@Override
    public void startElement(String uri, String localName, String qName,
                             Attributes attributes) throws SAXException {
        //匹配根节点元素
        if(qName.equals("book")){
            //创建一个新book用于接收元素中的数据
            book = new Book();
            //循环获取根元素的所有属性
            for(int i=0; i<attributes.getLength();i++){
                //打印元素属性
                System.out.println(attributes.getQName(i)+"---"+attributes.getValue(i));
                //匹配元素属性
                if(attributes.getQName(i).equals("id")){
                    //将匹配成功的元素属性值保存到book中
                    book.setId(Integer.parseInt(attributes.getValue(i)));
                }
            }
        }
    }
	@Override
    public void endElement(String uri, String localName, String qName)
            throws SAXException {
        //匹配相对概念下的根元素
        if(qName.equals("book")){
            //匹配成功将一本书添加至集合中
            bookList.add(book);
            //重新指向null
            book = null;
        }
        //以下是匹配相对概念下的子元素的值
        else if(qName.equals("name")){
            book.setName(value);
        }
        else if(qName.equals("year")){
            book.setYear(Integer.parseInt(value));
        }
        else if(qName.equals("author")){
            book.setAuthor(value);
        }
        else if(qName.equals("price")){
            book.setPrice(Integer.parseInt(value));
        }
        else if(qName.equals("language")) {
            book.setLanguage(value);
        }
    }

    @Override
    public void characters(char[] ch, int start, int length)
        throws SAXException {
        // 获取节点值数组
        value = new String(ch, start, length);
    }
}
~~~



## 实例

~~~java
package com.xml;

import java.util.ArrayList;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;

public class SAXParserHandler extends DefaultHandler {
    String value = null;
    Book book = null;

    private ArrayList<Book> bookList = new ArrayList<>();


    public ArrayList<Book> getBookList() {
        return bookList;
    }
    /*
     * XML 解析开始
     */
    @Override
    public void startDocument() throws SAXException {
        super.startDocument();
        System.out.println("xml 解析开始");
    }

    /*
     * XML 解析结束
     */
    @Override
    public void endDocument() throws SAXException {
        super.endDocument();
        System.out.println("xml 解析结束");
    }

    /*
     * 用于定位所要解析的根元素位置
     */
    @Override
    public void startElement(String uri, String localName, String qName,
                             Attributes attributes) throws SAXException {


        //匹配节点元素
        if(qName.equals("book")){
            //创建一个新book用于接收元素中的数据
            book = new Book();

            //循环获取元素的所有属性
            for(int i=0; i<attributes.getLength();i++){
                //打印元素属性
                System.out.println(attributes.getQName(i)+"---"+attributes.getValue(i));
                //匹配元素属性
                if(attributes.getQName(i).equals("id")){
                    //将匹配成功的元素属性值保存到book中
                    book.setId(Integer.parseInt(attributes.getValue(i)));
                }
            }


        }
        // 打印不是指定元素的其他元素
        else if(!qName.equals("bookstore")){
            System.out.print("节点名："+ qName + "---");
        }
    }

    /*
     * 通过定位好的根元素位置，解析提取根元素下子元素的值
     */
    @Override
    public void endElement(String uri, String localName, String qName)
            throws SAXException {

        //匹配相对概念下的根元素
        if(qName.equals("book")){
            //匹配成功将一本书添加至集合中
            bookList.add(book);
            System.out.println("成功添加一本书");
            //重新指向null
            book = null;
        }
        //以下是匹配相对概念下的子元素的值
        else if(qName.equals("name")){

            book.setName(value);
            System.out.println(book.getName());
        }
        else if(qName.equals("year")){

            book.setYear(Integer.parseInt(value));
            System.out.println(book.getYear());
        }
        else if(qName.equals("author")){

            book.setAuthor(value);
            System.out.println(book.getAuthor());
        }
        else if(qName.equals("price")){

            book.setPrice(Integer.parseInt(value));
            System.out.println(book.getPrice());
        }
        else if(qName.equals("language")) {

            book.setLanguage(value);
            System.out.println(book.getLanguage());
        }
    }

    @Override
    public void characters(char[] ch, int start, int length)
            throws SAXException {
        super.characters(ch, start, length);

        // 获取节点值数组
        value = new String(ch, start, length);
        if(!value.trim().equals("")){
            System.out.println("节点值："+value);
        }
    }
    
    public static void main(String[] args) {
        // 1. 获取一个 SAXParserFactory 的实例
        SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();

        try {
            // 2. 通过 saxParserFactory() 获取 SAXParser 实例
            SAXParser parser = saxParserFactory.newSAXParser();

            // 3. 创建一个 saxParserHandler() 对象
            SAXParserHandler saxParserHandler = new SAXParserHandler();
            // 4. 通过 parser 的 parse() 方法来解析 XML
            parser.parse(new FileInputStream("E:\\Git\\3122004572-huanghehua\\任务\\Test\\src\\hello.xml"), saxParserHandler);

            System.out.println("共有"+ saxParserHandler.getBookList().size()+ "本书");
            for(Book book : saxParserHandler.getBookList()){
                System.out.println(book.getName());
                System.out.println("id=" + book.getId());
                System.out.println(book.getAuthor());
                System.out.println(book.getYear());
                System.out.println(book.getPrice());
                System.out.println(book.getLanguage());
            }
        } catch (ParserConfigurationException | IOException | SAXException e) {
            e.printStackTrace();
        }
    }
}
~~~

# 以上两种方法的测试xml文档

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>


    <book id="1">    <!-- id="1" 属性名和属性值 -->
        <name>天龙八部</name>
        <author>金庸</author>
        <year>2014</year>
        <price>88</price>
    </book>


    <book id="2">
        <name>鹿鼎记</name>
        <year>2015</year>
        <price>66</price>
        <language>中文</language>
    </book>


    <book id="3">
        <name>射雕英雄传</name>
        <author>金庸</author>
        <year>2016</year>
        <price>44</price>
    </book>


</bookstore>
~~~





# 失败的尝试

不应该将所有语句分类集起来，因为不能打乱顺序来操作语句。

应该要做到顺序读取，并操作。

以下的代码虽然同类型语句是有序的，但不同类型之间就乱序了，会出问题。

~~~java
package com.dao;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

/**
 * 该xml文件解析器，会顺序读取所有的字符。<br/>
 * 文档中的CRUD要保证处于根元素sql之下，但可以分散
 */
public class SAXParserSQLHandler extends DefaultHandler {
    //用于保存读取到的元素值
    private String value;
    /**
     * key 代表 四种sql语句<br/>
     * Value 用于存放同类型的多种条语句
     */
    private Map<Integer, ArrayList<String>> map;

    /**
     * 获取select语句集合
     * @return ArrayList<String> 返回查询语句集合
     */
    public ArrayList<String> getSelectArrayList() {
        return map.get(SQLSet.SELECT);
    }

    /**
     * 获取insert语句集合
     * @return ArrayList<String> 返回添加语句集合
     */
    public ArrayList<String> getInsertArrayList() {
        return map.get(SQLSet.INSERT);
    }
    /**
     * 获取update语句集合
     * @return ArrayList<String> 返回修改语句集合
     */
    public ArrayList<String> getUpdateArrayList() {
        return map.get(SQLSet.UPDATE);
    }
    /**
     * 获取delete语句集合
     * @return ArrayList<String> 返回删除语句集合
     */
    public ArrayList<String> getDeleteArrayList() {
        return map.get(SQLSet.DELETE);
    }

    @Override
    public void startElement (String uri, String localName,
                              String qName, Attributes attributes)
            throws SAXException
    {
        //找到所需根节点
        if(qName.equals("sql")) {
            map = new HashMap<>();//创建哈希map
        }
        if(qName.equals("select") && (!map.containsKey(SQLSet.SELECT))) {
            map.put(SQLSet.SELECT, new ArrayList<String>());//创建select的集合
        }
        if(qName.equals("insert") && (!map.containsKey(SQLSet.INSERT))) {
            map.put(SQLSet.SELECT, new ArrayList<String>());//创建insertt的集合
        }
        if(qName.equals("update") && (!map.containsKey(SQLSet.UPDATE))) {
            map.put(SQLSet.SELECT, new ArrayList<String>());//创建update的集合
        }
        if(qName.equals("delete") && (!map.containsKey(SQLSet.DELETE))) {
            map.put(SQLSet.SELECT, new ArrayList<String>());//创建delete的集合
        }

    }

    @Override
    public void endElement (String uri, String localName, String qName)
            throws SAXException
    {
        //找到对应的sql语句，就添加到map中，有多条同类型语句，就通过arrayList来存放
        if(qName.equals("select")) {
            map.get(SQLSet.SELECT).add(value);
        }
        else if(qName.equals("insert")) {
            map.get(SQLSet.INSERT).add(value);
        }
        else if(qName.equals("update")) {
            map.get(SQLSet.UPDATE).add(value);
        }
        else if(qName.equals("delete")) {
            map.get(SQLSet.DELETE).add(value);
        }
    }

    @Override
    public void characters (char ch[], int start, int length)
            throws SAXException
    {
        //得到元素值
        value = new String(ch, start, length);
    }

    /**
     * 主要用于标记sql语句的类型
     * @author silent_child
     * @version 1.0
     **/

    static class SQLSet {
        private static final Integer SELECT = 0;
        private static final Integer INSERT = 1;
        private static final Integer UPDATE = 2;
        private static final Integer DELETE = 3;

    }
}
~~~



# dom4j

## 解析mybatis-config.xml

~~~java
package com.powernode.dom4j;

import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.Node;
import org.dom4j.io.SAXReader;
import org.junit.Test;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 使用dom4j解析XML文件
 */
public class ParseXMLByDom4j {
    @Test
    public void testGodBatisConfig() throws Exception{

        // 读取xml，获取document对象
        SAXReader saxReader = new SAXReader();
        Document document = saxReader.read(Thread.currentThread().getContextClassLoader().getResourceAsStream("godbatis-config.xml"));

        // 获取<environments>标签的default属性的值
        Element environmentsElt = (Element)document.selectSingleNode("/configuration/environments");
        String defaultId = environmentsElt.attributeValue("default");
        System.out.println(defaultId);

        // 获取environment标签
        Element environmentElt = (Element)document.selectSingleNode("/configuration/environments/environment[@id='" + defaultId + "']");

        // 获取事务管理器类型
        Element transactionManager = environmentElt.element("transactionManager");
        String transactionManagerType = transactionManager.attributeValue("type");
        System.out.println(transactionManagerType);

        // 获取数据源类型
        Element dataSource = environmentElt.element("dataSource");
        String dataSourceType = dataSource.attributeValue("type");
        System.out.println(dataSourceType);

        // 将数据源信息封装到Map集合
        Map<String,String> dataSourceMap = new HashMap<>();
        dataSource.elements().forEach(propertyElt -> {
            dataSourceMap.put(propertyElt.attributeValue("name"), propertyElt.attributeValue("value"));
        });

        dataSourceMap.forEach((k, v) -> System.out.println(k + ":" + v));

        // 获取sqlmapper.xml文件的路径
        Element mappersElt = (Element) document.selectSingleNode("/configuration/environments/mappers");
        mappersElt.elements().forEach(mapper -> {
            System.out.println(mapper.attributeValue("resource"));
        });
    }
}
~~~

## 关于document对象

~~~java
// 获取根节点
Element rootElt = document.getRootElement();
String rootEltName = rootElt.getName();// 获取根节点的名字

// 通过document对象获取节点
String xPath = "";// 用于定位元素标签位置。"/根元素名/..."表示定位根元素位置,定位其他元素
Node node = document.selectSingleNode(xPath);
Element environmentsElt = (Element)document.selectSingleNode(xPath);// 该方法返回的是DefaultElement类
// Element是Node的子类，方法更多、更便捷 
// document对象过滤获取节点的方式
String xPath1 = "/configuration/environments/environment[@id='" + defaultId + "']";
Element environmentElt = (Element)document.selectSingleNode(xPath1);
// document对象获取多个节点
String xPath2 = "//mapper";// 不从根节点开始查找节点用两个斜杠
List<Node> nodes = document.selectNodes(xpath2);

// 通过Element类获取子节点
Element xxxElt = environmentElt.element(String 子结点名);
// 通过Element类获取所有子节点
List<Element> propertyElts = dataSource.elements();


// 通过Element类获取属性值
String defaultId = environmentsElt.attributeValue(String 属性名);
~~~

## 解析sql映射的xml

~~~java
@Test
public void testSqlMapper() throws Exception{
    // 读取xml，获取document对象
    SAXReader saxReader = new SAXReader();
    Document document = saxReader.read(Thread.currentThread().getContextClassLoader().getResourceAsStream("sqlmapper.xml"));

    // 获取根节点
    Element mapperElt = (Element) document.selectSingleNode("/mapper");
    
    // 获取namespace
    String namespace = mapperElt.attributeValue("namespace");
    System.out.println(namespace);

    // 获取sql id
    mapperElt.elements().forEach(statementElt -> {
        // 标签名
        String name = statementElt.getName();
        System.out.println("name:" + name);
        // 如果是select标签，还要获取它的resultType
        if ("select".equals(name)) {
            String resultType = statementElt.attributeValue("resultType");
            System.out.println("resultType:" + resultType);
        }
        // sql id
        String id = statementElt.attributeValue("id");
        System.out.println("sqlId:" + id);
        // sql语句
        String sql = statementElt.getTextTrim();
        System.out.println("sql:" + sql);
    });
}
~~~



## 正则表达式解析sql语句

~~~java
// 获取根节点mapper
Element rootElt = (Element)document.selectSingleNode("/mapper");
// 再获取所有子节点
List<Element> sqlList = rootElt.elements();
for (Element sql : sqlList) {
    // 先获取slq语句
    String sql = element.getTextTrim();
    // 使用正则替换替换
    String newSql = sql.replaceAll("#\\{[0-9a-zA-Z_$]*}", "?");
}
~~~

​	
