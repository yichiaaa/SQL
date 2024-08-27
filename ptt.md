# ptt爬蟲分析專案

## 1. 專案簡介

本專案為了爬取整個網站內容或建立多個網路爬蟲，本專案旨在使用 Python 的 Scrapy 框架來爬取網頁內容

## 2. 不同爬蟲方法的異同

| 爬蟲方法                | 優勢                                                                                   | 劣勢                                                         | 適用情境                           |
|-------------------------|----------------------------------------------------------------------------------------|--------------------------------------------------------------|------------------------------------|
| **Scrapy**              | - 提供全面的爬蟲框架<br>- 內建功能豐富（請求、解析、存儲）<br>- 適合大型、複雜專案          | - 學習曲線較陡峭<br>- 設定和調試需要較多的配置                | - 大型、複雜的爬蟲專案<br>- 伺服器端生成的靜態網頁內容 |
| **requests + BeautifulSoup** | - 使用簡單<br>- 適合小型專案<br>- 易於學習和實施                                          | - 需要手動處理請求、解析和數據儲存<br>- 不適合處理複雜的網頁內容 | - 小型專案<br>- 單一頁面的數據抓取       |
| **Selenium**            | - 能處理動態網頁<br>- 支持瀏覽器交互<br>- 適合處理複雜的網頁內容                           | - 速度較慢<br>- 需要安裝和管理瀏覽器驅動<br>- 開銷較高        | - 動態網頁<br>- 需要瀏覽器交互的情境      |
| **Jupyter Notebook**    | - 便於即時測試和調試<br>- 直接在 Notebook 中使用<br>- 適合學習和小規模的爬蟲實驗             | - 在 Notebook 中使用 Scrapy 需要特殊處理<br>- 不如命令行工具高效 | - 學習和小規模實驗<br>- 爬蟲指令測試        |

## 3. scrapy的設定和調試
使用 Scrapy 進行網頁爬取時，與其他爬蟲方式相比，需要一些額外的準備和知識：

1. **了解 Scrapy 的架構**：Scrapy 是一個框架，具有特定的架構和組件，例如爬蟲（spiders）、管道（pipelines）和中間件（middlewares），這些都需要熟悉。
   
2. **設定 Scrapy 專案**：需要設立和配置 Scrapy 專案結構，包括設定項目（settings）、爬蟲文件（spiders）和管道文件（pipelines）。

3. **使用 Selector 進行數據提取**：Scrapy 使用 Selector 來解析網頁內容，需學習如何使用 XPath 或 CSS 選擇器來提取所需數據。

4. **處理異常和錯誤**：要了解如何處理網頁響應中的異常狀況，如網頁結構變更或網頁加載錯誤。

5. **併發和延遲控制**：Scrapy 具有內建的併發機制和延遲控制功能，需設定適當的併發數量和延遲以避免對目標網站造成過大壓力。

## 4. 實作範例

以下是使用 Scrapy 框架撰寫的多頁面網路爬蟲範例，爬取了 PTT 的股票看板內容並將結果儲存為 CSV 檔案：

```python
import scrapy
import time
from ptt.items import PttPostItem

class PttStockSpider(scrapy.Spider):
    name = 'ptt_stock'
    allowed_domains = ['ptt.cc']
    start_urls = ['https://www.ptt.cc/bbs/Stock/index.html']

    def parse(self, response):
        next_page = response.css('.wide::attr(href)').getall()[1]
        posts = response.css('.r-ent')

        for post in posts:
            item = PttPostItem()
            title = post.css('.title a::text').get()
            author = post.css('.author::text').get()
            date = post.css('.date::text').get().strip()  # 移除前後空白
            url = post.css('.title a::attr(href)').get()

            if date == '3/11':
                # 停止爬取更多頁面
                break

            item['title'] = title
            item['author'] = author
            item['date'] = date
            item['url'] = url
            yield item

        # 爬取下一頁（如果日期不是 '3/11'）
        if date != '3/11':
            # 控制爬蟲速度，避免對伺服器造成負擔
            time.sleep(3)
            yield response.follow(next_page, self.parse)

```
### 以下是爬蟲執行結果的截圖：

![執行結果1](https://github.com/yichiaaa/Data_Analysis/blob/2d03fed5dfc9f6f4b074cfdb573ef68b562af4d2/ptt.png)
![執行結果2](https://github.com/yichiaaa/Data_Analysis/blob/2d03fed5dfc9f6f4b074cfdb573ef68b562af4d2/ptt1.png)
