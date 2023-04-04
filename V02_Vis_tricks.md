---
---
---



## COLORS

## CHART TYPES

## Line Types

See more from ggthemes <https://github.com/BTJ01/ggthemes/tree/master/inst/examples>


```r
rescale01 <- function(x) {
    (x - min(x)) / diff(range(x))
  }

gather(economics, variable, value, -date) %>%
    group_by(variable) %>%
    mutate(value = rescale01(value)) %>%
    ggplot(aes(x = date, y = value, linetype = variable)) +
    geom_line() +
    scale_linetype_stata() + theme_minimal()
```

<img src="V02_Vis_tricks_files/figure-html/unnamed-chunk-2-1.png" width="100%" style="display: block; margin: auto;" />

## Smoothing

-   Smooth by [bin smoothing](http://rafalab.dfci.harvard.edu/dsbook/smoothing.html#bin-smoothing)

-   Example comes from [Rafael's online book](http://rafalab.dfci.harvard.edu/dsbook/smoothing.html)


```r
raw <- read_csv("data/vaccinations.csv")

fullvaccinated <- raw %>% select(country = location, date, 
                                people_fully_vaccinated_per_hundred) %>%
  drop_na(people_fully_vaccinated_per_hundred) %>%
  mutate(m = floor_date(date, unit = "month")) %>%
  group_by(country, m) %>%
  arrange(date) %>%
  slice(1) %>%
  ungroup() %>%
  select(-date)
```


```r
library(dslabs)
span <- 7 
fit <- with(polls_2008, 
            ksmooth(day, margin, kernel = "box", bandwidth = span))

polls_2008 %>% 
    mutate(smooth = fit$y) %>%
    ggplot(aes(day, margin)) +
    geom_point(size = 3, alpha = .5, color = "grey") + 
    geom_line(aes(day, smooth), color="red") + theme_minimal()
```

<img src="V02_Vis_tricks_files/figure-html/unnamed-chunk-4-1.png" width="100%" style="display: block; margin: auto;" />
