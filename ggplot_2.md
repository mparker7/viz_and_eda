ggplot\_2
================
Matthew Parker
10/01/2019

## Create weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/matthewparker/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-05-30 10:03:41

    ## file min/max dates: 1869-01-01 / 2019-05-31

    ## file path:          /Users/matthewparker/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-05-30 10:03:56

    ## file min/max dates: 1965-01-01 / 2019-05-31

    ## file path:          /Users/matthewparker/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-05-30 10:04:33

    ## file min/max dates: 1999-09-01 / 2019-05-31

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

## making new plots

start with an old plot

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

add labels

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

x axis tick marks etc

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15, -5, 20),
    labels = c("-15C", "-5", "-20")
  ) +
  scale_y_continuous(
    trans = "sqrt"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## colors

``` r
ggp_base =weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )
```

## themes

``` r
ggp_base +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
ggp_base +
  theme_bw() + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
ggp_base +
  theme_minimal() + 
  theme(legend.position = "none")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## more than one dataset

``` r
central_park = 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>% 
  filter(name == "Waikiki_HA")

ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp)) + 
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

(brief aside about colors)

``` r
waikiki %>% 
  ggplot(aes(x = date, y = tmax)) +
  geom_point(alpha = 0.5, color = "blue")
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](ggplot_2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## Multi-panel plots

``` r
ggp_scatter = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_density = 
  weather_df %>% 
  ggplot(aes(x = tmin)) + 
  geom_density()

ggp_box = 
  weather_df %>% 
  ggplot(aes(x = name, y = tmax, color = name)) + 
  geom_boxplot()

ggp_scatter + (ggp_density / ggp_box)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](ggplot_2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## data manipulation

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = fct_relevel(name, "Waikiki_HA", "CentralPark_NY")
  ) %>%
  ggplot(aes(x = name, y = tmax, color = name)) + 
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](ggplot_2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

reorder instead of level

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = fct_reorder(name, tmax)
  ) %>%
  ggplot(aes(x = name, y = tmax, color = name)) + 
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](ggplot_2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

## restructure then plot

``` r
weather_df %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperature"
  ) %>% 
  ggplot(aes(x = temperature, fill = observation)) +
  geom_density(alpha = 0.5) +
  facet_grid(~name) + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

![](ggplot_2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

## litters and pups

``` r
pup_data = 
  read_csv("./data/FAS_pups.csv", col_types = "ciiiii") %>%
  janitor::clean_names() %>%
  mutate(sex = recode(sex, `1` = "male", `2` = "female")) 

litter_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>%
  mutate(wt_gain = gd18_weight - gd0_weight,
         day_of_tx = as.numeric(day_of_tx))

fas_data = left_join(pup_data, litter_data, by = "litter_number") 
```

``` r
fas_data %>% 
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome",
    values_to = "pn_day"
  ) %>%
  drop_na(pn_day, dose, day_of_tx) %>% 
  mutate(
    outcome = factor(outcome),
    outcome = fct_reorder(outcome, pn_day)
  ) %>% 
  ggplot(aes(x = dose, y = pn_day)) +
  geom_violin() +
  facet_grid(day_of_tx ~ outcome)
```

![](ggplot_2_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->
