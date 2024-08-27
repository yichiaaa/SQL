# 政府公開資料 API 爬蟲專案

## 1. 專案簡介

本專案旨在使用 Python 中的 `requests` 和 `csv` 套件從政府公開資料網站抓取氣象觀測資料，並將其儲存為 CSV 格式。目標是從 [自動氣象站-氣象觀測資料](https://data.gov.tw/dataset/9176) API 獲取數據並將其寫入名為 `weather.csv` 的檔案。

## 2. 使用的工具與方法

- **`requests`**：這個 Python 套件用於發送 HTTP 請求以獲取 API 的回應資料。在本專案中，我們使用 `requests.get()` 方法來從 API 端點抓取資料，並使用 `.json()` 方法將回應內容解析為 Python 的字典格式。

- **`csv`**：Python 的 `csv` 模組用來處理 CSV 格式的檔案。透過 `csv.DictWriter`，我們可以將字典列表寫入 CSV 檔案，便於資料的儲存和後續分析。

- **`apscheduler`**：為了實現定時執行程式的功能，我們使用了 `apscheduler` 套件中的 `BlockingScheduler`。這允許我們設置一個間隔時間，定期運行我們的爬蟲程式。

## 3. 程式碼範例

以下是完整的 Python 程式碼，用於從 API 抓取資料並將其存入 CSV 檔案：

### 單次資料抓取

```python
import requests
import csv

# 設定請求頭，以模擬真實的瀏覽器請求
headers = {
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36'
}

# API 端點 URL
API_URL = 'https://opendata.cwa.gov.tw/api/v1/rest/datastore/O-A0001-001?Authorization=rdec-key-123-45678-011121314'

# 發出網路請求
resp = requests.get(API_URL, headers=headers)

# 使用 json 方法將回傳值從 JSON 格式轉成 Python dict 格式，方便存取
data = resp.json()
location_records = data['records']['Station']
row_list = []

# 一一取出資料
for location_record in location_records:
    lat = location_record['GeoInfo']['Coordinates'][0]['CoordinateFormat']
    lon = location_record['GeoInfo']['Coordinates'][0]['CoordinateName']
    location_name = location_record['StationName']
    station_id = location_record['StationId']
    obs_time = location_record['ObsTime']['DateTime']
    ele = location_record['GeoInfo']['StationAltitude'] 
    wdir = location_record['WeatherElement']['WindDirection']
    wdsd = location_record['WeatherElement']['WindSpeed']
    temp = location_record['WeatherElement']['AirTemperature']
    humd = location_record['WeatherElement']['RelativeHumidity']
    pres = location_record['WeatherElement']['AirPressure']
    
    # 將資料整理成 dict
    data = {
        'lat': lat,
        'lon': lon,
        'locationName': location_name,
        'stationId': station_id,
        'obsTime': obs_time,
        'ELE': ele,
        'WDIR': wdir,
        'WDSD': wdsd,
        'TEMP': temp,
        'HUMD': humd,
        'PRES': pres
    }

    # 加入到 row_list 中
    row_list.append(data)

# 印出資料內容，供檢查用
print(row_list)

# 設定 CSV 檔案標題
headers = ['lat', 'lon', 'locationName', 'stationId', 'obsTime', 'ELE', 'WDIR', 'WDSD', 'TEMP', 'HUMD', 'PRES']

# 將資料寫入 CSV 檔案
with open('weather_data.csv', 'w') as output_file:
    dict_writer = csv.DictWriter(output_file, headers)
    dict_writer.writeheader()  # 寫入標題
    dict_writer.writerows(row_list)  # 寫入資料
```
### 程式碼說明

1. **請求設定**：我們設置了 `user-agent` 來模擬真實的瀏覽器請求，防止被目標網站屏蔽。
2. **資料處理**：使用 `requests.get()` 方法抓取 API 的資料，並使用 `json()` 方法將 JSON 格式轉換為 Python 字典。這樣可以輕鬆地存取和處理所需的欄位資料。
3. **儲存資料**：使用 `csv.DictWriter` 將資料寫入 `weather_data.csv` 檔案中。首先定義標題行，然後將每一行資料逐一寫入。

### 截圖

以下為程式碼執行後的部分截圖：

![執行結果1](https://github.com/yichiaaa/Data_Analysis/blob/1db6bf192835c23721d69b2568fd3db633149325/api1.png)
![執行結果2]([api2.png](https://github.com/yichiaaa/Data_Analysis/blob/1db6bf192835c23721d69b2568fd3db633149325/Api2.png))

## 4. 定時執行程式

為了自動定期更新氣象資料，我們使用了 `apscheduler` 套件實現每 10 分鐘自動執行程式碼。以下是定時執行版本的程式碼：

```python
import requests
import csv
from apscheduler.schedulers.blocking import BlockingScheduler

# 定義一個函式來從 API 獲取資料
def get_api_data(API_URL):
    headers = {
        'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36'
    }
    resp = requests.get(API_URL, headers=headers)
    data = resp.json()
    return data
  
# 定義一個函式來解析 API 回應資料
def get_parse_data(data):
    location_records = data['records']['location']
    row_list = []

    for location_record in location_records:
        lat = location_record['GeoInfo']['Coordinates'][0]['CoordinateFormat']
        lon = location_record['GeoInfo']['Coordinates'][0]['CoordinateName']
        location_name = location_record['StationName']
        station_id = location_record['StationId']
        obs_time = location_record['ObsTime']['DateTime']
        ele = location_record['GeoInfo']['StationAltitude'] 
        wdir = location_record['WeatherElement']['WindDirection']
        wdsd = location_record['WeatherElement']['WindSpeed']
        temp = location_record['WeatherElement']['AirTemperature']
        humd = location_record['WeatherElement']['RelativeHumidity']
        pres = location_record['WeatherElement']['AirPressure']
        
        data = {
            'lat': lat,
            'lon': lon,
            'locationName': location_name,
            'stationId': station_id,
            'obsTime': obs_time,
            'ELE': ele,
            'WDIR': wdir,
            'WDSD': wdsd,
            'TEMP': temp,
            'HUMD': humd,
            'PRES': pres
        }
        row_list.append(data)

    return row_list

# 定義函式將資料儲存到 CSV
def save_data_to_csv(row_list):
    headers = ['lat', 'lon', 'locationName', 'stationId', 'obsTime', 'ELE', 'WDIR', 'WDSD', 'TEMP', 'HUMD', 'PRES']

    with open('weather_data.csv', 'w') as output_file:
        dict_writer = csv.DictWriter(output_file, headers)
        dict_writer.writeheader()
        dict_writer.writerows(row_list)

# 創建 Scheduler 物件實例，設定時區為台北
sched = BlockingScheduler({'apscheduler.timezone': 'Asia/Taipei'})

# decorator 設定 Scheduler 的類型和參數，例如 interval 間隔多久執行
@sched.scheduled_job('interval', minutes=10)
def timed_job():
    print('每 10 分鐘執行一次程式工作區塊')
    API_URL = 'https://opendata.cwa.gov.tw/api/v1/rest/datastore/O-A0001-001?Authorization=rdec-key-123-45678-011121314'
    data = get_api_data(API_URL)
    row_list = get_parse_data(data)
    save_data_to_csv(row_list)

# 開始執行
sched.start()
```
### 定時執行程式說明

1. **BlockingScheduler**：我們使用這個類來設置定時任務，並設定每隔 10 分鐘執行一次爬蟲程式。
2. **函式劃分**：程式碼被分割為不同的函式，以實現職責單一化。`get_api_data()` 負責抓取資料，`get_parse_data()` 負責解析資料，`save_data_to_csv()` 負責將資料儲存到 CSV 檔案中。
