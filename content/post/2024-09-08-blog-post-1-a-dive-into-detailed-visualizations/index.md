---
title: 'Blog Post 1: A Dive Into Detailed Visualizations'
author: Chris S
date: '2024-09-08'
slug: blog-post-1-a-dive-into-detailed-visualizations
categories: []
tags: []
---


``` r
library(ggplot2)
library(maps)
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ lubridate 1.9.3     ✔ tibble    3.2.1
## ✔ purrr     1.0.2     ✔ tidyr     1.3.1
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ✖ purrr::map()    masks maps::map()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
library(dplyr)
d_popvote <- read.csv("/Users/chrisshen/Downloads/GOV1347/Election Blog 4/popvote_1948-2020.csv")
```



``` r
my_custom_theme <- 
  theme_bw() + 
  theme(panel.border = element_blank(),
        plot.title = element_text(size = 15, hjust = 0.5), 
        axis.text.x = element_text(angle = 45, hjust = 1),
        axis.text = element_text(size = 12),
        strip.text = element_text(size = 18),
        axis.line = element_line(colour = "black"),
        legend.position = "right",
        legend.text = element_text(size = 12))

d_popvote |> 
  ggplot(aes(x = year, y = pv2p, color = party)) + 
  geom_line() + 
  scale_color_manual(values = c("dodgerblue4", "firebrick1")) + 
  my_custom_theme + 
  labs(
    title = "Visualize the Two-Party Presidential Popular Over Time"
  )
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />


``` r
states_map <- map_data("state")

# Reading the dataset
d_pvstate_wide <- read_csv("/Users/chrisshen/Downloads/GOV1347/Election Blog 4/clean_wide_state_2pv_1948_2020.csv")
```

```
## Rows: 959 Columns: 14
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (1): state
## dbl (13): year, D_pv, R_pv, D_pv2p, R_pv2p, D_pv_lag1, R_pv_lag1, D_pv2p_lag...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
# Merge d_pvstate_wide with state_map.
d_pvstate_wide$region <- tolower(d_pvstate_wide$state)

pv_map <- d_pvstate_wide |>
  filter(year == 2020) |>
  left_join(states_map, by = "region")

# Make map grid of state winners for each election year 
pv_win_map <- pv_map |> 
  mutate(winner = ifelse(R_pv2p > D_pv2p, "republican", "democrat"))

# Create state labels (centered)
state_centroids <- data.frame(state = tolower(state.name), 
                              long = state.center$x, 
                              lat = state.center$y, 
                              abb = state.abb)

# Plot the map with state labels in the center
pv_win_map |> 
  ggplot(aes(long, lat, group = group)) + 
  geom_polygon(aes(fill = winner)) + 
  scale_fill_manual(values = c("dodgerblue4", "firebrick1")) + 
  geom_text(data = state_centroids, aes(x = long, y = lat, label = abb), 
            color = "white", size = 3, inherit.aes = FALSE) + 
  theme_void() +
  theme(panel.background = element_rect(fill = "lightgrey", color = NA), 
        plot.background = element_rect(fill = "lightgrey", color = NA))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />


``` r
d_pvstate_wide |> 
  filter(year >= 1980) |> 
  left_join(states_map, by = "region") |> 
  mutate(winner = ifelse(R_pv2p > D_pv2p, "republican", "democrat")) |> 
  ggplot(aes(long, lat, group = group)) +
  facet_wrap(facets = year ~.) + 
  geom_polygon(aes(fill = winner), color = "white") + 
  scale_fill_manual(values = c("dodgerblue4", "firebrick1")) + 
  theme_void() + 
  ggtitle("Presidential Vote Share (1980-2020)") + 
  theme(strip.text = element_text(size = 12), 
        aspect.ratio = 1) +
  theme(panel.background = element_rect(fill = "lightgrey", color = NA), 
        plot.background = element_rect(fill = "lightgrey", color = NA))
```

```
## Warning in left_join(filter(d_pvstate_wide, year >= 1980), states_map, by = "region"): Detected an unexpected many-to-many relationship between `x` and `y`.
## ℹ Row 1 of `x` matches multiple rows in `y`.
## ℹ Row 1 of `y` matches multiple rows in `x`.
## ℹ If a many-to-many relationship is expected, set `relationship =
##   "many-to-many"` to silence this warning.
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />

