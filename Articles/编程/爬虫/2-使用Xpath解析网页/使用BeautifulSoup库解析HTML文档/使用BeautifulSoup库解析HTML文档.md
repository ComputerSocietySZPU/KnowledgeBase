源码与相关文档：
![2. BeautifulSoup语法例程-iStudy-课堂练习.py](./使用BeautifulSoup库解析HTML文档.assert/1746935962281-3d55e250-984a-4d78-b630-b445bb351a90.py)
![iStudy.html](./使用BeautifulSoup库解析HTML文档.assert/1746935962450-a03b53ee-0cd8-427c-9933-6b13a31d36a2.html)

## 一、教程目的
本教程将详细介绍如何使用Python的BeautifulSoup库对HTML文档进行解析，包括提取特定位置的标签、指定标签名的标签、特定范围下的标签，获取标签的属性值以及选择指定class属性的标签等操作，帮助读者掌握BeautifulSoup库的基本使用方法。
## 二、前置知识

1. 基本的Python编程知识，包括文件操作、列表、字符串等。
2. 了解HTML的基本结构，如标签、属性等。## 三、安装BeautifulSoup库
在使用BeautifulSoup库之前，需要确保已经安装了该库。可以使用以下命令进行安装：

```plain
pip install beautifulsoup4
```
## 四、示例代码及解释
以下是示例代码及对代码的详细解释：

```python
# 本代码演示如何使用BeautifulSoup库来解析HTML文档。
# 防止页面变动导致程序失效，程序从文件读取html。
# 在实际爬虫程序中改用requests.get抓取页面html
with open('iStudy.html', 'r', encoding='utf-8') as file:
    html = file.read()

# 构建BeautifulSoup解析器解析html
from bs4 import BeautifulSoup
bs = BeautifulSoup(html, 'lxml')
```
在上述代码中：

1. 使用```with open
```
语句打开名为```iStudy.html
```
的文件，并读取其内容到```html
```
变量中。
2. 从```bs4
```
模块导入```BeautifulSoup
```
类，使用```BeautifulSoup
```
创建一个解析对象```bs
```
，指定解析器为```lxml
```
（需要先安装```lxml
```
库，```pip install lxml
```
）。### （一）提取指定位置的标签

```python
# 提取指定位置的标签。例：提取页面中第一门课的课程名
# 路径通过在浏览器开发者模式下拷贝Selector得到
# 注意：bs.select的结果是一个标签列表，要添加[0]获取第一个标签
tag = bs.select('body > div > div:nth-child(1) > ul.xbkcList.clearAfter > li:nth-child(1) > dl > dt > a')[0]
tag = bs.select_one('body > div > div:nth-child(1) > ul.xbkcList.clearAfter > li:nth-child(1) > dl > dt > a')
# 也可以使用bs.select_one方法，返回满足查找路径的第一个标签
tag.text
# 练习：获取第一门课程的教师名称
tag = bs.select_one('body > div > div:nth-child(1) > ul.xbkcList.clearAfter > li:nth-child(1) > dl > dt > a')
tag.text
```
解释：

1. 使用```bs.select
```
方法，传入一个CSS选择器字符串```'body > div > div:nth-child(1) > ul.xbkcList.clearAfter > li:nth-child(1) > dl > dt > a'
```
，该选择器用于定位HTML文档中特定位置的标签。```bs.select
```
返回的是一个标签列表，通过```[0]
```
获取第一个标签。
2. ```bs.select_one
```
方法直接返回满足选择器的第一个标签，不需要再使用```[0]
```
获取。
3. 使用```tag.text
```
获取标签内的文本内容。### （二）提取指定标签名的标签

```python
# 提取指定标签名的标签。例：提取页面中所有img标签
# 查找页面中所有的img标签，共__个标签。思考：这里能用select_one吗
img_tags = bs.select('img')
len(img_tags)
```
解释：

1. 使用```bs.select('img')
```
方法，传入标签名```img
```
，获取HTML文档中所有的```<img>
```
标签，返回一个标签列表。
2. 使用```len(img_tags)
```
获取标签列表的长度，即```<img>
```
标签的数量。因为```select_one
```
只返回一个标签，所以这里不能用```select_one
```
获取所有```<img>
```
标签。### （三）提取指定范围下的标签

```python
# 提取指定范围下的标签。例：提取“校本课程”栏目下的所有img标签
ul_tag = bs.select_one('body > div > div:nth-child(1)') # 定位“本校课程”范围的父标签
img_tags = ul_tag.select('img')  # 从ul_tag下选择img标签，共__个标签
```
解释：

1. 首先使用```bs.select_one
```
方法定位到“校本课程”栏目的父标签，即```'body > div > div:nth-child(1)'
```
对应的标签，并赋值给```ul_tag
```
。
2. 然后在```ul_tag
```
的基础上，使用```ul_tag.select('img')
```
获取该范围内所有的```<img>
```
标签。### （四）获取标签的属性值

```python
# 获取标签的属性值。例：获取第一门课程的图片链接
img_tag = bs.select_one('body > div > div:nth-child(1) > ul.xbkcList.clearAfter > li:nth-child(1) > a > img')
# <img src="http://cs.istudy.szpu.edu.cn/star3/270_169/f73d47522396efa9b530fe32e4d5b7ad.png" alt="跨境电商大数据统计与分析">
img_url = img_tag.get('src')# 获取属性src的值
# 练习：获取img_tag的alt属性值
img_alt = img_tag.get('alt')
```
解释：

1. 使用```bs.select_one
```
方法定位到第一门课程的```<img>
```
标签。
2. 使用```img_tag.get('src')
```
获取```<img>
```
标签的```src
```
属性值，即图片的链接。
3. 使用```img_tag.get('alt')
```
获取```<img>
```
标签的```alt
```
属性值。### （五）选择指定class属性的标签

```python
# 选择指定class属性的标签。例：提取“本校课程”下最后一门课的ul标签
# <ul class="value">...</ul>
tag = bs.select_one('ul.value')
# 如果属性名带空格，把空格改成点。例：获取“精品课程”的ul标签
# <ul class="xbkcList clearAfter">
tag = bs.select_one('ul.xbkcList.clearAfter')
# 思考：如果改用bs.select('ul.clearAfter')，能获取到多少个标签？
```
解释：

1. 使用```bs.select_one('ul.value')
```
获取```class
```
属性为```value
```
的```<ul>
```
标签。
2. 当```class
```
属性值包含空格时，在选择器中使用点```'.'
```
代替空格，如```bs.select_one('ul.xbkcList.clearAfter')
```
获取```class
```
属性为```xbkcList clearAfter
```
的```<ul>
```
标签。
3. ```bs.select('ul.clearAfter')
```
会返回所有```class
```
属性包含```clearAfter
```
的```<ul>
```
标签，具体数量取决于HTML文档中符合条件的标签个数。
