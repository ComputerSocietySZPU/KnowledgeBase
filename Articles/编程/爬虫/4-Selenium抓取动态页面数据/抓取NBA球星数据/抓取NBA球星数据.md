源码：![4.2 抓取NBA球星数据.py](./抓取NBA球星数据.assert/1746938829224-84674989-3b74-44a9-95b8-8c0ea4c2e4d4.py)

## 一、教程目标
本教程旨在教会你如何运用Python中的Selenium库模拟浏览器操作，从腾讯体育NBA数据页面抓取球星数据，并将其保存为CSV文件。学习完成后，你将掌握以下技能：

1. 配置并使用Selenium库结合Edge浏览器驱动进行网页操作。
2. 运用CSS选择器在网页中精准定位元素。
3. 模拟浏览器的滚动、点击等操作。
4. 从网页元素中提取关键数据。
5. 使用pandas库处理和存储数据。## 二、环境准备
### （一）安装浏览器驱动
为了让Selenium能够控制Edge浏览器，需要下载并安装与你所使用的Edge浏览器版本相匹配的驱动程序。可以前往Microsoft Edge WebDriver官方网站进行下载。下载完成后，将驱动程序所在路径添加到系统环境变量中，这样在代码中调用```webdriver.Edge()
```
时，程序就能找到驱动。如果不想配置环境变量，也可以在代码中通过```executor_path
```
参数指定驱动路径，例如：```driver = webdriver.Edge(executor_path='驱动程序路径')
```
。
### （二）安装Python库
通过pip命令安装本教程所需的Python库：

```bash
pip install selenium pandas numpy
```

- ```selenium
```
：用于驱动浏览器，模拟用户操作并获取网页数据。
- ```pandas
```
：主要用于数据处理和将数据保存为CSV文件。
- ```numpy
```
：虽在本教程中未大量使用，但在数据处理的一些复杂场景中可能会用到，它为Python提供了高性能的数组处理能力。## 三、代码实现与解析
### （一）导入必要的库

```python
from selenium import webdriver
from selenium.webdriver.edge.options import Options
from selenium.webdriver.common.by import By
import pandas as pd
import os
import time
import numpy as np
```

- ```from selenium import webdriver
```
：引入```selenium
```
库中的```webdriver
```
模块，用于创建和控制浏览器实例。
- ```from selenium.webdriver.edge.options import Options
```
：导入```Options
```
类，用于设置Edge浏览器的启动选项，比如设置为无头模式等。
- ```from selenium.webdriver.common.by import By
```
：```By
```
类提供了多种定位网页元素的方式，如通过CSS选择器、XPath等。
- ```import pandas as pd
```
：导入```pandas
```
库，并将其别名为```pd
```
，用于数据处理和文件存储。
- ```import os
```
：用于与操作系统进行交互，例如检查文件是否存在等操作。
- ```import time
```
：提供时间相关的函数，用于设置程序的等待时间，确保网页元素加载完成后再进行操作。
- ```import numpy as np
```
：导入```numpy
```
库，并将其别名为```np
```
，用于数值计算和处理，本教程中主要用于数据处理相关操作。### （二）数据保存函数

```python
def save_data(data, file_name):
    pd_data = pd.DataFrame(data,
                           columns='姓名 英文名 号码 位置'.split())
    if not os.path.exists(file_name): 
        pd_data.to_csv(file_name,
                       header=True,
                       index=False,
                       encoding='utf-8-sig')
    else:
        pd_data.to_csv(file_name,
                       header=False,
                       index=False,
                       encoding='utf-8-sig',
                       mode = 'a+') 
```

- 该函数接受两个参数：```data
```
（包含球员数据的列表，每个元素是一个包含数据字段的元组）和```file_name
```
（保存数据的文件名）。
- 使用```pandas
```
的```DataFrame
```
将数据转换为表格形式，并指定列名。
- 通过```os.path.exists(file_name)
```
检查文件是否存在。若文件不存在，则以写入模式保存数据，并添加表头；若文件已存在，则以追加模式保存数据，且不添加表头，避免覆盖已有内容。
- 设置```encoding='utf-8-sig'
```
确保中文数据在CSV文件中能正确显示。### （三）提取单个球员数据函数

```python
def get_one_player_data(tr):
    name_elem = tr.find_element(By.CSS_SELECTOR,'td:nth-child(2) > a')
    name = name_elem.text

    english_name_elem = tr.find_element(By.CSS_SELECTOR, 'td.en > a')
    english_name = english_name_elem.text

    number_elem = tr.find_element(By.CSS_SELECTOR, 'td:nth-child(4)')
    number = number_elem.text

    position_elem = tr.find_element(By.CSS_SELECTOR, 'td:nth-child(5)')
    position = position_elem.text

    return name, english_name, number, position
```

