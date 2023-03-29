

## Paid Maternity Leave (base to dplyr)


```r
library(tidyverse)
options(stringsAsFactors = F)
options(scipen = 999)

library(readxl)
read_excel("data/WORLD-MACHE_Gender_6.8.15.xls", "Sheet1", col_names=T) %>% 
    select(iso3, 6:24) %>% 
    filter(matleave_13 == 5, matleave_95 == 5) %>% 
    gather("year", "degree", 2:20) %>% 
    # spread(year, degree, fill = 0) %>% View
    replace_na(list(degree = 0)) %>%
    mutate(year2 = as.POSIXct(strptime(year, "matleave_%y"))) %>%
    mutate(year3 = lubridate::year(year2)) %>%
    ggplot() + 
    aes(year3, degree) + 
    geom_col(color = "royalblue", fill = "royalblue") + 
    facet_wrap(~ iso3) +
    theme_void()
```

<img src="R26_base2dplyr_paid_maternity_files/figure-html/unnamed-chunk-2-1.png" width="672" />
