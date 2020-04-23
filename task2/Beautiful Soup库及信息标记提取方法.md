

#  Beautiful Soup库及信息标记提取方法

## 一. BeautifulSoup库

- 安装：pip install beautifulsoup4  (别忘了数字4)

- 导入：from bs4 import BeautifulSoup (注意大小写) 或 import bs4

- 创建 Beautiful Soup对象： 

  soup = BeautifulSoup( html代码 ，"html.parser")    或

  soup2 = BeautifulSoup( open( html文档 ) ，"html.parser") 

- BeautifulSoup库的作用

  **HTML <--> 标签树 <--> Beautiful Soup类 （三者是等价的）**

  （1）BeautifulSoup库是解析HTML文件和XML文件的功能库，Beautiful Soup对应一个HTML/XML文档的全部内容。

  （2）BeautifulSoup库是解析、遍历、维护 “标签树” 的功能库，只要给的文件是标签类型，BeautifulSoup库都可解析。

  通俗点理解就是：要把html熬成BeautifulSoup这锅汤，需要的原材料就是html代码，html解析器，最后获得的这锅汤就是html解析后的结果。

  （3）虽然说BeautifulSoup4 简单容易比较上手，但是匹配效率还是远远不如正则以及xpath的，一般不推荐使用，推荐正则的使用。

### 1. BeautifulSoup库的解析器

| 解析器           | 使用方法                           | 条件                 |
| ---------------- | ---------------------------------- | -------------------- |
| bs4的HTML解析器  | BeautifulSoup( mk ，'html.parser') | 安装bs4库（最常用）  |
| lxml的HTML解析器 | BeautifulSoup( mk ，'lxml')        | pip install lxml     |
| lxml的XML解析器  | BeautifulSoup( mk ，'xml')         | pip install lxml     |
| html5lib的解析器 | BeautifulSoup( mk ，'html5lib')    | pip install html5lib |

以 <http://python123.io/ws/demo.html> 为例

