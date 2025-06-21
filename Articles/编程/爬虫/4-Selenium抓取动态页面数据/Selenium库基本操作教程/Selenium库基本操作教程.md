源码：
![4.1 Selenium的基本操作-课堂练习.py](./Selenium库基本操作教程.assert/1746938184444-c6c35599-e2e0-4be8-8ea5-8e7abaf0018a.py)

## 一、教程目标
本教程旨在全面介绍Python中Selenium库的基本操作，包括打开网页、获取网页内容、解析网页元素（使用CSS选择器和XPath）、模拟浏览器操作（输入文本、点击按钮）以及设置浏览器为无头模式等。通过本教程，读者将能够熟练运用Selenium进行网页自动化操作，满足数据抓取、网页交互等多种需求。
## 二、环境准备

1. **安装浏览器驱动**：
- 对于Edge浏览器，从Microsoft Edge WebDriver官方网站下载与当前浏览器版本匹配的驱动程序，并将驱动程序路径添加到系统环境变量中。若不添加到环境变量，也可在代码中指定驱动路径，如 ```driver = webdriver.Edge(executor_path='驱动程序路径')
```
 。
- 其他浏览器（如Chrome、Firefox等）也需下载对应的驱动，并根据浏览器类型在代码中调用相应的WebDriver，如 ```webdriver.Chrome()
```
 、```webdriver.Firefox()
```
 等。
1. **安装Selenium库**：在命令行中执行 ```pip install selenium
```
 命令安装Selenium库。## 三、核心代码解析

1. **获取页面的HTML**：
```python
driver = webdriver.Edge()
driver.get('https://sports.qq.com/nba/players-list')
html = driver.page_source
print('泰·杰洛姆' in html)
```

- 使用 ```webdriver.Edge()
```
 创建Edge浏览器实例。
- ```driver.get('https://sports.qq.com/nba/players-list')
```
 打开指定网页。
- ```driver.page_source
```
 获取当前页面的HTML代码，存储在 ```html
```
 变量中。
- ```print('泰·杰洛姆' in html)
```
 检查指定字符串是否在获取的HTML中。
1. **解析网页**：
- **选取单个标签**：
```python
# 用CSS选择器
tag = driver.find_element(By.CSS_SELECTOR,'#app > div.page-content > div:nth-child(1) > table > tr:nth-child(2) > td:nth-child(1) > div > p')
name = tag.text
class_value = tag.get_attribute('class')

# 使用XPath选择元素
tag = driver.find_element(By.XPATH, '//*[@id="app"]/div[@class="page-content"]/div[1]/table/tr[2]/td[1]/div/p')
name = tag.text
class_value = tag.get_attribute('class')
```

```plain
 - 使用 `driver.find_element(By.CSS_SELECTOR, '选择器')` 通过CSS选择器定位单个元素，`tag.text` 获取标签内的文本，`tag.get_attribute('class')` 获取标签的 `class` 属性值。
 - 使用 `driver.find_element(By.XPATH, '路径')` 通过XPath路径定位单个元素。XPath路径中，`//` 表示在整个文档中查找，`*` 匹配任意标签，`[@属性名="属性值"]` 用于根据属性定位元素。
```

- **范围查找（提取所有球员头像的图片链接）**：
```python
# 用CSS选择器范围查找
table_tag = driver.find_element(By.CSS_SELECTOR,'#app > div.page-content > div:nth-child(1) > table')
img_tags = table_tag.find_elements(By.CSS_SELECTOR, 'img')
img_links = [img_tag.get_attribute('src') for img_tag in img_tags]

# 用XPath范围查找
table_tag = driver.find_element(By.XPATH, '//table[contains(@class, "player-avatars")]')
img_tags = table_tag.find_elements(By.XPATH, './/img')
img_links = [img_tag.get_attribute('src') for img_tag in img_tags]
```

```plain
 - 先用 `driver.find_element` 定位包含目标元素的父标签（如表格标签）。
 - 使用 `find_elements` 方法查找父标签下的所有符合条件的子标签（如 `img` 标签）。
 - 使用列表推导式 `[img_tag.get_attribute('src') for img_tag in img_tags]` 提取所有 `img` 标签的 `src` 属性值。
 - XPath中，`//table[contains(@class, "player-avatars")]` 查找包含特定类名的 `table` 标签，`.//img` 表示在当前元素（`table_tag`）下查找所有的 `img` 标签。
```

1. **模拟浏览器操作**：
- **模拟输入**：
```python
driver.get('http://cc.szpu.edu.cn/sSign.aspx')
input_box = driver.find_element(By.CSS_SELECTOR,'#TextBoxStudentNo')
input_box.send_keys('230200833')
input_box.clear()
```

```plain
 - `driver.get('http://cc.szpu.edu.cn/sSign.aspx')` 打开指定网页。
 - 通过CSS选择器定位输入框元素（`#TextBoxStudentNo`）。
 - 使用 `send_keys('230200833')` 模拟在输入框中输入文本。
 - 使用 `clear()` 方法清除输入框中的内容。
```

- **模拟点击按钮**：
```python
button = driver.find_element(By.ID, 'loginButton')
button.click()
```

```plain
 - 使用 `driver.find_element(By.ID, 'loginButton')` 通过元素的 `id` 定位按钮元素。
 - 使用 `click()` 方法模拟点击按钮。
```

1. **关闭浏览器**：
```python
driver.close()
```

- 使用 ```driver.close()
```
 关闭当前打开的浏览器窗口。
1. **设置浏览器为无头模式**：
```python
from selenium.webdriver.edge.options import Options
edge_options = Options()
edge_options.add_argument('--headless')
edge_options.add_argument('--disable-gpu')
driver = webdriver.Edge(options=edge_options)
driver.get('https://sports.qq.com/nba/players-list')
```

- 使用 ```Options()
```
 创建一个选项对象。
- ```edge_options.add_argument('--headless')
```
 添加 ```--headless
```
 参数，设置浏览器为无界面模式（即无头模式）。
- ```edge_options.add_argument('--disable-gpu')
```
 添加 ```--disable-gpu
```
 参数，禁用GPU加速，提高性能。
- 使用 ```driver = webdriver.Edge(options=edge_options)
```
 创建带有指定选项的浏览器实例。## 四、常见问题与解决方法

1. **元素定位失败**：当使用 ```find_element
```
 或 ```find_elements
```
 方法定位元素时，可能由于页面元素未加载完成或定位路径错误导致 ```NoSuchElementException
```
 异常。可以使用 ```WebDriverWait
```
 等待页面元素加载完成后再进行定位，示例代码如下：
```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

wait = WebDriverWait(driver, 10)
element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '元素选择器')))
```

1. **浏览器驱动版本不匹配**：确保下载的浏览器驱动版本与安装的浏览器版本匹配，否则可能导致驱动无法正常工作。若驱动版本不匹配，需下载并更换合适的驱动。
2. **网络问题**：在执行 ```driver.get
```
 方法时，网络不稳定可能导致页面加载失败。可以添加异常处理，如：
```python
try:
    driver.get('目标网址')
except:
    print('页面加载失败，请检查网络连接')
```
## 五、总结
本教程详细介绍了Selenium库的基本操作，涵盖了从打开网页到模拟浏览器操作的各个方面。通过学习本教程，读者可以利用Selenium实现网页自动化任务，如数据抓取、表单填写、按钮点击等。在实际应用中，根据具体需求合理运用Selenium的各种方法，并注意处理可能出现的问题，以确保程序的稳定性和可靠性。
