

# Text processing in Chinese {#tmchi}

1.  tidyverse內涵繪圖和操作資料所需要的ggplot2和dplyr
2.  stringr雖然隨著tidyverse被安裝了，但不會隨著tidyverse被載入，要另外載入。
3.  在中文斷詞的時候要用到tidytext和jiebaR。
4.  處理時間時要用到lubridate。

## Preprocessing

### Assigning unique id to each doc

-   為了便於後續建立Document-Term-Matrix，這時候若Document自身沒有編號的話，就得把整個Document內容當成該篇文章的id，但也有（極小）可能有兩篇Document內容相同，那就會被視為同一篇文章或發生錯誤。所以必須要編id。
-   `row_number()`產生每列的編號，所以這邊就直接把每列的編號視為每篇文章的id，可以保持該id的唯一性。


```r
# Read rds by readRDS("data/typhoon.rds")
# mutate doc_id by row_number()
# Assign to news.df
news.df <- readRDS("data/typhoon.rds") %>%
    mutate(doc_id = row_number()) %>%
  drop_na(title)
# View(news.df)
```

## Tokenization

### Initializer tokenizer

1.  斷詞的時候不見能把我們要的字詞斷出來，比方說你可能希望台北市不會被斷開，偏偏被斷成台北+市。請參見謝舒凱老師的講義。[https://legacy.gitbook.com/book/loperntu/ladsbook/details。最簡單的方法就是窮舉，例如下面的\`segment_not\`即是](https://legacy.gitbook.com/book/loperntu/ladsbook/details。最簡單的方法就是窮舉，例如下面的%60segment_not%60即是){.uri}。


```r
# segment_not to avoid to be segmented by jeiba cutter
segment_not <- c("第卅六條", "第卅八條", "蘇南成", "災前", "災後", "莫拉克", "颱風", "應變中心", "停班停課", "停課", "停班", "停駛", "路樹", "里長", "賀伯", "採收", "菜價", "蘇迪", "受災戶", "颱風警報", "韋恩", "台東縣", "馬總統", "豪大雨", "梅姬", "台東", "台北市政府", "工務段", "漂流木", "陳菊", "台南縣", "卡玫基", "魚塭", "救助金", "陳情", "全省", "強颱", "中颱", "輕颱", "小林村", "野溪", "蚵民", "農委會", "來襲", "中油公司", "蔣總統經國", "颱風天", "土石流", "蘇迪勒", "水利署", "陳說", "颱風假", "颱風地區", "台灣", "臺灣", "柯羅莎", "八八風災", "紓困","傅崑萁", "傅崐萁","台中", "文旦柚", "鄉鎮市公所", "鄉鎮市", "房屋稅", "高雄", "未達", "台灣省", "台北市")

# Initialize jieba cutter
cutter <- worker()

# Add segment_not into user defined dictionary to avoid being cutted
new_user_word(cutter, segment_not)
```

```{.output}
## [1] TRUE
```

```r
# loading Chinese stop words
stopWords <- readRDS("data/stopWords.rds")

# load("../segment_not.R")
```

### Tokenization


```r
news.df$time %>% summary
```

```{.output}
##                       Min.                    1st Qu. 
## "1986-01-29 00:00:00.0000" "1996-10-16 00:00:00.0000" 
##                     Median                       Mean 
## "2009-08-12 00:00:00.0000" "2006-05-24 21:13:22.3911" 
##                    3rd Qu.                       Max. 
## "2012-08-21 00:00:00.0000" "2016-12-29 00:00:00.0000"
```

```r
# Mutate timestamp to filter by timestamp range
# segment by jieba cutter


tokenized.df <- news.df %>% 
  slice(-3069) %>%
  mutate(timestamp=ymd(time)) %>% 
    # filter(timestamp > as.Date("2009-01-01")) %>%
  select(-time) %>%
  select(title, text, cat, timestamp, everything()) %>%
  mutate(word = purrr::map(text, function(x)segment(x, cutter)))

# word = purrr::map(news.df$text[3069], function(x)segment(x, cutter))

# unnest() to spread character into a new word variable
# filter out stop words
# filter out alphabetical and numeric characters
unnested.df <- tokenized.df %>%
    select(doc_id, text, word) %>%
    unnest(word) %>%
    filter(!(word %in% stopWords$word)) %>%
    filter(!str_detect(word, "[a-zA-Z0-9]+"))

unnested.df %>% head
```

```{.output}
## # A tibble: 6 × 3
##   doc_id text                                                              word 
##    <int> <chr>                                                             <chr>
## 1      1 昨天颱風天，北部各地區機關、學校經主管機關宣佈放假一天。民營事業… 昨天 
## 2      1 昨天颱風天，北部各地區機關、學校經主管機關宣佈放假一天。民營事業… 颱風…
## 3      1 昨天颱風天，北部各地區機關、學校經主管機關宣佈放假一天。民營事業… 北部 
## 4      1 昨天颱風天，北部各地區機關、學校經主管機關宣佈放假一天。民營事業… 地區 
## 5      1 昨天颱風天，北部各地區機關、學校經主管機關宣佈放假一天。民營事業… 機關 
## 6      1 昨天颱風天，北部各地區機關、學校經主管機關宣佈放假一天。民營事業… 學校
```

## Exploring wording features

### Word frequency distribution


```r
word.count <- tokenized.df %>%
    unnest(word) %>%
    count(word, sort=T) %>%
    filter(!(word %in% stopWords$word)) %>%
    filter(nchar(word) > 1) %>%
    filter(!str_detect(word, "[a-zA-Z0-9]+")) %>%
    filter(n > 3)


word.count %>%
    count(n, sort=T) %>%
    ggplot(aes(n, nn)) + 
    geom_point(alpha=0.5, size = 1, color="#333333")
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-5-1.png" width="672" />

```r
word.count %>%
    count(n, sort=T) %>%
    ggplot(aes(n, nn)) + 
    geom_point(alpha=0.5, size = 1, color="#333333")  + 
    scale_x_log10() + 
    scale_y_log10()
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-5-2.png" width="672" />

### Keyness by logratio

早、近期用字差異


```r
cat_word.tf <- tokenized.df %>%
    unnest(word) %>%
    count(cat, word) %>%
    ungroup() %>%
    filter(!str_detect(word, "[a-zA-Z0-9]+")) %>%
	filter(!(word %in% stopWords$word)) %>%
    filter(nchar(word)>1)
```


```r
early_lat_ratio <- cat_word.tf %>%
	filter(n>1) %>%
	spread(cat, n, fill = 0) %>%
	ungroup() %>%
    mutate_if(is.numeric, funs((. + 1) / sum(. + 1))) %>%
	mutate(logratio = log2(early / lat)) %>%
	arrange(desc(logratio))
```


```r
early_lat_ratio %>%
	group_by(logratio > 0) %>%
	top_n(20, abs(logratio)) %>%
	ungroup() %>%
	mutate(word = reorder(word, logratio)) %>%
	ggplot(aes(word, logratio, fill = logratio < 0)) +
	geom_bar(stat = "identity") +
	coord_flip() +
	ylab("early / recent log ratio") +
	scale_fill_manual(name = "", labels = c("early", "recent"),
		values = c("tomato", "lightblue")) +
	theme(axis.text.y=element_text(colour="black", family="Heiti TC Light"))
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-8-1.png" width="672" />

### Keyness by scatter


```r
frequency <- cat_word.tf %>%
    filter(n>3) %>%
    group_by(cat) %>%
    mutate(proportion = n/sum(n)) %>%
    select(-n) %>%
    spread(cat, proportion) %>%
    na.omit()

# datatable(frequency)
```


```r
library(scales)
frequency %>%
ggplot(aes(x = early, y = lat, color = abs(early - lat))) + 
    geom_abline(color = "gray40", lty = 2) + 
    geom_point(alpha = 0.1, size = 2.5, width = 0.3, height = 0.3) + 
    geom_text(aes(label = word), check_overlap = TRUE, vjust = 1.5, family="Heiti TC Light", size = 3) +
    scale_x_log10(labels = percent_format()) +
    scale_y_log10(labels = percent_format()) +
    theme(legend.position="none") + 
    coord_fixed(1)
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-10-1.png" width="672" />

## TF-IDF

### Term-frequency


```r
word.tf <- tokenized.df %>%
    unnest(word) %>%
    count(cat, word, sort=T) %>%
    # mutate(total_words=sum(n)) %>%
    mutate(rank=row_number(), tf= n/sum(n))
```

-   如果是跑histrogram因為不是常態分佈而是power-law分佈，通常會看不出什麼來


```r
ggplot(word.tf, aes(tf, fill=cat)) + 
    geom_histogram(show.legend = F) + 
    xlim(NA, 0.0009) + 
    scale_x_log10() +
    scale_y_log10() +
    facet_wrap(~cat, ncol=2, scales="free_y")
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-12-1.png" width="672" />

-   加上rank後可以看出最高rank到最後的差異，中段差不多，但是，相較於晚近的文章，早期文章用過很少次的字比較多，但用過很多次的字比較少。（是否可推論用字越來越貧乏？）


```r
word.tf %>%
    ggplot(aes(rank, tf, color=cat)) + 
    geom_line(size=1.1, alpha=0.5) + 
    scale_x_log10() + 
    scale_y_log10()
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-13-1.png" width="672" />


```r
rank_subset <- word.tf %>%
    filter(rank < 500,
           rank > 10)

lm_result <- lm(log10(tf) ~ log10(rank), data = rank_subset)
lm_result$coefficients[[1]]
```

```{.output}
## [1] -1.739581
```


```r
word.tf %>% 
  ggplot(aes(rank, tf, color = cat)) + 
  geom_abline(intercept = lm_result$coefficients[[1]], 
              slope = lm_result$coefficients[[2]], 
              color = "gray50", linetype = 2) +
  geom_line(size = 1.1, alpha = 0.8, show.legend = FALSE) + 
  scale_x_log10() +
  scale_y_log10()
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-15-1.png" width="672" />

### TF-IDF to filter significant words

tf_idf計算結果，讓原本tf的分布從一個powewr-law分佈變成一個鐘形曲線，可以去除尾部，也就是將tf-idf很低的文字用平均和標準差刪除，重新優化要處理的字集。也可以僅取出大於平均加上一個或兩個標準差的字，作為有效辨識文章的關鍵字。


```r
news_count <- tokenized.df %>%
    unnest(word) %>%
    count(doc_id, word) %>%
    bind_tf_idf(word, doc_id, n) %>%
    arrange(desc(tf_idf))

news_count %>%
    ggplot(aes(tf_idf)) +
    geom_histogram(bins = 100) + 
    scale_x_log10()
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-16-1.png" width="672" />


```r
news_count %>% 
    left_join(news.df, by="doc_id") %>%
    filter(!(word %in% c("NA"))) %>%
    group_by(cat) %>%
    arrange(desc(tf_idf)) %>%
    top_n(30, tf_idf) %>%
    ungroup() %>%
    mutate(word = reorder(word, tf_idf)) %>%
    ggplot(aes(word, tf_idf, fill = cat)) +
    geom_col(show.legend = FALSE) +
    labs(x = NULL, y = "tf-idf") +
    facet_wrap(~cat, ncol = 2, scales = "free") +
    coord_flip() + 
    theme(axis.text.y=element_text(family="Heiti TC Light"))
```

<img src="R35_tm_typhoon_files/figure-html/unnamed-chunk-17-1.png" width="672" />
