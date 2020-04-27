# lxml库及Xpath

## 一. Xpath语法

- Xpath时XML路径语言（XML Path Language），它是用来确定XML文档中某部分位置的语言

- 在Xpath中有7种类型的节点：元素、属性、文本、命名空间、处理指令、注释、文档节点(根节点)
- XML文档是被作为**节点树**来对待

### 1. Xpath常用的路径表达式

Xpath使用路径表达式在XML文档中选取节点。节点是通过沿着路径选取的。

详细学习：<https://www.cnblogs.com/gaojun/archive/2012/08/11/2633908.html>

| 路径表达式 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| nodeName   | 选取名称为nodeName的所有**儿子节点**                         |
| /          | 从根节点选取                                                 |
| //         | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置 （选择当前节点符合要求的所有**后代元素**，后面必须跟上nodeName） |
| //*        | 表示整个文档的所有标签                                       |
| .          | 选取当前节点                                                 |
| ..         | 选取当前节点的父节点                                         |
| @          | 选取属性                                                     |
| /text()    | 提取标签下面的文本内容                                       |

举例：针对下面的XML文档的XPath结果，当前节点为document

```xml
<?xml version="1.0"?>
	<root>
		<child attr="attr" />
		<child>
			<a><desc /></a>
		</child>
	</root>
```

| 路径                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| /root                     | 选取root                                                     |
| root                      | 选取root                                                     |
| child                     | 空,因为child不是document的儿子元素                           |
| //child                   | 选取两个child元素，**//表示所有后代**                        |
| //@attr                   | 选取attr属性节点                                             |
| /root/child//desc         | 返回child的后代元素desc                                      |
| /root/child[3]            | 取root元素的第三个child子元素 (注意,这和数组下标不一样,**从1开始计数**) |
| //child[@attr]            | 选取**所有**具有属性attr的child元素                          |
| //child[@attr="val"]/desc | 选取所有属性attr的值为val的child元素的子元素desc             |
| //child[desc]             | 选取所有的有desc子元素的child                                |

Xpath可以理解为电脑上的文件路径，一般可以直接从网页的f12里右键选择html语句copy-->copy Xpath。若copy下来的Xpath不起作用，再手动写。

个人理解：/ 表示在上一级的儿子节点中查找， //表示上一级的所有的子孙节点中查找

## 二. lxml库

- 安装：pip install lxml

- 导入：from lxml import etree

相比于BeautifulSoup库，lxml库的最大优点就是速度快。

- 使用：etree相当于把html文档解析为xml对象 (即Element对象)

  tree = etree.HTML(html文档)



