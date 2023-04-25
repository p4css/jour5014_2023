

# (PART) CRAWLER {.unnumbered}

# Introduction to Web Scraping {#crawler-overview}

爬蟲主要分為兩大類：一類是使用網站所提供的 API，另一類則是需要寫網頁爬蟲來剖析網頁。

第一類是使用網站所提供的 API，API 是指應用程式介面，是網站提供的一種接口，用戶可以通過 API 向網站發送請求，獲取網站數據。API 可以是 RESTful API、SOAP API、XML-RPC API 等等，使用 API 的好處是可以直接獲取需要的數據，且數據格式結構化，易於處理。不過使用 API 需要瞭解 API 的調用方式和接口參數等細節，而且不是所有網站都提供 API。常見且提供API讓客戶端來取用資料的社群網站服務包含：

1.  Google Maps API：提供地圖、地理位置等相關的 API。

2.  Twitter API：提供關於 Twitter 的相關數據，包括推文、用戶資訊等。

3.  Facebook API：提供關於 Facebook 的相關數據，包括用戶資訊、頁面資訊等。

4.  GitHub API：提供關於 GitHub 的相關數據，包括存儲庫資訊、用戶資訊等。

5.  OpenWeatherMap API：提供天氣資訊的 API。

6.  YouTube API：提供關於 YouTube 的相關數據，包括影片、頻道等。

7.  Spotify API：提供關於音樂的相關數據，包括歌曲、歌手等。

第二類是需要寫網頁爬蟲來剖析網頁，這種方法通常使用一些開源的爬蟲框架，如 Python 的 Scrapy 和 Beautiful Soup、R 的 rvest 等等。網頁爬蟲是通過模擬瀏覽器的方式，向網站發送請求，獲取網頁的 HTML 源代碼，然後使用相應的庫對 HTML 進行解析和剖析，獲取需要的數據。網頁爬蟲需要考慮很多因素，如網站的反爬機制、網頁的動態載入、網頁的解析方式等等，需要編寫複雜的代碼來處理這些問題。例如爬取國內外報紙的搜尋結果大多需要直接剖析網頁來找到所需要的資料。

## Using Web API {#webapi}

使用 Web API 需要瞭解 JSON 檔案格式，JSON 是一種輕量級的數據交換格式，可以被多種語言解析和生成，是目前 Web API 調用中使用最廣泛的數據格式之一。JSON 的全稱是 JavaScript Object Notation，為基於 JavaScript 對象的一種文本格式，可以被解析為不同的數據類型，如數字、布林代數、字串、數值組和物件等。JSON 與 XML 相比，具有更輕量級、更容易讀寫和解析等優勢，也因此在 Web API 中被廣泛應用。

JSON 格式的基本結構是一個鍵-值對應集合，其中每個鍵都是一個字串，每個值可以是數字、布爾值、字串、數組或對象等類型。例如，以下是一個簡單的 JSON 。在 JSON 中，可以使用大括號 `{}` 表示鍵值對應（Key-Value），使用中括號 `[]` 表示序列（類似R中的List），鍵和值之間用冒號 `:` 分隔以對應，不同的鍵值對之間用逗號 `,` 分隔。JSON可以是樹狀多階層的，即一個鍵的值可以是另一個 JSON物件的鍵值對應。

在 R 語言中讀取 JSON 檔案需要先將其轉換為 R 的物件。這可以使用 R 的 jsonlite 套件中的 `fromJSON()` 函數來實現。`jsonlite` 套件是一個方便解析 JSON 的工具，它提供了從 JSON 字符串到 R 物件之間的轉換功能。

```         
{
    "name": "John",
    "age": 30,
    "isMarried": false,
    "hobbies": ["reading", "music", "movies"],
    "address": {
        "street": "123 Main St",
        "city": "Anytown",
        "state": "CA"
    }
}
```

## Webpage Scraping {#craw_scraping}

網頁爬蟲是一種自動化工具，可用於收集網頁上的資料。想要寫出高效能、穩定的爬蟲程式，需要掌握以下基礎知識：

1.  HTML 和 CSS：這些是用於設計和呈現網頁的標準技術。HTML 是網頁的基礎架構，而 CSS 用於設計和美化網頁的外觀。例如了解了解 HTML 標籤和屬性的基本語法和用法，以及網頁的基本結構，例如 head、body、div、span、table、a 等等。並瞭解了解 CSS 的基本語法和用法，包括如何設置元素的樣式、顏色、字體、大小、邊框等等，以及常用的選擇器和屬性。

