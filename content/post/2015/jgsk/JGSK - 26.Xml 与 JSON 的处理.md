---
comments: true
date: 2015-10-04T21:25:38+08:00
description: ""
draft: false
keywords:
- java
- groovy
- scala
- kotlin
slug: "JGSK-26-xml-and-json"
tags:
- java
- groovy
- scala
- kotlin
title: JGSK - 26.Xml 与 JSON 的处理
toc: true
topics:
- JGSK
---

##  共通篇

JSON 是一种键值对形式的轻量级的数据交换格式，除了大量用于 Restful 请求外，其二进制形式的 BSON 也被用于作为 NO SQL 的数据存储格式。相比较 XML 而言，JSON 更为简单，灵活。

XML 也可以用于数据交换，最为有名的就是 SOAP 协议。相比较 JSON 而言由于需要有开始和结束标记所以 XML 略显啰嗦。不过相比较 JSON 的无模式形式，XML 可以通过指定 DTD 而为其中定义的数据指定一定的格式，所以可以作为配置文件，在使用 IDE 进行编辑时也能拥有代码提示功能。

<!--more-->

尽管现代语言都在一定程度上支持 JSON 和 XML 的创建和解析，但是很少有人会直接使用语言本身提供的 API 而不是第三方库。所以本章节只是简单的介绍一下基本用法。

## Java 篇

### JSON

Java 原来并不支持 JSON 的解析，不过在 Oracle 将 JavaFX 的 API 整合到 Java 中，原生的解析也就成了可能。当然更多人还是会选择 Jackson，GSON，Fastjson 等第三方库。

#### 创建 JSON

使用 JavaFX 创建 JSON

JSON 对象

```java
JSONDocument jsonDocument = JSONDocument.createObject();
JSONDocument results = JSONDocument.createObject();
results.setString("result", "x");
results.setString("result", "y");
jsonDocument.set("results", results);
System.out.println(jsonDocument.toJSON());
```

输出结果

```json
{"results":{"result":"y"}}
```

JSON 数组

```java
jsonDocument = JSONDocument.createArray();
JSONDocument jsonDocument1 = JSONDocument.createObject();
jsonDocument1.setString("404", "not found");
JSONDocument jsonDocument2 = JSONDocument.createObject();
jsonDocument2.setString("302", "redirect");
jsonDocument.array().add(jsonDocument1);
jsonDocument.array().add(jsonDocument2);
System.out.println(jsonDocument.toJSON());
```

输出结果

```json
[{"404":"not found"},{"302":"redirect"}]
```

#### 解析 JSON

```java
String json = "{\"data\":[{\"404\":\"not found\"},{\"302\":\"redirect\"}]}";
JSONReader jsonReader = new JSONStreamReaderImpl(new StringReader(json));
JSONDocument jsonDocument = jsonReader.build();
JSONDocument data = jsonDocument.get("data");
System.out.println(data.get(0).object().keySet().iterator().next());    //  404
System.out.println(data.get(1).object().values().iterator().next());    //  redirect
```

由于 Java 中使用引号时必须加上转义符，所以 JSON 字符串看起来非常不直观。

### XML

#### 创建 XML

