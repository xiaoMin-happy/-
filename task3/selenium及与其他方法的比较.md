# selenium

- selenium是一个用于Web应用程序测试的工具，可以用代码的方式去模拟在浏览器中操作的过程

- selenium在爬虫中的作用：selenium直接运行在浏览器，而我们所需要的数据一般都会被浏览器渲染在了页面上，所以selenium可以跳过网页的JS反爬和加密反爬。

## 一. selenium环境配置

- 安装selenium：pip install selenium

- 安装chromedriver：一个驱动程序，用以启动chrome浏览器

  在chrome浏览器中输入<chrome://version>查看chrome版本

  mac安装chromedriver教程：<https://www.jianshu.com/p/c145a5b8636c>

- 测试环境

  ```python
  from selenium import webdriver
  driver = webdriver.Chrome()
  driver.get("https://www.bilibili.com")
  ```

  ![b站](https://user-images.githubusercontent.com/62495140/80281476-ff0cb880-873d-11ea-85bd-81fefd1dac71.jpg)

  

## 二. selenium的使用

### 1. 基本使用

导入模块

```python
from selenium import webdriver   # 启动浏览器
```

创建一个WebDriver实例

```python
driver = webdriverwebdriver.Chrome()
```

打开一个页面

```python
driver.get("http://www.python.org") #chromedriver会打开一个chrome浏览器窗口，显示的是网址对应的页面
```

关闭页面

```python
driver.close()  # 关闭浏览器一个Tab
# 或者
driver.quit()   # 关闭浏览器窗口
```

### 2. 查找元素

```python
element = driver.find_element_by_name("q")   # 查找name为“q”的tag
```

### 3. 页面交互

找到元素后就是进行交互，如键盘输入，需要提前导入模块

```python
from selenium.webdriver.common.keys import Keys  # 提供键盘按键支持

element.send_keys("some text")   # 向查找元素的对象输入

element.send_keys(Keys.RETURN)  # 模拟键盘回车

element.clear()  # 清空element对象中的文字（文本框中的文字）
```

![交互](https://user-images.githubusercontent.com/62495140/80281545-780c1000-873e-11ea-92a7-daa061548f9c.jpg)

### 4. 等待页面加载

应用场景：含有ajax加载的page

在这种情况下，页面的某个节点不是一开始就有的，所以不能“查找元素”，查找不了元素就不能进行交互操作。

```python
from selenium import webdriver
# 两个模块经常一起导入
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
```

- 显示等待：触发某个条件后才能执行后续的代码

```python
driver = webdriver.Chrome()
driver.get("https://www.bilibili.com")
try:
    element = WebDriverWait(driver, 10).until(
        EC.presence_of_all_elements_located((By.ID, "myDynamicElement"))
    )
finally:
    driver.quit()
# presence_of_all_elements_located是条件，
# By.ID是通过什么方式来确认元素，这里是通过id，
# "myDynamicElement"元素的id
```

- 隐式等待：设置某个具体的等待时间

```python
driver = webdriver.Chrome()
driver.implicitly_wait(10)  #seconds
driver.get("https://www.bilibili.com")
myDynamicElement = driver.find_element_by_id("myDynamic")
```



## 三、BeautifulSoup、Re、Xpath和selenium

- BeautifulSoup：主要用于静态网页，将html解析为soup对象，再去提取标签树中的目标信息。匹配效率还是远远不如正则以及xpath的，一般不推荐使用，推荐正则的使用；

- Re正则化：re正则表达式方法可直接在html文本中查找，不需要像bs4一样先解析html再查找；

- Xpath：把html文档解析为xml对象，通过Xpath路径提取节点树中的目标信息；

- selenium：主要用于动态网页，速度很慢，selenium通过创建一个WebDriver实例直接运行在浏览器，然后模拟人在浏览器的操作，并提取目标信息，可以跳过网页的JS反爬和加密反爬；


