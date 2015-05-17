## swemaps: dataset with swedish polygon data for ggplot and leaflet in R

    devtools::install_github('reinholdsson/swemaps')

- `map_ln`: regions polygon data
- `map_kn`: municipalities polygon data
- `cent_ln`: regions centroids data
- `cent_kn`: municipalities centroids data

### Examples

```{r}
library(ggplot2)
library(leaflet)
library(rkolada)
library(swemaps)

# function to merge kolada data with map data from swemaps
prepare_map_data <- function(x) {
  x$knkod <- x$municipality.id
  data <- merge(map_kn, x, by = 'knkod')
  data[order(data$order),]# make sure it's sorted by "order" column
}

# rkolada conn
a <- rkolada::rkolada()

## Example 1 ###

# Get data from Kolada
x <- a$values('N00941', year = 2010)  # make sure "kpi.municipality_type == 'K'"
x <- prepare_map_data(x)
# Plot it!
ggplot(x, aes_string('ggplot_long', 'ggplot_lat', group = 'knkod', fill = 'value')) +
  geom_polygon() +
  coord_equal()

### Example 2 ###

x <- a$values('N00941', c('1440', '0604', '2505', '2084'), year = 2010, all.cols = T)
x <- prepare_map_data(x)
ggplot(x, aes_string('ggplot_long', 'ggplot_lat', group = 'knkod', fill = 'value')) +
  geom_polygon() +
  coord_equal() +
  facet_wrap(~municipality.title, scales = 'free', ncol = 2) +
  theme_bw()

### Example 3 ###

x <- a$values('N00941', year = 2010, all.cols = T)
x <- prepare_map_data(x)

m <- leaflet() %>% addTiles()

for (kn in unique(x$knkod)) {
  i <- x[x$knkod == kn,]
  browser()
  m <- m %>% addPolygons(i$leaflet_long, i$leaflet_lat, color = 'blue', weight = 1)
}

m  # plot!
```

### Source

arcgis (`Kommungränser_SCB_07`, `Länsgränser_SCB_07`), SCB
