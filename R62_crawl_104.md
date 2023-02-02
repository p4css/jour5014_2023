

## Scraping 104.com

### Complete Code

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



### Step-by-Step

#### Get the first pages
Must loading the second page


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

#### Get the first page by modifying url

```r
# Guessing the 1st page data url to url1
url1 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=1&mode=s&jobsource=2018indexpoc"

# Getting back the 1st page data
url1 <- "https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=1&mode=s&jobsource=2018indexpoc"

result1 <- fromJSON(content(GET(url1), "text"))

df1 <- result1$data$list
```


#### Combine two data with the same variables


```r
# all.df <- bind_rows(df1, df2) # will raise error
# Error in bind_rows_(x, .id) : 
#   Argument 31 can't be a list containing data frames
```

#### Drop out hierarchical variables
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

#### Dropping hierarchical variables by dplyr way

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


#### Finding out the last page number

```r
# Tracing the number of pages in result1
last_page_num <- result1$data$totalPage
# Checking the availability of the last page

# Examining if the last page data available by re-composing URL with paste0()
url.last_page <- paste0("https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=", last_page_num, "&mode=s&jobsource=2018indexpoc")

# Getting back and parsing the last page data
result.last_page <- fromJSON(content(GET(url.last_page), "text"))
```

#### Using for-loop to get all pages

```r
for(p in 1:last_page_num){
    url <- paste0("https://www.104.com.tw/jobs/search/list?ro=0&kwop=7&keyword=%E7%88%AC%E8%9F%B2&order=1&asc=0&page=", p, "&mode=s&jobsource=2018indexpoc")
    result <- fromJSON(content(GET(url), "text"))
    temp.df <- select(result$data$list)
    print(paste(p, nrow(temp.df)))
}
```

#### combine all data.frame

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







