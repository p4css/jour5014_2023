# (PART) VISUALIZATION {.unnumbered}

# Visualization



## ggplot2

ggplot2和Python的matplotlib都是常用的視覺化套件，但在設計上有一些主要的差異。首先，ggplot2是基於grammar of graphics的設計原則，而matplotlib則是基於pyplot-style的設計風格。這意味著ggplot2更加著重於資料和視覺化之間的關係，並且提供了一個統一的語法來描述這些關係，而matplotlib則更加注重對於底層圖形物件的控制。其次，ggplot2支持更多的圖形屬性，例如數值變數、類別變數、時間序列等等，並且可以輕鬆地進行層疊圖、面積圖等高級視覺化技巧，而matplotlib則需要手動設置較多的屬性來達到類似的效果。此外，ggplot2在設計上更加注重美學和可讀性，因此預設的圖形風格更加美觀且易於閱讀，而matplotlib的預設風格則比較簡單，需要進行額外的設置才能達到類似的效果。

## VIS packages

除了ggplot2本身之外，尚有相當多基於ggplot或tidyverse風格的視覺化套件，如：

1.  ggraph：ggraph是一個基於ggplot2的視覺化套件，專門用於網絡和關係圖的繪製，提供了多種佈局和美學風格的選擇。
2.  ggmap：ggmap是一個基於ggplot2的地圖繪製套件，可以將Google Maps、OpenStreetMap等地圖數據與ggplot2圖形整合起來，方便進行地理位置相關的資料視覺化。
3.  ggridge：ggridge是一個基於ggplot2的視覺化套件，專門用於繪製ridge plots，也就是密度圖的一種變形。它可以幫助使用者更好地展示數據的分佈和趨勢。[Introduction to ggridges (r-project.org)](https://cran.r-project.org/web/packages/ggridges/vignettes/introduction.html)
4.  ggthemes：ggthemes是一個基於ggplot2的視覺化套件，提供了多種高質量的主題風格和顏色調色板，可以讓使用者快速改善圖表的外觀和可讀性。
5.  ggnatimate：ggnatimate用於將ggplot所產製的圖表多增加一個變量以轉製為動態圖表，支持多種動畫效果。
6.  ggally：ggally是一個基於ggplot2的視覺化套件，提供了多種高級散點圖和數據矩陣的繪製方式，方便使用者進行多變量分析和資料探索。
7.  ggrepel：ggrepel是一個基於ggplot2的視覺化套件，用於解決文字標籤重疊的問題，可以自動調整文字標籤的位置，使其更加易讀和美觀。

## Covered Cases

1.  [WP: Paid Maternity Leave (產假支薪)](#maternity): barplot
2.  [NYT: Population Changes Over More Than 20,000 Years](#population_growth): Coordinate, lineplot
3.  [NYT: LeBron James' Achievement](#lebron): Coordinate, lineplot
4.  [Taiwan Village Population Distribution](#vilpopulation): Coordinate, lineplot
5.  [NYT: Net Worth by Age Group](#networth): Coordinate, barplot
6.  [NYT: Optimistic of different generation](#optimistic): Association, scatter
7.  [Vaccinating Proportion by countries](#vaccination): Amount, heatmap
8.  [NYT: Taiwan salary distribution](#twsalary): Distribution, boxmap
9.  [Taiwan income distribution by each town](#twincome): Distribution, boxmap
10. [NYT: Carbon by countries](#carbon): Proportion, Treemap
11. [Taiwan Annual Expenditure](#twbudget): Proportion, Treemap