```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
try {
    String defaultNamespaceUri = "http://myDefaultNamespace";
    String otherNamespaceUri = "http://someOtherNamespace";

    DocumentBuilder builder = factory.newDocumentBuilder();
    Document doc = builder.newDocument();

    //  指定 XSL
    ProcessingInstruction xmlSheet = doc.createProcessingInstruction("xml-sheet", "type='text/xsl' href='myfile.xslt'");
    doc.appendChild(xmlSheet);

    Element langs = doc.createElement("langs");
    langs.setAttribute("type", "current");
    //  指定 Namespace
    langs.setAttribute("xmlns:app", otherNamespaceUri);
    langs.setAttribute("xmlns", defaultNamespaceUri);
    doc.appendChild(langs);

    Element language1 = doc.createElement("language");
    Text text1 = doc.createTextNode("Java");
    language1.appendChild(text1);
    langs.appendChild(language1);

    Element language2 = doc.createElement("language");
    Text text2 = doc.createTextNode("Groovy");
    language2.appendChild(text2);
    langs.appendChild(language2);

    //   指定 CDATA
    CDATASection cdataSection = doc.createCDATASection("<!-- Support Android -->");
    langs.appendChild(cdataSection);

    Element language3 = doc.createElement("language");
    Text text3 = doc.createTextNode("Scala");
    language3.appendChild(text3);
    langs.appendChild(language3);

    Element language4 = doc.createElement("language");
    Text text4 = doc.createTextNode("Kotlin");
    language4.appendChild(text4);
    langs.appendChild(language4);

    // 输出 XML
    TransformerFactory tf = TransformerFactory.newInstance();
    Transformer transformer = tf.newTransformer();
    transformer.setOutputProperty(OutputKeys.INDENT, "yes");
    transformer.setOutputProperty(OutputKeys.VERSION, "1.0");
    transformer.setOutputProperty(OutputKeys.ENCODING, "UTF-8");
    StringWriter writer = new StringWriter();
    StreamResult streamResult = new StreamResult(writer);
    DOMSource source = new DOMSource(doc);
    transformer.transform(source, streamResult);
    String xmlString = writer.toString();
    System.out.println(xmlString);
} catch (ParserConfigurationException | TransformerException e) {
    e.printStackTrace();
}
```

输出结果

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-sheet type='text/xsl' href='myfile.xslt'?>
<langs xmlns="http://myDefaultNamespace" xmlns:app="http://someOtherNamespace" type="current">
    <language>Java</language>
    <language>Groovy</language><![CDATA[<!-- Support Android -->]]>
    <language>Scala</language>
    <language>Kotlin</language>
</langs>
```


#### 解析 XML

XML 有多种解析方式，但是最基本的就两种 DOM 和 SAX。DOM 需要将文档都读到内存中，所以可以实现随机读取。SAX 则是基于事件一步一步进行解析，解比 DOM 的实现方式要复杂。以下例子为 DOM 解析。

```java
String xml = "<langs type='current' count='4' mainstream='true'>\n" +
        "  <language flavor='static' version='1.8.0_25'>Java</language>\n" +
        "  <language flavor='dynamic' version='2.4.4'>Groovy</language>\n" +
        "  <language flavor='static' version='2.11.5'>Scala</language>\n" +
        "  <language flavor='static' version='0.12.613'>Kotlin</language>\n" +
        "</langs>";

byte[] xmlBytes = xml.getBytes();
InputStream is = new ByteArrayInputStream(xmlBytes);

DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
try {
    DocumentBuilder db = dbf.newDocumentBuilder();
    Document doc = db.parse(is);

    Element langs = doc.getDocumentElement();
    System.out.println("count = " + langs.getAttribute("count"));   //  count=4

    NodeList list = langs.getElementsByTagName("language");
    for (int i = 0; i < list.getLength(); i++) {
        Element language = (Element) list.item(i);
        System.out.println(language.getTextContent());
    }
} catch (ParserConfigurationException | SAXException | IOException e) {
    e.printStackTrace();
}
```

## Groovy 篇


### JSON

Groovy 中创建和解析 JSON 格式比起 Java 来说要简单不少。

#### 创建 JSON

JSON 对象

```groovy
def json = new JsonBuilder()
json.call {
    results {
        result("x")
        result("y")
    }
}
println(json.toPrettyString())
```

输出结果

```json
{
    "results": {
        "result": "y"
    }
}
```

JSON 数组

```groovy
def list = [
        [code: "404", value: "not found"],
        [code: "302", value: "redirect"]
]
def builder = new JsonBuilder(list)
println builder.toPrettyString()
```

输出结果

```json
[
    {
        "code": "404",
        "value": "not found"
    },
    {
        "code": "302",
        "value": "redirect"
    }
]
```

包含 JSON 数组的 JSON 对象

```groovy
def root = new JsonBuilder()
root {
    data(
            list.collect {
                [
                        code : it.code,
                        value: it.value
                ]
            }
    )
}
println root.toPrettyString()
```

输出结果

```json
{
    "data": [
        {
            "code": "404",
            "value": "not found"
        },
        {
            "code": "302",
            "value": "redirect"
        }
    ]
}
```

#### 解析 JSON

```groovy
def json = """
{
    "data": [
        {
            "code": "404",
            "value": "not found"
        },
        {
            "code": "302",
            "value": "redirect"
        }
    ]
}
"""
def result = new JsonSlurper().parseText(json)
println result.data[0].code     //  404
println result.data[1].value    //  redirect
```

Groovy 中由于支持原样输出，所以无需使用转义符。

### XML

#### 创建 XML

基于 MarkupBuilder

```groovy
def sw = new StringWriter()
def xml = new MarkupBuilder(sw)
xml.langs(type: "current") {
    language("Java")
    language("Groovy")
    language("Scala")
    language("Kotlin")
}
println(sw)
```

输出结果

```xml
<langs type='current'>
  <language>Java</language>
  <language>Groovy</language>
  <language>Scala</language>
  <language>Kotlin</language>
