源码：![4.3 抓取NBA球星数据-逆向分析法.py](./使用逆向分析法抓取NBA球星数据教程.assert/1746939165655-f455dd57-0a3b-4330-b258-7427a132a478.py)

## 一、教程目标
本教程将详细介绍如何使用Python通过逆向分析法从腾讯体育网站（https://sports.qq.com/nba-stats/player/list.htm）抓取NBA球星数据，并保存为CSV文件。通过本教程，你将学会以下内容：

1. 理解并运用逆向分析法找到包含球星数据的API链接。
2. 使用```requests
```
库发送HTTP请求获取数据。
3. 处理JSON格式的数据。
4. 使用```pandas
```
库将数据保存为CSV文件。## 二、环境准备
在开始编写代码之前，确保已经安装了以下Python库：

```bash
pip install requests pandas
```
这些库的作用分别为：

1. ```requests
```
：用于发送HTTP请求，获取网页或API返回的数据。
2. ```pandas
```
：用于数据处理和存储，将获取到的数据整理并保存为CSV文件。
3. ```os
```
：Python内置库，用于处理文件和目录相关操作，本教程中用于检查文件是否存在。
4. ```time
```
：Python内置库，用于设置请求间隔，避免过于频繁地请求网站，防止触发反爬机制。## 三、核心代码解析

1. **导入必要的库**：
```python
import pandas as pd
import os
import time
import requests
```
这部分代码导入了```pandas
```
、```os
```
、```time
```
和```requests
```
库，分别用于数据处理、文件操作、设置时间间隔和发送HTTP请求。

1. **定义获取JSON数据的函数**```get_json_data
```
：
```python
def get_json_data(url, params = None, referer = None, timeout = 3):
    print(f'正在抓取页面:{url}, params = {params}')
    headers = {
        'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 Edg/124.0.0.0',
        'referer': referer
    }
    try:
        response = requests.get(url,
                                params=params,
                                headers=headers,
                                timeout=timeout)
        response.raise_for_status()
        if 'json' in response.headers['Content-Type']:
            return response.json()
        else:
            return None
    except Exception as e:
        print(e)
        return None
    finally:
        time.sleep(3)
```

```plain
- `url`：要请求的API链接。
- `params`：请求参数，默认为`None`。
- `referer`：请求的来源页面，默认为`None`。
- `timeout`：请求超时时间，默认为3秒。
- `headers`：请求头，包含`User-Agent`和`referer`，用于模拟浏览器请求，避免被网站识别为爬虫。
- `requests.get(url, params=params, headers=headers, timeout=timeout)`：发送GET请求。
- `response.raise_for_status()`：如果响应状态码不是200，引发异常。
- `if 'json' in response.headers['Content-Type']:`：检查响应的`Content-Type`是否包含`json`，如果是，返回解析后的JSON数据；否则返回`None`。
- `finally`块中的`time.sleep(3)`：在请求完成后暂停3秒，降低请求频率。
```

1. **定义保存数据到CSV文件的函数**```save_data
```
：
```python
def save_data(data, file_name):
    pd_data = pd.DataFrame(data,
                           columns=data[0].keys()) 
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

```plain
- `data`：要保存的数据，应为包含字典元素的列表。
- `file_name`：要保存的CSV文件名。
- `pd_data = pd.DataFrame(data, columns=data[0].keys())`：将数据转换为`pandas`的`DataFrame`，并使用数据中第一个字典的键作为列名。
- `if not os.path.exists(file_name):`：检查文件是否存在，如果不存在，以写入模式保存数据并添加表头；如果存在，以追加模式保存数据且不添加表头。
- `encoding='utf-8-sig'`：设置编码为`utf-8-sig`，确保中文数据正确显示。
```

1. **主程序逻辑**：
```python
if __name__ == '__main__':
    for team_id in range(1, 31):
        url = r'https://matchweb.sports.qq.com/team/players' 
        params = {
            'teamId':team_id,
            'competitionId':100000
        }
        json_data = get_json_data(url, params, referer='https://sports.qq.com/')
        player_data = json_data['data'] 
        save_data(player_data, 'nba-逆向分析.csv')
```

```plain
- 使用`for`循环遍历`team_id`从1到31。
- `url = r'https://matchweb.sports.qq.com/team/players'`：设置API链接。
- `params = {'teamId':team_id, 'competitionId':100000}`：设置请求参数，`teamId`为当前循环的球队ID，`competitionId`为赛事ID。
- `json_data = get_json_data(url, params, referer='https://sports.qq.com/')`：调用`get_json_data`函数获取JSON数据。
- `player_data = json_data['data']`：从获取的JSON数据中提取包含球员数据的部分。
- `save_data(player_data, 'nba-逆向分析.csv')`：调用`save_data`函数将球员数据保存为CSV文件。
```
## 四、逆向分析法说明

1. **打开目标网页**：在浏览器中打开[https://sports.qq.com/nba-stats/player/list.htm。](https://sports.qq.com/nba-stats/player/list.htm。)
2. **检查网络请求**：打开浏览器的开发者工具（如Chrome的```Ctrl + Shift + I
```
），切换到```Network
```
选项卡。
3. **刷新页面**：刷新网页，观察```Network
```
选项卡中加载的请求。
4. **找到数据请求**：通过观察请求的```URL
```
和```Response
```
，找到包含球星数据的API请求。在本教程中，发现数据请求的链接为```https://matchweb.sports.qq.com/team/players
```
，并分析出请求参数```teamId
```
和```competitionId
```
。## 五、常见问题及解决方法

1. **请求失败**：如果请求失败，可能是网络问题或请求头设置不正确。检查网络连接，并确保```User-Agent
```
和```referer
```
设置合理。
2. **数据解析错误**：如果获取到的数据不是JSON格式，```response.json()
```
会引发异常。确保请求的链接返回的是JSON数据，或者根据实际情况调整数据解析方法。
3. **文件保存错误**：如果保存文件时出现问题，检查文件路径和权限。确保文件路径存在且有写入权限。​
