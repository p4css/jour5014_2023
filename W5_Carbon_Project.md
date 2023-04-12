

# Global Carbon Projects

本節所採用的案例來自紐約時報的「[Who Has The Most Historical Responsibility for Climate Change? - The New York Times (nytimes.com)](https://www.nytimes.com/interactive/2021/11/12/climate/cop26-emissions-compensation.html?campaign_id=29&emc=edit_up_20211112&instance_id=45236&nl=the-upshot&regi_id=52022771&segment_id=74222&te=1&user_id=7cc6d9cd8f523e256ae41958ee8a9cb5)」。該報導指出，在聯合國氣候峰會上，最大的爭議之一是富國應該如何賠償負責全球變暖的突出排放國所造成的損害，以及以何種方式進行賠償。全球23個富裕的發達國家負責了一半的歷史二氧化碳排放量，而其他150多個國家負責了另一半。貧窮的、脆弱的國家要求富裕國家提供更多的資金來幫助其適應氣候變化所帶來的風險，例如海堤或早期洪水和乾旱的預警系統。對於這個問題，稱為"損失與損害"，富國歷來都反對建立一個特定的資金來進行賠償，擔心這可能會開啟賠償請求的洪水門。目前，只有蘇格蘭政府願意提供具體的金額，承諾為氣候災難的受害者提供270萬美元的賠償。

這個案例所採用的視覺化圖表為Treemap。二氧化碳排放是導致氣溫上升和其他氣候變化的主要貢獻因素之一。但是，哪些國家排放最多的碳？這些圖表清晰地顯示了23個最富有的國家（按地理和政治區域分組），佔排放量的50％，以及負責另外50％的150多個國家的排放量。


```r
totreemap <- read_csv("data/GCB2021v34_MtCO2_flat.csv") %>% 
    drop_na(`Total`) %>%
    filter(!Country %in% c("Global", "International Transport")) %>%
    filter(Year==2020) %>%
    arrange(desc(`Total`)) %>%
    mutate(perc = Total/sum(Total)) %>%
    slice(1:20)
library(treemapify)
totreemap %>%
    ggplot() + aes(area = perc, fill=`Per Capita`, label=Country) +
    geom_treemap() + 
    geom_treemap_text(color="white", 
                      place="centre", 
                      grow=TRUE
                      )
```

<img src="W5_Carbon_Project_files/figure-html/unnamed-chunk-2-1.png" width="100%" style="display: block; margin: auto;" />