</langs>
```

基于 StreamingMarkupBuilder

相比较 MarkupBuilder 可以实现更多的功能

```groovy
def comment = "<![CDATA[<!-- address is new to this release -->]]>"
def builder = new StreamingMarkupBuilder()
builder.encoding = "UTF-8"
xml = {
    mkp.xmlDeclaration()
    mkp.pi("xml-stylesheet": "type='text/xsl' href='myfile.xslt'")
    mkp.declareNamespace('': 'http://myDefaultNamespace')
    mkp.declareNamespace('app': 'http://someOtherNamespace')
    langs(type: "current") {
        language("Java", flavor: 'static', "app:version": '1.8.0_25')
        language("Groovy", flavor: 'dynamic', "app:version": '2.4.4')
        language("Scala", flavor: 'static', "app:version": '2.11.5')
        language("Kotlin", flavor: 'static', "app:version": '0.12.613')
    }
}
def writer = new StringWriter()
writer << builder.bind(xml)
println(writer)
```

#### 解析 XML

Groovy 中主要使用 XmlSlurper 或者 XmlParser 进行 XML 解析，两种都属于 SAX 解析方式，API 基本一样。

使用 XmlParser

获得节点的属性时可以使用 `attribute()` 方法或者 XPATH 形式的 `@属性名`。

```groovy
def xml = """
<langs type='current' count='4' mainstream='true'>
<language flavor='static' version='1.8.0_25'>Java</language>
<language flavor='dynamic' version='2.4.4'>Groovy</language>
<language flavor='static' version='2.11.5'>Scala</language>
<language flavor='static' version='0.12.613'>Kotlin</language>
</langs>
"""
def langs = new XmlParser().parseText(xml)
println "count = ${langs.attribute("count")}" //  count=4
println "count = ${langs.@count}"
langs.language.each {
    println it.text()
}
```

XmlSlurper

```groovy
def langs2 = new XmlSlurper().parseText(xml)
println "count = ${langs2.@count}" //  count=4
langs2.language.each {
    println it.text()
}
println langs2.language[1].@flavor   //  dynamic
```

> XmlSlurper 与 XmlParser 非常相似，最大的区别就是前者是懒加载方式，只有使用时才会真正进行解析。有关两种方式应该如何选择可以参考 [Stackoverflow 上的讨论](http://stackoverflow.com/questions/7558019/groovy-xmlslurper-vs-xmlparser)。

## Scala 篇


### JSON

Scala 原来的 JSON API 目前已经不推荐使用了，如果希望进行 JSON 解析，通常需要使用 Playframeworks 之类的第三方框架或库，所以这里就不介绍了。

### XML

与其它三种语言不同，在 Scala 中 XML 是一等公民，这意味着可以直接定义 XML 而不是其它语言中的 XML 字符串，所以在 Scala 中使用 XML 非常简单。

#### 创建 XML

XML 无需使用 `""` 定义，在 XML 中可以使用 `{}` 来编写代码，就像在 JSP 中编写代码一样。

```scala
val langType = "current"
val langs =
  <langs type={langType}>
    <language>Java</language>
    <language>Groovy</language>{scala.xml.PCData("<!-- Support Android-->")}<language>Scala</language>
    <language>Kotlin</language>
  </langs>