- 函数接受一个代表单个球员数据行的```tr
```
标签作为参数。
- 使用```By.CSS_SELECTOR
```
定位方式，通过CSS选择器分别定位包含球员姓名、英文名、号码和位置信息的网页元素。
- 使用元素的```text
```
属性获取文本内容，并返回包含这些信息的元组。### （四）提取一支球队球员数据函数

```python
def get_one_team_data(driver):
    table_elem = driver.find_element(By.CSS_SELECTOR,
                                     'body > div.container.cf > div.players > table > tbody')
    tr_elems = table_elem.find_elements(By.CSS_SELECTOR,
                                    'tr')
    data = [get_one_player_data(tr) for tr in tr_elems] 
    save_data(data, 'nba.csv')
```

- 该函数接受一个浏览器驱动对象```driver
```
作为参数。
- 首先使用CSS选择器定位包含球队所有球员数据的表格元素```table_elem
```
。
- 接着在表格元素内定位每个球员所在的```tr
```
标签。
- 使用列表推导式遍历每个```tr
```
标签，调用```get_one_player_data
```
函数提取单个球员数据，并将所有球员数据收集到一个列表中。
- 最后调用```save_data
```
函数，将该球队的球员数据保存到```nba.csv
```
文件中。### （五）主程序逻辑

```python
if __name__ == '__main__':
    url = r'https://sports.qq.com/nba-stats/player/list.htm'
    driver = webdriver.Edge()
    driver.get(url)
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);") 
    time.sleep(2)
    team_elems = driver.find_elements(By.CSS_SELECTOR, 'body > div.container.cf > div.teams > table > tbody > tr > td > a')
    for team_elem in team_elems:
        driver.execute_script("arguments[0].click();", team_elem) 
        print(f'正在抓取{driver.current_url}')
        time.sleep(2)  
        get_one_team_data(driver)
```

- 在```if __name__ == '__main__':
```
代码块中，程序开始执行。
- 定义目标网址```url
```
，创建Edge浏览器驱动实例```driver
```
，并使用```driver.get(url)
```
打开目标网页。
- 通过```driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
```
执行JavaScript代码，将页面滚动到最底部，确保所有数据加载完整。
- 使用```time.sleep(2)
```
暂停2秒，等待页面加载完成。
- 使用CSS选择器定位所有球队的标签元素```team_elems
```
。
- 遍历每个球队标签，通过```driver.execute_script("arguments[0].click();", team_elem)
```
模拟点击操作，切换到相应球队的数据页面。
- 打印当前正在抓取数据的页面URL，再暂停2秒等待页面加载。
- 调用```get_one_team_data(driver)
```
函数，提取并保存该球队的球员数据。## 四、常见问题及解决方法
### （一）元素定位失败

1. **原因**：
- 网页元素加载延迟，在定位元素时，元素尚未在页面中呈现。
- CSS选择器或其他定位方式使用错误，导致无法准确找到目标元素。
1. **解决方法**：
- 使用```WebDriverWait
```
等待元素加载完成。例如：
```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

wait = WebDriverWait(driver, 10)
element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '元素选择器')))
```

```plain
- 仔细检查CSS选择器的正确性，可以在浏览器开发者工具中使用该选择器进行测试，确保能准确选中目标元素。
```
### （二）浏览器驱动版本不匹配

1. **原因**：下载的浏览器驱动版本与所使用的Edge浏览器版本不一致。
2. **解决方法**：前往Microsoft Edge WebDriver官方网站，下载与当前Edge浏览器版本完全匹配的驱动程序，并重新配置环境变量或在代码中指定正确的驱动路径。### （三）网络问题

1. **原因**：网络不稳定或网速较慢，导致页面加载失败或加载不完整。
2. **解决方法**：添加异常处理代码，在页面加载失败时进行提示或重试。例如：
```python
try:
    driver.get('目标网址')
except Exception as e:
    print(f'页面加载失败，原因是: {e}，请检查网络连接')
```
## 五、总结
本教程详细介绍了如何利用Selenium库模拟浏览器操作来抓取NBA球星数据，并使用pandas库将数据保存为CSV文件。在实际应用中，你可以根据具体需求对代码进行优化和扩展，比如增加数据清洗、数据去重等功能。同时，要注意遵守网站的使用规则和法律法规，避免过度抓取数据对网站造成不必要的负担。
