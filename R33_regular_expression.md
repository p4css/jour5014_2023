

# Regular expression {#re}


```r
library(tidyverse)
```

\
正規表達式（Regular Expression）是一種用來描述、搜尋和操作文本的強大工具。它是由一系列字元和特殊符號所組成的模式，用於匹配和處理符合特定模式的字串。

正規表達式提供了一種靈活且強大的方式來執行字串的模式匹配。這些模式可以包含字母、數字、特殊字符和控制字符等各種元素。使用正規表達式，可以進行字串的搜尋、替換、提取、驗證和分割等操作。

**Online tools for detecting regular expression (RE)**

-   <https://regex101.com/>
-   <http://regexr.com/>

**RE Patterns**

在正規表達式中，一些特殊符號和字符具有特殊的含義。例如，`\d`可以匹配任何一個數字，`\w`可以匹配任何一個字母或數字，`.`可以匹配除換行符之外的任何一個字符，`+`表示前面的模式可以出現一次或多次，`*`表示前面的模式可以出現零次或多次，`?`表示前面的模式可以出現零次或一次，`[]`用於指定一組字符中的任意一個等等。較完整的表格如下。**要注意到的是，如果是`\d`的話，R中要以`\\d`雙斜線來表示**。

`pattern <- "\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}\\b"`為一個用R來寫RE的範例：在R中，我們使用雙反斜線（\\）來表示正規表達式中的特殊字符。在這個示例中，使用的正規表達式模式包括以下幾個部分：

-   **`\\b`**：匹配單詞的邊界，確保郵件地址不是部分字詞的一部分。

-   **`[A-Za-z0-9._%+-]+`**：匹配郵件地址的用戶名部分，它可以包含字母、數字、點（.）、下劃線（\_）、百分比（%）、加號（+）和減號（-）。注意這個在中括號中的點（.）並非用來匹配任意字。後面的`+`號意思是代表至少要有一個字以上。

-   **`@`**：匹配郵件地址中的「\@」符號。

-   **`[A-Za-z0-9.-]+`**：匹配郵件地址的域名部分，它可以包含字母、數字、點（.）和減號（-）。

-   **`\\.`**：匹配郵件地址中的點（.），需要使用反斜線進行轉義。

-   **`[A-Za-z]{2,}`**：匹配郵件地址的頂級域名部分，它可以是由至少兩個字母組成的字詞。

