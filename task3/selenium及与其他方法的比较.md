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

- 创建一个WebDriver实例


```python
from selenium import webdriver   # 导入模块
driver = webdriverwebdriver.Chrome()  # 打开浏览器
```

- 打开一个页面


```python
driver.get("http://www.python.org") #chromedriver会打开一个chrome浏览器窗口，显示的是网址对应的页面
driver.get(r"C:\desktop\text.html")      # 打开本地 html页面
driver.title          # 获取打开网址 的名字 
```

- 关闭页面


```python
driver.close()  # 关闭浏览器一个Tab
# 或者
driver.quit()   # 关闭浏览器窗口
```

### 2. 定位元素

```python
element = driver.find_element_by_id("site-map-link")   #  定位id为“site-map-link"的元素
element = driver.find_element_by_name("q")   # 定位name为“q”的元素
element = chrome_obj.find_element_by_link_text("a标签中的链接内容 准确定位") 
```

在日常的网页源码中，我们基于元素的**id**去定位是最万无一失的，id在单个页面中是不会重复的。但是实际工作中，很多前端开发人员并未给每个元素都编写id属性。

- 基于class属性来定位元素

```python
driver.find_element_by_class_name("classname")
# 很多并列的元素如list表单，class都是共用同一个，使用如下方法可返回一个list列表
driver.find_elements_by_class_name("classname")[n]   #注意：是elements，不是element
```

- 基于xpath来定位元素最灵活

```python
driver.find_element_by_xpath("xpath路径")
```

### 3. 页面交互

```python
driver.maximize_window() # 窗口最大化 
driver.back() # 模拟浏览器返回上一个浏览页面
```

#### （1）模拟鼠标操作

需要提前导入模块

```python
from selenium.webdriver.common.action_chains import ActionChains  # 导入模块

element = chrome_obj.find_element_by_link_text("点我 悬浮 显示其他 a标签") #定位到含有点击连接的tag
element.click()   # 模拟用户点击
```

#### （2）模拟键盘操作

需要提前导入模块

```python
from selenium.webdriver.common.keys import Keys  # 提供键盘按键支持

element.send_keys("some text")   # 向element标签中输入内容"some text"

element.send_keys(Keys.BACK_SPANCE)  # 退格键
element.send_keys(Keys.CONTRL,'a')  # 全选
element.send_keys(Keys.CONTRL,'v')  # 粘贴
element.send_keys(Keys.CONTRL,'c')  # 复制
element.send_keys(Keys.CONTRL,'x‘) # 剪切
element.send_keys(Keys.RETURN)  # 回车

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