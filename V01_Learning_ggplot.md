## ggplot簡介 {#ggplot}

本節著重在介紹`ggplot`的基本概念與設定。



**訣竅：**可在一開始便透過`knitr::opts_chunk$set(echo = TRUE, fig.width = 2, fig.asp = 0.4)`來一次設定所有圖片。`fig.width = 8`與`fig.height = 6` 是以英吋（inches）為單位，或用`fig.dim = c(8, 6)`一次設定長寬[^v01_learning_ggplot-1]。`echo = TRUE`是設定knit出輸出格式（如html）時，也要包含程式碼。如果`echo = FALSE`的話，就只會輸出文字和圖形。

[^v01_learning_ggplot-1]: 參考資料：[5.4 Control the size of plots/images \| R Markdown Cookbook (bookdown.org)](https://bookdown.org/yihui/rmarkdown-cookbook/figure-size.html)

## 繪圖基礎：折線圖

### 繪圖三要素

用ggplot來繪製圖形有三個基本函式`ggplot()` + `aes()` + `geom_圖表類型`。

1.  **指定要進行繪圖`ggplot()`**：用`%>%`將資料（dataframe）pipe給`ggplot()`後，底下各增添的繪圖選項都用`+`的符號，類似不斷修正繪圖結果的意思。
2.  **指定X／Y軸與群組因子`aes()`**：指定圖表的X/Y軸分別是什麼變數，有些圖表只需要單一個變數（例如Density-chart和Histogram），有些需要X/Y兩個變數（例如Scatter-chart）什麼的變數要做視覺化，Boxplot甚至可以直接指定最大、最小、Q1、Q3和Median等多個變數。
3.  **指定要繪製的圖表類型**。例如Line-chart為`geom_line()`、Scatter-chart為`geom_point()`、Bar-chart為`geom_col()`或`geom_bar()`。查閱[ggplot cheat sheet](https://www.maths.usyd.edu.au/u/UG/SM/STAT3022/r/current/Misc/data-visualization-2.1.pdf)可以快速翻閱有哪些圖表類型（如截圖）。

![ggplot-cheat-sheet](images/paste-E7629FF5.png){alt="ggplot-cheat-sheet"}

#### `ggplot()` 會秀出預備要繪製的繪圖區


```r
tibble(a=1:5, b=5:1) %>%
    ggplot()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-2-1.png" width="576" style="display: block; margin: auto;" />

#### **指定X／Y軸與群組因子`aes()`**：`aes()`會在繪圖區上繪製X與Y軸


```r
tibble(a=1:5, b=5:1) %>%
    ggplot() +
    aes(x=a, y=b)
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-3-1.png" width="576" style="display: block; margin: auto;" />

#### **指定要繪製的圖表類型**。

例如折線圖為為`geom_line()`、X/Y散佈圖為`geom_point()`、長條圖我多會使用`geom_col()`。


```r
tibble(a=1:5, b=5:1) %>%
    ggplot() +
    aes(x=a, y=b) + 
    geom_line()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-4-1.png" width="576" style="display: block; margin: auto;" />

亦可同時繪製兩種類型的圖表於同一張圖上。例如以下同時繪製了`geom_line()`與`geom_plot()`。


```r
tibble(a=1:5, b=5:1) %>%
    ggplot() +
    aes(x=a, y=b) + 
    geom_line() + 
    geom_point()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-5-1.png" width="576" style="display: block; margin: auto;" />

### 範例-紐時世代貧富不均

[Teach About Inequality With These 28 New York Times Graphs - The New York Times (nytimes.com)](https://www.nytimes.com/2021/05/11/learning/lesson-plans/teach-about-inequality-with-these-28-new-york-times-graphs.html)

ggplot是以變數為基礎的視覺化套件，也就是說，當準備好dataframe後，就可以在ggplot中指定要用哪些變數來繪圖。也因此，務必把dataframe整理為tidy型態，也就是長表格（long-form）的型態。整理完資料後，我會習慣地用`names(plot)`或`glimpse(plot)`來看一下該資料所有的變項，好可以在下一階段的繪圖做參考。


```r
NW <- read_csv("data/interactive_bulletin_charts_agecl_median.csv") %>%
    select(Category, year, Net_Worth)  %>%
    group_by(Category) %>%
    arrange(year) %>%
    ungroup()
```

```
## Rows: 66 Columns: 37
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (1): Category
## dbl (36): year, Before_Tax_Income, Net_Worth, Assets, Financial_Assets, Tran...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
NW %>% glimpse()
```

```
## Rows: 66
## Columns: 3
## $ Category  <chr> "Less than 35", "35-44", "45-54", "55-64", "65-74", "75 or o…
## $ year      <dbl> 1989, 1989, 1989, 1989, 1989, 1989, 1992, 1992, 1992, 1992, …
## $ Net_Worth <dbl> 16.17019, 112.47530, 195.11630, 195.25554, 154.34277, 144.29…
```

這是預期視覺化的結果

![](images/paste-B46B3CD7.png)

先將`year`和`Net_worth`分別繪製在X與Y軸上，並用`geom_line()`繪製為折現圖。結果圖表中呈現鋸齒狀的折線，看似有問題，但其實是合理的。因為`year`是一個離散變數，而我們希望每個年齡層一條線的話，那就要照年齡層來分組。也因此，每一年都有有每個年齡層的資料，當我們把「年」作為X軸時，自然同一年就會有數筆不同年齡層的資料，因此才會是鋸齒狀的。


```r
NW %>%
    ggplot() + 
    aes(x=year, y=Net_Worth) + 
    geom_line()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-7-1.png" width="576" style="display: block; margin: auto;" />

**不同的圖表類型是可以疊加在同一張圖上的**。我們也可以把`geom_point()` 另一種圖表型態加入，也是可以的，兩者的X與Y不相衝突。`geom_line()`、`geom_point()`、`geom_text()`三者會經常伴隨出現。


```r
NW %>%
    ggplot() + 
    aes(x=year, y=Net_Worth) + 
    geom_line() +
    geom_point()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-8-1.png" width="576" style="display: block; margin: auto;" />

### 加入自變項群組

上圖是我們把多個年齡層的逐年資料畫在同一條折線上，所以會呈現鋸齒狀折現的狀況。但這些年齡層並非在同一條線上呀？因此，我們要根據`Category`這個變數來做分組。


```r
NW %>%
    ggplot() + 
    aes(x=year, y=Net_Worth, group=Category) + 
    geom_line() + 
    geom_point(stat="identity")
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-9-1.png" width="576" style="display: block; margin: auto;" />

如希望不同線條上不一樣的色彩，應指定`color=Category`。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-10-1.png" width="576" style="display: block; margin: auto;" />

用color、fill或group來做分組？

:   `geom_line()`的色彩是在**線**，而不是在**面**上。如果色彩是在點（如`geom_point()`）或線（`geom_line()`）上，就是用`color`來指定顏色。但如果是如類似下面的例子，用`geom_area()`來視覺化的話，因為顏色填的是**面**，所以要用`fill=Category`。以下範例甚至同時指定`color=Category, fill=Category`。但折線圖如果要用`geom_area()`來視覺化的話，最好要上顏色的不要超過二個，不然就會像底下這個例子一樣，即使設定`alpha=0.2`的半透明，仍然會看不懂哪些顏色疊在一起。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category, fill=Category) + 
    geom_area(position="dodge", alpha=0.2)
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-11-1.png" width="576" style="display: block; margin: auto;" />

## 圖表調整

### 點線型態

下面的例子同時用了`geom_line()`和`geom_point()`，且分別設定了線寬（`size=1`）、點的大小(`size=2`)，折線型態（`linetype="dashed"`）、半透明程度（`alpha`）。

[ggplot2 line types : How to change line types of a graph in R software? - Easy Guides - Wiki - STHDA](http://www.sthda.com/english/wiki/ggplot2-line-types-how-to-change-line-types-of-a-graph-in-r-software)


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line(size=1, linetype = "dashed", alpha=0.5) + 
    geom_point(size=2, color="dimgrey", alpha=0.5)
```

```
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-12-1.png" width="576" style="display: block; margin: auto;" />

### 標題、標籤與圖說

Titles, labels, and legend **設定標題與X／Y軸標題（法一）**：以下設定了圖表的圖表標題、和X軸與Y軸的軸標題（`xlab`與`ylab`）。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line() + 
    theme_minimal() + 
    xlab("Year") + 
    ylab("Net Worth") + 
    ggtitle("Net Worth by year grouped by age groups")
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-13-1.png" width="576" style="display: block; margin: auto;" />

**設定標題與X／Y軸標題（法二）**：這是一次設定圖表標題（`title`）、次標題（`suttitle`）、X軸與Y軸標題的方法。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line() +  
    theme_minimal() + 
    labs(title = "Net Worth by year grouped by age groups",
         subtitle = "Source from: ...",
         x = "Year",
         y = "Net Worth")
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-14-1.png" width="576" style="display: block; margin: auto;" />

**調整X軸與Y軸標題位置的**：必須要透過`theme()`來設定`axis.title.x = element_text(hjust=1)`。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line() + 
    theme_minimal() + 
    labs(title = "Net Worth by year grouped by age groups",
         x = "Year",
         y = "Net Worth") + 
    theme(axis.title.x = element_text(hjust=1),
          axis.title.y = element_text(hjust=1))
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-15-1.png" width="576" style="display: block; margin: auto;" />

**去除X／Y軸標題（不佳）**：直接將空字串Assign給`title`、`x`、與`y`即可。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line() + 
    theme_minimal() + 
    labs(title = "", x = "", y = "")
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-16-1.png" width="576" style="display: block; margin: auto;" />

**去除X／Y軸標題（較佳）**：透過設定`theme()`來調整。可發現透過這種設定方法，原本標題和X／Y軸標題的邊界空間就會被釋放出來。


```r
# No extra space for xlab, ylab and title
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line(show.legend = F) + 
    theme_minimal() + 
    theme(plot.title = element_blank(),
          axis.title.x = element_blank(),
          axis.title.y = element_blank())
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-17-1.png" width="576" style="display: block; margin: auto;" />

### 字體樣式

調整字型會建議都從`theme()`來做調整，所有圖面上看得到的字都有相對應的變數可以調整字型。例如以下的例子中，把標題的字型大小調整為14粗體、X與Y軸的字型則調整了向右對齊、10粗斜體、顏色為dimgrey。


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line() + 
    theme_minimal() + 
    labs(title = "Net Worth by year grouped by age groups",
         x = "Year",
         y = "Net Worth") + 
    theme(plot.title = element_text(size=14, face="bold"),
          axis.title.x = element_text(hjust=1, size=10,
                                      color="dimgrey", 
                                      face="bold.italic"),
          axis.title.y = element_text(hjust=1, size=10,
                                      color="dimgrey", 
                                      face="bold.italic")
          )
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-18-1.png" width="576" style="display: block; margin: auto;" />

### 圖表主題色調

ggplot也有其圖表主題色調。之前範例的灰色圖表背景就是預設的主題，ggplot中還有好幾個預設圖表主題可以選，例如`theme_minimal()`或`theme_tw()`等等。

-   [Modify components of a theme --- theme • ggplot2 (tidyverse.org)](https://ggplot2.tidyverse.org/reference/theme.html)
-   [bbplot/bbc_style.R at master · bbc/bbplot (github.com)](https://github.com/bbc/bbplot/blob/master/R/bbc_style.R)


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line() + 
    theme_minimal()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-19-1.png" width="576" style="display: block; margin: auto;" />

### 預設主題

如果希望所有的圖表都有一致的顏色和排版的調性，可以在一開始編輯Rmd的時候就設計好一套`theme()`並指給一個變數（例如以下的`th`）。


```r
th <- theme(plot.title = element_text(size=14, face="bold"),
          axis.title.x = element_text(hjust=1, size=10,
                                      color="dimgrey", 
                                      face="bold.italic"),
          axis.title.y = element_text(hjust=1, size=10,
                                      color="dimgrey", 
                                      face="bold.italic")
          )

NW %>%    
    ggplot() + aes(year, Net_Worth, color=Category) + 
    geom_line(linetype = "dashed", alpha=0.5) + 
    geom_point(size=2, color="dimgrey", alpha=0.5) + 
    theme_minimal() + 
    labs(title = "Net Worth by year grouped by age groups",
         x = "Year",
         y = "Net Worth") + th
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-20-1.png" width="576" style="display: block; margin: auto;" />

### 顯示中文字體

Python和R這些程式語言的預設視覺化套件都沒辦法顯示中文，所以如果要顯示中文的話，就要指定圖表標題、X、Y軸標籤、圖說和各個部件的字型。因為我在Mac上繪圖，所以我將字型指定為**Heiti TC Light**。如果想知道自己的電腦上有什麼可以用，可以到電腦的字體簿上查找中文字體名稱，或者上網google「ggplot 中文字型選擇」。

![](images/paste-A259BC75.png)


```r
county <- read_csv("data/tw_population_opendata110N010.csv") %>%
    slice(-1, -(370:375)) %>%
    type_convert() %>%
    mutate(county = str_sub(site_id, 1, 3)) %>%
    group_by(county) %>%
    summarize(
        area = sum(area), 
        people_total = sum(people_total)
    ) %>%
    ungroup()
```

```
## Rows: 375 Columns: 5
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): statistic_yyy, site_id, people_total, area, population_density
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
## 
## ── Column specification ────────────────────────────────────────────────────────
## cols(
##   statistic_yyy = col_double(),
##   site_id = col_character(),
##   people_total = col_double(),
##   area = col_double(),
##   population_density = col_double()
## )
```

下面這是一個長條圖的範例（barplot，不是histogram）。Barplot可以直接指定X軸為縣市（county）和Y軸為總人口數（people_total），但是要用`geom_col()`而非`geom_bar()`。除此之外，Bar的顏色有「面」的特徵，所以若要自訂整條bar的顏色，要用`fill`而非`color`，`color`只會是每條Bar的外框。


```r
county %>%
    arrange(desc(people_total)) %>%
    ggplot() + aes(county, people_total) %>%
    geom_col(fill="lightgrey", color="black") +
    theme_minimal()
```

<img src="V01_Learning_ggplot_files/figure-html/barplot-1.png" width="576" style="display: block; margin: auto;" />

舉例來說，中文字型可以是標楷體（BiauKai）、宋體（Songti TC）、黑體（Heiti TC Light）、蘋方（PingFang TC）、Noto（Noto Sans CJK TC）


```r
th <- 
  theme(title = element_text(family="Heiti TC Light"),
        text = element_text(family="Heiti TC Light"), 
        axis.text.y = element_text(family="PingFang TC"),
        axis.text.x = element_text(family="Heiti TC Light"),
        legend.text = element_text(family="Heiti TC Light"),
        plot.title = element_text(family="Heiti TC Light")
        )
county %>%
  ggplot() + aes(county, people_total) %>%
  geom_col(fill="skyblue") +
  theme_minimal() + th + 
  theme(axis.text.x = element_text(angle = 45))
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-22-1.png" width="576" style="display: block; margin: auto;" />

### X/Y軸方向

調整圖表方向


```r
county %>%
  ggplot() + aes(county, people_total) %>%
  geom_col(fill="skyblue") +
  coord_flip() + 
  theme_minimal() + th + 
  theme(axis.text.x = element_text(angle = 45))
```

<img src="V01_Learning_ggplot_files/figure-html/coord_flip()-1.png" width="576" style="display: block; margin: auto;" />

通常`coord_flip()`後往往會希望這些bar會是由上而下排序好的，但用`arrange(desc(people_total)`是無法解決問題的，因為Y軸原本會是照Y軸的刻度排列，而不是Y軸的數值。所以，要被排序的應該是Y軸的「文字」也就是那些縣市。因此，我們需要將該縣市轉為factor（1\~n），並且讓這些縣市被安排的factor數值照`people_total`排列，因此要用`mutate(county = reorder(county, people_total))`。`reorder()`是一個將文字轉factor的函式，但在此特別指定照`people_total`的編排。


```r
county %>%
  # arrange(desc(people_total) %>%
  mutate(county = reorder(county, people_total)) %>%
  ggplot() + aes(county, people_total) %>%
  geom_col(fill="skyblue") +
  coord_flip() + 
  theme_minimal() + th
```

<img src="V01_Learning_ggplot_files/figure-html/coord_flip-with-reorder-1.png" width="576" style="display: block; margin: auto;" />

## 視覺化說故事的技巧

「說故事」才是整則資料新聞的核心，在運用圖表來輔助敘事時，應搭配說理說服的內容來突顯（highlight）圖面上的特徵，而不是留待讀者自己觀察。以下有三種highlight圖表部分資料的方法。第一個方法是在繪圖時用`+ scale_color_manual()`或`+ scale_fill_manual()`指定顏色給不同群組；方法二是利用`gghighlight`這個套件來指定要上色的群組，而且`gghighlight`可以和`fill`與`color`相互搭配，仍然可以用`scale_fill_manual`和`scale_color_manual`來指定顏色。但會有個狀況是，如果原本沒群組那怎麼辦？就自己用`mutate()`打造群組就好。方法各有利弊與使用時機。

### 依群組指定顏色

`scale_color_manual()` 與`scale_fill_manual()`


```r
NW %>% 
    ggplot() + aes(year, Net_Worth, color = Category) + 
    geom_line() + 
    scale_color_manual(
        limits=c("65-74", "35-44"),   # original chart group
        values=c("gold", "skyblue"),  # map to color
        name="Age group",             # legend title
        breaks=c("65-74", "35-44"),   # original legend group labels
        labels=c("elder(65-74)","younger(35-44)"), # map to new labels
        na.value = "lightgrey" # color for other groups
        ) +
    theme_minimal()
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-23-1.png" width="576" style="display: block; margin: auto;" />

### 使用gghighlight套件


```r
library(gghighlight)
NW %>%    
    ggplot() + aes(year, Net_Worth, color = Category) + 
    geom_line() + 
    gghighlight(Category %in% c("65-74", "35-44")) + 
    theme_minimal() + 
    scale_x_continuous(breaks = NULL) + 
    theme(panel.background = element_rect(fill = "whitesmoke",
                                colour = "whitesmoke",
                                size = 0.5, linetype = "solid"))
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-24-1.png" width="576" style="display: block; margin: auto;" />

使用`gghighlight`仍能自己使用`scale_color_manual()`來指定顏色


```r
NW %>%    
    ggplot() + aes(year, Net_Worth, color = Category) + 
    geom_line() + 
    gghighlight(Category %in% c("65-74", "35-44")) + 
    scale_color_manual(
        limits=c("65-74", "35-44"),   # original chart group
        values=c("gold", "skyblue")) +   # map to color
    theme_minimal()
```

```
## Warning: Tried to calculate with group_by(), but the calculation failed.
## Falling back to ungrouped filter operation...
```

```
## label_key: Category
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-25-1.png" width="576" style="display: block; margin: auto;" />

### 為視覺化建立群組

這個方法是在原本的資料並沒有可以作為color或fill的因子，所以自行創建一個要突顯的群組。


```r
county %>%
  mutate(group = if_else(county %in% c("新竹縣", "新竹市"), "highlight", "other")) %>%
  mutate(county = reorder(county, people_total)) %>%
  ggplot() + aes(county, people_total, fill=group) %>%
  geom_col() +
  scale_fill_manual(values=c("highlight"="Khaki", "other"="lightgrey")) +
  guides(fill="none") + 
  coord_flip() + 
  theme_minimal() + th
```

<img src="V01_Learning_ggplot_files/figure-html/income-by-town-1.png" width="576" style="display: block; margin: auto;" />

但事實上也可以用`gghighlight`直接達成


```r
county %>%
    mutate(county = reorder(county, people_total)) %>%
    ggplot() + aes(county, people_total) %>%
    geom_col(fill="deeppink") +
    gghighlight(county %in% c("新竹縣", "新竹市")) + 
    guides(fill="none") + 
    coord_flip() + 
    theme_minimal() + th
```

<img src="V01_Learning_ggplot_files/figure-html/unnamed-chunk-26-1.png" width="576" style="display: block; margin: auto;" />