![网页](https://user-images.githubusercontent.com/62495140/79946131-ed0ee980-84a1-11ea-8f29-da0faaa8b128.jpg)

```python
>>> from bs4 import BeautifulSoup
>>> import requests
>>> url = "http://python123.io/ws/demo.html"
>>> r = requests.get(url)
>>> demo = r.text  
>>> demo   # 抓取网页的html内容
```

```
'<html><head><title>This is a python demo page</title></head>\r\n<body>\r\n<p class="title"><b>The demo python introduces several python courses.</b></p>\r\n<p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:\r\n<a href="http://www.icourse163.org/course/BIT-268001" class="py1" id="link1">Basic Python</a> and <a href="http://www.icourse163.org/course/BIT-1001870001" class="py2" id="link2">Advanced Python</a>.</p>\r\n</body></html>'
```

```python
>>> soup = BeautifulSoup(demo, "html.parser")  #bs4解析抓取的html内容
>>> soup.title  # title为页面标题标签
```

```
<title>This is a python demo page</title>
```

### 2. BeautifulSoup类的基本元素

| 基本元素        | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Tag             | 标签，最基本的信息组织单元，分别用<>和</>标明结尾和开头，格式**<tag> = soup.tag** |
| Name            | 标签的名字，<p>...</p>的名字是'，字符串类型，格式：**<tag>.name** |
| Attributes      | 标签的属性，字典形式组织，0个或多个，格式：**<tag>.attrs**   |
| NavigableString | 标签内的非属性字符，<>...</>中字符串，格式：**<tag>.string** |
| Comment         | 标签内字符串的注释部分，一种特殊的Comment类型                |

后四个均针对标签。**标签**的基本结构如下：

<img src="https://user-images.githubusercontent.com/62495140/79937350-3a805c00-848c-11ea-9d53-6a10adfea341.jpg" alt="标签的基本结构" style="zoom:67%;" />

- <tag>=soup.tag

```python
>>> soup.a
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>

# 可以通过soup.tag获得任意标签，如果html中存在多个相同标签，则只会获取第一个
>>> type(soup.a) 
<class 'bs4.element.Tag'>
```

- <tag>.name

```python
>>> soup.a.name
 'a'
  
```

- <tag>.attrs

```python
>>> soup.a.attrs  # 无论有无属性都会返回一个字典
{'href': 'http://www.icourse163.org/course/BIT-268001', 'class': ['py1'], 'id': 'link1'}
```

- <tag>.string

```python
>>> soup.a.string
'Basic Python'

>>> soup.p.string  
'The demo python introduces several python courses.'
# p标签中包含了b标签，返回的内容为b标签中的非属性字符串。所以，NavigableString可以跨越多个标签层次
```

- NavigableString和Comment比较

```python
>>> newsoup = BeautifulSoup("<b><!--This is a comment--></b><p>This is not a comment</p>","html.parser")
# comment注释首尾会有尖括号等符号 <!--comment-->

>>> newsoup.b.string
'This is a comment'

>>> newsoup.p.string
'This is not a comment'

>>> type(newsoup.b.string)
<class 'bs4.element.Comment'>

>>> type(newsoup.p.string)
<class 'bs4.element.NavigableString'>

# 使用.string会获得非属性字符串(NavigableString)或者注释(Comment)
# 如果不想获得注释，需要对.string的结果进行type判断
```

### 3. 标签树的遍历

标签树的遍历方法有三种：

<img src="https://user-images.githubusercontent.com/62495140/79947815-37459a00-84a5-11ea-8af7-452af9486cf0.jpg" alt="遍历" style="zoom:67%;" />

#### （1） 下行遍历

| 属性         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| .contents    | 子节点的列表，将<tag>所有**儿子**节点存入列表                |
| .children    | 子节点的迭代类型，返回类型为列表迭代器，内容与.contents类似，用于循环遍历**儿子**节点 |
| .descendants | 子孙节点的迭代类型，返回类型为生成器，内容包含所有**子孙**节点，用于循环遍历 |

**注意：**虽然标签树只指画出了标签，但是在**下行遍历中**标签树的**子节点**不仅有<u>标签</u>，还有<u>字符串子节点如'\n'，'and'等</u>，还有<u>标签中的NavigableString</u>

- .contents

```python
# .contents返回类型为列表，返回的仅是下一层的子节点
>>> soup.head
<head><title>This is a python demo page</title></head>

>>> soup.head.contents
[<title>This is a python demo page</title>]
```

```python
>>> soup.body
<body>
<p class="title"><b>The demo python introduces several python courses.</b></p>
<p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>
</body>

>>> soup.body.contents
['\n', <p class="title"><b>The demo python introduces several python courses.</b></p>, '\n', <p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>, '\n']
```

- .children

```python
# .children返回的是列表迭代器(利用for循环)，用于循环遍历儿子节点，仅是下一层的所有子节点，不再向后遍历
child_ls = []
for child in soup.body.children:
    child_ls.append(child)
child_ls 
```

```
['\n',
 <p class="title"><b>The demo python introduces several python courses.</b></p>,
 '\n',
 <p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>,
 '\n']
```

- .descendants

```python
#.descendants返回的是生成器，用于循环遍历子孙节点，即遍历之后所有层的节点
child_g_ls = []
for child in soup.body.descendants:
    child_g_ls.append(child)
child_g_ls
```

```
['\n',
 <p class="title"><b>The demo python introduces several python courses.</b></p>,
 <b>The demo python introduces several python courses.</b>,
 'The demo python introduces several python courses.',
 '\n',
 <p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>,
 'Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:\r\n',
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>,
 'Basic Python',
 ' and ',
 <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>,
 'Advanced Python',
 '.',
 '\n']
```

#### （2）上行遍历

| 属性     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| .parent  | 节点的**父亲标签**                                           |
| .parents | 节点**先辈标签**的迭代类型，返回类型为生成器，用于循环遍历**先辈标签** |

**注意**：在**上行遍历**中**父节点**只有<u>标签</u>，字符串等非标签的节点不作为父节点

- .parent

```python
# .parent仅返回上一层父标签（字符串等非标签的节点不作为父节点，父节点只有标签一种）
>>> soup.title.parent
<head><title>This is a python demo page</title></head>

# html是html标签树的根标签，则其父标签是整个标签树
>>> soup.html.parent
<html><head><title>This is a python demo page</title></head>
<body>
<p class="title"><b>The demo python introduces several python courses.</b></p>
<p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>
</body></html>

# soup的父标签是空的
>>> soup.parent
```

- .parents

```python
parent_ls = []
for parent in soup.a.parents:
    if parent is None: #在遍历标签的所有先辈标签会遍历到soup，但是soup的父标签为空，没有name属性
        parent_ls.append(parent)
        print(parent)
    else:
        parent_ls.append(parent)
        print(parent.name)
```

```
p
body
html
[document]
```

```
parent_ls的结果如下
[<p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>,
 <body>
 <p class="title"><b>The demo python introduces several python courses.</b></p>
 <p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>
 </body>,
 <html><head><title>This is a python demo page</title></head>
 <body>
 <p class="title"><b>The demo python introduces several python courses.</b></p>
 <p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>
 </body></html>,
 <html><head><title>This is a python demo page</title></head>
 <body>
 <p class="title"><b>The demo python introduces several python courses.</b></p>
 <p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
 <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>
 </body></html>]
```

####  （3） 平行遍历

| 属性               | 说明                                             |
| ------------------ | ------------------------------------------------ |
| .next_sibling      | 返回按照HTML文本顺序的下一个平行节点             |
| .previous_sibling  | 返回按照HTML文本顺序的上一个平行节点             |
| .next_siblings     | 迭代类型，返回按照HTML文本顺序的后续所有平行节点 |
| .previous_siblings | 迭代类型，返回按照HTML文本顺序的前序所有平行节点 |

**注意**：平行遍历的前提为发生在**同一个父节点下**的各节点之间；标签树的**平行遍历**中的**平行节点**不仅有<u>标签</u>，还有<u>字符串子节点如'\n'，'and'等</u>，还有<u>标签中的NavigableString</u>

```python
>>> soup.a.next_sibling
' and '

>>> soup.a.previous_sibling
'Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:\r\n'
```

### 4. 基于bs4库的HTML格式输出

#### （1）如何让html内容友好显示

**soup.prettify()    或   <tag>.prettify()** 

为HTML文本<>及其内容增加更加'\n'，使输出具有层次感

```python
print(soup.prettify())
```

```html
<html>
 <head>
  <title>
   This is a python demo page
  </title>
 </head>
 <body>
  <p class="title">
   <b>
    The demo python introduces several python courses.
   </b>
  </p>
  <p class="course">
   Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
   <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">
    Basic Python
   </a>
   and
   <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">
    Advanced Python
   </a>
   .
  </p>
 </body>
</html>
```

```python
>>> print(soup.a.prettify())
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">
 Basic Python
</a>
```

#### （2）bs4库将任何HTML输入都变成utf‐8编码

Python 3.x默认支持编码是utf‐8,解析无障碍

```
>>> newsoup = BeautifulSoup('<a>中文</a>', 'html.parser')
>>> print(newsoup.prettify())
<a>
 中文
</a>
```

## 二. 信息标记及提取方法

### 1. 信息标记的三种形式

####  （1）XML

eXtensible Markup Language，是HTML的扩展，标签树格式

XML举例：

```xml
<person>
  <firstName>Tian</firstName>
  <lastName>Song</lastName>
  <address>
    <streeAddr>中关村南大街5号</streeAddr>
    <city>北京市</city>
  </address>
  <prof>Computer System</prof><prof>Security</prof>
</person>
```

#### （2）JSON

JavaScript Object Notation，是有类型的键值对，字典格式

JSON举例：

```json
{
  "firstName":"Tian",
  "lastName" :"Song",
  "address"  :{
    "streeAddr":"中关村南大街5号"，
    "city"     :"北京市"
  },
  "prof"     :["Computer System","Security"]
}
```

#### （3）YAML

YAML Aint Markup Language，是无类型的键值对，缩进格式表达所属关系，减号表示并列关系，｜表示整块数据，#表示注释

YAML举例：

```yaml
firstName: Tian
lastName : Song
address  :
	stressAddr: 中关村南大街5号
	city	    : 北京市
prof     : 
-Computer System
-Security
```

#### （4）比较

| 形式 | 特点                                            | 应用                                                 |
| ---- | ----------------------------------------------- | ---------------------------------------------------- |
| XML  | 最早的通用信息标记语言，扩展性好，较繁琐        | Internet上的信息交互与传递                           |
| JSON | 信息有类型，适合程序处理(js)，较XML简洁，无注释 | 移动应用云端和节点的信息通信，在程序的接口处理中常用 |
| YAML | 信息无类型，文本信息比例高，可读性好            | 各类系统的配置文件                                   |

### 2. 信息提取的一般方法

| 方法                                               | 优点             | 缺点                     |
| -------------------------------------------------- | ---------------- | ------------------------ |
| 标记解析器——完整解析信息的标记形式，再提取关键信息 | 信息解析准确     | 提取过程繁琐，速度慢     |
| 文本查找函数——无视标记形式，直接搜索关键信息       | 提取简洁，速度快 | 提取结果准确性与内容相关 |

实际应用中一般为两种方法相结合：

```python
from bs4 import BeautifulSoup
import requests
url = "http://python123.io/ws/demo.html"
r = requests.get(url)
demo = r.text  
soup = BeautifulSoup(demo, "html.parser")
# 1. 查找html中的标签a
for link in soup.find_all('a'):
		# 2. 解析查找到的标记形式
    print(link)
    print(link.get('href'))
```

```html
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>
http://www.icourse163.org/course/BIT-268001
<a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>
http://www.icourse163.org/course/BIT-1001870001
```

### 3. html内容查找方法

#### （1）.find_all()方法

在Beautiful Soup库中：

<>.find_all(name, attires, recursive, string, **kwargs)

- 返回一个**列表**类型，存储soup中查找的**标签**结果
- name：对<u>标签</u>名称的检索<u>字符串</u>
- attrs：对标签属性值的检索<u>字符串</u>，可标注属性检索
- recursive：是否对**子孙**全部检索，默认True
- string：在标签<>...</>中字符串区域的检索字符串

- 简写形式

  ```python
  <tag>(...)     等价于    <tag>.find_all(...)
  
  <soup>(...)    等价于    <soup>.find_all(...)
  ```

举例：

- 对标签名称name检索

```python
>>> soup.find_all('a')
[<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>, <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>]

>>> soup.find_all(['a','b'])
[<b>The demo python introduces several python courses.</b>, <a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>, <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>]
```

```python
# 查找soup中所有的标签
for tag in soup.find_all(True):
    print(tag.name)
```

```
html
head
title
body
p
b
p
a
a
```

```python
#只显示以b开头的标签，利用正则表达式
import re 
for tag in soup.find_all(re.compile('b')):
    print(tag.name)
```

```
body
b
```

- 对标签属性值attrs检索

```python
>>> soup.find_all('p','course')
[<p class="course">Python is a wonderful general-purpose programming language. You can learn Python from novice to professional by tracking the following courses:
<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a> and <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>.</p>]

>>> soup.find_all(id='link1')
[<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>]

# 利用正则表达式，搜索可以不用完全精确
>>> soup.find_all(id=re.compile('link'))
[<a class="py1" href="http://www.icourse163.org/course/BIT-268001" id="link1">Basic Python</a>, <a class="py2" href="http://www.icourse163.org/course/BIT-1001870001" id="link2">Advanced Python</a>]
```

- recursive是否对子孙检索

```python
>>> soup.find_all('a',recursive=False)
[]
```

- 对标签中的内容NavigableString检索

```python
>>> soup.find_all(string='Basic Python')
['Basic Python']

>>> soup.find_all(string=re.compile('python'))
['This is a python demo page', 'The demo python introduces several python courses.']
```

#### （2）BeautifulSoup库中其他html内容查找方法

| 方法                        | 说明（参数均同.find_all()）              |
| --------------------------- | ---------------------------------------- |
| <>.find()                   | 搜索且只返回一个结果，字符串类型         |
| <>.find_parents()           | 在先辈节点中搜索，返回列表类型           |
| <>.find_parent()            | 在先辈节点中返回一个结果，字符串类型     |
| <>.find_next_siblings()     | 在后续平行节点中搜索，返回列表类型       |
| <>.find_next_sibling()      | 在后续平行节点中返回一个结果，字符串类型 |
| <>.find_previous_siblings() | 在前序平行节点中搜索，返回列表类型       |
| <>.find_previous_siblings() | 在前序平行节点中返回一个结果，字符串类型 |



|    :     |       <填充>       |            <对齐>            |      <宽度>      |                   ,                    |                   <精度>                   |                     <类型>                     |
| :------: | :----------------: | :--------------------------: | :--------------: | :------------------------------------: | :----------------------------------------: | :--------------------------------------------: |
| 引导符号 | 用于填充的单个字符 | < 左对齐；>右对齐；^居中对齐 | 槽的设定输出宽度 | 数字的千分位分隔符。适用于整数和浮点数 | 浮点数小数部分的精度或字符串输出的最大长度 | 整数类型b, c, d, o, x, X；浮点数类型e, E, f, % |

