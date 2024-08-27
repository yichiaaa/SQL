## 網頁爬蟲分析專案
- **基礎爬蟲專案**
  - LINE電影聲量與Youtube網頁爬蟲結果
  - 使用 requests 和 BeautifulSoup 處理靜態頁面，selenium 處理動態頁面。
  - 網站連結: [單頁爬蟲](https://github.com/yichiaaa/Data_Analysis/blob/9fd793a3f3a5a1879054e16a11053b0fc7953fa7/da.md)

- **政府公開資料API爬蟲專案**
  - 資料抓取與處理：用 `requests` 套件從 API 取得氣象數據，轉換為 Python 字典進行處理。
  - 資料儲存：使用 `csv.DictWriter` 將數據寫入 `weather_data.csv`。
  - 定時自動執行：利用 `apscheduler` 每 10 分鐘自動更新 CSV 檔案。
  -  網站連結: [open API爬蟲執行方式](https://github.com/yichiaaa/Data_Analysis/blob/e63738b99f10eb8e388ef4d13cee8939290bc0c5/open_api.md)

- **ptt爬蟲分析專案**
  - 使用 Scrapy 框架爬取 PTT 股票看板的內容，並儲存為 CSV 檔案。
  - 了解Scrapy 優勢與要求：Scrapy 提供全面框架，適合大型專案，但需了解架構設定和數據處理。
  - 展示了如何爬取多頁面內容，控制爬蟲速度，並處理數據存儲。
  - 網站連結: [ptt多頁爬蟲](https://github.com/yichiaaa/Data_Analysis/blob/41b8fea74993931339a6e837df06d14f27b8ee50/ptt.md)
  
