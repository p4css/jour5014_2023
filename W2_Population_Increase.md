


```r
library(tidyverse)
library(gghighlight)
```

# WGOITG: Population Increase

紐時這篇報導「[When Did the Anthropocene Start? Scientists Closer to Saying When. - The New York Times (nytimes.com)](https://www.nytimes.com/2022/12/17/climate/anthropocene-age-geology.html)」討論了人類活動對地球所產生的深遠影響，並探討人類是否已經進入了一個新的地質時期，被稱為「人新世」。報導指出，人類的經濟活動、能源消耗和人口增長是人新世的主要因素，並且這些因素已經在地球上留下了不可磨滅的痕跡。報導也提到，地質學家已經發現了人新世的證據，包括核爆炸中的鈽同位素、肥料中的氮和發電廠的灰燼等。然而，報導也問道，人新世是否真的已經開始，以及它的開始點是否應該是農業革命、工業革命、核彈（77年前）或其他發展。

![](https://static01.nyt.com/images/2023/02/07/learning/AnthropoceneGraphLN/AnthropoceneGraphLN-superJumbo.png?quality=75&auto=webp)

However, Package *tabulizer* cannot be installed in the current version of R.


```r
library(tidyverse)
library(tabulizer)
# Extract the table

tables <- extract_tables('data/world_population_change.pdf', pages = 1)

# Extract the first element of the variable
df <- as.data.frame(tables[[1]])

df %>%
   select(1, 2) %>%
   slice(-c(1:4)) %>%
   rename(years_to_2020 = V1, population = V2)
   # select(years_to_2020 = v1, population = v2)
   # mutate(years_to_2020 = v1, population = v2)

toplot <- df %>%
   mutate(years_to_2020 = map(years_to_2020, ~(str_remove(., ",")))) %>%
   mutate(years_to_2020 = as.numeric(years_to_2020),
           population = as.numeric(population))

toplot %>%
    ggplot() + 
    aes(x=years_to_2020, y=population) + 
    # aes(x=-log10(years_to_2020), y=log10(population)) + 
    # geom_col()
    geom_point() + 
    scale_x_log10() + scale_y_log10() + 
    scale_x_reverse() + 
    theme_bw()
```