|                |                                                                                                                                                                                               |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Pattern**    | Description                                                                                                                                                                                   |
| `^`            | Matches beginning of line.                                                                                                                                                                    |
| `$`            | Matches end of line.                                                                                                                                                                          |
| `.`            | Matches any single character except newline. Using m option allows it to match newline as well.                                                                                               |
| `[...]`        | Matches any single character in brackets.                                                                                                                                                     |
| `[^...]`       | Matches any single character not in brackets                                                                                                                                                  |
| `re*`          | Matches 0 or more occurrences of preceding expression.                                                                                                                                        |
| `re+`          | Matches 1 or more occurrence of preceding expression.                                                                                                                                         |
| `re?`          | Matches 0 or 1 occurrence of preceding expression.                                                                                                                                            |
| `*?, +?, ??`   | \*, +和?預設是greedy的，如果要匹配'\<H1\>title\</H1\>'時，會匹配整個句子。如果不希望它是greedy的，就要在上述符號後面加一個？。Using .\*? in the previous expression will match only '\<H1\>'. |
| re{ n}         | Matches exactly n number of occurrences of preceding expression.                                                                                                                              |
| re{ n,}        | Matches n or more occurrences of preceding expression.                                                                                                                                        |
| `re{n, m}`     | Matches at least n and at most m occurrences of preceding expression.                                                                                                                         |
| `a| b`         | Matches either a or b.                                                                                                                                                                        |
| `(re)`         | 問號括住的部分就是match要留下來的部分。Groups regular expressions and remembers matched text.                                                                                                 |
| (?imx)         | Temporarily toggles on i, m, or x options within a regular expression. If in parentheses, only that area is affected.                                                                         |
| (?-imx)        | Temporarily toggles off i, m, or x options within a regular expression. If in parentheses, only that area is affected.                                                                        |
| (?: re)        | Groups regular expressions without remembering matched text.                                                                                                                                  |
| (?imx: re)     | Temporarily toggles on i, m, or x options within parentheses.                                                                                                                                 |
| (?-imx: re)    | Temporarily toggles off i, m, or x options within parentheses.                                                                                                                                |
| (?#\...)       | Comment.                                                                                                                                                                                      |
| (?= re)        | Specifies position using a pattern. Doesn't have a range.                                                                                                                                     |
| (?! re)        | Specifies position using pattern negation. Doesn't have a range.                                                                                                                              |
| (?\> re)       | Matches independent pattern without backtracking.                                                                                                                                             |
| `\w`           | Matches word characters.                                                                                                                                                                      |
| \\W            | Matches nonword characters.                                                                                                                                                                   |
| `\s`           | Matches whitespace. Equivalent to [\\t\\n\\r\\f].                                                                                                                                             |
| `\S`           | Matches nonwhitespace.                                                                                                                                                                        |
| `\d`           | Matches digits. Equivalent to [0-9].                                                                                                                                                          |
| `\D`           | Matches nondigits.                                                                                                                                                                            |
| \\A            | Matches beginning of string.                                                                                                                                                                  |
| \\Z            | Matches end of string. If a newline exists, it matches just before newline.                                                                                                                   |
| \\z            | Matches end of string.                                                                                                                                                                        |
| \\G            | Matches point where last match finished.                                                                                                                                                      |
| \\b            | Matches word boundaries when outside brackets. Matches backspace (0x08) when inside brackets.                                                                                                 |
| \\B            | Matches nonword boundaries.                                                                                                                                                                   |
| \\n, \\t, etc. | Matches newlines, carriage returns, tabs, etc.                                                                                                                                                |
| \\1\...\\9     | Matches nth grouped subexpression.                                                                                                                                                            |
| \\10           | Matches nth grouped subexpression if it matched already. Otherwise refers to the octal representation of a character code.                                                                    |

## **RE applications on string operations**

-   **Replacing** specific pattern
    -   Replacing all space or new line character to one space or empty string
    -   Deleting all number and alphabetic by empty string
    -   Deleting all HTML tags
-   **Detecting** if it exists some specific pattern
    -   Fitering if one sentence starting with ".
-   **Extracting** some words by specific pattern
    -   Extracting hashtags in text
    -   Extracting timestamp, email, hyperlink, or url in text
    -   Extracting number after some specific patterns
-   **Splitting** Splitting string into several parts when detecting some specific pattern successfully.

**Replacing**

-   `\s` matching any whitespace character including space, tabs, and line breaks
-   R uses `\\s`, instead of `\s`.
-   `\\d` digit, `\\D` not digit
-   `\\s` whitepsace, `\\S` not whitespace
-   `\\w` word, `\\W` not word
-   `.` matches any
-   `[A-Z0-9a-z]` range
-   `[ABC]` character set
-   `[^ABC]` neglect set (Leadning `^` in `[]` means neglecting)


```r
s <- "   123   456   789   "
str_replace_all(s, "\\s", "")
```

```
## [1] "123456789"
```

### Extracting

-   `+` means matching word occurring at least one time, matching longer is better
-   `*` means matching word occurring any time, matching longer is better
-   `{1,3}` means matching at least one time, but at most 3 times
-   `{3}` means matching 3 times exactly.

#### by `str_extract()`

以下這個例子是在爬取PTT資料後，嘗試能夠取出每則貼文的ID與暱稱。


```r
pname <- c("pttid111(kefan)", "pid2(hangfan)")


# extracting at least one words until not word
str_extract(pname, "\\w")
```

```
## [1] "p" "p"
```

```r
str_extract(pname, "\\w+")
```

```
## [1] "pttid111" "pid2"
```

```r
str_extract(pname, "[A-Za-z0-9]+")
```

```
## [1] "pttid111" "pid2"
```

```r
str_extract(pname, "[A-Za-z0-9]*")
```

```
## [1] "pttid111" "pid2"
```

```r
str_extract(pname, "[A-Za-z0-9]{8}")
```

```
## [1] "pttid111" NA
```

```r
str_extract(pname, "[A-Za-z0-9]{1,8}")
```

```
## [1] "pttid111" "pid2"
```

```r
# extracting word between ()
# () is controlled vocabulary in regular expression, needing \\( and \\) to specify the character themself
str_extract(pname, "\\(\\w+\\)") %>% str_replace_all("[\\(\\)]", "")
```

```
## [1] "kefan"   "hangfan"
```

```r
str_extract(pname, "\\(.+\\)") 
```

```
## [1] "(kefan)"   "(hangfan)"
```

#### by `str_extract` for data frame


```r
data_frame(pname) %>%
    mutate(nickname = str_extract(pname, "\\(\\w+\\)") %>% str_replace_all("[\\(\\)]", "")) %>%
    mutate(id = str_extract(pname, "\\w+"))
```

```
## Warning: `data_frame()` was deprecated in tibble 1.1.0.
## ℹ Please use `tibble()` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```
## # A tibble: 2 × 3
##   pname           nickname id      
##   <chr>           <chr>    <chr>   
## 1 pttid111(kefan) kefan    pttid111
## 2 pid2(hangfan)   hangfan  pid2
```

#### by `str_replace()`

-   `(\\w+)`的括號代表，我等一下要抽取出這個pattern的字
-   `\\(.+\\)`則是把剩下的pattern matching 完
-   `\\1`代表我要抽取前面第一組抽取出來的pattern，也就是`(\\w+)`中間的`\\w+`。


```r
data_frame(pname) %>%
    mutate(id = str_replace(pname, "(\\w+)\\(.+\\)", "\\1"))
```

```
## # A tibble: 2 × 2
##   pname           id      
##   <chr>           <chr>   
## 1 pttid111(kefan) pttid111
## 2 pid2(hangfan)   pid2
```

#### by `tidyr::extract()` for data frame


```r
data_frame(pname) %>% 
    tidyr::extract(pname, c("id", "nickname"), "(\\w+)\\((.+)\\)", remove = F)
```

```
## # A tibble: 2 × 3
##   pname           id       nickname
##   <chr>           <chr>    <chr>   
## 1 pttid111(kefan) pttid111 kefan   
## 2 pid2(hangfan)   pid2     hangfan
```

```r
?tidyr::extract
```

### Detecting with non-greedy

這段程式碼使用R中的tidyverse套件中的**`extract()`**函數，從**`source`**向量中的每個元素中提取出「device」的資訊。假設我們希望從**`<p>`**和**`</p>`**之間提取「Twitter for iPhone」這個資訊。

此時我們會需要理解正規表示式的寫法預設是貪婪（greedy）的概念。當我們談到正規表達式的「貪婪」（greedy）和「非貪婪」（non-greedy）時，我們指的是模式匹配時的行為。

-   「貪婪」指的是匹配器在尋找模式時傾向於匹配盡可能多的字符。換句話說，它會儘可能地擴展匹配範圍。在正規表達式中，默認情況下，量詞（如**`*`**和**`+`**）是貪婪的。這意味著它們會匹配盡可能長的字串。

-   「非貪婪」（或稱「勉強」）則相反，它傾向於匹配最短的可能字串。在正規表達式中，可以在量詞後面加上「?」來表示非貪婪匹配。這樣一來，量詞就會儘可能少地匹配字符。

回到我們的例子，當使用默認的貪婪模式時，我們的正則表達式**`"Twitter for (.*)<"`**會從第一個**`<p>`**開始匹配，直到找到最後一個**`<`**之前的所有字符。這意味著它會將整個**`<p>Twitter for iphone</p><div><p>`**作為匹配結果，而不僅僅是**`<p>Twitter for iphone</p>`**。

但如果我們想要只匹配第一個**`<p>`**和之後的字符，而不是將整個**`<p>`**標籤內容都包括在內，我們可以使用「非貪婪」模式，將正則表達式修改為**`"Twitter for (.*?)<"`**，在量詞**`*`**後面加上「?」。這樣，它會匹配到第一個**`<`**之前的最短字串，即只匹配到**`<p>Twitter for iphone`**。


```r
source <- c("<p>Twitter for iphone</p>", 
            "<div><p>Twitter for iphone</p></div>")
data_frame(source) %>% extract(source, "device", "Twitter for (.*)<")  # Greedy
```

```
## # A tibble: 2 × 1
##   device    
##   <chr>     
## 1 iphone    
## 2 iphone</p>
```

```r
data_frame(source) %>% extract(source, "device", "Twitter for (.*?)<") # Non-Greedy
```

```
## # A tibble: 2 × 1
##   device
##   <chr> 
## 1 iphone
## 2 iphone
```

### Detecting multiple patterns

-   <https://stackoverflow.com/questions/8020848/how-is-the-and-or-operator-represented-as-in-regular-expressions>

以下練習重點是讓學生理解如何使用 **`str_detect()`** 函數和正則表達式進行模式匹配，以及如何進行邏輯操作來組合和篩選符合特定模式的句子。

1.  **`str_detect(teststr, re1) & str_detect(teststr, re2)`** 通過 **`&`** 進行邏輯「與」操作，將兩個邏輯向量進行元素級別的「與」運算，得到一個新的邏輯向量。該向量指示哪些句子同時符合 **`re1`** 和 **`re2`** 的模式。

2.  **`str_detect(teststr, re1) | str_detect(teststr, re2)`** 通過 **`|`** 進行邏輯「或」操作，將兩個邏輯向量進行元素級別的「或」運算，得到一個新的邏輯向量。該向量指示哪些句子符合 **`re1`** 或 **`re2`** 的模式。


```r
teststr <- c("B和A是不是男女朋友呢", 
             "C與B是不是在一起呢", 
             "A就是B。")
re1 <- "(.*B.*呢$)"
re2 <- "(.*A.*)"
str_detect(teststr, re1)
```

```
## [1]  TRUE  TRUE FALSE
```

```r
str_detect(teststr, re2)
```

```
## [1]  TRUE FALSE  TRUE
```

```r
str_detect(teststr, re1) & str_detect(teststr, re2)
```

```
## [1]  TRUE FALSE FALSE
```

```r
str_detect(teststr, re1) | str_detect(teststr, re2)
```

```
## [1] TRUE TRUE TRUE
```

### Extracting nearby words

#### Extracting nearby 3 English words


```r
string2 <- "..., compelled to defend as never before the hard-charging progressivism and ...."
data_frame(string2) %>%
    mutate(string2 = str_extract(string2, "(\\S+\\s){3}before(\\s\\S+){3}")) %>%
    extract(string2, c("prefix","hit", "tail"), "(.+)(before)(.+)")
```

```
## # A tibble: 1 × 3
##   prefix             hit    tail                              
##   <chr>              <chr>  <chr>                             
## 1 "defend as never " before " the hard-charging progressivism"
```

#### Extracting nearby 3 Chinese words


```r
string3 <- c("呵呵呵呵呵呵呵我家就住在台灣，那是個美麗的地方",
             "臺灣真是個美麗的地方齁齁齁", 
             "呵呵呵呵呵呵呵我愛台灣臺灣")
str_extract(string3, ".{5}台灣.{5}|.{5}臺灣.{5}")
```

```
## [1] "我家就住在台灣，那是個美" NA                        
## [3] NA
```

```r
str_extract(string3, ".{1,5}台灣.{1,5}|.{1,5}臺灣.{1,5}")
```

```
## [1] "我家就住在台灣，那是個美" NA                        
## [3] "呵呵呵我愛台灣臺灣"
```

#### Extracting nearby 3 Chinese words


```r
df <- data_frame(string3)
df %>% extract(string3, c("pre", "hit", "tail"), "(.{0,5})(台灣|臺灣)(.{0,5})")
```

```
## # A tibble: 3 × 3
##   pre          hit   tail      
##   <chr>        <chr> <chr>     
## 1 "我家就住在" 台灣  ，那是個美
## 2 ""           臺灣  真是個美麗
## 3 "呵呵呵我愛" 台灣  臺灣
```

## RE Case studies

### Getting the last page of PTT HatePolitics

這段程式碼的目的是從URL中提取出頁數或特定的部分，並進行一些後續處理和轉換。這些操作可用於從URL中獲取有用的訊息，例如網頁索引、頁數等。


```r
urls <- c("https://www.ptt.cc/bbs/HatePolitics/index4086.html", 
          "https://www.ptt.cc/bbs/HatePolitics/index348.html")
df <- tibble(urls)
str_extract(urls, "index(\\d+)\\.html")
```

```
## [1] "index4086.html" "index348.html"
```

```r
str_replace(urls, ".*index(\\d+)\\.html", "\\1") %>% as.numeric()
```

```
## [1] 4086  348
```

```r
data_frame(urls) %>% extract(urls, "last_page", "index(\\d+)\\.html", remove = F)
```

```
## # A tibble: 2 × 2
##   urls                                               last_page
##   <chr>                                              <chr>    
## 1 https://www.ptt.cc/bbs/HatePolitics/index4086.html 4086     
## 2 https://www.ptt.cc/bbs/HatePolitics/index348.html  348
```

::: practice
### Practice. Ask CHATGPT

````         
我現在有一程式碼如下
```
urls <- c("https://www.ptt.cc/bbs/HatePolitics/index4086.html", 
          "https://www.ptt.cc/bbs/HatePolitics/index348.html")
df <- tibble(urls)
```
我希望來抽取出頁面的編號，新增一個page_num的變項，使得
```
...index4086.html: 4086
...index348.html: 348
```
請以tidyverse pipeline的方式寫，並使用stringr的函式
````

以下為chatgpt的回答。

您可以使用tidyverse和stringr的函式來實現這個目標。以下是一個使用tidyverse pipeline和stringr的範例程式碼：

```         
library(tidyverse)

urls <- c("https://www.ptt.cc/bbs/HatePolitics/index4086.html", 
          "https://www.ptt.cc/bbs/HatePolitics/index348.html")

df <- tibble(urls) %>%
  mutate(page_num = str_extract(urls, "(?<=index)\\d+(?=.html)"))
```

PROMPT：請解釋**`(?<=index)\\d+(?=.html)`**

-   **`(?<=index)`**: 是一個Positive Lookbehind。它表示匹配位置之前必須出現"index"字串。在這種情況下，我們希望匹配的數字出現在"index"之後，所以使用正向肯定後願式來確定位置。

-   **`\\d+`**: 這是一個數字的匹配模式。**`\\d`**表示匹配一個數字字符（0-9），而**`+`**表示匹配一個或多個連續的數字字符。這樣可以確保我們匹配到的是一個或多個數字。

-   **`(?=.html)`**: 是一個Positive Lookahead。它表示匹配位置之後必須出現".html"字串。在這種情況下，我們希望匹配的數字出現在".html"之前，所以使用正向肯定前願式來確定位置。

-   **`index`**：匹配字串中的"index"字串。

-   **`(\\d+)`**：這是一個捕獲組（Capture Group），用於捕獲一個或多個連續的數字字符。**`\\d`**表示匹配一個數字字符（0-9），而**`+`**表示匹配一個或多個連續的數字字符。

-   **`\\.html`**：匹配字串中的".html"字串。由於"."在正則表達式中具有特殊意義，所以需要使用**`\\`**進行轉義。
:::


```r
urls <- c("https://www.ptt.cc/bbs/HatePolitics/index4086.html", 
          "https://www.ptt.cc/bbs/HatePolitics/index348.html")

df <- tibble(urls) %>%
  mutate(page_num = str_extract(urls, "(?<=index)\\d+(?=.html)"))
df
```

```
## # A tibble: 2 × 2
##   urls                                               page_num
##   <chr>                                              <chr>   
## 1 https://www.ptt.cc/bbs/HatePolitics/index4086.html 4086    
## 2 https://www.ptt.cc/bbs/HatePolitics/index348.html  348
```

## Useful cases

### Matching URL

這個正規表達式用於檢測是否符合超連結的格式。它可以匹配包含 **`http://`**、**`https://`**、**`ftp://`**、**`www.`** 或 **`ftp.`** 前綴的URL，並確保後續的部分符合網址的正確格式，例如域名、路徑和查詢參數等。

1.  **`^`** 表示匹配字符串的開頭。

2.  **`((https?|ftp)://|(www|ftp)\\.)?`** 是一個可選的前綴，它匹配以 **`http://`**、**`https://`**、**`ftp://`**、**`www.`** 或 **`ftp.`** 開頭的部分。其中 **`?`** 表示前綴部分是可選的。

3.  **`[a-z0-9-]+`** 匹配一個或多個小寫字母、數字或破折號字符。

4.  **`(\\.[a-z0-9-]+)+`** 是一個重複組，它匹配一個或多個點（**`.`**）後跟一個或多個小寫字母、數字或破折號字符。

5.  **`([/?].*)?`** 是另一個可選的後綴，它匹配以 **`/`** 或 **`?`** 開頭的部分，並跟隨著任意字符。

6.  **`$`** 表示匹配字符串的結尾。

根據需求和特定的使用情境，可以有不同的寫法。以下是一些可能的替代寫法：

1.  捕獲更多的URL前綴：當前的正規表達式只捕獲了 **`http://`**、**`https://`**、**`ftp://`**、**`www.`** 和 **`ftp.`** 這些前綴。如果需要捕獲更多的前綴，可以擴展前綴部分，例如 **`((https?|ftp|file)://|(www|ftp)\\.)?`**，這樣可以捕獲 **`file://`** 這樣的前綴。

2.  更精確的域名部分：當前的正規表達式使用 **`[a-z0-9-]+(\\.[a-z0-9-]+)+`** 匹配域名部分，這允許了字母、數字和破折號字符。如果需要更精確的域名匹配，可以使用更複雜的正規表達式，例如 **`(?!-)[A-Za-z0-9-]{1,63}(?<!-)\\.(?!-)[A-Za-z0-9-]{1,63}(?<!-)`**，這樣可以確保符合域名規範。


```r
pattern <- "^((https?|ftp)://|(www|ftp)\\.)?[a-z0-9-]+(\\.[a-z0-9-]+)+([/?].*)?$"

str_detect("http://www.yahoo.com.tw", pattern)
```

```
## [1] TRUE
```

```r
str_detect("https://m.facebook.com/story.php?story_fbid=1483357095305703&id=1435979486710131", pattern)
```

```
## [1] TRUE
```

```r
str_detect("https://www.facebook.com/groups/335691256571414/permalink/774316322708903/", pattern)
```

```
## [1] TRUE
```

### Removing all html tags but keeping comment content

ANS: `"推 ya870801: 推  218.166.12.10 10/16 15:56"`


```r
comment <- '<div class="push"><span class="hl push-tag">推 </span><span class="f3 hl push-userid">ya870801</span><span class="f3 push-content">: 推</span><span class="push-ipdatetime">  218.166.12.10 10/16 15:56</span></div>'
str_replace_all(comment, "<.+?>", "")
```

```
## [1] "推 ya870801: 推  218.166.12.10 10/16 15:56"
```

```r
str_replace_all(comment, "<.*?>", "")
```

```
## [1] "推 ya870801: 推  218.166.12.10 10/16 15:56"
```

### Removing space


```r
# Removing space in Chinese sentence
sentence <- c(' 噓 wwHui: 這批安好純   \n  辛苦了  \n 噓 ', '噓 wwHui: 這批安好純   ', '辛苦了  ')

# Removing all space characters
str_replace_all(sentence, "\\s", "")
```

```
## [1] "噓wwHui:這批安好純辛苦了噓" "噓wwHui:這批安好純"        
## [3] "辛苦了"
```

```r
# Removing leading and ending space by str_replace_all()
# You cannot use trimws()
str_replace_all(sentence, "^\\s+|\\s+$", "")
```

```
## [1] "噓 wwHui: 這批安好純   \n  辛苦了  \n 噓"
## [2] "噓 wwHui: 這批安好純"                    
## [3] "辛苦了"
```

### Testing


```r
teststr1 <- c("我最近過的挺爽", "最近我過的不是很好。", "我最近過得不太好。")
re1 <- "(^我.+)"
re2 <- "(.*。)"

str_detect(teststr1, str_c(re1,re2, sep = ""))
```

```
## [1] FALSE FALSE  TRUE
```

```r
str_detect(teststr1, str_c(re1,re2, sep = "|"))
```

```
## [1] TRUE TRUE TRUE
```