val printer = new PrettyPrinter(80, 4)
println(printer.format(langs))
println(scala.xml.Utility.trim(langs))
```

输出结果

```xml
<langs type="current">
    <language>Java</language>
    <language>Groovy</language>
    <![CDATA[<!-- Support Android-->]]>
    <language>Scala</language>
    <language>Kotlin</language>
</langs>
```

以上 XML 可以通过使用 List 循环进一步简化

```scala
val langLst = List("Java", "Groovy", "Scala", "kotlin")
val xml =
  <langs type={langType}>
    {langLst.map(l =>
    <language>
      {l}
    </language>
  )}
  </langs>
```

如果 XML 很长也可以通过 NodeBuffer 先构建不同部分再组装到一起。其中, `+=` 用于添加 XML 到当前 NodeBuffer，`&+` 用于构建新的 NodeBuffer。

```scala
var nb = new NodeBuffer
nb += <language>Java</language>
nb += <language>Groovy</language>
nb = nb &+ <language>Scala</language> &+ <language>Kotlin</language>
val langs3 =
  <langs>
    {nb}
  </langs>
println(printer.format(xml))
```

在生成 XML 时也可以通过重写来修改原来的 XML 的内容

```scala
val rewrite = new RuleTransformer(new RewriteRule {
  override def transform(n: Node): Node =
    n match {
      case <language>{l}</language> =>
        <language>{l}!!!</language>
      case other => other
    }
}
)
val result = rewrite.transform(langs)
println(printer.format(result.head))
```

输出 XML 到文件的时候也可以指定 DocType

```scala
val doctype = DocType("html",
  PublicID("-//W3C//DTD XHTML 1.0 Strict//EN",
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"),
  Nil)
XML.save("files/langs.xml", langs,
  "utf-8", xmlDecl = true, doctype)
```

以上将在 XML 头部追加如下信息

```scala
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

#### 解析 XML

Scala 解析 XML 方式类似 XPATH，可以使用 `\` 获得某一节点的直接子节点，使用 `\\` 获得子节点，使用 `@` 获得属性值。

```scala
val langs =
  <langs type='current' count='4' mainstream='true'>
    <language flavor='static' version='1.8.0_25'>
      <title>Java</title>
    </language>
    <language flavor='dynamic' version='2.4.4'>
      <title>Groovy</title>
    </language>
    <language flavor='static' version='2.11.5'>
      <title>Scala</title>
    </language>
    <language flavor='static' version='0.12.613'>
      <title>Kotlin</title>
    </language>
  </langs>

val java = (langs \ "language")(0)
val javaVersion = java \ "@version"
val javaTitle = (langs \\ "title")(0)
(langs \ "language" \ "title").foreach(n =>
  println(n.text)
)
println(javaVersion)    //  1.8.0_25
println(javaTitle.text) //  Java
```

## Kotlin 篇

###  JSON

Kotlin 目前没有什么通用的支持 JSON 的 API，需要自己定义 DSL 来解析。

###  XML

#### 创建 XML

```kotlin
val xml = """
<langs type='current' count='4' mainstream='true'>
<language flavor='static' version='1.8.0_25'>Java</language>
<language flavor='dynamic' version='2.4.4'>Groovy</language>
<language flavor='static' version='2.11.5'>Scala</language>
<language flavor='static' version='0.12.613'>Kotlin</language>
</langs>
"""

val builder = DocumentBuilderFactory.newInstance().newDocumentBuilder()
val doc = builder.parse(ByteArrayInputStream(xml.toByteArray()))
println(doc.toXmlString())
```

#### 解析 XML

```kotlin
val langs = doc.getElementsByTagName("langs").item(0)
println(langs.getAttributes().getNamedItem("count").getNodeValue()) //  4
```



## 总结

- 四种语言都原生提供 XML Api，但是只有 Scala 视 XML 为一等公民。
- 目前 Java 和 Groovy 都支持 JSON Api。
- 使用 JSON 和 XML 更多时候还是依赖第三方库而非原生 API。

---

项目源码见 [JGSK/_26_xml_json](https://github.com/SidneyXu/JGSK)