2.  XPath 和 CSS 選擇器：XPath 和 CSS 選擇器是用於定位 HTML 元素的語言。XPath 是 XML 語言的一部分，而 CSS 選擇器是 CSS 的一部分。Chrome 瀏覽器的 Inspector 是一個強大的工具，可以幫助我們查找 HTML 元素的 XPath 和 CSS 選擇器。

3.  HTTP 協議：HTTP 協議是網絡通信協議，用於網頁服務器和瀏覽器之間的通信。理解 HTTP 協議可以幫助理解網頁如何工作。例如最常見的HTTP回應代碼有「401 Unauthorized：未經授權，無法訪問所需內容」以及「404 Not Found：所請求的內容不存在」。

4.  防止反爬：由於網站經常會採取反爬蟲措施，因此開發者需要學習如何繞過這些措施，例如使用代理服務器、設置間隔時間、更換用戶代理等。

### HTTP Status Code {#status_code}

回應代碼用於向客戶端通報伺服器對請求的處理狀態，以便客戶端根據不同的回應代碼進行相應的處理。例如，當客戶端發送一個請求到服務器時，如果服務器返回的回應代碼是 200 OK，這意味著該請求已經成功處理，服務器已經返回所需的內容，客戶端可以根據返回的內容進行下一步操作；如果服務器返回的是 404 Not Found，這意味著客戶端所請求的內容不存在，客戶端需要提示用戶請求的資源不存在。以下是 HTTP 協議中常見的一些代碼：

1.  1xx（Informational）：這些代碼表示服務器已經接收到請求，但仍在處理中。

2.  2xx（Successful）：這些代碼表示請求已經成功處理。

3.  3xx（Redirection）：這些代碼表示客戶端需要採取進一步的操作才能完成請求。

4.  4xx（Client Error）：這些代碼表示客戶端發生了錯誤，請求無法完成。

5.  5xx（Server Error）：這些代碼表示服務器發生了錯誤，無法完成請求。

以下是常見的 HTTP 協議代碼：

-   200 OK：請求已經成功處理，並返回所需的內容。

-   301 Moved Permanently：請求的網頁已經永久轉移到新位置。

-   302 Found：請求的網頁暫時轉移到新位置。

-   400 Bad Request：請求的語法不正確。

-   401 Unauthorized：未經授權，無法訪問所需內容。

-   403 Forbidden：已經獲得授權，但仍無法訪問所需內容。

-   404 Not Found：所請求的內容不存在。

-   500 Internal Server Error：服務器內部錯誤，無法處理請求。

## Using Chrome DevTools

Chrome DevTools是一款由Google開發的網頁開發工具，可以幫助開發人員進行網頁測試、網頁性能分析、網頁設計等工作。DevTools提供了豐富的功能，包括元素查看器、Console、網絡監測器、源代碼編輯器等，可讓開發人員在開發過程中快速找到和解決問題。此外，DevTools還可以幫助開發人員模擬不同設備、網速，以及對網站進行性能分析和優化，提高網站的速度和使用體驗。

### Observing web request

在開始網頁爬蟲之前，我們需要找到網頁中的JSON數據，以便進行後續的數據提取和處理。使用Chrome DevTools可以很容易地找到網頁背後的JSON檔案。以下是一些步驟：

1.  打開Chrome瀏覽器，進入要爬取的網站。

2.  按下F12鍵或右鍵點擊網頁上的任意位置並選擇「檢查」來開啟DevTools。

3.  在DevTools中，選擇「Network」分頁。

4.  在瀏覽器中執行您要查找JSON數據的操作，例如點擊一個按鈕或輸入一個查詢。

5.  在DevTools的網絡監測器中，您可以看到所有網頁請求（Request）和回應（Response），包括我們感興趣的JSON檔案。如果您只想查看JSON請求，可以在過濾器中輸入「json」。

6.  點選JSON請求，您可以查看Request和Response中的的詳細信息，包括URL、Headers、Request Payload和Response等。

7.  在Response分頁中，您可以看到JSON數據的內容。如果JSON數據很大，您可以右鍵點擊JSON數據，然後選擇「Save Response As\...」將其保存到本地檔案中。
