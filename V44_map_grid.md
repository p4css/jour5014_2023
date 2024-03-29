

## Mapping data with grid


```r
library(sf)
```

### Loading Taiwan map


```r
TW.island <- st_read("data/shapefiles/COUNTY_MOI_1090820.shp") %>%
    st_transform(3826) %>%
    mutate(id = row_number())
```

```{.output}
## Reading layer `COUNTY_MOI_1090820' from data source 
##   `/Users/jirlong/Library/CloudStorage/Dropbox/Programming/JOUR5014/data/shapefiles/COUNTY_MOI_1090820.shp' 
##   using driver `ESRI Shapefile'
## Simple feature collection with 22 features and 4 fields
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: 114.3593 ymin: 10.37135 xmax: 124.5611 ymax: 26.38528
## Geodetic CRS:  TWD97
```

### Building grid


```r
# Defining grid size
grid.extent <- 
  matrix(c(-50000,  2920000,   # (Xmin, Ymax)
           610000,  2920000,   # (Xmax, Ymax)
           610000, 2420000,    # (Xmax, Ymin)
           -50000, 2420000,    # (Xmin, Ymin)
           -50000,  2920000),  # (Xmin, Ymax) 
         byrow = TRUE, ncol = 2) %>%
  list() %>% # convert to list for st_polygon()
  st_polygon() %>% # generate polygon
  st_sfc(crs = 3826) # convert format and crs
# plot(grid.extent)


# Generating grid
Grid.sys <- 
  st_make_grid(grid.extent,
               n = c(132, 100),   # Resolution of grids
               crs = 3826,  # crs: TWD97 121
               what = 'polygons') %>%   # output format: polygon
  st_sf('geometry' = ., data.frame('ID' = 1:length(.))) # convert to sf with id
  # st_transform(3826) # assigning crs again ? 
plot(Grid.sys)
```

<img src="V44_map_grid_files/figure-html/unnamed-chunk-4-1.png" width="100%" style="display: block; margin: auto;" />


```r
Grid.TW <- 
  Grid.sys[subset(TW.island),]
plot(Grid.TW)
```

<img src="V44_map_grid_files/figure-html/unnamed-chunk-5-1.png" width="100%" style="display: block; margin: auto;" />

### loading data


```r
president_vote <- readxl::read_xlsx('data/president.xlsx') %>% 
  mutate(total = chu + tsai + song) %>% 
  mutate(chu_ratio = chu / total,
         tsai_ratio = tsai / total,
         song_ratio = song / total,
         tsai_chu_ratio = tsai / chu)
```

### Merging data


```r
tw_info <- TW.island %>% 
    st_set_geometry(NULL) %>%
    left_join(president_vote, by=c("COUNTYNAME"="county"))
```


```r
# TW_info <- sf::st_intersects(Grid.TW, TW.island)  # creat a data.frame of IDs in IBA for 1km grid
grid_id <- sapply(st_intersects(Grid.TW, TW.island), function(z) if (length(z)==0) NA_integer_ else z[1])

Grid.TW <-  Grid.TW %>%
  mutate(grid_id = grid_id) %>%
  left_join(tw_info, by=c("grid_id"="id"))
```


```r
Grid.TW %>%
    ggplot(aes(fill = tsai_ratio)) + geom_sf(lwd = 0.1, color="black") + 
    scale_fill_continuous(high="#2EFF71", low="blue") + 
    theme_void()
```

<img src="V44_map_grid_files/figure-html/unnamed-chunk-9-1.png" width="100%" style="display: block; margin: auto;" />
