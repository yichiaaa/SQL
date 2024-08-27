# 基礎爬蟲專案

# LINE 電影聲量榜爬蟲專案

## 1. 專案簡介
本專案旨在使用 Python 中的 `requests` 和 `csv` 套件從 LINE 電影聲量榜抓取電影相關資料，並將其儲存為 CSV 格式。目標是從 LINE 影音 網頁抓取電影名、英文電影名、評分、電影時長及類型，並將這些資訊寫入名為 `movie.csv` 的檔案。

## 2. 使用的工具與方法
- **requests**：這個 Python 套件用於發送 HTTP 請求以獲取網頁內容。在本專案中，我們使用 `requests.get()` 方法從網頁抓取 HTML 內容。
- **BeautifulSoup**：用於解析 HTML 內容，將其轉換為 Python 可以操作的格式。這樣我們可以輕鬆地提取所需的數據。
- **csv**：Python 的 `csv` 模組用來處理 CSV 格式的檔案。透過 `csv.DictWriter`，我們可以將字典列表寫入 CSV 檔案，便於資料的儲存和後續分析。

## 3. 程式碼範例
以下是完整的 Python 程式碼，用於從 LINE 電影聲量榜抓取電影資料並將其存入 CSV 檔案：

```python
import csv
import requests
from bs4 import BeautifulSoup

# 設定請求頭，以模擬真實的瀏覽器請求
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36',
}

# 網頁 URL
URL = 'https://today.line.me/tw/v2/movie/chart/trending'

# 發出網路請求
resp = requests.get(URL, headers=headers)

# 解析網頁內容
soup = BeautifulSoup(resp.text, 'html.parser')
row_list = []

# 逐一取出電影資料
for item in soup.select('.detailListItem'):
    title = item.select_one('.detailListItem-title').get_text(strip=True)
    eng_title = item.select_one('.detailListItem-engTitle').get_text(strip=True)
    rating = item.select_one('.iconInfo-text').get_text(strip=True)
    duration = item.select_one('.text--ellipsis1').get_text(strip=True)
    genre = item.select_one('.detailListItem-category').get_text(strip=True)

    data = {
        'title': title,
        'eng_title': eng_title,
        'rating': rating,
        'duration': duration,
        'genre': genre
    }
    row_list.append(data)
    print(data)

# 設定 CSV 檔案標題
headers = ['title', 'eng_title', 'rating', 'duration', 'genre']

# 將資料寫入 CSV 檔案
with open('movie.csv', 'w', newline='', encoding='utf-8') as output_file:
    dict_writer = csv.DictWriter(output_file, headers)
    dict_writer.writeheader()  # 寫入標題
    dict_writer.writerows(row_list)  # 寫入資料

# 讀取並印出 CSV 檔案內容
with open('movie.csv', 'r', newline='', encoding='utf-8') as input_file:
    rows = csv.reader(input_file)
    for row in rows:
        print(row)
```
## 程式碼說明

- ### 請求設定
我們設置了 User-Agent 來模擬真實的瀏覽器請求，防止被目標網站屏蔽。

- ### 資料處理
使用 `requests.get()` 方法抓取網頁內容，並使用 `BeautifulSoup` 解析 HTML。這樣可以提取出電影名、英文電影名、評分、時長和類型等資訊。

- ### 儲存資料
使用 `csv.DictWriter` 將數據寫入 `movie.csv` 檔案中。首先定義標題行，然後逐一寫入每一行資料。

## 截圖
以下為程式碼執行後的部分截圖：
![執行結果1](https://github.com/yichiaaa/Data_Analysis/blob/fd25bdfd13841ceef0dba33aee9e5c5c1fc22647/line.png)

# YouTube 美食影片爬蟲專案

## 1. 專案簡介
本專案旨在使用 Python 中的 `selenium`、`BeautifulSoup` 和 `csv` 套件從 YouTube 搜尋結果抓取美食相關影片的名稱及影片時長，並將其儲存為 CSV 格式。目標是從 YouTube 搜尋「美食」後，抓取影片名稱及影片時長，並將這些資訊寫入名為 `product.csv` 的檔案。

## 2. 使用的工具與方法
- **selenium**：這個 Python 套件用於模擬瀏覽器行為，抓取前端 JavaScript 渲染的網頁內容。在本專案中，我們使用 `selenium` 來載入 YouTube 網頁並抓取所需數據。
- **BeautifulSoup**：用於解析 HTML 內容，將其轉換為 Python 可以操作的格式。這樣我們可以輕鬆地提取所需的數據。
- **csv**：Python 的 `csv` 模組用來處理 CSV 格式的檔案。透過 `csv.DictWriter`，我們可以將字典列表寫入 CSV 檔案，便於資料的儲存和後續分析。

## 3. 程式碼範例
以下是完整的 Python 程式碼，用於從 YouTube 搜尋結果抓取影片資料並將其存入 CSV 檔案：

```python
import time
import csv
from selenium import webdriver
from bs4 import BeautifulSoup

# 初始化 ChromeDriver
driver = webdriver.Chrome('./chromedriver.exe')

# 發出網路請求
driver.get('https://www.youtube.com/results?search_query=%E7%BE%8E%E9%A3%9F')
time.sleep(10)  # 等待頁面載入

# 取得頁面內容
page_content = driver.page_source
soup = BeautifulSoup(page_content, 'html.parser')

elements = soup.select('#contents ytd-video-renderer')
row_list = []

for element in elements:
    video_name = element.find('h3', {'class': 'title-and-badge'}).text.strip()
    duration = element.find('span', {'id': 'text'}).text.strip()

    data = {
        'video_name': video_name,
        'duration': duration
    }
    row_list.append(data)

# 設定 CSV 檔案標題
headers = ['video_name', 'duration']

# 將資料寫入 CSV 檔案
with open('products.csv', 'w', newline='', encoding='utf-8') as output_file:
    dict_writer = csv.DictWriter(output_file, headers)
    dict_writer.writeheader()  # 寫入標題
    dict_writer.writerows(row_list)  # 寫入資料

# 讀取並印出 CSV 檔案內容
with open('products.csv', 'r', newline='', encoding='utf-8') as input_file:
    rows = csv.reader(input_file)
    for row in rows:
        print(row)

# 關閉瀏覽器
driver.quit()
```
## 程式碼說明

- ### 初始化 WebDriver
我們使用 `selenium` 的 `webdriver.Chrome()` 初始化 Chrome 瀏覽器驅動。

- ### 頁面抓取
使用 `driver.get()` 來載入 YouTube 搜尋結果頁，並等待頁面載入完成。

- ### 資料處理
使用 `BeautifulSoup` 解析頁面內容，提取影片名稱和時長。

- ### 儲存資料
使用 `csv.DictWriter` 將數據寫入 `product.csv` 檔案中。首先定義標題行，然後逐一寫入每一行資料。


## 截圖
以下為程式碼執行後的部分截圖：
![執行結果1](https://github.com/yichiaaa/Data_Analysis/blob/fd25bdfd13841ceef0dba33aee9e5c5c1fc22647/yt.png)

