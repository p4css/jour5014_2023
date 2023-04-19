

# Scraping 104.com

## Complete Code

撰寫爬蟲時需要載入許多不同的函式庫，其中包括用於 HTTP 請求的`httr`，以及用於解析 JSON 數據的`jsonlite`。

1.  `httr`：`httr` 庫是 R 語言中用於發送 HTTP 請求和處理 HTTP 響應的函式庫，它提供了一組簡單易用的函數，可以讓使用者方便地設置 HTTP 請求的各種參數，如 URL、HTTP 方法、HTTP 頭、HTTP 主體等，並處理 HTTP 響應的內容和狀態碼等。

2.  `jsonlite`：`jsonlite` 庫是 R 語言中用於解析和生成 JSON 數據的函式庫，它提供了 `fromJSON()` 函數，可以將 JSON 字符串轉換為 R 物件，並提供 `toJSON()` 函數，可以將 R 物件轉換為 JSON 字符串。這個函式庫通常用於處理 API 回應數據中的 JSON 格式數據。


```r
library(tidyverse)
library(httr)
library(jsonlite)
# options(stringsAsFactors = F)
```


```r
all.df <- tibble()
refer_url <- "https://www.104.com.tw"

for(p in 1:10){
    url <- str_c('https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E8%B3%87%E6%96%99%E7%A7%91%E5%AD%B8&order=12&asc=0&page=', 
                 p, 
                 "&mode=s&jobsource=2018indexpoc")
    print(p)
    res <- GET(url, add_headers("referer"=refer_url)) %>%
        content("text") %>%
        fromJSON()
    
    res$data$list$tags <- NULL
    res$data$list$link <- NULL
    
    all.df <- bind_rows(all.df, res$data$list)
}

all.df$jobNo %>% unique %>% length
```

## Step-by-Step

### Get the first pages

這段程式碼是用於爬取104人力銀行網站上與「資料科學」相關的職缺資訊，並將其存儲到名為df2的Data.Frame中。首先，我們嘗試點選104人力銀行網站上的第1頁、第2頁和第3頁的職缺搜尋結果，並將這三個網址儲存為三個URL變量，應該不難觀察到，這三個網址有何差別？僅有`page=1`、`page=2`、`page=3`有所差別。接下來，我們使用R語言中的`httr`套件中的`GET()`函數，將URL作為參數傳入，從網站中獲取對應的數據，並使用`fromJSON()`函數將該JSON格式的內容轉換為R中的Data.Frame格式。下面程式在獲取第2頁數據時，使用了`add_headers()`函數設置了一個HTTP header，用於識別HTTP請求的來源。

這段程式碼使用了`add_headers`函數添加了一個名為「`Referer`」的HTTP header。這個header的作用是告訴104人力銀行網站，訪問這個頁面的用戶是從哪個網頁轉跳過來的，也就是告訴網站當前HTTP請求的來源。具體來說，這裡設置的「Referer」值為[`https://www.104.com.tw/`](https://www.104.com.tw/)，代表我們偽裝這個請求是來自於104人力銀行首頁。如果沒有這個Referer，該網站會認為你是一個可疑的爬取，從沒根據和不當的頁面或用不當的方式（例如爬蟲）連過來。

對的！人家網站不歡迎你爬它，所以我們應止於測試。


```r
url1 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=1&mode=s&jobsource=2018indexpoc"

# Assigning the 2nd page data url to url2
url2 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E8%B3%87%E6%96%99%E7%A7%91%E5%AD%B8&order=14&asc=0&page=2&mode=s&jobsource=2018indexpoc"

# Assigning the 3rd page data url to url3
url3 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=3&mode=s&jobsource=2018indexpoc"

# Getting back the url1 data, assigning to result1

res <- GET(url2, config = add_headers("Referer" = "https://www.104.com.tw/"))

res1 <- content(res, "text") %>% fromJSON()

result2 <- fromJSON(content(GET(url2), "text"))

# Tracing variable result2 and finding the data.frame, assigning to df2
df2 <- res1$data$list
```

### Get the first page by modifying url


```r
# Guessing the 1st page data url to url1
url1 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=1&mode=s&jobsource=2018indexpoc"

# Getting back the 1st page data
url1 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=1&mode=s&jobsource=2018indexpoc"

result1 <- fromJSON(content(GET(url1), "text"))

df1 <- result1$data$list
```

### Combine two data with the same variables


```r
# all.df <- bind_rows(df1, df2) # will raise error
# Error in bind_rows_(x, .id) : 
#   Argument 31 can't be a list containing data frames
```

### Drop out hierarchical variables

Preserving numeric or character, dropping list of data.frame by assigning NULL to the variable


```r
# Drop list and data.frame inside the data.frame
df1$link <- NULL
df1$tags <- NULL
df2$link <- NULL
df2$tags <- NULL

# Re-binding two data.frame df1 and df2
all.df <- bind_rows(df1, df2)
```

### Dropping hierarchical variables by dplyr way


```r
# Getting the 1st page data and dropping variable tags and link
# Assigning to df1
df1 <- result1$data$list %>% select(-tags, -link)

# Getting the 2nd page data and dropping variable tags and link
# Assigning to df2
df2 <- result2$data$list %>% select(-tags, -link)

# binding df1 and df2
all.df <- bind_rows(df1, df2)
```

### Finding out the last page number


```r
# Tracing the number of pages in result1
last_page_num <- result1$data$totalPage
# Checking the availability of the last page

# Examining if the last page data available by re-composing URL with paste0()
url.last_page <- paste0("https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=", last_page_num, "&mode=s&jobsource=2018indexpoc")

# Getting back and parsing the last page data
result.last_page <- fromJSON(content(GET(url.last_page), "text"))
```

### Using for-loop to get all pages


```r
for(p in 1:last_page_num){
    url <- paste0("https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=", p, "&mode=s&jobsource=2018indexpoc")
    result <- fromJSON(content(GET(url), "text"))
    temp.df <- select(result$data$list)
    print(paste(p, nrow(temp.df)))
}
```

### combine all data.frame


```r
#  The 1st url of the query
url1 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=1&mode=s&jobsource=2018indexpoc"

# Getting back the 1st page data
result1 <- fromJSON(content(GET(url1), "text"))

# Tracing and getting total number of page
last_page_num <- result1$data$totalPage

# Truncating hierarchical variables: link and tags
all.df <- select(result1$data$list, -link, -tags)

# for-loop to getting back data and joining them
for(p in 1:last_page_num){
    url <- paste0("https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=", p, "&mode=s&jobsource=2018indexpoc")
    result <- fromJSON(content(GET(url), "text"))
    temp.df <- select(result$data$list)
    all.df <- bind_rows(all.df, temp.df)
    print(paste(p, nrow(all.df)))
}
```
