源码：![4. 抓取iStudy课程信息.py](./iStudy平台获取课程数据.assert/1746936358225-099664e1-b3d5-4385-b4ac-19393d462fea.py)

## 一、教程目标
本教程将详细讲解如何使用Python编写一个爬虫程序，从深圳职业技术学院iStudy平台（https://istudy.szpu.edu.cn/）抓取课程信息，包括课程名称、教师团队和课程图片，并将这些数据保存到CSV文件中，同时下载课程图片。通过学习这个爬虫程序，你将掌握以下技能：

- 发送HTTP请求获取网页内容
- 使用BeautifulSoup解析HTML文档
- 从复杂网页结构中提取所需数据
- 实现数据的存储和图片的下载
- 提高代码的健壮性和容错能力## 二、环境准备
在开始编写爬虫之前，需要确保你的开发环境已经安装了以下Python库：

```bash
pip install requests chardet pandas beautifulsoup4
```
这些库的作用分别是：

- ```requests
```
：发送HTTP请求获取网页内容
- ```chardet
```
：自动检测网页编码
- ```pandas
```
：处理和保存数据到CSV文件
- ```beautifulsoup4
```
：解析HTML文档## 三、核心功能解析
### 1. 获取网页内容

```python
def get_html(url, params = None, timeout = 3):
    requests.DEFAULT_RETRIES = 5
    headers = {
        'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 Edg/124.0.0.0'
    }
    try:
        response = requests.get(url,
                                params=params,
                                headers=headers,
                                timeout=timeout)
        response.raise_for_status()
        response.encoding = chardet.detect(response.content)['encoding']
        return response.text
    except Exception as e:
        print(e)
        return None
    finally:
        time.sleep(2)
```
**关键点解析：**

- 设置请求头(```User-Agent
```
)伪装成浏览器，避免被网站识别为爬虫
- 使用```timeout
```
参数防止请求长时间挂起
- 自动检测网页编码，确保中文内容正确显示
- 添加异常处理，确保请求失败时程序不会崩溃
- 设置请求间隔时间，避免频繁请求导致IP被封### 2. 下载二进制内容（图片、文件等）

```python
def get_content(url, params=None, timeout=3):
    requests.DEFAULT_RETRIES = 5
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 Edg/124.0.0.0'
    }
    try:
        response = requests.get(url,
                                params=params,
                                headers=headers,
                                timeout=timeout)
        response.raise_for_status()
        type = response.headers['Content-Type'].split('/')[-1]
        return response.content, type
    except Exception as e:
        print(e)
        return None
    finally:
        time.sleep(2)
```
**关键点解析：**

- 与```get_html
```
函数类似，但返回原始二进制内容
- 从响应头中提取文件类型，用于保存文件时命名
- 同样添加了异常处理和请求间隔### 3. 保存数据到CSV文件

```python
def save_data(data, file_name):
    df = pd.DataFrame(data,
                      columns='课程名 教师 图片url'.split())
    if not os.path.exists(file_name):
        df.to_csv(file_name,
                  encoding='utf-8-sig',
                  header=True,
                  index=False)
    else:
        df.to_csv(file_name,
                  encoding='utf-8-sig',
                  header=False,
                  index=False,
                  mode='a')
```
**关键点解析：**

- 使用pandas的DataFrame处理表格数据
- 自动创建表头（第一次写入时）
- 使用```utf-8-sig
```
编码确保中文正常显示
- 支持追加模式写入数据，避免覆盖已有内容### 4. 从单个课程标签提取数据

```python
def get_one_course_data(dd_tag):
    name_tag = dd_tag.select_one('div.recom_bot > h3 > a')
    name = name_tag.text.strip() if name_tag is not None else None
    teacher_tag = dd_tag.select_one('div.recom_bot > p > span.recom_bg')
    teacher = teacher_tag.text.strip() if teacher_tag is not None else None
    img_tag = dd_tag.select_one('div.recom_pic > a > img')
    img_url = img_tag.get('src') if img_tag is not None else None
    return name, teacher, img_url
```
**关键点解析：**

- 使用CSS选择器定位特定元素
- 对每个元素进行判空检查，提高代码健壮性
- 使用```strip()
```
方法去除文本中的空白字符
- 直接通过```get()
```
方法获取标签属性值### 5. 从整个HTML页面提取多课程数据

```python
def get_data(html):
    bs = BeautifulSoup(html, 'lxml')
    dl_tag = bs.select_one('body > div.recom_cont.wrap > dl')
    if dl_tag is None:
        return None
    dd_tags = dl_tag.select('dd')
    data = [get_one_course_data(dd_tag) for dd_tag in dd_tags]
    return data
```
**关键点解析：**

- 先定位到包含所有课程的父标签
- 对父标签进行判空检查
- 使用列表推导式简化代码，一次性处理所有课程项### 6. 下载并保存图片

```python
def download_pic(course_name, pic_url):
    content, type = get_content(pic_url)
    if content is None:
        return
    file_name = os.path.join(pic_dir, f'{course_name}.{type}')
    with open(file_name, 'wb') as f:
        f.write(content)
```
**关键点解析：**

- 调用```get_content
```
获取图片二进制内容
- 使用课程名称作为图片文件名，确保唯一性
- 使用二进制写入模式保存图片文件## 四、主程序流程

```python
if __name__ == '__main__':
    file_name = 'iStudy.csv'
    pic_dir = './pic'
    for i in range(1, 3):
        url = f'https://istudy.szpu.edu.cn/portal/schoolCourseInfo/columnCourse?columnId=1&pageNum={i}'
        html = get_html(url)
        if html is None:
            print('抓取页面失败')
            continue

        data = get_data(html)
        for course in data:
            course_name, teacher, pic_url = course
            print(f'正在下载图片：{pic_url}')
            download_pic(course_name, pic_url)

        save_data(data, file_name)
```
**关键点解析：**

- 生成多页URL，实现分页抓取
- 对每一页数据进行处理和保存
- 下载每门课程的图片到指定目录
- 实现了基本的错误处理（跳过抓取失败的页面）## 五、注意事项

1. 爬取网站数据时，请遵守网站的robots.txt规则和相关法律法规
2. 控制爬取频率，避免对目标网站造成压力
3. 建议在本地测试环境中运行，确认代码无误后再正式部署
4. 网站结构可能随时变化，需要定期维护爬虫代码​
